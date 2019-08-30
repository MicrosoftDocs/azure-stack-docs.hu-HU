---
title: App Service on Azure Stack Update 7 kibocsátási megjegyzései | Microsoft Docs
description: Ismerje meg, hogy mi a hét frissítése App Service on Azure Stack, az ismert problémák és a frissítés letöltése.
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
ms.date: 08/29/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: ec07277043068835d1d1d5a41285ee5df5ee7691
ms.sourcegitcommit: 701685f0b59e5a3d1a8d39fe477b8df701a51cd2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165339"
---
# <a name="app-service-on-azure-stack-update-7-release-notes"></a>App Service on Azure Stack Update 7 kibocsátási megjegyzései

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ezek a kibocsátási megjegyzések ismertetik a Azure App Service Azure Stack Update 7 és bármely ismert probléma javításait és javításait. Az ismert problémák az üzembe helyezéssel, a frissítési folyamattal és a buildtel (a telepítés után) kapcsolatos problémákkal közvetlenül kapcsolatos problémákra vannak osztva.

> [!IMPORTANT]
> Alkalmazza a 1907-es frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack fejlesztői csomagot a Azure App Service 1,7 üzembe helyezése előtt.


## <a name="build-reference"></a>Hivatkozás létrehozása

A App Service a Azure Stack Update 7 Build száma **84.0.2.10**

### <a name="prerequisites"></a>Előfeltételek

Az üzembe helyezés megkezdése előtt tekintse át az [első lépések dokumentációját](azure-stack-app-service-before-you-get-started.md) .

Mielőtt megkezdené a Azure App Service frissítését Azure Stack 1,7-re:

- Győződjön meg arról, hogy az összes szerepkör készen áll a Azure App Service adminisztráció Azure Stack felügyeleti portálon

- A App Service és a fő adatbázisok biztonsági mentése:
  - AppService_Hosting;
  - AppService_Metering;
  - Fő

- A bérlői alkalmazás tartalmának fájlmegosztás biztonsági mentése

- Az **Egyéni szkriptek** bővítményének **1.9.3** -verziójának szindikátusa a piactéren

### <a name="new-features-and-fixes"></a>Új funkciók és javítások

A (z) Azure Stack Update 7-es Azure App Service a következő javításokat és javításokat tartalmazza:

- A **app Service bérlő, a rendszergazda, a functions portál és a kudu eszközök**frissítései. Konzisztens a Azure Stack Portal SDK verziójával.

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
  - A jelen kiadást használó felhasználók a közzétett dokumentációnak megfelelően konfigurálhatják a web/API/functions alkalmazás hozzáférési korlátozásait – [Azure app Service hozzáférési korlátozások](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions): A Azure Stack Azure App Service nem támogatja a szolgáltatási végpontokat.

- A **központi telepítési beállítások (klasszikus) funkcióinak visszaállítása**:
  - A felhasználók ismét használhatják az üzembe helyezési lehetőségeket (klasszikus) az alkalmazások GitHub-, bitbucket-, Dropbox-, OneDrive-, helyi és külső adattárakból való üzembe helyezésének konfigurálásához, valamint az alkalmazások telepítési hitelesítő adatainak beállításához.

- Az **Azure functions figyelése** megfelelően van konfigurálva.

- **Windows Update viselkedés**: Az ügyfelek visszajelzései alapján módosítjuk a Windows Update konfigurálásának módját a 7. frissítés App Service szerepköreiben:
  - Három mód:
    - Letiltva – Windows Update szolgáltatás letiltva, a Windows a (z) Azure stack kiadásokban Azure app Service által szállított tudásbázissal lesz frissítve.
    - Az **automatikus** Windows Update szolgáltatás engedélyezve van, és a Windows Update meghatározza, hogyan és mikor kell frissíteni;
    - A felügyelt Windows Update szolgáltatás le van tiltva, Azure app Service az egyes szerepkörök OnStart során Windows Update ciklust fog végezni.

  **Új** Központi telepítések – a Windows Update szolgáltatás alapértelmezés szerint le van tiltva.

  **Meglévő** Központi telepítések – Ha módosította a beállítást a vezérlőn, az érték a **false (hamis** ) értékről letiltva értékűre változik, és az **igaz** érték egy korábbi értéke lesz **automatikusan**

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

Tekintse meg a dokumentációt a [Azure Stack 1907 kibocsátási megjegyzésekben](azure-stack-release-notes-1907.md)

## <a name="next-steps"></a>További lépések

- A Azure App Service áttekintését lásd: [Azure App Service Azure stack áttekintése](azure-stack-app-service-overview.md).
- További információ a Azure Stack App Service telepítésének előkészítéséről: mielőtt megkezdi a [Azure Stack app Serviceének](azure-stack-app-service-before-you-get-started.md)megkezdését.
