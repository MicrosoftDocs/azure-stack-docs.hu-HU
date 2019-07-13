---
title: Az intelligens peremhálózaton egyaránt az Azure Stack fejlesztési és üzemeltetési minta |} A Microsoft Docs
description: További tudnivalók az intelligens peremhálózaton egyaránt az Azure Stack fejlesztési és üzemeltetési minta
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
ms.openlocfilehash: 76437cd37733984d3230d4c40ccc82c7e6ede2b9
ms.sourcegitcommit: 51ec68b5e6dbf437aaca19a9f35ba07d2c402892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856386"
---
# <a name="devops-pattern"></a>Fejlesztési és üzemeltetési minta

Kód egyetlen helyről, és üzembe helyezése fejlesztési, tesztelési és éles környezetekben, amelyek a helyi adatközpontban, privát felhőket vagy a nyilvános felhőben lehetnek több cél.

## <a name="context-and-problem"></a>Kontextus és probléma

Alkalmazások üzembe helyezési elérhetőségének folytonosságát, biztonságot és megbízhatóságot olyan szervezetek alapvető fontosságú, kritikus fontosságú fejlesztői csoportjaink számára.

Alkalmazások gyakran igényelnek újratervezhetők kód minden célként megadott környezetben való futtatásához. Ez azt jelenti, hogy az alkalmazás ne legyen teljesen hordozható. Azt kell kell frissíteni, és tesztelését, minden környezetben keresztül áthelyezi azt. Például egy fejlesztési környezetben írt kóddal kell majd feladatátvitelt tesztelési környezetben működik, és újraírja, ha végül azok éles környezetben. Ezenkívül ez a kód kifejezetten vannak kötve a gazdagéphez. Ez növeli a költséges és bonyolult az alkalmazás karbantartása. Az alkalmazás minden egyes verziója minden környezethez van kötve. A nagyobb összetettséget és a párhuzamos növelheti a biztonság és a kód minőségének kockázatát. Emellett a kód nem lehet könnyen újratelepítése távolítsa el őket a visszaállítás sikertelen volt, vagy kezelje az igény növekedésével további gazdagépként használandó számítógépek telepítéséhez.

## <a name="solution"></a>Megoldás

A fejlesztési és üzemeltetési minta lehetővé teszi, hogy összeállítását, tesztelését és helyezhet üzembe egy alkalmazást, amely több felhő futtat. Ez a minta több egység a gyakorlat, folyamatos integrációt és teljesítést. Folyamatos integráció, a kód a fejlesztett és tesztelt minden alkalommal, amikor egy csapattag változást véglegesíti a verziókezeléshez. Folyamatos készregyártás automatizálja a build éles környezetben minden egyes lépést. Ezek a folyamatok készítsen egy kiadási folyamatot, amely támogatja az üzembe helyezés különböző környezetek között. Ezzel a mintával vázlatszintű a kódot, és telepítheti egy helyszíni környezetben, más magánfelhők és a nyilvános felhők ugyanazt a kódot. Különbségek a környezetben van szükség, egy konfigurációs fájl módosítása helyett a kód módosításait.

![Fejlesztési és üzemeltetési minta](media/azure-stack-edge-pattern-hybrid-ci-cd/hybrid-ci-cd.png)

A fejlesztői eszközöket a helyszíni, magán- és nyilvános felhőkörnyezetek közötti konzisztens a folyamatos integrációt és teljesítést gyakorlata valósíthat meg. Alkalmazások és szolgáltatások a fejlesztési és üzemeltetési minta használatával üzembe helyezett felcserélhetők, és ezek a helyek, kihasználva a helyszíni és a nyilvános felhőbeli szolgáltatások és funkciók bármelyikét futtathatja.

A DevOps használatával kibocsátási folyamatok nyújt segítséget:

-   Kód véglegesítése a egyetlen tárházat alapján új build kezdeményezni.

-   Az újonnan létrehozott kód automatikus üzembe helyezése a felhasználói tesztelés a nyilvános felhőben.

-   Automatikus központi telepítése magánfelhőbe kódja megfelelt a tesztelés után.

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

A fejlesztési és üzemeltetési minta célja a célkörnyezet függetlenül üzembe helyezett konzisztencia biztosításához. Képességek eltérő lehet azonban, felhőbeli és helyszíni környezetek között. A következőket ajánljuk figyelmébe:

-   Az funkciók, a végpontok, szolgáltatások és egyéb erőforrások a központi telepítésben érhető el az üzembe helyezés célhelyek?

-   Konfigurációs összetevők helyen tárolódnak, amely több felhő között érhető el?

-   Működni fog az üzembe helyezéshez megadott paraméterek a cél-környezetekben?

