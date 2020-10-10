---
title: Titkok elforgatása
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan forgathatja el a titkokat Azure Stack hub-ban.
author: BryanLa
ms.topic: how-to
ms.date: 06/29/2020
ms.reviewer: ppacent
ms.author: bryanla
ms.lastreviewed: 08/15/2020
monikerRange: '>=azs-1803'
ms.openlocfilehash: aca163df1026193933ffb9d09dbdf4a854638a75
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91899805"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>Titkok elforgatása Azure Stack központban

*Ezek az utasítások csak Azure Stack hub integrált rendszerek 1803-es vagy újabb verziójára érvényesek. Ne kísérelje meg a titkos elforgatást a pre-1803 verziókon*

Ez a cikk útmutatást és PowerShell-parancsfájlt biztosít a titkos rotációs szolgáltatáshoz, amely segít a Azure Stack hub-infrastruktúra erőforrásaival és szolgáltatásaival való biztonságos kommunikáció fenntartásában. 

## <a name="overview"></a>Áttekintés

Azure Stack hub titkokat használ az infrastruktúra-erőforrásokkal és-szolgáltatásokkal való biztonságos kommunikáció fenntartásához. Az Azure Stack hub-infrastruktúra integritásának fenntartása érdekében a kezelőknek képesnek kell lenniük arra, hogy a szervezet biztonsági követelményeinek megfelelő gyakorisággal forgatni tudják a titkokat.

### <a name="internal-vs-external-secrets"></a>Belső és külső titkos kulcsok

A 1811-es verziótól kezdődően a titkos elforgatás el van különítve a belső és külső tanúsítványokhoz:

- **Belső titok**: az Azure stack hub-infrastruktúra által a Azure stack hub-kezelő beavatkozása nélkül használt tanúsítványok, jelszavak, biztonságos karakterláncok és kulcsok.

- **Külső titkok**: infrastruktúra-szolgáltatási tanúsítványok az Azure stack hub-kezelő által biztosított külső szolgáltatásokhoz. A külső titkok közé tartoznak a következő szolgáltatások tanúsítványainak:

    - Felügyeleti portál
    - Nyilvános portál
    - Rendszergazda Azure Resource Manager
    - Globális Azure Resource Manager
    - Rendszergazda Key Vault
    - Key Vault
    - Felügyeleti bővítmény gazdagépe
    - ACS (blob-, tábla-és üzenetsor-tárolóval együtt)
    - ADFS
    - Graph
    
    \* Csak akkor alkalmazható, ha a környezet identitás-szolgáltatója Active Directory összevont szolgáltatások (AD FS).

