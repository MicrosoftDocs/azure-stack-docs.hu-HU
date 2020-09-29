---
title: Azure Stack hub frissítéseinek telepítése
description: Ismerje meg, hogyan telepítheti Azure Stack hub-frissítéseket.
author: sethmanheim
ms.topic: how-to
ms.date: 07/22/2020
ms.author: sethm
ms.lastreviewed: 09/10/2019
ms.reviewer: sranthar
ms.openlocfilehash: d0222de6db809fcbb73d31ac1ef4298e18895643
ms.sourcegitcommit: 53b0dde60a6435936a5e0cb9e931245f262d637a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91106570"
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

2. Válassza az **irányítópult**lehetőséget. A **frissítések** panelen az aktuális verzió szerepel a felsorolásban. Ebben a képen például a verzió a következő: 1.1903.0.35:

    ![Az alapértelmezett irányítópulton lévő frissítések csempe](./media/azure-stack-update-apply/image1.png)

## <a name="install-updates-and-monitor-progress"></a>Frissítések telepítése és a folyamat megfigyelése

> [!IMPORTANT]
> Az Azure Stack hub frissítéseinek alkalmazása előtt győződjön meg róla, hogy végrehajtotta a [frissítés előtti ellenőrzőlista](release-notes-checklist.md) összes lépését, és ütemezte a megfelelő karbantartási időszakot az alkalmazott frissítési típushoz.

1. Nyissa meg az Azure Stack hub felügyeleti portált.

2. Válassza az **irányítópult**lehetőséget. Válassza a **Frissítés** lehetőséget.

3. Válassza ki az elérhető frissítést, amelyet telepíteni kíván. Ha nincs **elérhetőként**megjelölt frissítés, [készítse elő a frissítési csomagot](azure-stack-update-prepare-package.md).

4. Válassza a **Frissítés most**lehetőséget.

    ![Képernyőkép, amely bemutatja, hogyan indíthatja el a frissítést Azure Stack hub-ban.](./media/azure-stack-update-apply/image2.png)

5. Megtekintheti a magas szintű állapotot, mivel a frissítési folyamat megismétli a Azure Stack hub különböző alrendszerein keresztül. Az alrendszerek például olyan fizikai gazdagépek, Service Fabric, infrastruktúra-virtuális gépek és szolgáltatások, amelyek mind a rendszergazda, mind a felhasználói portált biztosítják. A frissítés erőforrás-szolgáltatója a frissítési folyamat során további részleteket jelent a frissítésről, például a sikeres lépések számáról és a folyamatban lévő számról.

6. A teljes naplók letöltéséhez válassza a **Letöltés összegzése** lehetőséget a frissítés futtatása részletek panelen.

    Ha probléma merül fel a frissítés figyelése közben, a [Kiemelt végpont](./azure-stack-privileged-endpoint.md) segítségével figyelheti az Azure stack hub frissítési futtatásának állapotát. A rendszerjogosultságú végpontot használhatja arra is, hogy az utolsó sikeres lépés után folytassa a sikertelen frissítés futtatását, ha az Azure Stack hub-portál elérhetetlenné válik. Útmutatásért lásd: [frissítések figyelése Azure stack központban a PowerShell használatával](azure-stack-update-monitor.md).

    ![Azure Stack hub frissítési futtatásának részletei](./media/azure-stack-update-apply/image3.png)

7. Ha elkészült, a frissítési erőforrás-szolgáltató **sikeres** megerősítő üzenetet jelenít meg, amely azt mutatja, hogy a frissítési folyamat befejeződött, és mennyi ideig tartott. Innen megtekintheti az összes frissítés, elérhető frissítés vagy telepített frissítés adatait a szűrő használatával.

    ![Azure-stack-Update-Apply](./media/azure-stack-update-apply/image4.png)

    Ha a frissítés sikertelen, a **frissítési** panel jelentései **figyelmet igényelnek**. A **teljes naplók letöltése** lehetőséggel olyan magas szintű állapotot érhet el, ahol a frissítés nem sikerült. Azure Stack hub-naplók gyűjtése a diagnosztika és a hibaelhárítás terén nyújt segítséget.

## <a name="review-update-history"></a>Frissítési előzmények áttekintése

1. Nyissa meg a felügyeleti portált.

2. Válassza az **irányítópult**lehetőséget. Válassza a **Frissítés** lehetőséget.

3. Válassza a **frissítési előzmények**lehetőséget.

    ![Azure Stack hub frissítési előzményei](./media/azure-stack-update-apply/image7.png)

## <a name="next-steps"></a>Következő lépések

- [Frissítések kezelése az Azure Stack Hubban – áttekintés](./azure-stack-updates.md)  
- [Azure Stack hub karbantartási szabályzata](./azure-stack-servicing-policy.md)  
