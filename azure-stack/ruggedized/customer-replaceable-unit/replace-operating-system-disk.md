---
title: Operációs rendszer lemezének cseréje
description: Útmutató az operációs rendszer lemezének lecseréléséhez
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 24447611a4b3c775b92f4d163bb250060e29417e
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487833"
---
# <a name="replacing-an-operating-system-disk"></a>Operációs rendszer lemezének cseréje

A következő eljárással lecserélheti az operációs rendszer hibás lemezét egy méretezési egység csomópontjában.

## <a name="prerequisites"></a>Előfeltételek

1.  Az útmutató elején tekintse át a *megjegyzéseket, a figyelmeztetéseket és a figyelmeztetéseket* .

2.  Tekintse át a kezelési óvintézkedéseket.

3.  A skálázási egység csomópontjainak az Azure Stack hub-ban való használatának szükséges ismerete robusztus, ha a méretezési egység csomópontja működik.

4.  Fejezze be a méretezési egység csomópont-hozzáférésének és állapotának ellenőrzését.

5.  Fejezze be a skálázási egység csomópontjainak kikapcsolását.

    Az Azure Stack hub skálázási egység csomópontjai esetében az operációs rendszer a rendszerindításra optimalizált tárolási megoldás (BOSS) kártyán található, M. 2 SSD-modulok tükrözött párja alapján fut. Az operációs rendszer lemezének cseréjéhez ki kell kapcsolni a rendszert.
    
## <a name="steps"></a>Lépések

1.  Keresse meg a fizikai csomópontot a rackben.

2.  Győződjön meg arról, hogy a csomópont ki van kapcsolva. A Power LED-nek narancssárganak kell lennie.

    > [!CAUTION]
    > Mielőtt leválasztja azokat a kiszolgálókat, amelyeken dolgozik, győződjön meg arról, hogy az egyes kábelek megfelelő címkével rendelkeznek. A kábeleket újra csatlakoztatni kell ugyanahhoz a porthoz.
    
3.  Cserélje le a sikertelen M. 2 SSD-modult.

    Kövesse az [M. 2 SSD-modul helyettesítési folyamatát](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) a skálázási egység csomópontjaihoz.
    
4.  Kapcsolja be a csomópontot.

    Ha a hálózat újrakapcsolódása után a kiszolgáló nem indul el automatikusan, nyomja meg a főkapcsoló gombot a csomópont újbóli bekapcsolásához.
    
## <a name="next-steps"></a>Következő lépések

1.  Egy méretezési egység csomópontjának bekapcsolása és javítása.

2.  Fejezze be a skálázási egység állapotának ellenőrzését.

