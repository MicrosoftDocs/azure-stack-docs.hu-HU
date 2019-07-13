---
title: Az intelligens peremhálózaton egyaránt az Azure Stack használatával a fejlesztési és üzemeltetési minta |} A Microsoft Docs
description: Ismerje meg az intelligens peremhálózaton egyaránt az Azure Stack fejlesztési és üzemeltetési minta.
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
ms.date: 07/11/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 07e0c3f0ca52c7079b879050ab36bf6a5f166f92
ms.sourcegitcommit: 51ec68b5e6dbf437aaca19a9f35ba07d2c402892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856364"
---
# <a name="geo-distributed-pattern"></a>Földrajzilag elosztott minta

Adja meg az alkalmazás végpontjainak több régióban. Útvonal felhasználói forgalom helyét és megfelelőségi igényeinek megfelelően.

## <a name="context-and-problem"></a>Kontextus és probléma

Wide elérése szervezetekre földrajzi területeken arra törekszik, hogy biztonságosan és nagy pontossággal terjesztése és adatokhoz való hozzáférés engedélyezéséhez szükséges szintű biztonsági, megfelelőségi és teljesítmény / felhasználó, helyen és eszközön határokon biztosítva.

## <a name="solution"></a>Megoldás

Az Azure Stack a földrajzi forgalom-útválasztási minta vagy Geo-Distributed alkalmazások, engedélyezi a forgalmat a rendszer az eszközspecifikus végpontokat különböző metrikák alapján. Útvonalakat hoz létre egy Traffic Manager földrajzi alapú útválasztást és a végpont-konfiguráció-végpontokra irányuló forgalom alapján regionális követelmények, a vállalati és nemzetközi szabályozás és az adatok igényeinek megfelelően.

![Földrajzilag elosztott minta](media/azure-stack-edge-pattern-geo-distribution/geo-distribution.png)

**Traffic Manager**  
Az ábrán ez a nyilvános felhőn kívül, de azt tudja a forgalmat a helyi adatközpontban és a nyilvános felhőben is működnie kell. A terheléselosztó átirányítja a forgalmat a földrajzi helyeket.

**Tartománynévrendszer (DNS)**  
A Domain Name System, vagy a DNS-beli felelős fordítása (vagy feloldása) az IP-címét a webhely vagy szolgáltatás nevét.

### <a name="public-cloud"></a>Nyilvános felhő

**Felhőbeli végpont**  
A bejövő forgalom a traffic manager átirányítása a nyilvános felhőben alkalmazásvégpont erőforrások nyilvános IP-címek használhatók.  

### <a name="local-clouds"></a>Helyi felhő

**Helyi végpont**  
A bejövő forgalom a traffic manager átirányítása a nyilvános felhőben alkalmazásvégpont erőforrások nyilvános IP-címek használhatók.

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

A minta megvalósítása során az alábbi pontokat vegye figyelembe:

### <a name="scalability-considerations"></a>Méretezési szempontok

A minta kezeli a földrajzi forgalom útválasztási méretezésről, hogy megfeleljenek a megnövekedett forgalom helyett. Ez a minta kombinálhatja azonban más Azure-ban és a helyszíni megoldásokkal. Ez a minta például használható a több felhőre kiterjedő skálázás mintával.

### <a name="availability-considerations"></a>Rendelkezésre állási szempontok

Győződjön meg arról helyileg telepített alkalmazások magas rendelkezésre állású keresztül a helyszíni hardver konfiguráció és a szoftverek központi telepítési vannak konfigurálva.

### <a name="manageability-considerations"></a>Felügyeleti szempontok

A minta biztosítja a zökkenőmentes kezelés és ismerős felületet környezetek között.

## <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?

-   Nemzetközi, regionális egyéni biztonsági és terjesztési házirendeket igénylő ágak rendelkező.

-   A szervezet irodával mindegyikének lekéri az alkalmazottak, az üzleti és létesítmény, tevékenység / helyi szabályozásokról és időzóna reporting igénylő.

-   Vízszintes horizontális felskálázás alkalmazások esetében kerül sor egy adott régión belül, valamint a régióban, annak érdekében rendkívüli terhelést jelent, hogy több alkalmazást üzembe helyezés az nagy méretű követelmények is kielégíthetők.

-   Az alkalmazások magas rendelkezésre állású és válaszol-e az ügyfelektől érkező kérelmeket, még akkor is egy adott régión szolgáltatáskimaradás esetén kell lennie.

## <a name="example"></a>Példa

Útmutató a forgalmat a földrajzilag elosztott alkalmazások minta használatával különböző metrikák alapján meghatározott végpontokhoz. Egy Traffic Manager létrehozása a földrajzi alapú útválasztást és a végpont konfigurációs profil biztosítja, információk alapján a regionális követelmények, a vállalati és a nemzetközi szabályozás és az adattárolási igényeinek végpontok van irányítva.

[Földrajzilag elosztott alkalmazás megoldás létrehozása az Azure és az Azure Stackben](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-geo-distributed)

## <a name="next-steps"></a>További lépések

Ismerje meg [hibrid felhő tervezési minták az Azure Stackhez](azure-stack-edge-pattern-overview.md)
