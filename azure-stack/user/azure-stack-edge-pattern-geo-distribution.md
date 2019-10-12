---
title: Az intelligens peremhálózat DevOps mintázata Azure Stack | Microsoft Docs
description: Ismerkedjen meg az intelligens peremhálózat DevOps-mintázatával Azure Stackával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: deab520045d50acefb03691b9b127f99676061a0
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277717"
---
# <a name="geo-distributed-pattern"></a>Földrajzilag elosztott minta

Több régióban is biztosíthatja az alkalmazások végpontját. A felhasználói forgalom irányítása a hely és a megfelelőségi igények alapján.

## <a name="context-and-problem"></a>Kontextus és probléma

Azok a szervezetek, amelyek széles körű földrajzi területtel rendelkeznek, az adathozzáférés biztonságos és pontos elosztására és engedélyezésére törekednek, miközben a biztonság, a megfelelőség és a teljesítmény szükséges szintje felhasználónként, helyen és eszközön a határokon belül.

## <a name="solution"></a>Megoldás

A Azure Stack földrajzi forgalom-útválasztási minta vagy földrajzilag elosztott alkalmazások lehetővé teszik, hogy a forgalom a különböző metrikák alapján meghatározott végpontokra legyen irányítva. A földrajzi alapú útválasztási és végponti konfigurációval rendelkező Traffic Managerek a regionális követelmények, a vállalati és a nemzetközi szabályozás, valamint az adatszükségletek alapján irányítják át a végpontokra irányuló forgalmat.

![Földrajzilag elosztott minta](media/azure-stack-edge-pattern-geo-distribution/geo-distribution.png)

**Traffic Manager**  
A diagramon ez a nyilvános felhőn kívül található, de a helyi adatközpontban és a nyilvános felhőben is képesnek kell lennie a forgalom koordinálására. A Balancer a földrajzi helyszínekre irányítja a forgalmat.

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

### <a name="scalability-considerations"></a>Méretezési szempontok

A minta a földrajzi forgalom útválasztását kezeli, és nem méretezhető, hogy megfeleljen a forgalom növekedésének. Ezt a mintát azonban más Azure-és helyszíni megoldásokkal is kombinálhatja. Ez a minta például a több felhőre kiterjedő skálázási mintával használható.

### <a name="availability-considerations"></a>Rendelkezésre állási szempontok

Győződjön meg arról, hogy a helyileg telepített alkalmazások magas rendelkezésre állásra vannak konfigurálva a helyszíni hardverkonfiguráció és a szoftverek központi telepítése révén.

### <a name="manageability-considerations"></a>Felügyeleti szempontok

A minta zökkenőmentes felügyeletet és ismerős felületet biztosít a környezetek között.

## <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?

-   A szervezetem olyan nemzetközi ágakat tartalmaz, amelyek egyéni regionális biztonsági és terjesztési házirendeket igényelnek.

-   A szervezeti irodák mindegyike lekéri az alkalmazottakat, az üzleti és a létesítményi tevékenységeket, amelyek helyi szabályozással és időzónával kapcsolatos jelentési tevékenységet igényelnek.

-   A nagy léptékű követelmények az alkalmazások horizontális felskálázásával hajthatók végre, több alkalmazás üzembe helyezése egyetlen régióban, valamint régiók között, a szélsőséges terhelési követelmények kezelése érdekében.

-   Az alkalmazásoknak rendkívül elérhetőnek kell lenniük, és az ügyfelek kéréseire kell reagálnia, még akkor is, ha egyetlen régió meghibásodása esetén.

## <a name="example"></a>Példa

Megtudhatja, hogyan irányíthatja át a forgalmat adott végpontokra különböző mérőszámok alapján a földrajzilag elosztott alkalmazások mintájának használatával. A Traffic Manager-profilok földrajzi alapú útválasztási és végponti konfigurációval való létrehozása biztosítja az információk átirányítását a végpontok számára a regionális követelmények, a vállalati és a nemzetközi szabályozás, valamint az adatok igényei alapján.

[Földrajzilag elosztott app-megoldás létrehozása az Azure-ban és Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-geo-distributed)

## <a name="next-steps"></a>Következő lépések

Ismerje meg a [hibrid felhő kialakítási mintáit Azure stack](azure-stack-edge-pattern-overview.md)
