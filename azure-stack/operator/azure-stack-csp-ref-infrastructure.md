---
title: A Azure Stack hub felhőalapú megoldás-szolgáltatóinak használati jelentési infrastruktúrája
description: A felhőalapú megoldás-szolgáltató (CSP) által kiszolgált bérlők használatának nyomon követésére használt használati jelentési infrastruktúra ismertetése.
author: sethmanheim
ms.topic: article
ms.date: 05/01/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 2a154390f3d3aef4af93d12899a6a658be41d884
ms.sourcegitcommit: 278aaeca069213a98b90751253f6b15423634849
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82742376"
---
# <a name="usage-reporting-infrastructure-for-cloud-solution-providers"></a>Használati jelentéskészítési infrastruktúra felhőalapú megoldás-szolgáltatók számára

Azure Stack hub magában foglalja a használat nyomon követéséhez és az Azure-ba való továbbításához szükséges infrastruktúrát. Az Azure-ban az Azure kereskedelmi feldolgozza a [használati adatokat és a díjakat](azure-stack-billing-and-chargeback.md) a megfelelő Azure-előfizetésekben. Ez a folyamat ugyanúgy működik, mint a globális Azure-felhőben a használat nyomon követése.

Bizonyos fogalmak konzisztensek a globális Azure és a Azure Stack hub között. Azure Stack hub helyi előfizetésekkel rendelkezik, amelyek egy Azure-előfizetéshez hasonló szerepkört teljesítenek. A helyi előfizetések csak helyileg érvényesek. A helyi előfizetések az Azure-előfizetésekhez vannak leképezve, amikor a használatot az Azure-ba továbbítja

Azure Stack hub helyi használati mérőszámokkal rendelkezik. A helyi használat az Azure-kereskedelemben használt mérőórákra van leképezve. A mérési azonosítók azonban eltérőek. Több mérőszám is elérhető helyileg, mint amennyit a Microsoft a számlázáshoz használ.

A szolgáltatások díjszabása a Azure Stack hub és az Azure területén is fennáll. Azure Stack hub esetében például a virtuális gépek díja csak virtuális mag/óra alapján történik, és az Azure-tól eltérően az összes virtuálisgép-sorozat esetében azonos a díjszabás. Ennek az az oka, hogy a globális Azure-ban a különböző árak különböző hardvereket tükröznek. Azure Stack hub-ban az ügyfél biztosítja a hardvert, így nincs ok arra, hogy a különböző virtuálisgép-osztályok esetében eltérő díjszabást kellene fizetni.

A kereskedelemben használt Azure Stack hub-mérőszámokról és azok árairól a partner Centerben talál további információt. A folyamat ugyanaz, mint az Azure-szolgáltatásokhoz:

1. A partner Centerben lépjen az **irányítópult** menüre, majd válassza az **értékesítés**, majd a **díjszabás és ajánlatok**lehetőséget.
2. A **használati alapú szolgáltatások**területen válassza az **aktuális**lehetőséget.
3. Nyissa meg az **Azure-t a globális CSP árlista-** számolótáblában.
4. Szűrés a **régión = Azure stack hub**.

## <a name="terms-used-for-billing-and-usage"></a>A számlázáshoz és a használathoz használt kifejezések

A Azure Stack hub használati és számlázási feltételeit a következő feltételek és fogalmak használják:

| Időtartam | Meghatározás |
| --- | --- |
| Közvetlen CSP-partner | A közvetlen CSP-partner közvetlenül a Microsofttól kapja meg az Azure-t és Azure Stack hub-használatot, és az ügyfeleket közvetlenül számlázza. |
| Közvetett CSP | A közvetett viszonteladók egy közvetett szolgáltatóval (más néven terjesztővel) működnek. A viszonteladók végfelhasználók toborzása; a közvetett szolgáltató a Microsofttal számlázási kapcsolatot tart fenn, kezeli az ügyfelek számlázását, és további szolgáltatásokat nyújt, mint például a terméktámogatás. |
| Végfelhasználó | A végfelhasználók az alkalmazások és az Azure Stack hub-on futó egyéb munkaterhelések tulajdonában lévő vállalkozások és kormányzati szervek. |

## <a name="next-steps"></a>További lépések

- További információ a CSP programról: [felhőalapú megoldások](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Ha többet szeretne megtudni a Azure Stack hub erőforrás-használati adatainak lekéréséről, tekintse meg [a használat és a számlázás Azure stack hub-ban](azure-stack-billing-and-chargeback.md)című témakört.
