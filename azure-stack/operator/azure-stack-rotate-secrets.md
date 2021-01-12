---
title: Titkok elforgatása
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan forgathatja el a titkokat Azure Stack hub-ban.
author: BryanLa
ms.topic: how-to
ms.date: 01/07/2021
ms.reviewer: fiseraci
ms.author: bryanla
ms.lastreviewed: 01/07/2021
monikerRange: '>=azs-1803'
ms.openlocfilehash: ec65268a76a8616d5fea213d6c4f0551a5b5ba38
ms.sourcegitcommit: a90b146769279ffbdb09c68ca0506875a867e177
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98123697"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>Az Azure Stack Hub titkos kulcsainak rotálása

Ez a cikk útmutatást nyújt a titkos rotációs feladatok végrehajtásához, és segít a Azure Stack hub-infrastruktúra erőforrásaival és szolgáltatásaival való biztonságos kommunikáció fenntartásában.

## <a name="overview"></a>Áttekintés

Azure Stack hub titkokat használ az infrastruktúra-erőforrásokkal és-szolgáltatásokkal való biztonságos kommunikáció fenntartásához. Az Azure Stack hub-infrastruktúra integritásának fenntartása érdekében a kezelőknek képesnek kell lenniük arra, hogy a szervezet biztonsági követelményeinek megfelelő gyakorisággal forgatni tudják a titkokat.

Ha a titkok hamarosan lejárnak, a következő riasztások jönnek létre a felügyeleti portálon. A titkos kód elforgatása megoldja ezeket a riasztásokat:

- A szolgáltatásfiók jelszavának lejárta folyamatban
- Belső tanúsítvány lejárata miatt függőben
- Külső tanúsítvány lejárata miatt függőben

> [!WARNING]
> A felügyeleti portálon a lejárat előtt két fázisban aktiválva van a riasztás:
> - 90 nappal a lejárat előtt figyelmeztető riasztás jön létre.
> - 30 nappal a lejárat előtt kritikus riasztás jön létre. 
>
> ***Fontos, hogy ha* ezeket az értesítéseket kapja, a titkos elforgatást hajtsa végre. Ha ezt elmulasztja, a számítási feladatok elvesztését és a lehetséges Azure Stack hub-újratelepítést a saját költségén keresztül teheti meg.**

A riasztások figyelésével és szervizelésével kapcsolatos további információkért tekintse meg az [állapot és riasztások figyelése Azure stack központban](azure-stack-monitor-health.md)című témakört.

::: moniker range="<azs-1811"  
> [!NOTE]
> Az 1811 előtti verziókban Azure Stack hub-környezetek riasztásokat láthatnak a függőben lévő belső tanúsítványokra vagy a titkos kódokra vonatkozóan. Ezek a riasztások pontatlanok, és figyelmen kívül kell hagyni a belső titkos rotáció futtatása nélkül. A belső titkos kulcs lejárati idejének pontatlan riasztásai a 1811-ben megoldott ismert problémák. A belső titkok nem járnak le, kivéve, ha a környezet két évig aktív.
::: moniker-end

## <a name="prerequisites"></a>Előfeltételek

1. Javasoljuk, hogy először frissítse a Azure Stack hub-példányt a [legújabb verzióra](release-notes.md).

    ::: moniker range="<azs-1811"  
    >[!IMPORTANT]
    > 1811 előtti verziók esetén:
    > - Ha már elvégezte a titkos mentést, frissítenie kell az 1811-es vagy újabb verzióra, mielőtt ismét elvégezte a titkos kód elforgatását. A titkos elforgatást a [Kiemelt végponton](azure-stack-privileged-endpoint.md) keresztül kell végrehajtani, és Azure stack hub-kezelő hitelesítő adatokat kell megadnia. Ha nem tudja, hogy a titkos rotációt futtatta-e a környezetében, frissítsen a 1811-re a titkos elforgatás végrehajtása előtt.
    > - A kiterjesztésű gazdagép-tanúsítványok hozzáadásához nem kell elforgatnia a titkokat. A bővítmények gazdagép-tanúsítványainak hozzáadásához kövesse az [Azure stack hub bővítmény-gazdagépének előkészítése](azure-stack-extension-host-prepare.md) című cikk utasításait.
    ::: moniker-end

