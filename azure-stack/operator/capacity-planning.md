---
title: Kapacitás megtervezése az Azure Stackhez |} A Microsoft Docs
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
ms.date: 05/30/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/30/2019
ms.openlocfilehash: a67a5010da2a67fb002a351b04a12fe7671fb73b
ms.sourcegitcommit: 4e0b450c91c6515794b663a39f9a4b8b49999918
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66411702"
---
# <a name="azure-stack-capacity-planning"></a>Az Azure Stack kapacitásának megtervezése
Az Azure Stack megoldás kiértékelésekor nincsenek hardver konfigurációs lehetőségeket, amelyek közvetlen hatással vannak az Azure Stack-felhő összesített kapacitását. Ezek a CPU, memória sűrűség, tárolási konfigurációt, és a teljes megoldás méretezési vagy kiszolgálók száma, a klasszikus lehetőségek. Ellentétben a hagyományos virtualizálási megoldás az egyszerű számtani felhasználható kapacitás meghatározásához ezeket az összetevőket nem vonatkozik. Az első ennek oka, hogy az Azure Stack tervezésnek lesz-e üzemeltetni az infrastruktúrát vagy felügyeleti összetevők magát a megoldáson belül. A második oka, hogy néhány, a megoldás a kapacitás rugalmasságot, a megoldás szoftverek oly módon, hogy bérlői számítási feladatok frissítése feladat foglalt. 

> [!IMPORTANT]
> Ebben a kapacitástervezéssel információkat és a [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) egy kiindulási pont, az Azure Stack tervezési és konfigurációs döntéseket hozhat. Nem célja a saját vizsgálati és elemzési helyettesítésére szolgál. Microsoft nem vállal értük felelősséget vagy rájuk kifejezett vagy törvényi garanciát az itt megjelenő információért.
 

Az Azure Stack megoldás egy hiperkonvergens fürttel, számítási és tárolási épül. Az átszervezés lehetővé teszi, hogy a hardver kapacitás megosztási néven a fürt egy *skálázási egység*. Az Azure Stackben a méretezési egység biztosít a rendelkezésre állás és méretezhetőség az erőforrások. Azure Stack-kiszolgálók, a továbbiakban egy csoportja áll egy skálázási egység *gazdagépek*. Infrastruktúra szoftver belüli virtuális gépek üzemel, és megosztja az ugyanazon fizikai kiszolgálók, a bérlői virtuális gépeket. Az összes Azure Stack virtuális gépek majd kezeli a skálázási egység a Windows Server fürtözési technológiái és az egyes Hyper-V-példányok. A skálázási egység egyszerűsíti a beszerzés és kezelése az Azure Stack. A skálázási egység is lehetővé teszi az adatátviteli és méretezhetőségét, az all Services (bérlői és infrastruktúra) az Azure Stack. 

## <a name="azure-stack-compute"></a>Azure Stack-számítás

A [Virtuálisgép-méretek](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes) támogatott az Azure Stackben is támogatja az Azure-ban a részhalmazát képezik. Az Azure erőforrások (helyi és a szolgáltatásiszint-kiszolgáló) overconsumption elkerülése érdekében számos vektorok mentén erőforráskorlátok ír elő. Nélkül betartatásához bérlői használat bizonyos korlátozások, a bérlő élményt, amikor más bérlők overconsume erőforrások romlani fog. Hálózati kimenő forgalom a virtuális gépről a sávszélesség a caps teljesülnek az Azure Stacken, amelyek megfelelnek az Azure korlátai vannak. Storage access-bérlők a tárolási erőforrások, az Azure Stack-erőforrások egyszerű overconsumption elkerülése érdekében történtek tárolási IOPS-korlátok.

>[!IMPORTANT]
>A [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) nem fontolja meg, és nem garantálja a IOPS teljesítményt.

### <a name="vm-placement"></a>Virtuális gépek elhelyezése

