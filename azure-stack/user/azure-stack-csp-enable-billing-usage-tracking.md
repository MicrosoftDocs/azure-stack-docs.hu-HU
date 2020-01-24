---
title: Lehetővé teszi, hogy a felhőalapú megoldás szolgáltatója kezelhesse Azure Stack hub-előfizetését | Microsoft Docs
description: Ismerje meg, hogyan kezelheti a felhőalapú megoldás-szolgáltató (CSP) a Azure Stack hub-előfizetését.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: d6ad986c35a819b8cd3250914aeeefc6fd961486
ms.sourcegitcommit: ecb541f53255c6a4433724ad2d20fb93c4720ce1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76706830"
---
# <a name="let-your-cloud-solution-provider-manage-your-azure-stack-hub-subscription"></a>Lehetővé teszi, hogy a felhőalapú megoldás szolgáltatója kezelhesse Azure Stack hub-előfizetését

Ha a Azure Stack hubot felhőalapú megoldás-szolgáltatóval (CSP) használja, akkor dönthet úgy, hogy saját előfizetését felügyeli az Azure-ban és a Azure Stack hub-ban található erőforrások eléréséhez. Azt is engedélyezheti, hogy a szolgáltató kezelje az előfizetését. Ez a cikk a következőkhöz nyújt útmutatást:

* Adja meg a szolgáltatóhoz való hozzáférést az előfizetéséhez.
* Győződjön meg arról, hogy a szolgáltató felügyelheti a szolgáltatást.

> [!NOTE]
> Ha a CSP nem kezeli a fiókját, és kihagyja a következő lépéseket, a CSP nem tudja kezelni a Azure Stack hub-előfizetését.

## <a name="manage-your-subscription-with-a-csp"></a>Előfizetés kezelése CSP-vel

Adja hozzá a CSP-t **felhasználóként** az előfizetéséhez.

1. Adja hozzá a CSP-t vendég felhasználóként a bérlői címtárhoz tartozó **felhasználói** szerepkörrel. A felhasználók hozzáadásával kapcsolatos segítségért lásd: [új felhasználók hozzáadása Azure Active Directoryhoz](/azure/active-directory/add-users-azure-active-directory).

2. A CSP létrehoz egy helyi Azure Stack hub-előfizetést. Készen áll az Azure Stack hub használatának megkezdésére.

3. A CSP-nek létre kell hoznia egy erőforrást az előfizetésben annak ellenőrzéséhez, hogy képes-e az erőforrások kezelésére is. [Létrehozhatnak például egy Windows rendszerű virtuális gépet az Azure stack hub portál](azure-stack-quick-windows-portal.md)használatával.

## <a name="let-the-csp-manage-your-subscription-using-rbac-rights"></a>Lehetővé teszi, hogy a CSP RBAC jogokkal kezelhesse az előfizetést

Adja hozzá a CSP-t **tulajdonosként** az előfizetéséhez.

1. Adja hozzá a CSP-t vendég felhasználóként a bérlői címtárhoz. További információ a felhasználók hozzáadásáról: [új felhasználók hozzáadása Azure Active Directoryhoz](/azure/active-directory/add-users-azure-active-directory).

2. Adja hozzá a **tulajdonos** szerepkört a CSP vendég felhasználóhoz. A CSP-felhasználók előfizetéshez való hozzáadásával kapcsolatos információkért lásd: [szerepköralapú Access Control használata az Azure-előfizetések erőforrásaihoz való hozzáférés kezeléséhez](/azure/role-based-access-control/role-assignments-portal). A CSP létrehoz egy helyi Azure Stack hub-előfizetést. Készen áll az Azure Stack hub használatának megkezdésére.
3. A CSP-nek létre kell hoznia egy erőforrást az előfizetésben annak ellenőrzéséhez, hogy képes-e az erőforrások kezelésére.

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni a Azure Stack hub erőforrás-használati adatainak lekéréséről, tekintse meg [a használat és a számlázás Azure stack hub-ban](../operator/azure-stack-billing-and-chargeback.md)című témakört.
