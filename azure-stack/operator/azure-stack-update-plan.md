---
title: Azure Stack frissítés megtervezése | Microsoft Docs
description: Megtudhatja, hogyan tervezheti meg Azure Stack frissítését.
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
ms.date: 08/23/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: b4f98f61068d6c428dc17c635651ad9aac54f6a8
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70010197"
---
# <a name="plan-for-an-azure-stack-update"></a>Azure Stack frissítés megtervezése

*Vonatkozik: Integrált rendszerek Azure Stack*

Készítse elő a Azure Stack, hogy a frissítés folyamata a lehető legzökkenőmentesebb legyen, így a felhasználók számára minimális hatással lehet. Ebben a cikkben áttekintheti a lehetséges szolgáltatási kimaradások bejelentésének lépéseit, és lépéseket kell végrehajtania a frissítés előkészítéséhez.

## <a name="notify-your-users-of-maintenance-operations"></a>Értesítse a felhasználókat a karbantartási műveletekről

Minden karbantartási műveletről értesítenie kell a felhasználókat, és ha lehetséges, a szokásos karbantartási időszakokat a munkaidőn kívüli időben kell ütemezni. A karbantartási műveletek befolyásolhatják a bérlői munkaterheléseket és a portál műveleteit is.

## <a name="prepare-for-an-azure-stack-update"></a>Felkészülés Azure Stack frissítésre

A frissítés előkészítéséhez győződjön meg arról, hogy alkalmazta a gyorsjavításokat, a biztonsági javításokat és az OEM-frissítéseket, ellenőrizte a Azure Stack példány állapotát, ellenőrizte a rendelkezésre álló kapacitást, és áttekintette a frissítési csomagot.

1. Tekintse át az ismert problémákat. Útmutatásért lásd: [Azure stack ismert problémák](https://docs.microsoft.com/azure-stack/operator/azure-stack-release-notes-known-issues-1907).

2. Tekintse át a biztonsági frissítéseket. A frissítések listáját itt tekintheti meg: [Azure stack biztonsági frissítések](https://docs.microsoft.com/azure-stack/operator/azure-stack-release-notes-security-updates-1907).

3. A frissítés telepítésének megkezdése előtt futtassa a [test-AzureStack](https://docs.microsoft.com/azure-stack/operator/azure-stack-diagnostic-test) parancsot a Azure stack állapotának ellenőrzéséhez és a talált működési problémák megoldásához, beleértve az összes figyelmeztetést és hibát. Emellett tekintse át az aktív riasztásokat, és ha bármelyik beavatkozást igényel, kezelje őket.

    1. Nyisson meg egy kiemelt jogosultságú végponti munkamenetet egy olyan gépről, amely hálózati hozzáféréssel rendelkezik a Azure Stack ERCS virtuális gépekhez. A PEP használatára vonatkozó utasításokért lásd: [a privilegizált végpont használata Azure Stackban](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).

    2. Test-AzureStack-Group UpdateReadiness

    3. Tekintse át a kimenetet, és javítsa ki az esetleges állapottal kapcsolatos hibákat. További információ: [Azure stack érvényesítési tesztének futtatása](https://docs.microsoft.com/azure-stack/operator/azure-stack-diagnostic-test).

4. Problémák megoldása. A megjelölt problémákkal kapcsolatos további információkért tekintse meg az állapot figyelése és riasztások című témakört. Útmutatásért lásd: [állapotok és riasztások figyelése Azure Stackban](https://docs.microsoft.com/azure-stack/operator/azure-stack-monitor-health).

5. A legújabb gyorsjavítások alkalmazása. A legújabb gyorsjavítások listáját a kibocsátási megjegyzések gyorsjavítása című szakaszban találja. A legújabb gyorsjavítás alkalmazása után ismételje meg a 3. és a 4. lépést.

6. Győződjön meg arról, hogy az OEM-csomag kompatibilis a frissíteni kívánt Azure Stack-verzióval. Ha az OEM-csomag nem kompatibilis a frissítendő Azure Stack verziójával, akkor Azure Stack frissítés futtatása előtt végre kell hajtania egy OEM-csomag frissítését. Útmutatásért tekintse meg a "Azure Stack eredeti berendezésgyártó (OEM) frissítéseinek alkalmazása" című témakört. Az OEM-csomag frissítésének alkalmazása után ismételje meg a 3. és a 4. lépést.

7. Futtassa a Capacity Planner eszközt. Az eszköz használatával kapcsolatos áttekintésért és útmutatásért lásd: [Azure stack kapacitás megtervezésének áttekintése](https://docs.microsoft.com/azure-stack/operator/azure-stack-capacity-planning-overview).

8. Tekintse át a frissítési csomagot. A karbantartási időszak megtervezésekor fontos áttekinteni a Microsoft által kiadott frissítési csomag adott típusát a kibocsátási megjegyzésekben leírtak szerint.

## <a name="next-steps"></a>További lépések

[Alkalmazza a Azure stack frissítést](azure-stack-apply-updates.md).
