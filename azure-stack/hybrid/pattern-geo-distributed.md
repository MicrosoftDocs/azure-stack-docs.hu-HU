---
title: Földrajzilag elosztott alkalmazási minta az intelligens peremhálózat számára az Azure és a Azure Stack hub használatával.
description: Ismerje meg az Azure-t és Azure Stack hubot használó, földrajzilag elosztott alkalmazási mintát az intelligens peremhálózat számára.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 37d4b517eec69ebab72aa3d82733829466b44fda
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875674"
---
# <a name="geo-distributed-pattern"></a>Földrajzilag elosztott minta

Megtudhatja, hogyan biztosíthat alkalmazás-végpontokat több régióban, és hogyan irányíthatja át a felhasználói forgalmat a hely és a megfelelőségi igények alapján.

## <a name="context-and-problem"></a>Kontextus és probléma

Azok a szervezetek, amelyek széles körű földrajzi területtel rendelkeznek, az adathozzáférés biztonságos és pontos elosztására és engedélyezésére törekednek, miközben a biztonság, a megfelelőség és a teljesítmény szükséges szintje felhasználónként, helyen és eszközön a határokon belül.

## <a name="solution"></a>Megoldás

A Azure Stack hub földrajzi forgalmának útválasztási mintája, illetve a földrajzilag elosztott alkalmazások lehetővé teszik, hogy a forgalom a különböző metrikák alapján meghatározott végpontokra legyen irányítva. A földrajzi alapú útválasztási és végponti konfigurációval rendelkező Traffic Managerek a regionális követelmények, a vállalati és a nemzetközi szabályozás, valamint az adatszükségletek alapján irányítják át a végpontokra irányuló forgalmat.

![Földrajzilag elosztott minta](media/pattern-geo-distributed/geo-distribution.png)

## <a name="components"></a>Összetevők

**Traffic Manager**  
A diagramon Traffic Manager a nyilvános felhőn kívül található, de a helyi adatközpontban és a nyilvános felhőben is képesnek kell lennie a forgalom koordinálására. A Balancer a földrajzi helyszínekre irányítja a forgalmat.

**Tartománynévrendszer (DNS)**  
A tartománynévrendszer vagy a DNS a webhely vagy szolgáltatás nevének az IP-címére való fordítására (vagy feloldására) felelős.

### <a name="public-cloud"></a>Nyilvános felhő

**Felhőbeli végpont**  
A nyilvános IP-címek használatával a bejövő forgalom átirányítható a Traffic Managerrel a nyilvános Felhőbeli alkalmazás erőforrásainak végpontján.  

### <a name="local-clouds"></a>Helyi felhők

**Helyi végpont**  
A nyilvános IP-címek használatával a bejövő forgalom átirányítható a Traffic Managerrel a nyilvános Felhőbeli alkalmazás erőforrásainak végpontján.

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

A minta megvalósítása során az alábbi pontokat vegye figyelembe:

### <a name="scalability"></a>Skálázhatóság

A minta a földrajzi forgalom útválasztását kezeli, és nem méretezhető, hogy megfeleljen a forgalom növekedésének. Ezt a mintát azonban más Azure-és helyszíni megoldásokkal is kombinálhatja. Ez a minta például a több felhőre kiterjedő skálázási mintával használható.

### <a name="availability"></a>Elérhetőség

Győződjön meg arról, hogy a helyileg telepített alkalmazások magas rendelkezésre állásra vannak konfigurálva a helyszíni hardverkonfiguráció és a szoftverek központi telepítése révén.

### <a name="manageability"></a>Kezelhetőség

A minta zökkenőmentes felügyeletet és ismerős felületet biztosít a környezetek között.

## <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?

- A szervezetem olyan nemzetközi ágakat tartalmaz, amelyek egyéni regionális biztonsági és terjesztési házirendeket igényelnek.
- A szervezeti irodák mindegyike lekéri az alkalmazottakat, az üzleti és a létesítményi tevékenységeket, amelyek helyi szabályozással és időzónával kapcsolatos jelentési tevékenységet igényelnek.
- A nagy léptékű követelmények az alkalmazások horizontális felskálázásával hajthatók végre, több alkalmazás üzembe helyezése egyetlen régióban, valamint régiók között, a szélsőséges terhelési követelmények kezelése érdekében.
- Az alkalmazásoknak rendkívül elérhetőnek kell lenniük, és az ügyfelek kéréseire kell reagálnia, még akkor is, ha egyetlen régió meghibásodása esetén.

## <a name="next-steps"></a>Következő lépések

További információ a cikkben bemutatott témakörökről:
- A DNS-alapú forgalom terheléselosztó működésével kapcsolatos további információkért tekintse meg az [Azure Traffic Manager áttekintését](/azure/traffic-manager/traffic-manager-overview) .
- Az ajánlott eljárásokkal kapcsolatos további információkért és a további kérdések megválaszolásáért tekintse meg a [hibrid alkalmazások kialakításával kapcsolatos szempontokat](overview-app-design-considerations.md) .
- A termékek és megoldások teljes portfóliójának megismeréséhez tekintse meg a [Azure stack termékcsaládot és megoldásokat](/azure-stack).

Ha készen áll a megoldás tesztelésére, folytassa a [földrajzilag elosztott app Solution telepítési útmutatóval](solution-deployment-guide-geo-distributed.md). A telepítési útmutató részletes útmutatást nyújt az összetevők üzembe helyezéséhez és teszteléséhez. Megtudhatja, hogyan irányíthatja át a forgalmat adott végpontokra, a földrajzilag elosztott alkalmazások mintáját használó különféle mérőszámok alapján. A Traffic Manager-profilok földrajzi alapú útválasztási és végponti konfigurációval való létrehozása biztosítja az információk átirányítását a végpontok számára a regionális követelmények, a vállalati és a nemzetközi szabályozás, valamint az adatok igényei alapján.