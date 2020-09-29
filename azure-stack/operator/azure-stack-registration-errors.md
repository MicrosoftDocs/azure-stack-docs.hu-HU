---
title: Használati és számlázási regisztrációs hibakódok Azure Stack központban
description: A Azure Stack hub használati és számlázási regisztrációs hibakódjának ismertetése.
author: sethmanheim
ms.topic: article
ms.date: 08/27/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 0c143a2a997a044e102eef63018dba5a492a3e11
ms.sourcegitcommit: 03aad17afe8519536066c735c59ad1bdfe8de083
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89041604"
---
# <a name="usage-and-billing-registration-error-codes"></a>Használati és számlázási regisztrációs hibakódok

Ha Ön egy felhőalapú megoldás-szolgáltató (CSP), a következő hibaüzenetek jelenhetnek meg, amikor [bérlőket ad hozzá](azure-stack-csp-ref-operations.md#add-tenant-to-registration) a regisztrációhoz az ügyfél Azure-ELŐfizetési azonosítójának használatával történő jelentéskészítéshez.

## <a name="list-of-registration-error-codes"></a>Regisztrációs hibakódok listája

| Hiba   | Részletek  | Megjegyzések  |
|---|---|---|
| RegistrationNotFound | A megadott regisztráció nem található. Győződjön meg arról, hogy helyesen adta meg a következő adatokat:<br>1. előfizetés azonosítója (megadott érték: **előfizetés azonosítója**),<br>2. erőforráscsoport (megadott érték: **erőforráscsoport**),<br>3. a regisztráció neve (megadott érték: **regisztrációs név**). | Ez a hiba általában akkor fordul elő, ha a kezdeti regisztrációra mutató információk nem megfelelőek. Ha ellenőriznie kell az erőforráscsoportot és a regisztráció nevét, megkeresheti a Azure Portalban az összes erőforrás listázásával. Ha egynél több regisztrációs erőforrást talál, tekintse meg a tulajdonságok **CloudDeploymentID** , és válassza ki azt a regisztrációt, amelynek **CloudDeploymentID** megfelel a felhőnek. A **CloudDeploymentID**megkereséséhez használhatja ezt a PowerShell-parancsot Azure stack hub-on:<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| BadCustomerSubscriptionId | A megadott **ügyfél-előfizetés azonosítója** és a **regisztrációs név** előfizetési azonosítója nem ugyanahhoz a Microsoft CSP-hez tartozik. Győződjön meg arról, hogy az ügyfél-előfizetés azonosítója helyes. Az ügyfél-előfizetés azonosítója megkülönbözteti a kis-és nagybetűket. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz. | Ez a hiba akkor fordul elő, ha az ügyfél-előfizetés CSP-előfizetés, de egy olyan CSP-partnerre mutat, amely eltér a kezdeti regisztráció során használt előfizetéstől. Ez az ellenőrzés olyan helyzetek elkerülését eredményezi, amelyek olyan CSP-partner számlázására vezethetnek, amely nem felel meg a használt Azure Stack hub-nak. |
| InvalidCustomerSubscriptionId  | Az **ügyfél-előfizetés azonosítója** érvénytelen. Győződjön meg arról, hogy érvényes Azure-előfizetés van megadva. |   |
| CustomerSubscriptionNotFound  | Nem található az **ügyfél-előfizetés azonosítója** a **regisztráció neve**alatt. Győződjön meg arról, hogy érvényes Azure-előfizetés van használatban, és hogy az előfizetés-azonosító hozzá lett adva a regisztrációhoz a PUT művelettel. | Ez a hiba akkor fordul elő, amikor egy bérlőt hozzáadtak egy előfizetéshez, de az ügyfél-előfizetés nem található a regisztrációhoz. Az ügyfél nem lett hozzáadva a regisztrációhoz, vagy az előfizetés-azonosító helytelenül lett írva. |
| UnauthorizedCspRegistration | A megadott **regisztrációs név** nem engedélyezett a több-bérlő használata esetén. Küldjön egy e-mailt, azstCSP@microsoft.com és adja meg a regisztrációs nevét, az erőforráscsoportot és a regisztrációhoz használt előfizetés-azonosítót. | A bérlők felvételének megkezdése előtt jóvá kell hagynia a regisztrációt a Microsoft több bérlője számára. |
| CustomerSubscriptionsNotAllowed | Az ügyfél-előfizetési műveletek nem támogatottak a leválasztott ügyfelek esetében. A szolgáltatás használatához regisztrálja újra az utólagos használatú licenceket. | A regisztráció, amelyhez bérlőket kíván felvenni, a kapacitás regisztrálása. A regisztráció létrehozásakor a rendszer a paramétert `BillingModel Capacity` használta. A bérlők hozzáadására csak az Ön által használt használati regisztrációk engedélyezettek. A paraméter használatával újra regisztrálnia kell `BillingModel PayAsYouUse` . |
| InvalidCSPSubscription | A megadott **ügyfél-előfizetés-azonosító** nem érvényes CSP-előfizetés. Győződjön meg arról, hogy érvényes Azure-előfizetés van megadva. | Ennek a hibának a legvalószínűbb oka az, hogy az ügyfél-előfizetés típusa hibás. |
| MetadataResolverBadGatewayError | Az egyik felsőbb rétegbeli kiszolgáló váratlan hibát adott vissza. Próbálkozzon újra később. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz. |

## <a name="next-steps"></a>Következő lépések

- További információ a [felhőalapú megoldások szolgáltatóinak használati jelentési infrastruktúráról](azure-stack-csp-ref-infrastructure.md).
- További információ a CSP programról: [felhőalapú megoldások](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Ha többet szeretne megtudni a Azure Stack hub erőforrás-használati adatainak lekéréséről, tekintse meg [a használat és a számlázás Azure stack hub-ban](azure-stack-billing-and-chargeback.md)című témakört.