Az Azure Stackben bérlői Virtuálisgép-Elhelyezés a végzi el automatikusan az elhelyezési motor rendelkezésre álló gazdagép között. A virtuális gépek elhelyezésekor csak két szempontok vannak, hogy nincs elég memória a gazdagépen a virtuális gép típusát, és ha a virtuális gépek egy részét képezik- [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) vagy [a virtual machine scale sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  

Magas rendelkezésre állás az Azure Stackben több virtuális gépre kiterjedő gyártási rendszer, virtuális gépek kerülnek egy rendelkezésre állási csoportban, a azokat több tartalék tartomány között. Tartalék tartomány egy rendelkezésre állási csoportban van definiálva egy csomópontot a skálázási egységben. Az Azure Stack támogatja a rendelkezésre állási csoport, amely legfeljebb három tartalék tartományt az Azure-ral konzisztens kellene. Virtuális gépeket egy rendelkezésre állási csoportot helyezett osztja szét őket lehető legegyenletesebben több tartalék tartomány, azt jelenti, az Azure Stack-gazdagépeken keresztül lesz fizikailag különítve egymástól. Hardverhiba esetén a sikertelen tartalék tartomány virtuális gépeket fogja indítani a többi tartalék tartományban, de, külön tartalék tartományokban, ha lehetséges, a más virtuális gépek ugyanazon rendelkezésre állási csoportban tartani. Ha ismét online elérhető a gazdagép, virtuális gépek fog rebalanced magas rendelkezésre állás fenntartása érdekében.  

Virtuális gépek méretezési vissza a feladatokat a rendelkezésre állási készletek használata befejezése, és győződjön meg arról, hogy minden virtuális gép méretezési csoport példánya kerül egy különböző tartalék tartományban. Ez azt jelenti, hogy külön Azure Stack-infrastruktúra csomópontokat használnak. Például az Azure Stack system, 4 csomópont lehet egy olyan helyzetet, ahol egy virtuálisgép-méretezési 3-példányok létrehozáskor 3 virtuális gép méretezési csoport példányaihoz helyezi 3 külön Azure Stack-csomópont 4 – csomópont-kapacitás hiánya miatt meghiúsul. Emellett az Azure Stack-csomópontok tölthető különböző szintű elhelyezési kísérlet előtt. 

Az Azure Stack túlterhelt nem véglegesíthető a memória. Azonban a fizikai Processzormagok számát egy túlzott véglegesítés engedélyezett. Elhelyezési algoritmus nem tekintse meg a meglévő virtuális és fizikai mag fölösleges üzembe helyezési arány tényezőként, mivel minden állomás egy eltérő arány rendelkezhet. Microsoft nem nyújtunk útmutatást az fizikai-virtuális mag arány a számítási feladatok és a szolgáltatás követelményeinek változása miatt. 

### <a name="azure-stack-memory"></a>Az Azure Stack memória 

Az Azure Stack, hogy a futó virtuális gépek, sikeresen kiépített tervezték. Például ha egy gazdagép egy hardverhiba miatt offline állapotban, az Azure Stack megkísérli egy másik gazdagépre a virtuális gép újraindítása. A második példa a javítás- és az Azure Stack szoftver frissítése. Ha egy fizikai gazdagép újraindítása szükséges, egy kísérlet történik helyezze át a virtuális gépek, amelyeken a megoldás érhető el egy másik gazdagépére végrehajtása.   

A virtuális gép kezelése vagy a mozgás érhető el, csak ha fenntartott memória-kapacitás, hogy az újraindítás vagy az áttelepítés történjen. Gazdagép teljes memóriájának egy részét a fenntartott és a bérlői virtuális gép elhelyezésre nem érhető el. 

A kördiagram, amely az Azure Stackben jeleníti meg az ingyenes és a használt memória a felügyeleti portálon tekintheti meg. Az alábbi ábrán a fizikai memória-kapacitás az Azure Stack skálázási egység az Azure stack a látható:

![Fizikai memória-kapacitás](media/azure-stack-capacity-planning/physical-memory-capacity.png)

Használt memória több összetevőből épül fel. A következő összetevőket a tortadiagram használata szakaszában a memóriát használnak fel.  

- Gazdagép operációs Rendszeréhez használati vagy tartalék – Ez a gazdagép, virtuális memória lap táblákat, a gazda operációs rendszer és a közvetlen tárolóhelyek memória-gyorsítótár a futó folyamatok, az az operációs rendszer (OS) által felhasznált memória. 
- Infrastruktúra-szolgáltatások – ezek azok az Azure Stack alkotó virtuális gépek infrastruktúra. Az Azure Stack 1902 kibocsátási verziókban, kezdődően ez maga után vonja 31 is igénybe vehet 242 GB-os virtuális gépek + (4 GB-os csomópont x). Ez a belső struktúra lehetővé teszi, hogy a jövőbeli bevezetése új infrastruktúra-szolgáltatások, azok lettek kifejlesztve.
- Rugalmasság tartalék – Azure Stack foglal le, hogy a virtuális gépek a sikeres működés közbeni áttelepítése közben egyetlen gazdagép hibája esetén, valamint javítási és a frissítés során a bérlő rendelkezésre állás érdekében engedélyezéséhez memóriájának egy részét. 
- Bérlő virtuális gépek – ezek azok a bérlői virtuális gépeket az Azure Stack-felhasználók által létrehozott. Virtuális gépek futtatása mellett memória olyan virtuális gépek, amelyek a hálón jut használja fel. Ez azt jelenti, hogy a virtuális gépek **létrehozása** vagy **sikertelen** állapot, vagy állítsa le a belül a Vendég virtuális gépek memóriát használnak fel. Virtuális gépek felszabadítva beállítás felszabadítása leállítását használatával lett azonban nem használnak fel az Azure Stack memória. 

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

> [!NOTE]
> Hálózati kapacitás tervezési követelményei minimálisak, mivel csak a nyilvános virtuális IP-cím mérete konfigurálható. Több nyilvános IP-címek hozzáadása az Azure Stackhez kapcsolatos információkért lásd: [nyilvános IP-címek hozzáadása](azure-stack-add-ips.md).

## <a name="azure-stack-storage"></a>Az Azure Stack-tároló 
Az alábbi szakaszok az Azure Stack tárolókapacitást biztosítanak tervezési információkat a megoldások tárolási szükségletek segítség.

### <a name="uses-and-organization-of-storage-capacity"></a>Használja, és a szervezet a tárolási kapacitás
Az Azure Stack hiperkonvergens konfigurációja lehetővé teszi a fizikai tárolóeszközök megosztását. A rendelkezésre álló tár három fő egységét vannak, az infrastruktúra, a bérlői virtuális gépek ideiglenes tároló és a tárolási, biztonsági a blobok, táblák és üzenetsorok az Azure-konzisztens tároló (ACS) szolgáltatások között.

### <a name="spaces-direct-cache-and-capacity-tiers"></a>Közvetlen tárolóhelyek gyorsítótárának és kapacitásszintek
Nincs a használt tárolási kapacitást az operációs rendszer, a helyi naplózás, a memóriaképek és a többi ideiglenes infrastruktúra tárolási igényeinek megfelelően. A helyi tárolási kapacitása (eszközökön és a kapacitás) a közvetlen tárolóhelyek szolgáltatás konfigurációjának kezelése alatt álló tárolóeszközök elkülönítése. A tárolóeszközök további része a tárolókapacitást a skálázási egységben kiszolgálók számától függetlenül egyetlen készlet kerül. Ezek az eszközök két típusa van: Gyorsítótár és a kapacitás.  A gyorsítótár-eszközök csak a - gyorsítótár lesznek. Közvetlen tárolóhelyek ezeknek az eszközöknek a késleltetve visszaírt használják, és olvasási gyorsítótárazás. A kapacitások ilyen gyorsítótár eszközt használja, amíg nem kerülnek át a formázott virtuális lemezek formázott, "látható" kapacitását. A kapacitáseszközöknek erre a célra használják, és adja meg a tárolóhelyek szolgáltatás által kezelt adatok "otthoni régiója".

Az összes tárolási kapacitás lefoglalt, és közvetlenül az Azure Stack-infrastruktúra kezel. Az operátor kell kiosztása, a konfigurációval kapcsolatos döntéseket vagy választási lehetőségek kezelése esetén, egyszerű kapacitásbővítést téve lehetővé. Ezek a tervezési döntések vezettük be, hogy összhangba kerüljenek a megoldás követelményeinek, és automatizált vagy kezdeti telepítési/üzembe helyezés során, vagy során egyszerű kapacitásbővítést téve lehetővé. A rugalmasság, újraépíteni fenntartott kapacitás és egyéb részletek részleteit vették a terv részeként. 

Kezelők vagy az összes flash, vagy egy hibrid tárolási konfiguráció közül választhat:

![Az Azure storage kapacitásának megtervezése](media/azure-stack-capacity-planning/storage.png)

Az összes flash konfigurációjában a konfiguráció lehet egy kétrétegű vagy egyrétegű konfigurációt.  Ha a konfiguráció egyrétegű, minden kapacitáseszközök azonos típusú (pl. NVMe vagy SATA SSD vagy SAS SSD) lesz, és gyorsítótárazó eszközöket nem használ. Egy kétrétegű minden flash konfiguráció, a tipikus konfigurációja NVMe, mint a gyorsítótár-eszközként, és ezután vagy SATA vagy a kapacitás eszközként SAS SSD-kkel.

A hibrid, kétrétegű konfiguráció, a gyorsítótár, NVMe, a SATA vagy SAS SSD és a kapacitás választhat HDD. 

Röviden összefoglalva a közvetlen tárolóhelyek és az Azure Stack tárolási konfigurációt a következőképpen történik:
- Egy szóközt tárolókészlet skálázási egység (az összes tárolóeszköz egyetlen belül vannak konfigurálva)
- Virtuális lemezek jönnek létre három példányt tükör a legjobb teljesítmény és rugalmasság
- Minden egyes virtuális lemez vannak formázva, a ReFS fájlrendszer
- Virtuális lemezkapacitás kiszámítása és lefoglalt úgy, hogy hagyja meg az adatok kapacitás a készletben lévő szabad egy kapacitás-eszköz mennyiségét. Ez megegyezik a Kiszolgálónként egy kapacitás-meghajtó.
- Minden egyes ReFS fájlrendszeren lesz engedélyezve az inaktív adatok titkosítása a BitLocker. 

A virtuális – a lemezek automatikusan létrehozza és kapacitásuk a következők:

|Name (Név)|A számítási kapacitás|Leírás|
|-----|-----|-----|
|Helyi vagy rendszerindító eszköz|340 GB-os minimális<sup>1</sup>|Egyes kiszolgálói tárhelyet operációsrendszer-lemezképek, a "local" infrastruktúra virtuális gépeinek|
|Infrastruktúra|3,5 TB|Minden Azure Stack-infrastruktúra használata|
|VmTemp|Lásd alább<sup>2</sup>|Bérlői virtuális gépeket egy ideiglenes lemez csatolva van, és ezek a virtuális lemezek tárolja az adatokat|
|ACS|Lásd alább <sup>3</sup>|Blobok, táblák és üzenetsorok karbantartási Azure konzisztens tárolási kapacitás|

<sup>1</sup> minimális tárolási kapacitást az Azure Stack megoldás partner szükséges.

<sup>2</sup> a bérlői virtuális gép ideiglenes lemezek használt virtuális lemez mérete számítjuk ki, hogy a kiszolgáló fizikai memória arány. Az alábbi táblázatokban az Azure IaaS Virtuálisgép-méretek feljegyzett ideiglenes lemez a virtuális géphez rendelt fizikai memória arány. A hozzárendelés kész "ideiglenes lemez" tárolás az Azure Stackben úgy, hogy a legtöbb használati esetek rögzítése végezheti el, de nem tudják kielégíteni minden ideiglenes lemez tárolási igényeinek megfelelően. Kiválasztott arány, az ideiglenes tároló elérhetővé tétele közben nem használja fel a tárolási kapacitás, a megoldás csak ideiglenes lemez kapacitás többsége között. A skálázási egységben kiszolgálónként egy ideiglenes tárolólemez jön létre. Az ideiglenes tárolási kapacitása 10 % a teljes elérhető kapacitást a tárolókészletben, a skálázási egység nem növekszik. A számítása a következőképpen fog kinézni a következő példa:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> a virtuális – ACS által használni létrehozott lemezei a fennmaradó kapacitás egy egyszerű osztás. Feljegyzett, az összes virtuális lemez egy háromutas tükrözött-e, és a egy kapacitás-meghajtó tekinthető meg minden olyan kiszolgáló kapacitásának le nem foglalt. A különböző virtuális lemezek fent felsorolt először vannak lefoglalva, és a fennmaradó kapacitás az ACS virtuális-lemezeit használja.

## <a name="azure-stack-capacity-planner"></a>Az Azure Stack Capacity Planner
Az Azure Stack Capacity Planner egy táblázatot, amely megjeleníti a számítási erőforrások különböző hozzárendelések helyezze egy kijelölt hardverek között. 

### <a name="worksheet-descriptions"></a>Munkalap leírása
Az alábbi táblázat minden egyes munkalap leírása szerepel az Azure Stack Capacity Planner, amely letölthető a [ https://aka.ms/azstackcapacityplanner ](https://aka.ms/azstackcapacityplanner). 

|Lap neve|Leírás|
|-----|-----|
|Verzió-jogi nyilatkozat|A kalkulátor, a verziószámot és a kiadási dátum célja.|
|Utasítások|Részletes utasításokat követve modell kapacitás megtervezése a virtuális gépek gyűjteményét.|
|DefinedSolutionSKUs|Legfeljebb öt hardver definíciók tartalmazó tábla. A bejegyzések példák. A részleteket a szóban forgó rendszerkonfigurációk megfelelően módosítsa.|
|DefineByVMFootprint|Keresse meg a megfelelő hardver SKU összehasonlítja a különböző méretekre és virtuális gépek mennyiségét konfigurációkat.|
|DefineByWorkloadFootprint|Keresse meg a megfelelő hardver SKU hoz létre az Azure Stack-munkaterhelések gyűjteménye.|
|  |  |

### <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs utasításokat
Ezen a munkalapon legfeljebb öt hardver definíció példákat tartalmaz. Módosítsa megfelelően a rendszer konfigurációját a szóban forgó részleteit.

#### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Hardver-beállításokat, jogosult a hardvergyártó partnerektől által biztosított
Az Azure Stack egy integrált rendszer megoldási partnerei által telepített szoftverekkel rendelkező érkeznek. Megoldáspartnerek adja meg a saját Azure Stack kapacitástervező eszközök mérvadó verzióit. Használja ezeket az eszközöket a végső discussions megoldás kapacitás.

#### <a name="multiple-ways-to-model-computing-resources"></a>Több lehetőség is modell számítási erőforrások
Az Azure Stack planner belül modellezési erőforrás attól függ, hogy a különböző méretű Azure Stack-beli virtuális. Akár a legnagyobb Standard_Fsv2 legkisebb alapszintű 0 méretű virtuális gépek tartománya. Két különböző módon modellezheti számítási erőforrás-hozzárendelések:

- Jelöljön ki egy adott hardverekhez ajánlat, és tekintse meg a különböző erőforrások mely kombinációit fér. 

- Hozzon létre Virtuálisgép-hozzárendelések egyedi kombinációját, és lehetővé teszik az Azure-erőforrás Díjkalkulátorunkkal megjelenítése, amely rendelkezésre álló termékváltozatok a következők támogathatja a Virtuálisgép-konfiguráció.

Ez az eszköz a Virtuálisgép-erőforrások kiosztásával kétféle módszert biztosít: a virtuális gép erőforrás-hozzárendelések egy egyetlen gyűjtemény vagy legfeljebb hat válaszolhat a különböző számítási feladatok konfigurációk gyűjteménye. Minden egyes számítási feladatok konfigurációjának tartalmazhat egy másik elérhető Virtuálisgép-erőforrások lefoglalását. A következő szakaszokban részletesen hozhat létre, és lefoglalási modellek az egyes rendelkezik. Csak érték nem háttérben lévő árnyékolt cellák, vagy Termékváltozat legördülő listák a munkalap belül kell módosítani. Árnyékolt cellák belül végrehajtott módosítások erőforrás számítások megszakadhat.


### <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint utasításokat
A különböző méretű és virtuális gépek mennyiségét egy egyetlen gyűjtemény használata modell létrehozásához, válassza az "DefineByVMFootprint" lapot, és kövesse az alábbi lépéseket:

1. Ez a munkalap jobb felső sarokban a legördülő listában szerepelnek vezérlők segítségével válassza ki a egy kezdeti szám (4 és 16) között kiszolgálók telepíteni kívánt minden egyes hardver rendszerben (Termékváltozat). A kiszolgálók számának megtekintéséhez, hogyan befolyásolja ez összességében a modellezési folyamat során bármikor módosítható az erőforrás-elosztási modellt számára elérhető erőforrások.
2. Ha azt szeretné, a modell a különböző virtuális gépek erőforrás-hozzárendelések egy adott hardverkonfiguráción ellen, a kék legördülő lista közvetlenül az "Aktuális Termékváltozat" címke alatt található az oldal jobb felső sarkában. Kérje le a lista, és válassza ki a kívánt hardver SKU.
3. Most már készen áll, kezdje el hozzáadni a különböző méretű virtuális gépeket a modell az. Adja meg egy adott virtuális gép típusát, írjon be egy mennyiség értéket, a kék bekeretezett bal oldalán, a virtuális gép bejegyzést.

   > [!NOTE]
   > Virtuális gép tárterületének teljes kapacitását az adatlemezt a virtuális gép hivatkozik (a támogatott lemezek száma * (1 TB) egyetlen lemez maximális kapacitása). A konfigurációs mutatók alapján azt ki vannak töltve a rendelkezésre álló tárolási konfigurációk táblában, így előfordulhat, hogy válassza ki a kívánt szint tárolási erőforrás minden egyes Azure Stack virtuális gép. Azonban fontos megjegyezni, hogy adhatók hozzá, vagy a rendelkezésre álló tárolási konfigurációk tábla szükség szerint módosítsa.<br><br>Minden virtuális gép egy kezdetben hozzárendelt helyi ideiglenes tárhely kezdődik. Ideiglenes tárterület kiosztás megfelelően a helyi-temp szám szeletének a legördülő menüből, beleértve a maximális megengedett ideiglenes tárterület-mennyiség módosítható.

4. Virtuális gépek hozzáadásakor, látni fogja a diagramok, amelyek megmutatják a rendelkezésre álló SKU-erőforrások módosítása. Ez lehetővé teszi különböző méretű és tranzakciómennyiségek, amelyek virtuális gépek hozzáadása a modellezési folyamat során a hatásukat. Változások megtekintése egy másik úgy, hogy tekintse meg a consumed oszlopban látható, és továbbra is elérhető számok közvetlenül alábbi elérhető virtuális gépek listáját. Ezek a számok az aktuálisan kiválasztott hardver SKU alapuló becsült értékek tükrözik.
5. Miután létrehozta a virtuális gépek csoportját, megtalálhatja a javasolt hardver SKU közvetlenül az "Aktuális Termékváltozat" címke alatt az oldal jobb felső sarokban található "Javasolt Termékváltozat" gombra kattintva. Ez a gomb használata esetén, módosíthatja a Virtuálisgép-konfigurációk és hardver támogatja-e az egyes konfigurációkhoz.


### <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint instructions
Létrehoz egy modellt használ az Azure Stack-munkaterhelések gyűjteménye, válassza a "DefineByWorkloadFootprint" lapot, és hajtsa végre a feladatütemezési lépéseket. Az Azure Stack számítási feladatok rendelkezésre álló Virtuálisgép-erőforrások használatával jön létre.   

> [!TIP]
> Egy Azure Stack-beli virtuális gép a megadott tárfiók méretének módosításához tekintse meg a megjegyzést. az előző szakaszban három lépésben.

1. Ez az oldal jobb felső sarokban a legördülő listában szerepelnek vezérlők segítségével válassza ki a egy kezdeti szám (4 és 16) között kiszolgálók telepíteni kívánt minden egyes hardver rendszerben (Termékváltozat).
2. Ha azt szeretné, a modell a különböző virtuális gépek erőforrás-hozzárendelések egy adott hardverkonfiguráción ellen, a kék legördülő lista közvetlenül az "Aktuális Termékváltozat" címke alatt található az oldal jobb felső sarkában. Kérje le a lista, és válassza ki a kívánt hardver SKU.
3. Válassza ki a megfelelő tárolási méretét a kívánt Azure Stack-beli virtuális DefineByVMFootprint lapon minden egyes DefineByVMFootprint utasításokat harmadik lépése a fentieknek megfelelően. A tárhelyméretet a virtuális gép a DefineByVMFootprint lap van definiálva.
4. A lap bal felső sarkában a DefineByWorkloadFootprint kezdődően létrehozásához konfigurációk legfeljebb hat különböző számítási feladatok esetében minden egyes VM-típus található, hogy a számítási feladatok mennyisége. Ez történik, numerikus értékeket közvetlenül az adott számítási feladat neve alatt az oszlopba való elhelyezésével. Számítási feladat nevét módosítani lehet, hogy milyen típusú számítási feladatok, amelyek ezt a konfigurációt támogat.
5. Minden egyes Számításifeladat-típust adott mennyiségű beír egy értéket alján, közvetlenül a "Mennyiség" címke alatt ez az oszlop tartalmazhat.
6. Létrehozása után a számítási feladatok típusa és mennyiség, a "javasolt Termékváltozat" gombra kattintva közvetlenül a "Az aktuális Termékváltozat" címke alatt az oldal jobb felső sarkában található gomb hatására a legkisebb Termékváltozat támogatására a teljes elegendő erőforrással rendelkező számítási feladatok megjeleníteni kívánt konfigurációját.
7. További modellezési végezhető hardveres Termékváltozat, a kiválasztott kiszolgálók számának módosításával vagy módosítása a virtuális gép kiosztásokhoz és mennyiség, a munkaterhelés-konfigurációk belül. A társított grafikonok jelenik meg arról, hogy a a végrehajtott módosítások miként befolyásolják a teljes erőforrás-felhasználásának megjelenítő azonnali visszajelzést.
8. Ha elégedett a módosításokkal, kattintson a **javasolt Termékváltozat** ismét megjelenik az új konfigurációhoz javasolt a Termékváltozat. A legördülő menüben válassza ki a kívánt Termékváltozatát is kattinthat.


## <a name="next-steps"></a>További lépések
További információ a [adatközpont integrációja szempontok az Azure Stackhez](azure-stack-datacenter-integration.md)
