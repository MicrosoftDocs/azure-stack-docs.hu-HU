---
title: Használati jelentéskészítési infrastruktúra felhőalapú megoldás-szolgáltatók számára a Azure Stackhoz | Microsoft Docs
description: A felhőalapú megoldás-szolgáltató (CSP) által kiszolgált bérlők használatának nyomon követésére használt használati jelentési infrastruktúra ismertetése.
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
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: c170e450739609e0ef0be353f2354a17e39a28b1
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961841"
---
# <a name="usage-reporting-infrastructure-for-cloud-solution-providers"></a>Használati jelentéskészítési infrastruktúra felhőalapú megoldás-szolgáltatók számára

Azure Stack tartalmazza a használat nyomon követéséhez és az Azure-ba való továbbításához szükséges infrastruktúrát. Az Azure-ban az Azure kereskedelmi feldolgozza a használati adatokat és a díjakat a megfelelő Azure-előfizetésekben. Ez a folyamat ugyanúgy működik, mint a globális Azure-felhőben a használat nyomon követése.

Bizonyos fogalmak konzisztensek a globális Azure és a Azure Stack között. Azure Stack rendelkezik helyi előfizetésekkel, amelyek az Azure-előfizetéshez hasonló szerepkört teljesítenek. A helyi előfizetések csak helyileg érvényesek. A helyi előfizetések az Azure-előfizetésekhez vannak leképezve, amikor a használatot az Azure-ba továbbítja

Azure Stack helyi használati mérőszámokkal rendelkezik. A helyi használat az Azure-kereskedelemben használt mérőórákra van leképezve. A mérési azonosítók azonban eltérőek. Több mérőszám is elérhető helyileg, mint amennyit a Microsoft a számlázáshoz használ.

A szolgáltatások díjszabása Azure Stack és az Azure-ban is fennáll. Azure Stack például a virtuális gépek díját csak virtuális mag/óra alapján számítjuk fel, az Azure-val ellentétben az összes virtuálisgép-sorozat esetében azonos sebességgel. Ennek az az oka, hogy a globális Azure-ban a különböző árak különböző hardvereket tükröznek. Azure Stack az ügyfél biztosítja a hardvert, így nincs ok arra, hogy a különböző virtuálisgép-osztályok esetében eltérő díjszabást kellene fizetni.

A kereskedelemben használt Azure Stack mérőszámokról és azok árairól a partner Centerben talál további információt. A folyamat ugyanaz, mint az Azure-szolgáltatásokhoz:

1. A partner Centerben lépjen az **irányítópult menüre**, majd válassza az **értékesítés**, majd a **díjszabás és ajánlatok**lehetőséget.
2. A **használati alapú szolgáltatások**területen válassza az **aktuális**lehetőséget.
3. Nyissa meg az **Azure-t a globális CSP árlista-** számolótáblában.
4. Szűrés a **régión = Azure stack**.

## <a name="terms-used-for-billing-and-usage"></a>A számlázáshoz és a használathoz használt kifejezések

A használat és a számlázás a következő feltételekkel és fogalmakkal történik Azure Stackban:

| Kifejezés | Meghatározás |
| --- | --- |
| Közvetlen CSP-partner | A közvetlen CSP-partner közvetlenül a Microsofttól kapja meg az Azure-t, és Azure Stack használatot, és az ügyfeleket közvetlenül számlázza. |
| Közvetett CSP | A közvetett viszonteladók egy közvetett szolgáltatóval (más néven terjesztővel) működnek. A viszonteladók végfelhasználók toborzása; a közvetett szolgáltató a Microsofttal számlázási kapcsolatot tart fenn, kezeli az ügyfelek számlázását, és további szolgáltatásokat nyújt, mint például a terméktámogatás. |
| Végfelhasználó | A végfelhasználók az alkalmazások és a Azure Stack-on futó egyéb munkaterhelések tulajdonában lévő vállalkozások és kormányzati szervek. |

## <a name="next-steps"></a>További lépések

- További információ a CSP programról: [felhőalapú megoldások](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Ha többet szeretne megtudni a Azure Stack erőforrás-használati adatok lekéréséről, tekintse meg a [használat és a számlázás Azure Stackban](azure-stack-billing-and-chargeback.md)című témakört.
