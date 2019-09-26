---
title: A Azure Stack frissítéseinek hibáinak megoldása | Microsoft Docs
description: Azure Stack operátorként megtudhatja, hogyan oldhatja meg a frissítéssel kapcsolatos problémákat, hogy Azure Stack a lehető leggyorsabban visszatérhet az éles környezetbe.
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
ms.date: 09/23/2019
ms.author: mabrigg
ms.lastreviewed: 09/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 76c6c21c2a728004d2a33c04a02b905ec674b972
ms.sourcegitcommit: d967cf8cae320fa09f1e97eeb888e3db5b6e7972
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301225"
---
# <a name="troubleshooting-patch-and-update-issues-for-azure-stack"></a>A Azure Stack javításával és frissítésével kapcsolatos hibák elhárítása

*Vonatkozik: Integrált rendszerek Azure Stack*

A jelen cikkben található útmutatást követve megoldhatja a Azure Stack frissítése során felmerülő problémákat.

## <a name="preparationfailed"></a>PreparationFailed

**Alkalmazható**: Ez a probléma az összes támogatott kiadásra vonatkozik.

**Ok**: A Azure Stack frissítés telepítésének megkísérlése során előfordulhat, hogy a frissítés állapota meghiúsul, és az állapot `PreparationFailed`módosul. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz.

**Szervizelés**: A 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti ezt a problémát úgy, hogy a **frissítés** most lehetőségre kattint (nem **folytatódik**). A URP ezután törli az előző kísérletből származó fájlokat, majd újraindítja a letöltést. Ha a probléma továbbra is fennáll, javasoljuk, hogy a [frissítések telepítése](azure-stack-apply-updates.md?#install-updates-and-monitor-progress) szakaszt követve manuálisan töltse fel a frissítési csomagot.

**Előfordulás**: Közös

## <a name="next-steps"></a>További lépések

- [Azure Stack frissítése](azure-stack-updates.md)  
- [Microsoft Azure Stack Súgó és támogatás](azure-stack-help-and-support-overview.md)
