---
title: Méretezési egység csomópontjának bekapcsolása és javítása
description: Útmutató a méretezési egységek csomópontjainak bekapcsolásához és kijavításához
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: e5ba7b7ea35484293cc0f93958e7ed9d66f62d51
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867876"
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
    
