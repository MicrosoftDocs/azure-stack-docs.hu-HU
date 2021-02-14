---
title: Méretezési egység csomópontjának bekapcsolása és javítása
description: Útmutató a méretezési egységek csomópontjainak bekapcsolásához és kijavításához
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 0a2aa54234b5dccc8f1ce3425906425064463910
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100488122"
---
# <a name="powering-on-and-repairing-a-scale-unit-node"></a>Méretezési egység csomópontjának bekapcsolása és javítása

**Lépések**

Ha vissza szeretné állítani a méretezési egység csomópontját az éles környezetbe, futtatnia kell az Azure Stack hub javítási eljárását.

> [!NOTE]
> A javítási művelet végrehajtása körülbelül három órát vesz igénybe.

1.  Az **adminisztrációs portálon** válassza ki a csomópontot, és válassza a **javítás** lehetőséget.

    ![Képernyőkép, amely megjeleníti a "felügyeleti csomópontok" lapot a csomóponttal és a kiválasztva a "javítás" műveletet.](media/image-52.png)

1.  Adja meg a kijavítani kívánt csomópontnak megfelelő **bmc IP-címet** , és válassza a **javítás** lehetőséget.

    ![Képernyőkép, amely megjeleníti a "felügyeleti csomópontok" lapot, amelyen egy csomópont van kiválasztva, az I P-címe kiemelve és a "csomópont javítása" párbeszédablak jelenik meg.](media/image-53.png)

1.  Figyelje meg a folyamat állapotát az értesítési panelen:

    ![Képernyőkép, amely megjeleníti az "értesítések" panelt és a "csomópont futtatásának javítása" feliratot.](media/image-54.png)
    
    
    > [!NOTE]
    > Ha a javítási eljárás három órán belül nem fejeződik be, vagy probléma merül fel, a további hibaelhárítás érdekében nyisson meg egy esetet Microsoft ügyfélszolgálata.
    
    A javítási folyamat befejezése után a csomópont egy **futó működési állapotba** tér vissza.
    
