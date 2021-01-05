---
title: A méretezési egység csomópont-hozzáférésének és állapotának ellenőrzése
description: Megtudhatja, hogyan ellenőrizheti a méretezési egység csomópont-hozzáférését és állapotát
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: c28d58d4416ab15885d687e9f0cbe7b6cdc8bdf8
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874455"
---
# <a name="verifying-scale-unit-node-access-and-health"></a>A méretezési egység csomópont-hozzáférésének és állapotának ellenőrzése



Jelentkezzen be az emelt szintű hozzáférési munkaállomásra, indítsa el a felügyeleti portált, ellenőrizze a rendszerállapotot, szerezze be a Kiemelt végpont IP-címeit, és azonosítsa, hogy mely csomópontokat kell kiüríteni vagy folytatni.

1.  A Távoli asztal használatával kapcsolódjon az emelt szintű hozzáférési munkaállomáshoz.

2.  Nyissa meg az Azure Stack hub felügyeleti portált.

    Jelentkezzen be az Azure Stack hub felügyeleti portálra az ügyféltől kapott hitelesítő adatokkal.
        
3.  Szerezze be a Kiemelt végpont IP-címeit.


    Válassza a **régió kezelése** csempét, és válassza a **Tulajdonságok** lehetőséget. Görgessen a panel aljára, és keresse meg az IP-címeket az emelt **szintű végpont IP-címei** mezőben. Ezeket az eljárás későbbi részében szükség lehet rájuk, vagy ha bármilyen probléma merül fel, támogatásra lesz szüksége.

    [![](media/image-18-inline.png)](media/image-18-expanded.png)
    
4.  Tekintse át az aktuális riasztásokat.

    A **régió kezelése** területen válassza a **riasztások** lehetőséget, és tekintse át az aktuális riasztásokat. Ha váratlan riasztások jelennek meg, ellenőrizze, hogy a Dell Technologies támogatja-e a törlést vagy a biztonság figyelmen kívül hagyása.
    
    [![](media/image-19-inline.png)](media/image-19-expanded.png)
    
5.  Azonosítsa a méretezési egység csomópontjait.

    Ha csak a szolgáltatási címkét adja meg, és nem tudja azonosítani, hogy melyik csomóponton van a probléma az Azure Stack hub felügyeleti portálján (ez a csomópont energiaellátási állapota már le van állítva), akkor a következő lépések végrehajtásával kapcsolja össze a méretezési egység csomópontját a szolgáltatási címkével:
    
    1.  A **régió kezelése** területen válassza a **méretezési egységek** lehetőséget, majd válassza ki a fürt **s-fürtöt**. Válassza ki a **csomópontokat**.
    
    1.  A Node szolgáltatás címkéjének beszerzéséhez válassza a **bmc** IP-cím hivatkozását, amely egy új lapon vagy ablakban nyitja meg a kiszolgáló iDRAC webes felületét.

        [![](media/image-20-inline.png)](media/image-20-expanded.png) 
    
    1.  Jelentkezzen be a iDRAC felületére, és ellenőrizze a csomópont szolgáltatási címkéjét a **rendszerinformációk** ablaktáblán.
    
    1.  Ismételje meg az eljárást minden csomópont esetében, és kapcsolja össze ezeket a szolgáltatási címkéket az ütemezett hardveres lecseréléssel annak megállapításához, hogy melyik csomópontot vagy csomópontokat kell szervizelni.

        [![](media/image-21-inline.png)](media/image-21-expanded.png)
    
