---
title: A hardveres életciklus-gazdagép hozzáférésének és állapotának ellenőrzése
description: Ismerje meg, hogyan ellenőrizheti a hardveres életciklus-gazdagépek hozzáférését és állapotát
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: b14bff53b8c8d5545a00424a543656a190c06bf3
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97391312"
---
# <a name="verifying-hardware-lifecycle-host-access-and-health"></a>A hardveres életciklus-gazdagép hozzáférésének és állapotának ellenőrzése

Jelentkezzen be a iDRAC és a hardver életciklus-gazdagép (HLH) operációs rendszerbe, és ellenőrizze a rendszerállapotot.

1.  Kapcsolódjon a iDRAC.

    1.  Webböngészővel nyissa meg a iDRAC webes felületét, és jelentkezzen be az ügyfél által megadott hitelesítő adatok használatával.

        ![](media/image-3.png) 
    
    1.  A felső navigációs menüben válassza a **rendszer** elemet.

        ![](media/image-4.png)
        
    1.  Az **Áttekintés** lapon ellenőrizze, hogy a rendszer teljesen kifogástalan állapotú-e, vagy azt a problémát jelzi, hogy a hardver cseréje során szervizelésre vár.
    
        ![](media/image-5.png)
    
2.  Kapcsolódjon a HLH operációs rendszerhez a iDRAC virtuális konzol használatával.

    > [!NOTE]
    > Ezt a lépést kihagyhatja, ha egy összeomlási kosárral jelentkezik be VGA-és USB-kapcsolattal.
    
    1.  A iDRAC webes felületén válassza az **irányítópult** lehetőséget.

        ![](media/image-6.png)
    
    1.  A **virtuális konzol** ablaktáblán válassza a **Beállítások** lehetőséget.
    
        ![](media/image-7.png)
        
    1.  Győződjön meg arról, hogy a **beépülő modul típusa** a **HTML 5** értékre van beállítva. Ha nem, módosítsa ezt a beállítást, majd kattintson az **alkalmaz**, majd **az OK gombra** , amikor a rendszer kéri.
    
        ![](media/image-8.png)
        
    1.  Válassza a **virtuális konzol indítása** lehetőséget.

        ![](media/image-9.png)
    
    1.  Ha megjelenik egy előugró figyelmeztetés, módosítsa a böngésző beállításait a mindig engedélyezett értékre. Például az Internet Explorerben válassza a **hely beállításai** lehetőséget, és válassza a **mindig engedélyezés** lehetőséget. Ha szükséges, a böngésző beállításainak módosítása után ismételje meg az előző lépést a virtuális konzol elindításához.
    
        ![](media/image-10.png)
        
    1.  A virtuális konzolnak most már jelen kell lennie. Az operációs rendszerbe való bejelentkezéshez a felső sarokban válassza ki a **konzol vezérlőelemeket**.
    
        ![](media/image-11.png)
        
    1.  Válassza ki a **billentyűzet makrót**, **Ctrl + Alt + Del** billentyűkombinációt, majd kattintson az **alkalmaz** , majd a **Bezárás** elemre.
    
        ![](media/image-12.png)
        
    1.  Válassza ki a **felhasználót** az ügyfél által megadott hitelesítő adatok alapján, írja be a jelszót, és válassza ki a bejelentkezni kívánt **nyilat** .
    
        ![](media/image-13.png)
        
        Most már bejelentkezett a HLH.
        
3.  Ellenőrizze az állapotot.

    1.  Indítsa el a **Kiszolgálókezelő alkalmazást**.

        ![](media/image-14.png)
        
    1.  Válassza az **Igen** lehetőséget a **felhasználói fiókok felügyelete** kérdésnél.
    
        ![](media/image-15.png)
        
    1.  A **Tools (eszközök** ) menüben válassza a **Hyper-V kezelője** elemet.
    
        ![](media/image-16.png)
        
    1.  A **Hyper-V kezelőjében** válassza ki a bal oldali menüben a legfelső csomópontot, majd ellenőrizze, hogy a virtuális gépek, például az emelt **szintű hozzáférési munkaállomás**(ha van) **futó** állapotban van-e.