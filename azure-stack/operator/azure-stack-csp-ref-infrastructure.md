---
title: Használati reporting infrastruktúra a Felhőbeli szolgáltatók számára az Azure Stackhez |} A Microsoft Docs
description: Az Azure Stack magában foglalja a bérlők szervizelt egy Cloud Service Provider (CSP), akkor fordul elő, majd továbbítja azokat az Azure használatának nyomon követéséhez szükséges infrastruktúrát.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/05/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: alfredop
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 1b8b83491211ae26493a6bb41c7f0f219f47f620
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64291902"
---
# <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>Jelentéskészítési infrastruktúra felhőszolgáltatók használat

Az Azure Stack magában foglalja a használat nyomon követésére, akkor fordul elő, majd továbbítja azokat az Azure-hoz szükséges infrastruktúrát. Az Azure-ban az Azure kereskedelmi dolgozza fel a használati adatok, és a megfelelő Azure-előfizetés használati díjak. Ez a használat nyomon követése a globális Azure-felhőben figyel ugyanúgy történik.

Vegye figyelembe, hogy egységes, globális Azure és az Azure Stack között-e bizonyos fogalmakat. Az Azure Stack helyi előfizetések, amelyek hasonló szerepet Azure-előfizetéssel rendelkezik. Csak érvényes helyileg a helyi előfizetések. Ha az Azure-bA továbbítja használati helyi előfizetések Azure-előfizetések vannak leképezve.

Az Azure Stack helyi használati mérőszámok rendelkezik. A mérőszámok az Azure kereskedelmi használt helyi használat lesz leképezve. Azonban a mérőszám-azonosítói különböznek. Nincsenek elérhető helyileg, mint a Microsoft az elszámolási az egyik további mérőszámok.

Nincsenek hogyan szolgáltatások díjszabása az Azure Stack és Azure közötti különbségeket. Például az Azure Stackben, a díjat a virtuális gépek csak alapul virtuális mag/óra, az összes Virtuálisgép-sorozat, eltérően az Azure ugyanez a díjszabás. A hiba oka, hogy a globális Azure-ban a különböző díjak másik hardverre. Az Azure Stack az ügyfél hardver, nyújt, így nem indokolt, több virtuális gép osztály különböző sebességeit díja szerint számítjuk fel.

Akkor is megismerheti az Azure Stack az Azure-szolgáltatásokhoz hasonlóan azonos módon kereskedelmi és az árak a Partner Center, a használt mérőszámok:

1. A Partner Center, lépjen a **Irányítópultos menüjében** > **díjszabás és ajánlatok**.
2. A **használat alapú szolgáltatások**válassza **aktuális**.
3. Nyissa meg a **Azure in CSP globális árlista** táblázatot.
4. Szűrés **régióban az Azure Stack =**.

## <a name="usage-and-billing-error-codes"></a>Használati és számlázási hibakódok

Az alábbi hibaüzenetek is kell észlelt, amikor egy regisztrációt a bérlők felvétele.

