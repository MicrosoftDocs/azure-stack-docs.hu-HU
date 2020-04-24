---
title: Az Azure-t és a Azure Stack Edge-t használó készletek észlelése
description: Ismerje meg, hogyan használhatja az Azure-t és a Azure Stack Edge-szolgáltatásokat a készletek észlelésének megvalósítására.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 865f63bc4234e50ed169aa29cefdb1886750594c
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "80891110"
---
# <a name="out-of-stock-detection-at-the-edge-pattern"></a>Az állományon kívüli észlelés az Edge-mintán

Ez a minta azt szemlélteti, hogyan állapítható meg, hogy a polcok kifogytak-e a Azure Stack Edge vagy Azure IoT Edge eszköz-és hálózati kamerák használatával.

## <a name="context-and-problem"></a>Kontextus és probléma

A fizikai kiskereskedelmi tárolók elvesztik az értékesítést, mert amikor az ügyfelek megkeresik az elemeket, nem jelennek meg a polcon. Előfordulhat azonban, hogy az elem a tároló hátulján volt, és nem lett újratárolva. A tárolók hatékonyabban szeretnék használni a munkatársaikat, és automatikusan értesítést kapnak, ha az elemek újrakészletezést igényelnek.

## <a name="solution"></a>Megoldás

A megoldás példája egy peremhálózati eszközt használ, például az egyes tárolókban lévő Azure Stack Edge-t, amely hatékonyan dolgozza fel az áruházban lévő kamerák adatait. Ez az optimalizált kialakítás lehetővé teszi, hogy a tárolók csak a releváns eseményeket és képeket küldjenek a felhőbe. A kialakítás csökkenti a sávszélességet, a tárterületet, és gondoskodik az ügyfelek adatainak védelméről. Mivel a rendszer minden kamerából beolvassa a képkockákat, egy ML-modell dolgozza fel a rendszerképet, és visszaadja az összes területet. Egy helyi webalkalmazásban megjelenik a rendszerkép és a készleten kívüli terület. Ezeket az információkat elküldheti egy idősorozat-betekintési környezetnek, hogy betekintést kapjon Power BI.

![Az Edge-megoldás architektúrája](media/pattern-out-of-stock-at-edge/solution-architecture.png)

A megoldás működése:

1. A lemezképek egy hálózati kamerából, HTTP vagy RTSP protokollon keresztül vannak rögzítve.
2. A rendszer átméretezi a lemezképet, és elküldi a következtetést, amely a ML-modellel kommunikálva megállapítja, hogy vannak-e a készleten kívüli rendszerképek.
3. A ML-modell a készletből származó összes területet visszaadja.
4. A következtetésre vezető illesztőprogram feltölti a nyers képet egy blobba (ha meg van adva), és elküldi az eredményeket a modellből az Azure IoT Hub és az eszközön lévő határolókeret processzorra.
5. A határolókeret processzora hozzáadja a kapcsolódó mezőket a képhez, és gyorsítótárazza a rendszerkép elérési útját egy memóriában tárolt adatbázisban.
6. A webalkalmazás lekérdezi a képeket, és a kapott sorrendben jeleníti meg azokat.
7. A IoT Hubból származó üzenetek Time Series Insightsban vannak összesítve.
8. Power BI interaktív jelentést jelenít meg a készleten kívüli elemekről a Time Series Insightsból származó adatokkal.


## <a name="components"></a>Összetevők

Ez a megoldás a következő összetevőket használja:

