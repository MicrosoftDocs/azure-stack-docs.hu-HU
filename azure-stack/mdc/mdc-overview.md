---
title: A moduláris adatközpont áttekintése |
description: Az Azure Modular Datacenter egy hordozható, gyorsan telepíthető adatközpont, amely alkalmas a nagyméretű, ideiglenes és rögzített értékű parancssori műveletek támogatásához.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: overview
ms.date: 11/06/2020
ms.reviewer: prchint
ms.lastreviewed: 11/06/2020
ms.openlocfilehash: eabafd31d25a59cd43daa1a0779afed0a80f8354
ms.sourcegitcommit: 25b234330df4e753ed2dd480c208ec88cd90234c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94924316"
---
# <a name="modular-datacenter-overview"></a>A moduláris adatközpont áttekintése

Az Azure Modular Datacenter (MDC) Azure Stack hub-on alapul. A MDC egy hordozható, gyorsan telepíthető adatközpont, amely alkalmas a nagyméretű harci műveletek támogatására az ideiglenes és rögzített parancsokban.

Az Azure Stack hub egy vízszintesen és vertikálisan méretezhető megoldás, amely több-bérlős, natív hibrid felhős képességeket biztosít a szolgáltatásként nyújtott infrastruktúra (IaaS) és a platform-szolgáltatásként szolgáló szolgáltatások (Pásti) számára a peremhálózati környezetekben. Az Azure Stack hub számos moduláris forgatókönyvet támogat az ideiglenes és a rögzített parancsok és az expedíciós erők számára.

Az Azure Stack hub egy integrált hardver-és szoftver-berendezés, amely számos különböző kapacitásban kapható a csomópontok méretezési egysége alapján. Az Azure Stack hub bővítményekkel érhető el, amelyek közé tartoznak az általános célú grafikus feldolgozási egység (GPU) által engedélyezett konfigurációk és a bővíthető külső tárolók.

## <a name="use-mdc"></a>MDC használata

A Azure Stack négy alapvető alapelvet biztosít, amelyek összhangban vannak az Azure képességeivel, és bővítik a moduláris Edge-forgatókönyveket.

### <a name="develop-and-deliver-apps-with-a-common-devops-model-including-api-symmetry-with-azure"></a>Alkalmazások fejlesztése és továbbítása közös DevOps-modellel, beleértve az API-szimmetriát az Azure-ban

Az Azure és a Azure Stack közötti konzisztencia azt jelenti, hogy a megoldások egyszer lettek kifejlesztve, a különböző használati esetek támogatásához, valamint az eszközök közös eszközkészletének használatával biztosítva és tartósan. Többek között az erőforrás-figyeléshez és-felügyelethez Azure Key Vault a kulcskezelő és a Azure Monitor. A Azure Stack a helyszíni adatokkal, alkalmazásokkal és eszközökkel, valamint DevOps és biztonságos műveletekkel (például a kulcskezelő szolgáltatással) való együttműködésre használható.

### <a name="deliver-azure-services-on-premises"></a>Helyszíni Azure-szolgáltatások megvalósítása

A Azure Stack a hátrányos helyzetű (nem tesztelt, túlzsúfolt vagy megtagadott) kommunikációs környezetekben és robusztus kommunikációs környezetekben futnak. A Azure Stack nem függ az Azure-hoz való kapcsolattól a Mission alkalmazások végrehajtásához és a helyi műveletek engedélyezéséhez.

### <a name="use-integrated-hardware-and-software-delivery-experience"></a>Integrált hardveres és szoftveres kézbesítési élmény használata

A moduláris Edge számos olyan képességet igényel, amely alapkonfigurációt és tárolási szolgáltatásokat biztosít, valamint a gépi tanulás, az AI és az elemzés speciális képességeit is. A biztonságos felhőhöz való csatlakozás lehetősége a moduláris szélétől, amikor a vagy a szolgáltatástól függetlenül, a megszorító környezetekben való működtetése kritikus fontosságú a döntések meghozatalához szükséges adatokhoz való hozzáférés biztosításához.

### <a name="keep-your-datacenter-secure-and-available-with-hybrid-cloud-security-operations"></a>Az adatközpont biztonságos és hibrid Felhőbeli biztonsági műveletekkel is elérhető

A Azure Stack Felhőbeli natív kialakítása eltávolítja a hagyományos virtualizációs környezetek működési bonyolultságát. A rendszergazdák kiválaszthatják, hogy mikor kell a teljes műveletet a telepítési folyamaton és a rendszerbe natív módon beépített Azure Stack felügyeleti hálón kijavítani és összehangolni.

A Microsoft vagy a Microsoft által felügyelt, a Azure Stack eszközökhöz tartozó területen végzett Távsegítség-felügyeletet engedélyezheti, ha a felhasználók számára megfelelő hozzáférést biztosít a szerepköralapú hozzáférés-vezérlés (RBAC) segítségével, hogy műveleteket hajtson végre a felügyeleti portálon, a privilegizált felügyeleti végponton vagy a parancssori felületen (CLI). Ez a funkció lehetővé teszi az összes javítási és egyéb felügyeleti és figyelési tevékenység végrehajtását a Microsoft számára. Azure Stack a mező a biztonságos felügyeleti Portálon és a CLI-parancsokon keresztül frissíthető, és a biztonsági frissítéseket, a IaaS és a Pásti-frissítéseket egyaránt alkalmazza a kereskedelmi paritás az Azure-ban való fenntartására, ahol szükséges, helyi vagy távoli hálózatokból.

## <a name="benefits-of-using-mdc"></a>A MDC használatának előnyei

A MDC egységes környezetet támogat az Azure-ban a hátrányos kommunikációs környezetekben:

 - Statikus, moduláris, gyors üzembe helyezhető adatközpontok az Azure Cloud Services szolgáltatással a nagyméretű analitikai alkalmazások számára moduláris operatív központokban.
 - Az Azure Modular Edge-ajánlatai a felhő és a peremhálózat közötti konzisztencia fenntartására szolgálnak a IaaS primitívek, például a virtuális gépek, a tárolók és a virtuális hálózatok támogatásához.
 - Azure Active Directory és RBAC támogatása.
 - Gyakori felügyeleti felületek.
 - API-szimmetria és a Microsoft, harmadik féltől származó és nyílt forráskódú DevOps-eszközök támogatása.
 - Felügyelet és monitorozás az Azure Log Analytics és Azure Security Center használatával.
 - A felhőalapú számítástechnika rugalmassága a helyszíni környezet és a peremhálózat számára a hibrid felhő engedélyezése révén.<br>A következőket teheti:
     - Az Azure-ban és a helyszíni környezetekben konzisztens módon használhat programkódot, és a felhőben natív alkalmazásokat is futtathat.
     - Hagyományos virtualizált számítási feladatok futtatása az Azure-szolgáltatásokhoz való opcionális csatlakozással.
     - Vigye át az adatait a felhőbe, vagy tartsa meg a szuverén adatközpontban a megfelelőség fenntartása érdekében.
     - Hardveres gyorsítású gépi tanulási, tárolós vagy virtualizált munkaterhelések futtatása az intelligens peremen.

## <a name="next-steps"></a>Következő lépések

[Azure Stack hub kapacitásának megtervezése](../operator/azure-stack-capacity-planning-overview.md)
