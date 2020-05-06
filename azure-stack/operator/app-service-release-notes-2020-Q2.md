---
title: App Service Azure Stack hub 2002 Q2 kibocsátási megjegyzései
description: Ismerje meg, mi a 2002 Q2-kiadás a Azure Stack hub App Service, az ismert problémák és a frissítés letöltésének helyétől.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/30/2020
ms.openlocfilehash: 36c2fabf06df1725938b512752f40f70728dd8e9
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82848282"
---
# <a name="app-service-on-azure-stack-hub-2020-q2-release-notes"></a>App Service Azure Stack hub 2020 Q2 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések a Azure App Service Azure Stack hub 2020 Q2-ben és bármely ismert problémával kapcsolatos javításokat és javításokat ismertetik. Az ismert problémák az üzembe helyezéssel, a frissítési folyamattal és a buildtel (a telepítés után) kapcsolatos problémákkal közvetlenül kapcsolatos problémákra vannak osztva.

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

## <a name="build-reference"></a>Build referenciája

A App Service a Azure Stack hub 2020 Q2 Build száma **87.0.2.10**

## <a name="prerequisites"></a>Előfeltételek

Az üzembe helyezés megkezdése előtt tekintse át az [első lépések dokumentációját](azure-stack-app-service-before-you-get-started.md) .

Mielőtt megkezdené a Azure App Service frissítését a Azure Stack a 2020 Q2-re:

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

A (z) Azure Stack Q2-es frissítésének Azure App Service a következő javításokat és javításokat tartalmazza:

- A **app Service bérlő, a rendszergazda, a functions portál és a kudu eszközök**frissítései. Konzisztens a Azure Stack Portal SDK verziójával.

- A **Azure functions Runtime** és a **v 1.0.13021**frissítése.

- Az alapszolgáltatások frissítései a megbízhatóság és a hibák javításához, ami lehetővé teszi a gyakori problémák egyszerűbb diagnosztizálását.

- **A következő alkalmazás-keretrendszerek és eszközök frissítései**:
  - ASP.NET-keretrendszer 4.7.2
  - ASP.NET Core 3.1.3
  - ASP.NET Core v2 13.1.19331.0 modul
  - PHP-7.4.2
  - Frissített kudu – 86.20224.4450
  - NodeJS 
    - 8.17.0
    - 10.19.0
    - 12.13.0
    - 12.15.0
  - NPM
    - 5.6.0
    - 6.1.0
    - 6.12.0
    - 6.13.4
  
