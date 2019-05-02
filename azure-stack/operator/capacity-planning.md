---
title: Kapacitás megtervezése az Azure Stackhez |} A Microsoft Docs
description: További információ az Azure Stack üzemelő példányok kapacitástervezése.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 1a5bde916779961f9cc4c099b57324b8ddbd3bda
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984500"
---
# <a name="azure-stack-capacity-planning"></a>Az Azure Stack kapacitásának megtervezése
Az Azure Stack megoldás kiértékelésekor nincsenek hardver konfigurációs lehetőségeket, amelyek közvetlen hatással vannak az Azure Stack-felhő összesített kapacitását. Ezek a CPU, memória sűrűség, tárolási konfigurációt, és a teljes megoldás méretezési vagy kiszolgálók száma, a klasszikus lehetőségek. Ellentétben a hagyományos virtualizálási megoldás az egyszerű számtani felhasználható kapacitás meghatározásához ezeket az összetevőket nem vonatkozik. Az első ennek oka, hogy az Azure Stack tervezésnek lesz-e üzemeltetni az infrastruktúrát vagy felügyeleti összetevők magát a megoldáson belül. A második oka, hogy a megoldás kapacitás némelyike rugalmasság; feladat foglalt a megoldás szoftverek oly módon, hogy bérlői számítási feladatok frissítése.

> [!IMPORTANT]
> A megadott kapacitás-tervezési információkat, és a kísérő számolótábla permanens van csak annak érdekében, hogy az Azure Stack tervezési útmutató és konfigurációval kapcsolatos döntéseket. Nem kívánják a saját vizsgálati és elemzési helyettesíti. 

## <a name="compute-and-storage-capacity-planning"></a>Számítási és tárolási kapacitásának megtervezése
Az Azure Stack megoldás egy hiperkonvergens fürttel, számítási, hálózati és tárolási épül. Ez lehetővé teszi a hatékony használata vagy a fürt minden hardver-kapacitás megosztási néven egy skálázási egység az Azure Stack, úgy, hogy a rendelkezésre állást és méretezhetőséget biztosít. Az összes infrastruktúra-szoftver belüli virtuális gépekről (VM) üzemel, és megosztja, a bérlői virtuális gépeknek ugyanazon fizikai kiszolgálók. Minden virtuális gép majd kezeli a skálázási egység a Windows Server fürtözési technológiái és az egyes Hyper-V-példányok. Ez a megközelítés leegyszerűsíti a beszerzési és a egy Azure Stack megoldás felügyeleti, és az adatátviteli és a méretezhetőség az all Services (bérlői és infrastruktúra) között a skálázási egység teljes végrehajtható.

A csak fizikai erőforrás, amely az Azure Stack megoldással túlterhelt nincs kiépítve kiszolgálómemória. Az egyéb erőforrások, CPU magok, hálózati sávszélesség és tárolási kapacitás, a rendelkezésre álló erőforrások használható a leghatékonyabban kell overprovisioned. A megoldás számára rendelkezésre álló kapacitást kiszámításakor a fizikai kiszolgáló memória mérete a fő közreműködője. Egyéb erőforrások felhasználása, majd ismertetése, amely túlzott arány, lehetséges, és mi lesz a tervezett terhelés alapján elfogadható.

Körülbelül 30 virtuális gépek üzemeltetése az Azure Stack-infrastruktúra, és összesen, körülbelül 230 GB memória és a virtuális magok 140 felhasználása szolgálnak. A közösségértékek a virtuális gépek száma, hogy megfelelnek a biztonság, a méretezhetőség, a karbantartás és javítás követelmények teljesítéséhez szükséges szolgáltatás szétválasztását. Ez a belső struktúra lehetővé teszi, hogy a jövőbeli bevezetése új infrastruktúra-szolgáltatások, azok lettek kifejlesztve.

A fizikai kiszolgáló és a infrastruktúra szoftverösszetevőket, vagy a javítás és a frissítés, infrastruktúra és a felhasználói virtuális gép automatikus frissítésének támogatására szabadon elhelyezhetők a munkaterhelések a skálázási egység minden memória-erőforrásaira nem használnak fel. Az összes kiszolgálóján, a skálázási egységek a teljes memória mennyisége nem lefoglalt támogatásához a megoldás rugalmassági követelmények lesz. Például Windows Server-lemezképet a fizikai kiszolgáló frissítésekor a kiszolgálón futtatott virtuális gépek kerülnek máshol belül a skálázási egység, miközben a kiszolgáló Windows Server-lemezképeket frissült. A frissítés befejeződése után a kiszolgáló újraindul és számítási feladatok karbantartásába adja vissza. A javítás és a egy Azure Stack megoldás frissítése célja, hogy ne legyen szükség üzemeltetett virtuális gépek leállítása. A mindent megtesz felel meg a cél elérésében tett, legalább egy kiszolgáló memória-kapacitás az, hogy a skálázási egységben lévő virtuális gépek áthelyezését a fel nem használt. Az Elhelyezés és -mozgatás infrastruktúra virtuális gépeinek és a felhasználó vagy a bérlő az Azure Stack-megoldás a nevében létrehozott virtuális gépek is vonatkozik. A végső megvalósításának eredménye, hogy támogatásához szükséges a virtuális gép áthelyezését fenntartott memória mennyisége is lehet csupán egyetlen kiszolgáló kapacitásának elhelyezési kihívások miatt, ha a virtuális gépek rendelkeznek, különböző igényeket támasztó memória vannak. További többletterhelést jelenthet a kategóriában a memóriahasználatot, amely a Windows Server-példány magát. Az alap operációs rendszer minden egyes kiszolgáló-példány memóriát az operációs rendszer és a virtuális lap táblák kezelését, az üzemeltetett virtuális gépek mindegyike Hyper-V által felhasznált memória együtt fog felhasználni.

A Virtuálisgép-kapacitás rendelkezésre álló memória határozza meg. A virtuális-magot fizikai mag arány exemplify, hogy a Virtuálisgép-sűrűség hogyan módosítani rendelkezésre álló processzorkapacitást, kivéve, ha a megoldás több fizikai mag (egy másik CPU kiválasztott) úgy van felépítve. Ugyanez érvényes tárolási kapacitás és a gyorsítótár tárolókapacitást.

Virtuálisgép-sűrűség, a fenti példákban csak a magyarázat célokat szolgálnak. Nincs a számítások támogatást további összetettséget. Microsoft-partner vagy megoldásszolgáltató partner szükség, így jobban megismerheti a tervezési döntések és az eredményül kapott, a rendelkezésre álló kapacitás.

Ez a szakasz további része a számítási és tárolási Azure Stack üzembe helyezési követelményeket ismerteti. Ezen információk használatával ismertetése, mely összetevők szükségesek alapul, és a minimális konfigurációs értékeket kapjanak. További információkat is tartalmaz, a megoldás tartományállapot elérhető kapacitás és a bérlői kapacitás és teljesítmény funkció kapcsolatban a rendszer potenciális korlátok konfigurálását ismertetik.

> [!NOTE]
> Hálózati kapacitás tervezési követelményei minimálisak, mivel csak a nyilvános virtuális IP-cím mérete konfigurálható. Több nyilvános IP-címek hozzáadása az Azure Stackhez kapcsolatos információkat lásd: [nyilvános IP-címek hozzáadása](azure-stack-add-ips.md).


## <a name="next-steps"></a>További lépések
[Számítási kapacitásának megtervezése](capacity-planning-compute.md)
