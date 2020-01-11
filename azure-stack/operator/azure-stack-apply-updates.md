---
title: Azure Stack hub frissítéseinek telepítése | Microsoft Docs
description: Ismerje meg, hogyan telepítheti Azure Stack hub-frissítéseket.
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
ms.date: 09/10/2019
ms.author: mabrigg
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 337f5bfccc7deccf7214d452546c1e0ba3296555
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882895"
---
# <a name="install-azure-stack-hub-updates"></a>Azure Stack hub frissítéseinek telepítése

A frissítési csomagokat a Azure Stack hub **frissítés** paneljén telepítheti. Ez a cikk végigvezeti a frissítési folyamat frissítésének, figyelésének és hibaelhárításának lépésein. A frissítés panel használatával megtekintheti a frissítési adatokat, telepítheti a frissítéseket, figyelheti a frissítések állapotát, áttekintheti az előzményeket, és megtekintheti az aktuális Azure Stack hub és OEM csomag verzióját.

A frissítéseket a felügyeleti portálról kezelheti, és az irányítópult **frissítésekkel** foglalkozó szakaszában a következőket végezheti el:

- Tekintse meg a fontos információkat, például az aktuális verziót.
- Frissítések telepítése és a figyelési folyamat.
- Tekintse át a korábban telepített frissítések korábbi frissítéseit.
- Tekintse meg a felhő jelenlegi OEM-csomagjának verzióját.

## <a name="determine-the-current-version"></a>Az aktuális verzió meghatározása

Az Azure Stack hub aktuális verzióját a **frissítések** panelen tekintheti meg. Megnyitás:

1.  Nyissa meg az Azure Stack hub felügyeleti portált.

2.  Válassza az **irányítópult**lehetőséget. A **frissítések** panelen az aktuális verzió szerepel a felsorolásban.

    ![Az alapértelmezett irányítópulton lévő frissítések csempe](./media/azure-stack-update-apply/image1.png)

    Ebben a képen például a verzió a 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Frissítések telepítése és a figyelési folyamat

1. Nyissa meg az Azure Stack hub felügyeleti portált.

2. Válassza az **irányítópult**lehetőséget. Válassza a **Frissítés** lehetőséget.

3. Válassza ki az elérhető frissítést, amelyet telepíteni kíván. Ha nincs **elérhetőként**megjelölt frissítés, [elő kell készítenie a frissítési csomagot](azure-stack-update-prepare-package.md)

4. Válassza a **Frissítés most**lehetőséget.

    ![Azure Stack hub frissítési futtatásának részletei](./media/azure-stack-update-apply/image2.png)

5. Megtekintheti a magas szintű állapotot, mivel a frissítési folyamat megismétli a Azure Stack hub különböző alrendszerein keresztül. Az alrendszerek például olyan fizikai gazdagépek, Service Fabric, infrastruktúra-virtuális gépek és szolgáltatások, amelyek mind a rendszergazda, mind a felhasználói portált biztosítják. A frissítés erőforrás-szolgáltatója a frissítési folyamat során további részleteket jelent a frissítésről, például a sikeres lépések számáról és a folyamatban lévő számról.

6. A teljes naplók letöltéséhez válassza a **Letöltés összegzése** lehetőséget a frissítés futtatása részletek panelen.

    Ha probléma merül fel a frissítés figyelése közben, a [privilegizált végpont](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint) segítségével figyelheti az Azure stack hub frissítési futtatásának állapotát. A rendszerjogosultságú végpontot használhatja arra is, hogy az utolsó sikeres lépés után folytassa a sikertelen frissítés futtatását, ha az Azure Stack hub-portál elérhetetlenné válik. Útmutatásért lásd: [frissítések figyelése Azure stack központban a PowerShell használatával](azure-stack-update-monitor.md).

    ![Azure Stack hub frissítési futtatásának részletei](./media/azure-stack-update-apply/image3.png)

7. Ha elkészült, a frissítési erőforrás-szolgáltató **sikeres** megerősítést biztosít annak bemutatásához, hogy a frissítési folyamat befejeződött, és mennyi ideig tartott. Innen megtekintheti az összes frissítés, elérhető frissítés vagy telepített frissítés adatait a szűrő használatával.

    ![Azure-stack-Update-Apply](./media/azure-stack-update-apply/image4.png)

    Ha a frissítés sikertelen, a **frissítési** panel jelentései **figyelmet igényelnek**. A **teljes naplók letöltése** lehetőséggel olyan magas szintű állapotot érhet el, ahol a frissítés nem sikerült. A Azure Stack hub log-gyűjtemény a diagnosztika és a hibaelhárítás terén nyújt segítséget.

## <a name="review-update-history"></a>Frissítési előzmények áttekintése

1. Nyissa meg a felügyeleti portált.

2. Válassza az **irányítópult**lehetőséget. Válassza a **Frissítés** lehetőséget.

3. Válassza a **frissítési előzmények**lehetőséget.

    ![Azure Stack hub frissítési előzményei](./media/azure-stack-update-apply/image7.png)

## <a name="next-steps"></a>Következő lépések

-   [Frissítések kezelése Azure Stack hub-ban – áttekintés](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)  
-   [Azure Stack hub karbantartási szabályzata](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy)  
