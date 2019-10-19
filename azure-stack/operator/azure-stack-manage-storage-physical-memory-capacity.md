---
title: A fizikai memória kapacitásának kezelése a Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan figyelheti és kezelheti Azure Stack a fizikai memóriát és a kapacitást.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: f72ad1b86bf8ef84e64f79603c27d14571b00838
ms.sourcegitcommit: b5eb024d170f12e51cc852aa2c72eabf26792d8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72534153"
---
# <a name="manage-physical-memory-capacity-in-azure-stack"></a>Fizikai memória kapacitásának kezelése a Azure Stackban

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

A rendelkezésre álló memória teljes kapacitásának növeléséhez Azure Stack több memóriát is hozzáadhat. Azure Stack a fizikai kiszolgálót a *méretezési egység csomópontjának*is nevezzük. Az egyetlen méretezési egységhez tartozó összes skálázási egység csomópontjainak azonos mennyiségű memóriával kell rendelkezniük.

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

1. Azure Stack leállítása a [Azure stack elindítása és leállítása](azure-stack-start-and-stop.md) című cikkben ismertetett lépések segítségével.
2. Frissítse a memóriát az egyes fizikai számítógépeken a hardver gyártójától származó dokumentáció használatával.
3. Indítsa el Azure Stack a [Azure stack elindítása és leállítása](azure-stack-start-and-stop.md) című cikkben ismertetett lépések alapján.

## <a name="next-steps"></a>Következő lépések

 - A Azure Stack lévő Storage-fiókok kezelésével kapcsolatos további információkért lásd: [a Storage-fiókok kezelése a Azure Stackban](azure-stack-manage-storage-accounts.md).
 - A Azure Stack üzemelő példány tárolási kapacitásának figyelésével és kezelésével kapcsolatos további információkért lásd: [Azure stack tárolókapacitásának kezelése](azure-stack-manage-storage-shares.md).
