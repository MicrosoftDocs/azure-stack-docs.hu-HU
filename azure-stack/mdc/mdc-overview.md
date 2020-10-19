---
title: A moduláris adatközpont áttekintése |
description: A moduláris adatközpont egy hordozható, gyorsan telepíthető adatközpont, amely alkalmas a nagyméretű, ideiglenes és rögzített értékű parancssori műveletek támogatásához.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: overview
ms.date: 01/13/2020
ms.reviewer: prchint
ms.lastreviewed: 01/13/2020
ms.openlocfilehash: d2803f1968be7a4a3e64f65f3d86a2dbb41aaf1c
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182964"
---
# <a name="modular-data-center-overview"></a>A moduláris adatközpont áttekintése 

A moduláris adatközpont (MDC) Azure Stack hub-alapú. A MDC egy hordozható, gyorsan telepíthető adatközpont, amely alkalmas a nagyméretű harci műveletek támogatására az ideiglenes és rögzített parancsokban.

Az Azure Stack hub egy vízszintesen és vertikálisan méretezhető megoldás, amely több-bérlős, natív hibrid felhős képességeket biztosít a IaaS és a Péter-szolgáltatások számára az Edge környezetekhez, és számos moduláris forgatókönyvet támogat az ideiglenes és a rögzített parancsok és az expedíciós erők számára. Az Azure Stack hub egy integrált hardver-és szoftver-berendezés, amely számos különböző kapacitásban kapható a csomópont-méretezési egységeken keresztül, és a bővítményekkel érhető el, beleértve a általános célú grafikus feldolgozási egység (GPU) által engedélyezett konfigurációkat és a bővíthető külső tárolót.

## <a name="how-you-can-use-the-mdc"></a>A MDC használata

A Azure Stack négy alapvető alapelvet biztosít, amelyek összhangban vannak az Azure képességeivel, és bővítik a moduláris Edge-forgatókönyveket. 

### <a name="develop-and-deliver-apps-with-a-common-devops-model-including-api-symmetry-with-azure"></a>Alkalmazások fejlesztése és továbbítása közös DevOps-modellel, beleértve az API-szimmetriát az Azure-ban

Az Azure és a Azure Stack közötti konzisztencia azt jelenti, hogy a War Fighter megoldásait egyszer kell kialakítani, a különböző használati esetek támogatásához, valamint a közös eszközök, például a Azure Key Vault a kulcsfontosságú felügyelethez, valamint az erőforrás-figyeléshez és-felügyelethez Azure Monitor. A Azure Stack a helyszíni adatokkal, alkalmazásokkal és eszközökkel, valamint DevOps és biztonságos műveletekkel (például a kulcskezelő szolgáltatással) való együttműködésre használható.

### <a name="deliver-azure-services-on-premises"></a>Helyszíni Azure-szolgáltatások megvalósítása

A Azure Stack a hátrányos helyzetű (megtámadó, túlterhelt vagy megtagadott) kommunikációs környezetekben, valamint robusztus kommunikációs környezetekben futnak, és nem függ az Azure-hoz való kapcsolattól a Mission alkalmazások végrehajtásához és a helyi műveletek engedélyezéséhez. 

### <a name="use-integrated-hardware-and-software-delivery-experience"></a>Integrált hardveres és szoftveres kézbesítési élmény használata

A moduláris Edge számos olyan képességet igényel, amely nem csak az alapkonfigurációt és a tárolási szolgáltatásokat nyújtja, hanem a gépi tanulás, az AI és az elemzés speciális funkcióit is. A biztonságos felhőhöz való csatlakozás lehetősége a moduláris szélétől, ha az képes, vagy fordítva a megszorító környezetekben való egymástól függetlenül működik, kritikus fontosságú ahhoz, hogy a háborús harcosok hozzáférhessenek a döntéshozatalhoz szükséges adatokhoz.

### <a name="keep-your-datacenter-secure-and-available-with-hybrid-cloud-security-operations"></a>Az adatközpont biztonságos és hibrid Felhőbeli biztonsági műveletekkel is elérhető

A Azure Stack Felhőbeli natív kialakítása eltávolítja a hagyományos virtualizációs környezetek működési bonyolultságát azáltal, hogy a rendszergazdák kiválaszthatják, hogy mikor kell kiválasztaniuk a teljes műveletet a telepítési folyamaton keresztül, valamint a rendszerbe natív módon épített Azure Stack felügyeleti hálót.

A Microsoft által felügyelt, illetve a Microsoft által kezelt, a Azure Stack eszközökhöz készült szolgáltatással végzett felügyeletet engedélyezheti, ha a felhasználók számára megfelelő hozzáférést biztosít a RBAC a felügyeleti portálon, a privilegizált felügyeleti végponton vagy a parancssori felületen keresztüli műveletek végrehajtásához. Ez lehetővé teszi az összes javítási és egyéb felügyeleti és figyelési tevékenység végrehajtását a Microsoft számára. Azure Stack a mező bővíthető és frissíthető a biztonságos felügyeleti portálon vagy a Command-Line Interface (CLI) parancsokon keresztül, amelyek mind a biztonsági, mind a IaaS-és a Péter-funkciók frissítésével biztosítják a kereskedelmi paritás az Azure-ban való fenntartását, ahol ez a helyi vagy távoli hálózatokon 

## <a name="benefits-of-using-the-mdc"></a>A MDC használatának előnyei

A MDC egységes környezetet támogat az Azure-ban a hátrányos kommunikációs környezetekben:
 - Statikus, moduláris, gyors üzembe helyezhető adatközpontok az Azure Cloud Services használatával a nagy elemzési alkalmazások számára a moduláris operatív központokban (TOC).
 - Az Azure moduláris peremhálózat-kínálata biztosítja a felhő és a peremhálózat közötti következetességet, és egy egyedülálló módszert kínál a IaaS-primitívek, például a virtuális gépek, a tárolók és a virtuális hálózatok támogatásához.
 - Azure Active Directory és szerepköralapú hozzáférés-vezérlés támogatása
 - Általános felügyeleti felületek
 - API-szimmetria és a Microsoft, harmadik féltől származó és nyílt forráskódú DevOps-eszközök támogatása
 - Felügyelet és monitorozás az Azure Log Analytics és Azure Security Center használatával
 - A hibrid felhő lehetővé teszi a felhő-számítástechnika rugalmasságát a helyszíni környezetben és az Edge-ben.<br>A következőket teheti:
     - Az Azure-ban és a helyszíni környezetekben konzisztens módon használhat programkódot, és a felhőben natív alkalmazásokat is futtathat.
     - Hagyományos virtualizált számítási feladatok futtatása az Azure-szolgáltatásokhoz való opcionális csatlakozással.
     - Vigye át az adatait a felhőbe, vagy tartsa meg a szuverén adatközpontban a megfelelőség fenntartása érdekében.
     - Hardveres gyorsítású gépi tanulási, tárolós vagy virtualizált munkaterhelések futtatása az intelligens peremhálózat minden részén.

## <a name="next-steps"></a>Következő lépések

[Azure Stack hub kapacitásának megtervezése](../operator/azure-stack-capacity-planning-overview.md)
