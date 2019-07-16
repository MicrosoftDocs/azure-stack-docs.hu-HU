---
title: Titkos kulcsok az Azure Stackben rotálása |} A Microsoft Docs
description: Ismerje meg az Azure Stack titkos kulcsainak rotálása.
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
ms.date: 07/15/2019
ms.reviewer: ppacent
ms.author: mabrigg
ms.lastreviewed: 07/15/2019
ms.openlocfilehash: 681daffabda3525effc1815e6aa6657c9c7c526c
ms.sourcegitcommit: ca7e6b7b9b27d0d93ee4d5d1eeaf3113bbcea4da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68229460"
---
# <a name="rotate-secrets-in-azure-stack"></a>Azure Stack titkos kulcsainak rotálása

*Ezek az utasítások csak az Azure Stack integrált rendszerek verzió 1803, és később érvényesek. Ne kísérelje meg az 1802-előtti Azure Stack-verzión titkos rotációja*

Az Azure Stack különböző titkos kódok kezelése az Azure Stack-infrastruktúra-erőforrások és szolgáltatások közötti biztonságos kommunikációhoz használ.

- **Belső titkos kulcsok**

Az összes a tanúsítványok, jelszavak, biztonságos karakterláncok és kulcsok az Azure Stack-infrastruktúra az Azure Stack-operátorokról beavatkozása nélkül használják.

- **Külső titkos kódok**

Infrastruktúra-szolgáltatás tanúsítványok kívülre irányuló szolgáltatások, az Azure Stack – operátor által biztosított. Külső titkos kódok a tanúsítványok, a következő szolgáltatásokat tartalmazza:

- Adminisztrátori portál
- Nyilvános portálra
- Rendszergazda az Azure Resource Manager
- Globális Azure Resource Manager
- Rendszergazdai KeyVault
- KeyVault
- Rendszergazdai kiterjesztés gazdagép
- Az ACS-(beleértve a blob, table és queue storage)
- ADFS *
- Graph *

\* Csak alkalmazható, ha a környezet identitásszolgáltató Active Directory összevonási szolgáltatásokban (AD FS).

> [!Note]
> Bármely más biztonságos kulcsokat és a karakterláncokat, beleértve a BMC, és váltson a jelszavak, a rendszergazda manuálisan is frissíti a felhasználói és rendszergazdai fiókok jelszavait.

> [!Important]
> Azure Stack 1811 kiadástól kezdve, titkos Elforgatás belső és külső tanúsítványok lett elválasztva.

Az Azure Stack-infrastruktúra biztonságának fenntartása, operátorok rendszeres időközönként elforgatásának az infrastruktúra titkos kódok, amelyek a szervezeti biztonsági követelményeknek való következetes frekvencia kell.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Egy új hitelesítésszolgáltató tanúsítványokat külső titkos kódok elforgatása

Az Azure Stack egy új tanúsítvány hitelesítésszolgáltató (CA) származó külső tanúsítványaival titkos Elforgatás a következő kontextusban támogatja:

|Telepített tanúsítvány Hitelesítésszolgáltatói|Hitelesítésszolgáltató elforgatása|Támogatott|Támogatott az Azure Stack-verziók|
|-----|-----|-----|-----|
|Az önaláírt|Enterprise|Támogatott|1903 és újabb verziók|
|Az önaláírt|Az önaláírt|Nem támogatott||
|Az önaláírt|Nyilvános<sup>*</sup>|Támogatott|1803 és újabb verziók|
|Enterprise|Enterprise|Támogatott. Az 1803-1903: mindaddig, amíg az ügyfelek használják a azonos vállalati hitelesítésszolgáltató központi telepítéskor használt támogatott|1803 és újabb verziók|
|Enterprise|Az önaláírt|Nem támogatott||
|Enterprise|Nyilvános<sup>*</sup>|Támogatott|1803 és újabb verziók|
|A nyilvános<sup>*</sup>|Enterprise|Támogatott|1903 és újabb verziók|
|A nyilvános<sup>*</sup>|Az önaláírt|Nem támogatott||
|A nyilvános<sup>*</sup>|Nyilvános<sup>*</sup>|Támogatott|1803 és újabb verziók|

