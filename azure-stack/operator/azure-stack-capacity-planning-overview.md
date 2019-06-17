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
ms.openlocfilehash: 335e92fcdb2eadd8bebfbfd3fb2e3b31edd00734
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131337"
---
# <a name="overview-of-azure-stack-capacity-planning"></a>Azure Stack-kapacitástervezés – áttekintés

Az Azure Stack megoldás mérlegeli, vegye figyelembe a hardver konfigurációs lehetőségek, amelyek közvetlen hatással vannak az Azure Stack-felhő összesített kapacitását. 

Ha például szeretne a CPU, memória sűrűség, tárolási konfigurációt, és a teljes megoldás méretezési vagy kiszolgálók száma kapcsolatos döntéseket. Ellentétben a hagyományos virtualizálási megoldás az egyszerű számtani felhasználható kapacitás meghatározásához ezeket az összetevőket nem vonatkozik. Az Azure Stack üzemeltetéséhez maga a megoldáson belül a infrastruktúrát vagy felügyeleti összetevők épül. Emellett néhány, a megoldás a kapacitás foglalt támogatásához a rugalmasság, a megoldás szoftverek oly módon, hogy bérlői számítási feladatok frissítése. 

> [!IMPORTANT]
> Ebben a kapacitástervezéssel információkat és a [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) egy kiindulási pont, az Azure Stack tervezési és konfigurációs döntéseket hozhat. Ezt az információt nem terjesztésre készült saját vizsgálati és elemzési helyettesíti. Microsoft nem vállal értük felelősséget vagy rájuk kifejezett vagy törvényi garanciát az itt megjelenő információért.
 
Az Azure Stack megoldás számítási és tárolási fürtként hiperkonvergens épül. Az átszervezés lehetővé teszi, hogy a hardver kapacitás megosztási néven a fürt egy *skálázási egység*. Az Azure Stackben a méretezési egység biztosít a rendelkezésre állás és méretezhetőség az erőforrások. Azure Stack-kiszolgálók, a továbbiakban egy csoportja áll egy skálázási egység *gazdagépek*. Az infrastruktúra szoftver belüli virtuális gépekről (VM) üzemel, és oszt meg, a bérlői virtuális gépeknek ugyanazon fizikai kiszolgálók. Az összes Azure Stack virtuális gépek majd kezeli a skálázási egység a Windows Server fürtözési technológiái és az egyes Hyper-V-példányok. 

A skálázási egység egyszerűsíti a beszerzés és kezelése az Azure Stack. A skálázási egység is lehetővé teszi az adatátviteli és méretezhetőségét, az all Services (bérlői és infrastruktúra) az Azure Stack. 

A következő témakörök nyújtanak további információt az egyes összetevők:

- [Azure Stack-számítás](azure-stack-capacity-planning-compute.md)
- [Az Azure Stack-tároló](azure-stack-capacity-planning-storage.md)
- [Azure Stack Capacity Planner](azure-stack-capacity-planner.md)
