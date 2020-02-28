---
title: A Azure Stack hub erőforrásaihoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel
description: Megtudhatja, hogyan kezelheti a szerepköralapú hozzáférés-vezérlés (RBAC) engedélyeit rendszergazdaként vagy bérlőként Azure Stack központban.
author: bryanla
ms.topic: article
ms.date: 09/13/2019
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 2f633e44b4646659a888df85e51450147104710e
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703978"
---
# <a name="manage-access-to-resources-in-azure-stack-hub-with-role-based-access-control"></a>A Azure Stack hub erőforrásaihoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel

Azure Stack hub támogatja a szerepköralapú hozzáférés-vezérlést (RBAC), ugyanazt a [biztonsági modellt](/azure/role-based-access-control/overview) , amely a Microsoft Azure által használt hozzáférés-kezelés. A RBAC segítségével kezelheti az előfizetésekhez, az erőforrásokhoz és a szolgáltatásokhoz való hozzáférést a felhasználók, csoportok és alkalmazások számára.

## <a name="basics-of-access-management"></a>Hozzáférés-kezelés alapjait

A szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít, amelyet a környezet biztonságossá tételére használhat. A felhasználók számára a szükséges engedélyeket adja meg egy adott hatókörhöz tartozó RBAC-szerepkör hozzárendelésével. A szerepkör-hozzárendelés hatóköre egy előfizetés, erőforráscsoport vagy egyetlen erőforrás lehet. A hozzáférés-kezeléssel kapcsolatos részletesebb információkért tekintse [meg a Azure Portal cikkben található szerepköralapú Access Control](/azure/role-based-access-control/overview) .

> [!NOTE]
> Ha Azure Stack hub a Active Directory összevonási szolgáltatások (AD FS) identitás-szolgáltató használatával lett telepítve, akkor a RBAC-forgatókönyvek esetében csak univerzális csoportok támogatottak.

### <a name="built-in-roles"></a>Beépített szerepkörök

Azure Stack hub három alapvető szerepkörrel rendelkezik, amelyeket az összes erőforrástípus alkalmazhat:

* **Tulajdonos**: mindent kezelhet, beleértve az erőforrásokhoz való hozzáférést is.
* **Közreműködő**: az erőforrásokhoz való hozzáférés kivételével mindent képes kezelni.
* **Olvasó**: mindent megtekinthet, de nem végezhet módosításokat.

### <a name="resource-hierarchy-and-inheritance"></a>Erőforrás-hierarchiát és öröklődés

Azure Stack hub a következő erőforrás-hierarchiával rendelkezik:

* Az egyes előfizetésekhez tartozik egy címtárban.
* Minden erőforráscsoport egy előfizetéshez tartozik.
* Minden erőforrás egy erőforráscsoportba tartozik.

A gyermek hatókörök örökölt, amely egy szülő hatókörben számára biztosítson hozzáférést. Például:

* Az **olvasó** szerepkört hozzárendelheti egy Azure ad-csoporthoz az előfizetés hatókörében. A csoport tagjai tekinthetik minden erőforráscsoport és az erőforrás az előfizetést.
* A **közreműködői** szerepkört hozzárendelheti egy alkalmazáshoz az erőforráscsoport hatókörében. Az alkalmazás kezelheti az adott erőforráscsoport összes típusának erőforrásait, de az előfizetésben nem található más erőforráscsoportok.

### <a name="assigning-roles"></a>Szerepkörök hozzárendelése

Egynél több szerepkörhöz hozzárendelni egy felhasználóhoz, és minden egyes szerepkör társítható egy másik hatókört. Például:

* Rendelje hozzá a tesztfelhasználó-a **olvasó** szerepkört az előfizetés-1 értékhez.
* Rendelje hozzá a tesztfelhasználó-a **tulajdonosi** szerepkört a TestVM-1 értékhez.

Az Azure [szerepkör-hozzárendelési](/azure/role-based-access-control/role-assignments-portal) cikk részletes információkat tartalmaz a szerepkörök megtekintéséről, hozzárendeléséről és törléséről.

## <a name="set-access-permissions-for-a-user"></a>Egy felhasználó hozzáférési engedélyek beállítása

A következő lépések bemutatják, hogyan engedélyek konfigurálása a felhasználó.

1. A felügyelni kívánt erőforrás tulajdonosi engedélyekkel rendelkező fiókkal jelentkezzen be.
2. A bal oldali navigációs panelen válassza az **Erőforráscsoportok** lehetőséget.
3. Válassza ki a használni kívánt engedélyeket állíthat be az erőforráscsoport nevét.
4. Az erőforráscsoport navigációs paneljén válassza a **hozzáférés-vezérlés (iam)** lehetőséget.<BR> A **szerepkör-hozzárendelések** nézet felsorolja az erőforráscsoporthoz hozzáférő elemeket. Szűrheti és az eredmények csoportosítása.
5. A **hozzáférés-vezérlés** menüsorán válassza a **Hozzáadás**lehetőséget.
6. Az **engedélyek hozzáadása** panelen:

   * Válassza ki azt a szerepkört, amelyet hozzá szeretne rendelni a **szerepkör** legördülő listából.
   * Válassza ki a hozzárendelni kívánt erőforrást a **hozzáférés kiosztása** legördülő listához.
   * Válassza ki a címtárban azt a felhasználót, csoportot vagy alkalmazást, amelyhez hozzáférést szeretne biztosítani. A címtárban rákereshet megjelenítendő nevekre, e-mail-címekre és objektumazonosítókra.

7. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

[Egyszerű szolgáltatások létrehozása](../operator/azure-stack-create-service-principals.md)
