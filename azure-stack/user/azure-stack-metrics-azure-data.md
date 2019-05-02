---
title: Az Azure Monitor az Azure Stackben |} A Microsoft Docs
description: További információ az Azure Monitor az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: a4905951910a220185a8ae0651f5297c97af41f2
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64301483"
---
# <a name="azure-monitor-on-azure-stack"></a>Az Azure Monitor az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk az Azure Stack az Azure Monitor szolgáltatás áttekintése. A művelet az Azure monitor és a további információk az Azure Monitor használata az Azure Stack tárgyalja. 

Bevezetésért áttekintése, és hogyan Ismerkedés az Azure Monitor szolgáltatással, a globális Azure-cikket [Ismerkedés az Azure Monitor az Azure Stacken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

![Az Azure Stack Monitor panel](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Az Azure Monitor egy olyan platformszolgáltatás, mely egyetlen forrást kínál az Azure-erőforrások figyeléséhez. Az Azure monitorral megjelenítheti, lekérdezése, irányíthatja, archív tárolási szint, és metrikákat és naplókat az Azure-erőforrások érkező egyéb műveleteket. Ezeket az adatokat az Azure Stack felügyeleti portálon, a Monitor PowerShell-parancsmagok, többplatformos parancssori felület vagy az Azure Monitor REST API-k használatával dolgozhat. Az Azure Stack által támogatott konkrét kapcsolatot, lásd: [hogyan lehet az Azure Stack származó megfigyelési adatokat](azure-stack-metrics-monitor.md)

> [!Note]
> Metrikák és diagnosztikai naplók nem állnak rendelkezésre az Azure Stack fejlesztői készlete számára.

## <a name="prerequisites-for-azure-monitor-on-azure-stack"></a>Az Azure Monitor az Azure Stackben előfeltételei

Regisztrálja a **Microsoft.insights** az előfizetéshez tartozó ajánlat erőforrás-szolgáltatók beállításokat az erőforrás-szolgáltató. Ellenőrizheti, hogy az erőforrás-szolgáltató érhető el az előfizetéséhez tartozó ajánlat:

1. Nyissa meg az Azure Stack felügyeleti portálon.
2. Válassza ki **kínál**.
3. Válassza ki az előfizetéshez társított ajánlat.
4. Válassza ki **erőforrás-szolgáltatók** alatt **beállításait.** 
5. Keresés **Microsoft.Insights** a listában, és győződjön meg arról, hogy a állapota **regisztrált.**.

## <a name="overview-of-azure-monitor-on-azure-stack"></a>Az Azure Stack az Azure Monitor áttekintése

Azure Monitor az Azure-ban, mint például az Azure Monitor az Azure Stacken biztosít a legtöbb szolgáltatás alapszinten – infrastruktúra-metrikák és naplók.

## <a name="azure-monitor-sources-compute-subset"></a>Az Azure Monitor forrásokból: részhalmazát compute

![Az Azure Monitor az Azure Stacken forrásokból – számítási részére](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

A **Microsoft.Compute** erőforrás-szolgáltató az Azure Stackben tartalmazza:
 - Virtuális gépek 
 - Virtuális gépek méretezési csoportjai

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Alkalmazás - diagnosztikai naplók, alkalmazásnaplókat és mérőszámok

Alkalmazások futtathatók az operációs rendszer egy virtuális gép futtatja a **Microsoft.Compute** erőforrás-szolgáltató. Ezek az alkalmazások és virtuális gépek gridre bocsáthatja ki naplókat és mérőszámokat külön készlete. Az Azure Monitor az Azure diagnosztikai bővítménye (Windows vagy Linux) segítségével gyűjti a legtöbb alkalmazásszintű metrikát és naplót. 

A mértékek típusai a következők:
 - Teljesítményszámlálók
 - Alkalmazásnaplók
 - Windows-eseménynaplók
 - .NET-esemény forrása
 - IIS-naplók
 - jegyzékalapú ETW.
 - összeomlási memóriaképek,
 - Ügyfél-hibanaplók

> [!Note]  
> Az Azure Stacken Linux diagnosztikai bővítmény használata nem támogatott.

### <a name="host-and-guest-vm-metrics"></a>Gazda és vendég virtuális gépek metrikái

A fent felsorolt számítási erőforrások dedikált virtuális gazdagép és Vendég operációs rendszer van. A virtuális gazdagép és Vendég operációs rendszer felelnek meg a legfelső szintű virtuális gép és a Vendég virtuális gép a Hyper-V hipervizort. Metrikák is összegyűjtheti a virtuális gazdagép és a vendég operációs rendszer. Diagnosztikai naplók ezenkívül gyűjtheti a vendég operációs rendszer számára. A gazdagép és Vendég virtuális gép mérőszámok az Azure Stacken collectible mérőszámok listája esetén érhető el [támogatott mérőszámok az Azure Monitor szolgáltatással az Azure Stacken](azure-stack-metrics-supported.md). 

### <a name="activity-log"></a>Tevékenységnapló

A számítási erőforrásokat az Azure Stack-infrastruktúra a kapcsolatos információ a vizsgálati naplók kereshet. A napló például az erőforrások létrehozási és megszüntetési időpontjaival kapcsolatos információkat tartalmaz. A vizsgálati naplók az Azure Stacken összhangban. További információkért tekintse meg a leírását [tevékenység napló áttekintése az Azure-ban](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). 


## <a name="azure-monitor-sources-everything-else"></a>Az Azure monitor forrásokból: minden más

![Az Azure Monitor az Azure Stack-források – minden más](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>Erőforrás - metrikák és diagnosztikai naplók

Az erőforrástípus collectible metrikák és diagnosztikai naplókat függ. Az egyes erőforrások az Azure Stacken collectible metrikáinak listája látható, a támogatott mérőszámok érhető el. További információkért lásd: [támogatott mérőszámok az Azure Monitor szolgáltatással az Azure Stacken](azure-stack-metrics-supported.md).

### <a name="activity-log"></a>Tevékenységnapló

A tevékenységnapló azonos, a számítási erőforrásokat. 

### <a name="uses-for-monitoring-data"></a>Monitorozási adatok felhasználása

**Store és az archív**  

Egyes monitorozási adatok már eleve tárolva vannak és elérhetők az Azure Monitor szolgáltatásban egy megadott ideig. 
 - A metrikák 90 napon keresztül érhetők el. 
 - A tevékenységnapló-bejegyzések 90 napon keresztül érhetők el. 
 - Diagnosztikai naplók nem tárolja.
 - Az adatok hosszabb adatmegőrzés megadásához a tárfiókba archiválhatja.

**Lekérdezés**  

Az Azure Monitor REST API, a többplatformos parancssori felület (CLI) parancsai, a PowerShell-parancsmagok vagy a .NET SDK használatával az adatok eléréséhez a rendszer vagy az Azure storage. 

**Vizualizáció**

A monitorozási adatok ábrákon és diagramokon való megjelenítésével a trendek könnyebben megfigyelhetőek, mint maguknak az adatoknak a vizsgálatával. 

Néhány vizualizációs módszer:
 - Az Azure Stack felhasználói és rendszergazdai portál használata
 - Útvonal-adatok a Microsoft Power bi-bA
 - Az adatok átirányítása egy külső vizualizációs eszközbe élő stream használatával vagy egy, az Azure-tárolóban lévő archívum az eszköz általi beolvasásával

## <a name="methods-of-accessing-azure-monitor-on-azure-stack"></a>Az Azure Stacken figyelése módszerekkel férnek hozzá az Azure

Általában az adatokat azok követéséhez, átirányításához és lekéréséhez a következő módszerekkel kezelheti. Nem mindegyik módszer érhető el mindegyik művelet és adattípus esetében.

 - [Azure Stack Portal](azure-stack-use-portal.md)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [Platformfüggetlen parancssori Interface(CLI)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [REST API](https://docs.microsoft.com/rest/api/monitor)
 - [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>További lépések

További információ a beállításokat a figyelési adatok fogyasztás az Azure Stacken cikkben [monitorozási adatai az Azure Stack felhasználás](azure-stack-metrics-monitor.md).