- Az **összes szerepkör mögöttes operációs rendszerének frissítései**:
  - [2020-04 összegző frissítés a Windows Server 2016 x64-alapú rendszerekhez (KB4550929)](https://support.microsoft.com/help/4550929)
  - [2020-04 karbantartási verem frissítése a Windows Server 2016 x64-alapú rendszerekhez (KB4550994)](https://support.microsoft.com/help/4550994)

- **A Windows Server összegző frissítései mostantól a központi telepítés és a frissítés részeként lesznek alkalmazva a vezérlő szerepköreire**

- Az **alapértelmezett virtuális gép és a méretezési csoport frissítése az új központi telepítésekhez**: a nyilvános felhőalapú szolgáltatással való konzisztencia fenntartása érdekében a Azure app Service új központi telepítései a Azure stack hub-on a következő SKU-ket fogják használni az erőforrás-szolgáltató üzemeltetéséhez használt mögöttes gépek és méretezési csoportok számára.
  
  | Szerepkör | Minimális példányszám | Minimális SKU |
  | --- | --- | --- |
  | Tartományvezérlő | 2 | Standard_A4_v2-(4 mag, 8192 MB) |
  | Kezelés | 1 | Standard_D3_v2-(4 mag, 14336 MB) |
  | Közzétevő | 1 | Standard_A2_v2 – (2 mag, 4096 MB) |
  | Előtér | 1 | Standard_A4_v2-(4 mag, 8192 MB) |
  | Megosztott feldolgozók | 1 | Standard_A4_v2-(4 mag, 8192 MB) |
  | Kisméretű dedikált feldolgozó | 1 | Standard_A1_v2-(1 mag, 2048 MB) |
  | Közepes méretű dedikált feldolgozó | 1 | Standard_A2_v2 – (2 mag, 4096 MB) |
  | Nagyméretű dedikált feldolgozó | 1 | Standard_A4_v2-(4 mag, 8192 MB) |

A ASDK-telepítések esetében a példányokat lekicsinyítheti alacsonyabb SKU-ra, hogy csökkentse az alapvető és a memória-végrehajtást, de a teljesítmény romlása is megtapasztalható.

## <a name="issues-fixed-in-this-release"></a>Ebben a kiadásban rögzített problémák

- A frissítések mostantól készen állnak, ha az SQL always on-fürt feladatátvétele a másodlagos csomópontra történik
- A Azure App Service Azure Stack hub-on lévő új központi telepítései már nem igénylik az adatbázisok manuális átalakítását a foglalt adatbázisokra.
- Ha további munkatársakat vagy infrastrukturális szerepkör-példányokat ad hozzá, kézi beavatkozás nélkül is elvégezhető
- Az egyéni munkavégző rétegek hozzáadása manuális beavatkozás nélkül végezhető el megfelelően
- Az egyéni feldolgozói rétegek eltávolítása mostantól a portál hibái nélkül fejeződött be
- A feldolgozók már nem állnak készen, ha a helyi lemez nem áll rendelkezésre.
- Időtúllépés történt az Azure Resource Manager tanúsítvány beolvasásakor
- A beolvasott üzenetek száma, a kiszolgáló naplóiból, és a felügyeleti portálon megjelenő üzenet a maximális Azure Resource Manager kérelmek méretének alatti tartózkodásra korlátozódik
- Időtúllépési hiba a használati szolgáltatás indítási hibái miatt
- Az adatbázis-telepítési probléma megoldódott az Orchard CMS-helyek létrehozásakor
- A vezérlők mostantól frissülnek a Windows kumulatív frissítéseivel az üzembe helyezés és a frissítés részeként
- Az egyéni tartomány ellenőrzésének sikertelensége esetén a App Service többé nem zárolja a műveleteket.

## <a name="pre-update-steps"></a>Frissítés előtti lépések

Tekintse át a [frissítés ismert problémáit](#known-issues-update) , és végezze el a szükséges műveleteket.

## <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

> [!IMPORTANT]
> Ha megadta a App Service erőforrás-szolgáltatót egy SQL always on példánnyal, [fel kell vennie a appservice_hosting és a appservice_metering adatbázist egy rendelkezésre állási csoportba](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) , és szinkronizálnia kell az adatbázisokat, hogy megakadályozza a szolgáltatás elvesztését egy adatbázis-feladatátvétel esetén.

## <a name="known-issues-update"></a>Ismert problémák (frissítés)

- Olyan helyzetekben, amikor az ügyfél átalakította a appservice_hosting és appservice_metering adatbázisokat a tárolt adatbázisba, a frissítés sikertelen lehet, ha a bejelentkezések nem lettek áttelepítve a tárolt felhasználók számára.

Azok az ügyfelek, akik átalakították a appservice_hosting és appservice_metering adatbázisokat a tárolt adatbázisba, és nem sikerült migrálni az adatbázis-bejelentkezéseket a tárolt felhasználók számára, frissítési hibák léphetnek fel.  

Az ügyfeleknek a következő szkriptet kell végrehajtaniuk a appservice_hosting és a appservice_metering SQL Server üzemeltetése előtt, mielőtt a Azure App Service Azure Stack hub-telepítésről 2020 Q2-re frissítené.  **Ez a szkript nem romboló jellegű, és nem okoz állásidőt**.

A szkriptnek a következő feltételekkel kell futnia

1. Rendszergazda jogosultsággal rendelkező felhasználó, például az SQL SA-fiók;
1. Ha az SQL always on szolgáltatást használja, győződjön meg arról, hogy az SQL-példányon fut a parancsfájl, amely tartalmazza az összes App Service bejelentkezést az űrlapon:
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

- A bérlők nem hozhatnak létre App Service csomagot a bérlői portálon a App Service Plan nézet új funkciójával

Új alkalmazás létrehozásakor a bérlők App Service terveket hozhatnak létre az alkalmazás létrehozása munkafolyamatban, illetve az aktuális alkalmazás App Service tervének módosításakor, vagy az App Service terv Marketplace-elemén keresztül.

- A leválasztott környezetek nem támogatják az egyéni tartományokat

App Service végrehajtja a tartomány tulajdonjogának ellenőrzését a nyilvános DNS-végpontokon, mivel az egyéni tartományok nem támogatottak a leválasztott forgatókönyvekben.

## <a name="next-steps"></a>További lépések

- A Azure App Service áttekintését lásd: [Azure App Service Azure stack áttekintése](azure-stack-app-service-overview.md).
- További információ a Azure Stack App Service telepítésének előkészítéséről: [mielőtt megkezdi a Azure Stack app Serviceének](azure-stack-app-service-before-you-get-started.md)megkezdését.