-   Erőforrás-specifikus tulajdonságokat az összes cél felhő érhető el?

További információkért lásd: [felhőalapú konzisztencia fejlesztése az Azure Resource Manager-sablonokkal](https://docs.microsoft.com/azure/azure-resource-manager/templates-cloud-consistency).

Emellett az Ez a minta megvalósítása során vegye figyelembe a következőket:

### <a name="scalability-considerations"></a>Méretezési szempontok

Üzembe helyezési automatizálás rendszereket a fő ellenőrzési pontot a DevOps-minták. Megvalósításokhoz eltérőek lehetnek. A kijelölés a kiszolgáló megfelelő méretű számítási feladatok várható méretétől függ. Virtuális gépek költség a méretezési csoport több mint a tárolóké. Ha azonban tárolókat használna a skálázáshoz, a build-folyamatoknak tárolókkal kell futnia.

### <a name="availability-considerations"></a>Rendelkezésre állási szempontok

A DevPattern kontextusában rendelkezésre állási azt jelenti, hogy képesek helyreállítani kapcsolatos állapotinformációkat társított a-munkafolyamatban például a vizsgálati eredmények, szintű kódok függőségeinek és más összetevőket. A rendelkezésre állásra vonatkozó követelmények felméréséhez két általános mérőszámot érdemes figyelembe vennie:

-   A helyreállítási időre vonatkozó célkitűzés (RTO) meghatározza, hogyan mennyi ideig üzemelhet a rendszer nélkül.

-   A helyreállítási időkorlátot (RPO) azt jelzi, hogy mennyi adatot, ha a szolgáltatás megengedhet érint a rendszer.

A gyakorlatban RTO és RPO hasonló redundancia és a biztonsági mentés. A globális Azure-felhő, rendelkezésre állás nem hardveres helyreállítás kérdése – részét képező Azure – de inkább biztosítását jelenti a fejlesztési és üzemeltetési rendszerek állapotát. Az Azure Stacken hardveres helyreállítás lehet veszi figyelembe.

Egy másik fő szempont az üzembe helyezési automatizálást használni a rendszer tervezésekor a hozzáférés-vezérléshez és a szolgáltatások üzembe helyezéséhez a felhőbeli környezetekhez szükséges jogokat megfelelő kezelésének. Melyik jogosultságok szükségesek, létrehozása, törlése, vagy módosíthatja a központi telepítések? Jogok egy halmaza például általában szükséges hozzon létre egy erőforráscsoportot az Azure, és a egy másik erőforráscsoportban szolgáltatások üzembe helyezéséhez.

### <a name="manageability-considerations"></a>Felügyeleti szempontok

A fejlesztési és üzemeltetési minta alapján rendszer kialakításától figyelembe kell vennie az automation-naplózás és az értesítések küldése az egyes szolgáltatások között a portfólió. Megosztott szolgáltatások, az alkalmazás csapatához vagy mindkettőt, és nyomon követheti a biztonsági szabályzatok és cégirányítási is.

Éles környezetben és a fejlesztési/tesztkörnyezeteket külön erőforráscsoportokban az Azure- vagy Azure Stack üzembe helyezése. Ezután figyelheti az egyes környezetek erőforrásait, és a költségeket erőforráscsoportonként. Erőforrások készletként, amely hasznos tesztkörnyezetek esetében is törölheti.

## <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?

Használja a következő mintát a következő helyzetekben:

-   Egy adott környezetben, amely a fejlesztők igényeit egyaránt kielégíti a kód fejlesztéséhez és a egy adott környezetben, ahol nehéz lehet fejleszthet új kódot a megoldás üzembe helyezése.

-   A kód és a fejlesztők szeretne velünk a kapcsolatot, amennyiben azok a folyamatos integrációt és folyamatos készregyártás a fejlesztési és üzemeltetési minta folyamatot követve eszközöket használhatja.

Ez a minta nem ajánlott a következő helyzetekben:

-   Ha nem automatizálja infrastruktúra kiépítése az erőforrások, konfigurációs, identitáskezelési és biztonsági feladatokat.

-   Ha a csapatok nincs hozzáférése a folyamatos integráció/folyamatos fejlesztési (CI/CD) megközelítés megvalósításához a hibrid felhőalapú erőforrásokhoz.

## <a name="example"></a>Példa

Ismerje meg, hogyan helyezhet üzembe egy alkalmazást az Azure és az Azure Stack egy hibrid folyamatos integráció/folyamatos teljesítés (CI/CD) folyamat használatával.

[Alkalmazások üzembe helyezése az Azure és az Azure Stackben](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline)

## <a name="next-steps"></a>További lépések

Ismerje meg [hibrid felhő tervezési minták az Azure Stackhez](azure-stack-edge-pattern-overview.md)
