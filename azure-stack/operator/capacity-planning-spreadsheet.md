---
title: Kapacitástervezési táblázat az Azure Stackhez |} A Microsoft Docs
description: További információ a kapacitástervezési táblázat az Azure Stack központi telepítéséhez.
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
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 1a8bf4a0b0bcd2b09bbb7ea2bc9283a43380a431
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267745"
---
# <a name="azure-stack-capacity-planner"></a>Az Azure Stack Capacity Planner
Az Azure Stack Capacity Planner egy táblázatot, használja az Azure Stack resource kapacitástervezés. A capacity planner lehetőséget nyújt a számítási erőforrások különböző hozzárendelések tervezéséhez, és tekintse meg, hogyan ezek lenne elférjenek hardver ajánlatok kijelölés. Az Azure Stack Számológép használatára vonatkozó részletes útmutatást az alábbiakban találhatók.

## <a name="worksheet-descriptions"></a>Munkalap leírása
Az alábbiakban röviden összefoglalja a munkalapok az Azure Stack Capacity Planner-táblázatot, amely letölthető a található, a [ https://aka.ms/azstackcapacityplanner ](https://aka.ms/azstackcapacityplanner):

|Lap neve|Leírás|
|-----|-----|
|Verzió-jogi nyilatkozat|A kalkulátor, a verziószámot és a kiadott dátum célja rövid áttekintést.|
|Útmutatás|Az Azure Stack Capacity Planner használata részletes útmutatást nyújt.|
|DefinedSolutionSKUs|Többoszlopos tábla, amely legfeljebb öt hardver kapcsolatos definíciókat tartalmazza. Ebben a táblázatban szerepelnek példák. A célja, hogy a felhasználó módosíthatja-e a használatra tekinti a rendszer konfigurációját, vagy vásároljon részletei.|
|DefineByVMFootprint|Keresse meg a megfelelő hardver SKU hozzon létre egy gyűjteményt a különböző méretű és virtuális gépek mennyiségét.|
|DefineByWorkloadFootprint|Keresse meg a megfelelő hardver SKU hoz létre az Azure Stack-munkaterhelések gyűjteménye.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs utasításokat
Ezen a munkalapon legfeljebb öt hardver definíció példákat tartalmaz. Módosítsa megfelelően a rendszer konfigurációját használatra fontolóra részleteit, vagy vásárolja meg.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Hardver-beállításokat, jogosult a hardvergyártó partnerektől által biztosított
Az Azure Stack egy integrált rendszer megoldási partnerei által telepített szoftverekkel rendelkező érkeznek. Ezek megoldási partnerei lesz a saját, Azure Stack kapacitástervezési eszközöket, és ezeket az eszközöket, használandó megoldás kapacitás hozzászólások véglegesítése mérvadó verzióit.

### <a name="multiple-ways-to-model-computing-resources"></a>Több lehetőség is a számítási erőforrások minta
Erőforrás modellezés az Azure Stack planner belül használt alapvető alkotóelemeket olyan Azure Stack virtuális gépek (VM) a különböző méretű. Ezek a legkisebb méret a virtuális gépek tartomány "Alapszintű 0", akár az aktuális legnagyobb VM "Standard_Fsv2." Igényei alapján, válaszolhat a különböző virtuális gépek különböző mennyiségű számítási erőforrás-hozzárendelések ezzel az eszközzel két különböző módon létrehozására használható.

1. A planner-felhasználó egy adott hardverekhez ajánlat kiválasztása, és megkeresheti a különböző erőforrások mely kombinációit felbontásán belül a hardveres erőforrás. 

2. A planner felhasználó hoz létre a virtuális gép hozzárendelések egyedi kombinációját, és lehetővé teszi, hogy az Azure erőforrás-Díjkalkulátorunkkal azt mutatja, amely a rendelkezésre álló termékváltozatok a következők támogathatja a Virtuálisgép-konfiguráció.

Ezzel az eszközzel; Virtuálisgép-erőforrások kiosztásával kétféle módszert biztosít. vagy virtuális gép erőforrás-hozzárendelések egy egyetlen gyűjteményként, vagy akár hat válaszolhat a különböző számítási feladatok konfigurációk gyűjteménye. Minden egyes számítási feladatok konfigurációjának tartalmazhat egy másik elérhető Virtuálisgép-erőforrások lefoglalását. Létrehozásával és használatával minden egyes foglalási modellek részletes információkat lejjebb találja. Csak érték nem háttérben lévő árnyékolt cellák, vagy Termékváltozat legördülő listák a munkalap belül kell módosítani. Árnyékolt cellák belül végrehajtott módosítások erőforrás számítások megszakadhat.


## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint utasításokat
Létrehoz egy modellt használ a különböző méretű és virtuális gépek mennyiségét egy gyűjteményt, válassza a "DefineByVMFootprint" lapot, és kövesse a feladatütemezési lépéseket.

1. Ez a munkalap jobb felső sarokban a legördülő listában szerepelnek vezérlők segítségével válassza ki a egy kezdeti szám (4 és 16) között kiszolgálók telepíteni kívánt minden egyes hardver rendszerben (Termékváltozat). A kiszolgálók számának megtekintéséhez, hogyan befolyásolja ez összességében a modellezési folyamat során bármikor módosítható az erőforrás-elosztási modellt számára elérhető erőforrások.
2. Ha azt szeretné, a modell a különböző virtuális gépek erőforrás-hozzárendelések egy adott hardverkonfiguráción ellen, a kék legördülő lista közvetlenül az "Aktuális Termékváltozat" címke alatt található az oldal jobb felső sarkában. Kérje le a lista, és válassza ki a kívánt hardver SKU.
3. Most már készen áll, kezdje el hozzáadni a különböző méretű virtuális gépeket a modell az. Adja meg egy adott virtuális gép típusát, írjon be egy mennyiség értéket, a kék bekeretezett bal oldalán, a virtuális gép bejegyzést.

   > [!NOTE]
   > Virtuális gép tárterületének teljes kapacitását az adatlemezt a virtuális gép hivatkozik (a támogatott lemezek száma * (1 TB) egyetlen lemez maximális kapacitása). A konfigurációs mutatók alapján azt ki vannak töltve a rendelkezésre álló tárolási konfigurációk táblában, így előfordulhat, hogy válassza ki a kívánt szint tárolási erőforrás minden egyes Azure Stack virtuális gép. Azonban fontos megjegyezni, hogy adhatók hozzá, vagy a rendelkezésre álló tárolási konfigurációk tábla szükség szerint módosítsa.<br><br>Minden virtuális gép egy kezdetben hozzárendelt helyi ideiglenes tárhely kezdődik. Ideiglenes tárterület kiosztás megfelelően a helyi-temp szám szeletének a legördülő menüből, beleértve a maximális megengedett ideiglenes tárterület-mennyiség módosítható.

4. Virtuális gépek hozzáadásakor, látni fogja a diagramok, amelyek megmutatják a rendelkezésre álló SKU-erőforrások módosítása. Ez lehetővé teszi különböző méretű és tranzakciómennyiségek, amelyek virtuális gépek hozzáadása a modellezési folyamat során a hatásukat. Változások megtekintése egy másik úgy, hogy tekintse meg a consumed oszlopban látható, és továbbra is elérhető számok közvetlenül alábbi elérhető virtuális gépek listáját. Ezek a számok az aktuálisan kiválasztott hardver SKU alapuló becsült értékek tükrözik.
5. Miután létrehozta a virtuális gépek csoportját, megtalálhatja a javasolt hardver SKU közvetlenül az "Aktuális Termékváltozat" címke alatt az oldal jobb felső sarokban található "Javasolt Termékváltozat" gombra kattintva. Ez a gomb használata esetén, módosíthatja a Virtuálisgép-konfigurációk és hardver támogatja-e az egyes konfigurációkhoz.


## <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint instructions
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
8. Ha elégedett a módosításokkal, újból a "Javasolt Termékváltozat" gombra kattintva jelenik meg a Termékváltozat javasolt az új konfigurációt.


## <a name="next-steps"></a>További lépések
További információ a [adatközpont integrációja szempontok az Azure Stackhez](azure-stack-datacenter-integration.md)
