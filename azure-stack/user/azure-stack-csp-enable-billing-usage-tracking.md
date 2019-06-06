---
title: Lehetővé teszik a Felhőszolgáltató az Azure Stack-előfizetés kezelése |} A Microsoft Docs
description: Ismerje meg, hogy a Felhőszolgáltató (CSP) kezelése az Azure Stack-előfizetést, hogyan.
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
ms.date: 05/20/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: 0dc162dd1d4021323f1bf80ad4f89fc721e575a9
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691975"
---
# <a name="let-your-cloud-service-provider-manage-your-azure-stack-subscription"></a>Lehetővé teszik a Felhőszolgáltató az Azure Stack-előfizetés kezelése

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ha az Azure Stack az a Felhőbeli szolgáltató (CSP) használ, választhatja erőforrások eléréséhez az Azure-ban, és az Azure Stack saját előfizetése kezelésére. A szolgáltató, az előfizetés kezelése is engedélyezheti. Ez a cikk bemutatja, hogyan való:

* A service provider hozzáférést biztosít az előfizetéshez.
* Ellenőrizze, hogy a szolgáltató kezelheti a szolgáltatást.

> [!NOTE]
> A kriptográfiai Szolgáltató nem a fiók kezelésében, és a következő lépés kihagyható, ha a kriptográfiai Szolgáltató az Azure Stack-előfizetést, nem tudja kezelni.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>A Felhőszolgáltató az előfizetés kezelése

Adja hozzá a CSP, **felhasználói** az előfizetéshez.

1. A CSP a vendégfelhasználó hozzáadása a **felhasználói** szerepkört a bérlő címtárát. A felhasználó hozzáadása kapcsolatos útmutatásért lásd: [új felhasználók hozzáadása az Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. A CSP hoz létre az Ön számára a helyi Azure Stack-előfizetéssel. Készen áll az Azure Stack használatának megkezdéséhez.
3. A CSP hozzon létre egy erőforrást az előfizetésében, győződjön meg arról, hogy is kezelhetik az erőforrásokat. Ha például a következőkre [Windows virtuális gép létrehozása az Azure Stack portal](azure-stack-quick-windows-portal.md).

## <a name="let-the-cloud-service-provider-manage-your-subscription-using-rbac-rights"></a>Lehetővé teszik a Felhőszolgáltató az előfizetésben az RBAC-jogosultságok kezelése

Adja hozzá a CSP, **tulajdonos** az előfizetéshez.

1. A kriptográfiai Szolgáltató, a vendégfelhasználó hozzáadása a bérlő címtárát. A felhasználó hozzáadása kapcsolatos útmutatásért lásd: [új felhasználók hozzáadása az Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Adja hozzá a **tulajdonos** a CSP Vendég felhasználói szerepkört. Segítség a CSP-felhasználók hozzáadása az előfizetéséhez, tekintse meg a [Use Role-Based hozzáférés-vezérlés az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése](/azure/role-based-access-control/role-assignments-portal). A CSP hoz létre az Ön számára a helyi Azure Stack-előfizetéssel. Készen áll az Azure Stack használatának megkezdéséhez.
3. A CSP hozzon létre egy erőforrást az előfizetésében, győződjön meg arról, hogy képesek kezelni az erőforrásokat.

## <a name="next-steps"></a>További lépések

* Erőforrás-használati adatok lekérése az Azure Stack kapcsolatos további tudnivalókért lásd: [használat és számlázás az Azure Stackben](../operator/azure-stack-billing-and-chargeback.md).
