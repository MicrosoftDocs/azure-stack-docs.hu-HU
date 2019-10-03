---
title: A Azure Stack fizikai memória kapacitásának kezelése | Microsoft Docs
description: Azure Stack számára elérhető tárterület figyelése és kezelése.
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
ms.openlocfilehash: 147bfb6ba35115f44fe24f388f36814db766c7f2
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829461"
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>A Azure Stack fizikai memória kapacitásának kezelése

*Vonatkozik: Integrált rendszerek Azure Stack*

További memória hozzáadásával megnövelheti az Azure Stack számára rendelkezésre álló teljes memóriakapacitást. Azure Stack a fizikai kiszolgálót a *méretezési egység csomópontjának*is nevezzük. Az egyetlen méretezési egységhez tartozó összes skálázási egység csomópontjainak azonos mennyiségű memóriával kell rendelkezniük.

> [!note]  
> A folytatás előtt tekintse meg a hardver gyártójától származó dokumentációt, és ellenőrizze, hogy a gyártója támogatja-e a fizikai memória frissítését. Előfordulhat, hogy az OEM hardveres gyártó támogatási szerződése megköveteli, hogy a szállító végrehajtsa a fizikai kiszolgáló rack elhelyezését és az eszköz belső vezérlőprogram-frissítését.

A következő folyamatábrában látható az általános folyamat, amellyel memóriát adhat hozzá az egyes skálázási egységek csomópontjaihoz.

![Memória hozzáadása az egyes méretezési egységek csomópontjaihoz](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Memória hozzáadása egy meglévő csomóponthoz
A következő lépések áttekintést nyújtanak a memória hozzáadása folyamatról. 

> [!Warning]
> Ne kövesse ezeket a lépéseket a SZÁMÍTÓGÉPGYÁRTÓ által biztosított dokumentációra való hivatkozás nélkül.
> 
> [!Warning]
> A teljes méretezési egységet le kell állítani, mivel a működés közbeni memória frissítése nem támogatott.

1. Azure Stack leállítása a [Azure stack elindítása és leállítása](azure-stack-start-and-stop.md) című cikkben ismertetett lépések segítségével.
2. Frissítse a memóriát az egyes fizikai számítógépeken a hardver gyártójától származó dokumentáció használatával.
3. Indítsa el Azure Stack a [Azure stack elindítása és leállítása](azure-stack-start-and-stop.md) című cikkben ismertetett lépések alapján.

## <a name="next-steps"></a>További lépések

 - Ha szeretné megtudni, hogyan kezelheti Azure Stack Storage-fiókjait a tárolási kapacitás üzleti igények alapján történő megkereséséhez, helyreállításához és visszaigényléséhez, tekintse meg [a Azure stack Storage-fiókok kezelése](azure-stack-manage-storage-accounts.md)című témakört.
 - Ha meg szeretné tudni, hogy az Azure Stack Cloud operátor hogyan figyeli és kezeli a Azure Stack üzembe helyezésének tárolókapacitását, tekintse meg a [Azure stack tárolási](azure-stack-manage-storage-shares.md)kapacitásának kezelése című témakört. 
