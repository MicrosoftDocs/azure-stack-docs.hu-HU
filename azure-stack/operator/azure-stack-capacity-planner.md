---
title: Az Azure Stack Capacity Planner |} A Microsoft Docs
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
ms.openlocfilehash: 98dafb9df568e5e14e5f1890103d0261e07beb98
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131358"
---
# <a name="azure-stack-capacity-planner"></a>Az Azure Stack Capacity Planner

Az Azure Stack Capacity Planner egy táblázatot, amely megjeleníti a számítási erőforrások különböző hozzárendelések helyezze egy kijelölt hardverek között. 

## <a name="worksheet-descriptions"></a>Munkalap leírása
A következő táblázat ismerteti az Azure Stack Capacity Planner, amely letölthető az egyes munkalap [ https://aka.ms/azstackcapacityplanner ](https://aka.ms/azstackcapacityplanner). 

|Munkalap neve|Leírás|
|-----|-----|
|Verzió-jogi nyilatkozat|A kalkulátor, a verziószámot és a kiadási dátum célja.|
|Utasítások|Részletes utasításokat követve modell kapacitás megtervezése a virtuális gépek (VM) gyűjteménye.|
|DefinedSolutionSKUs|Legfeljebb öt hardver definíciók tartalmazó tábla. A bejegyzések példák. A részleteket a szóban forgó rendszerkonfigurációk megfelelően módosítsa.|
|DefineByVMFootprint|Keresse meg a megfelelő hardver SKU összehasonlítja a különböző méretekre és virtuális gépek mennyiségét konfigurációkat.|
|DefineByWorkloadFootprint|Keresse meg a megfelelő hardver SKU hoz létre az Azure Stack-munkaterhelések gyűjteménye.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs utasításokat
Ezen a munkalapon legfeljebb öt hardver definíció példákat tartalmaz. Módosítsa megfelelően a rendszer konfigurációját a szóban forgó részleteit.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Hitelesített hardveren partnerek által biztosított hardver beállításokat
Az Azure Stack egy integrált rendszer megoldási partnerei által telepített szoftverekkel rendelkező érkeznek. Megoldáspartnerek adja meg a saját Azure Stack kapacitástervező eszközök mérvadó verzióit. Használja ezeket az eszközöket a végső discussions megoldás kapacitás.

### <a name="multiple-ways-to-model-computing-resources"></a>Több lehetőség is modell számítási erőforrások
Az Azure Stack Capacity Planner belül modellezési erőforrás attól függ, hogy a különböző méretű Azure Stack-beli virtuális. A legkisebb, alapvető 0, akár a legnagyobb, Standard_Fsv2 méretű virtuális gépek tartománya. Két különböző módon modellezheti számítási erőforrás-hozzárendelések:

- Válassza ki a adott hardverekhez, ajánlat, és tekintse meg a különböző erőforrások mely kombinációit illeszkednek. 

- Hozzon létre egy virtuális gép hozzárendelések egyedi kombinációja, és lehetővé teszik az Azure-erőforrás Díjkalkulátorunkkal megjelenítése, amely rendelkezésre álló termékváltozatok a következők támogathatja a Virtuálisgép-konfiguráció.

Ez az eszköz a Virtuálisgép-erőforrások kiosztásával kétféle módszert biztosít: virtuális gép erőforrás-hozzárendelések egy egyetlen gyűjteményként, vagy akár hat válaszolhat a különböző számítási feladatok konfigurációk gyűjteménye. Minden egyes számítási feladatok konfigurációjának tartalmazhat egy másik elérhető Virtuálisgép-erőforrások lefoglalását. A következő szakaszokban részletesen hozhat létre, és lefoglalási modellek az egyes rendelkezik. Csak érték, vagy belül Termékváltozat legördülő listák a munkalap nem háttér árnyékolt cellák szereplő módosítani kell. Árnyékolt cellák belül végrehajtott módosítások erőforrás számítások meghiúsulhat.


## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint utasításokat
A különböző méretű és virtuális gépek mennyiségét egy gyűjteményt egy modell létrehozásához válassza a **DefineByVMFootprint** lapra, és kövesse az alábbi lépéseket:

1. Ez a munkalap jobb felső sarkában a legördülő listában szerepelnek vezérlők használatával válassza ki a használni kívánt minden egyes hardver rendszeren (Termékváltozat) telepített kiszolgálón (4 és 16) között egy kezdeti száma. A kiszolgálók számának megtekintéséhez, hogyan befolyásolja ez összességében a modellezési folyamat során bármikor módosíthatók az erőforrás-elosztási modellt számára elérhető erőforrások.
2. Ha azt szeretné, a modellek különböző virtuális gépek erőforrás-hozzárendelések szemben egy adott hardverkonfiguráción, keresse meg a kék legördülő lista közvetlenül az alábbi a **aktuális Termékváltozat** címkét az oldal jobb felső sarokban. Kérje le a lista, és válassza ki a kívánt hardver SKU.
3. Most már készen áll a modell különböző méretű virtuális gépeket ad hozzá a kezdéshez. Adja meg egy adott virtuális gép típusát, írjon be egy mennyiség értéket, a kék bekeretezett bal oldalán, a virtuális gép bejegyzést.

   > [!NOTE]
   > Virtuális gép tárterületének teljes kapacitását az adatlemezt a virtuális gép (az [1 TB-os] egyetlen lemez maximális kapacitása megszorozza támogatott lemezek száma) hivatkozik. A konfigurációs mutatók alapján, hogy feltöltötte a rendelkezésre álló tárolási konfigurációk tábla így kiválaszthatja a kívánt szint tárolási erőforrás minden egyes Azure Stack virtuális gép. Azonban fontos megjegyezni, hogy adja hozzá, vagy a rendelkezésre álló tárolási konfigurációk tábla szükség szerint módosítsa.<br><br>Minden virtuális gép egy kezdetben hozzárendelt helyi ideiglenes tárhely kezdődik. Ideiglenes tárterület kiosztás tükrözéséhez módosíthatja a helyi-temp számot szeletének a legördülő menüből, beleértve a maximális megengedett ideiglenes tárterület keletkezett.

4. Virtuális gépek hozzáadásakor, látni fogja a diagramok, amelyek megmutatják a rendelkezésre álló SKU-erőforrások módosítása. Ez lehetővé teszi különböző méretű és tranzakciómennyiségek, amelyek virtuális gépek hozzáadása a modellezési folyamat során a hatásukat. Változások megtekintése másik módja, és tekintse meg a **Consumed** és **továbbra is elérhető** számok, közvetlenül a rendelkezésre álló virtuális gépek listája alább felsorolt. Ezek a számok az aktuálisan kiválasztott hardver SKU alapuló becsült értékek tükrözik.
5. Amikor létrehozta a virtuális gépek csoportját, annak a javasolt hardver SKU kiválasztásával **javasolt Termékváltozat**, az oldal jobb felső sarokban a közvetlenül az alábbi a **aktuális Termékváltozat** címkét. Ez a gomb használata esetén, módosíthatja a Virtuálisgép-konfigurációk és hardver támogatja-e az egyes konfigurációkhoz.


## <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint instructions
Modell létrehozása az Azure Stack-munkaterhelések gyűjteménye, jelölje be a **DefineByWorkloadFootprint** lapra, és kövesse a feladatütemezési lépéseket. Elérhető Virtuálisgép-erőforrások használatával hoz létre az Azure Stack számítási feladatokhoz.   

> [!TIP]
> Egy Azure Stack-beli virtuális gép a megadott tárfiók méretének módosításához tekintse meg a megjegyzést. az előző szakaszban három lépésben.

1. Ez a munkalap jobb felső sarkában a legördülő listában szerepelnek vezérlők használatával válassza ki a használni kívánt minden egyes hardver rendszeren (Termékváltozat) telepített kiszolgálón (4 és 16) között egy kezdeti száma.
2. Ha azt szeretné, a modellek különböző virtuális gépek erőforrás-hozzárendelések szemben egy adott hardverkonfiguráción, keresse meg a kék legördülő lista közvetlenül az alábbi a **aktuális Termékváltozat** címkét az oldal jobb felső sarokban. Kérje le a lista, és válassza ki a kívánt hardver SKU.
3. Válassza ki a megfelelő tárolási mérete az egyes Azure Stack kívánt virtuális gépeken a a **DefineByVMFootprint** lapon, a harmadik lépése az előző szakaszban leírtak szerint. A tárhelyméretet a virtuális gép a DefineByVMFootprint lap van definiálva.
4. A bal felső indítása a **DefineByWorkloadFootprint** hozza létre a konfiguráció legfeljebb hat különböző számítási feladatok esetében. Adja meg minden egyes VM-típus található, hogy a számítási feladatok mennyisége. Ehhez elhelyezését a numerikus értékek az oszlop közvetlenül az adott számítási feladat neve alatt. Módosíthatja a számítási feladat nevét, hogy milyen típusú számítási feladatok, amelyek ezt a konfigurációt támogat.
5. Minden egyes számításifeladat-típust adott mennyiségű hozzáadhatja a közvetlenül alá egy adott oszlop alján érték megadásával a **mennyiség** címkét.
6. Amikor létrehozta a számítási feladatok típusa és a mennyiségek, válassza ki **javasolt Termékváltozat** az oldal jobb felső sarokban a közvetlenül az alábbi a **aktuális Termékváltozat** címkét. Ez megjeleníti a legkisebb Termékváltozat elegendő erőforrással rendelkező számítási feladatok a teljes konfiguráció támogatásához.
7. További modellezési hardveres Termékváltozat, a kiválasztott kiszolgálók számának módosításával elvégezheti a virtuális gép kiosztásokhoz és mennyiségek belül a munkaterhelés-konfigurációk módosítása vagy. A társított grafikonok azonnali visszajelzést ad arról, hogy a a végrehajtott módosítások miként befolyásolják a teljes erőforrás-felhasználásának megjelenítő jelennek meg.
8. Ha már elégedett a módosításokkal, válassza ki a **javasolt Termékváltozat** újra az új konfigurációhoz javasolt Termékváltozat megjeleníthető. Választhatja a legördülő menüben válassza ki a kívánt Termékváltozatát.

## <a name="next-steps"></a>További lépések
Ismerje meg [adatközpont integrációja szempontok az Azure Stack](azure-stack-datacenter-integration.md).
