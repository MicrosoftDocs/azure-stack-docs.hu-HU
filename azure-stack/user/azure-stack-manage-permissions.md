---
title: A Azure Stack hub erőforrásaihoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel | Microsoft Docs
description: Megtudhatja, hogyan kezelheti a szerepköralapú hozzáférés-vezérlés (RBAC) engedélyeit rendszergazdaként vagy bérlőként Azure Stack központban.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: e242900d3604369306717a3d4cc2c5e3aae7932c
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75879581"
---
# <a name="manage-access-to-resources-in-azure-stack-hub-with-role-based-access-control"></a>A Azure Stack hub erőforrásaihoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel

Azure Stack hub támogatja a szerepköralapú hozzáférés-vezérlést (RBAC), ugyanazt a [biztonsági modellt](/azure/role-based-access-control/overview) , amely a Microsoft Azure által használt hozzáférés-kezelés. A RBAC segítségével kezelheti az előfizetésekhez, az erőforrásokhoz és a szolgáltatásokhoz való hozzáférést a felhasználók, csoportok és alkalmazások számára.

## <a name="basics-of-access-management"></a>A hozzáférés-kezelés alapjai

A szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít, amelyet a környezet biztonságossá tételére használhat. A felhasználók számára a szükséges engedélyeket adja meg egy adott hatókörhöz tartozó RBAC-szerepkör hozzárendelésével. A szerepkör-hozzárendelés hatóköre lehet előfizetés, erőforráscsoport vagy egyetlen erőforrás is. A hozzáférés-kezeléssel kapcsolatos részletesebb információkért tekintse [meg a Azure Portal cikkben található szerepköralapú Access Control](/azure/role-based-access-control/overview) .

> [!NOTE]
> Ha Azure Stack hub a Active Directory összevonási szolgáltatások (AD FS) identitás-szolgáltató használatával lett telepítve, akkor a RBAC-forgatókönyvek esetében csak univerzális csoportok támogatottak.

### <a name="built-in-roles"></a>Beépített szerepkörök

Azure Stack hub három alapvető szerepkörrel rendelkezik, amelyeket az összes erőforrástípus alkalmazhat:

* **Tulajdonos**: mindent kezelhet, beleértve az erőforrásokhoz való hozzáférést is.
* **Közreműködő**: az erőforrásokhoz való hozzáférés kivételével mindent képes kezelni.
* **Olvasó**: mindent megtekinthet, de nem végezhet módosításokat.

### <a name="resource-hierarchy-and-inheritance"></a>Erőforrás-hierarchia és öröklés

Azure Stack hub a következő erőforrás-hierarchiával rendelkezik:

* Minden előfizetés egy címtárhoz tartozik.
* Minden erőforráscsoport egy előfizetéshez tartozik.
* Mindegyik erőforrás egy erőforráscsoporthoz tartozik.

A szülő hatókörben megadott hozzáférési jogosultságokat a rendszer az alárendelt hatóköröknél örökli. Példa:

* Az **olvasó** szerepkört hozzárendelheti egy Azure ad-csoporthoz az előfizetés hatókörében. A csoport tagjai megtekinthetik az előfizetésben lévő összes erőforráscsoportot és erőforrást.
* A **közreműködői** szerepkört hozzárendelheti egy alkalmazáshoz az erőforráscsoport hatókörében. Az alkalmazás kezelheti az adott erőforráscsoport összes típusának erőforrásait, de az előfizetésben nem található más erőforráscsoportok.

### <a name="assigning-roles"></a>Szerepkörök kiosztása

Több szerepkört is hozzárendelhet egy felhasználóhoz, és minden szerepkör társítható egy másik hatókörhöz. Példa:

* Rendelje hozzá a tesztfelhasználó-a **olvasó** szerepkört az előfizetés-1 értékhez.
* Rendelje hozzá a tesztfelhasználó-a **tulajdonosi** szerepkört a TestVM-1 értékhez.

Az Azure [szerepkör-hozzárendelési](/azure/role-based-access-control/role-assignments-portal) cikk részletes információkat tartalmaz a szerepkörök megtekintéséről, hozzárendeléséről és törléséről.

## <a name="set-access-permissions-for-a-user"></a>Felhasználó hozzáférési engedélyeinek beállítása

A következő lépések azt ismertetik, hogyan lehet engedélyeket beállítani egy felhasználóhoz.

1. Jelentkezzen be egy olyan fiókkal, amely tulajdonosi engedélyekkel rendelkezik a kezelni kívánt erőforráshoz.
2. A bal oldali navigációs panelen válassza az **Erőforráscsoportok** lehetőséget.
3. Válassza ki annak az erőforráscsoportnak a nevét, amelyhez engedélyeket kíván beállítani.
4. Az erőforráscsoport navigációs paneljén válassza a **hozzáférés-vezérlés (iam)** lehetőséget.<BR> A **szerepkör-hozzárendelések** nézet felsorolja az erőforráscsoporthoz hozzáférő elemeket. Az eredményeket szűrheti és csoportosíthatja.
5. A **hozzáférés-vezérlés** menüsorán válassza a **Hozzáadás**lehetőséget.
6. Az **engedélyek hozzáadása** panelen:

   * Válassza ki azt a szerepkört, amelyet hozzá szeretne rendelni a **szerepkör** legördülő listából.
   * Válassza ki a hozzárendelni kívánt erőforrást a **hozzáférés kiosztása** legördülő listához.
   * Válassza ki a címtárban azt a felhasználót, csoportot vagy alkalmazást, amelyhez hozzáférést szeretne biztosítani. A címtárban rákereshet megjelenítendő nevekre, e-mail-címekre és objektumazonosítókra.

7. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

[Egyszerű szolgáltatások létrehozása](../operator/azure-stack-create-service-principals.md)
