---
title: Titkos kódok elforgatása
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan forgathatja el a titkokat Azure Stack hub-ban.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 12/13/2019
ms.reviewer: ppacent
ms.author: inhenkel
ms.lastreviewed: 12/13/2019
monikerRange: '>=azs-1802'
ms.openlocfilehash: 9eaa0ecdf3b8d544e916f5f94e106bc76dbe0e55
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77697722"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>Titkok elforgatása Azure Stack központban

*Ezek az utasítások csak Azure Stack hub integrált rendszerek 1803-es vagy újabb verziójára érvényesek. Ne kísérelje meg a titkos elforgatást az előre 1802 Azure Stack hub-verzióknál*

A titkok segítenek a Azure Stack hub-infrastruktúra erőforrásai és szolgáltatásai közötti biztonságos kommunikáció fenntartásában.

## <a name="rotate-secrets-overview"></a>A titkok elforgatása – áttekintés

1. Készítse elő a titkos rotációhoz használni kívánt tanúsítványokat.
2. Tekintse át az Azure Stack hub [nyilvánoskulcs-infrastruktúrájának tanúsítványára vonatkozó követelményeket](https://docs.microsoft.com/azure-stack/operator/azure-stack-pki-certs).
3. [Használja az emelt szintű végpontot](azure-stack-privileged-endpoint.md) , és futtassa a **test-azurestack** , és győződjön meg róla, hogy minden rendben van.  
4. Tekintse át a [titkos rotációs folyamat előzetes lépéseit](#pre-steps-for-secret-rotation).
5. [Ellenőrizze Azure stack hub PKI-tanúsítványait](https://docs.microsoft.com/azure-stack/operator/azure-stack-validate-pki-certs). Győződjön meg arról, hogy nincsenek speciális karakterek a jelszóban, például `*` vagy `)`.
6. Győződjön meg arról, hogy a PFX **-titkosítás TripleDES-SHA1**. Ha problémába ütközik, tekintse meg [a Azure stack hub PKI-tanúsítványok gyakori problémáinak elhárítása](https://docs.microsoft.com/azure-stack/operator/azure-stack-remediate-certs#pfx-encryption)című témakört.
7. Készítse elő a mappa szerkezetét.  A [külső titkok elforgatása](https://docs.microsoft.com/azure-stack/operator/azure-stack-rotate-secrets#rotating-external-secrets) részben talál példát.
8. [A titkos kód elforgatásának elindítása](#use-powershell-to-rotate-secrets).

## <a name="rotate-secrets"></a>Titkos kódok elforgatása

Azure Stack hub különböző titkokat használ az Azure Stack hub-infrastruktúra erőforrásai és szolgáltatásai közötti biztonságos kommunikáció fenntartásához.

- **Belső titkok**

    Az Azure Stack hub-infrastruktúra által a Azure Stack hub-kezelő beavatkozása nélkül használt összes tanúsítvány, jelszó, biztonságos karakterlánc és kulcs.

- **Külső titkok**

    Infrastruktúra-szolgáltatási tanúsítványok az Azure Stack hub-kezelő által biztosított külső szolgáltatásokhoz. A külső titkok közé tartoznak a következő szolgáltatások tanúsítványainak:

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
    
    \* csak akkor alkalmazható, ha a környezeti identitás szolgáltatója Active Directory összevont szolgáltatások (AD FS).

> [!Note]
> A rendszergazda továbbra is manuálisan frissíti az összes többi biztonságos kulcsot és karakterláncot, beleértve a BMC-t és a kapcsoló jelszavát, valamint a felhasználói és rendszergazdai fiók jelszavát.

> [!Important]
> A Azure Stack hub 1811-es kiadásával kezdődően a titkos rotációt a belső és külső tanúsítványok esetében elkülönítve.

Az Azure Stack hub-infrastruktúra integritásának fenntartása érdekében a kezelőknek képesnek kell lenniük arra, hogy rendszeres időközönként elforgatni az infrastruktúra titkait a szervezet biztonsági követelményeinek megfelelő gyakorisággal.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Titkos kulcsok elforgatása új hitelesítésszolgáltatótól származó külső tanúsítványokkal

Azure Stack hub a következő kontextusokban támogatja a titkos elforgatást egy új hitelesítésszolgáltatótól (CA) származó külső tanúsítványokkal:

|Telepített HITELESÍTÉSSZOLGÁLTATÓ|HITELESÍTÉSSZOLGÁLTATÓ, amely elforgatható|Támogatott|Azure Stack hub támogatott verziói|
|-----|-----|-----|-----|
|Önaláírt|– Vállalati|Támogatott|1903 & később|
|Önaláírt|Önaláírt|Nem támogatott||
|Önaláírt|A nyilvános<sup>*</sup>|Támogatott|1803 & később|
|Vállalattól|– Vállalati|Támogatott. 1803-1903: támogatott, amíg az ügyfelek ugyanazt a vállalati HITELESÍTÉSSZOLGÁLTATÓT használják, mint az üzembe helyezéskor|1803 & később|
|Vállalattól|Önaláírt|Nem támogatott||
|Vállalattól|A nyilvános<sup>*</sup>|Támogatott|1803 & később|
|Nyilvános<sup>*</sup>|– Vállalati|Támogatott|1903 & később|
|Nyilvános<sup>*</sup>|Önaláírt|Nem támogatott||
|Nyilvános<sup>*</sup>|A nyilvános<sup>*</sup>|Támogatott|1803 & később|

<sup>*</sup> Azt jelzi, hogy a nyilvános hitelesítésszolgáltatók olyanok, amelyek a Windows megbízható legfelső szintű program részét képezik. A teljes listát megtalálhatja a [Microsoft megbízható főtanúsítvány programja: résztvevők (2017. június 27.) című](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)cikkben.

## <a name="fixing-alerts"></a>Riasztások javítása

Ha a titkos kulcsok érvényessége 30 napon belül lejár, a következő riasztások jönnek létre a felügyeleti portálon:

- A szolgáltatásfiók jelszavának lejárta folyamatban
- Függőben lévő belső tanúsítvány lejárata
- Külső tanúsítvány lejárata miatt függőben

Ha az alábbi utasítások segítségével futtatja a titkos kódot, a riasztások kijavítása megtörténik.

> [!Note]
> Az 1811 előtti verziókban Azure Stack hub-környezetek riasztásokat láthatnak a függőben lévő belső tanúsítványokra vagy a titkos kódokra vonatkozóan. Ezek a riasztások pontatlanok, és figyelmen kívül kell hagyni a belső titkos rotáció futtatása nélkül. A belső titkos kulcs lejárati idejének pontatlan riasztásai a 1811-ben megoldott ismert problémák. A belső titkok nem járnak le, kivéve, ha a környezet két évig aktív.

## <a name="pre-steps-for-secret-rotation"></a>A titkos kód elforgatásának előzetes lépései

   > [!IMPORTANT]
   > Ha a titkos rotációt már elvégezték a Azure Stack hub-környezetben, akkor a titkos rotáció ismételt végrehajtása előtt frissítenie kell a rendszer 1811-es vagy újabb verziójára. A titkos elforgatást a [Kiemelt végponton](azure-stack-privileged-endpoint.md) keresztül kell végrehajtani, és Azure stack hub-kezelő hitelesítő adatokat kell megadnia. Ha a környezet Azure Stack hub-kezelő (k) nem tudja, hogy fut-e a titkos rotáció a környezetben, frissítsen az 1811-re, és ismételje meg a titkos kód megfordítását.

1. Erősen ajánlott frissíteni az Azure Stack hub-példányt a 1811-es verzióra.

    > [!Note]
    > A 1811 előtti verziókban nem kell elforgatni a titkokat a bővítmény-gazdagép tanúsítványainak hozzáadásához. A bővítmények gazdagép-tanúsítványainak hozzáadásához kövesse az [Azure stack hub bővítmény-gazdagépének előkészítése](azure-stack-extension-host-prepare.md) című cikk utasításait.

2. A kezelők megnyitják a riasztásokat, és automatikusan lezárulnak Azure Stack hub titkos kulcsainak forgása során.  Ez a viselkedés várható, és a riasztások figyelmen kívül hagyhatók.  Az operátorok a **test-AzureStack**használatával ellenőrizhetik a riasztások érvényességét.  Az Azure Stack hub-rendszerek figyelését System Center Operations Manager használó operátorok esetében a rendszer karbantartási módba helyezése megakadályozza, hogy ezek a riasztások elérjék ITSM rendszerüket, de továbbra is riasztást kapnak, ha az Azure Stack hub rendszer elérhetetlenné válik.

3. Értesítse a felhasználókat minden karbantartási műveletről. A szokásos karbantartási időszakok a munkaidőn kívüli időpontokban ütemezhetők. A karbantartási műveletek befolyásolhatják a felhasználói munkaterheléseket és a portálon végzett műveleteket is.

    > [!Note]
    > A következő lépések csak akkor érvényesek, ha a Azure Stack hub külső titkait elforgatja.

4. Futtassa a **[test-AzureStack](azure-stack-diagnostic-test.md)** , és győződjön meg róla, hogy az összes teszt kimenet kifogástalan állapotú a titkok elforgatása előtt.
5. Készítse elő a helyettesítő külső tanúsítványok új készletét. Az új készlet megfelel az [Azure stack hub PKI-tanúsítvány követelményeiben](azure-stack-pki-certs.md)ismertetett tanúsítvány-specifikációknak. Létrehozhat egy tanúsítvány-aláírási kérelmet (CSR) az új tanúsítványok megvásárlásához vagy létrehozásához a [PKI-tanúsítványok létrehozása](azure-stack-get-pki-certs.md) című témakörben leírtak szerint, valamint a Azure stack hub-környezetben való használatra való felkészülést a [Azure stack hub PKI-tanúsítványok előkészítésének](azure-stack-prepare-pki-certs.md)lépései című témakörben leírtak alapján. Győződjön meg arról, hogy a felkészülő tanúsítványokat a [PKI-tanúsítványok ellenőrzése](azure-stack-validate-pki-certs.md)című témakörben ismertetett lépésekkel érvényesíti.
6. Biztonsági másolat készítése a biztonságos biztonsági mentési helyen történő elforgatáshoz használt tanúsítványokról. Ha az elforgatás fut, majd a művelet meghiúsul, cserélje le a fájlmegosztás tanúsítványait a biztonsági másolatokra a elforgatás újrafuttatása előtt. Tárolja a biztonsági másolatokat a biztonságos biztonsági mentési helyen.
7. Hozzon létre egy fájlmegosztás, amely a ERCS virtuális gépekről érhető el. A fájlmegosztás legyen olvasható és írható a **CloudAdmin** -identitáshoz.
8. Nyisson meg egy PowerShell ISE-konzolt egy olyan számítógépről, amelyhez hozzáféréssel rendelkezik a fájlmegosztás. Navigáljon a fájlmegosztás.
9. Futtassa a **[CertDirectoryMaker. ps1](https://www.aka.ms/azssecretrotationhelper)** parancsot a külső tanúsítványok számára szükséges könyvtárak létrehozásához.

> [!IMPORTANT]
> A CertDirectoryMaker szkript létrehoz egy mappastruktúrát, amely betartja a következőket:
>
> **.\Certificates\AAD** vagy ***.\Certificates\ADFS*** az Azure stack hub-hoz használt identitás-szolgáltatótól függően.
>
> Rendkívül fontos, hogy a mappa szerkezete **HRE** vagy **ADFS** -mappákkal végződik, és az összes alkönyvtár ezen a struktúrán belül legyen; Ellenkező esetben a **Start-SecretRotation** a következőket eredményezi:
>
> ```powershell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> A hiba masszázs azt jelzi, hogy probléma van a fájlmegosztás való hozzáféréssel, de a valóságban ez a mappa szerkezete, amelyet itt kényszerítenek ki. További információt a Microsoft AzureStack Readiness-ellenőrző- [PublicCertHelper moduljában](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1)találhat.
>
> Fontos továbbá, hogy a fájlmegosztás-mappa szerkezete a **Certificates** mappában is megkezdődik, ellenkező esetben az érvényesítés sikertelen lesz.
> A fájlmegosztás csatlakoztatásának úgy kell kinéznie, mint a **\\\\\<IP-cím >\\\<megosztásnév >\\** , és tartalmaznia kell a mappa **Certificates\AAD** vagy **Certificates\ADFS** a belsejében.
>
> Például:
> - Fájlmegosztás = **\\\\\<ip_cím >\\\<megosztásnév >\\**
> - CertFolder = **Certificates\AAD**
> - FullPath = **\\\\\<ip_cím >\\\<megosztásnév > \Certificates\AAD**

## <a name="rotating-external-secrets"></a>Külső titkok elforgatása

Külső titkok elforgatása:

1. Az újonnan létrehozott **\Certificates\\\<IdentityProvider >** az előlépésekben létrehozott könyvtárat, amely a külső tanúsítványok új készletét helyezi el a címtár-struktúrában, a [Azure stack hub PKI-tanúsítvány követelményeinek](azure-stack-pki-certs.md#mandatory-certificates) **kötelező tanúsítványok** szakaszában leírt formátumnak megfelelően.

    Példa a mappastruktúrát az Azure AD Identity Provider számára:
    ```powershell
        <ShareName>
        │   │
        │   └───Certificates
        │         └───AAD
        │             ├───ACSBlob
        │             │       <CertName>.pfx
        │             │
        │             ├───ACSQueue
        │             │       <CertName>.pfx
        │             │
        │             ├───ACSTable
        │             │       <CertName>.pfx
        │             │
        │             ├───Admin Extension Host
        │             │       <CertName>.pfx
        │             │
        │             ├───Admin Portal
        │             │       <CertName>.pfx
        │             │
        │             ├───ARM Admin
        │             │       <CertName>.pfx
        │             │
        │             ├───ARM Public
        │             │       <CertName>.pfx
        │             │
        │             ├───KeyVault
        │             │       <CertName>.pfx
        │             │
        │             ├───KeyVaultInternal
        │             │       <CertName>.pfx
        │             │
        │             ├───Public Extension Host
        │             │       <CertName>.pfx
        │             │
        │             └───Public Portal
        │                     <CertName>.pfx

    ```

2. Hozzon létre egy PowerShell-munkamenetet a [privilegizált végponttal](azure-stack-privileged-endpoint.md) a **CloudAdmin** -fiók használatával, és tárolja a munkameneteket változóként. Ezt a változót paraméterként fogja használni a következő lépésben.

    > [!IMPORTANT]  
    > Ne adja meg a munkamenetet. A munkamenetet változóként tárolja.

3. Futtassa a **[meghívó parancsot](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)** . Adja át az emelt szintű Endpoint PowerShell munkamenet-változót a **munkamenet** -paraméterként.

4. Futtassa a **Start-SecretRotation** a következő paraméterekkel:
    - **PfxFilesPath**  
    Adja meg a korábban létrehozott tanúsítványok könyvtárának hálózati elérési útját.  
    - **PathAccessCredential**  
    Egy PSCredential objektum a megosztás hitelesítő adataihoz.
    - **CertificatePassword**  
    Az összes létrehozott pfx-tanúsítványfájl számára használt jelszó biztonságos karakterlánca.

5. Várjon, amíg a titkok elforgatása megtörtént. A külső titkos kód elforgatása körülbelül egy órát vesz igénybe.

    Ha a titkos kód elforgatása sikeresen befejeződött, a konzolon a **teljes művelet állapota jelenik meg: sikeres**.

    > [!Note]
    > Ha a titkos kód elforgatása meghiúsul, kövesse a hibaüzenet utasításait, majd futtassa újra a **Start-SecretRotation** **paramétert** .

    ```powershell
    Start-SecretRotation -ReRun
    ```

    Ha ismétlődő titkos rotációs hibát tapasztal, forduljon az ügyfélszolgálathoz.

6. A titkos elforgatás sikeres befejezése után távolítsa el a tanúsítványokat az előzetes lépésben létrehozott megosztásból, és tárolja őket a biztonságos biztonsági mentési helyükön.

## <a name="use-powershell-to-rotate-secrets"></a>A titkok elforgatása a PowerShell használatával

A következő PowerShell-példa azt mutatja be, hogy a parancsmagok és a paraméterek fussanak a titkok elforgatása érdekében.

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Run Secret Rotation
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>Csak belső titkok elforgatása

> [!Note]
> A belső titkos kód elforgatása csak akkor hajtható végre, ha a belső titkos kulcsot egy rosszindulatú entitás feltörte, vagy ha riasztást kapott (az 1811-es vagy újabb verzióban), amely jelzi a belső tanúsítványok közeledő érvényességét. Az 1811 előtti verziókban Azure Stack hub-környezetek riasztásokat láthatnak a függőben lévő belső tanúsítványokra vagy a titkos kódokra vonatkozóan. Ezek a riasztások pontatlanok, és figyelmen kívül kell hagyni a belső titkos rotáció futtatása nélkül. A belső titkos kulcs lejárati idejének pontatlan riasztásai a 1811-ben megoldott ismert problémák. A belső titkok nem járnak le, kivéve, ha a környezet két évig aktív.

1. Hozzon létre egy PowerShell-munkamenetet a [privilegizált végponttal](azure-stack-privileged-endpoint.md).
2. A Kiemelt végpont-munkamenetben futtassa a **Start-SecretRotation-Internal**parancsot.

    > [!Note]
    > Az 1811 előtti verziókban Azure Stack hub-környezetek nem igénylik a **-belső** jelzőt. A **Start-SecretRotation** csak a belső titkokat fogja forgatni.

3. Várjon, amíg a titkok elforgatása megtörtént.

   Ha a titkos kód elforgatása sikeresen befejeződött, a konzolon a **teljes művelet állapota jelenik meg: sikeres**.
    > [!Note]
    > Ha a titkos kód elforgatása meghiúsul, kövesse a hibaüzenet utasításait, és futtassa újra a **Start-SecretRotation** parancsot a **-Internal** és a **-újrafuttatási** paraméterekkel.  

```powershell
Start-SecretRotation -Internal -ReRun
```

Ha ismétlődő titkos rotációs hibát tapasztal, forduljon az ügyfélszolgálathoz.

## <a name="start-secretrotation-reference"></a>Start – SecretRotation-hivatkozás

Elforgatja egy Azure Stack hub-rendszer titkait. Csak az Azure Stack hub privilegizált végpontján hajtható végre.

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

### <a name="description"></a>Leírás

A **Start-SecretRotation** parancsmag egy Azure stack hub rendszer infrastruktúra-titkait forgatja. Alapértelmezés szerint csak az összes külső hálózati infrastruktúra-végpont tanúsítványait forgatja. Ha a-belső jelzővel van használatban, a belső infrastruktúra titka el lesz forgatva. Külső hálózati infrastruktúra-végpontok elforgatásakor a **Start-SecretRotation** -t egy **meghívásos parancsfájl-** blokkolással kell futtatni az Azure stack hub-környezet Kiemelt jogosultságú végpontjának **munkamenet-paraméterként** való átadásával.

### <a name="parameters"></a>Paraméterek

| Paraméter | Típus | Kötelező | Pozíció | Alapértelmezett | Leírás |
| -- | -- | -- | -- | -- | -- |
| `PfxFilesPath` | Sztring  | False (Hamis)  | Elemzi  | Nincs  | A **\Certificates** könyvtár fájlmegosztás elérési útja, amely az összes külső hálózati végpont tanúsítványát tartalmazza. Csak külső titkok elforgatásakor szükséges. A befejező könyvtárnak **\Certificates**kell lennie. |
| `CertificatePassword` | SecureString | False (Hamis)  | Elemzi  | Nincs  | A-PfXFilesPath megadott összes tanúsítvány jelszava. Kötelező érték, ha a PfxFilesPath a külső titkos kódok elforgatásakor van megadva. |
| `Internal` | Sztring | False (Hamis) | Elemzi | Nincs | A belső jelzőt csak akkor kell használni, amikor egy Azure Stack hub operátor belső infrastruktúra-titkokat kíván forgatni. |
| `PathAccessCredential` | PSCredential | False (Hamis)  | Elemzi  | Nincs  | Az összes külső hálózati végpont tanúsítványát tartalmazó **\Certificates** könyvtár fájlmegosztás tartozó PowerShell-hitelesítő adat. Csak külső titkok elforgatásakor szükséges.  |
| `ReRun` | SwitchParameter | False (Hamis)  | Elemzi  | Nincs  | Az újrafuttatást a sikertelen kísérlet után újra kell használni, ha a titkos kód elforgatása megtörtént. |

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
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

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

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Belső és külső infrastruktúra-titkok elforgatása (csak**előre 1811** )

> [!IMPORTANT]
> Ez a parancs csak a **1811** -es Azure stack hub esetében érvényes, mivel az elforgatás a belső és külső tanúsítványokra van bontva.
>
> **A *1811* -től kezdve a belső és a külső tanúsítványok már nem forgathatók el.**

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

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

Ez a parancs elforgatja Azure Stack hub belső hálózata számára elérhető összes infrastruktúra-titkot, valamint a Azure Stack hub külső hálózati infrastruktúra-végpontokhoz használt TLS-tanúsítványokat. A Start-SecretRotation elforgatja az összes verem által generált titkot, és mivel vannak megadott tanúsítványok, a külső végponti tanúsítványok is el lesznek forgatva.  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>A alaplapi felügyeleti vezérlő (BMC) hitelesítő adatainak frissítése

A alaplapi felügyeleti vezérlő (BMC) figyeli a kiszolgálók fizikai állapotát. A BMC felhasználói fiók nevének és jelszavának frissítéséhez tekintse meg az eredeti berendezésgyártó (OEM) hardver gyártójával kapcsolatos utasításokat.

>[!NOTE]
> A SZÁMÍTÓGÉPGYÁRTÓ további felügyeleti alkalmazásokat is biztosíthat. Más felügyeleti alkalmazások felhasználónevének vagy jelszavának frissítése nincs hatással a BMC-felhasználónévre vagy-jelszóra.

1. **1910-nál korábbi verziók**esetén: frissítse a bmc-t az Azure stack hub fizikai kiszolgálókon, az OEM-utasítások követésével. A környezet minden egyes BMC-beli felhasználónevének és jelszavának azonosnak kell lennie. A BMC-felhasználónevek száma nem lehet hosszabb 16 karakternél.

   **1910-es és újabb verzió**: már nem szükséges, hogy az OEM-utasítások követésével a Azure stack hub-beli fizikai kiszolgálókon először frissítse a bmc hitelesítő adatait. A környezet minden egyes BMC-beli felhasználónevének és jelszavának azonosnak kell lennie. A BMC-felhasználónevek száma nem lehet hosszabb 16 karakternél.

    | Paraméter | Leírás | Állapot |
    | --- | --- | --- |
    | BypassBMCUpdate | Ha a paramétert használja, a BMC-ben a hitelesítő adatok nem frissülnek. Csak az Azure Stack hub belső adattár frissül. | Optional |

2. Nyisson meg egy kiemelt jogosultságú végpontot Azure Stack hub-munkamenetekben. Útmutatásért lásd: [a privilegizált végpont használata Azure stack központban](azure-stack-privileged-endpoint.md).

3. Miután a PowerShell-kérés módosult **[IP-cím vagy ERCS virtuális gép neve]: ps >** vagy a (z) **[AZS-ERCS01]: PS >** , a környezettől függően futtassa `Set-BmcCredential` `Invoke-Command`futtatásával. Adja át a Kiemelt végponti munkamenet-változót paraméterként. Például:

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

    A statikus PowerShell-verziót a jelszavakkal is használhatja, a kódokat a következőképpen teheti meg:

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

## <a name="next-steps"></a>Következő lépések

[További információ a Azure Stack hub biztonságáról](azure-stack-security-foundations.md)
