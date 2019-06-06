---
title: Az Azure Stack kapacitástervezési számítási |} A Microsoft Docs
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
ms.openlocfilehash: 6005196fe98f83c11b9d87ff713e290bad9ef384
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692029"
---
# <a name="azure-stack-compute"></a>Azure Stack-számítás

A [Virtuálisgép-méretek](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes) támogatott az Azure Stackben is támogatja az Azure-ban a részhalmazát képezik. Az Azure erőforrások (helyi és a szolgáltatásiszint-kiszolgáló) overconsumption elkerülése érdekében számos vektorok mentén erőforráskorlátok ír elő. Nélkül betartatásához bérlői használat bizonyos korlátozások, a bérlő élményt, amikor más bérlők overconsume erőforrások romlani fog. Hálózati kimenő forgalom a virtuális gépről a sávszélesség a caps teljesülnek az Azure Stacken, amelyek megfelelnek az Azure korlátai vannak. Storage access-bérlők a tárolási erőforrások, az Azure Stack-erőforrások egyszerű overconsumption elkerülése érdekében történtek tárolási IOPS-korlátok.

>[!IMPORTANT]
>A [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) nem fontolja meg, és nem garantálja a IOPS teljesítményt.

## <a name="vm-placement"></a>Virtuális gépek elhelyezése

