---
title: Földrajzilag elosztott alkalmazás mintája Azure Stack központban
description: Ismerje meg az Azure-t és Azure Stack hubot használó, földrajzilag elosztott alkalmazási mintát az intelligens peremhálózat számára.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod2019
ms.openlocfilehash: 1f6243927390c7a520c2607c722664b2d31fc07f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "80891036"
---
# <a name="geo-distributed-app-pattern"></a>Földrajzilag elosztott alkalmazás mintája

Megtudhatja, hogyan biztosíthat alkalmazás-végpontokat több régióban, és hogyan irányíthatja át a felhasználói forgalmat a hely és a megfelelőségi igények alapján.

## <a name="context-and-problem"></a>Kontextus és probléma

Azok a szervezetek, amelyek széles körű földrajzi területtel rendelkeznek, az adathozzáférés biztonságos és pontos elosztására és engedélyezésére törekednek, miközben a biztonság, a megfelelőség és a teljesítmény szükséges szintje felhasználónként, helyen és eszközön a határokon belül.

## <a name="solution"></a>Megoldás

A Azure Stack hub földrajzi forgalmának útválasztási mintája vagy földrajzilag elosztott alkalmazások lehetővé teszi, hogy a forgalom a különböző metrikák alapján meghatározott végpontokra legyen irányítva. A földrajzi alapú útválasztási és végponti konfigurációval rendelkező Traffic Managerek a regionális követelmények, a vállalati és a nemzetközi szabályozás, valamint az adatszükségletek alapján irányítják át a végpontokra irányuló forgalmat.

![Földrajzilag elosztott minta](media/pattern-geo-distributed/geo-distribution.png)

## <a name="components"></a>Összetevők

### <a name="outside-the-cloud"></a>A felhőn kívül

#### <a name="traffic-manager"></a>Traffic Manager

A diagramon Traffic Manager a nyilvános felhőn kívül található, de a helyi adatközpontban és a nyilvános felhőben is képesnek kell lennie a forgalom koordinálására. A Balancer a földrajzi helyszínekre irányítja a forgalmat.

#### <a name="domain-name-system-dns"></a>Tartománynévrendszer (DNS)

A tartománynévrendszer vagy a DNS a webhely vagy szolgáltatás nevének az IP-címére való fordítására (vagy feloldására) felelős.

### <a name="public-cloud"></a>Nyilvános felhő

#### <a name="cloud-endpoint"></a>Felhőbeli végpont

A nyilvános IP-címek használatával a bejövő forgalom átirányítható a Traffic Managerrel a nyilvános Cloud app Resources végpontra.  

### <a name="local-clouds"></a>Helyi felhők

#### <a name="local-endpoint"></a>Helyi végpont

A nyilvános IP-címek használatával a bejövő forgalom átirányítható a Traffic Managerrel a nyilvános Cloud app Resources végpontra.

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

A minta megvalósítása során az alábbi pontokat vegye figyelembe:

### <a name="scalability"></a>Méretezhetőség

A minta a földrajzi forgalom útválasztását kezeli, és nem méretezhető, hogy megfeleljen a forgalom növekedésének. Ezt a mintát azonban más Azure-és helyszíni megoldásokkal is kombinálhatja. Ez a minta például a több felhőre kiterjedő skálázási mintával használható.

### <a name="availability"></a>Rendelkezésre állás

Győződjön meg arról, hogy a helyileg telepített alkalmazások magas rendelkezésre állásra vannak konfigurálva a helyszíni hardverkonfiguráció és a szoftverek központi telepítése révén.

### <a name="manageability"></a>Kezelhetőség

A minta zökkenőmentes felügyeletet és ismerős felületet biztosít a környezetek között.

## <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?

- A szervezetem olyan nemzetközi ágakat tartalmaz, amelyek egyéni regionális biztonsági és terjesztési házirendeket igényelnek.
- A szervezeti irodák mindegyike lekéri az alkalmazottakat, az üzleti és a létesítményi tevékenységeket, amelyek helyi szabályozással és időzónával kapcsolatos jelentési tevékenységet igényelnek.
- A nagy léptékű követelmények az alkalmazások horizontális felskálázásával hajthatók végre, több alkalmazás üzembe helyezése egyetlen régióban és régiókban a szélsőséges terhelési követelmények kezelése érdekében.
- Az alkalmazások számára elérhetőnek kell lennie, és az ügyfelek kéréseire is reagálni kell, még az egyrégiós kimaradások esetében is.

## <a name="next-steps"></a>További lépések

További információ a cikkben bemutatott témakörökről:

- A DNS-alapú forgalom terheléselosztó működésével kapcsolatos további információkért tekintse meg az [Azure Traffic Manager áttekintését](/azure/traffic-manager/traffic-manager-overview) .
- Az ajánlott eljárásokról és a további kérdésekre adott válaszokért lásd a [hibrid alkalmazások kialakításával kapcsolatos szempontokat](overview-app-design-considerations.md) .
- A termékek és megoldások teljes portfóliójának megismeréséhez tekintse meg a [Azure stack termékcsaládot és megoldásokat](/azure-stack) .

Ha készen áll a megoldás tesztelésére, folytassa a [földrajzilag elosztott app Solution telepítési útmutatóval](solution-deployment-guide-geo-distributed.md). A telepítési útmutató részletes útmutatást nyújt az összetevők üzembe helyezéséhez és teszteléséhez. Megtudhatja, hogyan irányíthatja át a forgalmat adott végpontokra, a földrajzilag elosztott alkalmazás mintáját használó különféle mérőszámok alapján. A Traffic Manager-profilok földrajzi alapú útválasztási és végponti konfigurációval való létrehozása biztosítja az információk átirányítását a végpontok számára a regionális követelmények, a vállalati és a nemzetközi szabályozás, valamint az adatok igényei alapján.
