---
title: Az intelligens peremhálózat DevOps mintázata az Azure és a Azure Stack hub használatával.
description: Ismerje meg az intelligens peremhálózat DevOps mintázatát az Azure és a Azure Stack hub használatával.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: f774fb0382409d5674c622f289767ca8ae81effb
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73638672"
---
# <a name="devops-pattern"></a>DevOps minta

Kód egyetlen helyről, és üzembe helyezése több célra olyan fejlesztési, tesztelési és éles környezetekben, amelyek lehetnek a helyi adatközpontban, a privát felhőkben vagy a nyilvános felhőben.

## <a name="context-and-problem"></a>Kontextus és probléma

Az alkalmazások üzembe helyezésének folytonossága, biztonsága és megbízhatósága elengedhetetlen a szervezetek számára, és kritikus fontosságú a fejlesztési csapatoknak.

Az alkalmazások gyakran megkövetelik, hogy az egyes célszámítógépeken fusson az újraszámított kód. Ez azt jelenti, hogy egy alkalmazás nem teljesen hordozható. Frissíteni, tesztelni és érvényesíteni kell, ahogy az az egyes környezeteken halad át. Például egy fejlesztési környezetben írt kódot újra kell írni, hogy a tesztelési környezetben működjön, és újra kell írni, amikor végül éles környezetben landol. Továbbá ez a kód kifejezetten a gazdagéphez van kötve. Ez növeli az alkalmazás fenntartásának költségeit és összetettségét. Az alkalmazás minden verziója az egyes környezetekhez van kötve. A megnövekedett összetettség és a megkettőzés növeli a biztonság és a kód minőségének kockázatát. Emellett a kód nem helyezhető újra üzembe, ha eltávolítja a visszaállítási sikertelen gazdagépeket, vagy további gazdagépeket telepít az igény szerinti növekedés kezelésére.

## <a name="solution"></a>Megoldás

A DevOps minta lehetővé teszi több felhőben futó alkalmazások készítését, tesztelését és üzembe helyezését. Ez a minta a folyamatos integráció és a folyamatos teljesítés gyakorlatát egyesíti. A folyamatos integráció révén a kód minden alkalommal felépítve és tesztelve lett, amikor egy csapattag véglegesíti a verziókövetés változását. A folyamatos teljesítés automatizálja az egyes lépéseket a buildről a termelési környezetbe. Ezek a folyamatok együttesen olyan kiadási folyamatot hoznak létre, amely a különböző környezetekben történő telepítést támogatja. Ezzel a mintával előkészítheti a kódot, majd telepítheti ugyanazt a kódot egy helyi környezetbe, a különböző privát felhőkbe és a nyilvános felhőkbe. A környezetbeli különbségekhez a kód módosítása helyett konfigurációs fájlra van szükség.

![DevOps minta](media/pattern-cicd-pipeline/hybrid-ci-cd.png)

A helyszíni, a saját Felhőbeli és a nyilvános felhőalapú környezetek egységes fejlesztési eszközeivel a folyamatos integráció és a folyamatos teljesítés gyakorlata valósítható meg. A DevOps mintázattal üzembe helyezett alkalmazások és szolgáltatások felcserélhetők, és bármelyik helyen futhatnak, így kihasználhatják a helyszíni és a nyilvános Felhőbeli funkciókat és képességeket.

A DevOps kiadási folyamata a következőket teszi lehetővé:

- Hozzon létre egy új buildet a kód alapján véglegesítve egyetlen adattárra.
- Az újonnan létrehozott kód automatikus üzembe helyezése a nyilvános felhőben a felhasználók elfogadásának teszteléséhez.
- Automatikus üzembe helyezés a privát felhőben, ha a kód sikeresen átadta a tesztelést.

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

A DevOps minta az üzemelő példányok közötti konzisztencia biztosítására szolgál, függetlenül a célként megadott környezettől. A képességek azonban eltérőek a Felhőbeli és a helyszíni környezetek között. A következőket ajánljuk figyelmébe:

- Elérhetők a központi telepítésben szereplő függvények, végpontok, szolgáltatások és egyéb erőforrások a cél telepítési helyein?
- A a felhőben elérhető helyen tárolt konfigurációs összetevők?
- Az üzembe helyezési paraméterek az összes cél környezetben működnek?
- Az erőforrás-specifikus tulajdonságok elérhetők az összes cél felhőkben?

