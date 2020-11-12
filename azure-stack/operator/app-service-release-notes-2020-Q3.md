---
title: App Service Azure Stack hub 2002 Q3 kibocsátási megjegyzései
description: Ismerje meg, hogy mi a 2002 Q3-kiadás a Azure Stack hub App Service, az ismert problémák és a frissítés letöltésének helyétől.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 10/28/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 10/23/2020
ms.openlocfilehash: 55c8e18f3bbd36a0fef4cfb745ab9d40651e7533
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548659"
---
# <a name="app-service-on-azure-stack-hub-2020-q3-release-notes"></a>App Service Azure Stack hub 2020 Q3 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések ismertetik a Azure App Service Azure Stack hub 2020 Q3 és az ismert problémákkal kapcsolatos javításait és javításait. Az ismert problémák az üzembe helyezéssel, a frissítési folyamattal és a buildtel (a telepítés után) kapcsolatos problémákkal közvetlenül kapcsolatos problémákra vannak osztva.

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

## <a name="build-reference"></a>Build referenciája

A App Service Azure Stack hub 2020 Q3 Build száma **89.0.2.15**

## <a name="prerequisites"></a>Előfeltételek

Az üzembe helyezés megkezdése előtt tekintse át az [első lépések dokumentációját](azure-stack-app-service-before-you-get-started.md) .

Mielőtt megkezdené a Azure App Service frissítését Azure Stackról 2020 Q3-ra:

- Győződjön meg arról, hogy az összes szerepkör készen áll a Azure App Service adminisztráció Azure Stack hub felügyeleti portálon

- App Service titkos kódok biztonsági mentése az Azure Stack hub felügyeleti portálján a App Service felügyelet használatával

- A App Service és a fő adatbázisok biztonsági mentése:
  - AppService_Hosting;
  - AppService_Metering;
  - Mester

- A bérlői alkalmazás tartalmának fájlmegosztás biztonsági mentése

  > [!Important]
  > A Felhőbeli operátorok felelősek a fájlkiszolgáló és a SQL Server karbantartásához és üzemeltetéséhez.  Az erőforrás-szolgáltató nem kezeli ezeket az erőforrásokat.  A felhő operátor feladata a App Service adatbázisok és a bérlői tartalom fájlmegosztás biztonsági mentése.

- Az **Egyéni szkriptek bővítményének** **1.9.3** -verziójának szindikátusa a piactéren

## <a name="updates"></a>Frissítések

A Azure App Service on Azure Stack Update Q3 a következő javításokat és javításokat tartalmazza:

- A **app Service bérlő, a rendszergazda, a functions portál és a kudu eszközök** frissítései. Konzisztens a Azure Stack Portal SDK verziójával.

- Teljes képernyős létrehozási élmény a web-és Function-alkalmazásokhoz

- Az új Azure Functions portál felületének konzisztensnek kell lennie Web Apps

- A **Azure functions Runtime** és a **v 1.0.13154** frissítése.

- Az alapszolgáltatások frissítései a megbízhatóság és a hibák javításához, ami lehetővé teszi a gyakori problémák egyszerűbb diagnosztizálását.

- **A következő alkalmazás-keretrendszerek és eszközök frissítései** :
  - ASP.NET Core 2.1.22
  - ASP.NET Core 2.2.14
  - ASP.NET Core 3.1.8
  - ASP.NET Core v2 13.1.19331.0 modul
  - Azul OpenJDK
    - 8.42.0.23
    - 8.44.0.11
    - 11.35.15
    - 11.37.17
  - Curl 7.55.1
  - Git a Windows 2.28.0.1
  - MSDeploy 3.5.90702.36
  - NodeJS
    - 14.10.1
  - NPM
    - 6.14.8
  - PHP-7.4.5
  - Tomcat
    - 8.5.47
    - 8.5.51
    - 9.0.273
    - 9.0.31
  - Frissített kudu – 90.21005.4823

