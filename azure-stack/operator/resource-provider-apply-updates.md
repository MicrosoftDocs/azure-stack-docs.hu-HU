---
title: Frissítések alkalmazása egy Azure Stack hub erőforrás-szolgáltatón
description: Ismerje meg, hogyan alkalmazhatja a szolgáltatás frissítését Azure Stack hub erőforrás-szolgáltatóján.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/18/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/18/2019
ms.openlocfilehash: a51a0f53b6df1ec88623f274dec30a68bfaa47c0
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100563039"
---
# <a name="how-to-update-an-azure-stack-hub-resource-provider"></a>Azure Stack hub erőforrás-szolgáltató frissítése

> [!IMPORTANT]
> A folytatás előtt tekintse át az erőforrás-szolgáltató legújabb kibocsátási megjegyzéseit, és ismerkedjen meg az új funkciókkal, javításokkal és az üzembe helyezést befolyásoló ismert problémákkal. A kibocsátási megjegyzések az erőforrás-szolgáltatóhoz szükséges minimális Azure Stack hub-verziót is megadhatják. Ha korábban még nem telepítette az erőforrás-szolgáltatót, az erőforrás-szolgáltató előfeltételeit és a kezdeti telepítési utasításokat itt találja.

A piactérről telepített erőforrás-szolgáltatóknak rendszeres karbantartásra lesz szükségük. A karbantartást a Microsoft által rendszeresen biztosított szolgáltatási frissítések alkalmazásával végezheti el. A frissítések tartalmazhatnak új szolgáltatásokat és javításokat is.  

## <a name="check-for-updates"></a>Frissítések keresése

Az erőforrás-szolgáltatók ugyanazzal a frissítési funkcióval frissülnek, mint Azure Stack hub-frissítések alkalmazásához.

1. Jelentkezzen be az Azure Stack Hub felügyeleti portálra.
2. Válassza a **minden szolgáltatás** hivatkozást a bal oldalon, majd a **felügyelet** szakaszban válassza a **frissítések** lehetőséget.
   ![Minden szolgáltatás lap](media/resource-provider-apply-updates/1-all-services.png)

3. A **frissítések** lapon megtalálhatja az erőforrás-szolgáltatók frissítéseit az erőforrás- **szolgáltató** szakaszban.

   [![Az erőforrás-szolgáltató szakaszt bemutató képernyőkép.](media/resource-provider-apply-updates/3-update-available.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

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

## <a name="next-steps"></a>Következő lépések

További információ a [rendszergazdai irányítópult frissítései szolgáltatásról](azure-stack-apply-updates.md).
