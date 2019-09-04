---
title: Az eredeti berendezésgyártó (OEM) frissítésének alkalmazása Azure Stackra | Microsoft Docs
description: Megtudhatja, hogyan alkalmazhat egy eredeti berendezésgyártó (OEM) frissítést Azure Stackra.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/03/2019
ms.author: justinha
ms.lastreviewed: 09/03/2019
ms.reviewer: ppacent
ms.openlocfilehash: 95b7d202d686edd123b552eaa95aa2b8c74c497a
ms.sourcegitcommit: 314fd74caf356b157583d38d2b8b1dee30408b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70234976"
---
# <a name="apply-updates-in-azure-stack"></a>Frissítések alkalmazása Azure Stack

*Vonatkozik: Integrált rendszerek Azure Stack*

A frissítés a Azure Stack **Update (frissítés** ) paneljén is alkalmazható. Ez a cikk végigvezeti a frissítési folyamat frissítésének, figyelésének és hibaelhárításának lépésein. A frissítés panel használatával megtekintheti a frissítési adatokat, telepítheti a frissítéseket, figyelheti a frissítések állapotát, áttekintheti a frissítési előzményeket, és megtekintheti a jelenlegi OEM-csomag verzióját.

A frissítéseket a felügyeleti portálról kezelheti. Az irányítópulton található **frissítéseket** a következő célra használhatja:

-   Tekintse meg a fontos információkat, például az aktuális verziót.
-   Frissítések telepítése és a figyelési folyamat.
-   Tekintse át a korábban telepített frissítések korábbi frissítéseit.
-   Tekintse meg a felhő jelenlegi OEM-csomagjának verzióját.

## <a name="determine-the-current-version"></a>Az aktuális verzió meghatározása

A **frissítések** panelen megtekintheti Azure stack aktuális verzióját. Megnyitás:

1.  Nyissa meg a Azure Stack felügyeleti portált.

2.  Válassza az **irányítópult**lehetőséget. A **frissítések** panelen az aktuális verzió szerepel a felsorolásban.

    ![Az alapértelmezett irányítópulton lévő frissítések csempe](./media/azure-stack-update-apply/image1.png)

    Ebben a képen például a verzió a 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Frissítések telepítése és a figyelési folyamat

1.  Nyissa meg a Azure Stack felügyeleti portált.

2.  Válassza az **irányítópult** > **frissítése**elemet.

3.  Válassza ki az alkalmazni kívánt elérhető frissítést. Ha nincs **elérhetőként**megjelölt frissítés, [elő kell készítenie a frissítési csomagot](azure-stack-update-prepare-package.md).

4.  Válassza a **Frissítés most**lehetőséget.

    ![Azure Stack frissítés futtatásának részletei](./media/azure-stack-update-apply/image2.png)

5.  Megtekintheti a magas szintű állapotot, mivel a frissítési folyamat megismétli a Azure Stack különböző alrendszerein keresztül. Az alrendszerek például olyan fizikai gazdagépek, Service Fabric, infrastruktúra-virtuális gépek és szolgáltatások, amelyek mind a rendszergazda, mind a felhasználói portált biztosítják. A frissítés erőforrás-szolgáltatója a frissítési folyamat során további részleteket jelent a frissítésről, például a sikeres lépések számáról és a folyamatban lévő számról.

6.  A teljes naplók letöltéséhez válassza a **Letöltés összegzése** lehetőséget a frissítés futtatása részletek panelen.

    Ha a frissítés figyelése során probléma lép fel, a [jogosultsági szintű végponton](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint) figyelheti egy Azure stack frissítési futtatásának állapotát, és folytathatja a sikertelen frissítés futtatását az utolsó sikeres lépéssel, ha a Azure stack-portál elérhetetlenné válik. Útmutatásért lásd: [frissítések figyelése Azure stack a PowerShell használatával](azure-stack-update-monitor.md).

    ![Azure Stack frissítés futtatásának részletei](./media/azure-stack-update-apply/image3.png)

7.  Ha elkészült, a frissítési erőforrás-szolgáltató **sikeres** megerősítést biztosít annak bemutatásához, hogy a frissítési folyamat befejeződött, és mennyi ideig tartott. A szűrő használatával megtekintheti az összes frissítéssel, elérhető frissítéssel vagy telepített frissítéssel kapcsolatos információt.

    ![Azure-stack-Update-Apply](./media/azure-stack-update-apply/image4.png)

    Ha a frissítés sikertelen, a **frissítési** panel jelentései **figyelmet igényelnek**. A **teljes naplók letöltése** lehetőséggel olyan magas szintű állapotot érhet el, ahol a frissítés nem sikerült. A Azure Stack log gyűjtemény segít a diagnosztika és a hibaelhárítás terén.

## <a name="review-update-history"></a>Frissítési előzmények áttekintése

1.  Nyissa meg a felügyeleti portált.

2.  Válassza az **irányítópult**lehetőséget. Válassza a **frissítés**lehetőséget.

3.  Válassza a **frissítési előzmények**lehetőséget.

    ![Azure Stack frissítési előzmények](./media/azure-stack-update-apply/image7.png)

## <a name="next-steps"></a>További lépések

-   [Frissítések kezelése az Azure Stackben – Áttekintés](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)  
-   [Azure Stack-karbantartási szabályzat](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy)  
