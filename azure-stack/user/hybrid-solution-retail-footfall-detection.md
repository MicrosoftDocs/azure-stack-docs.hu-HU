---
title: Hibrid megoldás mesterséges intelligencia-alapú lépés hangja-észlelési minta megvalósításához az Azure és a Azure Stack használatával
description: Ismerje meg, hogyan használhatja az Azure-t és a Azure Stack szolgáltatásokat a kereskedelmi tárolók forgalmának elemzésére szolgáló AI-alapú lépés hangja-észlelési minta megvalósításához.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: 32533b063d4c42b740825923e9e2b5692ec03728
ms.sourcegitcommit: bbf3edbfc07603d2c23de44240933c07976ea550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71714632"
---
# <a name="footfall-detection-solution"></a>Lépés hangja-észlelési megoldás

Ez a cikk áttekintést nyújt egy mesterséges intelligencia-alapú lépés hangja-észlelési minta megvalósítására szolgáló megoldásról. Ez a minta hasznos a kiskereskedelmi tárolók látogatói forgalmának elemzéséhez. A megoldás az Azure-ban, a Azure Stackon és a Custom Vision AI fejlesztői csomagon keresztül bepillantást ad a valós világbeli műveletekre.

## <a name="context-and-problem"></a>Kontextus és probléma

A contoso-áruházak elemzéseket szeretnének kapni arról, hogy az ügyfelek Hogyan kapják meg aktuális termékeiket az áruház elrendezésével kapcsolatban. Nem tudnak a személyzetet minden egyes szakaszba helyezni, és nem hatékony, hogy az elemzők csapata egy teljes áruházból származó kamerás felvételeket tekintse át. Emellett a tárolók egyike sem rendelkezik elegendő sávszélességgel, hogy az összes kameráról a felhőbe továbbítsa a videót elemzés céljából. 

A contoso szeretné megkeresni az ügyfelek demográfiai, lojalitási és adatkezelési lehetőségeit a kijelzők és termékek tárolására.

## <a name="solution"></a>Megoldás

Ez a kiskereskedelmi elemzési megoldás egy lépcsőzetes megközelítést használ a szélén való következtetéshez. A Custom Vision AI dev kit használatával az elemzést csak az emberi arcokat tartalmazó képek küldik el az Azure Cognitive Servicest futtató privát Azure Stack. Anonim, összesített adatokat küld az Azure-ba az összes áruházban és vizualizációban Power BI. A peremhálózat és a nyilvános felhő együttes használatával a contoso kihasználhatja a modern AI-technológiák előnyeit. Ugyanakkor továbbra is megfelel a vállalati szabályzatoknak, és tiszteletben tartja ügyfelei magánéletét.

[![Lépés hangja észlelési minta](media/hybrid-pattern-ai-footfall-detection/solution-architecture.png)](media/hybrid-pattern-ai-footfall-detection/solution-architecture.png)

Íme egy összefoglaló a megoldás működéséről: 

1. A Custom Vision AI dev Kit IoT Hubtól származó konfigurációt kap, amely telepíti a IoT Edge futtatókörnyezetet és egy ML modellt.
2. Ha a modell egy személyt lát, egy képet helyez el, és feltölti Azure Stack blob Storage-ba. 
3. A blob szolgáltatás elindít egy Azure-függvényt Azure Stackon. 
4. Az Azure-függvény egy tárolót hív meg a Face API, hogy demográfiai és érzelem-adatokhoz kapjon képet.
5. Az adattárolást és az Azure Event hub-ba küldi a rendszer.
6. Az Event hub leküldi az Stream Analyticsba az adatközpontot.
7. Stream Analytics összesíti az adatokat, és leküldi Power BIre.

## <a name="components"></a>Összetevők

Ez a megoldás a következő összetevőket használja:

