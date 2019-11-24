---
title: A felhőben, az Azure-ban és Azure Stack hub-on átívelő alkalmazások létrehozásának mintája.
description: Megtudhatja, hogyan használhatja az Azure-t és Azure Stack hub-t egy skálázható, Felhőbeli alkalmazások létrehozásához.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: ddc8218aadbf3940ea18ce80012441c5e43c7472
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73638804"
---
# <a name="cross-cloud-scaling-pattern"></a>Több felhőre kiterjedő skálázási minta

Erőforrások automatikus hozzáadása egy meglévő alkalmazáshoz a terhelés növekedésének kielégítése érdekében.

## <a name="context-and-problem"></a>Kontextus és probléma

Az alkalmazás nem növelheti a kapacitást, hogy megfeleljen az igény váratlan növekedésének. A skálázhatóság hiánya azt eredményezi, hogy a felhasználók nem érik el az alkalmazást a csúcsérték-használat idején. Az alkalmazás meghatározott számú felhasználót tud kiszolgálni.

A globális vállalatok biztonságos, megbízható és elérhető felhőalapú alkalmazásokat igényelnek. Az értekezlet igény szerint növekszik, és a megfelelő infrastruktúra használatával támogatja az igényeket. A vállalatok az üzleti adatbiztonsággal, a tárolással és a valós idejű rendelkezésre állással kapcsolatos költségek és karbantartás egyensúlyával küzdenek.

Előfordulhat, hogy nem tudja futtatni az alkalmazást a nyilvános felhőben. Előfordulhat azonban, hogy nem valósítható meg gazdaságosan a vállalat számára, hogy fenntartsa a helyszíni környezetben szükséges kapacitást, hogy kezelni tudja az alkalmazás iránti igényt. Ezzel a mintával a nyilvános felhő rugalmasságát a helyszíni megoldással is használhatja.

## <a name="solution"></a>Megoldás

A többfelhős méretezési minta kibővít egy helyi felhőben található alkalmazást, amely nyilvános Felhőbeli erőforrásokkal rendelkezik. A mintát az igény növelésével vagy csökkentésével, illetve a felhőben lévő erőforrások hozzáadásával vagy eltávolításával indítja el a rendszer. Ezek az erőforrások redundanciát, gyors rendelkezésre állást és földrajzilag megfelelő útválasztást biztosítanak.

![Több felhőre kiterjedő skálázási minta](media/pattern-cross-cloud-scale/cross-cloud-scaling.png)

> [!NOTE]
> Ez a minta csak az alkalmazás állapot nélküli összetevőire vonatkozik.

## <a name="components"></a>Összetevők

A több felhőre kiterjedő skálázási minta a következő összetevőkből áll.

**Traffic Manager**  

A diagramon ez a nyilvános felhőn kívül található, de a helyi adatközpontban és a nyilvános felhőben is képesnek kell lennie a forgalom koordinálására. A Balancer magas rendelkezésre állást biztosít az alkalmazás számára a végpontok figyelésével és szükség esetén a feladatátvételi újraelosztás biztosításával.

**Tartománynévrendszer (DNS)**  

A tartománynévrendszer vagy a DNS a webhely vagy szolgáltatás nevének az IP-címére való fordítására (vagy feloldására) felelős.

### <a name="cloud"></a>Felhő

**Üzemeltetett Build kiszolgáló**  
Környezet a build-folyamat üzemeltetéséhez.

**Alkalmazás-erőforrások**  
Az alkalmazás erőforrásainak képesnek kell lenniük a méretezésre és a vertikális felskálázásra, például a virtuális gépek ScaleSets és a tárolók méretezésére.

**Egyéni tartománynév**  
Használjon egyéni tartománynevet a glob útválasztási kérelmekhez.

**Nyilvános IP-címek**  
A nyilvános IP-címek használatával a bejövő forgalom átirányítható a Traffic Managerrel a nyilvános Felhőbeli alkalmazás erőforrásainak végpontján.  

