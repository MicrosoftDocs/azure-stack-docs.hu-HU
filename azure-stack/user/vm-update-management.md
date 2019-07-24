---
title: VIRTUÁLIS gépek frissítése és kezelése a Azure Stackkal | Microsoft Docs
description: Megtudhatja, hogyan használhatók a Azure Monitor for VMs, a Update Management, a Change Tracking és a leltározási megoldások a Azure Automationekben a Azure Stack-ban üzembe helyezett Windows-és Linux-alapú virtuális gépek kezeléséhez.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: 1e43f5df97ff91e7117759027499ffa6cd0df7c8
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418254"
---
# <a name="azure-stack-vm-update-and-management"></a>Azure Stack a virtuális gép frissítését és felügyeletét
A következő Azure Automation megoldási funkciókkal kezelheti a Azure Stack használatával üzembe helyezett Windows-és Linux-alapú virtuális gépeket:

- **[Update Management](https://docs.microsoft.com/azure/automation/automation-update-management)** . A Update Management megoldással gyorsan felbecsülheti az összes ügynökön elérhető frissítések állapotát, és kezelheti a szükséges frissítések telepítését ezen Windows-és Linux-alapú virtuális gépekhez.

- **[Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking)** . A felügyelt kiszolgálókon a telepített szoftverek, a Windows-szolgáltatások, a Windows-beállításjegyzék és a fájlok, valamint a Linux-démonok módosításai a felhőben a Azure Monitor szolgáltatásba kerülnek feldolgozásra. A fogadott adatokat logikát alkalmaz, és a felhőszolgáltatás-adatait rögzíti. A Change Tracking irányítópulton található információk használatával egyszerűen megtekintheti a kiszolgálói infrastruktúrában végrehajtott módosításokat.

- **[Leltár](https://docs.microsoft.com/azure/automation/automation-vm-inventory)** . Egy Azure Stack virtuális gép leltározásának nyomon követése egy böngészőalapú felhasználói felületet biztosít a leltár-gyűjtemény beállításához és konfigurálásához.

- **[Azure monitor for VMS](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)** . Azure Monitor for VMs figyeli az Azure-t és a Azure Stack virtuális gépeket (VM) és a virtuálisgép-méretezési csoportokat skálán. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket. 

> [!IMPORTANT]
> Ezek a megoldások megegyeznek az Azure-beli virtuális gépek kezeléséhez használt megoldásokkal. Az Azure és a Azure Stack virtuális gépek is ugyanúgy kezelhetők, mint ugyanazon a felületen, ugyanazokat az eszközöket használva. A Azure Stack virtuális gépek az Azure-beli virtuális gépekre is ugyanazok, mint a Update Management, a Change Tracking, a leltár és a Azure Monitor Virtual Machines megoldás használata esetén Azure Stack.

## <a name="prerequisites"></a>Előfeltételek
A szolgáltatások Azure Stack virtuális gépek frissítéséhez és kezeléséhez több előfeltételnek kell teljesülnie. Ezek közé tartoznak a Azure Portal, valamint a Azure Stack felügyeleti portálon elvégzendő lépések.

### <a name="in-the-azure-portal"></a>A Azure Portal
A Azure Stack virtuális gépekhez tartozó Azure Monitor for VMs, leltár, Change Tracking és Update Management Azure Automation-funkciók használatához először engedélyeznie kell ezeket a megoldásokat az Azure-ban.

> [!TIP]
> Ha már rendelkezik ezekkel a szolgáltatásokkal az Azure-beli virtuális gépekhez, használhatja a meglévő LogAnalytics-munkaterület hitelesítő adatait. Ha már rendelkezik egy használni kívánt LogAnalytics-munkaterület azonosítója és elsődleges kulccsal, ugorjon [a következő szakaszra](./vm-update-management.md#in-the-azure-stack-administration-portal). Ellenkező esetben folytassa ezt a szakaszt egy új LogAnalytics-munkaterület és egy Automation-fiók létrehozásához.

A megoldások engedélyezésének első lépéseként hozzon [létre egy LogAnalytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) -munkaterületet az Azure-előfizetésében. A Log Analytics munkaterület egy egyedi Azure Monitor-naplózási környezet, amely a saját adattárházával, adatforrásaival és megoldásaival rendelkezik. Miután létrehozott egy munkaterületet, jegyezze fel a munkaterület azonosítója és a kulcsot. Az információk megtekintéséhez lépjen a munkaterület panelre, kattintson a **Speciális beállítások**elemre, és tekintse át a **munkaterület azonosítóját** és az **elsődleges kulcs** értékeit. 

Ezután [létre kell hoznia egy Automation-fiókot](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Az Automation-fiók a Azure Automation erőforrásainak tárolója. Módot biztosít a környezetek elkülönítésére, illetve az automatizálási munkafolyamatok és erőforrások további rendszerezésére. Az Automation-fiók létrehozása után engedélyeznie kell a leltárt, a változások nyomon követését és a frissítési felügyeleti funkciókat. Ehhez hajtsa végre az alábbi lépéseket az egyes szolgáltatások engedélyezéséhez:

1. A Azure Portal lépjen a használni kívánt Automation-fiókra.

2. Válassza ki az engedélyezni kívánt megoldást ( **leltár**, **változások követése**vagy **frissítés kezelése**).

3. Használja a **munkaterület kiválasztása...** legördülő listát a használni kívánt log Analytics-munkaterület kiválasztásához.

4. Ellenőrizze, hogy a többi adat helyes-e, majd kattintson az **Engedélyezés** gombra a megoldás engedélyezéséhez.

5. Mindhárom megoldás engedélyezéséhez ismételje meg a 2-4. lépést. 

   [![](media/vm-update-management/1-sm.PNG "Automation-fiókok funkcióinak engedélyezése")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="enable-azure-monitor-for-vms"></a>Azure Monitor for VMs engedélyezése

A virtuális gépek az Azure Monitor figyeli az Azure-beli virtuális gépek (VM), és a virtuálisgép-méretezési csoportok ipari méretekben. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket.

Az Azure Monitor-beli virtuális gépek a megoldás, a teljesítmény- és alkalmazásfüggőségek figyelés, a virtuális gépek, amelyek üzemeltethető a helyszínen vagy egy másik felhőszolgáltatóval támogatását is tartalmazza. Három főbb funkcióiról részletes betekintést biztosít:

1. Windows és Linux rendszerű Azure-beli virtuális gépek logikai összetevői: Az előre konfigurált állapotra vonatkozó feltételek alapján történik, és riasztást küld a kiértékelt feltétel teljesülése esetén. 

2. Előre definiált trendek teljesítményének diagramjai: A vendég virtuális gép operációs rendszerének alapvető teljesítmény-mérőszámait jeleníti meg.

3. Függőségi Térkép: Megjeleníti az összekapcsolt összetevőket a virtuális géppel különböző erőforráscsoportok és előfizetések között.

A Log Analytics munkaterület létrehozása után engedélyeznie kell a teljesítményszámlálókat a munkaterületen a Linux-és Windows-alapú virtuális gépeken történő gyűjtéshez, valamint a ServiceMap és a InfrastructureInsights megoldás telepítéséhez és engedélyezéséhez a munkaterületen. A folyamat leírását a [Azure monitor for VMS üzembe helyezési](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-onboard#how-to-enable-azure-monitor-for-vms-preview) útmutatója tartalmazza.

### <a name="in-the-azure-stack-administration-portal"></a>A Azure Stack felügyeleti portálon
Miután engedélyezte a Azure Automation megoldásokat a Azure Portalban, be kell jelentkeznie a Azure Stack felügyeleti portálra felhőalapú rendszergazdaként, és le kell töltenie a **Azure monitor, a frissítés és a konfiguráció kezelését,** valamint a Azure monitort.  **A Linux-** bővítmények Azure stack Marketplace-elemek frissítése és konfigurálása. 

   ![Azure Monitor, frissítés és konfigurálási felügyeleti bővítmény Marketplace-elem](media/vm-update-management/2.PNG) 

Ha engedélyezni szeretné a Azure Monitor for VMs térképi megoldást, és betekintést nyerhet a hálózati függőségekre, le kell töltenie a **Azure Monitor Dependency Agent**is:

   ![Azure Monitor Dependency Agent](media/vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>Update Management engedélyezése Azure Stack virtuális gépekhez
Az alábbi lépéseket követve engedélyezheti Azure Stack virtuális gépek frissítésének kezelését.

1. Jelentkezzen be a Azure Stack felhasználói portálra.

2. A Azure Stack User-Portalon nyissa meg a virtuális gépek bővítmények paneljét, amely számára engedélyezni kívánja ezeket a megoldásokat, kattintson a **+ Hozzáadás**gombra, jelölje ki az **Azure Update and Configuration Management** bővítményt, és kattintson a **Létrehozás**gombra:

   [![](media/vm-update-management/3-sm.PNG "Virtuálisgép-bővítmény panel")](media/vm-update-management/3-lg.PNG#lightbox)

3. Adja meg a korábban létrehozott munkaterület azonosítója és elsődleges kulcsot, hogy összekapcsolja az ügynököt a LogAnalytics munkaterülettel, és kattintson **az OK** gombra a bővítmény telepítéséhez.

   [![](media/vm-update-management/4-sm.PNG "A munkaterület azonosítója és a kulcs megadása")](media/vm-update-management/4-lg.PNG#lightbox) 

4. Az [Automation Update Management dokumentációjában](https://docs.microsoft.com/azure/automation/automation-update-management)leírtaknak megfelelően engedélyeznie kell a Update Management megoldást minden felügyelni kívánt virtuális géphez. Ha engedélyezni szeretné a megoldást a munkaterületre jelentést küldő összes virtuális gépre vonatkozóan, válassza a **kezelés**, majd a **számítógépek kezelése**lehetőséget, és jelölje be az **Engedélyezés az összes elérhető és jövőbeli gépen** beállítást.

   [![](media/vm-update-management/5-sm.PNG "A munkaterület azonosítója és a kulcs megadása")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Ismételje meg ezt a lépést, hogy minden megoldást engedélyezzen a munkaterületnek jelentést tevő Azure Stack virtuális gépek számára. 
  
Ha az Azure Update és a Configuration Management bővítmény engedélyezve van, az egyes felügyelt virtuális gépeken naponta kétszer végeznek vizsgálatot. Az API-t 15 percenként hívja meg a rendszer, hogy lekérdezze az utolsó frissítés idejét annak megállapításához, hogy megváltozott-e az állapot. Ha az állapot módosult, a rendszer megfelelőségi vizsgálatot kezdeményez.

A virtuális gépek beolvasása után a rendszer a Update Management megoldás Azure Automation fiókjában fogja megjelenni: 

   [![](media/vm-update-management/6-sm.PNG "A munkaterület azonosítója és a kulcs megadása")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> A felügyelt számítógépekről származó frissített adatok megjelenítéséhez az irányítópult 30 perc és 6 óra között is eltarthat.

A Azure Stack virtuális gépek mostantól az Azure-beli virtuális gépekkel együtt is szerepelhetnek az ütemezett frissítések telepítése során.

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack"></a>Azure Stackon futó Azure Monitor for VMs engedélyezése
Miután a virtuális gép rendelkezik a **Azure monitor, a frissítés és a konfiguráció kezelése** és a **Azure monitor Dependency Agent** bővítmények telepítve vannak, a [Azure monitor for VMS](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) -megoldásban elkezdi a jelentéskészítési adatgyűjtést. 

> [!TIP]
> A **Azure Monitor Dependency Agent** kiterjesztéshez nincs szükség paraméterre. Az Azure Monitor, virtuális gépek térkép függőségi ügynök maga adatokat nem továbbít, és nincs szükség tűzfalak és a portok módosítása. A térképadatok mindig továbbítása a Log Analytics-ügynököket, az Azure Monitor szolgáltatásba, vagy közvetlenül vagy keresztül a [OMS-átjáró](https://docs.microsoft.com/azure/azure-monitor/platform/gateway) , ha az informatikai biztonsági szabályzatok nem engedélyezése a számítógépek a hálózat csatlakozik az internethez.

Azure Monitor for VMs olyan teljesítménymutatókat tartalmaz, amelyek több fő teljesítménymutatót (KPI-ket) céloznak meg, amelyek segítségével meghatározhatja, hogy a virtuális gép milyen jól van végrehajtva. A diagramok egy adott időszakon belül jelenítik meg az erőforrás-használatot, így azonosíthatja a szűk keresztmetszeteket, a rendellenességeket, vagy átválthat az egyes gépekre mutató perspektívára, hogy megtekintse az erőforrás-kihasználtságot a kiválasztott metrika alapján. Noha a teljesítmény kezelése során számos szempontot figyelembe kell venni, Azure Monitor for VMs figyeli a processzorral, a memóriával, a hálózati adapterrel és a lemez kihasználtságával kapcsolatos fő operációsrendszer-teljesítménymutatókat. A teljesítmény kiegészíti az állapotfigyelő funkciót, és segít a lehetséges rendszerösszetevő-meghibásodást jelző problémák megoldásában, a hatékonyság növelését és optimalizálását, illetve a kapacitás tervezésének támogatását.

   ![Azure Monitor teljesítmény lap](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

A felderített alkalmazás-összetevők megtekintését Azure Stack futó Windows-és Linux-alapú virtuális gépeken két módon lehet megfigyelni Azure Monitor for VMs, egy virtuális gépről közvetlenül vagy több, a Azure Monitor-ból származó virtuálisgép-csoport között.
Az [alkalmazások összetevőinek megismeréséhez használható Azure monitor for VMS (előzetes verzió) Térkép](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-maps) segítségével megismerheti a két perspektíva közötti élményt és a Térkép funkció használatát.

   ![Azure Monitor teljesítmény lap](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)


## <a name="enable-update-management-using-a-resource-manager-template"></a>Update Management engedélyezése Resource Manager-sablon használatával
Ha nagyszámú Azure Stack virtuális gépet használ, [ezzel a Azure Resource Manager sablonnal](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) könnyebben telepítheti a megoldást a virtuális gépeken. A sablon telepíti a Microsoft monitoring Agent bővítményt egy meglévő Azure Stack virtuális gépre, és hozzáadja azt egy meglévő Azure LogAnalytics-munkaterülethez.
 
## <a name="next-steps"></a>További lépések
[SQL Server VM teljesítmény optimalizálása](azure-stack-sql-server-vm-considerations.md)