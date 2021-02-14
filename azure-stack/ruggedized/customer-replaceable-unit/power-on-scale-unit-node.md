---
title: Teljesítmény a méretezési egység csomópontján
description: Útmutató a méretezési egység csomópontjának bekapcsolásához
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 48df8bda75ef7438e2fe86f0d05f8b170167c441
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100488003"
---
# <a name="powering-on-a-scale-unit-node"></a>Skálázási egység csomópontjának bekapcsolása

**Lépések**

1.  Indítsa el a skálázási egység csomópontot.

    1.  Térjen vissza az **adminisztrációs portálra**, ha a csomópont továbbra is **leáll**, válassza ki a csomópontot, és válassza a **Start**: képernyőkép lehetőséget, ![ amely megjeleníti a "felügyeleti csomópontok" lapot egy csomóponttal, a "Start" műveletet pedig kiválasztva.](media/image-46.png)

    1.  Válassza az **Igen** lehetőséget a kezdési művelet megerősítéséhez:

        ![A "felügyeleti csomópontok" oldalt megjelenítő képernyőkép, amelyen megjelenik a "csomópont elindítása" párbeszédpanel.](media/image-47.png)
        
    1.  Figyelje meg a folyamat állapotát az értesítési panelen:
    
        ![Képernyőkép, amely az "adminisztráció – csomópontok" oldalt jeleníti meg az értesítési panelen látható folyamattal.](media/image-48.png)
            
    > [!NOTE]
    > A iDRAC virtuális konzol használatával ellenőrizheti, hogy a POST művelet során nincsenek-e problémák a felcserélt összetevővel.
    
2.  A skálázási egység csomópontjának folytatása

    1.  Most, hogy a csomópont sikeresen bekapcsolta a biztonsági mentést, a **felügyeleti portálon** válassza ki a csomópontot, és válassza a **Folytatás** lehetőséget.

        ![Képernyőkép, amely megjeleníti a "felügyeleti csomópontok" lapot egy csomóponttal, a "Folytatás" műveletet pedig kiválasztva.](media/image-49.png)
        
    1.  Válassza az **Igen** lehetőséget a folytatási művelet megerősítéséhez:
    
        ![Képernyőkép, amely az "adminisztráció – csomópontok" lapot jeleníti meg a "csomópont folytatása" párbeszédablakban.](media/image-50.png)
    
    1.  Figyelje meg a folyamat állapotát az értesítési panelen:
    
        ![Képernyőkép, amely az "adminisztráció – csomópontok" lapot mutatja az értesítési ablaktáblán látható csomópont előrehaladásával.](media/image-51.png)
        
