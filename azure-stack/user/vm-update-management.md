---
title: A virtuális gépek frissítésének és kezelésének automatizálása Azure Stackban | Microsoft Docs
description: Ismerje meg, hogyan használhatók a Azure Monitor for VMs, a Update Management, a Change Tracking és a leltározási megoldások a Azure Automationban a Azure Stack-ban üzembe helyezett Windows-és Linux-alapú virtuális gépek kezeléséhez.
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
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 87549d27418f787b0e173cfda1ca835dc1c181e4
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955948"
---
# <a name="vm-update-and-management-automation-in-azure-stack"></a>VIRTUÁLIS gépek frissítése és kezelése automatizálás Azure Stack
A következő Azure Automation megoldási funkciókkal kezelheti a Azure Stack használatával üzembe helyezett Windows-és Linux-alapú virtuális gépeket (VM-EK):

- **[Update Management](https://docs.microsoft.com/azure/automation/automation-update-management)** : a Update Management megoldással gyorsan elemezheti az összes ügynök számítógépén elérhető frissítések állapotát, és kezelheti a Windows és Linux rendszerű virtuális gépek szükséges frissítéseinek telepítését.

- **[Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking)** : a telepített szoftverek, a Windows-szolgáltatások, a Windows-beállításjegyzék és-fájlok, valamint a figyelt kiszolgálókon található Linux-démonok módosításai a felhőben a Azure monitor szolgáltatásba kerülnek feldolgozásra. A felhőszolgáltatás egy logikát alkalmaz a kapott adatokon, és rögzíti azokat. A Change Tracking irányítópulton található információk használatával egyszerűen megtekintheti a kiszolgálói infrastruktúrában végrehajtott módosításokat.

- **[Leltár](https://docs.microsoft.com/azure/automation/automation-vm-inventory)** . Egy Azure Stack virtuális gép leltározásának nyomon követése egy böngészőalapú felhasználói felületet biztosít a leltár-gyűjtemény beállításához és konfigurálásához.

- **[Azure monitor for VMS](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)** : a Azure monitor for VMS figyeli az Azure-t és Azure stack virtuális gépeket és a virtuálisgép-méretezési csoportokat a skálán. Elemzi a Windows-és Linux-alapú virtuális gépek teljesítményét és állapotát, valamint figyeli a folyamatokat és a függőségeket más erőforrásokra és külső folyamatokra.

> [!IMPORTANT]
> Ezek a megoldások megegyeznek az Azure-beli virtuális gépek kezeléséhez használt megoldásokkal. Az Azure és a Azure Stack virtuális gépek is ugyanúgy kezelhetők, mint ugyanazon a felületen, ugyanazokat az eszközöket használva. A Azure Stack virtuális gépek az Azure-beli virtuális gépekre is ugyanazok, mint a Update Management, a Change Tracking, a leltár és a Azure Monitor for VMs megoldások használatakor Azure Stack.

## <a name="prerequisites"></a>Előfeltételek
A szolgáltatások Azure Stack virtuális gépek frissítéséhez és kezeléséhez több előfeltételnek kell teljesülnie. Ezek közé tartoznak a Azure Portal és a Azure Stack felügyeleti portálon elvégzendő lépések is.

### <a name="in-the-azure-portal"></a>A Azure Portal
A Azure Stack virtuális gépek Azure Monitor for VMs, leltározási, Change Trackingi és Update Management Azure Automation funkcióinak használatához először engedélyeznie kell ezeket a megoldásokat az Azure-ban.

> [!TIP]
> Ha már engedélyezte ezeket a szolgáltatásokat az Azure-beli virtuális gépekhez, használhatja a meglévő LogAnalytics-munkaterület hitelesítő adatait. Ha már rendelkezik egy használni kívánt LogAnalytics-munkaterület azonosítója és elsődleges kulccsal, ugorjon [a következő szakaszra](./vm-update-management.md#in-the-azure-stack-administrator-portal). Ellenkező esetben folytassa ezt a szakaszt egy új LogAnalytics-munkaterület és egy Automation-fiók létrehozásához.

A megoldások engedélyezésének első lépéseként [hozzon létre egy LogAnalytics-munkaterületet](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) az Azure-előfizetésében. A Log Analytics munkaterület egy egyedi Azure Monitor-naplózási környezet, amely a saját adattárházával, adatforrásaival és megoldásaival rendelkezik. Miután létrehozott egy munkaterületet, jegyezze fel a munkaterület azonosítója és a kulcsot. Az információk megtekintéséhez lépjen a munkaterület panelre, kattintson a **Speciális beállítások**elemre, és tekintse át a **munkaterület azonosítóját** és az **elsődleges kulcs** értékeit. 

Ezután [létre kell hoznia egy Automation-fiókot](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Az Automation-fiók a Azure Automation erőforrásainak tárolója. Módot biztosít a környezetek elkülönítésére, illetve az automatizálási munkafolyamatok és erőforrások további rendszerezésére. Az Automation-fiók létrehozása után engedélyeznie kell a leltár, a Change Tracking és a Update Management funkciókat. Az egyes funkciók engedélyezéséhez kövesse az alábbi lépéseket:

1. A Azure Portal lépjen a használni kívánt Automation-fiókra.

2. Válassza ki az engedélyezni kívánt megoldást ( **leltár**, **változások követése**vagy **frissítés kezelése**).

3. Használja a **munkaterület kiválasztása...** legördülő listát a használni kívánt log Analytics-munkaterület kiválasztásához.

4. Ellenőrizze, hogy a többi adat helyes-e, majd kattintson az **Engedélyezés** gombra a megoldás engedélyezéséhez.

5. Mindhárom megoldás engedélyezéséhez ismételje meg a 2-4. lépést. 

   [![](media/vm-update-management/1-sm.PNG "Enable Azure Automation account features")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="enable-azure-monitor-for-vms"></a>Azure Monitor for VMs engedélyezése

Azure Monitor for VMs figyeli az Azure-beli virtuális gépeket és a virtuálisgép-méretezési csoportokat a skálán. Elemzi a Windows-és Linux-alapú virtuális gépek teljesítményét és állapotát, valamint figyeli a folyamatokat és a függőségeket más erőforrásokra és külső folyamatokra.

Megoldásként a Azure Monitor for VMs támogatja a helyszíni vagy más felhőben üzemeltetett virtuális gépek teljesítmény-és alkalmazás-függőségeinek figyelését. A három fő funkció részletes elemzést nyújt:

1. A Windows és Linux rendszerű Azure-beli virtuális gépek logikai összetevői, amelyek az előre konfigurált állapotra vonatkozó feltételek alapján vannak meghatározva, és a kiértékelt feltétel teljesülése esetén riasztást küldenek.

2. Előre meghatározott, a vendég virtuális gép operációs rendszerének alapvető teljesítmény-mérőszámait megjelenítő teljesítmény-diagramok.

3. Függőségi Térkép, amely a virtuális géppel különböző erőforráscsoportok és előfizetések közötti összekapcsolt összetevőket jeleníti meg.

A Log Analytics munkaterület létrehozása után engedélyezze a teljesítményszámlálók a munkaterületen a Linux-és Windows-alapú virtuális gépeken történő gyűjteményhez. Ezután telepítse és engedélyezze a ServiceMap és a InfrastructureInsights megoldást a munkaterületen. A folyamat leírását a [Azure monitor for VMS üzembe helyezési](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-onboard#how-to-enable-azure-monitor-for-vms-preview) útmutatója tartalmazza.

### <a name="in-the-azure-stack-administrator-portal"></a>A Azure Stack felügyeleti portálon
Miután engedélyezte a Azure Automation megoldásokat a Azure Portalban, be kell jelentkeznie a Azure Stack felügyeleti portálra a Felhőbeli rendszergazdaként, és le kell töltenie a **Azure monitor, a frissítés és a konfiguráció felügyeletét** , valamint a Linux-bővítmény **Azure monitor, frissítését és konfigurálását** a Azure stack piactéren.

   ![Azure Monitor, frissítés és konfigurálási felügyeleti bővítmény Marketplace-elem](media/vm-update-management/2.PNG) 

A Azure Monitor for VMs Map megoldás engedélyezéséhez és a hálózati függőségek betekintéséhez töltse le a **Azure Monitor Dependency Agent**:

   ![Azure Monitor Dependency Agent](media/vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-vms"></a>Update Management engedélyezése Azure Stack virtuális gépekhez
Az alábbi lépéseket követve engedélyezheti Azure Stack virtuális gépek frissítésének kezelését.

1. Jelentkezzen be a Azure Stack felhasználói portálra.

2. A Azure Stack User-Portalon nyissa meg a virtuális gépek bővítmények paneljét, amelyen engedélyezni szeretné ezeket a megoldásokat, kattintson a **+ Hozzáadás**gombra, jelölje ki az **Azure Update és a Configuration Management** bővítményt, majd kattintson a **Létrehozás**gombra:

   [![](media/vm-update-management/3-sm.PNG "VM extension blade")](media/vm-update-management/3-lg.PNG#lightbox)

3. Adja meg a korábban létrehozott munkaterület azonosítója és elsődleges kulcsot, hogy összekapcsolja az ügynököt a LogAnalytics munkaterülettel. Ezután kattintson az **OK** gombra a bővítmény telepítéséhez.

   [![](media/vm-update-management/4-sm.PNG "Providing the WorkspaceID and Key")](media/vm-update-management/4-lg.PNG#lightbox) 

4. A [Update Management dokumentációjában](https://docs.microsoft.com/azure/automation/automation-update-management)leírtaknak megfelelően engedélyeznie kell a Update Management megoldást minden felügyelni kívánt virtuális géphez. Ha engedélyezni szeretné a megoldást a munkaterületre jelentést küldő összes virtuális gépre vonatkozóan, válassza a **kezelés**, majd a **számítógépek kezelése**lehetőséget, és jelölje be az **Engedélyezés az összes elérhető és jövőbeli gépen** beállítást.

   [![](media/vm-update-management/5-sm.PNG "Enable Update Management solution on all machines")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Ismételje meg ezt a lépést, hogy minden megoldást engedélyezzen a munkaterületnek jelentést tevő Azure Stack virtuális gépek számára. 
  
Ha az Azure Update és a Configuration Management bővítmény engedélyezve van, minden felügyelt virtuális gép esetében naponta kétszer végeznek vizsgálatot. Az API-t 15 percenként hívja meg a rendszer, hogy lekérdezze az utolsó frissítés idejét annak megállapításához, hogy megváltozott-e az állapot. Ha az állapot módosult, a rendszer egy megfelelőségi vizsgálatot indít el.

A virtuális gépek beolvasása után a Update Management megoldásban megjelennek a Azure Automation fiókban: 

   [![](media/vm-update-management/6-sm.PNG "Azure Automation account in Update Management")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> A felügyelt számítógépekről származó frissített adatok megjelenítéséhez az irányítópult 30 perc és 6 óra között is eltarthat.

A Azure Stack virtuális gépek mostantól az Azure-beli virtuális gépekkel együtt is szerepelhetnek az ütemezett frissítések telepítése során.

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack"></a>Azure Stackon futó Azure Monitor for VMs engedélyezése
Miután a virtuális gép rendelkezik a **Azure monitor, a frissítés és a konfiguráció kezelésével**, valamint a **Azure monitor Dependency Agent** bővítmények telepítésével, a [Azure monitor for VMS](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) megoldásban elkezdi a jelentéskészítési adatgyűjtést. 

> [!TIP]
> A **Azure Monitor Dependency Agent** kiterjesztéshez nincs szükség paraméterre. A Azure Monitor for VMs Map-függőségi ügynök nem küld magába adatokat, és nem igényel semmilyen módosítást a tűzfalakon vagy a portokon. A leképezési adatokat a Log Analytics ügynök mindig továbbítja a Azure Monitor szolgáltatásnak közvetlenül vagy a [OMS-átjárón](https://docs.microsoft.com/azure/azure-monitor/platform/gateway) keresztül, ha az informatikai biztonsági házirendek nem engedélyezik a hálózaton lévő számítógépek számára az internethez való kapcsolódást.

Azure Monitor for VMs olyan teljesítménymutatókat tartalmaz, amelyek több fő teljesítménymutatót (KPI-ket) céloznak meg, amelyek segítségével meghatározhatja, hogy a virtuális gépek milyen jól működnek. A diagramok egy adott időszakban jelenítik meg az erőforrás-használatot, így azonosíthatja a szűk keresztmetszeteket és a rendellenességeket. Átválthat egy perspektívára is, amely felsorolja az egyes gépeket, hogy megtekintse az erőforrás-használatot a kijelölt metrika alapján. Habár a teljesítmény kezelése során számos szempontot figyelembe kell venni, Azure Monitor for VMs figyeli a processzorral, a memóriával, a hálózati adapterrel és a lemezekkel kapcsolatos fő operációsrendszer-teljesítménymutatókat. A teljesítmény-diagramok kiegészítik az állapotfigyelő funkciót, és segítenek a rendszerösszetevők lehetséges meghibásodását jelző problémák megoldásában. A Azure Monitor for VMs Emellett támogatja a kapacitás megtervezését és finomhangolását, valamint a hatékonyság elérését.

   ![Azure Monitor virtuális gépek teljesítményének lapja](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

A felderített alkalmazás-összetevők megtekintése Azure Stackban futó Windows és Linux rendszerű virtuális gépeken két módon is megfigyelhető Azure Monitor for VMs. Az első közvetlenül egy virtuális gépről származik, a második pedig a Azure Monitor-ból származó virtuális gépek csoportjai között.
A [Azure monitor for VMS Map használata az App Components megismeréséhez](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-maps) című cikk segítséget nyújt a két perspektíva közötti élmény megértésében és a Térkép funkció használatának módjában.

   ![Azure Monitor virtuális gépek térképének lapja](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)

Ha a [Azure monitor for VMS](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) nem jelenít meg teljesítményadatokat, a [LogAnalytics-munkaterület](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters) speciális beállításainál engedélyeznie kell a Windows és a Linux teljesítményadatok gyűjtését.

## <a name="enable-update-management-using-a-resource-manager-template"></a>Update Management engedélyezése Resource Manager-sablon használatával
Ha nagyszámú Azure Stack virtuális gépet használ, [ezzel a Azure Resource Manager sablonnal](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) könnyebben telepítheti a megoldást a virtuális gépeken. A sablon telepíti a Microsoft monitoring Agent bővítményt egy meglévő Azure Stack virtuális gépre, és hozzáadja azt egy meglévő Azure LogAnalytics-munkaterülethez.
 
## <a name="next-steps"></a>Következő lépések
[SQL Server VM teljesítmény optimalizálása](azure-stack-sql-server-vm-considerations.md)
