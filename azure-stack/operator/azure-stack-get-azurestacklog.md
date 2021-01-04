---
title: Diagnosztikai naplók összegyűjtése a Kiemelt végponton keresztül (PEP)
description: Megtudhatja, hogyan gyűjthet diagnosztikai naplókat igény szerint Azure Stack hub-ban a felügyeleti portál vagy egy PowerShell-parancsfájl használatával.
author: justinha
ms.custom: conteperfq4
ms.topic: article
ms.date: 09/02/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 09/02/2020
ms.openlocfilehash: 48add21dfcbf5c83a525e1f0ebd6a9e2123f75e4
ms.sourcegitcommit: 076ece88c3177db321f0ae32cba1d05179ffc393
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2020
ms.locfileid: "97794158"
---
# <a name="send-azure-stack-hub-diagnostic-logs-by-using-the-privileged-endpoint-pep"></a>Az Azure Stack Hub diagnosztikai naplóinak küldése a kiemelt végpont (PEP) használatával

<!--how do you look up the PEP IP address. You look up the azurestackstampinfo.json--->

Ha Get-AzureStackLogt szeretne futtatni egy integrált rendszeren, hozzáféréssel kell rendelkeznie a privilegizált végponthoz (PEP). Az alábbi példa egy parancsfájlt futtat, amely a PEP használatával gyűjti a naplókat. Ha egy futó napló-gyűjteményt töröl egy új indításhoz, várjon 5 percet, mielőtt elindítja az új napló-gyűjteményt, és adja meg a következőt: `Remove-PSSession -Session $session` .


```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session { Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if ($session) {
    Remove-PSSession -Session $session
}
```

### <a name="examples"></a>Példák

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

* Begyűjti a naplókat az önfelügyelt Kubernetes-fürtöket (AK-t) futtató bérlői példányokról Azure Stackon. A Kubernetes-naplókat olyan formátumban kell tárolni, amely lehetővé teszi, hogy a gyűjtemény időtartománya is alkalmazható legyen rájuk. 

  ```powershell
  Get-AzureStackLog -OutputPath <Path> -InputSasUri "<Blob Service Sas URI>" -FromDate "<Beginning of the time range>" -ToDate "<End of the time range>"
  ```

  Például:

  ```powershell
  Get-AzureStackLog -OutputPath C:\KubernetesLogs -InputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>" -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2) 
  ```

* Gyűjtsön naplókat a Value-Add RPs értékhez. Az általános szintaxis a következőket használja:
 
  ```powershell
  Get-AzureStackLog -FilterByResourceProvider <<value-add RP name>>
  ```
 
  Naplók gyűjtése az SQL RP-hez: 

  ```powershell
  Get-AzureStackLog -FilterByResourceProvider SQLAdapter
  ```

  Naplók gyűjtése a MySQL RP-hez: 

  ```powershell
  Get-AzureStackLog -FilterByResourceProvider MySQLAdapter
  ```

  IoT Hub naplók összegyűjtése: 

  ```powershell
  Get-AzureStackLog -FilterByResourceProvider iothubServiceHealth
  ```
 
  Event Hubs naplók összegyűjtése:

  ```powershell
  Get-AzureStackLog -FilterByResourceProvider eventhub
  ```
 
  Naplók gyűjtése az Azure Stack Edge-hez:

  ```powershell
  Get-AzureStackLog -FilterByResourceProvide databoxedge
  ```

* Gyűjtsön naplókat, és tárolja őket a megadott Azure Storage blob-tárolóban. A művelet általános szintaxisa a következő:

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ```

  Például:

  ```powershell
  Get-AzureStackLog -OutputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>"
  ```

  > [!NOTE]
  > Ez az eljárás a naplók feltöltésére használható. Még ha nem rendelkezik elérhető SMB-megosztással vagy internet-hozzáféréssel, létrehozhat egy blob Storage-fiókot az Azure Stack hub-ban a naplók átviteléhez, majd az ügyfél használatával lekérheti a naplókat.  

  Az SAS-jogkivonat létrehozásához a Storage-fiókhoz a következő engedélyek szükségesek:

  * Hozzáférés a Blob Storage szolgáltatáshoz.
  * Hozzáférés a tároló erőforrástípus-típusához.

  A paraméterhez használandó SAS URI-érték létrehozásához `-OutputSasUri` kövesse az alábbi lépéseket:

  1. Hozzon létre egy Storage-fiókot a [cikk](/azure/storage/common/storage-quickstart-create-account)lépéseit követve.
  2. Nyissa meg a Azure Storage Explorer egy példányát.
  3. Kapcsolódjon az 1. lépésben létrehozott Storage-fiókhoz.
  4. Navigáljon a **Storage Services** **blob-tárolói** között.
  5. Válassza **az új tároló létrehozása** lehetőséget.
  6. Kattintson a jobb gombbal az új tárolóra, majd kattintson a **megosztott hozzáférés aláírásának beolvasása** elemre.
  7. A követelményektől függően adjon meg egy érvényes **kezdési** és **befejezési időpontot**.
  8. A szükséges engedélyek esetében válassza az **olvasás**, **írás** és **lista** lehetőséget.
  9. Kattintson a **Létrehozás** gombra.
  10. Közös hozzáférési aláírást fog kapni. Másolja az URL-címet, és adja meg a `-OutputSasUri` paraméternek.

### <a name="parameter-considerations"></a>Paraméterekkel kapcsolatos szempontok

* A **OutputSharePath** és a **OutputShareCredential** paraméterek a naplók a felhasználó által megadott helyen történő tárolására szolgálnak.
* A **FromDate** és a **ToDate** paraméterek egy adott időszakra vonatkozó naplók összegyűjtésére használhatók. Ha ezek a paraméterek nincsenek megadva, a rendszer alapértelmezés szerint a naplókat az elmúlt négy órára gyűjti.
* A naplók számítógép neve alapján történő szűréséhez használja a **FilterByNode** paramétert. Például:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```

