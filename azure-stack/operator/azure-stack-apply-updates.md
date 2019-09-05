---
title: OEM-frissítések telepítése a Azure Stackban | Microsoft Docs
description: Ismerje meg, hogyan telepíthet OEM-frissítéseket a Azure Stackban.
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
ms.openlocfilehash: a563c3ec43950122e045b0ec3168bcb3ca11fe56
ms.sourcegitcommit: e2f6205e6469b39c2395ee09424bb7632cb94c40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70271796"
---
# <a name="install-oem-updates-in-azure-stack"></a>OEM-frissítések telepítése Azure Stack

*Vonatkozik: Integrált rendszerek Azure Stack*

Az eredeti berendezésgyártó (OEM) frissítései a Azure Stack **frissítés** paneljén is telepíthetők. Ez a cikk végigvezeti a frissítési folyamat frissítésének, figyelésének és hibaelhárításának lépésein. A frissítés panel használatával megtekintheti a frissítési adatokat, telepítheti a frissítéseket, figyelheti a frissítések állapotát, áttekintheti az előzményeket, és megtekintheti a jelenlegi OEM-csomag verzióját.

A frissítéseket a felügyeleti portálról kezelheti, és az irányítópult **frissítésekkel** foglalkozó szakaszában a következőket végezheti el:

- Tekintse meg a fontos információkat, például az aktuális verziót.
- Frissítések telepítése és a figyelési folyamat.
- Tekintse át a korábban telepített frissítések korábbi frissítéseit.
- Tekintse meg a felhő jelenlegi OEM-csomagjának verzióját.

## <a name="determine-the-current-version"></a>Az aktuális verzió meghatározása

A **frissítések** panelen megtekintheti Azure stack aktuális verzióját. Megnyitás:

1.  Nyissa meg a Azure Stack felügyeleti portált.

2.  Válassza az **irányítópult**lehetőséget. A **frissítések** panelen az aktuális verzió szerepel a felsorolásban.

    ![Az alapértelmezett irányítópulton lévő frissítések csempe](./media/azure-stack-update-apply/image1.png)

    Ebben a képen például a verzió a 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Frissítések telepítése és a figyelési folyamat

1. Nyissa meg a Azure Stack felügyeleti portált.

2. Válassza az **irányítópult**lehetőséget. Válassza a **frissítés**lehetőséget.

3. Válassza ki az elérhető frissítést, amelyet telepíteni kíván. Ha nincs **elérhetőként**megjelölt frissítés, [elő kell készítenie a frissítési csomagot](azure-stack-update-prepare-package.md)

4. Válassza a **Frissítés most**lehetőséget.

    ![Azure Stack frissítés futtatásának részletei](./media/azure-stack-update-apply/image2.png)

5. Megtekintheti a magas szintű állapotot, mivel a frissítési folyamat megismétli a Azure Stack különböző alrendszerein keresztül. Az alrendszerek például olyan fizikai gazdagépek, Service Fabric, infrastruktúra-virtuális gépek és szolgáltatások, amelyek mind a rendszergazda, mind a felhasználói portált biztosítják. A frissítés erőforrás-szolgáltatója a frissítési folyamat során további részleteket jelent a frissítésről, például a sikeres lépések számáról és a folyamatban lévő számról.

6. A teljes naplók letöltéséhez válassza a **Letöltés összegzése** lehetőséget a frissítés futtatása részletek panelen.

    Ha probléma merül fel a frissítés figyelése közben, a [rendszerjogosultságú végpont](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint) segítségével figyelheti az Azure stack frissítési futtatásának állapotát. A rendszerjogosultságú végpont használatával folytathatja a sikertelen frissítés futtatását az utolsó sikeres lépésből, ha a Azure Stack portál elérhetetlenné válik. Útmutatásért lásd: [frissítések figyelése Azure stack a PowerShell használatával](azure-stack-update-monitor.md).

    ![Azure Stack frissítés futtatásának részletei](./media/azure-stack-update-apply/image3.png)

7. Ha elkészült, a frissítési erőforrás-szolgáltató **sikeres** megerősítést biztosít annak bemutatásához, hogy a frissítési folyamat befejeződött, és mennyi ideig tartott. Innen megtekintheti az összes frissítés, elérhető frissítés vagy telepített frissítés adatait a szűrő használatával.

    ![Azure-stack-Update-Apply](./media/azure-stack-update-apply/image4.png)

    Ha a frissítés sikertelen, a **frissítési** panel jelentései **figyelmet igényelnek**. A **teljes naplók letöltése** lehetőséggel olyan magas szintű állapotot érhet el, ahol a frissítés nem sikerült. A Azure Stack log gyűjtemény segít a diagnosztika és a hibaelhárítás terén.

## <a name="review-update-history"></a>Frissítési előzmények áttekintése

1. Nyissa meg a felügyeleti portált.

2. Válassza az **irányítópult**lehetőséget. Válassza a **frissítés**lehetőséget.

3. Válassza a **frissítési előzmények**lehetőséget.

    ![Azure Stack frissítési előzmények](./media/azure-stack-update-apply/image7.png)

## <a name="next-steps"></a>További lépések

-   [Frissítések kezelése az Azure Stackben – Áttekintés](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)  
-   [Azure Stack-karbantartási szabályzat](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy)  
