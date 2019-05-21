---
title: Az Azure Stack-előfizetés kezelése céljából Felhőszolgáltató engedélyezése |} A Microsoft Docs
description: Engedélyezze a service provider, az Azure Stack-előfizetés eléréséhez.
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
ms.openlocfilehash: 209a0da31d4bd5b519771f68f4bddcbb833284fa
ms.sourcegitcommit: d2012e765c3fa5bccb4756d190349e890f9f48bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65941212"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>Az Azure Stack-előfizetés kezelése céljából Felhőszolgáltató engedélyezése

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ha az Azure Stack az a Felhőbeli szolgáltató (CSP) használ, választhatja erőforrások eléréséhez az Azure-ban, és az Azure Stack saját előfizetése kezelésére. A szolgáltató, az előfizetés kezelése is engedélyezheti. Ez a cikk bemutatja, hogyan való:

* A service provider hozzáférést biztosít az előfizetéshez.
* Ellenőrizze, hogy a szolgáltató kezelheti a szolgáltatást.

> [!NOTE]
> A CSP a fiókot nem kezelő, és a következő lépés kihagyható, ha a kriptográfiai Szolgáltató az Azure Stack-előfizetést, nem tudja kezelni.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>A Felhőszolgáltató az előfizetés kezelése

Adja hozzá a CSP, **felhasználói** az előfizetéshez.

1. A CSP a vendégfelhasználó hozzáadása a **felhasználói** szerepkört a bérlő címtárát. Felhasználó hozzáadása lépéseiért lásd: [új felhasználók hozzáadása az Azure Active Directory](/azure/active-directory/add-users-azure-active-directory)
2. A CSP hoz létre az Ön számára a helyi Azure Stack-előfizetéssel. Készen áll az Azure Stack használatának megkezdéséhez.
3. A CSP hozzon létre egy erőforrást az előfizetésében, győződjön meg arról, hogy is kezelhetik az erőforrásokat. Ha például a következőkre [Windows virtuális gép létrehozása az Azure Stack portal](azure-stack-quick-windows-portal.md).

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>A Felhőszolgáltató kezeléséhez az előfizetésben az RBAC-jogosultságok engedélyezése

Adja hozzá a CSP, **tulajdonos** az előfizetéshez.

1. A kriptográfiai Szolgáltató, a vendégfelhasználó hozzáadása a bérlő címtárát. Felhasználó hozzáadása lépéseiért lásd: [új felhasználók hozzáadása az Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).
2. Adja hozzá a **tulajdonos** a CSP Vendég felhasználói szerepkört. A CSP-felhasználó hozzáadása az előfizetéséhez lépéseiért lásd: [Use Role-Based hozzáférés-vezérlés az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése](/azure/role-based-access-control/role-assignments-portal). A CSP hoz létre az Ön számára a helyi Azure Stack-előfizetéssel. Készen áll az Azure Stack használatának megkezdéséhez.
3. A CSP hozzon létre egy erőforrást az előfizetésében, győződjön meg arról, hogy képesek kezelni az erőforrásokat.

## <a name="next-steps"></a>További lépések

* Erőforrás-használati adatok lekérése az Azure Stack kapcsolatos további tudnivalókért lásd: [használat és számlázás az Azure Stackben](../operator/azure-stack-billing-and-chargeback.md).
