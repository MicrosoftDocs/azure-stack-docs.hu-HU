---
title: Az Azure Stack App Service frissítése 6 kibocsátási megjegyzései |} A Microsoft Docs
description: Ismerje meg a frissítés hat, az Azure Stack App Service, az ismert problémákról, valamint a frissítés letöltése helyét.
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
ms.date: 05/28/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: d280ffacf9cb74c519857ebafc907debc915ec21
ms.sourcegitcommit: 85367001c332ed53fba0d2294eae3c06e8578070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307840"
---
# <a name="app-service-on-azure-stack-update-6-release-notes"></a>App Service-ben az Azure Stack 6. összesített frissítéssel kibocsátási megjegyzései

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A kibocsátási megjegyzések láthatók a fejlesztései, valamint a javításokat az Azure App Service az Azure Stack frissítés 6 és olyan ismert problémákat. Ismert problémák az üzembe helyezési, frissítési folyamat és a build (telepítés utáni) problémái közvetlenül kapcsolódó problémák vannak felosztva.

> [!IMPORTANT]
> Az Azure Stackkel integrált rendszereknél 1904 frissítés alkalmazása, vagy a legújabb Azure Stack fejlesztői készletének telepítése az Azure App Service 1.6-os üzembe helyezése előtt.


## <a name="build-reference"></a>Hivatkozás létrehozása

Az App Service az Azure Stack 6. frissítés buildszámát **82.0.1.50**

### <a name="prerequisites"></a>Előfeltételek

Tekintse meg a [mielőtt elkezdené a dokumentáció](azure-stack-app-service-before-you-get-started.md) központi telepítésének megkezdése előtt.

Mielőtt elkezdené a frissítés az 1.6-os az Azure App Service az Azure Stacken:

- Győződjön meg, hogy minden szerepkör kész a az Azure App Service felügyeleti az Azure Stack felügyeleti portálon

- Az App Service-ben és a Master adatbázis biztonsági mentésére:
  - AppService_Hosting;
  - AppService_Metering;
  - Fő

- A bérlő alkalmazás tartalom fájlmegosztás biztonsági mentése

- Konzorcium a **egyéni szkriptek futtatására szolgáló bővítmény** verzió **1.9.1** a Marketplace-ről

### <a name="new-features-and-fixes"></a>Új funkciókkal és javításokkal

Az Azure App Service az Azure Stack 6-os frissítés tartalmazza a következő fejlesztések és javítások:

- Frissítések **App Services-bérlő, a rendszergazda, a Functions-portálok és eszközök a Kudu**. Az Azure Stack Portal SDK-verzió összhangban.

- Frissítések **Azure Functions runtime** való **v1.0.12299**.

- Fokozható a megbízhatóság és a hibaüzenetek gyakori problémák egyszerűbb diagnosztika engedélyezése a core-szolgáltatás frissítése.

- **A következő alkalmazás-keretrendszerek és eszközök frissítések**:
  - ASP.NET Core 2.2.4
  - NodeJS 10.15.2
  - Zulu OpenJDK 8.36.0.1
  - Tomcat 7.0.81
  - Tomcat 8.5.37
  - Tomcat 9.0.14
  - PHP 5.6.39
  - PHP 7.0.33
  - PHP 7.1.25
  - PHP 7.2.13
  - Updated Kudu to 81.10329.3844

- **Frissítések az alapul szolgáló operációs rendszer összes szerepkör**:
  - [A 2019-04 összegző frissítés-a Windows Server 2016 x64 alapú rendszerekhez (KB4493473)](https://support.microsoft.com/help/4493473/windows-10-update-kb4493473)

### <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

> [!IMPORTANT]
> Ha megadta az App Service erőforrás-szolgáltató-példánnyal SQL mindig meg kell [adja hozzá a appservice_hosting és appservice_metering adatbázisokat egy rendelkezésre állási csoport](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) és így elkerülhető, hogy az adatbázisok szinkronizálása a szolgáltatás adatbázis-feladatátvétel esetén.

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

- Feldolgozók nem érhető el a fájlkiszolgálót, ha App Service-ben üzemel egy meglévő virtuális hálózatot és a fájlkiszolgáló csak érhető el a magánhálózaton, feltüntettük az Azure App Service az Azure Stack központi telepítési dokumentációjában az.

Ha úgy döntött, hogy egy meglévő virtuális hálózattal és belső IP-cím szeretne csatlakozni a fájlkiszolgáló üzembe helyezése, hozzá kell adnia egy kimenő biztonsági szabályt a feldolgozó és a fájlkiszolgáló között SMB-forgalom engedélyezése. Nyissa meg a WorkersNsg a felügyeleti portálon, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:
 * Adatforrás: Bármely
 * Forrás porttartomány: *
 * Cél: IP-címek
 * Cél IP-címtartomány: IP-címtartományt a fájlkiszolgálóhoz
 * Cél porttartomány: 445
 * Protokoll: TCP
 * Művelet: Engedélyezés
 * Prioritás: 700
 * Név: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Ismert problémák működtetése az Azure App Service az Azure Stack felhő-rendszergazdák számára

A dokumentáció a [Azure Stack 1904 kibocsátási megjegyzései](azure-stack-release-notes-1904.md)

## <a name="next-steps"></a>További lépések

- Az Azure App Service áttekintését lásd: [Azure App Service az Azure Stack áttekintése](azure-stack-app-service-overview.md).
- Hogyan készíti elő az Azure Stack App Service üzembe helyezése kapcsolatos további információkért lásd: [az App Service-ben az Azure Stack használatának megkezdése előtt](azure-stack-app-service-before-you-get-started.md).
