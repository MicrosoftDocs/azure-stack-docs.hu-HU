---
title: App Service on Azure Stack Update 6 kibocsátási megjegyzései | Microsoft Docs
description: Ismerje meg, hogy mi a 6. frissítés a App Service Azure Stack, az ismert problémák és a frissítés letöltésének helye.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 93ec7faec82fad101c7bab2d2cec2783c8a060da
ms.sourcegitcommit: c46d913ebfa4cb6c775c5117ac5c9e87d032a271
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101021"
---
# <a name="app-service-on-azure-stack-update-6-release-notes"></a>App Service on Azure Stack Update 6 kibocsátási megjegyzései

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ezek a kibocsátási megjegyzések ismertetik a Azure App Service Azure Stack Update 6 és bármely ismert probléma javításait és javításait. Az ismert problémák az üzembe helyezéssel, a frissítési folyamattal és a buildtel (a telepítés után) kapcsolatos problémákkal közvetlenül kapcsolatos problémákra vannak osztva.

> [!IMPORTANT]
> Alkalmazza a 1904-es frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack fejlesztői csomagot a Azure App Service 1,6 üzembe helyezése előtt.


## <a name="build-reference"></a>Hivatkozás létrehozása

A App Service Azure Stack Update 6 Build száma **82.0.1.50**

### <a name="prerequisites"></a>Előfeltételek

Az üzembe helyezés megkezdése előtt tekintse át az [első lépések dokumentációját](azure-stack-app-service-before-you-get-started.md) .

Mielőtt megkezdené a Azure App Service frissítését Azure Stack 1,6-re:

- Győződjön meg arról, hogy az összes szerepkör készen áll a Azure App Service adminisztráció Azure Stack felügyeleti portálon

- A App Service és a fő adatbázisok biztonsági mentése:
  - AppService_Hosting;
  - AppService_Metering;
  - Fő

- A bérlői alkalmazás tartalmának fájlmegosztás biztonsági mentése

- Az **Egyéni szkriptek bővítményének** **1.9.1** -verziójának szindikátusa a piactéren

### <a name="new-features-and-fixes"></a>Új funkciók és javítások

A Azure Stack Update 6 Azure App Service a következő javításokat és javításokat tartalmazza:

- A **app Service bérlő, a rendszergazda, a functions portál és a kudu eszközök**frissítései. Konzisztens a Azure Stack Portal SDK verziójával.

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
> Ha megadta a App Service erőforrás-szolgáltatót egy SQL always on-példánnyal, [hozzá kell adnia a appservice_hosting-és appservice_metering-adatbázisokat egy rendelkezésre állási csoporthoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) , és szinkronizálnia kell az adatbázisokat, hogy elkerülje a szolgáltatás elvesztését a következő esetekben: egy adatbázis-feladatátvétel.

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

- A feldolgozók nem tudják elérni a fájlkiszolgálón, ha a App Service egy meglévő virtuális hálózatban van telepítve, és a fájlkiszolgáló csak a magánhálózaton érhető el, ahogy az a Azure App Service Azure Stack üzembe helyezési dokumentációban is szerepel.

Ha úgy döntött, hogy egy meglévő virtuális hálózatra és egy belső IP-címet helyez üzembe a fájlkiszolgálón való kapcsolódáshoz, hozzá kell adnia egy kimenő biztonsági szabályt, amely engedélyezi az SMB-forgalmat a munkavégző alhálózat és a fájlkiszolgáló között. Nyissa meg a WorkersNsg a felügyeleti portálon, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:
 * Forrás: Any
 * Forrásoldali porttartomány: *
 * Cél: IP-címek
 * Cél IP-címtartomány: A fájlkiszolgáló IP-címeinek tartománya
 * Célport tartománya: 445
 * Protokoll: TCP
 * Művelet: Allow
 * Fontosság: 700
 * Név: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Ismert problémák a Cloud adminok operációs Azure App Service Azure Stack

Tekintse meg a dokumentációt a [Azure Stack 1908 kibocsátási megjegyzésekben](/azure-stack/operator/release-notes?view=azs-1908)

### <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack"></a>Ismert problémák a bérlők számára az alkalmazások Azure App Serviceon való üzembe helyezéséhez Azure Stack

- A központi telepítési központ szürkén jelenik meg

A bérlők még nem használhatják az üzembe helyezési központot, amely a nyilvános felhőben, a 2018-es végén megjelent szolgáltatás.  A bérlők a portálon, a CLI-n és a PowerShellen keresztül továbbra is használhatják a szabványos üzembe helyezési módszereket (FTP, web Deploy, git stb.).

- Központi telepítési beállítások (klasszikus) UX és központi telepítési hitelesítő adatok portál beállításai nem érhetők el

Az üzembe helyezési lehetőségek és az üzembe helyezési hitelesítő adatok felhasználói élményének elérése érdekében Azure Stack a bérlőknek az URL-cím formátuma alapján https://portal.&lt kell elérniük a portált –; *régió.* &gt;&lt; *Teljes tartománynév* /? websitesExtension_oldvsts = True – a ASDK [https://portal.local.azurestack.external/?websitesExtension_oldvsts=true](https://portal.local.azurestack.external/?websitesExtension_oldvsts=true) esetében, majd a szokásos módon navigáljon az alkalmazásaihoz. &gt;

- Az Azure Function monitoring folyamatosan megjeleníti a betöltést a portálon

Amikor megkísérli figyelni az egyes függvényeket, a bérlői portálon nem fog megjelenni a Meghívási napló, a sikeres műveletek száma vagy a hibák száma.  A funkció újbóli engedélyezéséhez lépjen a **függvényalkalmazás**, lépjen a **platform funkciók**menüpontra, és válassza az **Alkalmazásbeállítások**lehetőséget.  Adjon hozzá egy új Alkalmazásbeállítás- **AzureWebJobsDashboard** nevet, és állítsa be az értéket a AzureWebJobsStorage beállítással megegyező értékre.  Ezután nyissa meg a figyelés nézetet a függvényben, és látni fogja a figyelési adatokat.

## <a name="next-steps"></a>További lépések

- A Azure App Service áttekintését lásd: [Azure App Service Azure stack áttekintése](azure-stack-app-service-overview.md).
- További információ a Azure Stack App Service telepítésének előkészítéséről: [mielőtt megkezdi a Azure Stack app Serviceének](azure-stack-app-service-before-you-get-started.md)megkezdését.
