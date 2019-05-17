---
title: Az Azure Stack fizikai memória-kapacitás kezelése |} A Microsoft Docs
description: Figyelheti és kezelheti a rendelkezésre álló tárhely az Azure Stackhez.
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
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 3aa69f1ea6e16b582a17da867c4895413d0043d2
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782381"
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Az Azure Stack fizikai memória-kapacitás kezelése

*Vonatkozik: Az Azure Stack integrált rendszerek*

A teljes rendelkezésre álló memória-kapacitás az Azure Stack növelése érdekében hozzáadhat további memória. Az Azure Stackben, a fizikai kiszolgálót is nevezik egy *skálázási egység csomópont*. Minden skálázási egység tagcsomópontja egyetlen skálázási egység azonos mennyiségű memóriával kell rendelkeznie.

> [!note]  
> A folytatás előtt tekintse meg a gyártó dokumentációját annak ellenőrzéséhez, hogy egy a gyártó támogatja a fizikai memória frissítése. Az OEM hardver szállítójával támogatási szerződés megkövetelheti, hogy a szállító hajtsa végre a fizikai kiszolgáló rack elhelyezése és az eszköz belső vezérlőprogramjának frissítése.

Az alábbi folyamatábrája bemutatja a memória hozzá minden egyes méretezési egység csomópont általános folyamata.

![Adja hozzá a memória minden egyes méretezési egység csomópont](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Memória hozzáadása egy meglévő csomópont
Az alábbi lépéseket a hozzáadási memória folyamat magas szintű áttekintését adja meg. 

> [!Warning]
> Ne kövesse ezeket a lépéseket az OEM által biztosított dokumentációt hivatkozó nélkül.
> 
> [!Warning]
> A skálázási egység le kell állítani a működés közbeni memória frissítés nem támogatott.

1. Állítsa le az Azure Stack használatával leírt lépéseket a [indítás és leállítás Azure Stack](azure-stack-start-and-stop.md) cikk.
2. Frissítse a memória minden egyes fizikai számítógépen a hardver gyártójával dokumentációnk segítségével.
3. Indítsa el az Azure Stack használatával a lépések a [indítás és leállítás Azure Stack](azure-stack-start-and-stop.md) cikk.

## <a name="next-steps"></a>További lépések

 - Ismerje meg, hogyan kezelheti a tárfiókok az Azure Stack keresése, helyreállítás és üzleti igényeinek megfelelően tárolási kapacitás visszaigényléséhez, lásd: [kezelése az Azure Stack tárfiókok](azure-stack-manage-storage-accounts.md).
 - Az Azure Stack-felhő üzemeltetője figyeli, és kezeli az Azure Stack üzemelő példányához tárkapacitása kapcsolatban lásd: [kezelése az Azure Stack a tárolókapacitás](azure-stack-manage-storage-shares.md). 
