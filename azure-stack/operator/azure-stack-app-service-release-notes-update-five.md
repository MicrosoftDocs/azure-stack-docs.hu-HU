---
title: Az Azure Stack App Service update 5 kibocsátási megjegyzései |} A Microsoft Docs
description: Ismerje meg a frissítés öt az App Service az Azure Stacken, az ismert hibákat, és hol töltse le a frissítést.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: e0801ecdce5ddeffd3bcae43d999121c62d3e052
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269167"
---
# <a name="app-service-on-azure-stack-update-5-release-notes"></a>App Service-ben az Azure Stack update 5 kibocsátási megjegyzései

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A kibocsátási megjegyzések láthatók a fejlesztései, valamint a javításokat az Azure App Service az Azure Stack Update 5 és olyan ismert problémákat. Ismert problémák az üzembe helyezési, frissítési folyamat és a build (telepítés utáni) problémái közvetlenül kapcsolódó problémák vannak felosztva.

> [!IMPORTANT]
> Az Azure Stackkel integrált rendszereknél 1901 frissítés alkalmazása, vagy a legújabb Azure Stack fejlesztői készletének telepítése az Azure App Service 1.5 telepítése előtt.


## <a name="build-reference"></a>Hivatkozás létrehozása

Az App Service az Azure Stack Update 5 buildszám **80.0.2.15**

### <a name="prerequisites"></a>Előfeltételek

Tekintse meg a [mielőtt elkezdené a dokumentáció](azure-stack-app-service-before-you-get-started.md) központi telepítésének megkezdése előtt.

Mielőtt elkezdené a frissítés az Azure App Service az Azure Stacken 1.5:

- Győződjön meg, hogy minden szerepkör kész a az Azure App Service felügyeleti az Azure Stack felügyeleti portálon

- Az App Service-ben és a Master adatbázis biztonsági mentésére:
  - AppService_Hosting;
  - AppService_Metering;
  - Fő

- A bérlő alkalmazás tartalom fájlmegosztás biztonsági mentése

- Konzorcium a **egyéni szkriptek futtatására szolgáló bővítmény** verzió **1.9.1** a Marketplace-ről

### <a name="new-features-and-fixes"></a>Új funkciókkal és javításokkal

Az Azure App Service-ben az Azure Stack Update 5 tartalmazza a következő fejlesztések és javítások:

- Frissítések **App Services-bérlő, a rendszergazda, a Functions-portálok és eszközök a Kudu**. Az Azure Stack Portal SDK-verzió összhangban.

- Frissítések **Azure Functions runtime** való **v1.0.12205**.

- Frissítések **Kudu eszközök** stílusának és funkciókkal kapcsolatos problémák megoldása működő ügyfelek **leválasztott** Azure Stack. 

- Fokozható a megbízhatóság és a hibaüzenetek gyakori problémák egyszerűbb diagnosztika engedélyezése a core-szolgáltatás frissítése.

- **A következő alkalmazás-keretrendszerek és eszközök frissítések**:
  - Új ASP.NET Core 2.1.6 és 2.2.0
  - A hozzáadott NodeJS 10.14.1
  - A hozzáadott NPM 6.4.1
  - Frissített Kudu 79.20129.3767
  
- **Frissítések az alapul szolgáló operációs rendszer összes szerepkör**:
  - [A 2019-02 összegző frissítés-a Windows Server 2016 x64 alapú rendszerekhez (KB4487006)](https://support.microsoft.com/help/4487006/windows-10-update-kb4487006)

### <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

> [!IMPORTANT]  
> Ha megadta az App Service erőforrás-szolgáltató-példánnyal SQL mindig meg kell [adja hozzá a appservice_hosting és appservice_metering adatbázisokat egy rendelkezésre állási csoport](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) és így elkerülhető, hogy az adatbázisok szinkronizálása a szolgáltatás adatbázis-feladatátvétel esetén.

### <a name="post-update-steps"></a>Frissítés utáni lépések

A tartalmazott adatbázis áttelepítése az Azure Stack üzemelő példányok a meglévő Azure App Service kívánó ügyfelek hajtsa végre ezeket a lépéseket az Azure App Service az Azure Stack 1.5-ös frissítés befejezése után:

> [!IMPORTANT]
> Az áttelepítési eljárás körülbelül 5 – 10 percet vesz igénybe.  Az eljárás magában foglalja a meglévő adatbázis-bejelentkezési munkamenetek leállítása.  Tervezze meg a állásidő telepíthetőek át és ellenőrizze az Azure App Service-ben az Azure Stack a migrálás után.  Ha végrehajtotta ezeket a lépéseket, majd ezeket a lépéseket nem szükségesek az Azure App Service az Azure Stack 1.3 frissítése után.
>
>

1. Adjon hozzá [az App Service-adatbázisok (appservice_hosting és appservice_metering) egy rendelkezésre állási csoporthoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. Enable foglalt adatbázis
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. A részlegesen tartalmazott adatbázis konvertálása, az átalakítás számítunk fel állásidő, az összes aktív munkamenetet kell le fognak állni

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
    '''

1. Migrate Logins to Contained Database Users

    ```sql
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

Érvényesítés

1. Ellenőrzi, hogy az SQL Server tartalmazza-e a tartalmazási engedélyezve

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Ellenőrizze a létező tartalmazott esetén
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

- Feldolgozók nem érhető el a fájlkiszolgálót, ha App Service-ben üzemel egy meglévő virtuális hálózatot és a fájlkiszolgáló csak érhető el a magánhálózaton, feltüntettük az Azure App Service az Azure Stack központi telepítési dokumentációjában az.

Ha úgy döntött, hogy egy meglévő virtuális hálózattal és belső IP-cím szeretne csatlakozni a fájlkiszolgáló üzembe helyezése, hozzá kell adnia egy kimenő biztonsági szabályt a feldolgozó és a fájlkiszolgáló között SMB-forgalom engedélyezése. Nyissa meg a WorkersNsg a felügyeleti portálon, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:
 * Adatforrás: Bármely
 * Forrás porttartomány: *
 * Cél: IP-címek
 * Cél IP-címtartomány: IP-címtartományt a fájlkiszolgálóhoz
 * Cél porttartomány: 445
 * Protokoll: TCP
 * Művelet: Engedélyezés
 * Prioritás: 700
 * Név: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Ismert problémák működtetése az Azure App Service az Azure Stack felhő-rendszergazdák számára

A dokumentáció a [Azure Stack 1809 kibocsátási megjegyzései](azure-stack-update-1903.md)

## <a name="next-steps"></a>További lépések

- Az Azure App Service áttekintését lásd: [Azure App Service az Azure Stack áttekintése](azure-stack-app-service-overview.md).
- Hogyan készíti elő az Azure Stack App Service üzembe helyezése kapcsolatos további információkért lásd: [az App Service-ben az Azure Stack használatának megkezdése előtt](azure-stack-app-service-before-you-get-started.md).