| Réteg | Összetevő | Leírás |
|----------|-----------|-------------|
| Helyszíni hardver | Hálózati kamera | Szükség van egy hálózati kamerára, vagy egy HTTP-vagy RTSP-hírcsatornával, hogy megismertesse a képeket a következtetésekhez. |
| Azure | Azure IoT Hub | Az [Azure IoT hub](/azure/iot-hub/) kezeli az eszközök üzembe helyezését és üzenetküldését az Edge-eszközökön. |
|  | Azure Time Series Insights | A [Azure Time Series Insights](/azure/time-series-insights/) az üzeneteket a IoT hub a vizualizációk számára tárolja. |
|  | Power BI | A [Microsoft Power bi](https://powerbi.microsoft.com/) üzleti célú jelentéseket biztosít a készleten kívüli eseményekről. A Power BI egy könnyen használható irányítópult-felületet biztosít a Azure Stream Analytics kimenetének megtekintéséhez. |
| Azure Stack Edge vagy<br>Eszköz Azure IoT Edge | Azure IoT Edge | [Azure IoT Edge](/azure/iot-edge/) összehangolja a futtatókörnyezetet a helyszíni tárolók számára, és kezeli az eszközkezelés és a frissítések kezelését.|
| | Azure Project agyhullám | Azure Stack Edge-eszközön a [Project agyhullám](https://blogs.microsoft.com/ai/build-2018-project-brainwave/) a Field-programozható Gate-tömböket (FPGA) használja a ml-következtetések felgyorsításához.|

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

A megoldás megvalósításának eldöntése során vegye figyelembe a következő szempontokat:

### <a name="scalability"></a>Méretezhetőség

A gépi tanulási modellek többsége a megadott hardvertől függően csak bizonyos számú képkockán futhat. Határozza meg a fényképezőgép (ek) optimális mintavételi sebességét, hogy a ML-folyamat ne legyen biztonsági másolat. A különböző típusú hardverek különböző számú kamerát és képkockákat fognak kezelni.

### <a name="availability"></a>Rendelkezésre állás

Fontos figyelembe venni, hogy mi történik, ha a peremhálózati eszköz elveszti a kapcsolódást. Vegye figyelembe, hogy milyen adatok elveszhetnek a Time Series Insights és Power BI irányítópulton. A példaként megadott megoldás nem kiválóan elérhető.

### <a name="manageability"></a>Kezelhetőség

Ez a megoldás számos eszközre és helyszínre képes, így nem lehet megszerezni a megoldást. Az Azure IoT szolgáltatásai automatikusan online állapotba helyezhetik az új telephelyeket és eszközöket, és naprakészen tarthatják őket. A megfelelő adatirányítási eljárásokat is követni kell.

### <a name="security"></a>Biztonság

Ez a minta a potenciálisan bizalmas adatokat kezeli. Győződjön meg arról, hogy a kulcsok rendszeresen el vannak forgatva, és az Azure Storage-fiókra és a helyi megosztásokra vonatkozó engedélyek megfelelően vannak beállítva.

## <a name="next-steps"></a>További lépések

További információ a cikkben bemutatott témakörökről:
- Ebben a mintában több IoT kapcsolódó szolgáltatás is használatos, beleértve a [Azure IoT Edge](/azure/iot-edge/), az [Azure IoT Hub](/azure/iot-hub/)és a [Azure Time Series Insights](/azure/time-series-insights/).
- Ha többet szeretne megtudni a Microsoft Project agyhullám, tekintse meg [a blog bejelentését](https://blogs.microsoft.com/ai/build-2018-project-brainwave/) , és a [Project agyhullám videójában vegye fel az Azure gyorsított Machine learning](https://www.youtube.com/watch?v=DJfMobMjCX0).
- Az ajánlott eljárásokról és a további kérdésekre adott válaszokért tekintse meg a [hibrid alkalmazások kialakításával kapcsolatos szempontokat](overview-app-design-considerations.md) .
- A termékek és megoldások teljes portfóliójának megismeréséhez tekintse meg a [Azure stack termékcsaládot és megoldásokat](/azure-stack) .

Ha készen áll a megoldás tesztelésére, folytassa az [elemzési megoldás üzembe helyezési útmutatója](https://aka.ms/edgeinferencingdeploy)című témakörben foglalt szintű adatelemzéssel. A telepítési útmutató részletes útmutatást nyújt az összetevők üzembe helyezéséhez és teszteléséhez.
