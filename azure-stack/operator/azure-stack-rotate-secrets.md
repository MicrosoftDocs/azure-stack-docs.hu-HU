---
title: Titkok elforgatása Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan forgathatja el a titkokat Azure Stackban.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.reviewer: ppacent
ms.author: mabrigg
ms.lastreviewed: 09/30/2019
monikerRange: '>=azs-1803'
ms.openlocfilehash: f32a25997e4336a24dfb9b673202882cff1845e9
ms.sourcegitcommit: 70147e858956443bc66b3541ec278c102bb45c07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72381458"
---
# <a name="rotate-secrets-in-azure-stack"></a>A titkok elforgatása Azure Stack

*Ezek az utasítások csak a 1803-es és újabb verziójú integrált rendszerek Azure Stack vonatkoznak. Ne kísérelje meg a titkos elforgatást a pre-1802 Azure Stack verzióknál*

A titkok segítenek a Azure Stack infrastruktúra-erőforrások és-szolgáltatások közötti biztonságos kommunikáció fenntartásában.

## <a name="overview-to-rotate-secrets"></a>A titkok elforgatásának áttekintése

1. Készítse elő a tanúsítványokat, amelyeket a rendszer titkos elforgatáshoz használ.
2. Tekintse át a Azure Stack [nyilvános kulcsú infrastruktúra tanúsítványára vonatkozó követelményeket](https://docs.microsoft.com/azure-stack/operator/azure-stack-pki-certs).
3. [Használja az emelt szintű végpontot](azure-stack-privileged-endpoint.md) , és futtassa a **test-azurestack** , és győződjön meg róla, hogy minden rendben van.  
4. További információt a [titkos kód elforgatásának előzetes lépéseivel](#pre-steps-for-secret-rotation)kapcsolatban talál.
5. [Ellenőrizze Azure stack PKI-tanúsítványokat](https://docs.microsoft.com/azure-stack/operator/azure-stack-validate-pki-certs). Győződjön meg arról, hogy nincsenek speciális karakterek a jelszóban, például `*` vagy `)`.
6. Győződjön meg arról, hogy a PFX **-titkosítás TripleDES-SHA1**. Ha problémába ütközik, tekintse meg [a Azure stack PKI-tanúsítványok gyakori problémáinak elhárítása](https://docs.microsoft.com/azure-stack/operator/azure-stack-remediate-certs#pfx-encryption)című témakört.
7. Készítse elő a mappa szerkezetét.  A [külső titkok elforgatása](https://docs.microsoft.com/azure-stack/operator/azure-stack-rotate-secrets#rotating-external-secrets) részben talál példát.
8. [A titkos kód elforgatásának elindítása](#use-powershell-to-rotate-secrets).

## <a name="rotate-secrets"></a>Titkok elforgatása

Azure Stack különböző titkokat használ a Azure Stack infrastruktúra-erőforrások és-szolgáltatások közötti biztonságos kommunikáció fenntartásához.

- **Belső titkok**

    Az Azure Stack infrastruktúra által a Azure Stack operátor beavatkozása nélkül használt összes tanúsítvány, jelszó, biztonságos karakterlánc és kulcs.

- **Külső titkok**

    Infrastruktúra-szolgáltatási tanúsítványok a Azure Stack operátor által biztosított külső szolgáltatásokhoz. A külső titkok közé tartoznak a következő szolgáltatások tanúsítványainak:

    - Felügyeleti portál
    - Nyilvános portál
    - Rendszergazda Azure Resource Manager
    - Globális Azure Resource Manager
    - Rendszergazdai kulcstartó
    - KeyVault
    - Felügyeleti bővítmény gazdagépe
    - ACS (blob-, tábla-és üzenetsor-tárolóval együtt)
    - ADFS
    - Graph
    
    @no__t – 0 – csak akkor alkalmazható, ha a környezeti identitás szolgáltatója Active Directory összevont szolgáltatások (AD FS).

> [!Note]
> Az összes többi biztonságos kulcsot és karakterláncot, beleértve a BMC-t és a jelszavakat is, a rendszergazda továbbra is manuálisan frissíti a felhasználói és rendszergazdai fiókok jelszavait.

> [!Important]
> A Azure Stack 1811-es kiadásával kezdődően a titkos elforgatás el lett különítve a belső és külső tanúsítványokhoz.

A Azure Stack infrastruktúra integritásának fenntartása érdekében a kezelőknek képesnek kell lenniük arra, hogy rendszeres időközönként elforgassák az infrastruktúra titkait a szervezet biztonsági követelményeinek megfelelő gyakorisággal.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Titkos kulcsok elforgatása új hitelesítésszolgáltatótól származó külső tanúsítványokkal

A Azure Stack a következő kontextusokban támogatja a titkos elforgatást egy új hitelesítésszolgáltatótól (CA) származó külső tanúsítványokkal:

|Telepített HITELESÍTÉSSZOLGÁLTATÓ|HITELESÍTÉSSZOLGÁLTATÓ, amely elforgatható|Támogatott|Azure Stack támogatott verziók|
|-----|-----|-----|-----|
|Önaláírt|– Vállalati|Támogatott|1903 & később|
|Önaláírt|Önaláírt|Nem támogatott||
|Önaláírt|Nyilvános<sup>@no__t – 1</sup>|Támogatott|1803 & később|
|Vállalattól|– Vállalati|Támogatott. 1803-1903: támogatott, amíg az ügyfelek ugyanazt a vállalati HITELESÍTÉSSZOLGÁLTATÓT használják, mint az üzembe helyezéskor|1803 & később|
|Vállalattól|Önaláírt|Nem támogatott||
|Vállalattól|Nyilvános<sup>@no__t – 1</sup>|Támogatott|1803 & később|
|Nyilvános<sup>@no__t – 1</sup>|– Vállalati|Támogatott|1903 & később|
|Nyilvános<sup>@no__t – 1</sup>|Önaláírt|Nem támogatott||
|Nyilvános<sup>@no__t – 1</sup>|Nyilvános<sup>@no__t – 1</sup>|Támogatott|1803 & később|

<sup>@no__t – 1</sup> Azt jelzi, hogy a nyilvános hitelesítésszolgáltatók olyanok, amelyek a Windows megbízható legfelső szintű program részét képezik. A teljes listát megtalálhatja a [Microsoft megbízható főtanúsítvány programja: résztvevők (2017. június 27.) című](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)cikkben.

## <a name="alert-remediation"></a>Riasztás szervizelése

Ha a titkos kulcsok érvényessége 30 napon belül lejár, a következő riasztások jönnek létre a felügyeleti portálon:

- A szolgáltatásfiók jelszavának lejárta folyamatban
- Függőben lévő belső tanúsítvány lejárata
- Külső tanúsítvány lejárata miatt függőben

Ha az alábbi utasítások segítségével futtatja a titkos kulcsot, a riasztások szervizelése megtörténik.

> [!Note]
> Az 1811-es előtti verziókban Azure Stack környezetek riasztásokat láthatnak a függőben lévő belső tanúsítványokra vagy a titkos kódokra vonatkozóan.
> Ezek a riasztások pontatlanok, és figyelmen kívül kell hagyni a belső titkos rotáció futtatása nélkül.
> A nem pontos belső titkos kulcsok lejárati riasztásai olyan ismert problémák, amelyek 1811 – belső titkok nem járnak le, kivéve, ha a környezet két évig aktív.

## <a name="pre-steps-for-secret-rotation"></a>A titkos kód elforgatásának előzetes lépései

   > [!IMPORTANT]
   > Ha a titkos rotációt már elvégezték a Azure Stack-környezetben, akkor a titkos elforgatás újbóli végrehajtása előtt frissítenie kell a rendszer 1811-es vagy újabb verziójára.
   > A titkos rotációt a [Kiemelt végponton](azure-stack-privileged-endpoint.md) keresztül kell végrehajtani, és Azure stack kezelői hitelesítő adatokat kell megadnia.
   > Ha a környezet Azure Stack operátora nem tudja, hogy fut-e a titkos rotáció a környezetben, frissítsen a 1811-re, és ismételje meg a titkos elforgatást.

1. Erősen ajánlott frissíteni a Azure Stack példányt a 1811-es verzióra.

    > [!Note] 
    > A 1811 előtti verziókban nem kell elforgatni a titkokat a bővítmény-gazdagép tanúsítványainak hozzáadásához. A bővítmény-gazdagép tanúsítványainak hozzáadásához kövesse a következő cikkben szereplő utasításokat: [felkészülés a bővítmény-gazdagépre Azure stack](azure-stack-extension-host-prepare.md) .

2. Az operátorok az Azure Stack titkos kulcsainak rotálása során észlelhetik és automatikusan lezárhatják a riasztásokat.  Ez a viselkedés várható, és a riasztások figyelmen kívül hagyhatók.  Az operátorok a **test-AzureStack**használatával ellenőrizhetik a riasztások érvényességét.  Azure Stack rendszerek figyelését System Center Operations Manager használó operátorok esetében a rendszer karbantartási módba helyezése megakadályozza, hogy ezek a riasztások elérjék ITSM rendszerüket, de továbbra is riasztást kapnak, ha az Azure Stack rendszer elérhetetlenné válik.

3. Értesítse a felhasználókat minden karbantartási műveletről. A szokásos karbantartási időszakok a munkaidőn kívüli időpontokban ütemezhetők. A karbantartási műveletek befolyásolhatják a felhasználói munkaterheléseket és a portálon végzett műveleteket is.

    > [!Note]
    > A következő lépések csak akkor érvényesek, ha Azure Stack külső titkokat elforgatják.

4. Futtasson AzureStack, és ellenőrizze, hogy az összes teszt kimenet kifogástalan állapotú **[-](azure-stack-diagnostic-test.md)** e a titkok elforgatása előtt.
5. Készítse elő a helyettesítő külső tanúsítványok új készletét. Az új készlet megfelel a [PKI-tanúsítványokra vonatkozó követelmények Azure Stackban](azure-stack-pki-certs.md)leírt tanúsítvány-specifikációknak. Létrehozhat egy tanúsítvány-aláírási kérelmet (CSR) az új tanúsítványok megvásárlásához vagy létrehozásához a [PKI-tanúsítványok létrehozása](azure-stack-get-pki-certs.md) című témakörben leírtak szerint, valamint a Azure stack-környezetben való használatra való felkészüléshez a [PKI-Azure stack Tanúsítványok](azure-stack-prepare-pki-certs.md). Győződjön meg arról, hogy a felkészülő tanúsítványokat a [PKI-tanúsítványok ellenőrzése](azure-stack-validate-pki-certs.md)című témakörben ismertetett lépésekkel érvényesíti.
6. Készítsen biztonsági másolatot a biztonságos biztonsági mentési helyen elforgatásra használt tanúsítványokról. Ha az elforgatás fut, majd a művelet meghiúsul, cserélje le a fájlmegosztás tanúsítványait a biztonsági másolatokra a elforgatás újrafuttatása előtt. Vegye figyelembe, hogy a biztonsági mentési helyen tárolja a biztonsági másolatokat.
7. Hozzon létre egy fájlmegosztás, amely a ERCS virtuális gépekről érhető el. A fájlmegosztás legyen olvasható és írható a **CloudAdmin** -identitáshoz.
8. Nyisson meg egy PowerShell ISE-konzolt egy olyan számítógépről, amelyhez hozzáféréssel rendelkezik a fájlmegosztás. Navigáljon a fájlmegosztás.
9. Futtassa a **[CertDirectoryMaker. ps1](https://www.aka.ms/azssecretrotationhelper)** parancsot a külső tanúsítványok számára szükséges könyvtárak létrehozásához.

> [!IMPORTANT]
> A CertDirectoryMaker szkript létrehoz egy mappastruktúrát, amely betartja a következőket:
>
> **.\Certificates\AAD** vagy ***.\Certificates\ADFS*** attól függően, hogy milyen a Azure stack használt identitás-szolgáltatója
>
> Rendkívül fontos, hogy a mappa szerkezete **HRE** vagy **ADFS** -mappákkal végződik, és az összes alkönyvtár ezen a struktúrán belül legyen; Ellenkező esetben a **Start-SecretRotation** a következőket eredményezi:
> ```powershell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> Amint láthatja, a hiba masszázs azt jelezheti, hogy probléma van a fájlmegosztás való hozzáféréssel, de a valóságban ez a mappa szerkezete, amelyet itt kényszerítenek ki.
> További információt a Microsoft AzureStack Readiness-ellenőrző- [PublicCertHelper moduljában](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1) találhat.
>
> Fontos továbbá, hogy a fájlmegosztás mappa szerkezete a **Certificates** mappában is megkezdődik, ellenkező esetben az érvényesítés sikertelen lesz.
> A fájlmegosztás csatlakoztatásának a következőhöz hasonlóan kell kinéznie: **\\ @ no__t-2 @ no__t-3IPAddress > \\ @ no__t-5ShareName > \\** , és tartalmaznia kell a **Certificates\AAD** vagy a **Certificates\ADFS** mappát.
>
> Példa:
> - Fájlmegosztás = **\\ @ no__t-2 @ no__t-3IPAddress > \\ @ no__t-5ShareName > \\**
> - CertFolder = **Certificates\AAD**
> - FullPath = **\\ @ no__t-2 @ no__t-3IPAddress > \\ @ no__t-5ShareName > \Certificates\AAD**

## <a name="rotating-external-secrets"></a>Külső titkok elforgatása

Külső titkok elforgatása:

1. Az újonnan létrehozott **\Certificates @ no__t-1 @ no__t-2IdentityProvider >** könyvtárban, amelyet az előzetes lépések során hoztak létre, helyezze el a külső tanúsítványok új készletét a címtár-struktúrában a kötelezően leírt formátumnak megfelelően. A [PKI-tanúsítványra vonatkozó követelmények Azure stack](azure-stack-pki-certs.md#mandatory-certificates)tanúsítványok szakasza.

    Példa a mappastruktúrát az Azure AD Identity Provider számára:
    ```powershell
        <ShareName>
        │   │
        │   ├───Certificates
        │   └───AAD
        │       ├───ACSBlob
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSQueue
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSTable
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Extension Host
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Portal
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Admin
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Public
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVault
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVaultInternal
        │       │       <CertName>.pfx
        │       │
        │       ├───Public Extension Host
        │       │       <CertName>.pfx
        │       │
        │       └───Public Portal
        │               <CertName>.pfx

    ```

2. Hozzon létre egy PowerShell-munkamenetet a [privilegizált végponttal](azure-stack-privileged-endpoint.md) a **CloudAdmin** -fiók használatával, és tárolja a munkameneteket változóként. Ezt a változót paraméterként fogja használni a következő lépésben.

    > [!IMPORTANT]  
    > Ne adja meg a munkamenetet, a munkamenetet változóként tárolja.

3. Futtassa a **[meghívó parancsot](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)** . Adja át az emelt szintű Endpoint PowerShell munkamenet-változót a **munkamenet** -paraméterként.

4. Futtassa a **Start-SecretRotation** a következő paraméterekkel:
    - **PfxFilesPath**  
    Adja meg a korábban létrehozott tanúsítványok könyvtárának hálózati elérési útját.  
    - **PathAccessCredential**  
    Egy PSCredential objektum a megosztás hitelesítő adataihoz.
    - **CertificatePassword**  
    Az összes létrehozott pfx-tanúsítványfájl számára használt jelszó biztonságos karakterlánca.

5. Várjon, amíg a titkok elforgatása megtörtént. A külső titkos kód elforgatása általában körülbelül egy órát vesz igénybe.

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
> A belső titkos kód elforgatása csak akkor hajtható végre, ha a belső titkos kulcsot egy rosszindulatú entitás feltörte, vagy ha riasztást kapott (a 1811-es vagy újabb verzióban), amely jelzi a belső tanúsítványok közeledő érvényességét.
> Az 1811-es előtti verziókban Azure Stack környezetek riasztásokat láthatnak a függőben lévő belső tanúsítványokra vagy a titkos kódokra vonatkozóan.
> Ezek a riasztások pontatlanok, és figyelmen kívül kell hagyni a belső titkos rotáció futtatása nélkül.
> A nem pontos belső titkos kulcsok lejárati riasztásai olyan ismert problémák, amelyek 1811 – belső titkok nem járnak le, kivéve, ha a környezet két évig aktív.

1. Hozzon létre egy PowerShell-munkamenetet a [privilegizált végponttal](azure-stack-privileged-endpoint.md).
2. A Kiemelt végpont-munkamenetben futtassa a **Start-SecretRotation-Internal**parancsot.

    > [!Note]
    > Az 1811-es verzión kívüli környezetek nem igénylik a **-belső** jelzőt. Azure stack A **Start-SecretRotation** csak a belső titkokat fogja forgatni.

3. Várjon, amíg a titkok elforgatása megtörtént.

   Ha a titkos kód elforgatása sikeresen befejeződött, a konzolon a **teljes művelet állapota jelenik meg: sikeres**.
    > [!Note]
    > Ha a titkos kód elforgatása meghiúsul, kövesse a hibaüzenet utasításait, és futtassa újra a **Start-SecretRotation** parancsot a **-Internal** és a **-újrafuttatási** paraméterekkel.  

```powershell
Start-SecretRotation -Internal -ReRun
```

Ha ismétlődő titkos rotációs hibát tapasztal, forduljon az ügyfélszolgálathoz.

## <a name="start-secretrotation-reference"></a>Start – SecretRotation-hivatkozás

Elforgatja egy Azure Stack rendszer titkait. Csak a Azure Stack privilegizált végponton hajtható végre.

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

A **Start-SecretRotation** parancsmag egy Azure stack rendszer infrastruktúra-titkait elforgatja. Alapértelmezés szerint csak az összes külső hálózati infrastruktúra-végpont tanúsítványait forgatja. Ha a belső Flag belső infrastruktúrájának titkát használja, a rendszer elforgatja. A külső hálózati infrastruktúra-végpontok elforgatásakor a **Start-SecretRotation** -t egy **meghívásos parancsfájl-** blokkolással kell futtatni, és a Azure stack környezet a **munkamenet** -paraméterként átadott privilegizált végponti munkamenetét.

### <a name="parameters"></a>Paraméterek

| Paraméter | Type (Típus) | Szükséges | Pozíció | Alapértelmezett | Leírás |
| -- | -- | -- | -- | -- | -- |
| `PfxFilesPath` | Sztring  | False (Hamis)  | Elemzi  | None  | A **\Certificates** könyvtár fájlmegosztás elérési útja, amely az összes külső hálózati végpont tanúsítványát tartalmazza. Csak külső titkok elforgatásakor szükséges. A befejező könyvtárnak **\Certificates**kell lennie. |
| `CertificatePassword` | SecureString | False (Hamis)  | Elemzi  | None  | A-PfXFilesPath megadott összes tanúsítvány jelszava. Kötelező érték, ha a PfxFilesPath a külső titkos kódok elforgatásakor van megadva. |
| `Internal` | Sztring | False (Hamis) | Elemzi | None | Belső jelzőt kell használni, amikor egy Azure Stack operátor belső infrastruktúra-titkokat kíván forgatni. |
| `PathAccessCredential` | PSCredential | False (Hamis)  | Elemzi  | None  | Az összes külső hálózati végpont tanúsítványát tartalmazó **\Certificates** könyvtár fájlmegosztás tartozó PowerShell-hitelesítő adat. Csak külső titkok elforgatásakor szükséges.  |
| `ReRun` | SwitchParameter | False (Hamis)  | Elemzi  | None  | Az újrafuttatást a sikertelen kísérlet után újra kell használni, ha a titkos kód elforgatása megtörtént. |

### <a name="examples"></a>Példák

#### <a name="rotate-only-internal-infrastructure-secrets"></a>Csak belső infrastruktúra-titkok elforgatása

Ezt a Azure Stack-környezet emelt [szintű végpontján](azure-stack-privileged-endpoint.md)keresztül kell futtatni.

```powershell
PS C:\> Start-SecretRotation -Internal
```

Ez a parancs elforgatja Azure Stack belső hálózatnak kitett összes infrastruktúra-titkot.

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

Ez a parancs elforgatja a Azure Stack külső hálózati infrastruktúra-végpontokhoz használt TLS-tanúsítványokat.

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Belső és külső infrastruktúra-titkok elforgatása (csak**előre 1811** )

> [!IMPORTANT]
> Ez a parancs csak a 1811-es **előtti** Azure Stackre vonatkozik, mivel a belső és külső tanúsítványok esetében az elforgatás megszakadt.
>
> **A *1811* -től kezdve a belső és a külső tanúsítványok nem forgathatók tovább!!!**

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

Ez a parancs elforgatja Azure Stack belső hálózatnak kitett összes infrastruktúra-titkot, valamint a Azure Stack külső hálózati infrastruktúra-végpontokhoz használt TLS-tanúsítványokat. A Start-SecretRotation elforgatja az összes verem által generált titkot, és mivel vannak megadott tanúsítványok, a külső végponti tanúsítványok is el lesznek forgatva.  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>A alaplapi felügyeleti vezérlő (BMC) hitelesítő adatainak frissítése

A alaplapi felügyeleti vezérlő (BMC) figyeli a kiszolgálók fizikai állapotát. A BMC felhasználói fiók nevének és jelszavának frissítéséhez tekintse meg az eredeti berendezésgyártó (OEM) hardver gyártójával kapcsolatos utasításokat. 

>[!NOTE]
> A SZÁMÍTÓGÉPGYÁRTÓ további felügyeleti alkalmazásokat is biztosíthat. Más felügyeleti alkalmazások felhasználónevének vagy jelszavának frissítése nincs hatással a BMC-felhasználónévre vagy-jelszóra.   

1. Frissítse a BMC-t a Azure Stack fizikai kiszolgálókon az OEM-utasítások követésével. A környezet minden egyes BMC-beli felhasználónevének és jelszavának azonosnak kell lennie. A BMC-felhasználónevek száma nem lehet hosszabb 16 karakternél.
2. Nyisson meg egy kiemelt jogosultságú végpontot Azure Stack munkamenetekben. Útmutatásért lásd: [a privilegizált végpont használata Azure Stackban](azure-stack-privileged-endpoint.md).
3. Miután a PowerShell-kérés módosult **[IP-cím vagy ERCS virtuális gép neve]: ps >** vagy a (z) **[AZS-ERCS01]: PS >** , a környezettől függően futtassa a `Set-BmcCredential` parancsot `Invoke-Command` futtatásával. Adja át a Kiemelt végponti munkamenet-változót paraméterként. Példa:

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

[További tudnivalók az Azure Stack biztonságáról](azure-stack-security-foundations.md)
