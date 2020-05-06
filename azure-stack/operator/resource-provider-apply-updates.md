---
title: Frissítések alkalmazása egy Azure Stack hub erőforrás-szolgáltatón.
description: Ismerje meg, hogyan alkalmazhatja a szolgáltatás frissítését Azure Stack hub erőforrás-szolgáltatóján.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/18/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/18/2019
ms.openlocfilehash: 5ec6f85fc2f2e526c4b85521337ab65dc2b939cc
ms.sourcegitcommit: 519f4298dc1ed5c33f9c4fef811f61d61731dd84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799866"
---
# <a name="how-to-update-an-azure-stack-hub-resource-provider"></a>Azure Stack hub erőforrás-szolgáltató frissítése

> [!IMPORTANT]
> A folytatás előtt tekintse át az erőforrás-szolgáltató legújabb kibocsátási megjegyzéseit, és ismerkedjen meg az új funkciókkal, javításokkal és az üzembe helyezést befolyásoló ismert problémákkal. A kibocsátási megjegyzések az erőforrás-szolgáltatóhoz szükséges minimális Azure Stack hub-verziót is megadhatják.

A piactérről telepített erőforrás-szolgáltatóknak rendszeres karbantartásra lesz szükségük. A karbantartást a Microsoft által rendszeresen biztosított szolgáltatási frissítések alkalmazásával végezheti el. A frissítések tartalmazhatnak új szolgáltatásokat és javításokat is.  

## <a name="check-for-updates"></a>Frissítések keresése

Az erőforrás-szolgáltatók ugyanazzal a frissítési funkcióval frissülnek, mint Azure Stack hub-frissítések alkalmazásához.

1. Jelentkezzen be az Azure Stack hub felügyeleti portálján.
2. Válassza a **minden szolgáltatás** hivatkozást a bal oldalon, majd a **felügyelet** szakaszban válassza a **frissítések**lehetőséget.
   ![Minden szolgáltatás lap](media/resource-provider-apply-updates/1-all-services.png)

3. A **frissítések** lapon megtalálhatja az erőforrás-szolgáltatók frissítéseit az erőforrás- **szolgáltató** szakaszban.

   [![Rendelkezésre álló oldal frissítése](media/resource-provider-apply-updates/3-update-available.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

## <a name="apply-an-update"></a>Frissítés alkalmazása

Ha van elérhető frissítés egy adott erőforrás-szolgáltatóhoz:

1. Válassza ki a frissíteni kívánt erőforrás-szolgáltató sorát. Figyelje meg, hogy a lap tetején található **letöltési** hivatkozás engedélyezve lesz.
   [![Rendelkezésre álló oldal frissítése](media/resource-provider-apply-updates/4-download.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

2. A **Letöltés** hivatkozásra kattintva megkezdheti az erőforrás-szolgáltató telepítési csomagjának letöltését. Figyelje meg, hogy az erőforrás-szolgáltatói sor **állapota** oszlopban az "elérhető" értékről a "Letöltés" értékre változik.
3. Ha az **állapot** "telepítésre kész" állapotúra változik, a letöltés befejeződött. Figyelje meg, hogy a lap tetején a **Telepítés most** hivatkozás is engedélyezve lesz.
4. Válassza a **Telepítés most** hivatkozást, és az erőforrás-szolgáltató **telepítési** lapjára kerül. 
5. A telepítés megkezdéséhez kattintson a **telepítés** gombra.
6. A "telepítés folyamatban" értesítés jelenik meg a jobb felső sarokban, és visszatér a **frissítések** oldalára. Az erőforrás-szolgáltató sor **állapota** oszlop a "telepítés" értékre is módosul.
7. A telepítés befejezésekor egy másik értesítés is jelzi a sikert vagy a hibát. A sikeres telepítés a **piactér-felügyeleti erőforrás-szolgáltatók** lapon is frissíti a **verziót** .

## <a name="next-steps"></a>További lépések

További információ a [rendszergazdai irányítópult frissítései szolgáltatásról](azure-stack-apply-updates.md).
