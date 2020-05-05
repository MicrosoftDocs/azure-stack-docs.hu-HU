---
title: Azure Monitor használata Azure Stack hub-on
description: Ismerje meg, hogyan használhatja a Azure Monitort Azure Stack hub-on.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 12/01/2019
ms.openlocfilehash: 0676354bb902ccafed49281c12296440a8aa8c5e
ms.sourcegitcommit: 278aaeca069213a98b90751253f6b15423634849
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82742446"
---
# <a name="use-azure-monitor-on-azure-stack-hub"></a>Azure Monitor használata Azure Stack hub-on

Ez a cikk az Azure Stack hub Azure Monitor szolgáltatásának áttekintését tartalmazza. Ismerteti Azure Monitor működését, valamint a Azure Monitor Azure Stack hub-beli használatáról szóló további információkat.

A Azure Monitor áttekintését lásd: a globális Azure-cikk első [lépései Azure monitor on Azure stack hub](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

![Azure Stack hub monitor panel](./media/azure-stack-metrics-azure-data/azs-monitor.png)

A Azure Monitor egy olyan platform-szolgáltatás, amely egyetlen forrást biztosít az Azure-erőforrások monitorozásához. Azure Monitor lehetővé teszi az Azure-ban lévő erőforrásokból származó mérőszámokkal és naplókkal kapcsolatos egyéb műveletek megjelenítését, lekérdezését, továbbítását, archiválását és elvégzését. Ezekkel az adatokkal a Azure Stack hub felügyeleti portálján, a PowerShell-parancsmagok, a platformfüggetlen CLI vagy a Azure Monitor REST API-k használatával is dolgozhat. Az Azure Stack hub által támogatott adott kapcsolathoz lásd: [figyelési adatok felhasználása Azure stack hub-ból](azure-stack-metrics-monitor.md).

> [!Note]
> A metrikák és diagnosztikai naplók nem érhetők el a Azure Stack Development Kit számára.

## <a name="prerequisites-for-azure-monitor-on-azure-stack-hub"></a>Az Azure Stack hub Azure Monitor előfeltételei

Regisztrálja a **Microsoft. reinsights** erőforrás-szolgáltatót az előfizetéshez tartozó ajánlat erőforrás-szolgáltatói beállításainál. Ellenőrizze, hogy az erőforrás-szolgáltató elérhető-e az előfizetéséhez tartozó ajánlatban:

1. Nyissa meg az Azure Stack hub felhasználói portált.
2. Válassza az **Előfizetések** lehetőséget.
3. Válassza ki a regisztrálni kívánt előfizetést.
4. Válassza az **erőforrás-szolgáltatók** lehetőséget a beállítások területen **.** 
5. Keresse meg a **Microsoft. bepillantást** a listából, és ellenőrizze, hogy az állapot **regisztrálva**van-e.

## <a name="overview-of-azure-monitor-on-azure-stack-hub"></a>Az Azure Stack hub Azure Monitor áttekintése

Az Azure-ban az Azure Monitorhoz hasonlóan a Azure Monitor a Azure Stack hub alapszintű infrastruktúra-mérőszámokat és naplókat biztosít a legtöbb szolgáltatáshoz.

## <a name="azure-monitor-sources-compute-subset"></a>Azure Monitor források: számítási részhalmaz

![Azure Monitor Azure Stack hub-forrásokon – számítási részhalmaz](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.svg)

A Azure Stack hub **Microsoft. számítási** erőforrás-szolgáltatója a következőket tartalmazza:
 - Virtual machines (Virtuális gépek) 
 - Virtuálisgép-méretezési csoportok

### <a name="application---diagnostics-logs-app-logs-and-metrics"></a>Alkalmazás-diagnosztikai naplók, alkalmazás-naplók és metrikák

Az alkalmazások a **Microsoft. számítási** erőforrás-szolgáltató használatával FUTTATott virtuális gépek operációs rendszerében futhatnak. Ezek az alkalmazások és virtuális gépek saját naplókat és mérőszámokat bocsátanak ki. A Azure Monitor az Azure Diagnostics bővítmény (Windows vagy Linux) használatával gyűjti össze a legtöbb alkalmazás-szintű mérőszámot és naplót.

A mértékek típusai a következők:
 - Teljesítményszámlálók
 - Alkalmazás-naplók
 - Windows-eseménynaplók
 - .NET-esemény forrása
 - IIS-naplók
 - jegyzékalapú ETW.
 - összeomlási memóriaképek,
 - Felhasználói hibák naplói

> [!Note]  
> Azure Stack hub Linux Diagnostics bővítménye nem támogatott.

### <a name="host-and-guest-vm-metrics"></a>Gazda és vendég virtuális gépek metrikái

A korábban felsorolt számítási erőforrások dedikált gazda virtuális géppel és vendég operációs rendszerrel rendelkeznek. A gazdagép virtuális gépe és a vendég operációs rendszer a Hyper-V hypervisorban található legfelső szintű virtuális gép és vendég virtuális gép megfelelője. A virtuális gép és a vendég operációs rendszer mérőszámait is összegyűjtheti. A vendég operációs rendszer diagnosztikai naplóit is gyűjtheti. A gazdagépek és a vendég virtuálisgép-metrikák Azure Stack hub-on való begyűjtői metrikáinak listája az [Azure stack hub Azure monitor által támogatott mérőszámokban](azure-stack-metrics-supported.md)érhető el. 

### <a name="activity-log"></a>Tevékenységnapló

A Azure Stack hub-infrastruktúra által látott számítási erőforrásokra vonatkozó információkat a tevékenység naplóiban keresheti meg. A napló például az erőforrások létrehozási és megszüntetési időpontjaival kapcsolatos információkat tartalmaz. Az Azure Stack hub tevékenység-naplói konzisztensek az Azure-ban. További információ: a [műveletnapló áttekintése az Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)-ban. 


## <a name="azure-monitor-sources-everything-else"></a>Azure monitor-források: minden más

![Azure Monitor Azure Stack hub-forrásokon – minden más](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.svg)

### <a name="resources---metrics-and-diagnostics-logs"></a>Erőforrások – mérőszámok és diagnosztikai naplók

A gyűjthető metrikák és diagnosztikai naplók az erőforrástípus alapján változnak. Az Azure Stack hub egyes erőforrásaihoz tartozó gyűjthető metrikák listája támogatott mérőszámokon érhető el. További információ: [támogatott mérőszámok Azure Monitor Azure stack hub-on](azure-stack-metrics-supported.md).

### <a name="activity-log"></a>Tevékenységnapló

A tevékenység naplója ugyanaz, mint a számítási erőforrások esetében. 

### <a name="uses-for-monitoring-data"></a>Az adatfigyelési szolgáltatás használata

**Tárolás és archiválás**  

Egyes monitorozási adatok már eleve tárolva vannak és elérhetők az Azure Monitor szolgáltatásban egy megadott ideig. 
 - A metrikák 90 napon keresztül érhetők el. 
 - A tevékenységnapló-bejegyzések 90 napon keresztül érhetők el. 
 - A diagnosztikai naplók nem tárolódnak.
 - Az adatok archiválása egy Storage-fiókba a hosszú megőrzés érdekében.

**Lekérdezés**  

Használhatja a Azure Monitor REST API, platformfüggetlen parancssori felületi (CLI) parancsokat, a PowerShell-parancsmagokat vagy a .NET SDK-t a rendszer vagy az Azure Storage szolgáltatásban tárolt adateléréshez. 

**Vizualizáció**

A monitorozási adatok ábrákon és diagramokon való megjelenítésével a trendek könnyebben megfigyelhetőek, mint maguknak az adatoknak a vizsgálatával. 

Néhány vizualizációs módszer:
 - Használja az Azure Stack hub felhasználói és felügyeleti portált.
 - Továbbítsa az adattovábbítást a Microsoft Power BIba.
 - Az adatok továbbítása harmadik féltől származó vizualizációs eszközre élő közvetítés vagy az eszköz egy Azure Storage-beli archívumból való beolvasása használatával.

## <a name="methods-of-accessing-azure-monitor-on-azure-stack-hub"></a>Az Azure monitor Azure Stack hub szolgáltatáshoz való hozzáférésének módszerei

Az alábbi módszerek egyikével dolgozhat az adatkövetéssel, az útválasztással és a lekéréssel. Nem mindegyik módszer érhető el mindegyik művelet és adattípus esetében. 

 - [Azure Stack hub felhasználói portál](azure-stack-use-portal.md)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [Platformfüggetlen parancssori felület (CLI)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [REST API](https://docs.microsoft.com/rest/api/monitor)
 - [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

> [!Important]  
> Ha egy virtuális gép teljesítményének megtekintésekor **nem talál** hibát, akkor ellenőrizze, hogy regisztrálta-e a Microsoft. bepillantást a virtuális géphez társított előfizetésre.

## <a name="next-steps"></a>További lépések

További információ a Azure Stack hub adatfelhasználásának figyeléséről a [Azure stack hub megfigyelési adatainak felhasználása](azure-stack-metrics-monitor.md)című cikkben olvasható.
