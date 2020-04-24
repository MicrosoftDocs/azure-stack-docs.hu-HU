---
title: App Service Azure Stack hub Update 6 kibocsátási megjegyzései
description: Ismerje meg, hogy mi a hatodik frissítés a App Service Azure Stack hub-on, az ismert problémákkal és a frissítés letöltésének módjával kapcsolatban.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 06/24/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: d41455823c6905a947a703412664fc52ff45e1a8
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77701122"
---
# <a name="app-service-on-azure-stack-hub-update-6-release-notes"></a>App Service Azure Stack hub Update 6 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések a Azure Stack hub Update 6 és bármely ismert probléma esetén Azure App Service javításait és javításait ismertetik. Az ismert problémák az üzembe helyezéssel, a frissítési folyamattal és a buildtel (a telepítés után) kapcsolatos problémákkal közvetlenül kapcsolatos problémákra vannak osztva.

> [!IMPORTANT]
> Alkalmazza az 1904-es frissítést az Azure Stack hub integrált rendszerére, vagy telepítse a legújabb Azure Stack fejlesztői csomagot a Azure App Service 1,6 üzembe helyezése előtt.


## <a name="build-reference"></a>Build referenciája

A App Service Azure Stack hub Update 6 Build száma **82.0.1.50**

### <a name="prerequisites"></a>Előfeltételek

Az üzembe helyezés megkezdése előtt tekintse át az [első lépések dokumentációját](azure-stack-app-service-before-you-get-started.md) .

Mielőtt megkezdené a Azure App Service frissítését Azure Stack hub-ról 1,6-re:

- Győződjön meg arról, hogy az összes szerepkör készen áll a Azure App Service adminisztráció Azure Stack hub felügyeleti portálon

- A App Service és a fő adatbázisok biztonsági mentése:
  - AppService_Hosting;
  - AppService_Metering;
  - Mester

- A bérlői alkalmazás tartalmának fájlmegosztás biztonsági mentése

- Az **Egyéni szkriptek bővítményének** **1.9.1** -verziójának szindikátusa a piactéren

### <a name="new-features-and-fixes"></a>Új funkciók és javítások

A Azure Stack hub Update 6 Azure App Service a következő javításokat és javításokat tartalmazza:

- A **app Service bérlő, a rendszergazda, a functions portál és a kudu eszközök**frissítései. Konzisztens Azure Stack hub Portal SDK verziójával.

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

### <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

> [!IMPORTANT]
> Ha megadta a App Service erőforrás-szolgáltatót egy SQL always on példánnyal, [fel kell vennie a appservice_hosting és a appservice_metering adatbázist egy rendelkezésre állási csoportba](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) , és szinkronizálnia kell az adatbázisokat, hogy megakadályozza a szolgáltatás elvesztését egy adatbázis-feladatátvétel esetén.

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Ismert problémák a Cloud adminok operációs Azure App Service Azure Stack központban

Tekintse meg az [Azure stack Hub 1908 kibocsátási megjegyzései](/azure-stack/operator/release-notes?view=azs-1908) dokumentációját

### <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack-hub"></a>Ismert problémák a bérlők számára a Azure Stack hub-on Azure App Service alkalmazások üzembe helyezése során

- A központi telepítési központ szürkén jelenik meg

A bérlők még nem használhatják az üzembe helyezési központot, amely a nyilvános felhőben, a 2018-es végén megjelent szolgáltatás.  A bérlők a portálon, a CLI-n és a PowerShellen keresztül továbbra is használhatják a szabványos üzembe helyezési módszereket (FTP, web Deploy, git stb.).

- Központi telepítési beállítások (klasszikus) UX és központi telepítési hitelesítő adatok portál beállításai nem érhetők el

Az üzembe helyezési lehetőségek és a központi telepítési hitelesítő adatok felhasználói élményének elérése érdekében Azure Stack a bérlőknek az URL-cím formátuma alapján kell elérniük a portált – https://portal.&lt; *régió*&gt;. &lt; *FQDN*FQDN&gt;/? websitesExtension_oldvsts = True – a ASDK a következő lesz [https://portal.local.azurestack.external/?websitesExtension_oldvsts=true](https://portal.local.azurestack.external/?websitesExtension_oldvsts=true) , majd a szokásos módon navigáljon az alkalmazásaihoz.

- Az Azure Function monitoring folyamatosan megjeleníti a betöltést a portálon

Amikor megkísérli figyelni az egyes függvényeket, a felhasználói portálon nem fog megjelenni a Meghívási napló, a sikeres műveletek száma vagy a hibák száma.  A funkció újbóli engedélyezéséhez lépjen a **függvényalkalmazás**, lépjen a **platform funkciók**menüpontra, és válassza az **Alkalmazásbeállítások**lehetőséget.  Adjon hozzá egy új Alkalmazásbeállítás- **AzureWebJobsDashboard** nevet, és állítsa be az értéket a AzureWebJobsStorage beállítással megegyező értékre.  Ezután nyissa meg a figyelés nézetet a függvényben, és látni fogja a figyelési adatokat.

## <a name="next-steps"></a>További lépések

- A Azure App Service áttekintését lásd: [Azure App Service Azure stack hub – áttekintés](azure-stack-app-service-overview.md).
- A App Service Azure Stack hub-on való telepítésének előkészítésével kapcsolatos további információkért lásd: [az Azure stack hub-beli app Service első lépéseinek](azure-stack-app-service-before-you-get-started.md)megkezdése.
