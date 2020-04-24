---
title: A kapacitás megtervezésének áttekintése
titleSuffix: Azure Stack Hub
description: További információ a Azure Stack hub üzemelő példányok kapacitásának megtervezéséről.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: inhenkel
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: e097904f990f76898253e2d56115b10ce230de36
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77700986"
---
# <a name="capacity-planning-for-azure-stack-hub-overview"></a>Azure Stack hub kapacitásának tervezése – áttekintés

Azure Stack hub-megoldás kiértékelése során vegye figyelembe a hardveres konfigurációs döntéseket, amelyek közvetlen hatással vannak az Azure Stack hub-felhő teljes kapacitására.

Döntéseket kell hoznia a CPU, a memória sűrűsége, a tárolási konfiguráció, valamint az általános megoldás-méretezés vagy a kiszolgálók száma tekintetében. A felhasználható kapacitás meghatározása azonban eltér a hagyományos virtualizációs megoldástól, mert a kapacitás már használatban van. Azure Stack hub a megoldás infrastruktúrájának vagy felügyeleti összetevőinek üzemeltetésére készült. Emellett a megoldás kapacitása a rugalmasság támogatásához van fenntartva. A rugalmasság úgy van meghatározva, hogy a megoldás szoftverének frissítése a bérlői számítási feladatok megszakításának csökkentése érdekében.

> [!IMPORTANT]
> Ez a kapacitás-tervezési információ és a [Azure stack hub Capacity Planner](https://aka.ms/azstackcapacityplanner) a Azure stack hub tervezési és konfigurációs döntéseinek kiindulási pontja. Ez az információ nem helyettesíti a saját vizsgálatát és elemzését. A Microsoft nem vállal semmilyen kifejezett vagy vélelmezett szavatosságot az itt megadott információra vonatkozóan.

## <a name="hyperconvergence-and-the-scale-unit"></a>Hyperconvergence és a skálázási egység
Az Azure Stack hub-megoldások számítási és tárolási hiperkonvergens-fürtökként készültek. A konvergencia lehetővé teszi a hardver kapacitása megosztását a fürtben, az úgynevezett *méretezési egységként*. Azure Stack központban a méretezési egység biztosítja az erőforrások rendelkezésre állását és méretezhetőségét. A skálázási egység olyan Azure Stack hub-kiszolgálók készletét tartalmazza, amelyeket *gazdagépeknek*nevezünk. Az infrastruktúra szoftvere virtuális gépeken (VM) belül található, és ugyanazokkal a fizikai kiszolgálókkal rendelkezik, mint a bérlő virtuális gépei. Ezt követően az összes Azure Stack hub-beli virtuális gépet felügyeli a skálázási egység Windows Server-fürtszolgáltatási technológiái és az egyes Hyper-V-példányok.

A skálázási egység leegyszerűsíti Azure Stack hub beszerzését és felügyeletét. A skálázási egység lehetővé teszi az összes szolgáltatás (bérlő és infrastruktúra) áthelyezését és méretezhetőségét Azure Stack központon keresztül.

A következő témakörök további információkat nyújtanak az egyes összetevőkről:

- [Azure Stack hub-számítás](azure-stack-capacity-planning-compute.md)
- [Azure Stack hub Storage](azure-stack-capacity-planning-storage.md)
- [Azure Stack hub Capacity Planner](azure-stack-capacity-planner.md)
