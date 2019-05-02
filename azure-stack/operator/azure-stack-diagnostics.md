---
title: Diagnosztika az Azure Stackben
description: Hogyan gyűjtheti az Azure stack diagnosztikai naplófájlok
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 04/30/2019
ms.author: jeffgilb
ms.reviewer: adshar
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: aaa929924399eb7e999bccdaa45e5ae9ce0dfa3a
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985360"
---
# <a name="azure-stack-diagnostics-tools"></a>Azure Stack-diagnosztikai eszközök

Az Azure Stack együttműködése meg és kezelhetők az egyes összetevők nagy gyűjteménye. Ezeket az összetevőket a saját egyedi naplófájlokat hoznak létre. Ez megnehezítheti diagnosztizálás problémák egy feladat, különösen a több, az Azure Stack-összetevők használatához érkező hibákat.

A diagnosztikai eszközök segítségével, győződjön meg, hogy a napló gyűjtése mechanizmus egyszerű és hatékony. Az alábbi ábrán látható gyűjtemény eszközök hogyan jelentkezzen be az Azure Stack során:

![Az Azure Stack-diagnosztikai eszközök](media/azure-stack-diagnostics/get-azslogs.png)

## <a name="trace-collector"></a>Nyomkövetési gyűjtő

A nyomkövetési gyűjtő alapértelmezés szerint engedélyezve van, és folyamatosan fut a háttérben, az összes esemény-nyomkövetése Windows (ETW) gyűjteni az Azure Stack Komponensszolgáltatások. ETW-naplók és a egy öt nap kora korlátja egy közös helyi megosztás vannak tárolva. Ha eléri ezt a korlátot, a legrégebbi fájlok törlődnek, amikor újakat hoz létre. Alapértelmezett maximális engedélyezett az egyes fájlok mérete 200 MB. Egy mérete ellenőrzés 2 percenként történik, és ha az aktuális fájl > 200 MB = a rendszer menti és a egy új fájl jön létre. Esemény-munkamenet által létrehozott fájlok összesített mérete is van egy 8 GB-os korlátot.

## <a name="log-collection-tool"></a>Napló gyűjtése eszköz

A PowerShell-parancsmag **Get-AzureStackLog** használható gyűjteni az Azure Stack-környezet lévő valamennyi összetevőnél. Ez menti őket a zip-fájlokat egy felhasználó által megadott helyen. Az Azure Stack technikai támogatási csapatával a probléma elhárításához naplók van szüksége, ha azok megkérheti, hogy az eszköz futtatásához.

> [!CAUTION]
> Ezek a naplófájlok személyes azonosításra alkalmas adatokat (PII) tartalmazhat. Vegye figyelembe ennek előtt nyilvánosan közzé minden naplófájl.

Az alábbiakban néhány példa log típusok gyűjtött:

* **Az Azure Stack-telepítési naplók**
* **Windows-eseménynaplók**
* **Panther naplók**
* **Fürt naplóit**
* **Diagnosztikai naplók tárolása**
* **ETW-naplók**

Ezek a fájlok vannak, és nyomkövetési gyűjtő által olyan megosztáson található, menti. A **Get-AzureStackLog** PowerShell-parancsmag felhasználható gyűjtéséhez őket, amikor erre szükség van.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Get-AzureStackLog futtathatók az Azure Stack integrált rendszerek

A napló gyűjtése eszköz futtatásához egy integrált rendszer, hozzáféréssel kell rendelkeznie az emelt szintű végpontját (EGP). Íme egy példa parancsfájl segítségével futtathat az EGP naplók összegyűjtése az integrált rendszereken:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)

$shareCred = Get-Credential

$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue

Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

### <a name="run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Az Azure Stack Development Kit (ASDK) rendszeren futtassa a Get-AzureStackLog

Futtassa az alábbi lépések segítségével `Get-AzureStackLog` ASDK gazdaszámítógépen.

1. Jelentkezzen be, **AzureStack\CloudAdmin** ASDK a gazdagépen.
2. Nyisson meg egy új PowerShell-ablakot rendszergazdaként.
3. Futtassa a **Get-AzureStackLog** PowerShell-parancsmagot.

#### <a name="examples"></a>Példák

* Gyűjtse össze az összes napló összes szerepköre:

```powershell
Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred
```

* Naplók gyűjtése a virtuális gép és BareMetal szerepkörök:

```powershell
Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
```

