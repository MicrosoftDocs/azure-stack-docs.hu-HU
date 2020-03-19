---
title: Azure Stack hub frissítéseinek hibáinak megoldása
description: Azure Stack hub-kezelőként megtudhatja, hogyan oldhatja meg a frissítéssel kapcsolatos problémákat, hogy Azure Stack hub a lehető leggyorsabban tudjon visszatérni az éles környezetbe.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 09/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 6cf5d83b9fdf71ba68506c760112635910d26eb3
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2020
ms.locfileid: "79512315"
---
# <a name="best-practices-for-troubleshooting-azure-stack-hub-patch-and-update-issues"></a>Ajánlott eljárások a Azure Stack hub javításával és frissítésével kapcsolatos hibák elhárításához

Ez a cikk áttekintést nyújt az Azure Stack hub javításával és frissítésével kapcsolatos hibák elhárításával kapcsolatos ajánlott eljárásokról, valamint a gyakori javítási és frissítési problémák megoldásáról.


Az Azure Stack hub javítási és frissítési folyamata úgy lett kialakítva, hogy a kezelők következetes és áramvonalas módon alkalmazzák a frissítési csomagokat. Habár nem gyakori, a javítások és a frissítési folyamat során problémák léphetnek fel. A javítási és frissítési folyamat során a következő lépések ajánlottak:

0. **Előfeltételek**: Ellenőrizze, hogy követte-e a [frissítési tevékenység ellenőrzőlistáját](release-notes-checklist.md) , és konfigurálta-e az [automatikus naplózási gyűjteményt](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md).
1. Ha a frissítés sikertelen volt, kövesse a sikertelen riasztások által létrehozott hibaelhárítási lépéseket.
2. Tekintse át az [általános Azure stack hub javítását és frissítésével kapcsolatos problémákat](#common-azure-stack-hub-patch-and-update-issues) , és hajtsa végre a javasolt műveleteket, ha a probléma szerepel a felsorolásban.
3. Ha nem tudta feloldani a problémát a fenti lépésekkel, hozzon létre egy [Azure stack hub támogatási jegyet](azure-stack-help-and-support-overview-tzl.md). Győződjön meg arról, hogy a probléma előfordulási TimeSpan [összegyűjtött naplók](https://docs.microsoft.com/azure-stack/operator/azure-stack-configure-on-demand-diagnostic-log-collection) vannak.

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>A Azure Stack hub általános javítási és frissítési problémái

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek*

### <a name="preparationfailed"></a>PreparationFailed

**Alkalmazható**: Ez a probléma az összes támogatott kiadásra vonatkozik.

**OK**: az Azure stack hub frissítésének telepítésére tett kísérlet során előfordulhat, hogy a frissítés állapota meghiúsul, és az állapota `PreparationFailed`értékre változik. Internetkapcsolattal rendelkező rendszerek esetében ez általában azt jelzi, hogy a frissítési csomag nem tölthető le megfelelően, mert gyenge az internetkapcsolat. 

**Szervizelés**: a probléma megkerüléséhez kattintson a **Telepítés most** lehetőségre. Ha a probléma továbbra is fennáll, javasoljuk, hogy a [frissítések telepítése](azure-stack-apply-updates.md?#install-updates-and-monitor-progress) szakaszt követve manuálisan töltse fel a frissítési csomagot.

**Előfordulás**: gyakori

## <a name="next-steps"></a>Következő lépések

- [Azure Stack hub frissítése](azure-stack-updates.md)  
- [Microsoft Azure Stack hub Súgó és támogatás](azure-stack-help-and-support-overview-tzl.md)
