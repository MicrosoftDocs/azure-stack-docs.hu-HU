---
title: Azure Stack hub használatának és számlázásának kezelése felhőalapú megoldás-szolgáltatóként | Microsoft Docs
description: Megtudhatja, hogyan regisztrálhat Azure Stack hub-t felhőalapú megoldás-szolgáltatóként (CSP), és hogyan veheti fel az ügyfeleket a számlázáshoz.
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
ms.date: 10/02/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 8b97636d8d0dfce09b17c54e52b0fcda1990151e
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75812127"
---
# <a name="manage-usage-and-billing-for-azure-stack-hub-as-a-cloud-solution-provider"></a>Azure Stack hub használatának és számlázásának kezelése felhőalapú megoldás-szolgáltatóként

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek*

Ez a cikk azt ismerteti, hogyan regisztrálhatók a Azure Stack hub felhőalapú megoldás-szolgáltatóként (CSP), és hogyan adhatók hozzá ügyfelek.

CSP-ként a Azure Stack hub használatával különböző ügyfelekkel dolgozhat. Minden ügyfél rendelkezik egy CSP-előfizetéssel az Azure-ban. Minden felhasználói előfizetéshez közvetlenül a Azure Stack-hubhoz kell használnia a használatot.

Az alábbi ábrán a megosztott szolgáltatások kiválasztásához szükséges lépések láthatók, valamint az Azure-fiók regisztrálása a Azure Stack hub-fiókkal. A regisztrációt követően a végfelhasználók a következőket vehetik igénybe:

[![A használat és a kezelés felhőalapú megoldás-szolgáltatóként való engedélyezésének folyamata](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "A használat és a kezelés felhőalapú megoldás-szolgáltatóként való engedélyezésének folyamata")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>CSP-vagy APSS-előfizetés létrehozása

### <a name="csp-subscription-types"></a>CSP-előfizetési típusok

Válassza ki a Azure Stack hub-hoz használt megosztott szolgáltatási fiók típusát. A több-bérlős Azure Stack központ regisztrálásához használható előfizetések típusai a következők:

- Cloud Solution Provider program
- Partner Shared Services-előfizetés

#### <a name="azure-partner-shared-services"></a>Azure partner megosztott szolgáltatások

Az Azure partner Shared Services (APSS) előfizetések előnyben részesítettek a regisztrációhoz, ha egy közvetlen CSP vagy egy CSP-terjesztő Azure Stack hub-t üzemeltet.

A APSS-előfizetések egy megosztott szolgáltatásbeli bérlőhöz vannak társítva. Azure Stack hub regisztrálása esetén meg kell adnia egy olyan fiók hitelesítő adatait, amely az előfizetés tulajdonosa. Az Azure Stack hub regisztrálásához használt fiók eltérhet az üzembe helyezéshez használt rendszergazdai fióktól. Továbbá a két fióknak nem kell ugyanahhoz a tartományhoz tartoznia; az üzembe helyezést a már használt bérlő használatával végezheti el. Használhatja például `ContosoCSP.onmicrosoft.com`, majd regisztrálhat egy másik bérlő használatával; például `IURContosoCSP.onmicrosoft.com`. Ha napi Azure Stack hub-felügyeletet hajt végre, ne felejtse el bejelentkezni `ContosoCSP.onmicrosoft.com` használatával. Ha regisztrációs műveletet kell végrehajtania, akkor `IURContosoCSP.onmicrosoft.com` használatával bejelentkezhet az Azure-ba.

A APSS-előfizetések és azok létrehozási módjának leírását az [Azure partner megosztott szolgáltatásainak hozzáadása](/partner-center/shared-services)című témakörben tekintheti meg.

#### <a name="csp-subscriptions"></a>CSP-előfizetések

A CSP-előfizetések előnyben részesítettek a regisztrációhoz, ha egy CSP-viszonteladó vagy egy végfelhasználó Azure Stack hub-t üzemeltet.

## <a name="register-azure-stack-hub"></a>Azure Stack hub regisztrálása

Használja az előző szakaszban található információk használatával létrehozott APSS-előfizetést az Azure-beli Azure Stack hub regisztrálásához. További információ: [Azure stack hub regisztrálása az Azure-előfizetéssel](azure-stack-registration.md).

## <a name="add-end-customer"></a>Végfelhasználó hozzáadása

Ha úgy szeretné konfigurálni Azure Stack hub-t, hogy az új bérlő erőforrás-felhasználását a CSP-előfizetésére jelentse, tekintse meg a [bérlő hozzáadása használathoz és számlázáshoz Azure stack hubhoz](azure-stack-csp-howto-register-tenants.md)című témakört.

## <a name="charge-the-right-subscriptions"></a>A megfelelő előfizetések díja

Azure Stack hub a *regisztráció*nevű szolgáltatást használja. A regisztráció egy, az Azure-ban tárolt objektum. A regisztrációs objektum azon dokumentumai, amelyekben az Azure-előfizetések az adott Azure Stack hub díjszabásához használhatók. Ez a szakasz a regisztráció fontosságát tárgyalja.

A regisztráció használatával a Azure Stack hub a következőket teheti:

- Továbbítsa Azure Stack hub használati adatait az Azure Commerce szolgáltatásba, és számlázjon egy Azure-előfizetést.
- A több-bérlős Azure Stack hub-alapú telepítéssel egy másik előfizetésen keresztül jelentést készít az egyes ügyfelek használatáról. A több-bérlő lehetővé teszi, hogy Azure Stack hub különböző szervezeteket támogassanak ugyanazon a Azure Stack hub-példányon.

Minden Azure Stack hub esetében van egy alapértelmezett előfizetés és számos bérlői előfizetés. Az alapértelmezett előfizetés egy olyan Azure-előfizetés, amelyért díjat számítunk fel, ha nincs bérlőre vonatkozó előfizetés. Regisztrálni kell az első regisztrációt. A több-bérlős használat jelentésének működéséhez az előfizetés csak CSP vagy APSS előfizetés lehet.

Ezt követően a regisztráció minden Azure Stack hub-t használó bérlőhöz Azure-előfizetéssel frissül. A bérlői előfizetésnek CSP típusúnak kell lennie, és az alapértelmezett előfizetést birtokló partnernek kell lennie. Mások ügyfelei nem regisztrálhatók.

Amikor Azure Stack hub a használati adatokat a globális Azure-ba továbbítja, az Azure szolgáltatás a regisztrációt és a bérlők használati feltételeit a megfelelő bérlői előfizetéshez rendeli. Ha egy bérlő nincs regisztrálva, a használat az Azure Stack hub azon példányához tartozó alapértelmezett előfizetésre kerül, ahonnan származik.

Mivel a bérlői előfizetések CSP-előfizetések, a számlát a CSP-partnernek küldik, és a használati adatok nem láthatók a végfelhasználók számára.

## <a name="next-steps"></a>Következő lépések

- További információ a CSP programról: [Cloud Solution Provider program](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Ha többet szeretne megtudni a Azure Stack hub erőforrás-használati adatainak lekéréséről, tekintse meg a [használat és a számlázás Azure stack hub-ban](azure-stack-billing-and-chargeback.md)című témakört.
