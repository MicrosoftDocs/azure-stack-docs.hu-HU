---
title: Frissítések alkalmazása egy Azure Stack hub erőforrás-szolgáltatón
description: Ismerje meg, hogyan alkalmazhatja a szolgáltatás frissítését Azure Stack hub erőforrás-szolgáltatóján.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 03/01/2021
ms.reviewer: jfggdl
ms.lastreviewed: 03/01/2021
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 60f273603d03c6625addcfeba345a198f82fbe31
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117005"
---
# <a name="how-to-update-an-azure-stack-hub-resource-provider"></a>Azure Stack hub erőforrás-szolgáltató frissítése

> [!IMPORTANT]
> A folytatás előtt tekintse át az erőforrás-szolgáltató legújabb kibocsátási megjegyzéseit, és ismerkedjen meg az új funkciókkal, javításokkal és az üzembe helyezést befolyásoló ismert problémákkal. A kibocsátási megjegyzések az erőforrás-szolgáltatóhoz szükséges minimális Azure Stack hub-verziót is megadhatják. Ha korábban még nem telepítette az erőforrás-szolgáltatót, az erőforrás-szolgáltató előfeltételeit és a kezdeti telepítési utasításokat itt találja.

A piactérről telepített erőforrás-szolgáltatóknak rendszeres karbantartásra van szükségük. A karbantartást a Microsoft által rendszeresen biztosított szolgáltatási frissítések alkalmazásával végezheti el. A frissítések tartalmazhatnak új szolgáltatásokat és javításokat is.  

## <a name="check-for-updates"></a>Frissítések keresése

Az erőforrás-szolgáltatók ugyanazzal a frissítési funkcióval frissülnek, mint Azure Stack hub-frissítések alkalmazásához.

1. Jelentkezzen be az Azure Stack Hub felügyeleti portálra.
2. Válassza a **minden szolgáltatás** hivatkozást a bal oldalon, majd a **felügyelet** szakaszban válassza a **frissítések** lehetőséget.
   [![Minden szolgáltatás lap](media/resource-provider-apply-updates/1-all-services.png)](media/resource-provider-apply-updates/1-all-services.png#lightbox)

3. A **frissítések** lapon az **erőforrás-szolgáltató** szakaszban található erőforrás-szolgáltatók frissítéseit találhatja meg, az "elérhető" **állapotot jelző állapottal** .

   [![Az erőforrás-szolgáltató szakaszt bemutató képernyőkép.](media/resource-provider-apply-updates/3-update-available.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

## <a name="download-package"></a>Csomag letöltése

Ha van elérhető frissítés egy adott erőforrás-szolgáltatóhoz:

::: zone pivot="state-connected"
1. A **frissítések** lap **erőforrás-szolgáltató** szakaszában válassza ki a frissíteni kívánt erőforrás-szolgáltató sorát. Figyelje meg, hogy a lap tetején található **letöltési** hivatkozás engedélyezve lesz.
   [![Rendelkezésre álló oldal frissítése](media/resource-provider-apply-updates/4-download.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

2. A **Letöltés** hivatkozásra kattintva megkezdheti az erőforrás-szolgáltató telepítési csomagjának letöltését. Figyelje meg, hogy az erőforrás-szolgáltatói sor **állapota** oszlopban az "elérhető" értékről a "Letöltés" értékre változik.
3. Ha az **állapot** "telepítésre kész" állapotúra változik, a letöltés befejeződött. 
::: zone-end

::: zone pivot="state-disconnected" 
[!INCLUDE [prereqs](../includes/resource-provider-va-package-download-disconnected.md)]
::: zone-end

## <a name="apply-an-update"></a>Frissítés alkalmazása

Miután letöltötte az erőforrás-szolgáltatói csomagot, térjen vissza a **frissítések** oldal **erőforrás-szolgáltató** szakaszába:

1. Válassza ki a frissíteni kívánt erőforrás-szolgáltató sorát. Ekkor a "telepítésre kész" **állapot** jelenik meg, és a lap tetején lévő **install (telepítés** ) hivatkozás lesz engedélyezve.
2. Válassza a **Telepítés most** hivatkozást, és az erőforrás-szolgáltató **telepítési** lapjára kerül. 
3. A telepítés megkezdéséhez kattintson a **telepítés** gombra.
4. A "telepítés folyamatban" értesítés jelenik meg a jobb felső sarokban, és visszatér a **frissítések** oldalára. Az erőforrás-szolgáltató sor **állapota** oszlop a "telepítés" értékre is módosul.
5. A telepítés befejezésekor egy másik értesítés is jelzi a sikert vagy a hibát. A sikeres telepítés a **piactér-felügyeleti erőforrás-szolgáltatók** lapon is frissíti a **verziót** .

## <a name="next-steps"></a>Következő lépések

További információ a [rendszergazdai irányítópult frissítései szolgáltatásról](azure-stack-apply-updates.md).
