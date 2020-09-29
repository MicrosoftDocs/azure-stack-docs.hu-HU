---
title: App Service Azure Stack hub Update 7 kibocsátási megjegyzései
description: Az Azure Stack hub App Service vonatkozó kibocsátási megjegyzései, beleértve az új funkciókat, javításokat és ismert problémákat.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 10/11/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 449c1dac3e983aacefe4d69581ee854239d435ae
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86489606"
---
# <a name="app-service-on-azure-stack-hub-update-7-release-notes"></a>App Service Azure Stack hub Update 7 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések a Azure Stack hub Update 7-es verziójában Azure App Service új funkcióit, javításait és ismert problémáit ismertetik. Az ismert problémák két szakaszra vannak osztva: a frissítési folyamattal és a buildtel (a telepítés után) kapcsolatos problémákról.

> [!IMPORTANT]
> Alkalmazza a 1910-es frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack Development Kitt (ASDK) a Azure App Service 1,7 üzembe helyezése előtt.

## <a name="build-reference"></a>Build referenciája

A App Service Azure Stack hub Update 7 Build száma **84.0.2.10**.

## <a name="prerequisites"></a>Előfeltételek

Az üzembe helyezés megkezdése előtt tekintse [meg a App Service Azure stack hub-ban való üzembe helyezésének előfeltételeit](azure-stack-app-service-before-you-get-started.md) .

Mielőtt megkezdené a Azure App Service frissítését Azure Stack hub-ról 1,7-re:

- Győződjön meg arról, hogy az összes szerepkör készen áll Azure App Service felügyeletre az Azure Stack hub felügyeleti portálján.

- App Service titkos kódok biztonsági mentése az Azure Stack hub felügyeleti portálján a App Service felügyelet használatával

- A App Service és a fő adatbázisok biztonsági mentése:
  - AppService_Hosting;
  - AppService_Metering;
  - master

- A bérlői alkalmazás tartalmának fájlmegosztás biztonsági mentése.

  > [!Important]
  > A Felhőbeli operátorok felelősek a fájlkiszolgáló és a SQL Server karbantartásához és üzemeltetéséhez.  Az erőforrás-szolgáltató nem kezeli ezeket az erőforrásokat.  A felhő operátor feladata a App Service adatbázisok és a bérlői tartalom fájlmegosztás biztonsági mentése.

- Adja meg az **Egyéni szkriptek bővítményének** **1.9.3** verzióját az Azure stack hub Piactérről.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások

A Azure App Service on Azure Stack hub Update 7 a következő javításokat és javításokat tartalmazza:

- A [CVE-2019-1372](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1372) távoli kódfuttatást lehetővé tévő biztonsági rés feloldása.

- Frissítések **app Service bérlő, rendszergazda, functions portálok és kudu eszközökhöz**. Konzisztens Azure Stack hub Portal SDK verziójával.

- A **Azure functions Runtime** és a **v 1.0.12582**frissítése.

- Az alapszolgáltatások frissítései a megbízhatóság és a hibák javításához, ami lehetővé teszi a gyakori problémák egyszerűbb diagnosztizálását.

- **A következő alkalmazás-keretrendszerek és eszközök frissítései**:

  - ASP.NET Core 2.2.46
  - OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP-5.6.40
  - PHP-7.3.6
  - Frissített kudu – 82.10503.3890

- Az **összes szerepkör mögöttes operációs rendszerének frissítései**:
  - [2019-08 összegző frissítés a Windows Server 2016 x64-alapú rendszerekhez (KB4512495)](https://support.microsoft.com/help/4512495)

- **A felhasználói portálon mostantól engedélyezhető a hozzáférési korlátozások**:
  - A felhasználók mostantól a web/API/functions alkalmazásaihoz hozzáférési korlátozásokat állíthatnak be a közzétett dokumentációnak megfelelően – [Azure app Service hozzáférési korlátozásokat](/azure/app-service/app-service-ip-restrictions).
  
  > [!NOTE]
  > Az Azure Stack hub Azure App Service nem támogatja a szolgáltatási végpontokat.

- A **központi telepítési beállítások (klasszikus) funkcióinak visszaállítása**:
  - A felhasználók ismét használhatják az üzembe helyezési lehetőségeket (klasszikus) az alkalmazások GitHub-, bitbucket-, Dropbox-, OneDrive-, helyi és külső adattárakból való üzembe helyezésének konfigurálásához, valamint az alkalmazások telepítési hitelesítő adatainak beállításához.

- Az **Azure functions figyelése** megfelelően van konfigurálva.

- A **Windows Update viselkedése**: az ügyfelek visszajelzései alapján módosítottuk a 7. frissítés app Service szerepköreire Windows Update konfigurálásának módját:
  - Három mód:
    - **Letiltva** – Windows Update szolgáltatás letiltva, a Windows az Azure stack hub-kiadásokban Azure app Service által szállított kb-val frissül.
    - Az **automatikus** Windows Update szolgáltatás engedélyezve van, és Windows Update határozza meg, hogyan és mikor kell frissíteni;
    - A **felügyelt** Windows Update szolgáltatás le van tiltva, Azure app Service hajt végre Windows Update ciklust az egyéni szerepkör OnStart során.

  **Új** Központi telepítések – a Windows Update szolgáltatás alapértelmezés szerint le van tiltva.

  **Meglévő** Központi telepítések – Ha módosította a vezérlő beállítását, az érték **false** értékről **Letiltva** értékűre változik, és az **igaz** érték korábbi értéke **automatikus**lesz.

## <a name="post-deployment-steps"></a>Üzembe helyezés után lépések

> [!IMPORTANT]
> Ha megadta a App Service erőforrás-szolgáltatót egy SQL always on példánnyal, [fel kell vennie a appservice_hosting és a appservice_metering adatbázist egy rendelkezésre állási csoportba](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) , és szinkronizálnia kell az adatbázisokat, hogy megakadályozza a szolgáltatás elvesztését egy adatbázis-feladatátvétel esetén.

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

Tekintse meg az [Azure stack Hub 1907 kibocsátási megjegyzései](./release-notes.md?view=azs-2002) dokumentációját

## <a name="next-steps"></a>Következő lépések

- A Azure App Service áttekintését lásd: [Azure app Service és Azure Functions Azure stack hub – áttekintés](azure-stack-app-service-overview.md).
- További információ a App Service telepítésének előkészítéséről Azure Stack hub-on: [app Service üzembe helyezésének előfeltételei az Azure stack hub-on](azure-stack-app-service-before-you-get-started.md).
