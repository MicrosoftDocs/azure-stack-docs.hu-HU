---
title: Az eredeti berendezésgyártó (OEM) frissítésének alkalmazása Azure Stackra | Microsoft Docs
description: Megtudhatja, hogyan alkalmazhat egy eredeti berendezésgyártó (OEM) frissítést Azure Stackra.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: mabrigg
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 4794ad098e83677d712af611fc74395e0e5f4e20
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019452"
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack karbantartási szabályzat

*Vonatkozik: Azure Stack integrált rendszerek @ no__t-0

Ez a cikk a Azure Stack integrált rendszerek karbantartási szabályzatát ismerteti, ami szükséges ahhoz, hogy a rendszer támogatott állapotban maradjon, és hogyan kérhet támogatást.

## <a name="keep-your-system-under-support"></a>Tartsa a rendszerét támogatás alatt

Ha továbbra is támogatást szeretne kapni, meg kell őriznie a Azure Stack aktuális frissítéseit.

Ahhoz, hogy a Azure Stack példány továbbra is támogatott állapotban maradjon, a példánynak a legutóbb kiadott frissítési verziót kell futtatnia, vagy futtatnia kell a két korábbi frissítési verzió valamelyikét.

A gyorsjavítások nem tekintendők jelentős frissítési verziónak. Ha a Azure Stack-példánya *több mint két frissítés*mögött van, akkor az nem felel meg a megfelelőségnek. A támogatás fogadásához legalább a támogatott verzióra kell frissítenie.

Ha például a legújabb elérhető verzió a 1904, és az előző két frissítési csomag a 1903-es és a 1902-as verziót is támogatja, a 1902 és az 1903 is továbbra is támogatott. A 1901 azonban nem támogatott. A házirend akkor is igaz, ha egy hónap vagy kettő nem rendelkezik kiadással. Ha például az aktuális kiadás 1807, és nem volt 1806-es kiadás, az előző két, 1805-as és 1804-es frissítési csomag továbbra is támogatott.

A Microsoft szoftverfrissítési csomagjai nem összesítő jellegűek, és előfeltételként igénylik az előző frissítési csomagot vagy gyorsjavítást. Ha úgy dönt, hogy egy vagy több frissítést elhalaszt, vegye figyelembe a teljes futtatókörnyezetet, ha a legújabb verzióra szeretne jutni.

## <a name="get-support"></a>Támogatás kérése

Azure Stack az Azure-beli támogatási folyamatot követi. A nagyvállalati ügyfelek követik az [Azure-támogatási kérelem létrehozása](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)című témakörben leírt eljárást. Ha Ön egy felhőalapú megoldás-szolgáltató (CSP) ügyfele, kérjen segítséget a CSP-től. További információ: [Azure-támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>További lépések

[Frissítések kezelése az Azure Stackben](azure-stack-updates.md)
