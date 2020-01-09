---
title: Hozzáférési engedélyek beállítása szerepköralapú hozzáférés-vezérlés használatával | Microsoft Docs
description: Megtudhatja, hogyan állíthat be hozzáférési engedélyeket szerepköralapú hozzáférés-vezérléssel (RBAC) Azure Stackban.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/23/2019
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: ae5b167ca807fc1e28c13c553e13ea89c7e6fe49
ms.sourcegitcommit: dc3d0b77ee77742525fa0cd72d8547d25393022f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/26/2019
ms.locfileid: "75492780"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>Hozzáférési engedélyek beállítása szerepköralapú hozzáférés-vezérlés használatával

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Az Azure Stack-felhasználók az előfizetés, erőforráscsoport vagy szolgáltatás minden egyes példányánál rendelkezhetnek olvasó, tulajdonos vagy közreműködő szerepkörrel. Előfordulhat például, hogy az A felhasználó rendelkezik olvasói engedéllyel az előfizetéshez, de a tulajdonosi engedélyekkel rendelkezik a hét virtuális géphez.

 - Olvasó: a felhasználó mindent megtekinthet, de nem végezhet módosításokat.
 - Közreműködő: a felhasználó mindent kezelhet, kivéve az erőforrásokhoz való hozzáférést.
 - Tulajdonos: a felhasználó mindent kezelhet, beleértve az erőforrásokhoz való hozzáférést is.
 - Egyéni: a felhasználó korlátozott, konkrét erőforrásokhoz férhet hozzá.

 Az egyéni szerepkörök létrehozásával kapcsolatos további információkért tekintse meg [Az Azure-erőforrások egyéni szerepkörei](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)című témakört.

## <a name="set-access-permissions-for-a-user"></a>Felhasználó hozzáférési engedélyeinek beállítása

1. Jelentkezzen be egy olyan fiókkal, amely tulajdonosi engedélyekkel rendelkezik a kezelni kívánt erőforráshoz.
2. Az erőforrás panelen kattintson a **hozzáférés** ikonra ![](media/azure-stack-manage-permissions/image1.png).
3. A **felhasználók** panelen kattintson a **szerepkörök**elemre.
4. A **szerepkörök** panelen kattintson a **Hozzáadás** gombra a felhasználó engedélyeinek hozzáadásához.

## <a name="set-access-permissions-for-a-universal-group"></a>Egy univerzális csoport hozzáférési engedélyeinek beállítása 

> [!Note]
> Csak Active Directory összevont szolgáltatások (AD FS) esetében alkalmazható.

1. Jelentkezzen be egy olyan fiókkal, amely tulajdonosi engedélyekkel rendelkezik a kezelni kívánt erőforráshoz.
2. Az erőforrás panelen kattintson a **hozzáférés** ikonra ![](media/azure-stack-manage-permissions/image1.png).
3. A **felhasználók** panelen kattintson a **szerepkörök**elemre.
4. A **szerepkörök** panelen kattintson a **Hozzáadás** gombra az univerzális csoport Active Directory csoport engedélyeinek hozzáadásához.

## <a name="next-steps"></a>Következő lépések

[Azure Stack-bérlő hozzáadása](azure-stack-add-new-user-aad.md)