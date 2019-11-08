---
title: Régiók kezelése Azure Stackban | Microsoft Docs
titleSuffix: Azure Stack
description: A Azure Stack régióinak felügyeletének áttekintése.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2018
ms.openlocfilehash: 38004b88f43ef59448ca99c3eb2762e5ca63e89c
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802279"
---
# <a name="region-management-in-azure-stack"></a>Területi felügyelet Azure Stack

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Azure Stack a *régiók*fogalmát használja, amelyek logikai entitások, amelyek az Azure stack infrastruktúrát alkotó hardveres erőforrásokból állnak. A területi felügyeletben megtalálhatja az összes olyan erőforrást, amely a Azure Stack infrastruktúra sikeres üzemeltetéséhez szükséges.

Az egyik integrált Rendszertelepítési környezet (más néven *Azure stack felhő*) egyetlen régiót tesz elérhetővé. Minden Azure Stack Development Kit (ASDK) egy **helyi**nevű régióval rendelkezik. Ha egy második Azure Stack integrált rendszer üzembe helyezését végzi, vagy ha különálló hardveren állítja be a ASDK egy másik példányát, akkor ez a Azure Stack a felhő egy másik régió.

## <a name="information-available-through-the-region-management-tile"></a>A régió-felügyeleti csempén keresztül elérhető információk

A Azure Stack régió-felügyeleti lehetőségek állnak rendelkezésre a régió- **felügyeleti** csempén. Ez a csempe a felügyeleti portál alapértelmezett irányítópultján lévő Azure Stack operátor számára érhető el. Ezen a csempén keresztül nyomon követheti és frissítheti Azure Stack régióját és összetevőit, amelyek régiónként jellemzőek.

![A Azure Stack felügyeleti portál régió-felügyeleti csempéje](media/azure-stack-region-management/image1.png)

Ha egy régióra kattint a **régió kezelése** csempén, akkor a következő információkhoz férhet hozzá:

[![Az ablaktáblák leírása a Azure Stack felügyeleti portál régió-kezelés paneljén](media/azure-stack-region-management/regionssm.png "Területi felügyelet panel Azure Stack felügyeleti portálon")](media/azure-stack-region-management/regions.png#lightbox)

1. **Az erőforrás menü**: hozzáférés különböző infrastruktúra-felügyeleti területekhez, valamint felhasználói erőforrások, például a Storage-fiókok és a virtuális hálózatok megtekintése és kezelése.

2. **Riasztások**: a rendszerszintű riasztások listázása és az egyes riasztások részleteinek megadása.

3. **Frissítések**: megtekintheti a Azure stack-infrastruktúra aktuális verzióját, az elérhető frissítéseket és a frissítési előzményeket. Az integrált rendszer frissítését is végezheti.

4. **Erőforrás-szolgáltatók**: a Azure stack futtatásához szükséges összetevők által kínált felhasználói funkciók kezelése. Minden erőforrás-szolgáltató felügyeleti felülettel rendelkezik. Ez a felhasználói élmény tartalmazhat riasztásokat az erőforrás-szolgáltatóra jellemző konkrét szolgáltatóhoz, metrikához és egyéb felügyeleti képességekhez.

5. **Infrastruktúra-szerepkörök**: a Azure stack futtatásához szükséges összetevők. Csak a riasztásokat jelentéssel rendelkező infrastruktúra-szerepkörök szerepelnek a felsorolásban. Egy szerepkör kiválasztásával megtekintheti a szerepkörhöz társított riasztásokat, valamint azokat a szerepkör-példányokat, ahol ez a szerepkör fut.

6. **Properties (Tulajdonságok**): a környezet regisztrációjának állapota és adatai a régió kezelése panelen. Az állapot **regisztrálható**, **nem regisztrálható**vagy nem **járt le**. Ha regisztrálva van, megjeleníti a Azure Stack regisztrálásához használt Azure-előfizetés AZONOSÍTÓját, valamint a regisztrációs erőforráscsoportot és a nevet is.

## <a name="next-steps"></a>Következő lépések

- [Állapot- és riasztásmonitorozás az Azure Stackben](azure-stack-monitor-health.md)
- [Frissítések kezelése az Azure Stackben](azure-stack-updates.md)
