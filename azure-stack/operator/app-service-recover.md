---
title: App Service helyreállítás Azure Stack hub-on
description: További információ a Azure Stack hub App Serviceának vész-helyreállításáról.
author: bryanla
ms.topic: article
ms.date: 03/21/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: e04fa2f3e9d03f1982ef67d4d19549b7b73a1f1a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77701683"
---
# <a name="app-service-recovery-on-azure-stack-hub"></a>App Service helyreállítás Azure Stack hub-on

Ez a témakör útmutatást nyújt a App Service vész-helyreállítással kapcsolatos műveletek elvégzéséhez.

A következő műveleteket kell végrehajtani a Azure Stack hub biztonsági másolatból történő App Service helyreállításához:
1. Állítsa vissza a App Service-adatbázisokat.
2. A fájlkiszolgáló megosztási tartalmának visszaállítása.
3. App Service szerepkörök és szolgáltatások visszaállítása.

Ha Azure Stack hub Storage-t használtak a Function apps Storage szolgáltatáshoz, akkor a Function apps visszaállításának lépéseit is végre kell hajtania.

## <a name="restore-the-app-service-databases"></a>A App Service adatbázisok visszaállítása
A App Service SQL Server-adatbázisokat egy éles használatra kész SQL Server példányon kell visszaállítani. 

Miután felkészítette [a SQL Server példányt](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance) a app Service adatbázisok üzemeltetéséhez, kövesse az alábbi lépéseket az adatbázisok biztonsági másolatból való visszaállításához:

1. Jelentkezzen be a visszaállított App Service-adatbázisokat futtató SQL Server a rendszergazdai engedélyekkel.
2. A következő parancsokkal állíthatja vissza a App Service adatbázisait rendszergazdai engedélyekkel futó parancssorból:
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. Győződjön meg arról, hogy mindkét App Service adatbázis visszaállítása sikeres volt, és lépjen ki SQL Server Management Studio.

> [!NOTE]
> A feladatátvételi fürtszolgáltatás meghibásodása miatti helyreállításról a [feladatátvételi fürt példányának meghibásodása esetén](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017)című témakörben talál további információt. 

## <a name="restore-the-app-service-file-share-content"></a>A App Service fájlmegosztás tartalmának visszaállítása
A [fájlkiszolgáló](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server) a app Service fájlmegosztás üzemeltetése után történő előkészítése után vissza kell állítania a bérlői fájlmegosztás tartalmát a biztonsági másolatból. A fájlok az újonnan létrehozott App Service fájlmegosztás helyére való másolásához tetszőleges módszert használhat. Ha ezt a példát a fájlkiszolgálón futtatja, a PowerShell és a Robocopy használatával csatlakozhat egy távoli megosztáshoz, és átmásolja a fájlokat a megosztásba:

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

A fájlmegosztás tartalmának másolása mellett a fájlmegosztás engedélyeit is vissza kell állítania. Az engedélyek alaphelyzetbe állításához nyisson meg egy rendszergazdai parancssort a fájlkiszolgáló számítógépen, és futtassa a **ReACL. cmd** fájlt. A **ReACL. cmd** fájl a **BCDR** könyvtárának app Service telepítési fájljaiban található.

## <a name="restore-app-service-roles-and-services"></a>Szerepkörök és szolgáltatások visszaállítása App Service
A App Service-adatbázisok és a fájlmegosztás tartalmának visszaállítása után a App Service szerepköreinek és szolgáltatásainak visszaállításához a PowerShellt kell használnia. Ezek a lépések visszaállítják App Service titkokat és szolgáltatás-konfigurációkat.  

1. Jelentkezzen be a App Service Controller **CN0-VM** virtuális gépre **roleadmin** -ként a app Service telepítésekor megadott jelszó használatával. 
    > [!TIP]
    > Az RDP-kapcsolatok engedélyezéséhez módosítania kell a virtuális gép hálózati biztonsági csoportját. 
2. Másolja a **SystemSecrets. JSON** fájlt helyileg a vezérlő virtuális gépre. A következő lépésben meg kell adnia a fájl elérési útját `$pathToExportedSecretFile` paraméterként.
3. A következő parancsok futtatásával emelt szintű PowerShell-konzol ablakban állíthatja vissza App Service szerepköröket és szolgáltatásokat:

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> A parancs befejezésekor erősen ajánlott a PowerShell-munkamenet lezárása.

## <a name="restore-function-apps"></a>Function-alkalmazások visszaállítása 
Az Azure Stack hub App Service nem támogatja a bérlői felhasználói alkalmazások vagy a fájlmegosztás tartalmán kívüli más adatokat visszaállítását. Az összes többi biztonsági mentést és helyreállítást App Service biztonsági mentési és visszaállítási műveleteken kívül kell végrehajtani. Ha Azure Stack hub Storage-t használtak a Function apps-tárolóhoz, a következő lépéseket kell végrehajtani az elveszett adatok helyreállításához:

1. Hozzon létre egy új, a függvényalkalmazás által használandó Storage-fiókot. Ez a tároló lehet Azure Stack hub Storage, Azure Storage vagy bármilyen kompatibilis tároló.
2. A tárolóhoz tartozó kapcsolatok karakterláncának beolvasása.
3. Nyissa meg a Function Portalt, és keresse meg a Function alkalmazást.
4. Keresse meg a **platform szolgáltatásai** lapot, és kattintson az **Alkalmazásbeállítások**elemre.
5. Módosítsa a **AzureWebJobsDashboard** és a **AzureWebJobsStorage** az új kapcsolódási karakterláncra, majd kattintson a **Mentés**gombra.
6. Váltson át az **áttekintésre**.
7. Indítsa újra az alkalmazást. Több próbálkozást is igénybe vehet az összes hiba törléséhez.

## <a name="next-steps"></a>További lépések
[Az Azure Stack hub App Service áttekintése](azure-stack-app-service-overview.md)