* Gyűjtsön naplókat azokról a virtuális gép és BareMetal szerepkörök, a dátum szerinti szűrést a naplófájlok az elmúlt 8 óra:

```powershell
Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
```

* Gyűjtsön naplókat azokról a virtuális gép és BareMetal szerepkörök, a dátum szerinti szűrést a naplófájlokon 8 órával ezelőtt és 2 órával ezelőtt között az adott időszakban:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

* Naplók gyűjtése és a megadott Azure Storage blob-tárolóban tárolja őket. Ehhez a művelethez általános szintaxisa a következőképpen történik:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -OutputSasUri "<Blob service SAS Uri>"
  ```

  Példa:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -OutputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS Token>"
  ```

  A tárfiók SAS-token létrehozásához, a következő engedélyek szükségesek:

  * A Blob Storage szolgáltatáshoz való hozzáférést
  * A hozzáférést a tároló-erőforrás típusa

  Hozzon létre egy SAS Uri értéket kell használni, a `-OutputSasUri` paramétert, hajtsa végre az alábbi lépéseket:

  1. Hozzon létre egy tárfiókot, a lépések [ebben a cikkben](/azure/storage/common/storage-quickstart-create-account).
  2. Nyissa meg az Azure Storage Explorer egy példánya.
  3. Csatlakozás az 1. lépésben létrehozott tárfiókot.
  4. Navigáljon a **Blobtárolók** a **tárolószolgáltatások**.
  5. Válassza ki **hozzon létre egy új tárolót**.
  6. Kattintson a jobb gombbal az új tárolót, majd kattintson a **közös hozzáférési jogosultságkód igénylése**.
  7. Válasszon ki egy érvényes **kezdő időpont** és **befejezésének**, attól függően, a követelmények.
  8. Válassza ki a szükséges engedélyekkel, **olvasási**, **írási**, és **lista**.
  9. Kattintson a **Létrehozás** gombra.
  10. Egy közös hozzáférésű Jogosultságkód fog kapni. Másolja az URL-cím része, és adja meg, hogy a `-OutputSasUri` paraméter.
```powershell
Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
```

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>A paraméter szempontok ASDK és integrált rendszereket

* A paraméterek **OutputSharePath** és **OutputShareCredential** egy felhasználó által megadott a naplók tárolására szolgáló helyre.

* A **FromDate** és **ToDate** paraméterek használhatók naplók összegyűjtése egy adott időszakban. Ezek a paraméterek nincsenek megadva, ha vannak összegyűjtött naplók az elmúlt 4 óra alapértelmezés szerint.

* Használja a **FilterByNode** paraméter naplók szűrése a számítógép neve szerint. Példa:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```

* Használja a **FilterByLogType** paraméter típusa szerint naplók szűrése. Ha szeretné, fájl, fájlmegosztás vagy WindowsEvent alapján. Példa:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByLogType File
    ```

* Használhatja a **TimeOutInMinutes** paraméter segítségével állítsa be a naplógyűjtés időkorlátja. Időintervallumként 150 (2,5 óra) alapértelmezés szerint.
* Memóriakép-fájl naplógyűjtés alapértelmezés szerint le van tiltva. Annak engedélyezéséhez használja a **IncludeDumpFile** paraméter váltani.
* Jelenleg is használhatja a **FilterByRole** szűrőt szeretne gyűjteni a következő szerepkörök paramétert:

  |   |   |   |    |
  | - | - | - | -  |
  |ACS                   |CacheService                   |IBC                            |OEM|
  |ACSDownloadService    |Compute                        |InfraServiceController         |OnboardRP|
  |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE|
  |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator|
  |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker|
  |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing|
  |ACSMonitoringService  |Domain                         |KeyVaultInternalDataPlane      |SeedRingServices|
  |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB|
  |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL|
  |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP   |
  |ACSWac                |ExternalDNS                    |MetricsRP                      |Storage|
  |ADFS                  |FabricRing                     |MetricsServer                  |StorageController   |
  |ApplicationController |FabricRingServices             |MetricsStoreService            |URP   |
  |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |UsageBridge|
  |AzureBridge           |FRP                            |MonRP                          |VirtualMachines   |
  |AzureMonitor          |Átjáró                        |NC                             |WAS|
  |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |WASPUBLIC|
  |BRP                   |HintingServiceV2               |NRP                            |   |
  |CA                    |HRP                            |OboService                     |   |
  |   |   |   |    |

