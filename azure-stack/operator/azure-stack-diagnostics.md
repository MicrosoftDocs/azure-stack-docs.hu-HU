---
title: Diagnosztika az Azure Stackben
description: Diagnosztikai naplófájlok gyűjtése a Azure Stack
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 05/29/2019
ms.author: justinha
ms.reviewer: adshar
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: b37c9599028cef0cc8d85bcd8004c5290604c1a3
ms.sourcegitcommit: 2063332b4d7f98ee944dd1f443847eea70eb5614
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68303123"
---
# <a name="azure-stack-diagnostics-tools"></a>Diagnosztikai eszközök Azure Stack

Azure Stack az összetevők nagy gyűjteménye, amely együttműködik egymással, és interakciót végez. Ezek az összetevők saját egyedi naplókat hoznak. Ez egy kihívást jelentő feladat kijavítását teszi elérhetővé, különösen a több, interakcióban lévő Azure Stack-összetevőtől érkező hibák esetén.

A diagnosztikai eszközök segítségével könnyen és hatékonyan biztosítható a naplók gyűjtési mechanizmusa. Az alábbi ábra azt mutatja be, hogyan működik a Azure Stack a log Collection eszközei:

![Diagnosztikai eszközök Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)

## <a name="trace-collector"></a>Trace Collector

A Trace Collector alapértelmezés szerint engedélyezve van, és folyamatosan fut a háttérben, hogy összegyűjtse az összes Windows esemény-nyomkövetés (ETW) naplót a Azure Stack Component servicesből. A ETW-naplókat egy közös helyi megosztás tárolja, amely egy öt napos korhatárt eredményez. Ha elérte ezt a korlátot, a rendszer törli a legrégebbi fájlokat, mert újak jönnek létre. Az egyes fájlokhoz engedélyezett alapértelmezett maximális méret 200 MB. A méret-ellenőrzési művelet 2 percenként történik, és ha az aktuális fájl > = 200 MB, a rendszer menti, és létrehoz egy új fájlt. Az esemény-munkamenetek által generált teljes fájlméret 8 GB-os korláttal is rendelkezik.

## <a name="log-collection-tool"></a>Log Collection eszköz

A **Get-AzureStackLog PowerShell-** parancsmag használatával gyűjthet naplókat az összes összetevőből egy Azure stack környezetben. Egy felhasználó által megadott helyen tárolja őket zip-fájlokban. Ha a Azure Stack technikai támogatási csapatnak szüksége van a naplókra a probléma megoldásához, kérheti, hogy futtassa ezt az eszközt.

> [!CAUTION]
> Ezek a naplófájlok személyes azonosításra alkalmas adatokat is tartalmazhatnak. Ezt vegye figyelembe, mielőtt nyilvánosan közzéteszi a naplófájlokat.

Az alábbiakban néhány példa a begyűjtött naplózási típusokra:

* **Központi telepítési naplók Azure Stack**
* **Windows-eseménynaplók**
* **Párduc-naplók**
* **Fürtök naplói**
* **Tárolási diagnosztikai naplók**
* **ETW-naplók**

Ezeket a fájlokat a rendszer összegyűjti és menti egy megosztásban Trace Collector. A **Get-AzureStackLog PowerShell-** parancsmag segítségével szükség esetén összegyűjtheti őket.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>A Get-AzureStackLog futtatása Azure Stack integrált rendszereken

Ha egy integrált rendszeren szeretné futtatni a log Collection eszközt, hozzáféréssel kell rendelkeznie a privilegizált végponthoz (PEP). Az alábbi példa egy olyan parancsfájlt futtat, amely a PEP használatával gyűjti a naplókat egy integrált rendszeren:

```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session { Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if ($session) {
    Remove-PSSession -Session $session
}
```

### <a name="run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Get-AzureStackLog futtatása Azure Stack Development Kit (ASDK) rendszeren

Ezekkel a lépésekkel `Get-AzureStackLog` futtathat ASDK gazdaszámítógépen.

1. Jelentkezzen be **AzureStack\CloudAdmin** -ként a ASDK-gazdaszámítógépen.
2. Nyisson meg egy új PowerShell-ablakot rendszergazdaként.
3. Futtassa a **Get-AzureStackLog** PowerShell-parancsmagot.

#### <a name="examples"></a>Példák

