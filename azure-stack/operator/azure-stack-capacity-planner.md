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
ms.openlocfilehash: aec1d6fab044c731501433e6578257e8b09570eb
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2019
ms.locfileid: "66461002"
---
# <a name="azure-stack-capacity-planner"></a>Az Azure Stack Capacity Planner

Az Azure Stack Capacity Planner egy táblázatot, amely megjeleníti a számítási erőforrások különböző hozzárendelések helyezze egy kijelölt hardverek között. 

## <a name="worksheet-descriptions"></a>Munkalap leírása
Az alábbi táblázat minden egyes munkalap leírása szerepel az Azure Stack Capacity Planner, amely letölthető a [ https://aka.ms/azstackcapacityplanner ](https://aka.ms/azstackcapacityplanner). 

|Lap neve|Leírás|
|-----|-----|
|Verzió-jogi nyilatkozat|A kalkulátor, a verziószámot és a kiadási dátum célja.|
|Utasítások|Részletes utasításokat követve modell kapacitás megtervezése a virtuális gépek gyűjteményét.|
|DefinedSolutionSKUs|Legfeljebb öt hardver definíciók tartalmazó tábla. A bejegyzések példák. A részleteket a szóban forgó rendszerkonfigurációk megfelelően módosítsa.|
|DefineByVMFootprint|Keresse meg a megfelelő hardver SKU összehasonlítja a különböző méretekre és virtuális gépek mennyiségét konfigurációkat.|
|DefineByWorkloadFootprint|Keresse meg a megfelelő hardver SKU hoz létre az Azure Stack-munkaterhelések gyűjteménye.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs utasításokat
Ezen a munkalapon legfeljebb öt hardver definíció példákat tartalmaz. Módosítsa megfelelően a rendszer konfigurációját a szóban forgó részleteit.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Hardver-beállításokat, jogosult a hardvergyártó partnerektől által biztosított
Az Azure Stack egy integrált rendszer megoldási partnerei által telepített szoftverekkel rendelkező érkeznek. Megoldáspartnerek adja meg a saját Azure Stack kapacitástervező eszközök mérvadó verzióit. Használja ezeket az eszközöket a végső discussions megoldás kapacitás.

### <a name="multiple-ways-to-model-computing-resources"></a>Több lehetőség is modell számítási erőforrások
Az Azure Stack planner belül modellezési erőforrás attól függ, hogy a különböző méretű Azure Stack-beli virtuális. Akár a legnagyobb Standard_Fsv2 legkisebb alapszintű 0 méretű virtuális gépek tartománya. Két különböző módon modellezheti számítási erőforrás-hozzárendelések:

- Jelöljön ki egy adott hardverekhez ajánlat, és tekintse meg a különböző erőforrások mely kombinációit fér. 

- Hozzon létre Virtuálisgép-hozzárendelések egyedi kombinációját, és lehetővé teszik az Azure-erőforrás Díjkalkulátorunkkal megjelenítése, amely rendelkezésre álló termékváltozatok a következők támogathatja a Virtuálisgép-konfiguráció.

Ez az eszköz a Virtuálisgép-erőforrások kiosztásával kétféle módszert biztosít: a virtuális gép erőforrás-hozzárendelések egy egyetlen gyűjtemény vagy legfeljebb hat válaszolhat a különböző számítási feladatok konfigurációk gyűjteménye. Minden egyes számítási feladatok konfigurációjának tartalmazhat egy másik elérhető Virtuálisgép-erőforrások lefoglalását. A következő szakaszokban részletesen hozhat létre, és lefoglalási modellek az egyes rendelkezik. Csak érték nem háttérben lévő árnyékolt cellák, vagy Termékváltozat legördülő listák a munkalap belül kell módosítani. Árnyékolt cellák belül végrehajtott módosítások erőforrás számítások megszakadhat.


## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint utasításokat
A különböző méretű és virtuális gépek mennyiségét egy egyetlen gyűjtemény használata modell létrehozásához, válassza az "DefineByVMFootprint" lapot, és kövesse az alábbi lépéseket:

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
8. Ha elégedett a módosításokkal, kattintson a **javasolt Termékváltozat** ismét megjelenik az új konfigurációhoz javasolt a Termékváltozat. A legördülő menüben válassza ki a kívánt Termékváltozatát is kattinthat.

## <a name="next-steps"></a>További lépések
További információ a [adatközpont integrációja szempontok az Azure Stackhez](azure-stack-datacenter-integration.md)