2. Értesítse a felhasználókat a tervezett karbantartási műveletekről. A szokásos karbantartási időszakok a munkaidőn kívüli időpontokban ütemezhetők. A karbantartási műveletek befolyásolhatják a felhasználói munkaterheléseket és a portálon végzett műveleteket is.

3. A titkok forgása során a kezelők megnyitva és automatikusan lezárulva láthatják a riasztásokat. Ez a viselkedés elvárt, a riasztások így figyelmen kívül hagyhatók. Az operátorok a [test-AzureStack PowerShell-parancsmag](azure-stack-diagnostic-test.md)használatával ellenőrizhetik a riasztások érvényességét. Az Azure Stack hub-rendszerek figyelését System Center Operations Manager használó operátorok esetében a rendszer karbantartási módba helyezése megakadályozza, hogy ezek a riasztások elérjék ITSM rendszerüket, de továbbra is riasztást kapnak, ha az Azure Stack hub rendszer elérhetetlenné válik.

::: moniker range=">=azs-1811"
## <a name="rotate-external-secrets"></a>Külső titkok elforgatása

> [!Important]
> Külső titkos kód elforgatása:
> - A rendszergazdának manuálisan kell elvégeznie a **nem Tanúsítványos titkokat, például a biztonságos kulcsokat és a karakterláncokat** . Ide tartoznak a felhasználói és rendszergazdai fiókok jelszavai és a [hálózati kapcsolók jelszavai](azure-stack-customer-defined.md).
> - **Érték – az erőforrás-szolgáltatói (RP) titkokat** külön útmutatóban tárgyaljuk:
>    - [App Service az Azure Stack Hubon](app-service-rotate-certificates.md)
>    - [IoT Hub az Azure Stack Hubon](iot-hub-rp-rotate-secrets.md)
>    - [MySQL Azure Stack hub-on](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation)
>    - [SQL Azure Stack hub-on](azure-stack-sql-resource-provider-maintain.md#secrets-rotation)
> - A **alaplapi felügyeleti vezérlő (bmc) hitelesítő adatai** is a [jelen cikk későbbi részében ismertetett](#update-the-bmc-credential)manuális folyamat. 

Ez a szakasz a külső szolgáltatások biztonságossá tételéhez használt tanúsítványok rotációját ismerteti. Ezeket a tanúsítványokat a Azure Stack hub operátora nyújtja, a következő szolgáltatásokhoz:

- Felügyeleti portál
- Nyilvános portál
- Rendszergazda Azure Resource Manager
- Globális Azure Resource Manager
- Rendszergazda Key Vault
- Key Vault
- Felügyeleti bővítmény gazdagépe
- ACS (blob-, tábla-és üzenetsor-tárolóval együtt)
- ADFS<sup>*</sup>
- Graph<sup>*</sup>

<sup>*</sup>Active Directory összevont szolgáltatások (AD FS) használata esetén alkalmazható.

### <a name="preparation"></a>Előkészítés

A külső titkok forgása előtt:

1. Futtassa a **[`Test-AzureStack`](azure-stack-diagnostic-test.md)** PowerShell-parancsmagot a (z) `-group SecretRotationReadiness` paraméter használatával, hogy az összes teszt kimenet állapota Kifogástalan legyen a titkok elforgatása előtt.
2. Új helyettesítő külső tanúsítványok előkészítése:
   - Az új készletnek meg kell egyeznie az [Azure stack hub PKI-tanúsítvány követelményeiben](azure-stack-pki-certs.md)ismertetett tanúsítvány-specifikációkkal. 
   - Tanúsítvány-aláírási kérés (CSR) létrehozása a hitelesítésszolgáltató (CA) számára történő küldéshez. Kövesse a [tanúsítvány-aláírási kérelmek előállítása](azure-stack-get-pki-certs.md) és a Azure stack hub-környezetben való használatra való előkészítésének lépéseit a [PKI-tanúsítványok előkészítése](azure-stack-prepare-pki-certs.md)című témakörben leírtak alapján. Az Azure Stack hub a következő kontextusokban támogatja a külső tanúsítványok titkos elforgatását egy új hitelesítésszolgáltatótól (CA):

     |Forgatás a CA-ból|Elforgatás a CA-ba|Azure Stack hub verziójának támogatása|
     |-----|-----|-----|-----|
     |Self-Signed|Enterprise| 1903 & később|
     |Self-Signed|Self-Signed|Nem támogatott|
     |Self-Signed|Nyilvános<sup>*</sup>|1803 & később|
     |Enterprise|Enterprise|1803 & később; 1803-1903 ha ugyanazt a vállalati HITELESÍTÉSSZOLGÁLTATÓT használja az üzembe helyezés során|
     |Enterprise|Self-Signed|Nem támogatott|
     |Enterprise|Nyilvános<sup>*</sup>|1803 & később|
     |Nyilvános<sup>*</sup>|Enterprise|1903 & később|
     |Nyilvános<sup>*</sup>|Self-Signed|Nem támogatott|
     |Nyilvános<sup>*</sup>|Nyilvános<sup>*</sup>|1803 & később|

     <sup>*</sup>A [Windows megbízható legfelső szintű programjának](/security/trusted-root/participants-list)része.

   - Győződjön meg arról, hogy érvényesíti a felkészített tanúsítványokat a PKI- [tanúsítványok ellenőrzése](azure-stack-validate-pki-certs.md) című témakörben ismertetett lépésekkel.
   - Győződjön meg arról, hogy nincsenek speciális karakterek a jelszóban, például `*` vagy `)` .
   - Győződjön meg arról, hogy a PFX **-titkosítás TripleDES-SHA1**. Ha problémába ütközik, tekintse meg [a Azure stack hub PKI-tanúsítványok gyakori problémáinak elhárítása](azure-stack-remediate-certs.md#pfx-encryption)című témakört.

3. Biztonsági másolat készítése a biztonságos biztonsági mentési helyen történő elforgatáshoz használt tanúsítványokról. Ha az elforgatás fut, majd a művelet meghiúsul, cserélje le a fájlmegosztás tanúsítványait a biztonsági másolatokra a elforgatás újrafuttatása előtt. Tárolja a biztonsági másolatokat a biztonságos biztonsági mentési helyen.
4. Hozzon létre egy fájlmegosztás, amely a ERCS virtuális gépekről érhető el. A fájlmegosztás legyen olvasható és írható a **CloudAdmin** -identitáshoz.
5. Nyisson meg egy PowerShell ISE-konzolt egy olyan számítógépről, amelyhez hozzáféréssel rendelkezik a fájlmegosztás. Navigáljon a fájlmegosztás, ahol a külső tanúsítványok elhelyezésére szolgáló címtárakat hoz létre.
6. Töltse le **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** a hálózati fájlmegosztás, és futtassa a parancsfájlt. A szkript létrehoz egy mappastruktúrát, amely megfelel a **_.\Certificates\AAD_*_ vagy _*_.\Certificates\ADFS_*_ értéknek, az Ön személyazonossági szolgáltatójától függően. A mappa struktúrájának _* \\ Certificates mappával kell kezdődnie** , amelyet csak egy **\\ HRE** vagy **\\ ADFS** -mappa követ. Az előző struktúrában minden további alkönyvtár szerepel. Például:
    - Fájlmegosztás = **\\\\\<IPAddress>\\\<ShareName>**
    - Tanúsítvány gyökérkönyvtára az Azure AD-szolgáltatóhoz = **\\ Certificates\AAD**
    - Teljes elérési út = **\\ \\ \<IPAddress> \\ \<ShareName> \Certificates\AAD**

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

### <a name="rotation"></a>Változtatás

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

2. A külső titkos kód elforgatása körülbelül egy órát vesz igénybe. A sikeres befejezést követően a konzol egy üzenetet jelenít meg, majd a következőt `ActionPlanInstanceID ... CurrentStatus: Completed` : `DONE` . Távolítsa el a tanúsítványokat az előkészítés szakaszban létrehozott megosztásból, és tárolja őket a biztonságos biztonsági mentési helyükön.

    > [!Note]
    > Ha a titkos kód elforgatása meghiúsul, kövesse a hibaüzenetben található utasításokat, és futtassa újra `Start-SecretRotation` a `-ReRun` paraméterrel.
    >
    >```powershell
    >Start-SecretRotation -ReRun
    >```  
    >
    >Ha ismétlődő titkos rotációs hibát tapasztal, forduljon az ügyfélszolgálathoz.
::: moniker-end

## <a name="rotate-internal-secrets"></a>Belső titkok elforgatása

A belső titkok közé tartoznak az Azure Stack hub-infrastruktúra által használt tanúsítványok, jelszavak, biztonságos karakterláncok és kulcsok, az Azure Stack hub-kezelő beavatkozása nélkül. A belső titkos kód elforgatása csak akkor szükséges, ha azt gyanítja, hogy az egyik sérült, vagy lejárati riasztást kapott. 
::: moniker range="<azs-1811"  
Az 1811-es előtti üzembe helyezések riasztásokat látnak a függőben lévő belső tanúsítvány vagy a titkos lejáratok esetében. Ezek a riasztások pontatlanok, és figyelmen kívül hagyhatók, és ismert probléma történt a 1811-es megoldásban.
::: moniker-end

A belső titkok elforgatásához hajtsa végre az alábbi lépéseket:

1. Futtassa a következő PowerShell-szkriptet. Figyelje meg, hogy a belső titkos kód elforgatásakor a "titkos kód futtatása" szakasz csak a `-Internal` paramétert használja a [Start-SecretRotation parancsmaghoz](../reference/pep-2002/start-secretrotation.md):

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
        Start-SecretRotation -Internal
    }
    Remove-PSSession -Session $PEPSession
    ```

    ::: moniker range="<azs-1811"
    > [!Note]
    > Az 1811 előtti verziókban nincs szükség a `-Internal` jelzőre. 
    ::: moniker-end


2. A sikeres befejezést követően a konzol egy üzenetet jelenít meg, majd a következőt `ActionPlanInstanceID ... CurrentStatus: Completed` : `DONE` .

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
| `PfxFilesPath` | Sztring  | Hamis  | Elemzi  | Nincs  | A **\Certificates** könyvtár fájlmegosztás elérési útja, amely az összes külső hálózati végpont tanúsítványát tartalmazza. Csak külső titkok elforgatásakor szükséges. A befejező könyvtárnak **\Certificates** kell lennie. |
| `CertificatePassword` | SecureString | Hamis  | Elemzi  | Nincs  | A-PfXFilesPath megadott összes tanúsítvány jelszava. Kötelező érték, ha a PfxFilesPath a külső titkos kódok elforgatásakor van megadva. |
| `Internal` | Sztring | Hamis | Elemzi | Nincs | A belső jelzőt csak akkor kell használni, amikor egy Azure Stack hub operátor belső infrastruktúra-titkokat kíván forgatni. |
| `PathAccessCredential` | PSCredential | Hamis  | Elemzi  | Nincs  | Az összes külső hálózati végpont tanúsítványát tartalmazó **\Certificates** könyvtár fájlmegosztás tartozó PowerShell-hitelesítő adat. Csak külső titkok elforgatásakor szükséges.  |
| `ReRun` | Kapcsolóparaméter | Hamis  | Elemzi  | Nincs  | A rendszer a sikertelen kísérlet után újrapróbálkozik a titkos kód megfordításával. |

### <a name="syntax"></a>Szintaxis

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
#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Belső és külső infrastruktúra-titkok elforgatása (csak **előre 1811** )

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