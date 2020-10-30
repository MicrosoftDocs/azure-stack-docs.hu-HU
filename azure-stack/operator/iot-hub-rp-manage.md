---
title: IoT Hub kezelése Azure Stack hub-on
description: IoT Hub felügyeleti élmény és riasztások
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: ca410a3ec5b66a3ab3243ae06f82f4cb736af836
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93049994"
---
# <a name="how-to-manage-iot-hub-on-azure-stack-hub"></a>IoT Hub kezelése Azure Stack hub-on

A IoT Hub felügyeleti felület lehetővé teszi a teljes állapot, riasztások és kapacitás megjelenítését és kezelését.

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

## <a name="iot-hub-management-dashboard"></a>IoT Hub felügyeleti irányítópult

A IoT Hub felügyeleti irányítópultjának elérése:

1. Jelentkezzen be az Azure Stack hub felügyeleti portálra, válassza ki az **irányítópult** nézetet a bal oldalon, majd válassza ki a **IoT hub** erőforrás-szolgáltatót:

   [![operátor irányítópult](media\iot-hub-rp-manage\dashboard.png)](media\iot-hub-rp-manage-capacity\dashboard.png#lightbox)

2. Az IoT Hub irányítópulton egy összegző nézet látható, amely megjeleníti az aktuális riasztásokat, a bélyegzőn létrehozott kvótákat, valamint az előfizetésben IoT Hub fürtök teljes számát: 

   [![IOT hub irányítópult – áttekintés](media\iot-hub-rp-manage\dashboard-rp-iot-hub-overview.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-overview.png#lightbox)

## <a name="alerts"></a>Riasztások

A IoT Hub erőforrás-szolgáltató a következő riasztásokat támogatja:

|Kategória|Riasztás|Típus|Leírás|
|-|-|-|-|
|Teljesítmény|A CPU-használat IoT Hub figyelmet igényel.|Figyelmeztetés|Az IoT Hub erőforrás-szolgáltató átlagos CPU-használata az elmúlt 6 órában több mint 75%.|
|Teljesítmény|IoT Hub memóriahasználat figyelmet igényel.|Figyelmeztetés|IoT Hub erőforrás-szolgáltató fennmaradó memória-használata az elmúlt 6 órában kevesebb, mint 1024 MB.|
|Teljesítmény|Kevés a lemezterület a IoT Hub erőforrás-szolgáltató számára.|Figyelmeztetés|A fennmaradó lemezterület kevesebb, mint 10%.|
|Erőforrás|Nem sikerült létrehozni vagy frissíteni a IoT Hub erőforrást.|Figyelmeztetés|IoT Hub erőforrás-szolgáltató létrehozása vagy frissítése IoT Hub a hibák száma 15 percnél nem kevesebb, mint 1.|
|Szolgáltatás|IoT Hub erőforrás-szolgáltató naplójának hibáira figyelmet igényel.|Figyelmeztetés|IoT Hub erőforrás-szolgáltatói napló meghibásodásának száma a szerepkör-példányon belül több mint 3, 15 perc.|
|Szolgáltatás|IoTHub-SU-InternalError|Figyelmeztetés|A IoTHub SU belső hibája és az időtúllépések száma nem lehet kisebb, mint 5 az elmúlt 30 percben.|
|Szolgáltatás|IoT Hub adatsík biztonsági mentési hiba történt.|Figyelmeztetés|IoT Hub adatsíkon az elmúlt 15 percben biztonsági mentési hibák történtek. Az eszközbeállítások nem lesznek védve.|
|Szolgáltatás|IoT Hub adatsík egymást követő biztonsági mentési hiba történt.|Figyelmeztetés|IoT Hub adatsíkon egymást követő biztonsági mentési hibák történtek az elmúlt 15 percben. Az eszközbeállítások nem lesznek védve.|
|Szolgáltatás|IoT Hub adatsíkon nem sikerült biztonsági mentést készíteni.|Figyelmeztetés|IoT Hub nem rendelkezik sikeres biztonsági mentéssel az elmúlt 3 órában. Előfordulhat, hogy az eszköz adatai nem védettek.|
|Szolgáltatás|IoT Hub adatsík visszaállítása sikertelen volt.|Figyelmeztetés|Nem sikerült a IoT Hub eszköz adatainak visszaállítása. A IotHubPerformanceMetrics meghaladja az elmúlt 15 perc küszöbértékét.|
|Szolgáltatás|IoT Hub átjáró meghibásodása történt.|Figyelmeztetés|IoT Hub átjáró meghibásodása történt. Lehetséges, hogy az eszköz telemetria funkciója hatással lehet.|
|Szolgáltatás|IoT Hub tároló meghibásodása történt.|Figyelmeztetés|IoT Hub tároló meghibásodása történt. Az eszköz hitelesítése sikertelen lehet. |
|Szolgáltatás|IoT Hub eszközkezelés tárolójának meghibásodása történt.|Figyelmeztetés|IoT Hub eszközkezelés tárolójának meghibásodása történt. Az eszköz Twin, a közvetlen módszer funkciói csökkenhetnek.|

Riasztások és kvóták figyelése:

1. Válassza a **riasztások** lehetőséget a riasztási előzmények megtekintéséhez: 
 
   [![IOT hub irányítópult – riasztások](media\iot-hub-rp-manage\dashboard-rp-iot-hub-alerts.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-alerts.png#lightbox)  

2. Válassza ki a **kvótákat** a hatályos kvóták listájának megtekintéséhez:  

   > [!NOTE]
   > Az előzetes verzióban a **Létrehozás** funkció le van tiltva, és korlátlan számú alapértelmezett kvóta van megadva. A **Létrehozás** engedélyezve lesz a ga számára.

   [![IOT hub irányítópult – kvóták](media\iot-hub-rp-manage\dashboard-rp-iot-hub-quotas.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-quotas.png#lightbox) 

## <a name="capacity-management"></a>Kapacitáskezelés

Kezelőként ugyanúgy kezelheti és üzemeltetheti Azure Stack hub-példányát, mint bármelyik Felhőbeli operátort. Győződjön meg arról, hogy a szoftver megfelelően van telepítve, megfelelően van konfigurálva, és magas rendelkezésre állású (HA), következetesen és hatékonyan működik. A kapacitás-kezelési alapelveket a Azure Stack hub-példány és a IoT Hub erőforrás-szolgáltató üzemeltetéséhez kell alkalmaznia.

### <a name="azure-stack-hub-capacity-management"></a>Azure Stack hub kapacitásának kezelése

A IoT Hub szükséges kapacitásának meghatározásához meg kell becsülnie a munkaterhelést, főként az eszközök számát és az üzenet átviteli sebességét. A tervezéshez való segítségnyújtáshoz a következő teszteket hajtottuk végre egy 4 csomópontos Azure Stack hub-környezettel való használatra:

| Használati eset | Virtuális gépek | Virtuális mag | Eszközök/központ | [Hub-kiadás](https://azure.microsoft.com/pricing/details/iot-hub) | Hubs | Egység/hub | Összes eszköz | Összes hub-egység | Több millió üzenet/nap |
|----------|---------------|------------------|-----------------------|-------------------|-|-|-|-|-|
|Alapértelmezett|5|20|300 000|S2|4|200|1 200 000|800|4800|
|12 virtuális gép|12|48|500 000|S2|4|200|2 000 000|800|4800|
|18 virtuális gép|18|72|400,000|S3|4|10|1 600 000|40|12 000|

További részletekért tekintse meg [Azure stack Hub Capacity Planner](azure-stack-capacity-planner.md) .

### <a name="iot-hub-capacity-management"></a>IoT Hub kapacitások kezelése

Mivel Azure Stack hub korlátozott erőforrásokkal rendelkező helyszíni adatközpontban van üzembe helyezve, a Azure Stack hub-megosztáson futó összes szolgáltatás és ugyanazon erőforráskészlet verseng. A kezelőknek az üzleti igények alapján kell megtervezniük és kezelniük a kapacitást. A IoT Hub erőforrás-szolgáltató lehetővé teszi a kezelők számára a szolgáltatás kapacitási követelményeinek kezelését.

IoT Hub egyetlen virtuálisgép-típussal rendelkezik. IoT Hub üzembe helyezésének részeként a rendszer a virtuális gépek készletét Azure Stack hub-on. A virtuális gépek bizonyos számú eszközt és üzenetküldési sebességet is támogatnak. Az alapértelmezett beállításnak meg kell felelnie a legtöbb követelménynek. Ha azonban több eszközre vagy magasabb szintű üzenetre van szüksége, növelheti a kapacitást a felügyeleti portál, a CLI vagy a PowerShell használatával. 

A kapacitás beállításainak figyelése és módosítása:

1. Válassza ki a **kapacitás** nézetet a bal oldalon. Megtekintheti a kapacitás állapotát, beleértve a IoT Hub-fürtben kiépített virtuális gépek számát és erőforrás-kihasználtságát. A megjelenített csomópontok száma a IoT Hub számára lefoglalt csomópontok aktuális száma. 

2. A kapacitás növeléséhez válassza ki a IoT Hub fürt nevét, módosítsa a csomópontok számát, majd válassza a **skála frissítése** lehetőséget. Növelheti a kapacitást úgy, hogy annyi virtuális gépet ad hozzá, amennyit a rendelkezésre álló erőforrások engedélyeznek.

   > [!IMPORTANT]
   > Az előzetes verzióban csak IoT Hub fürt kibővíthető (kisebb és nagyobb). A méretezés (nagyobb és kisebb) a IoT Hub általánosan elérhető (GA) verziójában támogatott.

   [![IOT hub irányítópult – kapacitás](media\iot-hub-rp-manage\dashboard-rp-iot-hub-capacity.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-capacity.png#lightbox)


## <a name="next-steps"></a>További lépések

További információ a következőről:

Azure Stack hub figyelési képességei, beleértve a riasztásokat, lásd: [állapotok és riasztások figyelése](azure-stack-monitor-health.md).

Azure Stack hub-naplófájlok gyűjteménye: [Azure stack diagnosztikai naplók áttekintése](azure-stack-diagnostic-log-collection-overview.md).

