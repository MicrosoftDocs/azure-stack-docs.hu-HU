---
title: Régiók kezelése az Azure Stackben |} A Microsoft Docs
description: Régiók kezelése az Azure Stack áttekintése.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 04/16/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2018
ms.openlocfilehash: d0c61d5a0056b416bcbfaa26003f55955a89d0ad
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64295301"
---
# <a name="region-management-in-azure-stack"></a>Régiók kezelése az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack koncepcióját használja *régiók*, melyek áll az Azure Stack-infrastruktúrát alkotó hardver-erőforrások logikai entitásokat. Régiók kezelése az Azure Stack-infrastruktúra sikeres működéséhez szükséges összes erőforrást is megtalálhatja.

Egy integrált rendszer központi telepítéséhez (a továbbiakban egy *Azure Stack-felhőben*) lekérdezhetővé teszi akár egyetlen régión. Minden egyes Azure Stack Development Kit (ASDK) rendelkezik egy régiót nevű **helyi**. Ha telepít egy második Azure Stackkel integrált rendszereknél, vagy beállíthat egy másik példánya a csomagban található külön hardvert, az Azure Stack-felhőben egy másik régióban.

## <a name="information-available-through-the-region-management-tile"></a>A régió felügyeleti csempén keresztül érhetők el információk

Az Azure Stack szolgáltatásban elérhető Eszközkezelési lehetőségeivel régió készletével rendelkezik a **régiók kezelése** csempére. Ez a csempe az Azure Stack operátorait, az alapértelmezett irányítópult, a felügyeleti portálon érhető el. Ez a csempe keresztül figyelheti és frissítése az Azure Stack-régió és annak összetevői, amelyek régióspecifikus.

![A régió felügyeleti csempe](media/azure-stack-region-management/image1.png)

Ha rákattint egy régiót a a **régiók kezelése** csempét, akkor férhet hozzá a következő információkat:

[![A régió felügyeleti panel ablaktáblát leírása](media/azure-stack-region-management/regionssm.png "régió felügyeleti panel")](media/azure-stack-region-management/regions.png#lightbox)

1. **Az erőforrások menüjének**. Eléréséhez adott infrastruktúra felügyeleti területeket, és megtekintheti, és felhasználói erőforrások, például a storage-fiókok és a virtuális hálózatok kezelése.

2. **Riasztások**. Rendszerszintű riasztások listája, és adja meg az adatokat az egyes ezeket a riasztásokat.

3. **Frissítések**. A jelenlegi verzió az Azure Stack-infrastruktúra, a rendelkezésre álló frissítéseket és a frissítési előzmények megtekintése. Az integrált rendszer frissítheti is.

4. **Erőforrás-szolgáltatók**. Kezelheti a felhasználói funkciói az Azure Stack futtatásához szükséges összetevőket. Mindegyik erőforrás-szolgáltató olyan felügyeleti megoldást tartalmaz. Ez a tapasztalat riasztások tartalmazhatnak a szolgáltató, mérőszámok és más felügyeleti képességek jellemző az erőforrás-szolgáltató.

5. **Infrastruktúra-szerepkörök**. Az Azure Stack futtatásához szükséges összetevőket. Csak az infrastruktúra-szerepkör, amely a jelentés a riasztások jelennek meg. A szerepkör kiválasztásával megtekintheti a riasztásokat, a szerepkör és a szerepkörpéldányok, ezt a szerepkört futtató társított.

6. **Tulajdonságok**. A regisztrációs állapot és a régió felügyeleti panelen a környezet sajátosságait. Az állapot lehet **regisztrált**, **nincs regisztrálva**, vagy **lejárt**. Ha regisztrált, azt is bemutatja az Azure-előfizetés azonosítója segítségével regisztrálja az Azure Stack, a regisztrációs erőforráscsoportot és a neve mellett.

## <a name="next-steps"></a>További lépések

- [Állapot- és riasztásmonitorozás az Azure Stackben](azure-stack-monitor-health.md)
- [Frissítések kezelése az Azure Stackben](azure-stack-updates.md)

<!-- Update_Description: wording update -->