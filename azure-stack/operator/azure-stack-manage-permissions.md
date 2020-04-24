---
title: Hozzáférési engedélyek beállítása szerepköralapú hozzáférés-vezérlés használatával
description: Megtudhatja, hogyan állíthat be hozzáférési engedélyeket szerepköralapú hozzáférés-vezérléssel (RBAC) az Azure Stack hub-ban.
author: justinha
ms.topic: article
ms.date: 12/23/2019
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: f74f35a8b1124ed9678e6b4bc254d4d2cd05a84e
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77699269"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>Hozzáférési engedélyek beállítása szerepköralapú hozzáférés-vezérlés használatával

Azure Stack hub egyik felhasználója lehet olvasó, tulajdonos vagy közreműködő az előfizetés, az erőforráscsoport vagy a szolgáltatás minden példánya számára. Előfordulhat például, hogy az A felhasználó rendelkezik olvasói engedéllyel az előfizetéshez, de a tulajdonosi engedélyekkel rendelkezik a hét virtuális géphez.

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

## <a name="next-steps"></a>További lépések

[Azure Stack hub-bérlő hozzáadása](azure-stack-add-new-user-aad.md)