### <a name="additional-considerations"></a>Néhány fontos megjegyzés

* A parancsnak némi időre melyik szerepkör(ök) gyűjti a naplók alapján futtathatók. Hozzájáruló tényezők is a naplógyűjtés és az Azure Stack-környezet a csomópontok számát a megadott időtartam.
* Gyűjtemény futtatások naplózásához ellenőrizze a létrehozott új mappa a **OutputSharePath** a parancsban megadott paraméter.
* Minden egyes szerepkörhöz naplók belül az egyes zip-fájlokat. Az összegyűjtött naplók méretétől függően egy szerepkör előfordulhat, hogy a naplókat ossza fel több zip-fájlt. Egy szerepkör egyetlen mappába a kicsomagolt naplófájlok szeretne használni, ha egy eszköz, amely képes csomagolja ki, egyszerre több (például 7zip) használja. Válassza ki a tömörített fájlokat a szerepkörhöz, és válassza ki **itt kinyerése**. Ez unzips a szerepkör egyetlen egyesített mappában található naplófájlokat.
* Fájl neve **Get-AzureStackLog_Output.log** szintén létrejön a ZIP naplófájlokat tartalmazó mappát. Ez a fájl a parancs kimenete, amely során az Erőforrásnapló-gyűjtés problémák elhárításához használható naplóját. Egyes esetekben a naplófájl tartalmazza `PS>TerminatingError` bejegyzéseket, amelyek biztonságosan figyelmen kívül hagyható, kivéve, ha a várt naplófájlok hiányoznak a gyűjtemény futtatások naplózása.
* Egy adott hibák vizsgálatához több összetevőtől naplók lehet szükség.

  * Rendszer- és az összes infrastruktúra-beli virtuális gépek eseménynaplóinak az gyűjtött a **virtuális gép** szerepkör.
  * Rendszer- és minden gazdagép eseménynaplóit a gyűjtött a **BareMetal** szerepkör.
  * Feladatátvevő fürt és a Hyper-V-eseménynaplók az gyűjti a **tárolási** szerepkör.
  * A gyűjtött naplók az ACS a **tárolási** és **ACS** szerepköröket.

> [!NOTE]
> Méret és a korszűrő vannak korlátozva a naplókban gyűjtött, fontos, hogy annak érdekében, nem áramlanak a naplókkal a tárterület hatékony felhasználása érdekében. Azonban a probléma diagnosztizálásakor néha szüksége naplóival, amelyeket már nem létezik ezek a korlátok miatt. Így **erősen ajánlott** , hogy a külső tárhelyen (tárfiókok az Azure-ban, egy további helyi tárolóeszköz, stb.) a naplók kiürítési 8 – 12 óránként és láthatóan tartja őket van 1 – 3 hónapos, attól függően, a követelmények. Gondoskodjon arról is, a tárolási hely titkosítása.

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

Használhatja a **Invoke-AzureStackOnDemandLog** parancsmag segítségével hozzon létre igény szerinti naplózza az egyes szerepkörök (lásd a listában, ez a szakasz végén található). Ez a parancsmag által létrehozott naplók nem találhatók alapértelmezés szerint a napló kötegben végre, amikor a **Get-AzureStackLog** parancsmagot. Emellett ajánlott, hogy gyűjtse össze ezek a naplók csak akkor, ha a Microsoft támogatási csapata által kért.

Jelenleg is használhatja a `-FilterByRole` szűrőt szeretne gyűjteni a következő szerepkörök paramétert:

* OEM
* NC
* SLB
* Átjáró

#### <a name="example-of-collecting-on-demand-logs"></a>Igény szerinti naplók gyűjtésére – példa

```powershell
$ip = "<IP address of the PEP VM>" # You can also use the machine name instead of IP here.

$pwd= ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<domain name>\CloudAdmin", $pwd)

$shareCred = Get-Credential

$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue

Invoke-Command -Session $s
{
   Invoke-AzureStackOnDemandLog -Generate -FilterByRole "<on-demand role name>" #Provide the supported on-demand role name : OEM, NC, SLB , Gateway
   Get-AzureStackLog -OutputSharePath "<external share address>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate

}

if($s)
{
   Remove-PSSession $s
}
```

## <a name="next-steps"></a>További lépések

[A Microsoft Azure Stack hibáinak elhárítása](azure-stack-troubleshooting.md)