* A naplók típus szerinti szűréséhez használja a **FilterByLogType** paramétert. Dönthet úgy, hogy fájl, megosztás vagy WindowsEvent alapján végez szűrést. Például:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByLogType File
    ```

* A **TimeOutInMinutes** paraméterrel állíthatja be a naplók időtúllépését. Alapértelmezés szerint 150 (2,5 óra) értékre van állítva.
* Alapértelmezés szerint le van tiltva a fájl naplófájljainak gyűjteménye. Az engedélyezéshez használja a **IncludeDumpFile** switch paramétert.
* Jelenleg a **FilterByRole** paraméterrel szűrheti a naplózási gyűjteményt a következő szerepkörök használatával:

:::row:::
   :::column span="":::

      ACS

      ACSBlob

      ACSDownloadService

      ACSFabric

      ACSFrontEnd

      ACSMetrics

      ACSMigrationService

      ACSMonitoringService

      ACSSettingsService

      ACSTableMaster

      ACSTableServer

      ACSWac

      ADFS

      ApplicationController

      ASAppGateway

      AzureBridge

      AzureMonitor

      BareMetal

      BRP

      CA

      CacheService

      Compute

      CPI

      CRP

      DeploymentMachine

      DiskRP

      Tartomány

   :::column-end:::
   :::column span="":::

      EGB

      EventAdminRP

      EventRP

      ExternalDNS

      FabricRing

      FabricRingServices

      FirstTierAggregationService

      FRP

      Átjáró

      HealthMonitoring

      HintingServiceV2

      HRP

      IBC

      InfraServiceController

      KeyVaultAdminResourceProvider

      KeyVaultControlPlane

      KeyVaultDataPlane

      KeyVaultInternalControlPlane

      KeyVaultInternalDataPlane

      KeyVaultNamingService

      MDM

      MetricsAdminRP

      MetricsRP

      MetricsServer

      MetricsStoreService

      MonAdminRP

      MonRP

   :::column-end:::
   :::column span="":::

      NC

      NonPrivilegedAppGateway

      NRP

      OboService

      OEM

      OnboardRP

      PXE

      QueryServiceCoordinator

      QueryServiceWorker

      SeedRing

      SeedRingServices

      SLB

      SQL

      SRP

      Storage

      StorageController

      URP

      SupportBridgeController

      SupportRing

      SupportRingServices

      SupportBridgeRP

      UsageBridge

      VirtualMachines

      LETT

      WASPUBLIC
   
   :::column-end:::
:::row-end:::

### <a name="additional-considerations-on-diagnostic-logs"></a>További szempontok a diagnosztikai naplókhoz

* A parancs eltarthat egy ideig, hogy a naplók milyen szerepkör (ek) gyűjtését végzik. A közreműködő tényezők közé tartozik a naplók számára megadott időtartam és a Azure Stack hub-környezet csomópontjainak száma is.
* A napló-gyűjtemény futtatásakor ellenőrizze a parancsban megadott **OutputSharePath** paraméterben létrehozott új mappát.
* Minden szerepkör saját naplókat tartalmaz az egyes zip-fájlokon belül. Az összegyűjtött naplók méretétől függően előfordulhat, hogy a naplók több zip-fájlba vannak felosztva. Ha egy ilyen szerepkörhöz egyetlen mappába szeretné kibontani az összes naplófájlt, használjon olyan eszközt, amely kibontható tömegesen. Válassza ki a szerepkörhöz tartozó összes tömörített fájlt, és válassza a **Kibontás** lehetőséget. Az adott szerepkörhöz tartozó összes naplófájl egyetlen egyesített mappába lesz kibontva.
* A **Get-AzureStackLog_Output. log** nevű fájl a tömörített naplófájlokat tartalmazó mappában is létrejön. Ez a fájl a parancs kimenetének naplója, amely a naplózási problémák elhárításához használható. Előfordulhat, hogy a naplófájl olyan `PS>TerminatingError` bejegyzéseket tartalmaz, amelyek nyugodtan figyelmen kívül hagyhatók, kivéve, ha a napló-gyűjtemény futtatása után hiányoznak a várt naplófájlok.
* Egy adott hiba kivizsgálásához több összetevőre is szükség lehet a naplókra.

  * Az összes infrastruktúra-virtuális gép rendszer-és eseménynaplói a **VirtualMachines** szerepkörben lesznek összegyűjtve.
  * Az összes gazdagép rendszer-és eseménynaplói a **BareMetal** szerepkörben lesznek összegyűjtve.
  * A rendszer a feladatátvevő fürtöt és a Hyper-V eseménynaplókat gyűjti a **tárolási** szerepkörben.
  * Az ACS-naplókat a **tároló** és az **ACS** szerepkör gyűjti.

> [!NOTE]
> A rendszer kikényszeríti a méretre és a korra vonatkozó korlátokat a gyűjtött naplókon, mivel ez elengedhetetlen a tárterület hatékony kihasználtságának biztosításához, valamint a naplók beszerzésének elkerüléséhez. A probléma diagnosztizálásakor azonban néha olyan naplókra van szükség, amelyek ezen korlátok miatt már nem léteznek. Ezért **erősen ajánlott** , hogy a naplókat egy külső tárolóhelyre (az Azure-beli Storage-fiókba, egy további helyszíni tárolóeszközre stb.), 8 – 12 óránként, a követelményektől függően pedig 1-3 hónapig őrizze meg. Győződjön meg arról is, hogy a tárolási hely titkosítva van.

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

A **meghívó-AzureStackOnDemandLog** parancsmag használatával létrehozhat igény szerinti naplókat bizonyos szerepkörökhöz (lásd a szakasz végén található listát). A parancsmag által létrehozott naplók alapértelmezés szerint nem jelennek meg a **Get-AzureStackLog** parancsmag végrehajtásakor kapott naplófájlban. Azt is javasoljuk, hogy csak akkor Gyűjtse össze ezeket a naplókat, ha a Microsoft támogatási csapata kéri.

Jelenleg a (z) `-FilterByRole` paraméterrel szűrheti a naplózási gyűjteményt a következő szerepkörök használatával:

* OEM
* NC
* SLB
* Átjáró

#### <a name="example-of-collecting-on-demand-diagnostic-logs"></a>Példa az igény szerinti diagnosztikai naplók gyűjtésére

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

### <a name="how-diagnostic-log-collection-using-the-pep-works"></a>A diagnosztikai naplók gyűjtése a PEP Works használatával

A Azure Stack hub diagnosztikai eszközei megkönnyítik és hatékonyabbá teszik a naplók gyűjtését. Az alábbi ábra a diagnosztikai eszközök működését mutatja be:

![Azure Stack hub diagnosztikai eszközök munkafolyamat-diagramja](media/azure-stack-diagnostics/get-azslogs.png)


#### <a name="trace-collector"></a>Trace Collector

A Trace Collector alapértelmezés szerint engedélyezve van, és folyamatosan fut a háttérben, hogy összegyűjtse az összes Windows esemény-nyomkövetés (ETW) naplót az Azure Stack hub Component servicesből. A ETW-naplókat egy közös helyi megosztás tárolja, amely egy öt napos korhatárt használ. Ha elérte ezt a korlátot, a rendszer törli a legrégebbi fájlokat, mert újak jönnek létre. Az egyes fájlokhoz engedélyezett alapértelmezett maximális méret 200 MB. A méret-ellenőrzési folyamat 2 percenként történik, és ha az aktuális fájl >= 200 MB, a rendszer menti, és új fájlt hoz létre. Az esemény-munkamenetek által generált teljes fájlméret 8 GB-os korláttal is rendelkezik.

#### <a name="get-azurestacklog"></a>Get-AzureStackLog

A PowerShell-parancsmag Get-AzureStackLog használható a Azure Stack hub-környezet összes összetevőjéből származó naplók összegyűjtésére. Egy felhasználó által megadott helyen tárolja őket zip-fájlokban. Ha a Azure Stack hub technikai támogatási csapatának szüksége van a naplókra, hogy segítsen a problémák megoldásában, kérheti a Get-AzureStackLog futtatását.

> [!CAUTION]
> Ezek a naplófájlok személyes azonosításra alkalmas adatokat is tartalmazhatnak. Ezt vegye figyelembe, mielőtt nyilvánosan közzéteszi a naplófájlokat.

Az alábbiakban néhány példa a begyűjtött naplózási típusokra:

* **Azure Stack hub telepítési naplói**
* **Windows-eseménynaplók**
* **Párduc-naplók**
* **Fürtök naplói**
* **Tárolási diagnosztikai naplók**
* **ETW-naplók**

Ezeket a fájlokat a rendszer összegyűjti és menti egy megosztásban Trace Collector. Ezt követően a Get-AzureStackLog a szükséges adatok gyűjtésére is felhasználhatók.

