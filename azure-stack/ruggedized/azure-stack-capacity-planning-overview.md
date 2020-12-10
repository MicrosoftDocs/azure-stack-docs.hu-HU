---
title: Az Azure Stack hub kapacitásának megtervezése – áttekintés | Microsoft Docs
description: További információ a Azure Stack hub üzemelő példányok kapacitásának megtervezéséről.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 7ccaee0f134f2aa2a56f0fe14bab8fba72a84726
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96940078"
---
# <a name="overview-of-azure-stack-hub-capacity-planning"></a>Az Azure Stack hub kapacitás-tervezésének áttekintése

Azure Stack hub-megoldás kiértékelése során vegye figyelembe a hardveres konfigurációs döntéseket, amelyek közvetlen hatással vannak az Azure Stack hub-felhő teljes kapacitására. 

Például döntéseket kell hoznia a CPU-ra, a memória sűrűségére, a tárolási konfigurációra, valamint az általános megoldási méretre vagy a kiszolgálók számára vonatkozóan. A hagyományos virtualizációs megoldástól eltérően az összetevők egyszerű aritmetikaa nem alkalmazható a felhasználható kapacitás meghatározására. Azure Stack hub a megoldás infrastruktúrájának vagy felügyeleti összetevőinek üzemeltetésére készült. Emellett a megoldás bizonyos kapacitása a rugalmasság támogatásához van fenntartva, a megoldás szoftverének frissítése pedig a bérlői számítási feladatok megszakításának minimalizálására szolgál. 

> [!IMPORTANT]
> Ez a kapacitás-tervezési információ és a [Azure stack hub Capacity Planner](https://aka.ms/azstackcapacityplanner) a Azure stack hub tervezési és konfigurációs döntéseinek kiindulási pontja. Ez az információ nem helyettesíti a saját vizsgálatát és elemzését. A Microsoft nem vállal semmilyen kifejezett vagy vélelmezett szavatosságot az itt megadott információra vonatkozóan.
 
Az Azure Stack hub-megoldások számítási és tárolási hiperkonvergens-fürtökként készültek. A konvergencia lehetővé teszi a hardver kapacitása megosztását a fürtben, az úgynevezett *méretezési egységként*. Azure Stack központban a méretezési egység biztosítja az erőforrások rendelkezésre állását és méretezhetőségét. A skálázási egység olyan Azure Stack hub-kiszolgálók készletét tartalmazza, amelyeket *gazdagépeknek* nevezünk. Az infrastruktúra szoftvere virtuális gépeken (VM) belül található, és ugyanazokkal a fizikai kiszolgálókkal rendelkezik, mint a bérlő virtuális gépei. Ezt követően az összes Azure Stack hub-beli virtuális gépet felügyeli a skálázási egység Windows Server-fürtszolgáltatási technológiái és az egyes Hyper-V-példányok. 

A skálázási egység leegyszerűsíti a beszerzési és felügyeleti Azure Stack hubot. A skálázási egység lehetővé teszi az összes szolgáltatás (bérlő és infrastruktúra) áthelyezését és méretezhetőségét Azure Stack központon keresztül. 

A következő témakörök további információkat nyújtanak az egyes összetevőkről:

- [Azure Stack hub-számítás](../operator/azure-stack-capacity-planning-compute.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
- [Azure Stack hub Storage](../operator/azure-stack-capacity-planning-storage.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
- [Azure Stack hub Capacity Planner](../operator/azure-stack-app-service-capacity-planning.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)

## <a name="azure-stack-hub-ruggedized-high-and-low-models"></a>Azure Stack hub robusztus magas és alacsony modellek

Az alábbi táblázat a Azure Stack hub magas és alacsony modelljeinek specifikációit sorolja fel.

| Összetevő               | Specifikáció |
|-------------------------|---------------|
| CPU                     |Magas: 284 vCPU magok<br>Alacsony: 184 vCPU magok  |
| Memory (Memória)                  |Magas: 1 037 GB<br>Alacsony: 547 GB                |
| Storage                 |Magas: 34,2 TB<br>Alacsony: 15,4 TB                |
| Acceleration            |N.A.                                          |
| Méret/súly             |Számítás: 31,5 "L x 23,8" W x 15,33 "H<br>Súly 120 lbs. (2. mennyiség)<br>Hálózatkezelés: 31,5 "L x 23,8" W x 17,12 "H<br>Súly 145 lbs. (1. mennyiség)              |
| Választható konfigurációk |Előmelegítő<br>Magas vagy alacsony konfigurációk     |

## <a name="next-steps"></a>Következő lépések

[Azure stack hub-számítás](../operator/azure-stack-capacity-planning-compute.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json) 
 [Azure stack hub Storage](../operator/azure-stack-capacity-planning-storage.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json) 
 [Azure stack Hub Capacity Planner](../operator/azure-stack-app-service-capacity-planning.md?toc=/azure-stack/tdc/toc.json&bc=/azure-stack/breadcrumb/toc.json)
