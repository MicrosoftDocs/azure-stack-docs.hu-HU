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
ms.openlocfilehash: d5606e7904fe311a54d792a18e5d4029c709b33c
ms.sourcegitcommit: 0866555e0ed240a65595052899ef1b836dd07fbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72257751"
---
# <a name="troubleshooting-patch-and-update-issues-for-azure-stack"></a>A Azure Stack javításával és frissítésével kapcsolatos hibák elhárítása

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

A jelen cikkben található útmutatást követve megoldhatja a Azure Stack frissítése során felmerülő problémákat.

## <a name="preparationfailed"></a>PreparationFailed

**Alkalmazható**: Ez a probléma az összes támogatott kiadásra vonatkozik.

**OK**: amikor a Azure stack frissítés telepítését kísérli meg, a frissítés állapota meghiúsulhat, és az állapotot `PreparationFailed` értékre módosíthatja. Internetkapcsolattal rendelkező rendszerek esetében ez általában azt jelzi, hogy a frissítési csomag nem tölthető le megfelelően, mert gyenge az internetkapcsolat. 

**Szervizelés**: a probléma megkerüléséhez kattintson a **Telepítés most** lehetőségre. Ha a probléma továbbra is fennáll, javasoljuk, hogy a [frissítések telepítése](azure-stack-apply-updates.md?#install-updates-and-monitor-progress) szakaszt követve manuálisan töltse fel a frissítési csomagot.

**Előfordulás**: gyakori

## <a name="next-steps"></a>Következő lépések

- [Azure Stack frissítése](azure-stack-updates.md)  
- [Microsoft Azure Stack Súgó és támogatás](azure-stack-help-and-support-overview.md)
