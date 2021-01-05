---
title: Azure Stack hub számítási kapacitása
description: További információ a Azure Stack hub üzembe helyezésének számítási kapacitásának megtervezéséről.
author: PatAltimore
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: prchint
ms.lastreviewed: 06/13/2019
ms.openlocfilehash: 8d1d6c6da0e11278b2b7ce796ca3dffd77385e81
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871501"
---
# <a name="azure-stack-hub-compute-capacity"></a>Azure Stack hub számítási kapacitása

Az Azure Stack hub által támogatott [virtuális gépek (VM-EK) mérete](../user/azure-stack-vm-sizes.md) az Azure-ban támogatottak egyik részhalmaza. Az Azure erőforrás-korlátozásokat vezet be számos vektoron az erőforrások túlfelhasználásának elkerüléséhez (a kiszolgáló helyi és szolgáltatási szintjén). A bérlői felhasználás korlátozásai nélkül a bérlői élmények akkor fognak szenvedni, ha más bérlők túlterhelik az erőforrásokat. A virtuális gépről kifelé irányuló hálózati forgalomhoz az Azure-korlátozásoknak megfelelő Azure Stack hub-sávszélesség van érvényben. Az Azure Stack hub tárolási erőforrásai esetében a tárolási IOPS korlátokat biztosít a bérlők által a tárolók eléréséhez szükséges alapszintű erőforrások felhasználásának elkerülése érdekében.

