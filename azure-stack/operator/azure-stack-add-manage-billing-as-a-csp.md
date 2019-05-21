---
title: Használat és számlázás az Azure Stackhez, egy Felhőszolgáltató kezelése |} A Microsoft Docs
description: Regisztrálás az Azure Stack, egy Felhőbeli Felhőszolgáltató (CSP) és a számlázási ügyfelek hozzáadása példakóddal.
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
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 5f11e9dd4f029228e73e5933cd8362c0b4710178
ms.sourcegitcommit: d2012e765c3fa5bccb4756d190349e890f9f48bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65941159"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Használat és számlázás az Azure Stack felhő szolgáltató kezelése

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk bemutatja, hogyan regisztrálhat az Azure Stack, egy Felhőszolgáltató (CSP), és hogyan adhat hozzá ügyfelek.

Mint egy CSP-hez használható különféle ügyfelek, az Azure Stack használatával. Minden ügyfél az Azure-ban rendelkezik a CSP-előfizetésekben. Az Azure Stack minden felhasználói előfizetésben kell közvetlen használat.

Az alábbi ábra bemutatja a szükséges lépések végrehajtásával válassza ki a megosztott szolgáltatások fiókját, és az Azure-fiók regisztrálása az Azure Stack-fiókkal. Miután regisztrált, segítségével készítheti elő a végfelhasználók számára:

[![Engedélyezheti a használati és felügyelete megegyezik a Felhőszolgáltató](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "engedélyezheti a használati és felügyelete megegyezik a Felhőszolgáltató")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>CSP vagy APSS előfizetés létrehozása

### <a name="cloud-service-provider-subscription-types"></a>Szolgáltatói előfizetés felhőtípusok

Válassza ki az Azure Stackhez készült használt fióknak a megosztott szolgáltatások. Használható a regisztrációhoz egy több-bérlős Azure Stack-előfizetést a típusok a következők:

- Felhőszolgáltató
- Megosztott szolgáltatások típusú partneri előfizetéshez

#### <a name="azure-partner-shared-services"></a>Azure-partneri közös szolgáltatások

Az Azure-partneri közös szolgáltatásokat (APSS) előfizetések a regisztráció során egy közvetlen CSP előnyben részesített választás vagy CSP terjesztő működik az Azure Stack.

Megosztott szolgáltatások bérlő APSS előfizetések tartoznak. Ha regisztrálja az Azure Stack, adja meg hitelesítő adatait, amely az előfizetés tulajdonosa. A fiókja, regisztrálhat az Azure Stack eltérhetnek az üzembe helyezéshez használt rendszergazdai fiók. Továbbá a két fiók nem kell ugyanabban a tartományban; tartozik telepíthet, amelyek már használják a bérlőnek a használatával. Használhatja például `ContosoCSP.onmicrosoft.com`, majd regisztrálja a használatával egy másik bérlőben; például `IURContosoCSP.onmicrosoft.com`. Jelentkezzen be a vágólapra kell `ContosoCSP.onmicrosoft.com` napi Azure Stack felügyeleti végrehajtásakor. Azure-bA bejelentkezik `IURContosoCSP.onmicrosoft.com` számíthat regisztrációs műveletek végrehajtásához.

APSS előfizetések, és hogyan hozhat létre, azok leírását lásd: [hozzáadása az Azure partneri megosztott szolgáltatások](/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>CSP-előfizetésekben

Cloud Service szolgáltatói előfizetések a regisztráció során a CSP-viszonteladó előnyben részesített választás vagy egy végfelhasználói működik az Azure Stack.

## <a name="register-azure-stack"></a>Az Azure Stack regisztrálása

A létrehozott, az előző szakaszban szereplő információk segítségével regisztrálja az Azure Stack az Azure-ral APSS előfizetést használ. További információkért lásd: [regisztrálása az Azure Stack az Azure-előfizetésében](azure-stack-registration.md).

## <a name="add-end-customer"></a>Adja hozzá a végfelhasználó ügyfél

Azure Stack beállítása úgy, hogy amikor egy új bérlőt erőforrásokat használ, használatuk a CSP-előfizetésének jelentett: [hozzáadása bérlői használat és számlázás az Azure Stackhez](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>A megfelelő előfizetések díja szerint számítjuk fel

Az Azure Stack nevű szolgáltatást használja a *regisztrációs*. A regisztráció az Azure-ban tárolt objektum. A regisztrációs objektum dokumentumok mely Azure-előfizetés(ek) díja szerint számítjuk fel az egy adott Azure Stack használatával. Ez a szakasz azon regisztrációs fontosságát.

Regisztráció, az Azure Stack használatával:

- Azure Stack-használati adatok továbbítása az Azure kereskedelmi és számlázás az Azure-előfizetéssel.
- Minden ügyfél használati jelentést egy másik előfizetésben egy több-bérlős Azure Stack üzembe helyezéssel. Több-bérlős lehetővé teszi, hogy az Azure Stack használatával támogatja a különböző szervezetek ugyanazon az Azure Stack-példányon.

Minden egyes Azure stack van egy alapértelmezett előfizetést, és számos bérlői előfizetések. Az alapértelmezett előfizetést egy Azure-előfizetést, akkor kell fizetnie, ha nincs bérlőspecifikus előfizetése. Az első előfizetést regisztrálni kell lennie. A jelentéskészítés működéséhez a több-bérlős használatot az előfizetés CSP vagy APSS előfizetést kell lennie.

Ezt követően a regisztráció frissül az Azure-előfizetés minden bérlő által használt Azure Stack. Bérlői előfizetések a CSP-típusúnak kell lennie, és a partnerrel, aki az alapértelmezett előfizetés tulajdonosa kell összesítő. Valaki más ügyfelek nem lehet regisztrálni.

Azure Stack globális Azure-bA továbbítja a használati adatokat, amikor Azure egy szolgáltatása, consults szintűre frissül a regisztrációt, és minden bérlő használati leképezi a megfelelő bérlői előfizetéshez. Ha a bérlő nincs regisztrálva, az alapértelmezett előfizetést az Azure Stack-példányhoz jut, hogy használat kerül.

Mivel bérlői előfizetések CSP-előfizetésekben, számlán küld a CSP-partner, és használati adatok nem látható a végfelhasználók számára.

## <a name="next-steps"></a>További lépések

- A CSP program kapcsolatos további információkért lásd: [Cloud Solution Provider program](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Erőforrás-használati adatok lekérése az Azure Stack kapcsolatos további tudnivalókért lásd: [használat és számlázás az Azure Stackben](azure-stack-billing-and-chargeback.md).