* Összes napló összegyűjtése az összes szerepkörhöz:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred
  ```

* Naplók gyűjtése a VirtualMachines és a BareMetal szerepkörökből:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

* VirtualMachines-és BareMetal-szerepkörökből származó naplók gyűjtése az elmúlt 8 órában a naplófájlok szűrésével:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* VirtualMachines-és BareMetal-szerepkörökből származó naplók gyűjtése, a naplófájlok dátum szerinti szűrésével a 8 órája és 2 óra között eltelt időszakra:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

* Gyűjtsön naplókat, és tárolja őket a megadott Azure Storage blob-tárolóban. A művelet általános szintaxisa a következő:

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ```

  Példa:

  ```powershell
  Get-AzureStackLog -OutputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>"
  ```

  > [!NOTE]
  > Ez az eljárás akkor lehet hasznos, ha Microsoft ügyfélszolgálatakal nyit meg egy esetet, és a rendszer kéri a naplók feltöltésére. Még ha nincs elérhető SMB-megosztás a ERCS virtuális gépről, és a ERCS virtuális gépe nem rendelkezik internet-hozzáféréssel, létrehozhat egy blob Storage-fiókot a Azure Stack a naplók átviteléhez, majd az ügyfél használatával letöltheti ezeket a naplókat, és feltöltheti azokat a Microsoftnak.  

  Az SAS-jogkivonat létrehozásához a Storage-fiókhoz a következő engedélyek szükségesek:

  * Hozzáférés a Blob Storage szolgáltatáshoz
  * Hozzáférés a tároló erőforrástípus

  A `-OutputSasUri` paraméterhez használandó sas URI-érték létrehozásához hajtsa végre a következő lépéseket:

  1. Hozzon létre egy Storage-fiókot a [cikk](/azure/storage/common/storage-quickstart-create-account)lépéseit követve.
  2. Nyissa meg a Azure Storage Explorer egy példányát.
  3. Kapcsolódjon az 1. lépésben létrehozott Storage-fiókhoz.
  4. Navigáljon a **Storage Services** **blob** -tárolói között.
  5. Válassza **az új tároló létrehozása**lehetőséget.
  6. Kattintson a jobb gombbal az új tárolóra, majd kattintson a **megosztott hozzáférés aláírásának**beolvasása elemre.
  7. A követelményektől  függően adjon meg egy érvényes kezdési és **befejezési**időpontot.
  8. A szükséges engedélyek esetében válassza az **olvasás**, **írás**és **lista**lehetőséget.
  9. Kattintson a **Létrehozás** gombra.
  10. Közös hozzáférési aláírást fog kapni. Másolja az URL-címet, és adja meg a `-OutputSasUri` paraméternek.

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>A ASDK és az integrált rendszerek paramétereinek szempontjai

* A **OutputSharePath** és a **OutputShareCredential** paraméterek a naplók a felhasználó által megadott helyen történő tárolására szolgálnak.

* A **FromDate** és a **ToDate** paraméterek egy adott időszakra vonatkozó naplók összegyűjtésére használhatók. Ha ezek a paraméterek nincsenek megadva, alapértelmezés szerint a rendszer a naplókat az elmúlt négy órára gyűjti.

* A naplók számítógép neve alapján történő szűréséhez használja a **FilterByNode** paramétert. Példa:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```

* A naplók típus szerinti szűréséhez használja a **FilterByLogType** paramétert. Dönthet úgy, hogy fájl, megosztás vagy WindowsEvent alapján végez szűrést. Példa:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByLogType File
    ```

* A **TimeOutInMinutes** paraméterrel állíthatja be a naplók időtúllépését. Alapértelmezés szerint 150 (2,5 óra) értékre van állítva.
* Alapértelmezés szerint le van tiltva a fájl naplófájljainak gyűjteménye. Az engedélyezéshez használja a **IncludeDumpFile** switch paramétert.
* Jelenleg a **FilterByRole** paraméterrel szűrheti a naplózási gyűjteményt a következő szerepkörök használatával:

  |   |   |   |    |
  | - | - | - | -  |
  |ACS                   |CA                             |HRP                            |OboService|
  |ACSBlob               |CacheService                   |IBC                            |OEM|
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
  |   |   |   |    |

### <a name="additional-considerations"></a>Néhány fontos megjegyzés

