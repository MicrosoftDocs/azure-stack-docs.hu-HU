---
title: Helyreállítás az Azure Stack App Service szolgáltatásában |} A Microsoft Docs
description: Részletes útmutatás a vész-helyreállítási Azure Stack App Service-ben
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: mabrigg
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 7932530f88365597de24ed49e93820150bc88c3c
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618313"
---
# <a name="recovery-of-app-service-on-azure-stack"></a>Helyreállítás az App Service az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*  

Ez a dokumentum érvénybe az App Service-ben vész-helyreállítási műveleteivel kapcsolatos útmutatást nyújt.

Az alábbi műveleteket az Azure Stack App Service-ben helyreállítása biztonsági másolatból kell venni:
1.  Az App Service-adatbázisok visszaállítása
2.  A fájl megosztási tartalom visszaállítása
3.  Az App Service-szerepkörök és szolgáltatások visszaállítása

Ha az Azure Stack tárolási Függvényalkalmazások tároláshoz használt, majd is szükségesek lépést Függvényalkalmazások visszaállításához.

## <a name="restore-the-app-service-databases"></a>Az App Service-adatbázisok visszaállítása
Éles kész SQL Server-példányt az App Service az SQL Server-adatbázisokat kell visszaállítani. 

Miután [előkészítése az SQL Server-példány](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance) az App Service-adatbázisok üzemeltetéséhez, használja ezeket a lépéseket adatbázisok biztonsági másolatból történő visszaállítását:

1. Jelentkezzen be rendszergazdai engedélyekkel a helyreállított App Service-ben adatbázisokat üzemeltető SQL Server.
2. A következő parancsokat használja egy parancssort rendszergazdai jogosultságokkal fut az App Service-adatbázisok visszaállítása:
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. Győződjön meg arról, hogy mindkét App Service-adatbázisokat sikeresen vissza lett állítva, és lépjen ki az SQL Server Management Studióval.

> [!NOTE]
> A feladatátvevő fürt példány hiba, helyreállítás [ezek az utasítások](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017). 

## <a name="restore-the-app-service-file-share-content"></a>Az App Service-ben fájltartalom megosztás visszaállítása
Miután [előkészítése a fájlkiszolgáló](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server) üzemeltetni az App Service-fájlmegosztás, a bérlő fájltartalom fájlmegosztás visszaállítása biztonsági másolatból kell. Tetszőleges módszerrel is használhat, másolja a fájlt az újonnan létrehozott App Service-ben fájlmegosztási helyet elérhető. Ebben a példában a fájl kiszolgálón futó használandó PowerShell és a robocopy csatlakozik egy távoli megosztásra mutat, és másolja a fájlokat a megosztásba:

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

Másolja a fájlmegosztás tartalmát, mellett is vissza kell állítania magát a fájlmegosztás engedélyei. Ehhez nyisson meg egy rendszergazdai parancssort azon a fájlkiszolgálón, és futtassa a **ReACL.cmd** fájlt. A **ReACL.cmd** fájl található, az App Service-ben telepítőfájlok a **BCDR** könyvtár.

## <a name="restore-app-service-roles-and-services"></a>Az App Service-szerepkörök és szolgáltatások visszaállítása
Miután az App Service-adatbázisokat és a megosztás tartalma vissza lett állítva, ezután az App Service-szerepkörök és szolgáltatások visszaállítása a PowerShell használatával kell. Ezeket a lépéseket az App Service titkos kódok és a szolgáltatáskonfiguráció állítja vissza.  

1. Jelentkezzen be az App Service-vezérlő **CN0 virtuális** -alapú virtuális gép **roleadmin** App Service-ben a telepítés során megadott jelszó segítségével. 
    > [!TIP]
    > Az RDP-kapcsolatok engedélyezése a virtuális gép hálózati biztonsági csoportot módosítani kell. 
2. Másolás a **SystemSecrets.JSON** fájlt helyileg a virtuális gép vezérlő. Meg kell adnia, a fájl elérési útját a `$pathToExportedSecretFile` paraméter a következő lépésben. 
3. Egy rendszergazda jogú PowerShell-konzolablakot a következő parancsok segítségével visszaállíthatja az App Service-szerepkörök és szolgáltatások:

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
> Erősen ajánlott gombra kattintva zárja be a parancs befejeződésekor a PowerShell-munkamenetben.

## <a name="restore-function-apps"></a>A Függvényalkalmazások visszaállítása 
Az Azure Stack App Service nem támogatja a bérlő felhasználói alkalmazások vagy a megosztás tartalma adatok helyreállítása. Ezért ezek kell biztonsági mentése és helyreállítása App Service-en kívül biztonsági mentési és visszaállítási műveletek. Ha az Azure Stack tárolási Függvényalkalmazások tároláshoz használt, elveszett adatok helyreállítására a következő lépéseket kell tenni:

1. A Függvényalkalmazás által használt új tárfiók létrehozása. Ez a tároló lehet az Azure Stack storage, az Azure storage vagy kompatibilis tárolási.
2. A tárolási kapcsolati karakterlánc lekéréséhez.
3. Nyissa meg a függvény portált, tallózással keresse meg a függvényalkalmazást.
4. Keresse meg a **platformfunkciók** fülre, és **Alkalmazásbeállítások**.
5. Változás **AzureWebJobsDashboard** és **AzureWebJobsStorage** az új kapcsolati karakterlánc, és kattintson a **mentése**.
6. Váltson **áttekintése**.
7. Indítsa újra az alkalmazást. Törölje az összes hiba több próbálkozás is eltarthat.

## <a name="next-steps"></a>További lépések
[Az Azure Stack App Service áttekintése](azure-stack-app-service-overview.md)