---
title: A Azure Stack erőforrásaihoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel | Microsoft Docs
description: Ismerje meg, hogyan kezelheti a szerepköralapú hozzáférés-vezérlés (RBAC) engedélyeit rendszergazdaként vagy bérlőként a Azure Stack-ben.
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
ms.openlocfilehash: a784da0d16f6ec92a105d9360430f4e8da2817ef
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974925"
---
# <a name="manage-access-to-resources-in-azure-stack-with-role-based-access-control"></a>A Azure Stack erőforrásaihoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Az Azure Stack is támogatja a szerepköralapú hozzáférés-vezérlést (RBAC), ugyanez [kezelési biztonsági modell](/azure/role-based-access-control/overview) , amely a Microsoft Azure. A RBAC segítségével kezelheti az előfizetésekhez, az erőforrásokhoz és a szolgáltatásokhoz való hozzáférést a felhasználók, csoportok és alkalmazások számára.

## <a name="basics-of-access-management"></a>Hozzáférés-kezelés alapjait

A szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít, amelyet a környezet biztonságossá tételére használhat. A felhasználók számára a szükséges engedélyeket adja meg egy adott hatókörhöz tartozó RBAC-szerepkör hozzárendelésével. A szerepkör-hozzárendelés hatóköre egy előfizetés, erőforráscsoport vagy egyetlen erőforrás lehet. A hozzáférés-kezeléssel kapcsolatos részletesebb információkért tekintse [meg a Azure Portal cikkben található szerepköralapú Access Control](/azure/role-based-access-control/overview) .

> [!NOTE]
> Ha a Azure Stack Active Directory összevonási szolgáltatások (AD FS) identitás-szolgáltatóként van telepítve, a RBAC-forgatókönyvek esetében csak univerzális csoportok támogatottak.

### <a name="built-in-roles"></a>Beépített szerepkörök

Az Azure Stack rendelkezik, amelyek minden erőforrástípus alkalmazhat három alapvető szerepkörök:

* **Tulajdonos**: mindent kezelhet, beleértve az erőforrásokhoz való hozzáférést is.
* **Közreműködő**: az erőforrásokhoz való hozzáférés kivételével mindent képes kezelni.
* **Olvasó**: mindent megtekinthet, de nem végezhet módosításokat.

### <a name="resource-hierarchy-and-inheritance"></a>Erőforrás-hierarchiát és öröklődés

Az Azure Stack a következő erőforrás-hierarchia rendelkezik:

* Az egyes előfizetésekhez tartozik egy címtárban.
* Minden erőforráscsoport egy előfizetéshez tartozik.
* Minden erőforrás egy erőforráscsoportba tartozik.

A gyermek hatókörök örökölt, amely egy szülő hatókörben számára biztosítson hozzáférést. Példa:

* Hozzárendelhet a **olvasó** szerepkört az Azure AD-csoportok az előfizetések szintjén. A csoport tagjai tekinthetik minden erőforráscsoport és az erőforrás az előfizetést.
* A **közreműködői** szerepkört hozzárendelheti egy alkalmazáshoz az erőforráscsoport hatókörében. Az alkalmazás kezelheti az adott erőforráscsoport összes típusának erőforrásait, de az előfizetésben nem található más erőforráscsoportok.

### <a name="assigning-roles"></a>Szerepkörök hozzárendelése

Egynél több szerepkörhöz hozzárendelni egy felhasználóhoz, és minden egyes szerepkör társítható egy másik hatókört. Példa:

* Rendelje hozzá a tesztfelhasználó-a **olvasó** szerepkört az előfizetés-1 értékhez.
* Rendelje hozzá a tesztfelhasználó- a tulajdonosi szerepkört a TestVM-1 értékhez.

Az Azure [szerepkör-hozzárendelések](/azure/role-based-access-control/role-assignments-portal) cikk megtekintése, hozzárendelése és törlése a szerepkörök részletes információkat tartalmaz.

## <a name="set-access-permissions-for-a-user"></a>Egy felhasználó hozzáférési engedélyek beállítása

A következő lépések bemutatják, hogyan engedélyek konfigurálása a felhasználó.

1. A felügyelni kívánt erőforrás tulajdonosi engedélyekkel rendelkező fiókkal jelentkezzen be.
2. A bal oldali navigációs panelen válassza az **Erőforráscsoportok** lehetőséget.
3. Válassza ki a használni kívánt engedélyeket állíthat be az erőforráscsoport nevét.
4. Az erőforrás csoport navigációs ablaktábláján válassza **hozzáférés-vezérlés (IAM)** .<BR> A **szerepkör-hozzárendelések** megtekintése az erőforráscsoport számára elérhető elemeket sorolja fel. Szűrheti és az eredmények csoportosítása.
5. A **hozzáférés-vezérlés** menüsorán válassza a **Hozzáadás**lehetőséget.
6. Az **engedélyek hozzáadása** panelen:

   * Válassza ki a hozzárendelni kívánt szerepkört az **szerepkör** legördülő listából.
   * Válassza ki a hozzárendelni kívánt erőforrást a **rendelhet hozzáféréseket** legördülő listából.
   * Válassza ki a címtárban azt a felhasználót, csoportot vagy alkalmazást, amelyhez hozzáférést szeretne biztosítani. A címtárban rákereshet megjelenítendő nevekre, e-mail-címekre és objektumazonosítókra.

7. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

[Egyszerű szolgáltatások létrehozása](../operator/azure-stack-create-service-principals.md)
