---
title: DIMM cseréje
description: Tudnivalók a DIMM lecseréléséről
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: f20cdf77cb5ed3b63bcbd9b57770039efb6d29e5
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487935"
---
# <a name="replacing-a-dimm"></a>A DIMM cseréje

A DIMM cseréjéhez kövesse az alábbi eljárást.

## <a name="prerequisites"></a>Előfeltételek

1.  Az útmutató elején tekintse át a *megjegyzéseket, a figyelmeztetéseket és a figyelmeztetéseket*

2.  Tekintse át a kezelési óvintézkedéseket.

3.  Áttekintés

    -   Szükséges ismeretek a méretezési egység csomópontjainak a robusztus felhőalapú berendezésben való használatához, ha a méretezési egység csomópontja működik

    -   Szükséges ismeretek a hardveres életciklus-gazdagép használatáról, ha a hardveres életciklus-gazdagépet dolgozik

4.  Kész

    -   A méretezési egység csomópont-hozzáférésének és állapotának ellenőrzése, ha a méretezési egység csomópontja működik

    -   A hardveres életciklus-gazdagép hozzáférésének és állapotának ellenőrzése, ha együttműködik a hardver életciklus-Gazdagépével

5.  Kész

    -   A skálázási egység csomópontjainak kikapcsolása, ha a méretezési egység csomópontja működik

    -   A hardveres életciklus-gazdagép kikapcsolása, ha a hardveres életciklus-gazdagépen dolgozik

## <a name="steps"></a>Lépések

1.  Keresse meg a fizikai csomópontot a rackben.

2.  Győződjön meg arról, hogy a csomópont ki van kapcsolva. A Power LED-nek narancssárganak kell lennie.

    > [!CAUTION]
    > Mielőtt leválasztja azokat a kiszolgálókat, amelyeken dolgozik, győződjön meg arról, hogy az egyes kábelek megfelelő címkével rendelkeznek. A kábeleket újra csatlakoztatni kell ugyanahhoz a porthoz.
    
3.  Cserélje le a DIMM vagy a DIMM.

    A skálázási egység csomópontjai vagy a hardveres életciklus-gazdagépek esetében kövesse a [memóriamodul cseréjének folyamatát](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) .
    
4.  Kapcsolja be a csomópontot.

    Ha a hálózat újrakapcsolódása után a kiszolgáló nem indul el automatikusan, nyomja meg a főkapcsoló gombot a csomópont újbóli bekapcsolásához.
    
## <a name="next-steps"></a>Következő lépések

Ha a méretezési egység csomópontot dolgozik:

1.  Fejezze be a méretezési egység csomópontjának bekapcsolását.

2.  Fejezze be a skálázási egység állapotának ellenőrzését. Ha a hardveres életciklus-Gazdagéptel dolgozik, fejezze be a hardver életciklus-gazdagép állapotának ellenőrzését.
    
