---
title: Területi felügyelet Azure Stack központban
titleSuffix: Azure Stack Hub
description: Az Azure Stack hub régió-felügyeletének áttekintése.
author: sethmanheim
ms.topic: article
ms.date: 12/15/2020
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: 86e449c9a825c80fa230d94948281c67e4756a25
ms.sourcegitcommit: a53ea4a28e715c80a99fa89e9d364bc4556558de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577023"
---
# <a name="region-management-in-azure-stack-hub"></a>Területi felügyelet Azure Stack központban

Azure Stack hub a *régiók* fogalmát használja, amelyek logikai entitások, amelyek az Azure stack hub-infrastruktúrát alkotó hardveres erőforrásokból állnak. A régiók kezelése területen megtalálhatja az Azure Stack hub-infrastruktúra sikeres üzemeltetéséhez szükséges összes erőforrást.

Egy integrált rendszertelepítés (más néven *Azure stack hub-felhő*) egyetlen régiót tesz elérhetővé. Minden Azure Stack Development Kit (ASDK) egy **helyi** nevű régióval rendelkezik. Ha egy második Azure Stack hub integrált rendszer üzembe helyezését végzi, vagy ha különálló hardveren állítja be a ASDK egy másik példányát, akkor ez a Azure Stack hub-felhő egy másik régió.

## <a name="information-available-through-the-region-management-tile"></a>A régió-felügyeleti csempén keresztül elérhető információk

Azure Stack hub **a régió-felügyeleti csempén** belül elérhető régió-felügyeleti képességekkel rendelkezik. Ez a csempe a felügyeleti portál alapértelmezett irányítópultján lévő Azure Stack hub-operátor számára érhető el. Ezen a képernyőn nyomon követheti és frissítheti Azure Stack hub-régióját és összetevőit, amelyek régió-specifikusak.

![A régió-felügyeleti csempe Azure Stack hub felügyeleti portálon](media/azure-stack-region-management/image1.png)

Ha kiválaszt egy régiót a **régió kezelése** csempén, akkor a következő információk érhetők el:

[![A Azure Stack hub felügyeleti portál régió-kezelés paneljén található ablaktáblák leírása](media/azure-stack-region-management/regionssm.png "Területi felügyelet panel Azure Stack hub felügyeleti portálon")](media/azure-stack-region-management/regions.png#lightbox)

1. **Az erőforrás menü**: hozzáférés különböző infrastruktúra-felügyeleti területekhez, valamint felhasználói erőforrások, például a Storage-fiókok és a virtuális hálózatok megtekintése és kezelése.

2. **Riasztások**: a rendszerszintű riasztások listázása és az egyes riasztások részleteinek megadása.

3. **Frissítések**: megtekintheti az Azure stack hub-infrastruktúra aktuális verzióját, az elérhető frissítéseket és a frissítési előzményeket. Az integrált rendszer frissítését is végezheti.

4. **Erőforrás-szolgáltatók**: kezelheti az Azure stack hub futtatásához szükséges összetevők által kínált felhasználói funkciókat. Minden erőforrás-szolgáltató felügyeleti felülettel rendelkezik. Ez a felhasználói élmény tartalmazhat riasztásokat az erőforrás-szolgáltatóra jellemző konkrét szolgáltatóhoz, metrikához és egyéb felügyeleti képességekhez.

5. **Infrastruktúra-szerepkörök**: Azure stack hub futtatásához szükséges összetevők. Csak a riasztásokat jelentéssel rendelkező infrastruktúra-szerepkörök szerepelnek a felsorolásban. Egy szerepkör kiválasztásával megtekintheti a szerepkörhöz társított riasztásokat, valamint azokat a szerepkör-példányokat, ahol ez a szerepkör fut.

6. **Properties (Tulajdonságok**): a környezet regisztrációjának állapota és adatai a régió kezelése panelen. Az állapot **regisztrálható**, **nem regisztrálható** vagy nem **járt le**. Ha regisztrálva van, akkor az Azure Stack hub regisztrálásához használt Azure-előfizetés AZONOSÍTÓját is megjeleníti a regisztrációs erőforráscsoport és a név mellett.

## <a name="next-steps"></a>További lépések

- [Állapot- és riasztásmonitorozás az Azure Stack Hubban](azure-stack-monitor-health.md)
- [Frissítések kezelése az Azure Stack Hubban](azure-stack-updates.md)
