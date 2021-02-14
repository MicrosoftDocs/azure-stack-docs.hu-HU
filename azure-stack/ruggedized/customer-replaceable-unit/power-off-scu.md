---
title: Méretezési egység csomópontjainak kikapcsolása
description: Útmutató a méretezési egység csomópontjainak kikapcsolásához
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: f35c21320a90a68c7933e9fd0bf898496fda9398
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100488054"
---
# <a name="powering-off-scale-unit-nodes"></a>A skálázási egység csomópontjainak kikapcsolása

A méretezési egység csomópontjának kikapcsolásához ellenőriznie kell a méretezési egységek \' állapotát, és azonosítania kell a javítást igénylő csomópontot.

Ha a csomópont \' s **energiaellátási állapota** nem áll le, a csomópont biztonságos leállításához használja az alábbi eljárást.

**Lépések**

1.  Ürítse ki a méretezési egység csomópontot.

    1.  A felügyeleti portálon válassza ki a kijavításra szoruló csomópontot, majd válassza a **drain (kiürítés**) lehetőséget.

        ![Képernyőkép, amely megjeleníti az adminisztrációs oldalt a "drain" művelet kiválasztásával és egy kiemelt csomóponttal.](media/image-23.png)
        
    1.  Ha a rendszer kéri, adja meg a kiüríteni kívánt csomópont nevét, majd válassza az **Igen** lehetőséget.

        ![A "felügyeleti csomópontok" oldalt megjelenítő képernyőkép.](media/image-24.png)
    
    1.  Ekkor megjelenik egy értesítés, amely azt jelzi, hogy a kiürítés folyamatban van.
    
        ![Képernyőkép, amely megjeleníti a "felügyeleti csomópontok" lapot, amelyen megjelenik a kiürítési értesítés.](media/image-25.png)
        
    1.  Jelentkezzen be a iDRAC felületére, és ellenőrizze a csomópont \' s szolgáltatás címkéjét a **Rendszerinformációban**.
    

2.  Állítsa le a méretezési egység csomópontot.

    1.  A kiürítés befejezése után válassza újra a csomópontot, ellenőrizze, hogy a **működési állapot** **karbantartásra** van-e állítva, majd válassza a **Leállítás** lehetőséget.

        ![Képernyőkép, amely megjeleníti a "felügyeleti csomópontok" lapot a csomóponttal, a "Leállítás" műveletet pedig kiválasztva.](media/image-26.png)
        
    1.  Ha a rendszer kéri, válassza az **Igen** lehetőséget a Leállítás megerősítéséhez.
    
        ![A "felügyeleti csomópontok" oldalt megjelenítő képernyőkép a "leállítási csomópont" párbeszédablakban.](media/image-27.png)
        
    1.  Ekkor megjelenik egy értesítés, amely azt jelzi, hogy a Leállítás folyamatban van.

        ![Képernyőkép, amely megjeleníti a "felügyeleti csomópontok" lapot, amelyen megjelenik a Leállítás folyamatban párbeszédablak.](media/image-28.png)
    
    1.  A Leállítás befejezése után a **tápellátás állapota** **leáll**.
    
        ![Képernyőfelvétel: az "adminisztráció – csomópontok" oldal, amelyen a csomópont ki van választva, és a "leállítva" érték jelenik meg a "Power status" elemnél.](media/image-29.png)
        