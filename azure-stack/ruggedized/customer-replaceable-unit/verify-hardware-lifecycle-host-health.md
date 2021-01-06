---
title: A hardver életciklus-gazdagép állapotának ellenőrzése
description: Útmutató a hardveres életciklus-gazdagép állapotának ellenőrzéséhez
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 47d3d5198418042ad0bc24e35414bd309a492bc2
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909854"
---
# <a name="verifying-hardware-lifecycle-host-health"></a>A hardver életciklus-gazdagép állapotának ellenőrzése



Ha a hardver cseréje befejeződött, és mielőtt elhagyja a helyet, a iDRAC segítségével ellenőrizheti a hardver életciklus-állomásának állapotát.


1.  Ellenőrizze a rendszerállapotot a iDRAC webes felületén, és ellenőrizze, hogy az Ön által használt probléma törölve lett-e a webes felületen a cserélhető folyamat előtt.

    ![Képernyőkép, amely a "rendszer" oldalt jeleníti meg az "áttekintés" kijelölt műveletekkel.](media/image-5.png)
    
2.  Győződjön meg arról, hogy a Hyper-V virtuális gépek **futó** állapotban vannak.

    ![A "Virtual Machines" szakasszal jelölt "Hyper-V Manager" oldalt bemutató képernyőkép.](media/image-55.png) 

