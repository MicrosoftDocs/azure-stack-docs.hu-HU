---
title: Azure Monitor használata a Azure Stackon | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Azure Monitort a Azure Stack.
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
ms.date: 07/23/2019
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: aafdc25293ea3cc584d24688d071dadb2151ce22
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418542"
---
# <a name="use-azure-monitor-on-azure-stack"></a>Azure Monitor használata Azure Stack

*Vonatkozik: Integrált rendszerek Azure Stack*

Ez a cikk a Azure Stack Azure Monitor szolgáltatásának áttekintését tartalmazza. Ismerteti Azure Monitor működését, valamint a Azure Monitor a Azure Stack használatával kapcsolatos további információkat. 

A Azure Monitor áttekintését lásd: a globális Azure-cikk első [lépései Azure monitor on Azure stack](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

![Azure Stack monitor panel](./media/azure-stack-metrics-azure-data/azs-monitor.png)

A Azure Monitor egy olyan platform-szolgáltatás, amely egyetlen forrást biztosít az Azure-erőforrások monitorozásához. Azure Monitor lehetővé teszi az Azure-ban lévő erőforrásokból származó mérőszámokkal és naplókkal kapcsolatos egyéb műveletek megjelenítését, lekérdezését, továbbítását, archiválását és elvégzését. Ezekkel az adatokkal a Azure Stack felügyeleti portálon, a PowerShell-parancsmagok, a platformfüggetlen CLI vagy a Azure Monitor REST API-k használatával is dolgozhat. A Azure Stack által támogatott adott kapcsolathoz tekintse meg a [figyelési adatok felhasználása Azure stackből](azure-stack-metrics-monitor.md)című témakört.

> [!Note]
> A metrikák és diagnosztikai naplók nem érhetők el a Azure Stack Development Kit számára.

## <a name="prerequisites-for-azure-monitor-on-azure-stack"></a>A Azure Stack Azure Monitor előfeltételei

Regisztrálja a **Microsoft.** reinsights erőforrás-szolgáltatót az előfizetéshez tartozó ajánlat erőforrás-szolgáltatói beállításainál. Ellenőrizze, hogy az erőforrás-szolgáltató elérhető-e az előfizetéséhez tartozó ajánlatban:

1. Nyissa meg a Azure Stack felügyeleti portált.
2. Válasszon **ajánlatokat**.
3. Válassza ki az előfizetéshez társított ajánlatot.
4. Válassza az **erőforrás-szolgáltatók** lehetőséget a beállítások területen **.** 
5. Keresse meg a **Microsoft. bepillantást** a listából, és ellenőrizze, hogy az állapot regisztrálva van-e.

## <a name="overview-of-azure-monitor-on-azure-stack"></a>A Azure Stack Azure Monitor áttekintése

Azure Monitor az Azure-ban, mint például az Azure Monitor az Azure Stacken biztosít a legtöbb szolgáltatás alapszinten – infrastruktúra-metrikák és naplók.

## <a name="azure-monitor-sources-compute-subset"></a>Azure Monitor források: számítási részhalmaz

![Azure Monitor Azure Stack forrásokon – számítási részhalmaz](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

A (z **) Azure stack Microsoft. számítási** erőforrás-szolgáltatója a következőket tartalmazza:
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
 - Összeomlási memóriaképek
 - Felhasználói hibák naplói

> [!Note]  
> A Linux diagnosztikai bővítmény Azure Stackon nem támogatott.

### <a name="host-and-guest-vm-metrics"></a>Gazda és vendég virtuális gépek metrikái

A korábban felsorolt számítási erőforrások dedikált gazda virtuális géppel és vendég operációs rendszerrel rendelkeznek. A gazdagép virtuális gépe és a vendég operációs rendszer a Hyper-V hypervisorban található legfelső szintű virtuális gép és vendég virtuális gép megfelelője. A virtuális gép és a vendég operációs rendszer mérőszámait is összegyűjtheti. A vendég operációs rendszer diagnosztikai naplóit is gyűjtheti. A gazdagépek és a vendég virtuális gépek metrikáinak Azure Stack-ra vonatkozó gyűjthető metrikáinak listája a [Azure monitor on Azure stack címen támogatott mérőszámokban](azure-stack-metrics-supported.md)érhető el. 

### <a name="activity-log"></a>Tevékenységnapló

A Azure Stack infrastruktúra által látott számítási erőforrásokra vonatkozó információkat a tevékenység naplóiban keresheti meg. A napló például az erőforrások létrehozási és megszüntetési időpontjaival kapcsolatos információkat tartalmaz. A Azure Stack a tevékenység naplói konzisztensek az Azure-ban. További információ: a [műveletnapló áttekintése az Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)-ban. 


## <a name="azure-monitor-sources-everything-else"></a>Azure monitor-források: minden más

![Azure Stack-források Azure Monitor – minden más](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>Erőforrások – mérőszámok és diagnosztikai naplók

A gyűjthető metrikák és diagnosztikai naplók az erőforrástípus alapján változnak. A Azure Stackon lévő erőforrások gyűjthető metrikáinak listája támogatott mérőszámokon érhető el. További információ: [támogatott mérőszámok Azure monitor on Azure stack](azure-stack-metrics-supported.md).

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
 - Használja a Azure Stack felhasználói és felügyeleti portált.
 - Továbbítsa az adattovábbítást a Microsoft Power BIba.
 - Az adatok továbbítása harmadik féltől származó vizualizációs eszközre élő közvetítés vagy az eszköz egy Azure Storage-beli archívumból való beolvasása használatával.

## <a name="methods-of-accessing-azure-monitor-on-azure-stack"></a>Az Azure monitor Azure Stackhoz való hozzáférésének módszerei

Általában az adatokat azok követéséhez, átirányításához és lekéréséhez a következő módszerekkel kezelheti. Nem mindegyik módszer érhető el mindegyik művelet és adattípus esetében.

 - [Azure Stack portál](azure-stack-use-portal.md)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [Platformfüggetlen parancssori felület (CLI)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [REST API](https://docs.microsoft.com/rest/api/monitor)
 - [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>További lépések

További információ az adatfelhasználás figyeléséről a Azure Stack cikkben, amely a [Azure stack figyelési adatait használja](azure-stack-metrics-monitor.md)fel.