* A parancs eltarthat egy ideig, hogy a naplók milyen szerepkör (ek) gyűjtését végzik. A közreműködő tényezők közé tartozik a naplókhoz megadott időtartam és a Azure Stack környezet csomópontjainak száma is.
* A napló-gyűjtemény futtatásakor ellenőrizze a parancsban megadott **OutputSharePath** paraméterben létrehozott új mappát.
* Minden szerepkör saját naplókat tartalmaz az egyes zip-fájlokon belül. Az összegyűjtött naplók méretétől függően előfordulhat, hogy a naplók több zip-fájlba vannak felosztva. Ha egy ilyen szerepkörhöz egyetlen mappába szeretné kibontani az összes naplófájlt, használjon olyan eszközt, amely kibontható tömegesen (például 7zip). Válassza ki a szerepkörhöz tartozó összes tömörített fájlt, majd válassza a Kibontás lehetőséget. Ezzel kibontja az adott szerepkörhöz tartozó összes naplófájlt egyetlen egyesített mappában.
* A **Get-AzureStackLog_Output. log** nevű fájl a tömörített naplófájlokat tartalmazó mappában is létrejön. Ez a fájl a parancs kimenetének naplója, amely a naplózási problémák elhárításához használható. Előfordulhat, hogy a naplófájl `PS>TerminatingError` olyan bejegyzéseket tartalmaz, amelyek nyugodtan figyelmen kívül hagyhatók, kivéve, ha a napló-gyűjtemény futtatása után hiányoznak a várt naplófájlok.
* Egy adott hiba kivizsgálásához több összetevőre is szükség lehet a naplókra.

  * Az összes infrastruktúra-virtuális gép rendszer-és eseménynaplói a **VirtualMachines** szerepkörben lesznek összegyűjtve.
  * Az összes gazdagép rendszer-és eseménynaplói a **BareMetal** szerepkörben lesznek összegyűjtve.
  * A rendszer a feladatátvevő fürtöt és a Hyper-V eseménynaplókat gyűjti a **tárolási** szerepkörben.
  * Az ACS-naplókat a **tároló** és az **ACS** szerepkör gyűjti.

> [!NOTE]
> A rendszer kikényszeríti a méretre és a korra vonatkozó korlátokat a gyűjtött naplók esetében, mivel ez elengedhetetlen a tárolóhelyek hatékony kihasználtságának biztosításához, így biztosítva, hogy nem lesz elárasztva a naplókkal. A probléma diagnosztizálásakor azonban néha olyan naplókra van szükség, amelyek esetleg nem léteznek többé ezen korlátok miatt. Ezért **erősen ajánlott** , hogy a naplókat egy külső tárolóhelyre (az Azure-beli Storage-fiókba, egy további helyszíni tárolóeszközre stb.), 8 – 12 óránként, a követelményektől függően pedig 1-3 hónapig őrizze meg. Győződjön meg arról is, hogy a tárolási hely titkosítva van.

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

A **meghívó-AzureStackOnDemandLog** parancsmag használatával létrehozhat igény szerinti naplókat bizonyos szerepkörökhöz (lásd a szakasz végén található listát). A parancsmag által létrehozott naplók nem találhatók meg alapértelmezés szerint a **Get-AzureStackLog** parancsmag végrehajtásakor kapott naplófájlban. Azt is javasoljuk, hogy csak akkor Gyűjtse össze ezeket a naplókat, ha a Microsoft támogatási csapata kéri.

Jelenleg a `-FilterByRole` (z) paraméterrel szűrheti a naplózási gyűjteményt a következő szerepkörök használatával:

* OEM
* NC
* SLB
* Átjáró

#### <a name="example-of-collecting-on-demand-logs"></a>Példa az igény szerinti naplók gyűjtésére

```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session {
   Invoke-AzureStackOnDemandLog -Generate -FilterByRole "<on-demand role name>" # Provide the supported on-demand role name e.g. OEM, NC, SLB, Gateway
   Get-AzureStackLog -OutputSharePath "<external share address>" -OutputShareCredential $using:shareCred -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate
}

if ($session) {
   Remove-PSSession -Session $session
}
```

## <a name="next-steps"></a>További lépések

[A Microsoft Azure Stack hibáinak elhárítása](azure-stack-troubleshooting.md)
