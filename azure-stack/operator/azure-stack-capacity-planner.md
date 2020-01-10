---
title: Azure Stack hub Capacity Planner | Microsoft Docs
description: További információ a Azure Stack hub üzemelő példányok kapacitásának megtervezéséről.
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
ms.openlocfilehash: deff6d15374105b56e504da4edf89eecc8f6a640
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75816853"
---
# <a name="azure-stack-hub-capacity-planner"></a>Azure Stack hub Capacity Planner

Az Azure Stack hub Capacity Planner egy táblázat, amely azt mutatja be, hogy a számítástechnikai erőforrások különböző kiosztásai hogyan illeszkednek egymáshoz a hardveres ajánlatok között. 

## <a name="worksheet-descriptions"></a>A munkalap leírása
A következő táblázat az Azure Stack hub Capacity Planner összes munkalapját ismerteti, amelyek a [https://aka.ms/azstackcapacityplanner](https://aka.ms/azstackcapacityplanner)tölthetők le. 

|Munkalap neve|Leírás|
|-----|-----|
|Verzió – jogi nyilatkozat|A számológép, a verziószám és a kiadási dátum célja.|
|Utasítások|Részletes útmutató a kapacitás megtervezéséhez a virtuális gépek (VM-EK) gyűjteményének modellezéséhez.|
|DefinedSolutionSKUs|Legfeljebb öt hardveres definícióval rendelkező tábla. A bejegyzések példák. Módosítsa a részleteket úgy, hogy azok megfeleljenek az érintett rendszerkonfigurációknak.|
|DefineByVMFootprint|Keresse meg a megfelelő hardveres SKU-t úgy, hogy összehasonlítja a különböző méretű és mennyiségű virtuális gépet.|
|DefineByWorkloadFootprint|Keresse meg a megfelelő hardveres SKU-t Azure Stack hub számítási feladatok gyűjteményének létrehozásával.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs utasítások
Ez a munkalap legfeljebb öt hardver-definíciós példát tartalmaz. Módosítsa a részleteket úgy, hogy azok megfeleljenek a figyelembe vett rendszerkonfigurációknak.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Az engedélyezett hardveres partnerek által biztosított hardver-beállítások
Azure Stack hub a megoldás partnerei által telepített szoftverekkel integrált rendszerként érhető el. A megoldás partnerei a Azure Stack hub kapacitás-tervezési eszközeinek saját mérvadó verzióját nyújtják. Ezeket az eszközöket a megoldási kapacitás végső megvitatására használhatja.

### <a name="multiple-ways-to-model-computing-resources"></a>Több módszer a számítási erőforrások modellezésére
Az Azure Stack hub erőforrás-modellezése Capacity Planner a Azure Stack hub-alapú virtuális gépek különböző méreteitől függ. A virtuális gépek tartománya a legkisebb, alapszintű 0, legfeljebb a legnagyobb Standard_Fsv2. A számítási erőforrások kiosztásának modellezése kétféleképpen lehetséges:

- Válasszon ki egy adott hardveres ajánlatot, és nézze meg, hogy a különböző erőforrások mely kombinációi illeszkednek. 

- Hozzon létre egy adott virtuálisgép-kiosztást, és engedje, hogy az Azure Resource Calculator megjelenjen a virtuális gép konfigurációjának támogatásához rendelkezésre álló hardveres SKU-ban.

Ez az eszköz két módszert biztosít a virtuálisgép-erőforrások lefoglalásához: akár egyetlen virtuálisgép-erőforrás-foglalást, akár akár hat eltérő munkaterhelés-konfigurációt tartalmazó gyűjteményt. Minden munkaterhelés-konfiguráció tartalmazhatja a rendelkezésre álló virtuálisgép-erőforrások különböző kiosztását. A következő szakaszokban részletes útmutatást talál az egyes felosztási modellek létrehozásához és használatához. Csak a nem háttérben árnyékolt cellákban található, illetve a munkalapon lévő SKU legördülő listában szereplő értékeket kell módosítani. Az árnyékolt cellákon belüli módosítások megszakítják az erőforrás-számításokat.


## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint utasítások
Ha a modellt különböző méretű virtuális gépek és mennyiségek egyetlen gyűjteményével szeretné létrehozni, válassza a **DefineByVMFootprint** lapot, és kövesse az alábbi lépéseket:

1. A munkalap jobb felső sarkában található legördülő lista vezérlőelemekkel kiválaszthatja az egyes hardverrendszer (SKU) által telepíteni kívánt kiszolgálók kezdeti számát (4 és 16 között). Ez a számú kiszolgáló bármikor módosítható a modellezési folyamat során, hogy megtudja, hogy ez milyen hatással van az erőforrás-kiosztási modell teljes rendelkezésre állási erőforrásaira.
2. Ha a különböző virtuálisgép-erőforrások kiosztásait egy adott hardverkonfiguráció alapján szeretné modellezni, akkor a lap jobb felső sarkában található kék legördülő listát közvetlenül az **aktuális SKU** -címke alatt találja. Húzza le ezt a listát, és válassza ki a kívánt hardveres SKU-t.
3. Most már készen áll arra, hogy megkezdje a különböző méretű virtuális gépek hozzáadását a modellhez. Egy adott virtuálisgép-típus belefoglalásához adjon meg egy mennyiségi értéket a virtuális gép bejegyzésének bal oldalán található kék színnel megadott mezőbe.

   > [!NOTE]
   > A teljes virtuálisgép-tárterület a virtuális gép adatlemezének teljes kapacitására utal (a támogatott lemezek száma megszorozva az egyetlen lemez maximális kapacitásával [1 TB]). A konfigurációs mutatók alapján kitöltöttük a rendelkezésre álló tárolási konfigurációk táblázatát, így kiválaszthatja az egyes Azure Stack hub-alapú virtuális gépek kívánt tárolási erőforrását. Fontos azonban megjegyezni, hogy szükség szerint hozzáadhatja vagy módosíthatja a rendelkezésre álló tárolási konfigurációk táblázatát.<br><br>Minden virtuális gép egy eredetileg hozzárendelt helyi ideiglenes tárolóval kezdődik. A temp Storage dinamikus kiosztásának megjelenítéséhez a legördülő menüben tetszőleges értékre módosíthatja a helyi-Temp számot, beleértve a maximálisan engedélyezett Temp Storage-mennyiséget.

4. A virtuális gépek hozzáadásakor megjelennek az elérhető SKU-erőforrások megjelenítését bemutató diagramok. Ez lehetővé teszi, hogy megtekintse a különböző méretek és mennyiségű virtuális gép hozzáadásának hatását a modellezési folyamat során. A változtatások hatásának megtekintéséhez a másik lehetőség, hogy megtekintse a **felhasznált** és **még elérhető** számokat, amelyek közvetlenül a rendelkezésre álló virtuális gépek listáján szerepelnek. Ezek a számok a jelenleg kiválasztott hardveres SKU alapján becsült értékeket tükrözik.
5. Ha létrehozta a virtuális gépek készletét, a javasolt hardver SKU a **javasolt SKU**lehetőség kiválasztásával az oldal jobb felső sarkában található, közvetlenül az **aktuális SKU** -címke alatt. Ezen gomb használatával módosíthatja a virtuálisgép-konfigurációkat, és megtekintheti, hogy melyik hardver támogatja az egyes konfigurációkat.


## <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint utasítások
Ha Azure Stack hub-munkaterhelések gyűjteményével szeretne modellt létrehozni, válassza a **DefineByWorkloadFootprint** lapot, és kövesse az alábbi lépéseket. Azure Stack hub számítási feladatait elérhető virtuálisgép-erőforrások használatával hozhatja létre.   

> [!TIP]
> Azure Stack hub-beli virtuális gép megadott tárolási méretének módosításához tekintse meg az előző szakasz harmadik lépésének megjegyzését.

1. A munkalap jobb felső sarkában található legördülő lista vezérlőelemekkel kiválaszthatja az egyes hardverrendszer (SKU) által telepíteni kívánt kiszolgálók kezdeti számát (4 és 16 között).
2. Ha a különböző virtuálisgép-erőforrások kiosztásait egy adott hardverkonfiguráció alapján szeretné modellezni, akkor a lap jobb felső sarkában található kék legördülő listát közvetlenül az **aktuális SKU** -címke alatt találja. Húzza le ezt a listát, és válassza ki a kívánt hardveres SKU-t.
3. A **DefineByVMFootprint** lapon válassza ki a megfelelő tárolási méretet a kívánt Azure stack hub-beli virtuális gépek számára az előző szakasz 3. lépésében leírtak szerint. A virtuális gépenként megadott tárterület a DefineByVMFootprint lapon van definiálva.
4. A **DefineByWorkloadFootprint** lap bal felső sarkában a konfigurációk legfeljebb hat különböző számítási feladathoz hozhatók létre. Adja meg az adott számítási feladatban található egyes virtuálisgép-típusok mennyiségét. Ezt úgy teheti meg, hogy numerikus értékeket helyez el az oszlopba közvetlenül a munkaterhelés neve alatt. A munkaterhelések neve módosítható úgy, hogy az adott konfiguráció által támogatott számítási feladatok típusát tükrözze.
5. Az egyes számítási feladatok típusának bizonyos mennyiségét megadhatja az oszlop alján található érték megadásával közvetlenül a **mennyiség** felirat alatt.
6. Amikor létrehozta a számítási feladatok típusát és mennyiségét, válassza a **javasolt SKU** lehetőséget az oldal jobb felső sarkában, közvetlenül az **aktuális SKU** -címke alatt. Ez megjeleníti a minimális SKU-t, amely elegendő erőforrással rendelkezik a számítási feladatok általános konfigurációjának támogatásához.
7. A további modellezést a hardver SKU-ra kiválasztott kiszolgálók számának módosításával, illetve a virtuális gépek kiosztásának vagy a munkaterhelés-konfigurációkon belüli mennyiségének a módosításával végezheti el. A társított gráfok azonnali visszajelzést jelenítenek meg, amely bemutatja, hogyan befolyásolja a módosítások a teljes erőforrás-felhasználást.
8. Ha elégedett a módosításaival, válassza a **javasolt SKU** újra lehetőséget az új konfigurációhoz javasolt SKU megjelenítéséhez. Kiválaszthatja a legördülő menüt is a kívánt SKU kiválasztásához.

## <a name="next-steps"></a>Következő lépések
[A Azure stack hub Datacenter integrációs szempontjainak](azure-stack-datacenter-integration.md)megismerése.
