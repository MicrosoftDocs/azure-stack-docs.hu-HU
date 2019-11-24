---
title: A Azure Stack használatának és számlázásának kezelése felhőalapú megoldás-szolgáltatóként | Microsoft Docs
description: Megtudhatja, hogyan regisztrálhat Azure Stack felhőalapú megoldás-szolgáltatóként (CSP), és hogyan veheti fel az ügyfeleket a számlázáshoz.
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
ms.openlocfilehash: e14c57cb5d0444c68619e6be5db688d7b1542e93
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961876"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-solution-provider"></a>A Azure Stack használatának és számlázásának kezelése felhőalapú megoldás-szolgáltatóként

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

Ez a cikk bemutatja, hogyan regisztrálhat Azure Stack felhőalapú megoldás-szolgáltatóként (CSP), és hogyan adhat hozzá ügyfeleket.

CSP-ként különböző ügyfelekkel dolgozhat a Azure Stack használatával. Minden ügyfél rendelkezik egy CSP-előfizetéssel az Azure-ban. Minden felhasználói előfizetéshez közvetlenül a Azure Stack kell használnia a használatot.

Az alábbi ábrán a megosztott szolgáltatási fiók kiválasztásához szükséges lépések láthatók, valamint az Azure-fiók regisztrálása a Azure Stack fiókkal. A regisztrációt követően a végfelhasználók a következőket vehetik igénybe:

[A használat és a ![kezelés felhőalapú megoldás-szolgáltatói folyamatként való engedélyezésének folyamata]a(media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "használat és a kezelés felhőalapú megoldás-szolgáltatóként való engedélyezéséhez")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>CSP-vagy APSS-előfizetés létrehozása

### <a name="csp-subscription-types"></a>CSP-előfizetési típusok

Válassza ki az Azure Stackhez használandó megosztott szolgáltatásfiók típusát. Az alábbi előfizetés-típusokkal lehet regisztrálni egy több-bérlős Azure Stacket:

- Cloud Solution Provider program
- Partner Shared Services-előfizetés

#### <a name="azure-partner-shared-services"></a>Azure partner megosztott szolgáltatások

Az Azure partner Shared Services (APSS) előfizetések használata ajánlott a regisztrációhoz, ha egy közvetlen CSP vagy egy CSP-terjesztő Azure Stack működik.

A APSS-előfizetések egy megosztott szolgáltatásbeli bérlőhöz vannak társítva. Azure Stack regisztrálásakor meg kell adnia egy olyan fiók hitelesítő adatait, amely az előfizetés tulajdonosa. A Azure Stack regisztrálásához használt fiók eltérhet az üzembe helyezéshez használt rendszergazdai fióktól. Továbbá a két fióknak nem kell ugyanahhoz a tartományhoz tartoznia; az üzembe helyezést a már használt bérlő használatával végezheti el. Használhatja például `ContosoCSP.onmicrosoft.com`, majd regisztrálhat egy másik bérlő használatával; például `IURContosoCSP.onmicrosoft.com`. A napi Azure Stack felügyelet végrehajtásakor ne felejtse el bejelentkezni `ContosoCSP.onmicrosoft.com` használatával. Ha regisztrációs műveletet kell végrehajtania, akkor `IURContosoCSP.onmicrosoft.com` használatával bejelentkezhet az Azure-ba.

A APSS-előfizetések és azok létrehozási módjának leírását az [Azure partner megosztott szolgáltatásainak hozzáadása](/partner-center/shared-services)című témakörben tekintheti meg.

#### <a name="csp-subscriptions"></a>CSP-előfizetések

A CSP-előfizetések előnyben részesítettek a regisztrációhoz, ha a CSP-viszonteladó vagy a végfelhasználó Azure Stack működik.

## <a name="register-azure-stack"></a>Az Azure Stack regisztrálása

Használja az előző szakasz információi alapján létrehozott APSS-előfizetést az Azure-beli Azure Stack regisztrálásához. További információ: [Azure stack regisztrálása az Azure-előfizetéssel](azure-stack-registration.md).

## <a name="add-end-customer"></a>Végfelhasználó hozzáadása

Ha úgy szeretné konfigurálni a Azure Stackt, hogy az új bérlő erőforrás-felhasználását jelentse a CSP-előfizetésének, tekintse meg a [bérlő hozzáadása a használathoz és a számlázáshoz Azure stack](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>A megfelelő előfizetések díja

Azure Stack a *regisztráció*nevű szolgáltatást használja. A regisztráció egy, az Azure-ban tárolt objektum. A regisztrációs objektum azon dokumentumai, amelyekben az Azure-előfizetések az adott Azure Stackért felszámított díjat használják. Ez a szakasz a regisztráció fontosságát tárgyalja.

A regisztráció használatával a Azure Stack a következőket teheti:

- Azure Stack használati adatok továbbítása az Azure Commerce szolgáltatásba és egy Azure-előfizetés számlázása.
- A több-bérlős Azure Stack-telepítéssel egy másik előfizetésen keresztül jelentést készít az egyes ügyfelek használatáról. A több-bérlős szolgáltatás lehetővé teszi, hogy Azure Stack különböző szervezeteket támogassanak ugyanazon a Azure Stack-példányon.

Minden Azure Stack esetében van egy alapértelmezett előfizetés és számos bérlői előfizetés. Az alapértelmezett előfizetés egy olyan Azure-előfizetés, amelyért díjat számítunk fel, ha nincs bérlőre vonatkozó előfizetés. Regisztrálni kell az első regisztrációt. A több-bérlős használat jelentésének működéséhez az előfizetés csak CSP vagy APSS előfizetés lehet.

Ezt követően a regisztráció minden Azure Stackt használó bérlőhöz egy Azure-előfizetéssel frissül. A bérlői előfizetésnek CSP típusúnak kell lennie, és az alapértelmezett előfizetést birtokló partnernek kell lennie. Mások ügyfelei nem regisztrálhatók.

Amikor Azure Stack továbbítja a használati adatokat a globális Azure-ba, az Azure szolgáltatás a regisztrációt és a bérlők használati feltételeit a megfelelő bérlői előfizetéshez rendeli. Ha a bérlő nincs regisztrálva, a használat az alapértelmezett előfizetésre kerül a Azure Stack példánytól, amelyből származik.

Mivel a bérlői előfizetések CSP-előfizetések, a számlát a CSP-partnernek küldik, és a használati adatok nem láthatók a végfelhasználók számára.

## <a name="next-steps"></a>További lépések

- További információ a CSP programról: [Cloud Solution Provider program](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Ha többet szeretne megtudni a Azure Stack erőforrás-használati adatok lekéréséről, tekintse meg a [használat és a számlázás Azure Stackban](azure-stack-billing-and-chargeback.md)című témakört.
