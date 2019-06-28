---
title: Használati reporting infrastruktúra a Felhőbeli szolgáltatók számára az Azure Stackhez |} A Microsoft Docs
description: Az Azure Stack magában foglalja a bérlők szervizelt egy Cloud Service Provider (CSP), akkor fordul elő, majd továbbítja azokat az Azure használatának nyomon követéséhez szükséges infrastruktúrát.
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
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 0b4ff1e6c76bedc4618bfa527b0045d7bfce41af
ms.sourcegitcommit: bcaad8b7db2ea596018d973cb29283d8c6daebfb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2019
ms.locfileid: "67419483"
---
# <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>Jelentéskészítési infrastruktúra felhőszolgáltatók használat

Az Azure Stack magában foglalja a használat nyomon követésére, akkor fordul elő, majd továbbítja azokat az Azure-hoz szükséges infrastruktúrát. Az Azure-ban az Azure kereskedelmi dolgozza fel a használati adatok, és a megfelelő Azure-előfizetés használati díjak. Ez a használat nyomon követése a globális Azure-felhőben figyel ugyanúgy történik.

Egyes fogalmak egységes, globális Azure és az Azure Stack között. Az Azure Stack helyi előfizetések, amelyek hasonló szerepet Azure-előfizetéssel rendelkezik. Csak érvényes helyileg a helyi előfizetések. Ha az Azure-bA továbbítja használati helyi előfizetések Azure-előfizetések vannak leképezve.

Az Azure Stack helyi használati mérőszámok rendelkezik. A mérőszámok az Azure kereskedelmi használt helyi használat lesz leképezve. Azonban a mérőszám-azonosítói különböznek. Nincsenek elérhető helyileg, mint a Microsoft az elszámolási az egyik további mérőszámok.

Nincsenek hogyan szolgáltatások díjszabása az Azure Stack és Azure közötti különbségeket. Például az Azure Stackben, a díjat a virtuális gépek csak alapul virtuális mag/óra, az összes Virtuálisgép-sorozat, eltérően az Azure ugyanez a díjszabás. A hiba oka, hogy a globális Azure-ban a különböző díjak másik hardverre. Az Azure Stack az ügyfél hardver, nyújt, így nem indokolt, több virtuális gép osztály különböző sebességeit díja szerint számítjuk fel.

Akkor is megismerheti az Azure Stack az Azure-szolgáltatásokhoz hasonlóan azonos módon kereskedelmi és az árak a Partner Center, a használt mérőszámok:

1. Partnerközpont, nyissa meg a **Irányítópultos menüjében**, majd **értékesíteni**, majd válassza **díjszabás és ajánlatok**.
2. A **használat alapú szolgáltatások**válassza **aktuális**.
3. Nyissa meg a **Azure in CSP globális árlista** táblázatot.
4. Szűrés **régióban az Azure Stack =** .

## <a name="terms-used-for-billing-and-usage"></a>Számlázási és használati feltételek

Az alábbi kifejezések és fogalmak használt használati és számlázási az Azure Stack:

| Kifejezés | Meghatározás |
| --- | --- |
| Közvetlen CSP-partner | Közvetlen Felhőszolgáltató (CSP) partner közvetlenül kap számlát közvetlenül a Microsoft Azure és az Azure Stack használatának, és a számlák vásárlóink számára. |
| A közvetett CSP | Közvetett viszonteladók az közvetett szolgáltatóval (is terjesztő) működik. A viszonteladók toborzására végfelhasználókat; a közvetett szolgáltató tárolja a számlázást, a Microsoft, a számlázás a vásárlók kezeli, és további szolgáltatásokat, például a terméktámogatási szolgálathoz. |
| Végfelhasználói | Végfelhasználókat a vállalkozás és az alkalmazások és más az Azure Stacken futó alkalmazások és szolgáltatások saját kormányzati szervek. |

## <a name="next-steps"></a>További lépések

- A CSP program kapcsolatos további információkért lásd: [Felhőmegoldások](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Erőforrás-használati adatok lekérése az Azure Stack kapcsolatos további tudnivalókért lásd: [használat és számlázás az Azure Stackben](azure-stack-billing-and-chargeback.md).
