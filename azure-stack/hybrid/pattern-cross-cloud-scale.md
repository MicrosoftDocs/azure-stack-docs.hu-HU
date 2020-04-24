---
title: Több felhőre kiterjedő méretezési minta az Azure Stack központban
description: Megtudhatja, hogyan hozhat létre méretezhető, többfelhős alkalmazást az Azure-ban és Azure Stack hub-on.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: a830f96e97c347cbbcc09a1b17f4836ecb6eb3e6
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "80891029"
---
# <a name="cross-cloud-scaling-pattern"></a>Több felhőre kiterjedő skálázási minta

Erőforrások automatikus hozzáadása egy meglévő alkalmazáshoz a terhelés növekedésének kielégítése érdekében.

## <a name="context-and-problem"></a>Kontextus és probléma

Az alkalmazás nem növelheti a kapacitást, hogy megfeleljen az igény váratlan növekedésének. A skálázhatóság hiánya azt eredményezi, hogy a felhasználók nem érik el az alkalmazást a csúcsérték-használat idején. Az alkalmazás meghatározott számú felhasználót tud kiszolgálni.

A globális vállalatok biztonságos, megbízható és elérhető felhőalapú alkalmazásokat igényelnek. A találkozó az igény szerint növekszik, és a megfelelő infrastruktúra használatával támogatja az igényeket. A vállalatok az üzleti adatbiztonsággal, a tárolással és a valós idejű rendelkezésre állással kapcsolatos költségek és karbantartás egyensúlyával küzdenek.

Előfordulhat, hogy nem tudja futtatni az alkalmazást a nyilvános felhőben. Előfordulhat azonban, hogy nem valósítható meg gazdaságosan a vállalat számára, hogy fenntartsa a helyszíni környezetben szükséges kapacitást, hogy kezelni tudja az alkalmazás iránti igényt. Ezzel a mintával a nyilvános felhő rugalmasságát a helyszíni megoldással is használhatja.

## <a name="solution"></a>Megoldás

A többfelhős méretezési minta kibővít egy helyi felhőben található alkalmazást, amely nyilvános Felhőbeli erőforrásokkal rendelkezik. A mintát az igény növelésével vagy csökkentésével, illetve a felhőben lévő erőforrások hozzáadásával vagy eltávolításával indítja el a rendszer. Ezek az erőforrások redundanciát, gyors rendelkezésre állást és földrajzilag megfelelő útválasztást biztosítanak.

![Több felhőre kiterjedő skálázási minta](media/pattern-cross-cloud-scale/cross-cloud-scaling.png)

> [!NOTE]
> Ez a minta csak az alkalmazás állapot nélküli összetevőire vonatkozik.

## <a name="components"></a>Összetevők

A több felhőre kiterjedő skálázási minta a következő összetevőkből áll.

### <a name="outside-the-cloud"></a>A felhőn kívül

#### <a name="traffic-manager"></a>Traffic Manager

A diagramon ez a nyilvános felhőn kívül esik, de a helyi adatközpontban és a nyilvános felhőben is képesnek kell lennie a forgalom koordinálására. A Balancer magas rendelkezésre állást biztosít az alkalmazás számára a végpontok figyelésével, valamint szükség esetén a feladatátvételi újraelosztás biztosításával.

#### <a name="domain-name-system-dns"></a>Tartománynévrendszer (DNS)

A tartománynévrendszer vagy a DNS a webhely vagy szolgáltatás nevének az IP-címére való fordítására (vagy feloldására) felelős.

### <a name="cloud"></a>Felhő

#### <a name="hosted-build-server"></a>Üzemeltetett Build kiszolgáló

Környezet a build-folyamat üzemeltetéséhez.

#### <a name="app-resources"></a>Alkalmazás-erőforrások

Az alkalmazás erőforrásainak képesnek kell lenniük a méretezésre és a vertikális felskálázásra, például a virtuálisgép-méretezési csoportokra és a tárolók méretezésére.

#### <a name="custom-domain-name"></a>Egyéni tartománynév

Használjon egyéni tartománynevet a glob útválasztási kérelmekhez.

#### <a name="public-ip-addresses"></a>Nyilvános IP-címek

A nyilvános IP-címek használatával a bejövő forgalom átirányítható a Traffic Managerrel a nyilvános Cloud app Resources végpontra.  

### <a name="local-cloud"></a>Helyi felhő

#### <a name="hosted-build-server"></a>Üzemeltetett Build kiszolgáló

Környezet a build-folyamat üzemeltetéséhez.

#### <a name="app-resources"></a>Alkalmazás-erőforrások

Az alkalmazás erőforrásainak képesnek kell lenniük a méretezésre és a vertikális felskálázásra, például a virtuálisgép-méretezési csoportokra és a tárolók tárolására.

#### <a name="custom-domain-name"></a>Egyéni tartománynév

Használjon egyéni tartománynevet a glob útválasztási kérelmekhez.

#### <a name="public-ip-addresses"></a>Nyilvános IP-címek

A nyilvános IP-címek használatával a bejövő forgalom átirányítható a Traffic Managerrel a nyilvános Cloud app Resources végpontra.

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
- Ha nem szeretne olyan erőforrásba befektetni, amelyet csak a csúcsok során használunk. A ténylegesen használt funkciókért kell fizetnie.

Ez a minta nem ajánlott a következő esetekben:

- A megoldáshoz az interneten keresztül csatlakozó felhasználók szükségesek.
- Vállalata rendelkezik olyan helyi előírásokkal, amelyek megkövetelik, hogy a kezdeményező kapcsolódás egy helyszíni hívásból történjen.
- A hálózat olyan rendszeres szűk keresztmetszeteket tapasztal, amelyek korlátozzák a skálázás teljesítményét.
- A környezet nem kapcsolódik az internethez, és nem éri el a nyilvános felhőt.

## <a name="next-steps"></a>További lépések

További információ a cikkben bemutatott témakörökről:

- A DNS-alapú forgalom terheléselosztó működésével kapcsolatos további információkért tekintse meg az [Azure Traffic Manager áttekintését](/azure/traffic-manager/traffic-manager-overview) .
- Az ajánlott eljárásokról és a további kérdésekre adott válaszokért lásd a [hibrid alkalmazások kialakításával kapcsolatos szempontokat](overview-app-design-considerations.md) .
- A termékek és megoldások teljes portfóliójának megismeréséhez tekintse meg a [Azure stack termékcsaládot és megoldásokat](/azure-stack) .

Ha készen áll a megoldás tesztelésére, folytassa a [többfelhős méretezési megoldás telepítési útmutatóját](solution-deployment-guide-cross-cloud-scaling.md). A telepítési útmutató részletes útmutatást nyújt az összetevők üzembe helyezéséhez és teszteléséhez. Megtudhatja, hogyan hozhat létre többfelhős megoldást egy olyan manuálisan aktivált folyamat megadására, amely egy Azure Stack hub által üzemeltetett webalkalmazásból egy Azure-ban üzemeltetett webalkalmazásra vált át. Azt is megtudhatja, hogyan használhatja az automatikus skálázást a Traffic Manager használatával, rugalmas és méretezhető felhőalapú segédprogramot biztosítva a betöltés alatt.
