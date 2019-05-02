---
title: Az Azure Stack App Service frissítése 3 kibocsátási megjegyzések |} A Microsoft Docs
description: Ismerje meg a frissítés három az App Service az Azure Stacken, az ismert hibákat, és hol töltse le a frissítést.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/25/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: sethm
ms.lastreviewed: 08/20/2018
ms.openlocfilehash: 5ea711d3d4ffff72279e745290c1c8d9d854298e
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64291066"
---
# <a name="app-service-on-azure-stack-update-3-release-notes"></a>App Service-ben az Azure Stack 3-as frissítés – kibocsátási megjegyzések

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A kibocsátási megjegyzések láthatók a fejlesztései, valamint a javításokat az Azure App Service az Azure Stack Update 3 és olyan ismert problémákat. Ismert problémák az üzembe helyezési, frissítési folyamat és a build (telepítés utáni) problémái közvetlenül kapcsolódó problémák vannak felosztva.

> [!IMPORTANT]
> Az Azure Stackkel integrált rendszereknél 1807 frissítés alkalmazása, vagy a legújabb Azure Stack fejlesztői készletének telepítése Azure App Service 1.3 üzembe helyezése előtt.
>
>

## <a name="build-reference"></a>Hivatkozás létrehozása

Az App Service az Azure Stack Update 3 buildszám **74.0.13698.31**

### <a name="prerequisites"></a>Előfeltételek

Tekintse meg a [mielőtt elkezdené a dokumentáció](azure-stack-app-service-before-you-get-started.md) központi telepítésének megkezdése előtt.

Mielőtt elkezdené a frissítés az Azure App Service az Azure Stacken, 1.3, győződjön meg arról, minden szerepkör kész a az Azure App Service felügyeleti az Azure Stack felügyeleti portálon

![Az App Service-szerepkör állapota](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>Új funkciókkal és javításokkal

Az Azure App Service-ben az Azure Stack Update 3 tartalmazza a következő fejlesztések és javítások:

- Használja az SQL Server Always On Azure App Service erőforrás-szolgáltató adatbázisok támogatása.

- Új környezet paraméter hozzáadva a létrehozás-AADIdentityApp segítő parancsfájlt, amelyek segítik a célcsoport-kezelési régiókon aad-ben.

- Frissítések **App Services-bérlő, a rendszergazda, a Functions-portálok és eszközök a Kudu**. Az Azure Stack Portal SDK-verzió összhangban.

- Frissítések **Azure Functions runtime** való **v1.0.11820**.

- Fokozható a megbízhatóság és a hibaüzenetek gyakori problémák egyszerűbb diagnosztika engedélyezése a core-szolgáltatás frissítése.

- **A következő alkalmazás-keretrendszerek és eszközök frissítések**:
  - Added ASP.NET Core 2.1.2
  - Added NodeJS 10.0.0
  - Zulu openjdk csomagját 8.30.0.1 hozzáadva
  - A hozzáadott Tomcat 8.5.31 és 9.0.8
  - A hozzáadott PHP-verziók:
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - A hozzáadott Wincache 2.0.0.8
  - V 2.17.1.2 Windows a frissített Git esetében
  - Frissített Kudu 74.10611.3437
  
- **Frissítések az alapul szolgáló operációs rendszer összes szerepkör**:
  - [Karbantartási frissítését Windows Server 2016 x64 alapú rendszerekhez (KB4132216)](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [2018-07 összegző frissítés a Windows Server 2016 x64 alapú rendszerekhez (KB4338822)](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

### <a name="post-update-steps-optional"></a>Közzététel a frissítési lépéseket (nem kötelező)

A tartalmazott adatbázis áttelepítése az Azure Stack üzemelő példányok a meglévő Azure App Service kívánó ügyfelek hajtsa végre ezeket a lépéseket az Azure App Service az Azure Stack 1.3-as frissítés befejezése után:

> [!IMPORTANT]
> Ez az eljárás körülbelül 5 – 10 percet vesz igénybe.  Ez az eljárás magában foglalja a meglévő adatbázis-bejelentkezési munkamenetek leállítása.  Állásidő telepíthetőek át és ellenőrizze az Azure App Service-ben az Azure Stack a migrálás után tervezése
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

1. A részlegesen tartalmazott adatbázis konvertálása.  Ebben a lépésben állásidő számítunk fel, az összes aktív munkamenetet kell le fognak állni

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

- Feldolgozók nem érhető el a fájlkiszolgálót, amikor az App Service-ben meglévő virtuális hálózaton van üzembe helyezve, és a fájlkiszolgáló csak érhető el a magánhálózaton.  Ez is feltüntettük az Azure App Service az Azure Stack központi telepítési dokumentációjában az.

Ha úgy döntött, hogy egy meglévő virtuális hálózattal és belső IP-cím szeretne csatlakozni a fájlkiszolgáló üzembe helyezése, hozzá kell adnia egy kimenő biztonsági szabályt a feldolgozó és a fájlkiszolgáló között SMB-forgalom engedélyezése. Ehhez nyissa meg a WorkersNsg a felügyeleti portálon, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:
 * Forrás: Bármelyik
 * Forrás porttartomány: *
 * Cél: IP-címek
 * Cél IP-címtartomány: IP-címtartományt a fájlkiszolgálóhoz
 * Cél porttartomány: 445
 * Protokoll: TCP
 * Művelet: Engedélyezés
 * Prioritás: 700
 * Név: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Ismert problémák működtetése az Azure App Service az Azure Stack felhő-rendszergazdák számára

Tekintse meg az Azure Stack 1807 kibocsátási megjegyzésekben a dokumentációt.

## <a name="next-steps"></a>További lépések

- Az Azure App Service áttekintését lásd: [Azure App Service az Azure Stack áttekintése](azure-stack-app-service-overview.md).
- Hogyan készíti elő az Azure Stack App Service üzembe helyezése kapcsolatos további információkért lásd: [az App Service-ben az Azure Stack használatának megkezdése előtt](azure-stack-app-service-before-you-get-started.md).
