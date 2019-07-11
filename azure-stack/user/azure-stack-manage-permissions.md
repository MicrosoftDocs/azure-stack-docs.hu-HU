---
title: Felhasználónként az Azure Stack-erőforrások kezelése |} A Microsoft Docs
description: Szolgáltatás-rendszergazda vagy bérlői ismerje meg, hogyan kezelheti a szerepköralapú hozzáférés-vezérlés (RBAC) engedélyekkel.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 20bf709cb3c2026910a1283fb0b39ba80c719390
ms.sourcegitcommit: 7f441f246242fa42147ab5aa69ddc8766ba293e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67791356"
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>A hozzáférés-vezérléssel Azure Stack Role-Based erőforrásaihoz való hozzáférés kezelése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack is támogatja a szerepköralapú hozzáférés-vezérlést (RBAC), ugyanez [kezelési biztonsági modell](https://docs.microsoft.com/azure/role-based-access-control/overview) , amely a Microsoft Azure. Az RBAC használatával kezelheti a felhasználó, csoport vagy alkalmazás-hozzáférés az előfizetések, erőforrások és szolgáltatások.

## <a name="basics-of-access-management"></a>Hozzáférés-kezelés alapjait

Szerepköralapú hozzáférés-vezérlés, hogy biztosítsa a környezetét segítségével részletes hozzáférés-vezérlést biztosít. Felhasználóknak szükségük van egy bizonyos hatókörben RBAC szerepkör hozzárendelésével pontos engedélyeket engedélyezi. A szerepkör-hozzárendelés hatóköre egy előfizetés, erőforráscsoport vagy egyetlen erőforrás lehet. Olvassa el a [szerepköralapú hozzáférés-vezérlés az Azure Portalon](https://docs.microsoft.com/azure/role-based-access-control/overview) cikk kezelési részletes információt szeretne kapni.

### <a name="built-in-roles"></a>Beépített szerepkörök

Az Azure Stack rendelkezik, amelyek minden erőforrástípus alkalmazhat három alapvető szerepkörök:

* **Tulajdonos** mindent felügyelhetnek, beleértve az erőforrásokhoz való hozzáférést.
* **Közreműködői** erőforrásokhoz való hozzáférés kivételével mindent felügyelhetnek.
* **Olvasó** , aki mindent megtekinthet, de nem végezhet módosításokat.

### <a name="resource-hierarchy-and-inheritance"></a>Erőforrás-hierarchiát és öröklődés

Az Azure Stack a következő erőforrás-hierarchia rendelkezik:

* Az egyes előfizetésekhez tartozik egy címtárban.
* Minden erőforráscsoport egy előfizetéshez tartozik.
* Minden erőforrás egy erőforráscsoportba tartozik.

A gyermek hatókörök örökölt, amely egy szülő hatókörben számára biztosítson hozzáférést. Példa:

* Hozzárendelhet a **olvasó** szerepkört az Azure AD-csoportok az előfizetések szintjén. A csoport tagjai tekinthetik minden erőforráscsoport és az erőforrás az előfizetést.
* Hozzárendelhet a **közreműködői** szerepkör erőforrás-csoportot a csoporthatókörben, egy alkalmazásba. Az alkalmazás az adott erőforráscsoportba tartozó, de nem egyéb erőforráscsoportok az előfizetés összes típusú erőforrások is kezelhetők.

### <a name="assigning-roles"></a>Szerepkörök hozzárendelése

Egynél több szerepkörhöz hozzárendelni egy felhasználóhoz, és minden egyes szerepkör társítható egy másik hatókört. Példa:

* TestUser-A az Olvasó szerepkör hozzárendelése – 1. előfizetéshez.
* TestUser-A a tulajdonosi szerepkör hozzárendelése TestVM – 1.

Az Azure [szerepkör-hozzárendelések](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) cikk megtekintése, hozzárendelése és törlése a szerepkörök részletes információkat tartalmaz.

## <a name="set-access-permissions-for-a-user"></a>Egy felhasználó hozzáférési engedélyek beállítása

A következő lépések bemutatják, hogyan engedélyek konfigurálása a felhasználó.

1. A felügyelni kívánt erőforrás tulajdonosi engedélyekkel rendelkező fiókkal jelentkezzen be.
2. A bal oldali navigációs panelen válassza az **Erőforráscsoportok** lehetőséget.
3. Válassza ki a használni kívánt engedélyeket állíthat be az erőforráscsoport nevét.
4. Az erőforrás csoport navigációs ablaktábláján válassza **hozzáférés-vezérlés (IAM)** . A **szerepkör-hozzárendelések** megtekintése az erőforráscsoport számára elérhető elemeket sorolja fel. Szűrheti és az eredmények csoportosítása.
5. Az a **hozzáférés-vezérlés** menü sávot, válassza a **Hozzáadás**.
6. A **engedélyek hozzáadása** panelen:

   * Válassza ki a hozzárendelni kívánt szerepkört az **szerepkör** legördülő listából.
   * Válassza ki a hozzárendelni kívánt erőforrást a **rendelhet hozzáféréseket** legördülő listából.
   * Válassza ki azt a felhasználót, csoportot vagy alkalmazást a címtárában, amely számára hozzáférést kíván biztosítani. A címtárban rákereshet megjelenítendő nevekre, e-mail-címekre és objektumazonosítókra.

7. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

[Egyszerű szolgáltatások létrehozása](../operator/azure-stack-create-service-principals.md)