### <a name="local-cloud"></a>Helyi felhő

**Üzemeltetett Build kiszolgáló**  
Környezet a build-folyamat üzemeltetéséhez.

**Alkalmazás-erőforrások**  
Az alkalmazás erőforrásainak képesnek kell lenniük a méretezésre és a felskálázásra, például a virtuális gépek ScaleSets és a tárolók méretezésére.

**Egyéni tartománynév**  
Használjon egyéni tartománynevet a glob útválasztási kérelmekhez.

**Nyilvános IP-címek**  
A nyilvános IP-címek használatával a bejövő forgalom átirányítható a Traffic Managerrel a nyilvános Felhőbeli alkalmazás erőforrásainak végpontján. 

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

A minta megvalósítása során az alábbi pontokat vegye figyelembe:

### <a name="scalability"></a>Méretezhetőség

A többfelhős méretezés fő összetevője az igény szerinti skálázás lehetősége. A skálázásnak a nyilvános és a helyi felhőalapú infrastruktúra között kell történnie, és igény szerint egységes, megbízható szolgáltatást kell biztosítania.

### <a name="availability"></a>Rendelkezésre állás

Győződjön meg arról, hogy a helyileg telepített alkalmazások magas rendelkezésre állásra vannak konfigurálva a helyszíni hardverkonfiguráció és a szoftverek központi telepítése révén.

### <a name="manageability"></a>Kezelhetőség

A Felhőbeli mintázat zökkenőmentes felügyeletet és ismerős felületet biztosít a környezetek között.

## <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?

Használja a következő mintát a következő helyzetekben:

- Ha az alkalmazás kapacitását nem várt igényekkel vagy igény szerinti rendszeres igényekkel kell bővíteni.
- Ha nem szeretne olyan erőforrást befektetni, amelyet csak a csúcsok során használunk. Csak a valóban használt funkciókért kell fizetni.

Ez a minta nem ajánlott a következő esetekben:

- A megoldáshoz az interneten keresztül csatlakozó felhasználók szükségesek.
- Vállalata rendelkezik olyan helyi előírásokkal, amelyek megkövetelik, hogy a kezdeményező kapcsolódás egy helyszíni hívásból történjen.
- A hálózat olyan rendszeres szűk keresztmetszeteket tapasztal, amelyek korlátozzák a skálázás teljesítményét.
- A környezet nem kapcsolódik az internethez, és nem érhető el a nyilvános felhőben.

## <a name="next-steps"></a>Következő lépések

További információ a cikkben bemutatott témakörökről:
- A DNS-alapú forgalom terheléselosztó működésével kapcsolatos további információkért tekintse meg az [Azure Traffic Manager áttekintését](/azure/traffic-manager/traffic-manager-overview) .
- Az ajánlott eljárásokkal kapcsolatos további információkért és a további kérdések megválaszolásáért tekintse meg a [hibrid alkalmazások kialakításával kapcsolatos szempontokat](overview-app-design-considerations.md) .
- A termékek és megoldások teljes portfóliójának megismeréséhez tekintse meg a [Azure stack termékcsaládot és megoldásokat](/azure-stack).

Ha készen áll a megoldás tesztelésére, folytassa a [többfelhős méretezési megoldás telepítési útmutatóját](solution-deployment-guide-cross-cloud-scaling.md). A telepítési útmutató részletes útmutatást nyújt az összetevők üzembe helyezéséhez és teszteléséhez. Megtudhatja, hogyan hozhat létre többfelhős megoldást egy olyan manuálisan aktivált folyamat megadására, amely egy Azure Stack hub által üzemeltetett webalkalmazásból egy Azure-ban üzemeltetett webalkalmazásba vált át. Azt is megtudhatja, hogyan használhatja az automatikus skálázást a Traffic Manager használatával, rugalmas és méretezhető felhőalapú segédprogramot biztosítva a betöltés alatt.
