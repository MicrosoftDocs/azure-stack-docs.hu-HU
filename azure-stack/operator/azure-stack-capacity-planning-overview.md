---
title: Az Azure Stack hub kapacitásának tervezésének áttekintése
description: További információ a Azure Stack hub üzemelő példányok kapacitásának megtervezéséről.
author: ihenkel
ms.topic: article
ms.date: 05/31/2019
ms.author: inhenkel
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: f948502eb419abf067c4e71497faf09305430c33
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890340"
---
# <a name="overview-of-azure-stack-hub-capacity-planning"></a>Az Azure Stack hub kapacitás-tervezésének áttekintése

Azure Stack hub-megoldás kiértékelése során vegye figyelembe a hardveres konfigurációs döntéseket, amelyek közvetlen hatással vannak az Azure Stack hub-felhő teljes kapacitására. 

Például döntéseket kell hoznia a CPU-ra, a memória sűrűségére, a tárolási konfigurációra, valamint az általános megoldási méretre vagy a kiszolgálók számára vonatkozóan. A hagyományos virtualizációs megoldástól eltérően az összetevők egyszerű aritmetikaa nem alkalmazható a felhasználható kapacitás meghatározására. Azure Stack hub a megoldás infrastruktúrájának vagy felügyeleti összetevőinek üzemeltetésére készült. Emellett a megoldás bizonyos kapacitása a rugalmasság támogatásához van fenntartva, a megoldás szoftverének frissítése pedig a bérlői számítási feladatok megszakításának minimalizálására szolgál. 

> [!IMPORTANT]
> Ez a kapacitás-tervezési információ és a [Azure stack hub Capacity Planner](https://aka.ms/azstackcapacityplanner) a Azure stack hub tervezési és konfigurációs döntéseinek kiindulási pontja. Ez az információ nem helyettesíti a saját vizsgálatát és elemzését. A Microsoft nem vállal semmilyen kifejezett vagy vélelmezett szavatosságot az itt megadott információra vonatkozóan.
 
Az Azure Stack hub-megoldások számítási és tárolási hiperkonvergens-fürtökként készültek. A konvergencia lehetővé teszi a hardver kapacitása megosztását a fürtben, az úgynevezett *méretezési egységként*. Azure Stack központban a méretezési egység biztosítja az erőforrások rendelkezésre állását és méretezhetőségét. A skálázási egység olyan Azure Stack hub-kiszolgálók készletét tartalmazza, amelyeket *gazdagépeknek*nevezünk. Az infrastruktúra szoftvere virtuális gépeken (VM) belül található, és ugyanazokkal a fizikai kiszolgálókkal rendelkezik, mint a bérlő virtuális gépei. Ezt követően az összes Azure Stack hub-beli virtuális gépet felügyeli a skálázási egység Windows Server-fürtszolgáltatási technológiái és az egyes Hyper-V-példányok. 

A skálázási egység leegyszerűsíti a beszerzési és felügyeleti Azure Stack hubot. A skálázási egység lehetővé teszi az összes szolgáltatás (bérlő és infrastruktúra) áthelyezését és méretezhetőségét Azure Stack központon keresztül. 

A következő témakörök további információkat nyújtanak az egyes összetevőkről:

- [Azure Stack hub-számítás](azure-stack-capacity-planning-compute.md)
- [Azure Stack hub Storage](azure-stack-capacity-planning-storage.md)
- [Azure Stack hub Capacity Planner](azure-stack-capacity-planner.md)