Az Azure Stackben bérlői Virtuálisgép-Elhelyezés a végzi el automatikusan az elhelyezési motor rendelkezésre álló gazdagép között. A virtuális gépek elhelyezésekor csak két szempontok vannak, hogy nincs elég memória a gazdagépen a virtuális gép típusát, és ha a virtuális gépek egy részét képezik- [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) vagy [a virtual machine scale sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  

Magas rendelkezésre állás az Azure Stackben több virtuális gépre kiterjedő gyártási rendszer, virtuális gépek kerülnek egy rendelkezésre állási csoportban, a azokat több tartalék tartomány között. Tartalék tartomány egy rendelkezésre állási csoportban van definiálva egy csomópontot a skálázási egységben. Az Azure Stack támogatja a rendelkezésre állási csoport, amely legfeljebb három tartalék tartományt az Azure-ral konzisztens kellene. Virtuális gépeket egy rendelkezésre állási csoportot helyezett osztja szét őket lehető legegyenletesebben több tartalék tartomány, azt jelenti, az Azure Stack-gazdagépeken keresztül lesz fizikailag különítve egymástól. Hardverhiba esetén a sikertelen tartalék tartomány virtuális gépeket fogja indítani a többi tartalék tartományban, de, külön tartalék tartományokban, ha lehetséges, a más virtuális gépek ugyanazon rendelkezésre állási csoportban tartani. Ha ismét online elérhető a gazdagép, virtuális gépek fog rebalanced magas rendelkezésre állás fenntartása érdekében.  

Virtuális gépek méretezési vissza a feladatokat a rendelkezésre állási készletek használata befejezése, és győződjön meg arról, hogy minden virtuális gép méretezési csoport példánya kerül egy különböző tartalék tartományban. Ez azt jelenti, hogy külön Azure Stack-infrastruktúra csomópontokat használnak. Például az Azure Stack system, 4 csomópont lehet egy olyan helyzetet, ahol egy virtuálisgép-méretezési 3-példányok létrehozáskor 3 virtuális gép méretezési csoport példányaihoz helyezi 3 külön Azure Stack-csomópont 4 – csomópont-kapacitás hiánya miatt meghiúsul. Emellett az Azure Stack-csomópontok tölthető különböző szintű elhelyezési kísérlet előtt. 

Az Azure Stack túlterhelt nem véglegesíthető a memória. Azonban a fizikai Processzormagok számát egy túlzott véglegesítés engedélyezett. Elhelyezési algoritmus nem tekintse meg a meglévő virtuális és fizikai mag fölösleges üzembe helyezési arány tényezőként, mivel minden állomás egy eltérő arány rendelkezhet. Microsoft nem nyújtunk útmutatást az fizikai-virtuális mag arány a számítási feladatok és a szolgáltatás követelményeinek változása miatt. 

## <a name="azure-stack-memory"></a>Az Azure Stack memória 

Az Azure Stack, hogy a futó virtuális gépek, sikeresen kiépített tervezték. Például ha egy gazdagép egy hardverhiba miatt offline állapotban, az Azure Stack megkísérli egy másik gazdagépre a virtuális gép újraindítása. A második példa a javítás- és az Azure Stack szoftver frissítése. Ha egy fizikai gazdagép újraindítása szükséges, egy kísérlet történik helyezze át a virtuális gépek, amelyeken a megoldás érhető el egy másik gazdagépére végrehajtása.   

A virtuális gép kezelése vagy a mozgás érhető el, csak ha fenntartott memória-kapacitás, hogy az újraindítás vagy az áttelepítés történjen. Gazdagép teljes memóriájának egy részét a fenntartott és a bérlői virtuális gép elhelyezésre nem érhető el. 

A kördiagram, amely az Azure Stackben jeleníti meg az ingyenes és a használt memória a felügyeleti portálon tekintheti meg. Az alábbi ábrán a fizikai memória-kapacitás az Azure Stack skálázási egység az Azure stack a látható:

![Fizikai memória-kapacitás](media/azure-stack-capacity-planning/physical-memory-capacity.png)

Használt memória több összetevőből épül fel. A következő összetevők használata a memória a tortadiagram használata szakaszában:  

 -  Gazdagép operációs Rendszeréhez használati vagy tartalék – Ez a gazdagép, virtuális memória lap táblákat, a gazda operációs rendszer és a közvetlen tárolóhelyek memória-gyorsítótár a futó folyamatok, az az operációs rendszer (OS) által felhasznált memória. Mivel ez az érték a Hyper-V a gazdagépen futó másik folyamat által használt memória függ, hogy ingadozhaz.
 - Infrastruktúra-szolgáltatások – ezek azok az Azure Stack alkotó virtuális gépek infrastruktúra. Az Azure Stack 1904 kibocsátási verziókban, kezdődően ez maga után vonja körülbelül 31 virtuális gépeket is igénybe vehet 242 GB + (4 GB x csomópontok száma) memória. Az infrastruktúra-szolgáltatások összetevő memóriahasználata változhatnak, gondoskodik az infrastruktúra-szolgáltatások jobban méretezhető és rugalmas dolgozunk.
 - Rugalmasság tartalék – Azure Stack foglal le, hogy a virtuális gépek a sikeres működés közbeni áttelepítése közben egyetlen gazdagép hibája esetén, valamint javítási és a frissítés során a bérlő rendelkezésre állás érdekében engedélyezéséhez memóriájának egy részét.
 - Bérlő virtuális gépek – ezek azok a bérlői virtuális gépeket az Azure Stack-felhasználók által létrehozott. Virtuális gépek futtatása mellett memória olyan virtuális gépek, amelyek a hálón jut használja fel. Ez azt jelenti, hogy a "Létrehozás" vagy "Sikertelen" állapotú virtuális gépet, vagy állítsa le a belül a Vendég virtuális gépek memóriát fog felhasználni. Virtuális gépek, amelyek használatával a leállítási lehetőséget a portal/powershell/cli felszabadítása felszabadítása azonban nem használnak fel az Azure Stack memória.
 - Bővítmény RPs – virtuális gépet üzembe helyezni a kiegészítő RPs például SQL, MySQL, App Service és egyebek számára


A memóriát a portál megértésének legjobb módja az, hogy használja a [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) megtekintéséhez a különböző számítási feladatok hatását. A következő számítási megegyezik egy, a planner által használt.

Ehhez a számításhoz eredményezi a teljes, rendelkezésre álló memória, amely a bérlői virtuális gépek elhelyezése is használható. Ez a memória-kapacitás az Azure Stack skálázási egység teljes szól. 


  A Virtuálisgép-Elhelyezés a rendelkezésre álló memória = futó bérlői virtuális gépek – az Azure Stack-infrastruktúra terhelését által használt összes gazdagépen memória - rugalmasság tartalék - memória <sup>1</sup>

  Rugalmasság tartalék H + R = * ((N-1) * H) + V * (N-2)

> Az elemek magyarázata:
> - H = egykiszolgálós memória mérete
> - N = mérete a skálázási egység (kiszolgálók száma)
> - Az R = az operációs rendszer számára fenntartott az operációs rendszer többletterhelést okoz, ami ebben a képletben.15<sup>2</sup>
> - V legnagyobb virtuális gép = a skálázási egységben

  <sup>1</sup> azure Stack-infrastruktúra terhelését = 242 GB + (4 GB-os csomópont x). Körülbelül 31 virtuális gépek üzemeltetése az Azure Stack-infrastruktúra, és összességében felhasználása 242 GB-os használt + (4 GB x csomópontok száma) memóriával és 146 virtuális maggal. A közösségértékek a virtuális gépek száma, hogy megfelelnek a biztonság, a méretezhetőség, a karbantartás és javítás követelmények teljesítéséhez szükséges szolgáltatás szétválasztását. Ez a belső struktúra lehetővé teszi, hogy a jövőbeli bevezetése új infrastruktúra-szolgáltatások, azok lettek kifejlesztve. 

  <sup>2</sup> többletterhelést az operációs rendszer tartalék = 15 %-os (. 15) csomópont memória. Az operációs rendszer számára fenntartott érték egy becsült, és terméktípustól függ a kiszolgáló és az általános operációs rendszer terhelést a fizikai memória kapacitását.


Az érték V, a skálázási egységben legnagyobb virtuális gép dinamikusan alapul a legnagyobb bérlői virtuális gép memória méretét. A virtuális gép legnagyobb értéket lehet például, 7 GB vagy 112 GB vagy bármilyen egyéb támogatott virtuális gép memória mérete az Azure Stack-megoldásban. A legnagyobb virtuális Gépet az Azure Stack-hálón módosítása a memóriát a virtuális gép magát a növekedése mellett a rugalmasság tartalék növekedése eredményez. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

K: A bérlőhöz egy új virtuális Gépet üzembe helyezett, mennyi ideig tart a funkció a felügyeleti portálon diagram megjelenítése a fennmaradó kapacitás?
V: A kapacitás panel frissül 15 percenként, ezért kérjük figyelembe kell venni, amelyek.

K: A saját Azure Stack üzembe helyezett virtuális gépek száma nem változott, de saját kapacitása van ingadozik. Hogy miért?
V: A rendelkezésre álló memóriát a virtuális gép elhelyezéséhez több függőségeket, amelyek egyike a gazda operációs rendszer tartalék rendelkezik. Ez az érték szolgáltatás fut a gazdagépen, amely nem egy konstans érték különböző Hyper-V folyamat által használt memória függ.

K: Milyen állapotban rendelkeznek bérlői virtuális gépeket a memóriát kell?
V: Virtuális gépek futtatása mellett memória olyan virtuális gépek, amelyek a hálón jut használja fel. Ez azt jelenti, hogy a "Létrehozás", "Sikertelen" vagy a virtuális gépek állítsa le a belül a Vendég virtuális gépek leállítása a felszabadítása sikeresen megtörtént a portal/powershell vagy parancssori felület adatkötetekkel fog memóriát igényelnek.


K: Azure Stack 4 gazdagépre van. A bérlőhöz 3 által felhasználható 56 GB RAM (D5_v2) mindegyik virtuális gépet tartalmaz. A virtuális gépek egyik átméretezett 112 GB RAM (D14_v2), és a rendelkezésre álló memória-jelentések irányítópult a kapacitás panelen 168 GB használati ugrásszerű eredményezett. Csak 56GB RAM-MAL növekedést eredményezett a két D5_v2 VMs D14_v2, az ezt követő átméretezése. Ez miért van így?

V: A rendelkezésre álló memória, akkor a függvény a rugalmasság tartalék Azure Stack tartja karban. A rugalmasság tartalék feladata a legnagyobb Virtuálisgép-méretet az Azure Stack stamp. Először a legnagyobb virtuális Gépet a blokkban volt 56 GB memóriával. Ha a virtuális gép át lett méretezve, a legnagyobb virtuális Gépet a blokkban vált, 112 GB memória, amely nem csak a bérlő virtuális gép által felhasznált memória növekedett, de a rugalmasság tartalék is növekedett. Ennek következtében 56 GB (56 GB, 112 GB bérlői virtuális gép memóriájának növelése) növelésének + 112 GB rugalmasság számára fenntartott memória növelése. Amikor további virtuális gépeket is méretezhető, a legnagyobb Virtuálisgép-méret maradt, mint a 112 GB-os virtuális gép, és ezért növelése nélkül eredő rugalmasság foglalás nem sikerült. Memóriahasználat növekedése csak a bérlői virtuális gép memóriájának növelése (56 GB) volt. 


> [!NOTE]
> Hálózati kapacitás tervezési követelményei minimálisak, mivel csak a nyilvános virtuális IP-cím mérete konfigurálható. Több nyilvános IP-címek hozzáadása az Azure Stackhez kapcsolatos információkért lásd: [nyilvános IP-címek hozzáadása](azure-stack-add-ips.md).

## <a name="next-steps"></a>További lépések
Ismerje meg [Azure Stack-tároló](azure-stack-capacity-planning-storage.md)
