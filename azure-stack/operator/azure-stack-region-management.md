---
title: Régiók kezelése Azure Stackban | Microsoft Docs
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
ms.openlocfilehash: da80b144987e1dd90e45e83bb7be4ed581db143e
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991763"
---
# <a name="region-management-in-azure-stack"></a>Területi felügyelet Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Azure Stack a *régiók*fogalmát használja, amelyek logikai entitások, amelyek az Azure stack infrastruktúrát alkotó hardveres erőforrásokból állnak. A területi felügyeletben megtalálhatja az összes olyan erőforrást, amely a Azure Stack infrastruktúra sikeres üzemeltetéséhez szükséges.

Az egyik integrált Rendszertelepítési környezet (más néven *Azure stack felhő*) egyetlen régiót tesz elérhetővé. Minden Azure Stack Development Kit (ASDK) egy **helyi**nevű régióval rendelkezik. Ha egy második Azure Stack integrált rendszer üzembe helyezését végzi, vagy ha különálló hardveren állítja be a ASDK egy másik példányát, akkor ez a Azure Stack a felhő egy másik régió.

## <a name="information-available-through-the-region-management-tile"></a>A régió-felügyeleti csempén keresztül elérhető információk

A Azure Stack régió-felügyeleti lehetőségek állnak rendelkezésre a régió- **felügyeleti** csempén. Ez a csempe a felügyeleti portál alapértelmezett irányítópultján lévő Azure Stack operátor számára érhető el. Ezen a csempén keresztül nyomon követheti és frissítheti Azure Stack régióját és összetevőit, amelyek régiónként jellemzőek.

![A régió kezelése csempe](media/azure-stack-region-management/image1.png)

Ha egy régióra kattint a **régió kezelése** csempén, akkor a következő információkhoz férhet hozzá:

[![A régiók kezelése panel ablaktábláinak leírása](media/azure-stack-region-management/regionssm.png "Területi felügyelet") panel](media/azure-stack-region-management/regions.png#lightbox)

1. **Az erőforrás menüje**: Hozzáférés adott infrastruktúra-felügyeleti területekhez, valamint felhasználói erőforrások, például Storage-fiókok és virtuális hálózatok megtekintése és kezelése.

2. **Riasztások**: A rendszerszintű riasztások listázása és az egyes riasztások részleteinek megadása.

3. **Frissítések**: Megtekintheti az Azure Stack-infrastruktúra aktuális verzióját, az elérhető frissítéseket és a frissítési előzményeket. Az integrált rendszer frissítését is végezheti.

4. **Erőforrás-szolgáltatók**: Felügyelheti a Azure Stack futtatásához szükséges összetevők által kínált felhasználói funkciókat. Minden erőforrás-szolgáltató felügyeleti felülettel rendelkezik. Ez a felhasználói élmény tartalmazhat riasztásokat az erőforrás-szolgáltatóra jellemző konkrét szolgáltatóhoz, metrikához és egyéb felügyeleti képességekhez.

5. **Infrastruktúra-szerepkörök**: A Azure Stack futtatásához szükséges összetevők. Csak a riasztásokat jelentéssel rendelkező infrastruktúra-szerepkörök szerepelnek a felsorolásban. Egy szerepkör kiválasztásával megtekintheti a szerepkörhöz társított riasztásokat, valamint azokat a szerepkör-példányokat, ahol ez a szerepkör fut.

6. **Tulajdonságok**: A környezet regisztrációs állapota és adatai a régió kezelése panelen. Az állapot regisztrálható, **nem regisztrálható**vagy nem **járt le**. Ha regisztrálva van, megjeleníti a Azure Stack regisztrálásához használt Azure-előfizetés AZONOSÍTÓját, valamint a regisztrációs erőforráscsoportot és a nevet is.

## <a name="next-steps"></a>További lépések

- [Állapot- és riasztásmonitorozás az Azure Stackben](azure-stack-monitor-health.md)
- [Frissítések kezelése az Azure Stackben](azure-stack-updates.md)