| Hiba                           | Részletek                                                                                                                                                                                                                                                                                                                           | Megjegyzések                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **RegistrationNotFound**            | A megadott bejegyzés nem található. Győződjön meg arról, hogy helyesen adta meg a következő információkat:<br>1. Előfizetés-azonosító (megadott érték: _előfizetés-azonosító_),<br>2. Erőforráscsoport (megadott érték: _erőforráscsoport_),<br>3. Regisztrációs név (megadott érték: _regisztrációs név_).                             | Ez a hiba általában akkor fordul elő, ha az adatokat a kezdeti regisztráció mutató nem megfelelő. Ellenőrizze az erőforráscsoportot és a regisztráció nevére van szüksége, megkeresni, az Azure Portalon, az összes erőforrás listáját. Ha egynél több regisztrációs erőforrás megkereséséhez tekintse meg a **CloudDeploymentID** a tulajdonságokat, és válassza ki a regisztrációs amelynek **CloudDeploymentID** megegyezik a felhőben. Keresése a **CloudDeploymentID**, a PowerShell használata az Azure Stack:<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| **BadCustomerSubscriptionId**       | A megadott _ügyfél-előfizetés-azonosító_ és a _regisztrációs név_ előfizetés-azonosító nem tulajdonosa a ugyanazon Microsoft Felhőszolgáltató. Ellenőrizze, hogy helyesen szerepel-e a felhasználói előfizetés-azonosító. Ha a probléma tartósan fennáll, forduljon az ügyfélszolgálathoz. | Ez a hiba akkor fordul elő, amikor az ügyfél-előfizetés CSP-előfizetésekben, de ez összesíti egy CSP-partner, amelyhez a kezdeti regisztráció használt előfizetés-összesíti eltérő. Ez az ellenőrzés jön létre, amely egy CSP-partnerrel, aki nem tehető felelőssé a használt Azure Stackhez készült számlázási eredményez olyan helyzet elkerülése érdekében.                                                                                                                                                                                                                                                                          |
| **InvalidCustomerSubscriptionId**   | A "_ügyfél-előfizetés-azonosító_" nem érvényes. Ellenőrizze, érvényes Azure-előfizetéssel rendelkezik.                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| **CustomerSubscriptionNotFound**    | _Ügyfél-előfizetés-azonosító_ _registration néven nem található. Ellenőrizze, hogy egy érvényes Azure-előfizetés használatban van, és az előfizetés-azonosító hozzáadásának a regisztrációhoz használja a PUT műveletet.                                                   | Ez a hiba akkor fordul elő, amikor próbál meg nem felelőség súlyossága, amely egy bérlőt egy előfizetés hozzá van adva, és az ügyfél-előfizetés nem található a regisztráció társítani kell. Az ügyfél még nem lett hozzáadva a regisztrációhoz, vagy az előfizetés-azonosító nem megfelelően lett írva.                                                                                                                                                                                                                                                                                                                                |
| **UnauthorizedCspRegistration**     | A megadott _regisztrációs név_ több-bérlős használata nem engedélyezett. E-mail küldése azstCSP@microsoft.com és a regisztrációs név, erőforráscsoport és a regisztrációt a használt előfizetés-azonosító.                                                                                    | Hagyja jóvá a több-bérlős Microsoft bérlők hozzáadása előtt kell egy regisztrációt.                                                                                                                                                                                                                                                                                                                                                                                             |
| **CustomerSubscriptionsNotAllowed** | Ügyfél-előfizetési műveletek nem támogatottak a leválasztott ügyfelek számára. Ez a funkció használatához regisztrálja újra a licenceléssel kell fizetnie, használhatja.                                                                                                                                                                    | A regisztráció, amelyhez bérlők hozzáadni kívánt egy kapacitás-regisztráció; Ez azt jelenti, a regisztráció létrehozásának, a paraméter `BillingModel Capacity` lett megadva. Feladatonként regisztrációk használata során adja hozzá a bérlők számára engedélyezett. Újra regisztrálnia kell a paraméter használatával `BillingModel PayAsYouUse`.                                                                                                                                                                                                                                                                                          |
| **InvalidCSPSubscription**          | A megadott _ügyfél-előfizetés-azonosító_ nem érvényes CSP-előfizetésekben. Ellenőrizze, érvényes Azure-előfizetéssel rendelkezik.                                                                                                                                                        | Ez a legvalószínűbb oka az ügyfél-előfizetés alatt rosszul adott meg.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **MetadataResolverBadGatewayError** | A felsőbb rétegbeli kiszolgálók egyike a nem várt hibát adott vissza. Próbálkozzon újra később. Ha a probléma tartósan fennáll, forduljon az ügyfélszolgálathoz.                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="terms-used-for-billing-and-usage"></a>Számlázási és használati feltételek

Az alábbi kifejezések és fogalmak használt használati és számlázási az Azure Stack:

| Időtartam | Meghatározás |
| --- | --- |
| Közvetlen CSP-partner | Közvetlen Cloud Solution Provider (CSP) partner közvetlenül kap számlát közvetlenül a Microsoft Azure és az Azure Stack használatának, és a számlák vásárlóink számára. |
| A közvetett CSP | Közvetett viszonteladók az közvetett szolgáltatóval (is terjesztő) működik. A viszonteladók toborzására végfelhasználókat; a közvetett szolgáltató tárolja a számlázást, a Microsoft, a számlázás a vásárlók kezeli, és további szolgáltatásokat, például a terméktámogatási szolgálathoz. |
| Végfelhasználói | Végfelhasználókat a vállalkozás és az alkalmazások és más az Azure Stacken futó alkalmazások és szolgáltatások saját kormányzati szervek. |

## <a name="next-steps"></a>További lépések

- A CSP program kapcsolatos további információkért lásd: [Cloud Solution Provider program](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Erőforrás-használati adatok lekérése az Azure Stack kapcsolatos további tudnivalókért lásd: [használat és számlázás az Azure Stackben](azure-stack-billing-and-chargeback.md).

<!-- Update_Description: wording update -->