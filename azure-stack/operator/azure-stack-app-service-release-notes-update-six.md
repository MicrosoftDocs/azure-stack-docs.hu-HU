---
title: App Service Azure Stack hub Update 6 kibocsátási megjegyzései
description: Frissítse a 6. Azure Stack hub App Service vonatkozó kibocsátási megjegyzéseit, beleértve az új funkciókat, javításokat és ismert problémákat.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 06/24/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 974b338ff7da9bd4442d4aadc02d475bd01686df
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374627"
---
# <a name="app-service-on-azure-stack-hub-update-6-release-notes"></a>App Service Azure Stack hub Update 6 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések ismertetik a Azure Stack hub Update 6-os verziójában Azure App Service új funkcióit, javításait és ismert problémáit. Az ismert problémák két szakaszra vannak osztva: a frissítési folyamattal és a buildtel (a telepítés után) kapcsolatos problémákról.

> [!IMPORTANT]
> Alkalmazza az 1904-es frissítést az Azure Stack hub integrált rendszerére, vagy telepítse a legújabb Azure Stack Development Kit (ASDK) a Azure App Service 1,6 telepítése előtt.

## <a name="build-reference"></a>Build referenciája

A App Service Azure Stack hub Update 6 Build száma **82.0.1.50**.

## <a name="prerequisites"></a>Előfeltételek

Az üzembe helyezés megkezdése előtt tekintse [meg az előfeltételek a App Service Azure stack hub-ban történő telepítéséhez](azure-stack-app-service-before-you-get-started.md) című témakört.

Mielőtt megkezdené a Azure App Service frissítését Azure Stack hub-ról 1,6-re:

- Győződjön meg arról, hogy az összes szerepkör készen áll Azure App Service felügyeletre az Azure Stack hub felügyeleti portálján.

- App Service titkos kódok biztonsági mentése az Azure Stack hub felügyeleti portálján a App Service felügyelet használatával

- A App Service és a fő adatbázisok biztonsági mentése:
  - AppService_Hosting;
  - AppService_Metering;
  - master

- A bérlői alkalmazás tartalmának fájlmegosztás biztonsági mentése.

  > [!Important]
  > A Felhőbeli operátorok felelősek a fájlkiszolgáló és a SQL Server karbantartásához és üzemeltetéséhez.  Az erőforrás-szolgáltató nem kezeli ezeket az erőforrásokat.  A felhő operátor feladata a App Service adatbázisok és a bérlői tartalom fájlmegosztás biztonsági mentése.

- Adja meg az **Egyéni szkriptek bővítményének** **1.9.1** verzióját az Azure stack hub Piactérről.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások

A Azure Stack hub Update 6 Azure App Service a következő javításokat és javításokat tartalmazza:

- Frissítések **app Service bérlő, a rendszergazda, a functions portál és a kudu eszközökhöz**. Konzisztens Azure Stack hub Portal SDK verziójával.

- A **Azure functions Runtime** és a **v 1.0.12299**frissítése.

- Az alapszolgáltatások frissítései a megbízhatóság és a hibák javításához, ami lehetővé teszi a gyakori problémák egyszerűbb diagnosztizálását.

- **A következő alkalmazás-keretrendszerek és eszközök frissítései**:

  - ASP.NET Core 2.2.4
  - NodeJS 10.15.2
  - Zulu OpenJDK 8.36.0.1
  - Tomcat 7.0.81
  - Tomcat 8.5.37
  - Tomcat 9.0.14
  - PHP-5.6.39
  - PHP-7.0.33
  - PHP-7.1.25
  - PHP-7.2.13
  - Frissített kudu – 81.10329.3844

- Az **összes szerepkör mögöttes operációs rendszerének frissítései**:
  - [2019-04 összegző frissítés a Windows Server 2016 x64-alapú rendszerekhez (KB4493473)](https://support.microsoft.com/help/4493473/windows-10-update-kb4493473)

## <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

> [!IMPORTANT]
> Ha a App Service erőforrás-szolgáltatót egy SQL always on-példánnyal adta meg, akkor [a appservice_hosting és a appservice_metering adatbázisokat hozzá kell adnia egy rendelkezésre állási csoporthoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) , és szinkronizálnia kell az adatbázisokat, hogy megakadályozza a szolgáltatás elvesztését egy adatbázis-feladatátvétel esetén.

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)

- A feldolgozók nem tudják elérni a fájlkiszolgálón, ha a App Service egy meglévő virtuális hálózatban van telepítve, és a fájlkiszolgáló csak a magánhálózaton érhető el, ahogy az a Azure App Service Azure Stack hub telepítési dokumentációjában szerepel.

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

Tekintse meg az [Azure stack Hub 1908 kibocsátási megjegyzései](/azure-stack/operator/release-notes?view=azs-1908)dokumentációját.

## <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack-hub"></a>Ismert problémák a bérlők számára a Azure Stack hub-on Azure App Service alkalmazások üzembe helyezése során

- A központi telepítési központ szürkén jelenik meg/nem érhető el.

    A bérlők még nem használhatják az üzembe helyezési központot, amely a nyilvános felhőben, a 2018-es végén megjelent szolgáltatás. A bérlők a portálon, a CLI-n és a PowerShellen keresztül továbbra is használhatják a szabványos üzembe helyezési módszereket (FTP, web Deploy, git stb.).

- A központi telepítési beállítások (klasszikus) UX és az üzembe helyezési hitelesítő adatok portál beállításai nem érhetők el.

    Az üzembe helyezési lehetőségek és a központi telepítési hitelesítő adatok felhasználói felületének eléréséhez az Azure Stack hub üzembe helyezése során a bérlőknek az URL-cím formátuma alapján kell elérniük a portált: `https://portal.&lt;*region*&gt;.&lt;*FQDN*&gt;/?websitesExtension_oldvsts=true` -ami a ASDK lenne `https://portal.local.azurestack.external/?websitesExtension_oldvsts=true` , majd navigáljon az alkalmazásaihoz.

- Az Azure Function monitoring folyamatosan megjeleníti a betöltést a portálon.

    Amikor megkísérli figyelni az egyes függvényeket a felhasználói portálon, nem fog megjelenni a Meghívási napló, a sikeres műveletek száma vagy a hibák száma. A funkció újbóli engedélyezéséhez lépjen a **függvényalkalmazás**, lépjen a **platform funkciók**menüpontra, és válassza az **Alkalmazásbeállítások**lehetőséget.  Vegyen fel egy **AzureWebJobsDashboard** nevű új alkalmazás-beállítást, és állítsa be az értéket a AzureWebJobsStorage beállítással megegyező értékre. Ezután nyissa meg a figyelés nézetet a függvényben, és tekintse meg a figyelési adatokat.

## <a name="next-steps"></a>Következő lépések

- A Azure App Service áttekintését lásd: [Azure app Service és Azure Functions Azure stack hub – áttekintés](azure-stack-app-service-overview.md).
- További információ a App Service telepítésének előkészítéséről Azure Stackn: [app Service üzembe helyezésének Előfeltételei Azure stack hub-on](azure-stack-app-service-before-you-get-started.md).