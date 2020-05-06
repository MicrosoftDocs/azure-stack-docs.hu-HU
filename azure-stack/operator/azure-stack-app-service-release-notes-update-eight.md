---
title: App Service Azure Stack hub Update 8 kibocsátási megjegyzései
description: Frissítse a Azure Stack hub App Service-es kiadási megjegyzéseit, beleértve az új funkciókat, javításokat és ismert problémákat.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 5bdf06b740d8a2c12f96494c52a683f50fe31340
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847809"
---
# <a name="app-service-on-azure-stack-hub-update-8-release-notes"></a>App Service Azure Stack hub Update 8 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések ismertetik az új funkciókat, javításokat és ismert problémákat a Azure App Service Azure Stack hub Update 8 rendszeren. Az ismert problémák két szakaszra vannak osztva: a frissítési folyamattal és a buildtel (a telepítés után) kapcsolatos problémákról.

> [!IMPORTANT]
> Alkalmazza a 1910-es frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack Development Kitt (ASDK) a Azure App Service 1,8 üzembe helyezése előtt.

## <a name="build-reference"></a>Build referenciája

Az Azure Stack hub Update 8 build számának App Service **86.0.2.13**.

## <a name="prerequisites"></a>Előfeltételek

Az üzembe helyezés megkezdése előtt tekintse [meg az előfeltételek a App Service Azure stack hub-ban történő telepítéséhez](azure-stack-app-service-before-you-get-started.md) című témakört.

Mielőtt megkezdené a Azure App Service frissítését Azure Stack hub-ról 1,8-re:

- Győződjön meg arról, hogy az összes szerepkör készen áll Azure App Service felügyeletre az Azure Stack hub felügyeleti portálján.

- App Service titkos kódok biztonsági mentése az Azure Stack hub felügyeleti portálján a App Service felügyelet használatával

- A App Service és a fő adatbázisok biztonsági mentése:
  - AppService_Hosting;
  - AppService_Metering;
  - master

- A bérlői alkalmazás tartalmának fájlmegosztás biztonsági mentése.

  > [!Important]
  > A Felhőbeli operátorok felelősek a fájlkiszolgáló és a SQL Server karbantartásához és üzemeltetéséhez.  Az erőforrás-szolgáltató nem kezeli ezeket az erőforrásokat.  A felhő operátor feladata a App Service adatbázisok és a bérlői tartalom fájlmegosztás biztonsági mentése.

- Az **Egyéni szkriptek bővítményének** **1.9.3** -verziójának benyújtása az Azure stack hub piactérről

## <a name="new-features-and-fixes"></a>Új funkciók és javítások

A Azure App Service on Azure Stack hub Update 8 a következő javításokat és javításokat tartalmazza:

- Frissítések **app Service bérlő, a rendszergazda, a functions portál és a kudu eszközökhöz**. Konzisztens a Azure Stack Portal SDK verziójával.

- Az **Azure functions Runtime** és a **v 1.0.12615**frissítése.

- Az alapszolgáltatás frissítése a megbízhatóság és a hibaüzenetek javítása érdekében, ami lehetővé teszi a gyakori problémák egyszerűbb diagnosztizálását.

- **A következő alkalmazás-keretrendszerek és eszközök frissítései**:

  - ASP.NET Core 3.1.0
  - ASP.NET Core 3.0.1
  - ASP.NET Core 2.2.8
  - ASP.NET Core v2 13.1.19331.0 modul
  - Azul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP-7.1.32
  - PHP-7.2.22
  - PHP-7.3.9
  - Frissített kudu – 85.11024.4154
  - MSDeploy 3.5.80916.15
  - NodeJS 10.16.3
  - NPM 6.9.0
  - Git a Windows 2.19.1.0

- Az **összes szerepkör mögöttes operációs rendszerének frissítései**:
  - [2019-12 összegző frissítés a Windows Server 2016 x64-alapú rendszerekhez (KB4530689)](https://support.microsoft.com/help/4530689)

- **Felügyelt lemezek támogatása új központi telepítések esetén**

Azure Stack hub Azure App Service összes új központi telepítése a felügyelt lemezeket fogja használni az összes virtuális gép és virtuálisgép-méretezési csoport számára. A meglévő telepítések továbbra is nem felügyelt lemezeket használnak.

- **A TLS 1,2 az előtér-terheléselosztó által kényszerített**

A **TLS 1,2** mostantól minden alkalmazásnál érvényben van.

## <a name="known-issues-upgrade"></a>Ismert problémák (frissítés)

- A frissítés sikertelen, ha a SQL Server always on fürtön feladatátvétel történt a másodlagos csomópontra.

A frissítés során a rendszer meghívja az adatbázis létezésének ellenőrzését a nem megfelelő főkapcsolati sztring használatával, mert a bejelentkezés az előző főcsomóponton volt.

Hajtsa végre az alábbi műveletek egyikét, és válassza az újra lehetőséget a telepítőn belül.

- Másolja a `appservice_hostingAdmin` bejelentkezést a most másodlagos SQL-csomópontból;

    **VAGY**

- Az SQL-fürt feladatátvétele az előző aktív csomópontra.

## <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

> [!IMPORTANT]
> Ha megadta a App Service erőforrás-szolgáltatót egy SQL always on példánnyal, [fel kell vennie a appservice_hosting és a appservice_metering adatbázist egy rendelkezésre állási csoportba](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) , és szinkronizálnia kell az adatbázisokat, hogy megakadályozza a szolgáltatás elvesztését egy adatbázis-feladatátvétel esetén.

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)