> [!Important]
> A rendszergazda manuálisan frissíti az összes többi biztonságos kulcsot és karakterláncot. Ez magában foglalja a felhasználói és rendszergazdai fiókok jelszavait, a [hálózati kapcsolók jelszavait és engedélyeit](azure-stack-customer-defined.md), valamint a alaplapi felügyeleti vezérlő (bmc) hitelesítő adatait, amelyek a [jelen cikk későbbi részében](#update-the-bmc-credential)szerepelnek. 
>
>Emellett ez a cikk nem oldja meg az erőforrás-szolgáltatók értékkel való elforgatását. A titkos kódok elforgatásához tekintse át a következő cikkeket:
>
> - [Az App Service rotálása az Azure Stack Hubon – titkos kódok és tanúsítványok](app-service-rotate-certificates.md)
> - [MySQL erőforrás-szolgáltató – titkok elforgatása](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation)
> - [SQL erőforrás-szolgáltató – titkok elforgatása](azure-stack-sql-resource-provider-maintain.md#secrets-rotation)

### <a name="expiration-alerts"></a>Lejárati riasztások

Ha a titkos kulcsok érvényessége 30 napon belül lejár, a következő riasztások jönnek létre a felügyeleti portálon:

- A szolgáltatásfiók jelszavának lejárta folyamatban
- Függőben lévő belső tanúsítvány lejárata
- Külső tanúsítvány lejárata miatt függőben

A következő szakaszban található titkos elforgatási lépések elvégzésével elháríthatja ezeket a riasztásokat.

> [!Note]
> Az 1811 előtti verziókban Azure Stack hub-környezetek riasztásokat láthatnak a függőben lévő belső tanúsítványokra vagy a titkos kódokra vonatkozóan. Ezek a riasztások pontatlanok, és figyelmen kívül kell hagyni a belső titkos rotáció futtatása nélkül. A belső titkos kulcs lejárati idejének pontatlan riasztásai a 1811-ben megoldott ismert problémák. A belső titkok nem járnak le, kivéve, ha a környezet két évig aktív.

### <a name="external-certificates-from-a-new-certificate-authority"></a>Külső tanúsítványok új hitelesítésszolgáltatótól

Azure Stack hub a következő kontextusokban támogatja a titkos elforgatást egy új hitelesítésszolgáltatótól (CA) származó külső tanúsítványokkal:

|Telepített HITELESÍTÉSSZOLGÁLTATÓ|HITELESÍTÉSSZOLGÁLTATÓ, amely elforgatható|Támogatott|Azure Stack hub támogatott verziói|
|-----|-----|-----|-----|
|Self-Signed|– Vállalati|Támogatott|1903 & később|
|Self-Signed|Self-Signed|Nem támogatott||
|Self-Signed|A nyilvános<sup>*</sup>|Támogatott|1803 & később|
|Vállalattól|– Vállalati|Támogatott. 1803-1903: támogatott, amíg az ügyfelek ugyanazt a vállalati HITELESÍTÉSSZOLGÁLTATÓT használják, mint az üzembe helyezéskor|1803 & később|
|Vállalattól|Self-Signed|Nem támogatott||
|Vállalattól|A nyilvános<sup>*</sup>|Támogatott|1803 & később|
|Nyilvános<sup>*</sup>|– Vállalati|Támogatott|1903 & később|
|Nyilvános<sup>*</sup>|Self-Signed|Nem támogatott||
|Nyilvános<sup>*</sup>|A nyilvános<sup>*</sup>|Támogatott|1803 & később|

<sup>*</sup>Azt jelzi, hogy a nyilvános hitelesítésszolgáltatók a Windows megbízható legfelső szintű program részét képezik. A teljes listát megtalálhatja a [résztvevők listája – Microsoft megbízható legfelső szintű program](/security/trusted-root/participants-list).

## <a name="prerequisites"></a>Előfeltételek

Belső és külső titkok rotációja:

1. Javasoljuk, hogy először frissítse a Azure Stack hub-példányt a legújabb verzióra.

    ::: moniker range="<azs-1811"  
    >[!IMPORTANT]
    > 1811 előtti verziók esetén:
    > - Ha már elvégezte a titkos mentést, frissítenie kell az 1811-es vagy újabb verzióra, mielőtt ismét elvégezte a titkos kód elforgatását. A titkos elforgatást a [Kiemelt végponton](azure-stack-privileged-endpoint.md) keresztül kell végrehajtani, és Azure stack hub-kezelő hitelesítő adatokat kell megadnia. Ha nem tudja, hogy a titkos rotációt futtatta-e a környezetében, frissítsen a 1811-re a titkos elforgatás végrehajtása előtt.
    > - A kiterjesztésű gazdagép-tanúsítványok hozzáadásához nem kell elforgatnia a titkokat. A bővítmények gazdagép-tanúsítványainak hozzáadásához kövesse az [Azure stack hub bővítmény-gazdagépének előkészítése](azure-stack-extension-host-prepare.md) című cikk utasításait.
    ::: moniker-end

2. Értesítse a felhasználókat a tervezett karbantartási műveletekről. A szokásos karbantartási időszakok a munkaidőn kívüli időpontokban ütemezhetők. A karbantartási műveletek befolyásolhatják a felhasználói munkaterheléseket és a portálon végzett műveleteket is.

3. A titkok forgása során a kezelők megnyitva és automatikusan lezárulva láthatják a riasztásokat. Ez a viselkedés elvárt, a riasztások így figyelmen kívül hagyhatók. Az operátorok a [test-AzureStack PowerShell-parancsmag](azure-stack-diagnostic-test.md)használatával ellenőrizhetik a riasztások érvényességét. Az Azure Stack hub-rendszerek figyelését System Center Operations Manager használó operátorok esetében a rendszer karbantartási módba helyezése megakadályozza, hogy ezek a riasztások elérjék ITSM rendszerüket, de továbbra is riasztást kapnak, ha az Azure Stack hub rendszer elérhetetlenné válik.

A külső titkok elforgatásához hajtsa végre ezeket a további előfeltételeket:

1. Futtassa a **[`Test-AzureStack`](azure-stack-diagnostic-test.md)** PowerShell-parancsmagot a (z) `-group SecretRotationReadiness` paraméter használatával, hogy az összes teszt kimenet állapota Kifogástalan legyen a titkok elforgatása előtt.
2. Új helyettesítő külső tanúsítványok előkészítése:
    - Az új készletnek meg kell egyeznie az [Azure stack hub PKI-tanúsítvány követelményeiben](azure-stack-pki-certs.md)ismertetett tanúsítvány-specifikációkkal. 
    - Létrehozhat egy tanúsítvány-aláírási kérést (CSR) a hitelesítésszolgáltató (CA) számára a [tanúsítvány-aláírási kérelmek előállítása](azure-stack-get-pki-certs.md) és az Azure stack hub-környezetben való használatra való felkészülés során a [PKI-tanúsítványok előkészítése](azure-stack-prepare-pki-certs.md)című témakörben ismertetett lépések segítségével. 
    - Győződjön meg arról, hogy érvényesíti a felkészített tanúsítványokat a PKI- [tanúsítványok ellenőrzése](azure-stack-validate-pki-certs.md) című témakörben ismertetett lépésekkel.
    - Győződjön meg arról, hogy nincsenek speciális karakterek a jelszóban, például `*` vagy `)` .
    - Győződjön meg arról, hogy a PFX **-titkosítás TripleDES-SHA1**. Ha problémába ütközik, tekintse meg [a Azure stack hub PKI-tanúsítványok gyakori problémáinak elhárítása](azure-stack-remediate-certs.md#pfx-encryption)című témakört.
3. Biztonsági másolat készítése a biztonságos biztonsági mentési helyen történő elforgatáshoz használt tanúsítványokról. Ha az elforgatás fut, majd a művelet meghiúsul, cserélje le a fájlmegosztás tanúsítványait a biztonsági másolatokra a elforgatás újrafuttatása előtt. Tárolja a biztonsági másolatokat a biztonságos biztonsági mentési helyen.
4. Hozzon létre egy fájlmegosztás, amely a ERCS virtuális gépekről érhető el. A fájlmegosztás legyen olvasható és írható a **CloudAdmin** -identitáshoz.
5. Nyisson meg egy PowerShell ISE-konzolt egy olyan számítógépről, amelyhez hozzáféréssel rendelkezik a fájlmegosztás. Navigáljon a fájlmegosztás, ahol a külső tanúsítványok elhelyezésére szolgáló címtárakat hoz létre.
6. Töltse le **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** egy olyan hálózati fájlmegosztás számára, amely a rotációs folyamat során elérhető, és futtassa a parancsfájlt. A szkript létrehoz egy ***.\Certificates\AAD*** -vagy ***.\Certificates\ADFS***-alapú mappastruktúrát, az Ön személyazonossági szolgáltatójától függően. A mappa struktúrájának a ** \\ certificates (tanúsítványok** ) mappával kell KEZDŐDNIE, amelyet csak egy ** \\ HRE** vagy ** \\ ADFS** -mappa követ. Az előző struktúrában az összes további alkönyvtár szerepel. Példa:
    - Fájlmegosztás = **\\\\\<IPAddress>\\\<ShareName>**
    - Tanúsítvány gyökérkönyvtára az Azure AD-szolgáltatóhoz = ** \\ Certificates\AAD**
    - Teljes elérési út = ** \\ \\ \<IPAddress> \\ \<ShareName> \Certificates\AAD**

    > [!IMPORTANT]
    > `Start-SecretRotation`A későbbi futtatásakor a rendszer ellenőrzi a mappa szerkezetét. A nem megfelelő mappastruktúrát a következő hibaüzenetet fogja kialakítani:
    >
    > ```powershell
    > Cannot bind argument to parameter 'Path' because it is null.
    > + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
    > + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
    > + PSComputerName        : xxx.xxx.xxx.xxx
    > ```

7. Másolja ki a #2 lépésben létrehozott helyettesítő külső tanúsítványok új készletét a következő lépésben **létrehozott \\ \<IdentityProvider> \Certificates** könyvtárra: #6. Ügyeljen rá, hogy kövesse a `cert.<regionName>.<externalFQDN>` következő formátumot: \<CertName\> . 

    Az alábbi példa egy, az Azure AD-identitás-szolgáltatóhoz tartozó mappastruktúrát szemlélteti:
    ```powershell
        <ShareName>
            │
            └───Certificates
                  └───AAD
                      ├───ACSBlob
                      │       <CertName>.pfx
                      │
                      ├───ACSQueue
                      │       <CertName>.pfx
                      │
                      ├───ACSTable
                      │       <CertName>.pfx
                      │
                      ├───Admin Extension Host
                      │       <CertName>.pfx
                      │
                      ├───Admin Portal
                      │       <CertName>.pfx
                      │
                      ├───ARM Admin
                      │       <CertName>.pfx
                      │
                      ├───ARM Public
                      │       <CertName>.pfx
                      │
                      ├───KeyVault
                      │       <CertName>.pfx
                      │
                      ├───KeyVaultInternal
                      │       <CertName>.pfx
                      │
                      ├───Public Extension Host
                      │       <CertName>.pfx
                      │
                      └───Public Portal
                              <CertName>.pfx

    ```

## <a name="rotate-external-secrets"></a>Külső titkok elforgatása

A külső titkok elforgatásához hajtsa végre az alábbi lépéseket:

1. A titkok elforgatásához használja az alábbi PowerShell-szkriptet. A parancsfájlhoz hozzá kell férnie egy kiemelt jogosultságú végpont (PEP) munkamenethez. A PEP egy távoli PowerShell-munkameneten keresztül érhető el a PEP-t futtató virtuális gépen (VM). Ha integrált rendszert használ, a PEP három példánya van, amelyek mindegyike egy virtuális gépen (előtag – ERCS01, előtag-ERCS02 vagy előtag-ERCS03) belül fut különböző gazdagépeken. Ha a ASDK használja, ez a virtuális gép neve AzS-ERCS01. Az értékek frissítése a `<placeholder>` Futtatás előtt:

    ```powershell
    # Create a PEP Session
    winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
    $PEPCreds = Get-Credential
    $PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS_Machine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    # Run Secret Rotation
    $CertPassword = ConvertTo-SecureString "<Cert_Password>" -AsPlainText -Force
    $CertShareCreds = Get-Credential
    $CertSharePath = "<Network_Path_Of_CertShare>"
    Invoke-Command -Session $PEPSession -ScriptBlock {
        Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
    }
    Remove-PSSession -Session $PEPSession
    ```

    A szkript a következő lépéseket hajtja végre:

    - Létrehoz egy PowerShell-munkamenetet a [privilegizált végponttal](azure-stack-privileged-endpoint.md) a **CloudAdmin** -fiók használatával, és változóként tárolja a munkamenetet. Ezt a változót paraméterként használjuk a következő lépésben. 

    - Futtatja a [Meghívási parancsot](/powershell/module/microsoft.powershell.core/Invoke-Command), és a (z `-Session` ) paraméterként átadja a PEP-munkamenet változót.

    - A `Start-SecretRotation` következő paraméterek használatával fut a PEP-munkamenetben:
        - `-PfxFilesPath`: A korábban létrehozott tanúsítványok könyvtárának hálózati elérési útja.  
        - `-PathAccessCredential`: Az PSCredential objektum a megosztás hitelesítő adataihoz.
        - `-CertificatePassword`: A létrehozott pfx-tanúsítványfájl jelszavának biztonságos karakterlánca.

2. A külső titkos kód elforgatása körülbelül egy órát vesz igénybe. A sikeres befejezést követően a konzol a következővel fog megjelenni `ActionPlanInstanceID ... CurrentStatus: Completed` : `DONE` . Távolítsa el a tanúsítványokat az előfeltételek szakaszban létrehozott megosztásból, és tárolja őket a biztonságos biztonsági mentési helyükön.

    > [!Note]
    > Ha a titkos kód elforgatása meghiúsul, kövesse a hibaüzenetben található utasításokat, és futtassa újra `Start-SecretRotation` a `-ReRun` paraméterrel.
    >
    >```powershell
    >Start-SecretRotation -ReRun
    >```  
    >
    >Ha ismétlődő titkos rotációs hibát tapasztal, forduljon az ügyfélszolgálathoz.

## <a name="rotate-internal-secrets"></a>Belső titkok elforgatása

A belső titkos kód elforgatása csak akkor szükséges, ha azt gyanítja, hogy az egyik sérült, vagy lejárati riasztást kapott. A pre-1811 verziók a függőben lévő belső tanúsítványokra vagy titkos kódokra vonatkozó riasztásokat is láthatnak. Ezek a riasztások pontatlanok, és figyelmen kívül hagyhatók, és ismert probléma történt a 1811-es megoldásban. A belső titkok nem járnak le, kivéve, ha a környezet két évig aktív.

Hivatkozzon a PowerShell-szkriptre a [külső titkok elforgatása](#rotate-external-secrets)2. lépésében. A parancsfájl egy példát mutat be, amely a belső titkos elforgatáshoz alkalmazkodik azáltal, hogy néhány módosítást végrehajt a következő lépések végrehajtásához:

1. A "titkos kód futtatása" szakaszban adja hozzá a `-Internal` (z) paramétert a [Start-SecretRotation parancsmaghoz](../reference/pep-2002/start-secretrotation.md), például:

    ```powershell
    # Run Secret Rotation
    ...
    Invoke-Command -Session $PEPSession -ScriptBlock {
        Start-SecretRotation -Internal
    }
    ...
    ```

    ::: moniker range="<azs-1811"
    > [!Note]
    > Az 1811 előtti verziókban nincs szükség a `-Internal` jelzőre. 
    ::: moniker-end

3. A sikeres befejezést követően a konzol megjelenik `ActionPlanInstanceID ... CurrentStatus: Completed` , majd egy `DONE`

    > [!Note]
    > Ha a titkos kód elforgatása meghiúsul, kövesse a hibaüzenetben található utasításokat, és futtassa újra `Start-SecretRotation` a  `-Internal` és a `-ReRun` paramétereket.  
    >
    >```powershell
    >Start-SecretRotation -Internal -ReRun
    >```
    >
    > Ha ismétlődő titkos rotációs hibát tapasztal, forduljon az ügyfélszolgálathoz.

## <a name="update-the-bmc-credential"></a>A BMC hitelesítő adatainak frissítése

A alaplapi-kezelő vezérlő figyeli a kiszolgálók fizikai állapotát. A BMC felhasználói fiók nevének és jelszavának frissítéséhez tekintse meg az eredeti berendezésgyártó (OEM) hardver gyártójával kapcsolatos utasításokat.

>[!NOTE]
> A SZÁMÍTÓGÉPGYÁRTÓ további felügyeleti alkalmazásokat is biztosíthat. Más felügyeleti alkalmazások felhasználónevének vagy jelszavának frissítése nincs hatással a BMC-felhasználónévre vagy-jelszóra. 

::: moniker range="<azs-1910"
1. Frissítse a BMC-t az Azure Stack hub fizikai kiszolgálókon az OEM-utasítások követésével. A környezet minden egyes BMC-beli felhasználónevének és jelszavának azonosnak kell lennie. A BMC-felhasználónevek száma nem lehet hosszabb 16 karakternél.
::: moniker-end

::: moniker range=">=azs-1910"
1. Már nem szükséges, hogy először frissítse a BMC hitelesítő adatait az Azure Stack hub fizikai kiszolgálókon az OEM-utasítások követésével. A környezet minden egyes BMC-beli felhasználónevének és jelszavának azonosnak kell lennie, és nem lehet hosszabb 16 karakternél. 
::: moniker-end

2. Nyisson meg egy kiemelt jogosultságú végpontot Azure Stack hub-munkamenetekben. Útmutatásért lásd: [a privilegizált végpont használata Azure stack központban](azure-stack-privileged-endpoint.md). 

3. Egy emelt szintű végpont-munkamenet megnyitása után futtassa az alábbi PowerShell-parancsfájlok egyikét, amely a set-BmcCredential futtatásához Invoke-Command használ. Ha a választható-BypassBMCUpdate paramétert használja a set-BMCCredential, a BMC-ben a hitelesítő adatok nem frissülnek. Csak az Azure Stack hub belső adattár frissül. Adja át a Kiemelt végponti munkamenet-változót paraméterként.

    Íme egy példa egy PowerShell-szkriptre, amely a Felhasználónév és a jelszó megadását kéri: 

    ```powershell
    # Interactive Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPCreds = Get-Credential "<Domain>\CloudAdmin" -Message "PEP Credentials"
    $NewBmcPwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBmcUser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

    A felhasználónevet és a jelszót is elvégezheti a változókban, ami kevésbé biztonságos lehet:

    ```powershell
    # Static Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEPPwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PEPCreds = New-Object System.Management.Automation.PSCredential ($PEPUser, $PEPPwd)
    $NewBmcPwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force
    $NewBmcUser = "<New BMC User name>"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="reference-start-secretrotation-cmdlet"></a>Hivatkozás: Start-SecretRotation parancsmag

A [Start-SecretRotation parancsmag](../reference/pep-2002/start-secretrotation.md) egy Azure stack hub-rendszer infrastruktúra-titkait elforgatja. Ezt a parancsmagot csak az Azure Stack hub privilegizált végpontján lehet végrehajtani, egy parancsfájl-blokk használatával, amely a (z  `Invoke-Command` ) paraméterben a PEP-munkamenetet továbbítja `-Session` . Alapértelmezés szerint csak az összes külső hálózati infrastruktúra-végpont tanúsítványait forgatja.

| Paraméter | Típus | Kötelező | Pozíció | Alapértelmezett | Leírás |
|--|--|--|--|--|--|
| `PfxFilesPath` | Sztring  | Hamis  | Elemzi  | Nincsenek  | A **\Certificates** könyvtár fájlmegosztás elérési útja, amely az összes külső hálózati végpont tanúsítványát tartalmazza. Csak külső titkok elforgatásakor szükséges. A befejező könyvtárnak **\Certificates**kell lennie. |
| `CertificatePassword` | SecureString | Hamis  | Elemzi  | Nincsenek  | A-PfXFilesPath megadott összes tanúsítvány jelszava. Kötelező érték, ha a PfxFilesPath a külső titkos kódok elforgatásakor van megadva. |
| `Internal` | Sztring | Hamis | Elemzi | Nincsenek | A belső jelzőt csak akkor kell használni, amikor egy Azure Stack hub operátor belső infrastruktúra-titkokat kíván forgatni. |
| `PathAccessCredential` | PSCredential | Hamis  | Elemzi  | Nincsenek  | Az összes külső hálózati végpont tanúsítványát tartalmazó **\Certificates** könyvtár fájlmegosztás tartozó PowerShell-hitelesítő adat. Csak külső titkok elforgatásakor szükséges.  |
| `ReRun` | Kapcsolóparaméter | Hamis  | Elemzi  | Nincsenek  | A rendszer a sikertelen kísérlet után újrapróbálkozik a titkos kód megfordításával. |

### <a name="syntax"></a>Syntax

#### <a name="for-external-secret-rotation"></a>Külső titkos kód elforgatásához

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential <PSCredential>] [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>Belső titkos kód elforgatásához

```powershell
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>A külső titkos kód elforgatásának újrafuttatása

```powershell
Start-SecretRotation [-ReRun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>A belső titok elforgatásának újrafuttatása

```powershell
Start-SecretRotation [-ReRun] [-Internal]
```

### <a name="examples"></a>Példák

#### <a name="rotate-only-internal-infrastructure-secrets"></a>Csak belső infrastruktúra-titkok elforgatása

Ezt a parancsot az Azure Stack hub- [környezet privilegizált végpontján](azure-stack-privileged-endpoint.md)keresztül kell futtatni.

```powershell
PS C:\> Start-SecretRotation -Internal
```

Ez a parancs elforgatja az Azure Stack hub belső hálózata számára elérhető összes infrastruktúra-titkot.

#### <a name="rotate-only-external-infrastructure-secrets"></a>Csak a külső infrastruktúra titkainak elforgatása  

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {  
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

Ez a parancs elforgatja Azure Stack hub külső hálózati infrastruktúra-végpontokhoz használt TLS-tanúsítványokat.

::: moniker range="<azs-1811"
#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Belső és külső infrastruktúra-titkok elforgatása (csak**előre 1811** )

> [!IMPORTANT]
> Ez a parancs csak a **1811** -es Azure stack hub esetében érvényes, mivel az elforgatás a belső és külső tanúsítványokra van bontva.
>
> **A *1811* -től kezdve a belső és a külső tanúsítványok már nem forgathatók el.**

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

Ez a parancs elforgatja Azure Stack hub belső hálózata számára elérhető infrastruktúra-titkokat, valamint a Azure Stack hub külső hálózati infrastruktúra-végpontokhoz használt TLS-tanúsítványokat. Start-SecretRotation elforgatja az összes verem által generált titkot, és mivel vannak megadott tanúsítványok, a külső végponti tanúsítványok is el lesznek forgatva.  
::: moniker-end

## <a name="next-steps"></a>Következő lépések

[További információ a Azure Stack hub biztonságáról](azure-stack-security-foundations.md)