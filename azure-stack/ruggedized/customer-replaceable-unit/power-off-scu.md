---
title: Méretezési egység csomópontjainak kikapcsolása
description: Útmutató a méretezési egység csomópontjainak kikapcsolásához
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: fde7496a54f0c2d3b28d98584295a761c683e17c
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97391033"
---
# <a name="powering-off-scale-unit-nodes"></a>A skálázási egység csomópontjainak kikapcsolása

A méretezési egység csomópontjának kikapcsolásához ellenőriznie kell a méretezési egységek \' állapotát, és azonosítania kell a javítást igénylő csomópontot.

Ha a csomópont \' s **energiaellátási állapota** nem áll le, a csomópont biztonságos leállításához használja az alábbi eljárást.

**Lépések**

1.  Ürítse ki a méretezési egység csomópontot.

    1.  A felügyeleti portálon válassza ki a kijavításra szoruló csomópontot, majd válassza a **drain (kiürítés**) lehetőséget.

        ![](media/image-23.png)
        
    1.  Ha a rendszer kéri, adja meg a kiüríteni kívánt csomópont nevét, majd válassza az **Igen** lehetőséget.

        ![](media/image-24.png)
    
    1.  Ekkor megjelenik egy értesítés, amely azt jelzi, hogy a kiürítés folyamatban van.
    
        ![](media/image-25.png)
        
    1.  Jelentkezzen be a iDRAC felületére, és ellenőrizze a csomópont \' s szolgáltatás címkéjét a **Rendszerinformációban**.
    

2.  Állítsa le a méretezési egység csomópontot.

    1.  A kiürítés befejezése után válassza újra a csomópontot, ellenőrizze, hogy a **működési állapot** **karbantartásra** van-e állítva, majd válassza a **Leállítás** lehetőséget.

        ![](media/image-26.png)
        
    1.  Ha a rendszer kéri, válassza az **Igen** lehetőséget a Leállítás megerősítéséhez.
    
        ![](media/image-27.png)
        
    1.  Ekkor megjelenik egy értesítés, amely azt jelzi, hogy a Leállítás folyamatban van.

        ![](media/image-28.png)
    
    1.  A Leállítás befejezése után a **tápellátás állapota** **leáll**.
    
        ![](media/image-29.png)
        