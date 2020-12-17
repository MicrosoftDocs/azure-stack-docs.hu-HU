---
title: Azure Stack hub frissítéseinek telepítése
description: Ismerje meg, hogyan telepítheti Azure Stack hub-frissítéseket.
author: sethmanheim
ms.topic: how-to
ms.date: 12/16/2020
ms.author: sethm
ms.lastreviewed: 09/10/2019
ms.reviewer: sranthar
ms.openlocfilehash: a54e47ff5788ca100e45be103e4d3d5192766f3b
ms.sourcegitcommit: f30e5178e0b4be4e3886f4e9f699a2b51286e2a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97620787"
---
# <a name="install-azure-stack-hub-updates"></a>Azure Stack hub frissítéseinek telepítése

A frissítési csomagokat a Azure Stack hub felügyeleti portáljának **Update (frissítés** ) paneljén is telepítheti. Ez a cikk a frissítési folyamat frissítésének, figyelésének és hibaelhárításának lépéseit ismerteti. A frissítés panel használatával megtekintheti **a frissítési** adatokat, telepítheti a frissítéseket, figyelheti a frissítések állapotát, áttekintheti az előzményeket, és megtekintheti az aktuális Azure stack hub és OEM csomag verzióját.

A frissítéseket a felügyeleti portálról kezelheti, és az irányítópult **frissítésekkel** foglalkozó szakaszában a következőket végezheti el:

- Tekintse meg a fontos információkat, például az aktuális verziót.
- Frissítések telepítése és a figyelési folyamat.
- Tekintse át a korábban telepített frissítések korábbi frissítéseit.
- Tekintse meg a felhő jelenlegi OEM-csomagjának verzióját.

## <a name="determine-the-current-version"></a>Az aktuális verzió meghatározása

Az Azure Stack hub aktuális verzióját a **frissítések** panelen tekintheti meg. Megnyitás:

1. Nyissa meg az Azure Stack hub felügyeleti portált.

2. Válassza az **irányítópult** lehetőséget. A **frissítések** ablaktáblán az aktuális verzió szerepel a felsorolásban:

    ![Az alapértelmezett irányítópulton lévő frissítések csempe](./media/azure-stack-apply-updates/dashboard.png)

## <a name="install-updates-and-monitor-progress"></a>Frissítések telepítése és a folyamat megfigyelése

> [!IMPORTANT]
> Az Azure Stack hub frissítéseinek alkalmazása előtt győződjön meg róla, hogy végrehajtotta a [frissítés előtti ellenőrzőlista](release-notes-checklist.md) összes lépését, és ütemezte a megfelelő karbantartási időszakot az alkalmazott frissítési típushoz.

1. Nyissa meg az Azure Stack hub felügyeleti portált.

2. Válassza az **irányítópult** lehetőséget. Válassza a **Frissítés** lehetőséget.

3. Válassza ki az elérhető frissítést, amelyet telepíteni szeretne. Ha nincs **elérhetőként** megjelölt frissítés, [készítse elő a frissítési csomagot](azure-stack-update-prepare-package.md).

4. Válassza a **Frissítés most** lehetőséget.

    ![Képernyőkép, amely bemutatja, hogyan indíthatja el a frissítést Azure Stack hub-ban.](./media/azure-stack-apply-updates/image2.png)

5. Megtekintheti a magas szintű állapotot, mivel a frissítési folyamat megismétli a Azure Stack hub különböző alrendszerein keresztül. Az alrendszerek például olyan fizikai gazdagépek, Service Fabric, infrastruktúra-virtuális gépek és szolgáltatások, amelyek mind a rendszergazda, mind a felhasználói portált biztosítják. A frissítés erőforrás-szolgáltatója a frissítési folyamat során további részleteket jelent a frissítésről, például a sikeres lépések számáról és a folyamatban lévő számról.

6. A teljes naplók letöltéséhez válassza a **Letöltés összegzése** lehetőséget a frissítés futtatása részletek panelen.

    Ha probléma merül fel a frissítés figyelése közben, a [Kiemelt végpont](./azure-stack-privileged-endpoint.md) segítségével figyelheti az Azure stack hub frissítési futtatásának állapotát. A rendszerjogosultságú végpontot használhatja arra is, hogy az utolsó sikeres lépés után folytassa a sikertelen frissítés futtatását, ha az Azure Stack hub-portál elérhetetlenné válik. Útmutatásért lásd: [frissítések figyelése Azure stack központban a PowerShell használatával](azure-stack-update-monitor.md).

    ![Azure Stack hub frissítési futtatásának részletei](./media/azure-stack-apply-updates/image3.png)

7. Ha elkészült, a frissítési erőforrás-szolgáltató **sikeres** megerősítő üzenetet jelenít meg, amely azt mutatja, hogy a frissítési folyamat befejeződött, és mennyi ideig tartott. Innen megtekintheti az összes frissítés, elérhető frissítés vagy telepített frissítés adatait a szűrő használatával.

    ![Azure-stack-Update-Apply](./media/azure-stack-apply-updates/image4.png)

    Ha a frissítés sikertelen, a **frissítési** panel jelentései **figyelmet igényelnek**. A **teljes naplók letöltése** lehetőséggel olyan magas szintű állapotot érhet el, ahol a frissítés nem sikerült. Azure Stack hub-naplók gyűjtése a diagnosztika és a hibaelhárítás terén nyújt segítséget.

## <a name="review-update-history"></a>Frissítési előzmények áttekintése

1. Nyissa meg a felügyeleti portált.

2. Válassza az **irányítópult** lehetőséget. Válassza a **Frissítés** lehetőséget.

3. Válassza a **frissítési előzmények** lehetőséget.

    ![Azure Stack hub frissítési előzményei](./media/azure-stack-apply-updates/image5.png)

## <a name="next-steps"></a>További lépések

- [Frissítések kezelése az Azure Stack Hubban – áttekintés](./azure-stack-updates.md)  
- [Azure Stack hub karbantartási szabályzata](./azure-stack-servicing-policy.md)  
