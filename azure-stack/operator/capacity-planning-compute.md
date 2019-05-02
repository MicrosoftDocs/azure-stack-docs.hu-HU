---
title: Számítási kapacitás megtervezése az Azure Stackhez |} A Microsoft Docs
description: További információ az Azure Stack központi telepítésének tervezése a számítási kapacitást.
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
ms.date: 04/03/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 04/03/2019
ms.custom: ''
ms.openlocfilehash: f20dd8499abf4d8c7fd2097d47e655790c19d33d
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984002"
---
# <a name="azure-stack-compute-capacity-planning"></a>Az Azure Stack számítási kapacitásának megtervezése
A [Virtuálisgép-méretek támogatott az Azure Stacken](../user/azure-stack-vm-sizes.md) részhalmazát alkotják, az Azure-ban támogatott. Az Azure erőforrások (helyi és a szolgáltatásiszint-kiszolgáló) overconsumption elkerülése érdekében számos vektorok mentén erőforráskorlátok ír elő. Nélkül betartatásához bérlői használat bizonyos korlátozások, a bérlő élményt, amikor más bérlők overconsume erőforrások romlani fog. Hálózati kimenő forgalom a virtuális gépről a sávszélesség a caps teljesülnek az Azure Stacken, amelyek megfelelnek az Azure korlátai vannak. Storage access-bérlők a tárolási erőforrások, az Azure Stack-erőforrások egyszerű overconsumption elkerülése érdekében történtek tárolási IOPs-korlátok.  

## <a name="vm-placement-and-virtual-to-physical-core-overprovisioning"></a>Virtuálisgép-elhelyezés és -túlzott virtuális és fizikai mag
Az Azure Stackben nem adja meg egy adott kiszolgálóra a bérlő Virtuálisgép-Elhelyezés a használandó lehetőség. A csak szempont, virtuális gépek elhelyezésekor, hogy van-e elegendő memória a gazdagépen a virtuális gép típusa. Az Azure Stack nem szükségesnél memória; azonban a magok számát egy overcommit használata engedélyezett. Elhelyezési algoritmus nem vizsgálja a meglévő virtuális és fizikai mag túlzott arány tényezőként, mivel minden állomás egy eltérő arány rendelkezhet. 

Az Azure-ban magas rendelkezésre állás egy virtuális gépre kiterjedő éles rendszer, a virtuális gépek kerüljenek a rendelkezésre állási több tartalék tartomány között lehetnek elosztva. Az Azure Stack egy tartalék tartományt egy rendelkezésre állási csoportba egy csomópontot a skálázási egységben van definiálva.

Az Azure Stack-infrastruktúra képes legyen hatással a meghibásodások, míg az alapul szolgáló technológiát (feladatátvételi fürtszolgáltatás) továbbra is leállást bizonyos virtuális gépek érintett fizikai kiszolgálón egy hardverhiba. Jelenleg az Azure Stack támogatja tekintettel a rendelkezésre állási csoport, amely legfeljebb három tartalék tartományt konzisztens Azure-ral. Virtuális gépeket egy rendelkezésre állási csoportot helyezett osztja szét őket lehető legegyenletesebben több tartalék tartomány (az Azure Stack-csomópontok) keresztül lesz fizikailag különítve egymástól. Hardverhiba esetén a sikertelen tartalék tartomány virtuális gépeket fog a többi csomópont újraindítása, de, ha lehetséges, külön tartalék tartományokban tartani a más virtuális gépek ugyanazon rendelkezésre állási csoportban. A hardver visszatér online állapotba, ha virtuális gépek fog rebalanced magas rendelkezésre állás fenntartása érdekében.

Egy másik magas rendelkezésre állás biztosításához az Azure által használt fogalma rendelkezésre állási csoport platformfrissítési tartományainak formájában. A frissítési tartomány a mögöttes hardverelemek logikus csoportja, amelyen egyszerre végezhető karbantartás vagy újraindítás. Az Azure Stackben, virtuális gépek élő áttelepítése a fürt többi online gazdagép között, a mögöttes állomás frissítése előtt. A gazdagép frissítése közben nem bérlői jár, mivel a frissítési tartomány funkció az Azure Stacken csak létezik sablon kompatibilitás érdekében az Azure-ral.

## <a name="azure-stack-resiliency-resources"></a>Az Azure Stack rugalmasság erőforrások
Javítás és frissítése az Azure Stackkel integrált rendszer, és is ellenáll hardvermeghibásodások, a teljes kiszolgáló memóriájának egy részét a fenntartott és a bérlői virtuális gép (VM) elhelyezésre nem érhető el.

Ha egy kiszolgáló meghibásodik, a sikertelen kiszolgálón futtatott virtuális gépek a fennmaradó, rendelkezésre álló kiszolgálók nyújt a virtuális gépek rendelkezésre állása újraindul. Ehhez hasonlóan a javítási és frissítési folyamat során a kiszolgálón futó összes virtuális gép fog kell élő áttelepítése más kiszolgáló elérhető, az. Ez a virtuális gépek felügyelete vagy adattovábbítási érhető el, csak tartalékkapacitást, hogy az újraindítás vagy az áttelepítés előfordulása esetén.

A következő számítási eredményezi a teljes, rendelkezésre álló memória, amely a bérlői virtuális gépek elhelyezése is használható. Ez a memória-kapacitás az Azure Stack skálázási egység teljes szól.

  A Virtuálisgép-Elhelyezés a rendelkezésre álló memória = futtató virtuális gépek – az Azure Stack-infrastruktúra terhelését által használt teljes kiszolgáló memória - tartalék rugalmasság – memória <sup>1</sup>

  Rugalmasság tartalék H + R = * ((N-1) * H) + V * (N-2)

> Az elemek magyarázata:
> - H = egykiszolgálós memória mérete
> - N = mérete a skálázási egység (kiszolgálók száma)
> - Az R = az operációs rendszer az operációs rendszer terhelést pufferkészletének tartalék<sup>2</sup>
> - V legnagyobb virtuális gép = a skálázási egységben

  <sup>1</sup> az azure Stack-infrastruktúra többletterhelést okoz a 230 GB =

  <sup>2</sup> többletterhelést az operációs rendszer tartalék = 15 %-a csomópont memóriája. Az operációs rendszer számára fenntartott érték egy becsült, és terméktípustól függ a kiszolgáló és az általános operációs rendszer terhelést a fizikai memória kapacitását.

Az érték V, a skálázási egységben legnagyobb virtuális gép dinamikusan alapul a legnagyobb bérlői virtuális gép memória méretét. A virtuális gép legnagyobb értéket lehet például, 7 GB vagy 112 GB vagy bármilyen egyéb támogatott virtuális gép memória mérete az Azure Stack-megoldásban.

A fenti számítás becsült és az Azure Stack jelenlegi verziója alapján változhat. Lehetővé teszi a bérlő virtuális gépek és szolgáltatások telepítéséhez a telepített megoldás tulajdonságairól alapul. Ez a példa a számítás csak egy útmutató, és nem képes virtuális gépek üzembe helyezése abszolút válasz.



## <a name="next-steps"></a>További lépések
[Tárolási kapacitásának megtervezése](capacity-planning-storage.md)
