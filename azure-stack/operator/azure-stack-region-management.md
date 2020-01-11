---
title: Régiók kezelése Azure Stack hub-ban | Microsoft Docs
titleSuffix: Azure Stack Hub
description: Az Azure Stack hub régió-felügyeletének áttekintése.
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
ms.openlocfilehash: 56fb993f7c4059ba468100904d10baabac38cd7d
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882045"
---
# <a name="region-management-in-azure-stack-hub"></a>Területi felügyelet Azure Stack központban

Azure Stack hub a *régiók*fogalmát használja, amelyek logikai entitások, amelyek az Azure stack hub-infrastruktúrát alkotó hardveres erőforrásokból állnak. A régiók kezelése területen megtalálhatja az Azure Stack hub-infrastruktúra sikeres üzemeltetéséhez szükséges összes erőforrást.

Egy integrált rendszertelepítés (más néven *Azure stack hub-felhő*) egyetlen régiót tesz elérhetővé. Minden Azure Stack Development Kit (ASDK) egy **helyi**nevű régióval rendelkezik. Ha egy második Azure Stack hub integrált rendszer üzembe helyezését végzi, vagy ha különálló hardveren állítja be a ASDK egy másik példányát, akkor ez a Azure Stack hub-felhő egy másik régió.

## <a name="information-available-through-the-region-management-tile"></a>A régió-felügyeleti csempén keresztül elérhető információk

Azure Stack hub **a régió-felügyeleti csempén** belül elérhető régió-felügyeleti képességekkel rendelkezik. Ez a csempe a felügyeleti portál alapértelmezett irányítópultján lévő Azure Stack hub-operátor számára érhető el. Ezen a csempén keresztül nyomon követheti és frissítheti Azure Stack hub-régióját és összetevőit, amelyek régiónként jellemzőek.

![A régió-felügyeleti csempe Azure Stack hub felügyeleti portálon](media/azure-stack-region-management/image1.png)

Ha egy régióra kattint a **régió kezelése** csempén, akkor a következő információkhoz férhet hozzá:

[![A Azure Stack hub felügyeleti portál régió-kezelés paneljén található ablaktáblák leírása](media/azure-stack-region-management/regionssm.png "Területi felügyelet panel Azure Stack hub felügyeleti portálon")](media/azure-stack-region-management/regions.png#lightbox)

1. **Az erőforrás menü**: hozzáférés különböző infrastruktúra-felügyeleti területekhez, valamint felhasználói erőforrások, például a Storage-fiókok és a virtuális hálózatok megtekintése és kezelése.

2. **Riasztások**: a rendszerszintű riasztások listázása és az egyes riasztások részleteinek megadása.

3. **Frissítések**: megtekintheti az Azure stack hub-infrastruktúra aktuális verzióját, az elérhető frissítéseket és a frissítési előzményeket. Az integrált rendszer frissítését is végezheti.

4. **Erőforrás-szolgáltatók**: kezelheti az Azure stack hub futtatásához szükséges összetevők által kínált felhasználói funkciókat. Minden erőforrás-szolgáltató felügyeleti felülettel rendelkezik. Ez a felhasználói élmény tartalmazhat riasztásokat az erőforrás-szolgáltatóra jellemző konkrét szolgáltatóhoz, metrikához és egyéb felügyeleti képességekhez.

5. **Infrastruktúra-szerepkörök**: Azure stack hub futtatásához szükséges összetevők. Csak a riasztásokat jelentéssel rendelkező infrastruktúra-szerepkörök szerepelnek a felsorolásban. Egy szerepkör kiválasztásával megtekintheti a szerepkörhöz társított riasztásokat, valamint azokat a szerepkör-példányokat, ahol ez a szerepkör fut.

6. **Properties (Tulajdonságok**): a környezet regisztrációjának állapota és adatai a régió kezelése panelen. Az állapot **regisztrálható**, **nem regisztrálható**vagy nem **járt le**. Ha regisztrálva van, akkor az Azure Stack hub regisztrálásához használt Azure-előfizetés AZONOSÍTÓját is megjeleníti a regisztrációs erőforráscsoport és a név mellett.

## <a name="next-steps"></a>Következő lépések

- [Állapot és riasztások figyelése Azure Stack központban](azure-stack-monitor-health.md)
- [Frissítések kezelése Azure Stack központban](azure-stack-updates.md)
