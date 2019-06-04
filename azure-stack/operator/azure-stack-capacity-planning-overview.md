---
title: Kapacitás megtervezése az Azure Stack áttekintése |} A Microsoft Docs
description: További információ az Azure Stack üzemelő példányok kapacitástervezése.
services: azure-stack
documentationcenter: ''
author: prchint
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 0d2610d2082973f4ab255027cc299b5858660c58
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2019
ms.locfileid: "66460991"
---
# <a name="overview-of-azure-stack-capacity-planning"></a>Azure Stack-kapacitástervezés – áttekintés

Az Azure Stack megoldás kiértékelésekor nincsenek hardver konfigurációs lehetőségeket, amelyek közvetlen hatással vannak az Azure Stack-felhő összesített kapacitását. Ezek a CPU, memória sűrűség, tárolási konfigurációt, és a teljes megoldás méretezési vagy kiszolgálók száma, a klasszikus lehetőségek. Ellentétben a hagyományos virtualizálási megoldás az egyszerű számtani felhasználható kapacitás meghatározásához ezeket az összetevőket nem vonatkozik. Az első ennek oka, hogy az Azure Stack tervezésnek lesz-e üzemeltetni az infrastruktúrát vagy felügyeleti összetevők magát a megoldáson belül. A második oka, hogy néhány, a megoldás a kapacitás rugalmasságot, a megoldás szoftverek oly módon, hogy bérlői számítási feladatok frissítése feladat foglalt. 

> [!IMPORTANT]
> Ebben a kapacitástervezéssel információkat és a [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) egy kiindulási pont, az Azure Stack tervezési és konfigurációs döntéseket hozhat. Nem célja a saját vizsgálati és elemzési helyettesítésére szolgál. Microsoft nem vállal értük felelősséget vagy rájuk kifejezett vagy törvényi garanciát az itt megjelenő információért.
 
Az Azure Stack megoldás egy hiperkonvergens fürttel, számítási és tárolási épül. Az átszervezés lehetővé teszi, hogy a hardver kapacitás megosztási néven a fürt egy *skálázási egység*. Az Azure Stackben a méretezési egység biztosít a rendelkezésre állás és méretezhetőség az erőforrások. Azure Stack-kiszolgálók, a továbbiakban egy csoportja áll egy skálázási egység *gazdagépek*. Infrastruktúra szoftver belüli virtuális gépek üzemel, és megosztja az ugyanazon fizikai kiszolgálók, a bérlői virtuális gépeket. Az összes Azure Stack virtuális gépek majd kezeli a skálázási egység a Windows Server fürtözési technológiái és az egyes Hyper-V-példányok. A skálázási egység egyszerűsíti a beszerzés és kezelése az Azure Stack. A skálázási egység is lehetővé teszi az adatátviteli és méretezhetőségét, az all Services (bérlői és infrastruktúra) az Azure Stack. 

A következő témakörök nyújtanak további információt az egyes összetevők:

- [Azure Stack-számítás](azure-stack-capacity-planning-compute.md)
- [Az Azure Stack-tároló](azure-stack-capacity-planning-storage.md)
- [Azure Stack Capacity Planner](azure-stack-capacity-planner.md)
