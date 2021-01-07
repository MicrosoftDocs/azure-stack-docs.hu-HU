---
title: App Service Azure Stack hub Update 5 kibocsátási megjegyzései
description: Ismerkedjen meg a Azure Stack hub App Servicejának 5. frissítésében szereplő javításokkal, javításokkal és ismert problémákkal.
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 3a4b2ae49e052731e372f4bfdaf8ff30c9b5490e
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974404"
---
# <a name="app-service-on-azure-stack-hub-update-5-release-notes"></a>App Service Azure Stack hub Update 5 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések ismertetik a Azure App Service Azure Stack hub Update 5-ös verziójának javításait, javításait és ismert problémáit. Az ismert problémák három szakaszra oszlanak: az üzembe helyezéshez közvetlenül kapcsolódó problémák, a frissítési folyamattal kapcsolatos problémák és a build (telepítés utáni) problémák.

> [!IMPORTANT]
> Alkalmazza az 1901-es frissítést az Azure Stack hub integrált rendszerére, vagy telepítse a legújabb Azure Stack Development Kit (ASDK) a Azure App Service 1,5 telepítése előtt.

## <a name="build-reference"></a>Build referenciája

A App Service Azure Stack hub Update 5 Build száma **80.0.2.15**.

## <a name="prerequisites"></a>Előfeltételek

Az üzembe helyezés megkezdése előtt tekintse át a [app Service telepítésének Előfeltételeit Azure stack hub-on](azure-stack-app-service-before-you-get-started.md) .

Mielőtt megkezdené a Azure App Service frissítését Azure Stack hub-ról 1,5-re:

- Győződjön meg arról, hogy az összes szerepkör készen áll a Azure App Service adminisztrációban az Azure Stack hub felügyeleti portálján.

- App Service titkos kódok biztonsági mentése az Azure Stack hub felügyeleti portálján a App Service felügyelet használatával

- A App Service és a fő adatbázisok biztonsági mentése:
  - AppService_Hosting;
  - AppService_Metering;
  - Mester

- A bérlői alkalmazás tartalmának fájlmegosztás biztonsági mentése

  > [!Important]
  > A Felhőbeli operátorok felelősek a fájlkiszolgáló és a SQL Server karbantartásához és üzemeltetéséhez.  Az erőforrás-szolgáltató nem kezeli ezeket az erőforrásokat.  A felhő operátor feladata a App Service adatbázisok és a bérlői tartalom fájlmegosztás biztonsági mentése.

- Az **Egyéni szkriptek bővítményének** **1.9.1** az Azure Marketplace-ről.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások

Az Azure Stack hub Update 5 Azure App Service a következő javításokat és javításokat tartalmazza:

- A **app Service bérlő, a rendszergazda, a functions portál és a kudu eszközök** frissítései. Konzisztens Azure Stack hub Portal SDK verziójával.

- A **Azure functions Runtime** és a **v 1.0.12205** frissítése.

- A **kudu-eszközök** frissítései a **megszakadt** Azure stack hub-t használó ügyfelek stílusával és funkcióival kapcsolatos problémák megoldásához. 

- Az alapszolgáltatások frissítései a megbízhatóság és a hibák javításához, ami lehetővé teszi a gyakori problémák egyszerűbb diagnosztizálását.

- **A következő alkalmazás-keretrendszerek és eszközök frissítései**:
  - Hozzáadott ASP.NET Core 2.1.6 és 2.2.0
  - NodeJS 10.14.1 hozzáadva
  - NPM 6.4.1 hozzáadva
  - Frissített kudu – 79.20129.3767
  
- Az **összes szerepkör mögöttes operációs rendszerének frissítései**:
  - [2019-02 összegző frissítés a Windows Server 2016 x64-alapú rendszerekhez (KB4487006)](https://support.microsoft.com/help/4487006/windows-10-update-kb4487006)

## <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

> [!IMPORTANT]  
> Ha megadta a App Service erőforrás-szolgáltatót egy SQL always on példánnyal, fel *kell* [vennie a appservice_hosting és a appservice_metering adatbázist egy rendelkezésre állási csoportba](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) , és szinkronizálnia kell az adatbázisokat, hogy megakadályozza a szolgáltatás elvesztését egy adatbázis-feladatátvétel esetén.

## <a name="post-update-steps"></a>Frissítés utáni lépések

Azon ügyfelek számára, akik egy tárolt adatbázisba kívánnak migrálni a meglévő Azure App Service Azure Stack hub-alapú telepítések esetében, hajtsa végre ezeket Azure App Service a lépéseket az Azure Stack hub 1,5 frissítésének befejezése után:

> [!IMPORTANT]
> Az áttelepítési eljárás körülbelül 5-10 percet vesz igénybe. Az eljárás magában foglalja a meglévő adatbázis-bejelentkezési munkamenetek leölését. Tervezze meg a Azure App Service áttelepítését és érvényességének ellenőrzését Azure Stack hub-on az áttelepítés után. Ha a Azure Stack hub 1,3-es Azure App Service frissítését követően végrehajtotta ezeket a lépéseket, akkor ezek a lépések nem szükségesek.

1. [AppService-adatbázisok (appservice_hosting és appservice_metering) hozzáadása egy rendelkezésre állási csoporthoz](/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

1. A tárolt adatbázis engedélyezése.
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Az adatbázisok részlegesen való konvertálásakor az átalakítás állásidőt von maga után, mivel minden aktív munkamenetet le kell ölni.

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    ```

1. Bejelentkezések migrálása a tárolt adatbázis-felhasználók számára.

    ```sql
        USE appservice_hosting
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO

        USE appservice_metering
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

**Érvényesítés**

1. Ellenőrizze, hogy a SQL Server engedélyezve van-e.

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. A meglévő foglalt viselkedés megtekintése.
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)

- A feldolgozók nem tudják elérni a fájlkiszolgálón, ha a App Service egy meglévő virtuális hálózaton van telepítve, és a fájlkiszolgáló csak a magánhálózaton érhető el. Ezt a problémát a Azure Stack hub üzembe helyezési dokumentációjának Azure App Servicejában nevezzük.

Ha úgy döntött, hogy egy meglévő virtuális hálózatra és egy belső IP-címet helyez üzembe a fájlkiszolgálón való kapcsolódáshoz, hozzá kell adnia egy kimenő biztonsági szabályt, amely engedélyezi az SMB-forgalmat a munkavégző alhálózat és a fájlkiszolgáló között. Nyissa meg a WorkersNsg a felügyeleti portálon, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:

 * Forrás: bármely
 * Forrásoldali porttartomány: *
 * Cél: IP-címek
 * Célként megadott IP-címtartomány: a fájlkiszolgáló IP-címeinek tartománya
 * Célport tartománya: 445
 * Protokoll: TCP
 * Művelet: Engedélyezés
 * Prioritás: 700
 * Név: Outbound_Allow_SMB445

## <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Ismert problémák a Cloud adminok operációs Azure App Service Azure Stack központban

Tekintse meg az [Azure stack Hub 1809 kibocsátási megjegyzései](./release-notes.md)dokumentációját.

## <a name="next-steps"></a>Következő lépések

- A Azure App Service áttekintését lásd: [Azure App Service Azure stack hub – áttekintés](azure-stack-app-service-overview.md).
- A App Service Azure Stack hub-on való telepítésének előkészítésével kapcsolatos további információkért lásd: [app Service üzembe helyezésének előfeltételei az Azure stack hub-on](azure-stack-app-service-before-you-get-started.md).