| Réteg | Összetevő | Leírás |
|----------|-----------|-------------|
| Tárolt hardver | [Custom Vision AI fejlesztői csomag](https://azure.github.io/Vision-AI-DevKit-Pages/) | Egy helyi ML-modell használatával biztosítja az áruházbeli szűrést, amely csak az elemzésre alkalmas személyek képét rögzíti. Biztonságos kiépítés és frissítés IoT Hubon keresztül.<br><br>|
| Azure Stack | [APP SERVICE](../operator/azure-stack-app-service-overview.md) | A App Service erőforrás-szolgáltató (RP) az Edge-összetevők alapját biztosítja. Beleértve a Web Apps/API-k és a függvények üzemeltetési és felügyeleti funkcióit. |
| | Azure Kubernetes szolgáltatás [(ak) motorjának](https://github.com/Azure/aks-engine) fürtje | A Azure Stack-ben üzembe helyezett, AK-t tartalmazó AK RP-vel rendelkező, méretezhető, rugalmas motort biztosít az Face API tároló futtatásához. |
| | Azure Cognitive Services [Face API tárolók](/azure/cognitive-services/face/face-how-to-install-containers)| Az Azure Cognitive Services RP Face API tárolókkal biztosítja a demográfiai, érzelem-és egyedi látogatói észlelést a contoso magánhálózaton. |
| | Blobtároló | A AI fejlesztői csomagból rögzített rendszerképek fel lesznek töltve a Azure Stack blob Storage-tárolóba. |
| | Azure Functions | Azure Stack rendszerű Azure-függvény fogadja a blob Storage-ból érkező adatokat, és kezeli az interakciókat a Face API. A rendszer az Azure-ban található egyik Event hub-ba névtelenül küldi el az adattárolást.<br><br>|
| Azure |  |  |
|  | [Azure Event Hubs](/azure/event-hubs/) | Az Azure Event Hubs méretezhető platformot biztosít a Azure Stream Analyticshoz szépen integrálható, névtelenül tárolt adatmennyiségek betöltéséhez. |
|  | [Azure Stream Analytics](/azure/stream-analytics/) | Az Azure Stream Analytics-feladatok összesítik a névtelen adatokat, és a vizualizációk 15 másodperces Windowsba csoportosítják azokat. |
|  | [Microsoft Power BI](https://powerbi.microsoft.com/) | A Power BI egy könnyen használható irányítópult-felületet biztosít a Azure Stream Analytics kimenetének megtekintéséhez. |

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

A minta megvalósítása során az alábbi pontokat vegye figyelembe:

### <a name="scalability"></a>Méretezhetőség 

Ha engedélyezni szeretné, hogy a megoldás több kamera és hely között is méretezhető legyen, meg kell győződnie arról, hogy az összes összetevő képes kezelni a megnövekedett terhelést. Előfordulhat, hogy a következő műveleteket kell végrehajtania:

- Növelje Stream Analytics folyamatos átviteli egységek számát
- A Face API központi telepítésének felskálázása
- A Event Hubs átviteli sebesség növelése
- Szélsőséges esetekben szükség lehet a Azure Functionsról a virtuális gépre való Migrálás.

### <a name="availability"></a>Rendelkezésre állás

Mivel ez a megoldás többrétegű, fontos, hogy meggondolja, hogyan kell kezelni a hálózati vagy áramkimaradásokat. Az üzleti igényektől függően célszerű lehet egy olyan mechanizmust megvalósítani, amely helyileg gyorsítótárazza a lemezképeket, majd a kapcsolat visszaadásakor Azure Stack továbbítja őket. Ha a hely elég nagy, akkor lehet, hogy egy Data Box Edge üzembe helyezése a Face API tárolóval az adott helyen jobb megoldás lehet.

### <a name="manageability"></a>Kezelhetőség

Ez a megoldás számos eszközre és helyszínre képes, így nem lehet megszerezni a megoldást. Az [Azure IoT Services](/azure/iot-fundamentals/) használatával automatikusan online állapotba helyezhetők az új helyszínek és eszközök, és naprakészen tarthatók. 

### <a name="security"></a>Biztonság

Ez a megoldás rögzíti a vásárlói rendszerképeket, így a biztonság a legfontosabb szempont. Győződjön meg arról, hogy az összes Storage-fiók biztonságos a megfelelő hozzáférési házirendekkel, és hogy a kulcsok elforgatása rendszeresen megtörténik. Győződjön meg arról, hogy a Storage-fiókok és a Event Hubs rendelkeznek a vállalati és kormányzati adatvédelmi szabályozásoknak megfelelő adatmegőrzési szabályzatokkal Győződjön meg arról is, hogy a réteg hozzáférési szintjei vannak, így minden olyan személynek, akinek hozzáférésre van szüksége a rendszerhez, csak a szerepkörük számára szükséges adathoz férhet hozzá.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni a megoldás által használt kapcsolódó mintázatokról, tekintse meg a [Lépcsőzetes adatminta](azure-stack-solution-staged-data.md)című témakört. 
- Ha többet szeretne megtudni az egyéni jövőkép használatáról, tekintse meg a [Custom Vision AI fejlesztői készletet](https://azure.github.io/Vision-AI-DevKit-Pages/). 
- Töltse le és telepítse a [lépés hangja-megoldás mintájának megvalósítását](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/footfall-analysis). Megtudhatja, hogyan hozhatja ki a valós tevékenységekből származó információkat az Azure, a Azure Stack és a Custom Vision AI dev kit használatával.   
