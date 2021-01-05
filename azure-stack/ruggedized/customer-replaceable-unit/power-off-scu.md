---
title: Méretezési egység csomópontjainak kikapcsolása
description: Útmutató a méretezési egység csomópontjainak kikapcsolásához
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 972c9e8f0b66470a6e1b7cbc81d40062ff34a950
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874710"
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
        