- Az **összes szerepkör mögöttes operációs rendszerének frissítései** :
  - [2020-10 összegző frissítés a Windows Server 2016 x64-alapú rendszerekhez (KB4580346)](https://support.microsoft.com/help/4580346)
  - [2020-09 karbantartási verem frissítése a Windows Server 2016 x64-alapú rendszerekhez (KB4576750)](https://support.microsoft.com/help/4576750)
  - Defender-definíció 1.325.755.0

- **A Windows Server összegző frissítései mostantól a központi telepítés és a frissítés részeként lesznek alkalmazva a vezérlő szerepköreire**

## <a name="issues-fixed-in-this-release"></a>Ebben a kiadásban rögzített problémák

- A bérlők mostantól új App Service tervet hozhatnak létre a bérlői portálon App Service terv nézetben

- A bérlők a bérlői portálon kezelhetik alkalmazásaikban lévő tanúsítványokat

- A függvények figyelése mostantól lekérheti az adatok lekérését a TLS 1,2-t végrehajtó tárolási végpontokról

- Várakozás a felügyeleti kiszolgálók felé a felhő üzembe helyezése lépésen kívüli lépésre a telepítés során az üzembe helyezés és a frissítés megbízhatóságának növelése érdekében

## <a name="pre-update-steps"></a>Frissítés előtti lépések

Tekintse át a [frissítés ismert problémáit](#known-issues-update) , és végezze el a szükséges műveleteket.

## <a name="post-deployment-steps"></a>Üzembe helyezés után lépések

> [!IMPORTANT]
> Ha megadta a App Service erőforrás-szolgáltatót egy SQL always on példánnyal, [fel kell vennie a appservice_hosting és a appservice_metering adatbázist egy rendelkezésre állási csoportba](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) , és szinkronizálnia kell az adatbázisokat, hogy megakadályozza a szolgáltatás elvesztését egy adatbázis-feladatátvétel esetén.

## <a name="known-issues-update"></a>Ismert problémák (frissítés)

- Olyan helyzetekben, amikor az ügyfél átalakította a appservice_hosting és appservice_metering adatbázisokat a tárolt adatbázisba, a frissítés sikertelen lehet, ha a bejelentkezések nem lettek áttelepítve a tárolt felhasználók számára.

Azok az ügyfelek, akik átalakították a appservice_hosting és appservice_metering adatbázisokat a tárolt adatbázisba, és nem sikerült migrálni az adatbázis-bejelentkezéseket a tárolt felhasználók számára, frissítési hibák léphetnek fel.  

Az ügyfeleknek a következő szkriptet kell végrehajtaniuk a SQL Server üzemeltetési appservice_hosting és a appservice_metering előtt, mielőtt a Azure App Service Azure Stack központi telepítésről 2020 Q3-ra frissítené.  **Ez a szkript nem romboló jellegű, és nem okoz állásidőt**.

A szkriptnek a következő feltételekkel kell futnia

- Rendszergazda jogosultsággal rendelkező felhasználó, például az SQL SA-fiók;
- Ha az SQL always on szolgáltatást használja, győződjön meg arról, hogy az SQL-példányon fut a parancsfájl, amely tartalmazza az összes App Service bejelentkezést az űrlapon:

    - appservice_hosting_FileServer
    - appservice_hosting_HostingAdmin
    - appservice_hosting_LoadBalancer
    - appservice_hosting_Operations
    - appservice_hosting_Publisher
    - appservice_hosting_SecurePublisher
    - appservice_hosting_WebWorkerManager
    - appservice_metering_Common
    - appservice_metering_Operations
    - Minden olyan webworker-bejelentkezés, amely az űrlapon van WebWorker_<instance ip address>

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

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)

- A feldolgozók nem tudják elérni a fájlkiszolgálón, ha a App Service egy meglévő virtuális hálózatban van telepítve, és a fájlkiszolgáló csak a magánhálózaton érhető el, ahogy az a Azure App Service Azure Stack üzembe helyezési dokumentációban is szerepel.

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Ismert problémák a Cloud adminok operációs Azure App Service Azure Stack

- A leválasztott környezetek nem támogatják az egyéni tartományokat

App Service végrehajtja a tartomány tulajdonjogának ellenőrzését a nyilvános DNS-végpontokon, mivel az egyéni tartományok nem támogatottak a leválasztott forgatókönyvekben.

## <a name="next-steps"></a>Következő lépések

- A Azure App Service áttekintését lásd: [Azure App Service Azure stack áttekintése](azure-stack-app-service-overview.md).
- További információ a Azure Stack App Service telepítésének előkészítéséről: [mielőtt megkezdi a Azure Stack app Serviceének](azure-stack-app-service-before-you-get-started.md)megkezdését.