<sup>*</sup>Azt jelzi, hogy a nyilvános hitelesítésszolgáltatók azokat, amelyek a Windows megbízható legfelső szintű programhoz. A teljes listát megtalálja a cikkben [a Microsoft megbízható Root Certificate Program: A résztvevők (2017. június 27.,)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Riasztás szervizelése

Ha a titkos kulcsok érvényessége 30 napon belül, a következő riasztások jönnek létre a felügyeleti portálon:

- Függőben levő fiók jelszólejárat
- Függőben lévő belső tanúsítvány lejárata
- Külső tanúsítvány lejárata miatt függőben

Az alábbi utasításokat követve titkos Elforgatás futó fog kijavítani ezeket a riasztásokat.

> [!Note]
> Az Azure Stack-környezetekben előtti-1811 verzióin függőben lévő belső tanúsítvány vagy titkos lejárhat riasztások jelenhet meg.
> Ezek a riasztások helytelenek, és figyelmen kívül lehet hagyni belső titkos Elforgatás futtatása nélkül.
> Pontatlan belső titkos kód lejáratáról riasztások egy ismert probléma, amely el nem hárul 1811 – belső titkos kódok nem járnak le, ha a környezet két évig használtak.

## <a name="pre-steps-for-secret-rotation"></a>Titkos elforgatás előtti lépések

   > [!IMPORTANT]
   > Ha a titkos Elforgatás már végre az Azure Stack környezettel majd frissítenie kell a rendszer 1811 vagy titkos Elforgatás újra végre, mielőtt újabb verzióra.
   > Titkos Elforgatás keresztül szerint kell végrehajtani a [kiemelt végponthoz](azure-stack-privileged-endpoint.md) és az Azure Stack-operátorokról hitelesítő adatokra van szüksége.
   > Ha a környezet az Azure Stack üzemeltetőinek nem tudja-e az adott környezet titkos Elforgatás futtatása, frissítse 1811 újra titkos Elforgatás végrehajtása előtt.

1. Azt javasoljuk, hogy az Azure Stack-példány 1811 verzióra frissíti.

    > [!Note] 
    > Előre-1811 verziók nem kell bővítmény gazdagép tanúsítványok hozzáadása a titkos kulcsok rotálására. Kövesse a cikkben lévő utasítások [előkészítése az Azure stack-bővítmény gazdagép](azure-stack-extension-host-prepare.md) bővítmény gazdagép tanúsítványok hozzáadása.

2. Az operátorok az Azure Stack titkos kulcsainak rotálása során észlelhetik és automatikusan lezárhatják a riasztásokat.  Ez az elvárt viselkedés, és a riasztások figyelmen kívül hagyható.  Operátorok futtatásával ellenőrizni tudja érvényességét, ezek a riasztások **Test-AzureStack**.  Az operátorok használatával a System Center Operations Manager figyelése az Azure Stack-rendszereket, a karbantartási módba helyezi el a rendszer megakadályozza, hogy ezek a riasztások az ITSM-rendszerekkel éri el, de továbbra is riasztás, ha az Azure Stack rendszer elérhetetlenné válik.

3. A felhasználók értesítése a karbantartási műveleteket. Normál karbantartási időszakokat, a lehető legnagyobb mértékben, során munkaidőn kívüli ütemezése. Karbantartási műveletek hatással lehet a felhasználó számítási feladatok és a webportálos műveletek.

    > [!Note]
    > A következő lépések csak akkor alkalmazható, ha az Azure Stack külső titkos kódok elforgatása.

4. Futtatás **[Test-AzureStack](azure-stack-diagnostic-test.md)** és győződjön meg róla az összes teszt kimenetek előtt titkok Elforgatás kifogástalan állapotú.
5. Készítse el új helyettesítési külső tanúsítványok. Az új készlet a tanúsítvány előírásoknak megfelel a [Azure Stack PKI-tanúsítványkövetelmények](azure-stack-pki-certs.md). Tanúsítvány-aláírási kérelem (CSR) is létrehozhat vásárol, és létrehozhatja az új tanúsítványokat, az ismertetett lépéseket követve [PKI-tanúsítványok létrehozása](azure-stack-get-pki-certs.md) és készítse elő azokat az Azure Stack környezettel lépéseivel használatra[ Készítse elő az Azure Stack PKI-tanúsítványokat](azure-stack-prepare-pki-certs.md). Ellenőrizze a tanúsítványok, az ismertetett lépéseket követve készítse elő [PKI-tanúsítványok ellenőrzése](azure-stack-validate-pki-certs.md).
6. A biztonsági mentés rotációs biztonsági mentési biztonságos helyen használt tanúsítványok Store. Az elforgatás fut, és akkor sikertelen lesz, ha cserélje le a tanúsítványokat a fájlmegosztásban a biztonsági másolatokat előtt, futtassa újra a elforgatási. Vegye figyelembe, a biztonsági másolatok megőrzése a biztonsági mentési biztonságos helyen.
7. Hozzon létre egy fájlmegosztást a ERCS virtuális gépeken érhető el. A fájlmegosztás olvasható és írható a kell lennie. a **CloudAdmin** identitás.
8. Nyisson meg egy PowerShell ISE-ben konzolt egy olyan számítógépről, ahol hozzáférhet a fájlmegosztáson. Keresse meg a fájlmegosztás.
9. Futtatás **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** a szükséges könyvtárak a külső tanúsítványok létrehozásához.

> [!IMPORTANT]
> A mappastruktúrát, amely csak a CertDirectoryMaker parancsfájl hozza létre:
>
> **.\Certificates\AAD** vagy ***.\Certificates\ADFS*** függően az identitásszolgáltató használja az Azure Stackhez
>
> Kiemelkedő fontosságú, amely a mappaszerkezetet végződik **AAD** vagy **ADFS** e szerkezetéhez belül vannak mappák és annak összes alkönyvtárát ellenkező esetben **Start-SecretRotation**fog kapja meg:
> ```powershell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> Amint láthatja a hiba masszírozó azt jelzi, hogy a fájlmegosztás eléréséhez probléma merül fel, de a valóságban a mappastruktúrát, amely itt van érvényben.
> További információ található a Microsoft AzureStack készültségi ellenőrző - [PublicCertHelper modul](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1)
>
> Emellett az is fontos, hogy a fileshare mappastruktúra kezdődik **tanúsítványok** mappa egyébként az érvényesítés is meghiúsul.
> Fájlmegosztás csatlakoztatása hasonlóan kell kinéznie **\\ \\ \<IP-cím >\\\<ShareName >\\** mappába kell tartalmaznia, és  **Certificates\AAD** vagy **Certificates\ADFS** belül.
>
> Példa:
> - Fájlmegosztás =  **\\ \\ \<IP-cím >\\\<ShareName >\\**
> - CertFolder = **Certificates\AAD**
> - FullPath =  **\\ \\ \<IP-cím >\\\<ShareName > \Certificates\AAD**

## <a name="rotating-external-secrets"></a>Külső titkos kódok elforgatása

Külső titkos kódok rotálása:

1. Az újonnan létrehozott belül **\Certificates\\\<IdentityProvider >** az üzem előtti lépésben létrehozott directory helyezze a külső tanúsítványok cseréje új készletét a könyvtárstruktúra szerint a a kötelező tanúsítványok szakaszában formátum a [Azure Stack PKI-tanúsítványkövetelmények](azure-stack-pki-certs.md#mandatory-certificates).

    Példa a mappastruktúra az AAD identitásszolgáltató számára:
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

2. Hozzon létre egy PowerShell-munkamenetet a [kiemelt végponthoz](azure-stack-privileged-endpoint.md) használatával a **CloudAdmin** fiókját, és tárolja a munkamenetek változóként. Ezt a változót a következő lépésben paraméterként fogja használni.

    > [!IMPORTANT]  
    > Adja meg a munkamenet, ne tárolja a munkamenet változóként.

3. Futtatás  **[Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)** . Adja át, a kiemelt végponthoz PowerShell munkamenet-változó a **munkamenet** paraméter.

4. Futtatás **Start-SecretRotation** a következő paraméterekkel:
    - **PfxFilesPath**  
    Adja meg a korábban létrehozott tanúsítványok címtár hálózati elérési útját.  
    - **PathAccessCredential**  
    Egy PSCredential objektumot, a hitelesítő adatokat a megosztáshoz.
    - **CertificatePassword**  
    A jelszót az összes létrehozott pfx-tanúsítvány fájlokat egy biztonságos karakterláncot.

5. Várjon, amíg a titkos kulcsok elforgatása. Külső titkos Elforgatás általában körülbelül egy órát vesz igénybe.

    Titkos kód Elforgatás sikeres befejeződése után a konzol megjeleníti **összesített művelet állapota: Sikeres**.

    > [!Note]
    > Ha titkos elforgatás nem sikerül, kövesse a hibaüzenet a, és futtassa újra a **Start-SecretRotation** az a **-ismétlés** paraméter.

    ```powershell
    Start-SecretRotation -ReRun
    ```
    Forduljon az ügyfélszolgálathoz tapasztal titkos Elforgatás hibák ismétlődik.

6. Titkos elforgatásának szögét a sikeres telepítést követően távolítsa el a tanúsítványokat az üzem előtti. lépésében létrehozott megosztás, és azok biztonságos biztonsági mentési helyen tárolja őket.

## <a name="walkthrough-of-secret-rotation"></a>Titkos kód elforgatásának szögét forgatókönyv

A következő PowerShell-példa bemutatja a parancsmagokat és futtatni kell a titkos kulcsok elforgatása paraméterek.

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

## <a name="rotating-only-internal-secrets"></a>Csak a belső titkok elforgatása

> [!Note]
> Belső titkos elforgatás csak akkor ajánlott végrehajtani, ha azt gyanítja, egy belső titkos kulcs egy rosszindulatú entitás biztonsága sérült, vagy ha kapott egy belső tanúsítványok lejárnak jelző riasztás (a build 1811 vagy újabb).
> Az Azure Stack-környezetekben előtti-1811 verzióin függőben lévő belső tanúsítvány vagy titkos lejárhat riasztások jelenhet meg.
> Ezek a riasztások helytelenek, és figyelmen kívül lehet hagyni belső titkos Elforgatás futtatása nélkül.
> Pontatlan belső titkos kód lejáratáról riasztások egy ismert probléma, amely el nem hárul 1811 – belső titkos kódok nem járnak le, ha a környezet két évig használtak.

1. Hozzon létre egy PowerShell-munkamenetet a [kiemelt végponthoz](azure-stack-privileged-endpoint.md).
2. A kiemelt végponthoz-munkamenetben futtassa **Start-SecretRotation – belső**.

    > [!Note]
    > Az Azure Stack-környezetekben előtti-1811 verzióin nem szükséges a **– belső** jelzőt. **Start-SecretRotation** elforgatása a csak belső titkos kulcsok.

3. Várjon, amíg a titkos kulcsok elforgatása.

Titkos kód Elforgatás sikeres befejeződése után a konzol megjeleníti **összesített művelet állapota: Sikeres**.
    > [!Note]
    > If secret rotation fails, follow the instructions in the error message and rerun **Start-SecretRotation** with the  **-Internal** and **-ReRun** parameters.  

```powershell
Start-SecretRotation -Internal -ReRun
```

Forduljon az ügyfélszolgálathoz tapasztal titkos Elforgatás hibák ismétlődik.

## <a name="start-secretrotation-reference"></a>Start-SecretRotation referencia

A titkos kulcsok az Azure Stack rendszer forog. Az Azure Stack kiemelt végponthoz csak hajtott végre.

### <a name="syntax"></a>Szintaxis

#### <a name="for-external-secret-rotation"></a>A külső titkos rotációja

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential <PSCredential>] [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>A belső titkos rotációja

```powershell
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>A külső, futtassa újra a titkos rotációja

```powershell
Start-SecretRotation [-ReRun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>A belső, futtassa újra a titkos rotációja

```powershell
Start-SecretRotation [-ReRun] [-Internal]
```

### <a name="description"></a>Leírás

A **Start-SecretRotation** parancsmag a infrastruktúra titkos kulcsok az Azure Stack rendszer forog. Alapértelmezés szerint csak a külső hálózat összes infrastruktúra-végpontokra tanúsítványokat forog. Ha a - belső belső jelző használt infrastruktúra titkok lesznek elforgatva. Elforgatás külső hálózati infrastruktúra-végpontokra, ha **Start-SecretRotation** kell futtatni egy **Invoke-Command** az Azure Stack környezettel való parancsprogram-blokkot a rendszerjogosultságú végpont munkamenet az átadott, a **munkamenet** paraméter.

### <a name="parameters"></a>Paraméterek

| Paraméter | Type | Szükséges | Beosztás | Alapértelmezett | Leírás |
| -- | -- | -- | -- | -- | -- |
| `PfxFilesPath` | Sztring  | False (Hamis)  | nevű  | Nincsenek  | A fájlmegosztás elérési útját a **\Certificates** könyvtárra, amelyben minden külső hálózati végpont tanúsítványokat. Csak akkor szükséges, ha külső titkos kódok elforgatása. Záró könyvtárnak kell lennie **\Certificates**. |
| `CertificatePassword` | SecureString | False (Hamis)  | nevű  | Nincsenek  | A jelszó - PfXFilesPath megadott összes tanúsítvány esetében. Kötelező érték, ha PfxFilesPath biztosított külső titkos kódok vannak-e forgatni. |
| `Internal` | Sztring | False (Hamis) | nevű | Nincsenek | Belső jelző bármikor belső infrastruktúra titkos kulcsok rotálására felhasználja az Azure Stack operátorait kell használni. |
| `PathAccessCredential` | PSCredential | False (Hamis)  | nevű  | Nincsenek  | A fájlmegosztáson az PowerShell hitelesítő adatait a **\Certificates** könyvtárra, amelyben minden külső hálózati végpont tanúsítványokat. Csak akkor szükséges, ha külső titkos kódok elforgatása.  |
| `ReRun` | SwitchParameter | False (Hamis)  | nevű  | Nincsenek  | Bármikor titkos Elforgatás van reattempted sikertelen próbálkozások után futtassa újra kell használni. |

### <a name="examples"></a>Példák

#### <a name="rotate-only-internal-infrastructure-secrets"></a>Csak a belső infrastruktúra titkok elforgatása

Ez az Azure Stack-n keresztül kell futtatni [környezet a rendszerjogosultságú végpont](azure-stack-privileged-endpoint.md).

```powershell
PS C:\> Start-SecretRotation -Internal
```

Ezzel a paranccsal az összes Azure Stack belső hálózaton vannak kitéve, az infrastruktúra titkos kulcsok forog.

#### <a name="rotate-only-external-infrastructure-secrets"></a>Csak a külső infrastruktúra titkok elforgatása  

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

Ez a parancs az Azure Stack külső hálózati infrastruktúra-végpontokra használt TLS-tanúsítványok elforgatása.

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Belső és külső infrastruktúra titkok elforgatása (**előtti-1811** csak)

> [!IMPORTANT]
> Ez a parancs csak akkor az Azure Stackhez **előtti-1811** , elforgatási felosztott belső és külső tanúsítványok.
>
> **A *1811 +* nem forgathatók el mind a belső és külső tanúsítványok bármilyen más!!!**

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

Ezzel a paranccsal az összes Azure Stack belső hálózaton vannak kitéve, az infrastruktúra titkos kulcsok, valamint az Azure Stack külső hálózati infrastruktúra-végpontokra használt TLS-tanúsítványok elforgatása. Start-SecretRotation forog verem által létrehozott összes titkos, és a tanúsítványok szolgálnak, mert külső végpont tanúsítványokat is rotációja.  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>Az alaplapi felügyeleti vezérlőnek (BMC) hitelesítő adatok frissítése

Az alaplapi felügyeleti vezérlőnek (BMC) figyeli a fizikai kiszolgálókhoz. Az előírások és a felhasználói fiók nevét és jelszavát a BMC frissítésével kapcsolatos utasításokat a számítógépgyártó (OEM) hardver szállítójával megoldástól. Frissítenie kell az Azure Stack-összetevők rendszeresen jelszavakat.

1. Frissítés az Azure Stack fizikai kiszolgálókon található bmc-k az OEM utasításai szerint. A felhasználónév és jelszó a környezetben minden egyes bmc azonos kell lennie. A BMC-felhasználónevek nem lehet hosszabb 16 karakternél.

    > [!Note]  
    > Először frissítse a alaplap felügyeleti vezérlőn a fizikai kiszolgáló; BMC hitelesítő adatok Ellenkező esetben az Azure Stack-parancs meghiúsul érvényesítése során.

2. Nyisson meg egy emelt szintű végpontot az Azure Stack-munkamenetekben. Útmutatásért lásd: [a rendszerjogosultságú végpont használata az Azure Stackben](azure-stack-privileged-endpoint.md).
3. Után a PowerShell parancssort változott **[IP-cím vagy ERCS virtuális gép neve]: PS >** vagy **[azs-ercs01]: PS >** , attól függően, a környezet futtatása `Set-BmcCredential` futtatásával `Invoke-Command`. Paraméterként adja át a kiemelt végponthoz munkamenet-változó. Példa:

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

    A statikus PowerShell-verzió adódnak a Jelszavaival, kódsorok is használhatja:

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

## <a name="next-steps"></a>További lépések

[További tudnivalók az Azure Stack biztonságáról](azure-stack-security-foundations.md)