>[!IMPORTANT]
>Az [Azure stack Hub Capacity Planner](https://aka.ms/azstackcapacityplanner) nem veszi figyelembe vagy garantálja a IOPS teljesítményét.

## <a name="vm-placement"></a>Virtuális gépek elhelyezése

Az Azure Stack hub elhelyezési motorja a bérlői virtuális gépeket a rendelkezésre álló gazdagépeken helyezi át.

Azure Stack hub két szempontot használ a virtuális gépek elhelyezésekor. Lehet, hogy van elég memória a gazdagépen a virtuális gép típusához? És kettő, a virtuális gépek egy [rendelkezésre állási csoport](/azure/virtual-machines/windows/manage-availability) részét képezik, vagy [virtuálisgép-méretezési](/azure/virtual-machine-scale-sets/overview)csoportok?

A Azure Stack hub több virtuális gépre kiterjedő számítási feladatainak magas rendelkezésre állása érdekében a virtuális gépek (VM-EK) egy rendelkezésre állási csoportba kerülnek, amely több tartalék tartományon keresztül terjed ki. Egy rendelkezésre állási csoport tartalék tartománya egyetlen csomópontként van definiálva a skálázási egységben. Azure Stack hub támogatja a rendelkezésre állási csoport legfeljebb három tartalék tartománnyal való egységességét az Azure-ban. A rendelkezésre állási csoportba helyezett virtuális gépeket fizikailag el kell különíteni egymástól a több tartalék tartomány (Azure Stack hub-csomópontok) lehető legegyenletesebb kiterjedésével. Hardverhiba esetén a sikertelen tartalék tartományba tartozó virtuális gépek más tartalék tartományokban lesznek újraindítva. Ha lehetséges, külön tartalék tartományokban lesznek tárolva ugyanazon rendelkezésre állási csoportba tartozó többi virtuális gépről. Ha a gazdagép online állapotba kerül, a virtuális gépek újra lesznek egyenlítve a magas rendelkezésre állás fenntartása érdekében.  

A virtuálisgép-méretezési csoportok a háttérbeli rendelkezésre állási csoportokat használják, és minden virtuálisgép-méretezési csoport példánya egy másik tartalék tartományba kerül. Ez azt jelenti, hogy külön Azure Stack hub infrastruktúra-csomópontokat használnak. Például egy négy csomópontos Azure Stack hub rendszer esetén előfordulhat, hogy egy három példányból álló virtuálisgép-méretezési csoport nem sikerül a létrehozás során, mert a négy csomópontos kapacitás három különálló Azure Stack hub-csomóponton helyezi el a három virtuálisgép-méretezési csoport példányát. Emellett Azure Stack hub-csomópontok különböző szinteken tölthetők fel az elhelyezés megkísérlése előtt.

Azure Stack hub nem véglegesíti a memóriát. A fizikai magok számának túlérvényesítése azonban engedélyezett.

Mivel az elhelyezési algoritmusok nem tekintik meg a meglévő virtuális fizikai mag feletti túlépítési arányt tényezőként, az egyes gazdagépek eltérő aránnyal rendelkezhetnek. A Microsoft a számítási feladatok és a szolgáltatási szint követelményeinek változása miatt nem biztosítunk útmutatást a fizikai – virtuális alapvető arányhoz.

## <a name="consideration-for-total-number-of-vms"></a>A virtuális gépek teljes számának figyelembevétele

Az Azure Stack hub kapacitásának pontos megtervezése új szempont. Az 1901-es frissítéssel (és minden további frissítéssel) mostantól a létrehozható virtuális gépek teljes száma korlátozva lesz. Ez a korlát ideiglenes lehet a megoldás instabilitásának elkerülése érdekében. A stabilitási probléma forrása nagyobb számú virtuális gépen, de a szervizeléshez megadott ütemterv nem lett meghatározva. A 60-os virtuális gépek száma mostantól a 700-es teljes megoldási korláttal rendelkezik. Tegyük fel például, hogy egy nyolc kiszolgáló Azure Stack hub virtuális gép korlátja 480 (8 * 60). 12 – 16 kiszolgáló Azure Stack hub-megoldás esetén a korlát 700. Ez a korlát a számítási kapacitással kapcsolatos megfontolásokat hozta létre, például a rugalmassági tartalékot és a CPU virtuális – fizikai arányát, amelyet egy operátor a bélyegzőn szeretne fenntartani. További információ: a Capacity Planner új kiadása.

Ha elérte a virtuális gép méretezési korlátját, a rendszer a következő hibakódokat adja vissza eredményként: `VMsPerScaleUnitLimitExceeded` , `VMsPerScaleUnitNodeLimitExceeded` .

## <a name="consideration-for-batch-deployment-of-vms"></a>A virtuális gépek kötegelt üzembe helyezésének megfontolása

Az 2002-es és újabb verziók, valamint a kötegek közötti, 5 perces eltéréssel rendelkező kötegekben 2-5 VM-es kiadásokban megbízható virtuálisgép-üzembe helyezést biztosítanak a 700-os virtuális gépek méretének eléréséhez. Az Azure Stack hub 2005-es verziójával megbízhatóan kiépíthető a virtuális gépek száma a 40-es batch-méretekben, 5 perces eltéréssel a Batch-telepítések között.

## <a name="azure-stack-hub-memory"></a>Azure Stack hub memóriája

Azure Stack hub úgy van kialakítva, hogy megőrizze a sikeresen kiépített virtuális gépeket. Ha például egy gazdagép hardverhiba miatt offline állapotba kerül, Azure Stack hub megkísérli újraindítani a virtuális gépet egy másik gazdagépen. Egy második példa az Azure Stack hub szoftver javítása és frissítése során. Ha a fizikai gazdagép újraindítására van szükség, a rendszer kísérletet tesz arra, hogy a gazdagépen futó virtuális gépeket egy másik elérhető gazdagépre helyezze át a megoldásban.

Ez a virtuálisgép-kezelés vagy-áthelyezés csak akkor érhető el, ha az újraindítást vagy az áttelepítést lehetővé tevő foglalt memória kapacitása engedélyezve van. A teljes gazdagép memóriájának egy része le van foglalva, és nem érhető el a bérlői virtuális gép elhelyezéséhez.

A felügyeleti portálon áttekintheti a tortadiagramot, amely megjeleníti Azure Stack hub szabad és használt memóriáját. Az alábbi ábrán a Azure Stack hub Azure Stack hub skálázási egységének fizikai memória-kapacitása látható:

![Fizikai memória kapacitása egy Azure Stack hub skálázási egységen](media/azure-stack-capacity-planning/physical-memory-capacity.png)

A felhasznált memória több összetevőből áll. A tortadiagram használat szakaszában a következő összetevők használják a memóriát:  

- **Gazda operációs rendszer használata vagy tartalék:** Az operációs rendszer által a gazdagépen, a virtuális memória lapján, a gazdagép operációs rendszere és a közvetlen tárolóhelyek memóriájának gyorsítótára által használt memória. Mivel ez az érték függ a gazdagépen futó különböző Hyper-V-folyamatok által használt memóriától, az képes ingadozni.
- **Infrastruktúra-szolgáltatások:** Az Azure Stack hub-t alkotó infrastruktúra-alapú virtuális gépek. Az Azure Stack hub 1904-es kiadási verziójától kezdve ez olyan ~ 31 virtuális gépeket foglal magában, amelyek a memóriából 242 GB-ot (4 GB x # csomópontot) vesznek igénybe. Az infrastruktúra-szolgáltatások összetevő memóriájának kihasználtsága változhat, ahogy az infrastrukturális szolgáltatások méretezhetőségének és rugalmasságának hatékonyabbá tétele.
- **Rugalmassági tartalék:** Azure Stack hub fenntartja a memória egy részét, amely lehetővé teszi a bérlők rendelkezésre állását egy gazdagép meghibásodása során, valamint a javítás és a frissítés során, hogy lehetővé váljon a virtuális gépek sikeres élő áttelepítése.
- **Bérlői virtuális gépek:** Azure Stack hub-felhasználók által létrehozott bérlői virtuális gépek. A virtuális gépeken kívül minden olyan virtuális gép felhasznál memóriát, amely a hálóra van kiképezve. Ez azt jelenti, hogy a "létrehozás" vagy "sikertelen" állapotú virtuális gépek, illetve a virtuális gépek a vendégen belülről leállnak, memóriát fognak használni. A portálról/PowerShell-ből vagy a CLI-ből a delefoglalt leállítás lehetőség használatával felhasználható virtuális gépek azonban nem fogják használni a memóriát Azure Stack hub-ból.
- **Érték – erőforrás-szolgáltatók hozzáadása (RPs):** Az értékhez telepített virtuális gépek, például az SQL, a MySQL, a App Service és így tovább.

A portálon a memória-használat megértésének legjobb módja az [Azure stack Hub Capacity Planner](https://aka.ms/azstackcapacityplanner) használata a különböző számítási feladatok hatásának megtekintéséhez. A Planner a következő számítást használja.

Ez a számítás a bérlői virtuális gépek elhelyezéséhez használható teljes rendelkezésre álló memóriát eredményezi. Ez a memória kapacitása a Azure Stack hub skálázási egység teljes egészében használható.

Rendelkezésre álló memória a virtuális gép elhelyezéséhez = teljes gazda memória – rugalmassági tartalék – a bérlői virtuális gépek futtatásához használt memória (a Azure Stack hub infrastruktúra terhelése <sup>1</sup>

Rugalmassági tartalék = H + R * ((N-1) * H) + V * (N-2)

> Kimenet:
> -    H = az egyetlen kiszolgáló memóriájának mérete
> - N = a méretezési egység mérete (kiszolgálók száma)
> -    R = az operációsrendszer-terheléshez tartozó operációs rendszer tartaléka, amely a<sup>2</sup> . képletben .15
> -    V = a méretezési egység legnagyobb virtuális gépe

<sup>1</sup> Azure stack hub-infrastruktúra terhelése = 242 GB + (4 GB x # csomópontok száma). A rendszer körülbelül 31 virtuális gépet használ a Azure Stack hub infrastruktúrájának üzemeltetéséhez, és összesen a memória és a 146 virtuális mag 242 GB + (4 GB x # csomópont) mennyiségét használja fel. A virtuális gépekre vonatkozó ésszerűség az, hogy kielégítse a szükséges szolgáltatások elkülönítését a biztonság, a méretezhetőség, a karbantartás és a javítási követelmények kielégítése érdekében. Ez a belső szolgáltatási struktúra lehetővé teszi az új infrastrukturális szolgáltatások jövőbeli bevezetését a fejlesztésük során.

<sup>2</sup> az operációs rendszer tartaléka a csomópont-memória 15%-ában (. 15). Az operációs rendszer foglalásának értéke becslés, és a kiszolgáló fizikai memória-kapacitása és az általános operációs rendszer terhelése alapján változhat.

A skálázási egységben a V, a legnagyobb méretű virtuális gép a legnagyobb bérlői virtuálisgép-memória méretétől függ. A legnagyobb virtuálisgép-érték például lehet 7 GB vagy 112 GB, vagy bármely más támogatott virtuálisgép-memória mérete az Azure Stack hub-megoldásban. Ha megváltoztatja a Azure Stack hub-háló legnagyobb virtuális gépét, a rugalmasságot és a virtuális gép memóriájának növekedését is eredményezi.

## <a name="considerations-for-deallocation"></a>A felszabadítás szempontjai

Ha egy virtuális gép a _delefoglalt_ állapotban van, a memória erőforrásai nincsenek használatban. Ez lehetővé teszi, hogy más virtuális gépek a rendszerbe kerüljenek.

Ha a visszafoglalt virtuális gép újraindul, a memóriahasználat vagy a foglalás úgy lesz kezelve, mint a rendszerbe helyezett új virtuális gép, és a rendelkezésre álló memória felhasználható. Ha nincs elérhető memória, a virtuális gép nem indul el.

Az aktuálisan telepített nagyméretű virtuális gépek azt mutatják, hogy a lefoglalt memória 112 GB, de ezeknek a virtuális gépeknek a memória iránti igénye körülbelül 2-3 GB.
    
| Név | Hozzárendelt memória (GB) | Memória igénye (GB) | ComputerName |  
| ---- | -------------------- | ------------------ | ------------ |                                        
| ca7ec2ea-40fd-4d41-9d9b-b11e7838d508 |                 112  |     2.2392578125  |  LISSA01P-NODE01 |
| 10cd7b0f-68f4-40ee-9d98-b9637438ebf4  |                112  |     2.2392578125  |   LISSA01P-NODE01 |
| 2e403868-ff81-4abb-b087-d9625ca01d84   |               112   |    2.2392578125  |   LISSA01P-NODE04 |

A virtuális gépek elhelyezésének a képlet **rugalmassága Reserve = H + R * ((n-1) * h) + V * (N-2)** értékkel való felszabadításának három módja van:
* Csökkentse a legnagyobb méretű virtuális gép méretét
* Csomópont memóriájának bővítése
* Csomópont hozzáadása

### <a name="reduce-the-size-of-the-largest-vm"></a>Csökkentse a legnagyobb méretű virtuális gép méretét 

A legnagyobb méretű virtuális gép méretének csökkentése a Stamp (24 GB) következő legkisebb virtuális géphez csökkenti a rugalmassági tartalék méretét.

![A virtuális gép méretének csökkentése](media/azure-stack-capacity-planning/decrease-vm-size.png)        
        
 Rugalmassági foglalás = 384 + 172,8 + 48 = 604,8 GB
        
| Teljes memória | Infra GB | Bérlői GB | Rugalmassági tartalék | Lefoglalt memória összesen          | Az elhelyezéshez rendelkezésre álló GB összesen |
|--------------|--------------------|---------------------|--------------------|--------------------------------|----------------------------------|
| 1536 GB      | 258 GB             | 329,25 GB           | 604,8 GB           | 258 + 329,25 + 604,8 = 1168 GB | **~ 344 GB**                         |
     
### <a name="add-a-node"></a>Csomópont hozzáadása

[Azure stack hub-csomópont hozzáadásával](./azure-stack-add-scale-node.md) felszabadítja a memóriát a két csomópont közötti egyenlő elosztással.

![Csomópont hozzáadása](media/azure-stack-capacity-planning/add-a-node.png)

Rugalmassági foglalás = 384 + (0,15) ((5) * 384) + 112 * (3) = 1008 GB
    
| Teljes memória | Infra GB | Bérlői GB | Rugalmassági tartalék | Lefoglalt memória összesen          | Az elhelyezéshez rendelkezésre álló GB összesen |
|--------------|--------------------|---------------------|--------------------|--------------------------------|----------------------------------|
| 1536 GB      | 258 GB             | 329,25 GB           | 604,8 GB           | 258 + 329,25 + 604,8 = 1168 GB | **~ 344 GB**                         |

### <a name="increase-memory-on-each-node-to-512-gb"></a>Növelje az egyes csomópontok memóriáját 512 GB-ra

Az [egyes csomópontok memóriájának növelése](./azure-stack-manage-storage-physical-memory-capacity.md) növeli a teljes rendelkezésre álló memóriát.

![A csomópont méretének növeléséhez](media/azure-stack-capacity-planning/increase-node-size.png)

Rugalmassági foglalás = 512 + 230,4 + 224 = 966,4 GB
    
| Teljes memória    | Infra GB | Bérlői GB | Rugalmassági tartalék | Lefoglalt memória összesen | Az elhelyezéshez rendelkezésre álló GB összesen |
|-----------------|----------|-----------|--------------------|-----------------------|----------------------------------|
| 2048 (4 * 512) GB | 258 GB   | 505,75 GB | 966,4 GB           | 1730,15 GB            | **~ 318 GB**                         |

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K**: a bérlő üzembe helyezett egy új virtuális gépet, mennyi időt vesz igénybe a felügyeleti portál képesség diagramja a fennmaradó kapacitás megjelenítéséhez?

**A**: a kapacitás panel 15 percenként frissül, ezért figyelembe kell vennie.

**K**: Hogyan tekinthetem meg az elérhető magok és a hozzárendelt magok?

**A**: a **PowerShell** futtatásakor a `test-azurestack -include AzsVmPlacement -debug` következőhöz hasonló kimenetet hoz létre:

```console
    Starting Test-AzureStack
    Launching AzsVmPlacement
     
    Azure Stack Scale Unit VM Placement Summary Results
     
    Cluster Node    VM Count VMs Running Physical Core Total Virtual Co Physical Memory Total Virtual Mem
    ------------    -------- ----------- ------------- ---------------- --------------- -----------------
    LNV2-Node02     20       20          28            66               256             119.5            
    LNV2-Node03     17       16          28            62               256             110              
    LNV2-Node01     11       11          28            47               256             111              
    LNV2-Node04     10       10          28            49               256             101              
    
    PASS : Azure Stack Scale Unit VM Placement Summary
```

**K**: a telepített virtuális gépek száma az Azure stack hub-on nem módosult, de a kapacitásom ingadozik. Miért?

**A**: a virtuális gép elhelyezéséhez rendelkezésre álló memória több függőséggel rendelkezik, amelyek közül az egyik a gazda operációs rendszer tartaléka. Ez az érték függ a gazdagépen futó különböző Hyper-V-folyamatok által használt memóriától, amely nem állandó érték.

**K**: milyen állapotban kell lennie a bérlői virtuális gépeknek a memória felhasználásához?

**A**: a virtuális gépeken kívül minden olyan virtuális gép használja a memóriát, amely a hálóra van leképezve. Ez azt jelenti, hogy a "létrehozás" vagy "sikertelen" állapotú virtuális gépek a memóriát használják. A virtuális gépek nem a portálról, hanem a powershellből vagy a parancssori felületről való leállása után állnak le a memóriából.

**K**: van egy négy gazdagép Azure stack hub. A bérlőnek 3 virtuális gép van, amely 56 GB RAM memóriát (D5_v2) használ. Az egyik virtuális gép mérete 112 GB RAM-ra (D14_v2), az irányítópulton rendelkezésre álló memória-jelentéskészítés pedig 168 GB-nyi használatot eredményezett a kapacitás panelen. A másik két D5_v2 virtuális gép későbbi átméretezése D14_v2 eredményezte a 56 GB RAM-ot. Miért van ez?

**A**: a rendelkezésre álló memória a Azure stack hub által fenntartott rugalmassági tartalék függvénye. A rugalmassági tartalék a Azure Stack hub-bélyegző legnagyobb virtuálisgép-méretének függvénye. Eleinte a bélyeg legnagyobb virtuális gépe 56 GB memóriával rendelkezik. A virtuális gép átméretezése után a bélyeg legnagyobb virtuális gépe 112 GB memóriát vett igénybe, amely nem csak növelte a bérlői virtuális gép által használt memóriát, de megnövelte a rugalmassági tartalékot is. Ez a változás a 56 GB-os (56 GB – 112 GB bérlői VM-memória növekedése) és a 112 GB rugalmasság tartalék memória növelését eredményezte. Ha a további virtuális gépek átméretezése megtörtént, a legnagyobb méretű virtuálisgép-méret a 112 GB-os virtuális gép marad, ezért nem volt ilyen mértékű rugalmassági tartalék. A memóriahasználat növekedése csak a bérlői virtuális gép memóriájának növekedését (56 GB) eredményezte.

> [!NOTE]
> A hálózatkezelésre vonatkozó kapacitás-tervezési követelmények minimálisak, mert csak a nyilvános VIP mérete konfigurálható. További információ a nyilvános IP-címek Azure Stack hubhoz való hozzáadásáról: [nyilvános IP-címek hozzáadása](azure-stack-add-ips.md).

## <a name="next-steps"></a>További lépések
További tudnivalók az [Azure stack hub Storage](azure-stack-capacity-planning-storage.md) szolgáltatásról