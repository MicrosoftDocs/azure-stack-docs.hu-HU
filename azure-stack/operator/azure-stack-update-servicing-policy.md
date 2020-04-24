---
title: Az eredeti berendezésgyártó (OEM) frissítésének alkalmazása Azure Stack hubhoz
description: Útmutató az eredeti berendezésgyártó (OEM) frissítésének Azure Stack hub-ra való alkalmazásához.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppace
ms.openlocfilehash: 402450c9a0abc8096456450e1e81f92a68d65114
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "78367267"
---
# <a name="azure-stack-hub-servicing-policy"></a>Azure Stack hub karbantartási szabályzata

Ez a cikk az Azure Stack hub integrált rendszereinek karbantartási szabályzatát ismerteti, ami szükséges ahhoz, hogy a rendszer támogatott állapotban maradjon, és hogyan kérhet támogatást.

## <a name="keep-your-system-under-support"></a>Tartsa a rendszerét támogatás alatt

Ahhoz, hogy továbbra is megkapja a támogatást, meg kell őriznie a Azure Stack hub aktuális frissítéseit.

Ahhoz, hogy az Azure Stack hub rendszer továbbra is támogatott állapotban maradjon, a példánynak a legutóbb kiadott frissítési verziót kell futtatnia, vagy a két korábbi frissítési verzió valamelyikét kell futtatnia.

A gyorsjavítások nem tekintendők jelentős frissítési verziónak. Ha a Azure Stack hub-rendszer *több mint két frissítés*mögött van, akkor az nem felel meg a megfelelőségnek. A támogatás fogadásához legalább a támogatott verzióra kell frissítenie.

Ha például a legújabb elérhető verzió a 1904, és az előző két frissítési csomag a 1903-es és a 1902-as verziót is támogatja, a 1902 és az 1903 is továbbra is támogatott. A 1901 azonban nem támogatott. A házirend akkor is igaz, ha egy hónap vagy kettő nem rendelkezik kiadással. Ha például az aktuális kiadás 1807, és nem volt 1806-es kiadás, az előző két, 1805-as és 1804-es frissítési csomag továbbra is támogatott.

A Microsoft szoftverfrissítési csomagjai nem összesítő jellegűek, és előfeltételként igénylik az előző frissítési csomagot vagy gyorsjavítást. Ha úgy dönt, hogy egy vagy több frissítést elhalaszt, vegye figyelembe a teljes futtatókörnyezetet, ha a legújabb verzióra szeretne jutni.

## <a name="get-support"></a>Támogatás kérése

Azure Stack hub ugyanazt a támogatási folyamatot követi, mint az Azure. A nagyvállalati ügyfelek követik az [Azure-támogatási kérelem létrehozása](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)című témakörben leírt eljárást. Ha Ön egy felhőalapú megoldás-szolgáltató (CSP) ügyfele, kérjen segítséget a CSP-től. További információ: [Azure-támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>További lépések

[Frissítések kezelése Azure Stack központban](azure-stack-updates.md)
