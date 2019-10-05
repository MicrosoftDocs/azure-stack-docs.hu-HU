---
title: Lehetővé teszi, hogy a felhőalapú megoldás szolgáltatója kezelhesse Azure Stack-előfizetését | Microsoft Docs
description: Ismerje meg, hogyan kezelheti a felhőalapú megoldás-szolgáltató (CSP) a Azure Stack-előfizetését.
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
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: a11846feb852a44a5ae526e9c060ca1626bbe245
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961634"
---
# <a name="let-your-cloud-solution-provider-manage-your-azure-stack-subscription"></a>Lehetővé teszi, hogy a felhőalapú megoldás szolgáltatója kezelhesse Azure Stack-előfizetését

*Vonatkozik: Azure Stack integrált rendszerek @ no__t-0

Ha a Azure Stackt felhőalapú megoldás-szolgáltatóval (CSP) használja, akkor dönthet úgy, hogy saját előfizetését felügyeli az Azure-ban és a Azure Stack található erőforrásokhoz való hozzáféréshez. Azt is engedélyezheti, hogy a szolgáltató kezelje az előfizetését. Ez a cikk bemutatja, hogyan végezheti el a következőket:

* Adja meg a szolgáltatóhoz való hozzáférést az előfizetéséhez.
* Győződjön meg arról, hogy a szolgáltató felügyelheti a szolgáltatást.

> [!NOTE]
> Ha a CSP nem kezeli a fiókját, és kihagyja a következő lépéseket, a CSP nem tudja kezelni a Azure Stack-előfizetését.

## <a name="manage-your-subscription-with-a-csp"></a>Előfizetés kezelése CSP-vel

Adja hozzá a CSP-t **felhasználóként** az előfizetéséhez.

1. Adja hozzá a CSP-t vendég felhasználóként a bérlői címtárhoz tartozó **felhasználói** szerepkörrel. A felhasználók hozzáadásával kapcsolatos segítségért lásd: [új felhasználók hozzáadása Azure Active Directoryhoz](/azure/active-directory/add-users-azure-active-directory).

2. A CSP létrehoz egy helyi Azure Stack-előfizetést. Készen áll a Azure Stack használatának megkezdésére.

3. A CSP-nek létre kell hoznia egy erőforrást az előfizetésben annak ellenőrzéséhez, hogy képes-e az erőforrások kezelésére is. [Létrehozhatnak például egy Windows rendszerű virtuális gépet a Azure stack-portálon](azure-stack-quick-windows-portal.md).

## <a name="let-the-csp-manage-your-subscription-using-rbac-rights"></a>Lehetővé teszi, hogy a CSP RBAC jogokkal kezelhesse az előfizetést

Adja hozzá a CSP-t **tulajdonosként** az előfizetéséhez.

1. Adja hozzá a CSP-t vendég felhasználóként a bérlői címtárhoz. További információ a felhasználók hozzáadásáról: [új felhasználók hozzáadása Azure Active Directoryhoz](/azure/active-directory/add-users-azure-active-directory).

2. Adja hozzá a **tulajdonos** szerepkört a CSP vendég felhasználóhoz. A CSP-felhasználók előfizetéshez való hozzáadásával kapcsolatos információkért lásd: [szerepköralapú Access Control használata az Azure-előfizetések erőforrásaihoz való hozzáférés kezeléséhez](/azure/role-based-access-control/role-assignments-portal). A CSP létrehoz egy helyi Azure Stack-előfizetést. Készen áll a Azure Stack használatának megkezdésére.
3. A CSP-nek létre kell hoznia egy erőforrást az előfizetésben annak ellenőrzéséhez, hogy képes-e az erőforrások kezelésére.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni a Azure Stack erőforrás-használati adatok lekéréséről, tekintse meg a [használat és a számlázás Azure Stackban](../operator/azure-stack-billing-and-chargeback.md)című témakört.