További információ: Azure Resource Manager- [sablonok fejlesztése a felhő konzisztenciája](https://docs.microsoft.com/azure/azure-resource-manager/templates-cloud-consistency)érdekében.

Emellett vegye figyelembe a következő szempontokat is a minta megvalósításának eldöntése során:

### <a name="scalability"></a>Méretezhetőség

A központi telepítési automatizálási rendszerek a DevOps-mintázatok kulcsfontosságú vezérlői pontja. A megvalósítások eltérőek lehetnek. A megfelelő kiszolgáló méretének kiválasztása a várt munkaterhelés méretétől függ. A virtuális gépek drágábbak, mint a tárolók. Ha azonban tárolókat használna a skálázáshoz, a build-folyamatoknak tárolókkal kell futnia.

### <a name="availability"></a>Rendelkezésre állás

A DevPattern kontextusában a rendelkezésre állás azt jelenti, hogy képes helyreállítani a munkafolyamathoz társított állapotinformációkat, például a teszteredmények, a kódok függőségei vagy más összetevők számára. A rendelkezésre állásra vonatkozó követelmények felméréséhez két általános mérőszámot érdemes figyelembe vennie:

-   A helyreállítási időre vonatkozó célkitűzés (RTO) határozza meg, hogy mennyi ideig mehet a rendszer nélkül.

-   A helyreállítási időkorlát (RPO) azt jelzi, hogy mennyi adat veszíthető el, ha a szolgáltatás fennakadása hatással van a rendszerre.

A gyakorlatban a RTO és a RPO a redundanciát és a biztonsági mentést jelenti. A globális Azure-felhőben a rendelkezésre állás nem a hardveres helyreállítás – amely az Azure része –, hanem a DevOps rendszerek állapotának fenntartása. Azure Stack központban a hardveres helyreállítás megfontolásra kerülhet.

Az üzembe helyezés automatizálásához használt rendszer tervezésekor egy másik fontos szempont a hozzáférés-vezérlés, valamint a szolgáltatások Felhőbeli környezetekben való üzembe helyezéséhez szükséges jogok megfelelő kezelése. Milyen jogosultságok szükségesek az üzemelő példányok létrehozásához, törléséhez vagy módosításához? Például az egyik jogosultságot általában egy erőforráscsoport létrehozásához kell létrehozni az Azure-ban, egy másikat pedig az erőforráscsoport szolgáltatásainak telepítéséhez.

### <a name="manageability"></a>Kezelhetőség

A DevOps minta alapján bármely rendszer kialakításának meg kell fontolnia az automatizálást, a naplózást és a riasztást az egyes szolgáltatásokhoz a portfólión keresztül. A megosztott szolgáltatásokat, az alkalmazások csapatait vagy mindkettőt használhatja, és nyomon követheti a biztonsági házirendeket és a szabályozást is.

Éles környezetek és fejlesztési/tesztelési környezetek üzembe helyezése különálló erőforráscsoportok az Azure-ban vagy Azure Stack hub-ban. Ezután nyomon követheti az egyes környezetek erőforrásait, és elvégezheti a számlázási költségek csoportosítását az erőforráscsoport alapján. Az erőforrásokat készletként is törölheti, ami hasznos lehet a tesztelési környezetekben való üzembe helyezéshez.

## <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?

Használja ezt a mintát, ha:

- Létrehozhat egy kódot egy olyan környezetben, amely megfelel a fejlesztői igényeknek, és a megoldásra jellemző környezetbe helyezi, ahol nehéz lehet új kódot kifejleszteni.
- Használhatja a fejlesztőknek szóló kódot és eszközöket, ha a DevOps-mintában a folyamatos integráció és a folyamatos kézbesítés folyamatát szeretné követni.

Ez a minta nem ajánlott a következő helyzetekben:

- Ha nem automatizálható az infrastruktúra, az erőforrások, a konfiguráció, az identitás és a biztonsági feladatok kiépítése.
- Ha a csapatok nem férnek hozzá a hibrid felhőalapú erőforrásokhoz a folyamatos integráció/folyamatos fejlesztési (CI/CD) megközelítés megvalósításához.

## <a name="next-steps"></a>Következő lépések

További információ a cikkben bemutatott témakörökről:
- Az Azure DevOps és a kapcsolódó eszközökről, például az Azure Reposről és az Azure-folyamatokról az Azure [DevOps dokumentációjában](/azure/devops) tájékozódhat.
- A termékek és megoldások teljes portfóliójának megismeréséhez tekintse meg a [Azure stack termékcsaládot és megoldásokat](/azure-stack).

Ha készen áll a megoldás tesztelésére, folytassa a [DevOps Hybrid CI/CD megoldás telepítési útmutatóját](https://aka.ms/hybriddevopsdeploy). A telepítési útmutató részletes útmutatást nyújt az összetevők üzembe helyezéséhez és teszteléséhez. Megtudhatja, hogyan helyezhet üzembe egy alkalmazást az Azure-ban és Azure Stack hub-ban hibrid folyamatos integráció/folyamatos kézbesítés (CI/CD) folyamat használatával.
