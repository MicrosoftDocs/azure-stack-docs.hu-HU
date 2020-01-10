---
title: App Service Azure Stack hub Update 7 kibocsátási megjegyzései | Microsoft Docs
description: Ismerje meg, hogy mi a hét frissítése a App Service Azure Stack hub-on, az ismert problémákról, valamint a frissítés letöltésének módjáról.
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
ms.date: 10/11/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: ca1206a23f4f275304b975164dd757f274cc000c
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75804503"
---
# <a name="app-service-on-azure-stack-hub-update-7-release-notes"></a>App Service Azure Stack hub Update 7 kibocsátási megjegyzései

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek és Azure Stack Development Kit*

Ezek a kibocsátási megjegyzések ismertetik a Azure Stack hub Update 7 és az ismert problémák Azure App Serviceének javításait és javításait. Az ismert problémák az üzembe helyezéssel, a frissítési folyamattal és a buildtel (a telepítés után) kapcsolatos problémákkal közvetlenül kapcsolatos problémákra vannak osztva.

> [!IMPORTANT]
> Alkalmazza az 1907-es frissítést az Azure Stack hub integrált rendszerére, vagy telepítse a legújabb Azure Stack fejlesztői csomagot a Azure App Service 1,7 üzembe helyezése előtt.


## <a name="build-reference"></a>Build referenciája

A App Service on Azure Stack hub Update 7 Build száma **84.0.2.10**

### <a name="prerequisites"></a>Előfeltételek

Az üzembe helyezés megkezdése előtt tekintse át az [első lépések dokumentációját](azure-stack-app-service-before-you-get-started.md) .

Mielőtt megkezdené a Azure App Service frissítését Azure Stack hub-ról 1,7-re:

- Győződjön meg arról, hogy az összes szerepkör készen áll a Azure App Service adminisztráció Azure Stack hub felügyeleti portálon

- A App Service és a fő adatbázisok biztonsági mentése:
  - AppService_Hosting;
  - AppService_Metering;
  - Fő

- A bérlői alkalmazás tartalmának fájlmegosztás biztonsági mentése

- Az **Egyéni szkriptek bővítményének** **1.9.3** -verziójának szindikátusa a piactéren

### <a name="new-features-and-fixes"></a>Új funkciók és javítások

A Azure App Service on Azure Stack hub Update 7 a következő javításokat és javításokat tartalmazza:

- A [CVE-2019-1372](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1372) távoli kódfuttatást lehetővé tévő biztonsági rés feloldása

- A **app Service bérlő, a rendszergazda, a functions portál és a kudu eszközök**frissítései. Konzisztens Azure Stack hub Portal SDK verziójával.

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
  - A jelen kiadást használó felhasználók a közzétett dokumentáció alapján konfigurálhatják a webes/API/functions [Azure app Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)-alkalmazásokhoz való hozzáférési korlátozásokat, **Megjegyzés**: a (z) Azure app Service on Azure stack hub nem támogatja a szolgáltatási végpontokat.

- A **központi telepítési beállítások (klasszikus) funkcióinak visszaállítása**:
  - A felhasználók ismét használhatják az üzembe helyezési lehetőségeket (klasszikus) az alkalmazások GitHub-, bitbucket-, Dropbox-, OneDrive-, helyi és külső adattárakból való üzembe helyezésének konfigurálásához, valamint az alkalmazások telepítési hitelesítő adatainak beállításához.

- Az **Azure functions figyelése** megfelelően van konfigurálva.

- **Windows Update viselkedés**: a felhasználói visszajelzések alapján módosítjuk, hogy a Windows Update hogyan van konfigurálva app Service szerepkörökben a 7. frissítésből:
  - Három mód:
    - **Letiltva** – Windows Update szolgáltatás letiltva, a Windows a (z) Azure stack hub-kiadásokban Azure app Service által szállított kb-val frissül.
    - Az **automatikus** Windows Update szolgáltatás engedélyezve van, és a Windows Update meghatározza, hogyan és mikor kell frissíteni;
    - A **felügyelt** Windows Update szolgáltatás le van tiltva, Azure app Service az egyes szerepkörök OnStart során Windows Update ciklust fog végezni.

  **Új** Központi telepítések – a Windows Update szolgáltatás alapértelmezés szerint le van tiltva.

  **Meglévő** Központi telepítések – Ha módosította a beállítást a vezérlőn, az érték a **false (hamis** ) értékről **Letiltva** értékűre változik, és az **igaz** érték egy korábbi értéke lesz **automatikusan**

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

Tekintse meg az [Azure stack Hub 1907 kibocsátási megjegyzései](azure-stack-release-notes-1907.md) dokumentációját

## <a name="next-steps"></a>Következő lépések

- A Azure App Service áttekintését lásd: [Azure App Service Azure stack hub – áttekintés](azure-stack-app-service-overview.md).
- A App Service Azure Stack hub-on való telepítésének előkészítésével kapcsolatos további információkért lásd: [az Azure stack hub-beli app Service első lépéseinek](azure-stack-app-service-before-you-get-started.md)megkezdése.
