---
title: Azure Stack karbantartási szabályzat | Microsoft Docs
description: Ismerje meg a Azure Stack karbantartási szabályzatát, és azt, hogy miként lehet egy támogatott állapotban tartani egy integrált rendszerét.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 1022ab056157ea1a9bc925d3992a99bd0b395a35
ms.sourcegitcommit: 71d7990a2b21576c44bb2aea13ae2026e9510c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70188082"
---
- **OEM hardver gyártó által megadott frissítések**. Azure Stack hardveres partnerek felelősek a hardveres belső vezérlőprogram és az illesztőprogram-frissítési csomagok végpontok közötti karbantartási életciklusához (beleértve az útmutatást is). Emellett Azure Stack a hardveres partnereknek is, és útmutatást nyújtanak a hardveres életciklus-gazdagépen található összes szoftverhez és hardverhez. Az OEM hardvergyártó a saját letöltési helyén tárolja ezeket a frissítési csomagokat.

## <a name="update-package-release-cadence"></a>A csomag kiadási ritmusának frissítése

A Microsoft havi ütemben várja a szoftverfrissítési csomagok kiadását. Azonban lehetséges, hogy több vagy nem frissülő kiadás van egy hónapban. Az OEM-hardvergyártók a szükséges módon szabadítják fel a frissítéseiket.

A frissítések tervezésével és kezelésével, valamint az aktuális verzió meghatározásával kapcsolatos további információkért tekintse meg a frissítések [kezelése](azure-stack-updates.md)című témakörben található dokumentációt.

Egy adott frissítéssel kapcsolatos információkért, beleértve a letöltés módját is, tekintse meg a frissítéshez tartozó kibocsátási megjegyzéseket:

- [Azure Stack 1908 frissítés](azure-stack-release-notes-1908.md)
- [Azure Stack 1907 frissítés](azure-stack-release-notes-1907.md)
- [Azure Stack 1906 frissítés](azure-stack-release-notes-1906.md)
- [Azure Stack 1905 frissítés](azure-stack-release-notes-1905.md)

## <a name="hotfixes"></a>Gyorsjavítások

Alkalmanként a Microsoft gyorsjavításokat biztosít a Azure Stack, amelyek gyakran megelőző vagy időérzékeny problémákra vonatkoznak.  Az egyes gyorsjavítások a Microsoft Tudásbázis megfelelő cikkében jelennek meg, amely a probléma, az ok és a megoldás részleteit ismerteti.

*Vonatkozik: Integrált rendszerek Azure Stack*

Ez a cikk a Azure Stack integrált rendszerek karbantartási szabályzatát ismerteti, ami szükséges ahhoz, hogy a rendszer támogatott állapotban maradjon, és hogyan kérhet támogatást.

## <a name="keep-your-system-under-support"></a>Tartsa a rendszerét támogatás alatt

Ahhoz, hogy a Azure Stack példány továbbra is támogatott állapotban maradjon, a példánynak a legutóbb kiadott frissítési verziót kell futtatnia, vagy futtatnia kell a két korábbi frissítési verzió valamelyikét.

A gyorsjavítások nem tekintendők jelentős frissítési verziónak. Ha a Azure Stack-példánya *több mint két frissítés*mögött van, akkor az nem felel meg a megfelelőségnek. A támogatás fogadásához legalább a támogatott verzióra kell frissítenie.

Ha például a legújabb elérhető verzió a 1904, és az előző két frissítési csomag a 1903-es és a 1902-as verziót is támogatja, a 1902 és az 1903 is továbbra is támogatott. A 1901 azonban nem támogatott. A házirend akkor is igaz, ha egy hónap vagy kettő nem rendelkezik kiadással. Ha például az aktuális kiadás 1807, és nem volt 1806-es kiadás, az előző két, 1805-as és 1804-es frissítési csomag továbbra is támogatott.

A Microsoft szoftverfrissítési csomagjai nem összesítő jellegűek, és előfeltételként igénylik az előző frissítési csomagot vagy gyorsjavítást. Ha úgy dönt, hogy egy vagy több frissítést elhalaszt, vegye figyelembe a teljes futtatókörnyezetet, ha a legújabb verzióra szeretne jutni.

## <a name="get-support"></a>Támogatás kérése

Azure Stack az Azure-beli támogatási folyamatot követi. A nagyvállalati ügyfelek követik az [Azure-támogatási kérelem létrehozása](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)című témakörben leírt eljárást. Ha Ön egy felhőalapú szolgáltató (CSP) ügyfele, kérjen segítséget a CSP-től. További információ: Azure-támogatással kapcsolatos [Gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>További lépések

- [Frissítések kezelése az Azure Stackben](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)
