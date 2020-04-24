---
title: App Service Azure Stack hub Update 3 kibocsátási megjegyzései
description: Ismerkedjen meg a Azure Stack hub App Service frissítésének javításait, javításait és ismert problémáit ismertető cikkből.
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 0b277f33472c25d0dc8e6b813685029aea81a303
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77701394"
---
# <a name="app-service-on-azure-stack-hub-update-3-release-notes"></a>App Service Azure Stack hub Update 3 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések ismertetik a Azure Stack hub 3. frissítésének Azure App Service javításait, javításait és ismert problémáit. Az ismert problémák három szakaszra oszlanak: az üzembe helyezéshez közvetlenül kapcsolódó problémák, a frissítési folyamattal kapcsolatos problémák és a build (telepítés utáni) problémák.

> [!IMPORTANT]
> Alkalmazza az 1807-es frissítést az Azure Stack hub integrált rendszerére, vagy telepítse a legújabb Azure Stack Development Kit (ASDK) a Azure App Service 1,3 telepítése előtt.

## <a name="build-reference"></a>Build referenciája

Az Azure Stack hub Update 3 Build számának App Service **74.0.13698.31**.

### <a name="prerequisites"></a>Előfeltételek

Az üzembe helyezés megkezdése előtt tekintse át a [app Service telepítésének Előfeltételeit Azure stack hub-on](azure-stack-app-service-before-you-get-started.md) .

Mielőtt megkezdené a Azure App Service frissítését Azure Stack hub-ról 1,3-re, győződjön meg arról, hogy az összes szerepkör készen áll az Azure App Service adminisztrációban az Azure Stack hub felügyeleti portálján.

![App Service szerepkör állapota](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>Új funkciók és javítások

A Azure App Service on Azure Stack hub Update 3 a következő javításokat és javításokat tartalmazza:

- SQL Server always on Azure App Service erőforrás-szolgáltatói adatbázisokhoz való használatának támogatása.

- Új környezeti paraméter hozzáadva a Create-AADIdentityApp Helper parancsfájlhoz a különböző Azure AD-régiók célzásának elősegítése érdekében.

- A **app Service bérlő, a rendszergazda, a functions portál és a kudu eszközök**frissítései. Konzisztens Azure Stack hub Portal SDK verziójával.

- A **Azure functions Runtime** és a **v 1.0.11820**frissítése.

- Az alapszolgáltatások frissítései a megbízhatóság és a hibák javításához, ami lehetővé teszi a gyakori problémák egyszerűbb diagnosztizálását.

- **A következő alkalmazás-keretrendszerek és eszközök frissítései**:
  - Hozzáadott ASP.NET Core 2.1.2
  - NodeJS 10.0.0 hozzáadva
  - Zulu OpenJDK 8.30.0.1 hozzáadva
  - Tomcat 8.5.31 és 9.0.8 hozzáadva
  - PHP-verziók hozzáadva:
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - Wincache 2.0.0.8 hozzáadva
  - Frissített git a Windows to v 2.17.1.2
  - Frissített kudu – 74.10611.3437
  
- Az **összes szerepkör mögöttes operációs rendszerének frissítései**:
  - [Karbantartási verem frissítése a Windows Server 2016 x64-alapú rendszerekhez (KB4132216)](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [2018-07 összegző frissítés a Windows Server 2016 x64-alapú rendszerekhez (KB4338822)](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

### <a name="post-update-steps-optional"></a>Frissítés utáni lépések (nem kötelező)

Azon ügyfelek számára, akik egy tárolt adatbázisba kívánnak migrálni a meglévő Azure App Service Azure Stack hub-alapú telepítések esetében, hajtsa végre ezeket Azure App Service a lépéseket az Azure Stack hub 1,3 frissítésének befejezése után:

> [!IMPORTANT]
> Ez az eljárás körülbelül 5-10 percet vesz igénybe. Ez az eljárás a meglévő adatbázis-bejelentkezési munkamenetek leölését foglalja magában. A Azure App Service áttelepítésének és ellenőrzésének megtervezése Azure Stack hub-beli áttelepítés után
>
>

1. [AppService-adatbázisok (appservice_hosting és appservice_metering) hozzáadása egy rendelkezésre állási csoporthoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

1. A tárolt adatbázis engedélyezése.
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Adatbázis konvertálása részben foglalt értékre. Ez a lépés állásidőt von maga után, mivel minden aktív munkamenetet le kell ölni.

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

1. Migrate logins to contained database users.

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

**Érvényesítés**

1. Ellenőrizze, hogy a SQL Server engedélyezve van-e.

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. A meglévő foglalt viselkedés megtekintése.
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Ismert problémák a Cloud adminok operációs Azure App Service Azure Stack központban

Tekintse meg az Azure Stack hub 1807 kibocsátási megjegyzései dokumentációját.

## <a name="next-steps"></a>További lépések

- A Azure App Service áttekintését lásd: [Azure App Service Azure stack hub – áttekintés](azure-stack-app-service-overview.md).
- További információ a App Service telepítésének előkészítéséről Azure Stack hub-on: [app Service üzembe helyezésének előfeltételei az Azure stack hub-on](azure-stack-app-service-before-you-get-started.md).
