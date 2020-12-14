---
title: Méretezési egység csomópontjának bekapcsolása és javítása
description: Útmutató a méretezési egységek csomópontjainak bekapcsolásához és kijavításához
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 255a40bc2439ae1a6995d4ddf89df192d5e57551
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97391397"
---
# <a name="powering-on-and-repairing-a-scale-unit-node"></a>Méretezési egység csomópontjának bekapcsolása és javítása

**Lépések**

Ha vissza szeretné állítani a méretezési egység csomópontját az éles környezetbe, futtatnia kell az Azure Stack hub javítási eljárását.

> [!NOTE]
> A javítási művelet végrehajtása körülbelül három órát vesz igénybe.

1.  Az **adminisztrációs portálon** válassza ki a csomópontot, és válassza a **javítás** lehetőséget.

    ![](media/image-52.png)

1.  Adja meg a kijavítani kívánt csomópontnak megfelelő **bmc IP-címet** , és válassza a **javítás** lehetőséget.

    ![](media/image-53.png)

1.  Figyelje meg a folyamat állapotát az értesítési panelen:

    ![](media/image-54.png)
    
    
    > [!NOTE]
    > Ha a javítási eljárás három órán belül nem fejeződik be, vagy probléma merül fel, nyisson meg egy esetet a Dell Technologies támogatásával, aki további hibaelhárításhoz is felvehet Microsoft ügyfélszolgálata.
    
    A javítási folyamat befejezése után a csomópont egy **futó működési állapotba** tér vissza.
    