- A feldolgozók nem tudják elérni a fájlkiszolgálón, ha a App Service egy meglévő virtuális hálózatban van telepítve, és a fájlkiszolgáló csak a magánhálózaton érhető el, ahogy az a Azure App Service Azure Stack hub telepítési dokumentációjában szerepel.

  Ha úgy döntött, hogy egy meglévő virtuális hálózatra és egy belső IP-címet helyez üzembe a fájlkiszolgálón való kapcsolódáshoz, hozzá kell adnia egy kimenő biztonsági szabályt, amely engedélyezi az SMB-forgalmat a munkavégző alhálózat és a fájlkiszolgáló között. Nyissa meg a WorkersNsg a felügyeleti portálon, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:

  - Forrás: bármely
  - Forrásoldali porttartomány: *
  - Cél: IP-címek
  - Célként megadott IP-címtartomány: a fájlkiszolgáló IP-címeinek tartománya
  - Célport tartománya: 445
  - Protokoll: TCP
  - Művelet: Engedélyezés
  - Prioritás: 700
  - Név: Outbound_Allow_SMB445

- A Azure App Service Azure Stack hub 1,8-es új központi telepítése megköveteli, hogy az adatbázisok a bennük található adatbázisokra legyenek konvertálva.

    Ebben a kiadásban a regresszió miatt a App Service-adatbázisokat (appservice_hosting és appservice_metering) is át kell alakítani a foglalt adatbázisokra az **újonnan** telepítettek során.  Ez **nem** befolyásolja a **frissített** központi telepítéseket.

    > [!IMPORTANT]
    > Ez az eljárás körülbelül 5-10 percet vesz igénybe. Ez az eljárás a meglévő adatbázis-bejelentkezési munkamenetek leölését foglalja magában. Tervezze meg a Azure App Service áttelepítését és érvényességének ellenőrzését Azure Stack hub-on az áttelepítés után.

    1. [AppService-adatbázisok (appservice_hosting és appservice_metering) hozzáadása egy rendelkezésre állási csoporthoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

    1. A tárolt adatbázis engedélyezése.

        ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
        ```

    1. Az adatbázis konvertálása részben foglalt értékre. Ez a lépés leállást eredményez, mivel minden aktív munkamenetet le kell ölni.

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

- Nem lehet kibővíteni a feldolgozókat

  Az új feldolgozók nem tudják megszerezni a szükséges adatbázis-kapcsolódási karakterláncot.  Ha orvosolni szeretné ezt a helyzetet, kapcsolódjon az egyik vezérlő-példányhoz (például CN0-VM), és futtassa a következő PowerShell-parancsfájlt:

    ```powershell

    [System.Reflection.Assembly]::LoadWithPartialName("Microsoft.Web.Hosting")
    $siteManager = New-Object Microsoft.Web.Hosting.SiteManager

    $builder = New-Object System.Data.SqlClient.SqlConnectionStringBuilder -ArgumentList (Get-AppServiceConnectionString -Type Hosting)
    $conn = New-Object System.Data.SqlClient.SqlConnection -ArgumentList $builder.ToString()

    $siteManager.RoleServers | Where-Object {$_.IsWorker} | ForEach-Object {
        $worker = $_
        $dbUserName = "WebWorker_" + $worker.Name

        if (!$siteManager.ConnectionContexts[$dbUserName]) {
            $dbUserPassword = [Microsoft.Web.Hosting.Common.Security.PasswordHelper]::GenerateDatabasePassword()

            $conn.Open()
            $command = $conn.CreateCommand()
            $command.CommandText = "CREATE USER [$dbUserName] WITH PASSWORD = '$dbUserPassword'"
            $command.ExecuteNonQuery()
            $conn.Close()

            $conn.Open()
            $command = $conn.CreateCommand()
            $command.CommandText = "ALTER ROLE [WebWorkerRole] ADD MEMBER [$dbUserName]"
            $command.ExecuteNonQuery()
            $conn.Close()

            $builder.Password = $dbUserPassword
            $builder["User ID"] = $dbUserName

            $siteManager.ConnectionContexts.Add($dbUserName, $builder.ToString())
        }
    }

    $siteManager.CommitChanges()

    ```

## <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Ismert problémák a Cloud adminok operációs Azure App Service Azure Stack központban

Tekintse meg az [Azure stack Hub 1907 kibocsátási megjegyzései](azure-stack-release-notes-1907.md)dokumentációját.

## <a name="next-steps"></a>További lépések

- A Azure App Service áttekintését lásd: [Azure app Service és Azure Functions Azure stack hub – áttekintés](azure-stack-app-service-overview.md).
- További információ a App Service telepítésének előkészítéséről Azure Stack hub-on: [app Service üzembe helyezésének előfeltételei az Azure stack hub-on](azure-stack-app-service-before-you-get-started.md).
