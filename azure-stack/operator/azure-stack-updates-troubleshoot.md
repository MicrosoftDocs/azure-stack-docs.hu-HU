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
ms.openlocfilehash: 0021776f5b3be93a82305f41a9aa70c524273e4b
ms.sourcegitcommit: e57a53caac50d1f8762307e065fe886fcda7eadf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2019
ms.locfileid: "75190880"
---
# <a name="best-practices-for-troubleshooting-azure-stack-hub-patch-and-update-issues"></a>Ajánlott eljárások a Azure Stack hub javításával és frissítésével kapcsolatos hibák elhárításához

Ez a cikk áttekintést nyújt az ajánlott eljárásokról Azure Stack javítással és frissítéssel kapcsolatos hibák elhárításához, valamint a gyakori javítási és frissítési problémák elhárításához.


Az Azure Stack hub javítási és frissítési folyamata úgy lett kialakítva, hogy a kezelők következetes és áramvonalas módon alkalmazzák a frissítési csomagokat. Habár a probléma nem gyakori, a javítások és a frissítés során problémák léphetnek fel. A javítási és frissítési folyamat során a következő lépések ajánlottak:

0. **Előfeltételek**: Ellenőrizze, hogy követte-e a [frissítési tevékenység ellenőrzőlistáját](release-notes-checklist.md) , és konfigurálta-e az [automatikus naplózási gyűjteményt](azure-stack-configure-automatic-diagnostic-log-collection.md).
1. Ha a frissítés sikertelen volt, kövesse a sikertelen riasztások által létrehozott hibaelhárítási lépéseket.
2. Tekintse át az [általános Azure stack hub javítását és frissítésével kapcsolatos problémákat](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates-troubleshoot#Common-Azure-Stack-Hub-patch-and-update-issues) , és hajtsa végre a javasolt műveleteket, ha a probléma szerepel a felsorolásban.
3. Ha nem tudta feloldani a problémát a fenti lépésekkel, hozzon létre egy [Azure stack támogatási jegyet](azure-stack-help-and-support-overview.md). Győződjön meg arról, hogy a probléma előfordulási TimeSpan [összegyűjtött naplók](https://docs.microsoft.com/azure-stack/operator/azure-stack-configure-on-demand-diagnostic-log-collection) vannak.

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>A Azure Stack hub általános javítási és frissítési problémái

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek*

### <a name="preparationfailed"></a>PreparationFailed

**Alkalmazható**: Ez a probléma az összes támogatott kiadásra vonatkozik.

**OK**: amikor a Azure stack frissítés telepítését kísérli meg, a frissítés állapota meghiúsulhat, és az állapotot `PreparationFailed`értékre módosíthatja. Internetkapcsolattal rendelkező rendszerek esetében ez általában azt jelzi, hogy a frissítési csomag nem tölthető le megfelelően, mert gyenge az internetkapcsolat. 

**Szervizelés**: a probléma megkerüléséhez kattintson a **Telepítés most** lehetőségre. Ha a probléma továbbra is fennáll, javasoljuk, hogy a [frissítések telepítése](azure-stack-apply-updates.md?#install-updates-and-monitor-progress) szakaszt követve manuálisan töltse fel a frissítési csomagot.

**Előfordulás**: gyakori

## <a name="next-steps"></a>Következő lépések

- [Azure Stack frissítése](azure-stack-updates.md)  
- [Microsoft Azure Stack Súgó és támogatás](azure-stack-help-and-support-overview.md)
