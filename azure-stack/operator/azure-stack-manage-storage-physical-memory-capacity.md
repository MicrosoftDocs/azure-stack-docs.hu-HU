---
title: Fizikai memória kapacitásának kezelése Azure Stack hub-ban
description: Megtudhatja, hogyan figyelheti és kezelheti Azure Stack hub fizikai memóriáját és kapacitását.
author: PatAltimore
ms.topic: how-to
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 82aa7c6bd9295d5727ca7a37fa6798d2bf352887
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870188"
---
# <a name="manage-physical-memory-capacity-in-azure-stack-hub"></a>Fizikai memória kapacitásának kezelése Azure Stack hub-ban

Azure Stack hub teljes rendelkezésre álló memória-kapacitásának növeléséhez további memóriát adhat hozzá. Azure Stack hub-ban a fizikai kiszolgálót a *skálázási egység csomópontjának* is nevezzük. Az ugyanahhoz a skálázási egységhez tartozó skálázásiegység-csomópontokhoz azonos mennyiségű memóriát kell hozzárendelni.

> [!note]  
> A folytatás előtt tekintse meg a hardver gyártójától származó dokumentációt, és ellenőrizze, hogy a gyártója támogatja-e a fizikai memória frissítését. Előfordulhat, hogy az OEM hardveres gyártó támogatási szerződése megköveteli, hogy a szállító végrehajtsa a fizikai kiszolgáló rack elhelyezését és az eszköz belső vezérlőprogram-frissítését.

A következő folyamatábrában látható az általános folyamat, amellyel memóriát adhat hozzá az egyes skálázási egységek csomópontjaihoz.

![Memória hozzáadásának folyamata az egyes skálázási egységek csomópontjaihoz](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Memória hozzáadása egy meglévő csomóponthoz
A következő lépések áttekintést nyújtanak a memória hozzáadásának folyamatáról.

> [!Warning]
> Ne kövesse ezeket a lépéseket a SZÁMÍTÓGÉPGYÁRTÓ által biztosított dokumentációra való hivatkozás nélkül.
> 
> [!Warning]
> A teljes méretezési egységet le kell állítani, mivel a működés közbeni memória frissítése nem támogatott.

1. Állítsa le Azure Stack hubot az [Azure stack hub elindítása és leállítása](azure-stack-start-and-stop.md) című cikkben ismertetett lépések alapján.
2. Frissítse a memóriát az egyes fizikai számítógépeken a hardver gyártójától származó dokumentáció használatával.
3. Indítsa el Azure Stack hubot az [Azure stack hub elindítása és leállítása](azure-stack-start-and-stop.md) című cikk lépéseivel.

## <a name="next-steps"></a>További lépések

 - A Azure Stack hub Storage-fiókjainak kezelésével kapcsolatos további információkért lásd: [Storage-fiókok kezelése Azure stack hub-ban](azure-stack-manage-storage-accounts.md).
 - Az Azure Stack hub üzemelő példányának tárolási kapacitásának figyeléséről és kezeléséről további információt a [Azure stack hub tárolókapacitásának kezelése](azure-stack-manage-storage-shares.md)című témakörben talál.
