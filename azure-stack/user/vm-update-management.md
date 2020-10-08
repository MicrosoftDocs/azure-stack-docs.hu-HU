---
title: VIRTUÁLIS gépek frissítési és kezelési automatizálása Azure Stack központban
description: A Azure Automation Azure Monitor for VMs, Update Management, Change Tracking és leltározási megoldásait használhatja a Azure Stack hub-ban üzembe helyezett Windows-és Linux-alapú virtuális gépek felügyeletéhez.
author: mattbriggs
ms.topic: article
ms.date: 10/08/2020
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 10/08/2020
ms.openlocfilehash: b9a37df9404de01bd9b094ae259c8c62637cb369
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91853261"
---
# <a name="vm-update-and-management-automation-in-azure-stack-hub"></a>VIRTUÁLIS gépek frissítési és kezelési automatizálása Azure Stack központban
Az Azure Stack hub használatával üzembe helyezett Windows-és Linux-alapú virtuális gépek (VM-EK) kezeléséhez használja az alábbi Azure Automation megoldás-szolgáltatásokat:

- **[Update Management](/azure/automation/automation-update-management)**: a Update Management megoldással gyorsan elemezheti az összes ügynök számítógépén elérhető frissítések állapotát, és kezelheti a Windows és Linux rendszerű virtuális gépek szükséges frissítéseinek telepítését.

- **[Change Tracking](/azure/automation/automation-change-tracking)**: a telepített szoftverek, a Windows-szolgáltatások, a Windows-beállításjegyzék és-fájlok, valamint a figyelt kiszolgálókon található Linux-démonok módosításai a felhőben a Azure monitor szolgáltatásba kerülnek feldolgozásra. A felhőszolgáltatás egy logikát alkalmaz a kapott adatokon, és rögzíti azokat. A Change Tracking irányítópulton található információk használatával egyszerűen megtekintheti a kiszolgálói infrastruktúrában végrehajtott módosításokat.

- **[Leltár](/azure/automation/automation-vm-inventory)**. Egy Azure Stack hub-alapú virtuális gép leltározási követése egy böngészőalapú felhasználói felületet biztosít a leltár-gyűjtemény beállításához és konfigurálásához.

- **[Azure monitor for VMS](/azure/azure-monitor/insights/vminsights-overview)**: a Azure monitor for VMS figyeli az Azure-beli és a Azure stack hub-beli virtuális gépeket és a virtuálisgép-méretezési csoportokat a skálán. Elemzi a Windows-és Linux-alapú virtuális gépek teljesítményét és állapotát, valamint figyeli a folyamatokat és a függőségeket más erőforrásokra és külső folyamatokra.

> [!IMPORTANT]
> Ezek a megoldások megegyeznek az Azure-beli virtuális gépek kezeléséhez használt megoldásokkal. Mind az Azure-, mind a Azure Stack hub-beli virtuális gépek ugyanúgy kezelhetők, mint ugyanazon a felületen, ugyanazokat az eszközöket használva. Az Azure Stack hub virtuális gépek is ugyanazok, mint az Azure virtuális gépek, ha a Update Management, a Change Tracking, a leltár és a Azure Monitor for VMs megoldásokat használja az Azure Stack hub használatával.

## <a name="prerequisites"></a>Előfeltételek
Ezeknek a szolgáltatásoknak a használata előtt több előfeltételnek kell teljesülnie a Azure Stack hub virtuális gépek frissítéséhez és kezeléséhez. Ezek közé tartoznak a Azure Portal és az Azure Stack hub felügyeleti portálon elvégzendő lépések is.

### <a name="in-the-azure-portal"></a>Az Azure Portalon
Az Azure Stack hub-beli virtuális gépek Azure Monitor for VMs, leltár, Change Tracking és Update Management Azure Automation funkcióinak használatához először engedélyeznie kell ezeket a megoldásokat az Azure-ban.

> [!TIP]
> Ha már engedélyezte ezeket a szolgáltatásokat az Azure-beli virtuális gépekhez, használhatja a meglévő LogAnalytics-munkaterület hitelesítő adatait. Ha már rendelkezik egy használni kívánt LogAnalytics-munkaterület azonosítója és elsődleges kulccsal, ugorjon [a következő szakaszra](./vm-update-management.md#in-the-azure-stack-hub-administrator-portal). Ellenkező esetben folytassa ezt a szakaszt egy új LogAnalytics-munkaterület és egy Automation-fiók létrehozásához.

A megoldások engedélyezésének első lépéseként [hozzon létre egy LogAnalytics-munkaterületet](/azure/log-analytics/log-analytics-quick-create-workspace) az Azure-előfizetésében. A Log Analytics munkaterület egy egyedi Azure Monitor-naplózási környezet, amely a saját adattárházával, adatforrásaival és megoldásaival rendelkezik. Miután létrehozott egy munkaterületet, jegyezze fel a munkaterület azonosítója és a kulcsot. Az információk megtekintéséhez lépjen a munkaterület panelre, kattintson a **Speciális beállítások**elemre, és tekintse át a **munkaterület azonosítóját** és az **elsődleges kulcs** értékeit. 

Ezután [létre kell hoznia egy Automation-fiókot](/azure/automation/automation-create-standalone-account). Az Automation-fiók a Azure Automation erőforrásainak tárolója. Módot biztosít a környezetek elkülönítésére, illetve az automatizálási munkafolyamatok és erőforrások további rendszerezésére. Az Automation-fiók létrehozása után engedélyeznie kell a leltár, a Change Tracking és a Update Management funkciókat. Az egyes funkciók engedélyezéséhez kövesse az alábbi lépéseket:

1. A Azure Portal lépjen a használni kívánt Automation-fiókra.

2. Válassza ki az engedélyezni kívánt megoldást ( **leltár**, **változások követése**vagy **frissítés kezelése**).

3. Használja a **munkaterület kiválasztása...** legördülő listát a használni kívánt log Analytics-munkaterület kiválasztásához.

4. Ellenőrizze, hogy a többi adat helyes-e, majd kattintson az **Engedélyezés** gombra a megoldás engedélyezéséhez.

5. Mindhárom megoldás engedélyezéséhez ismételje meg a 2-4. lépést. 

   [![A "problémák diagnosztizálása és megoldása" ablak két olyan listát mutat be, amelyeken három lehetőség van kiemelve. A leltár ki van választva. Létezik egy "Log Analytics munkaterület" legördülő lista és egy engedélyezés gomb is.](media//vm-update-management/1-sm.PNG "Azure Automation fiók funkcióinak engedélyezése")](media//vm-update-management/1-lg.PNG)

### <a name="enable-azure-monitor-for-vms"></a>Azure Monitor for VMs engedélyezése

Azure Monitor for VMs figyeli az Azure-beli virtuális gépeket és a virtuálisgép-méretezési csoportokat a skálán. Elemzi a Windows-és Linux-alapú virtuális gépek teljesítményét és állapotát, valamint figyeli a folyamatokat és a függőségeket más erőforrásokra és külső folyamatokra.

Megoldásként a Azure Monitor for VMs támogatja a helyszíni vagy más felhőben üzemeltetett virtuális gépek teljesítmény-és alkalmazás-függőségeinek figyelését. A három fő funkció részletes elemzést nyújt:

1. A Windows és Linux rendszerű Azure-beli virtuális gépek logikai összetevői, amelyek az előre konfigurált állapotra vonatkozó feltételek alapján vannak meghatározva, és a kiértékelt feltétel teljesülése esetén riasztást küldenek.

2. Előre meghatározott, a vendég virtuális gép operációs rendszerének alapvető teljesítmény-mérőszámait megjelenítő teljesítmény-diagramok.

3. Függőségi Térkép, amely a virtuális géppel különböző erőforráscsoportok és előfizetések közötti összekapcsolt összetevőket jeleníti meg.

A Log Analytics munkaterület létrehozása után engedélyezze a teljesítményszámlálók a munkaterületen a Linux-és Windows-alapú virtuális gépeken történő gyűjteményhez. Ezután telepítse és engedélyezze a ServiceMap és a InfrastructureInsights megoldást a munkaterületen. A folyamat leírását a [Azure monitor for VMS üzembe helyezési](/azure/azure-monitor/insights/vminsights-enable-overview) útmutatója tartalmazza.

### <a name="in-the-azure-stack-hub-administrator-portal"></a>Az Azure Stack hub felügyeleti portálján
Miután engedélyezte a Azure Automation megoldásokat a Azure Portalban, be kell jelentkeznie a Azure Stack hub felügyeleti portálra a felhő-rendszergazdaként, és le kell töltenie a **Azure monitor, a frissítés és a konfiguráció felügyeletét** , valamint a Linux-bővítmény **Azure monitor, frissítését és konfigurálását** a Azure stack hub piactéren.

   ![A "Home > Marketplace felügyeleti > Hozzáadás az Azure-ból > Azure Monitor, frissítés és konfiguráció kezelése" párbeszédpanel leírja a bővítményt, és a letöltés gombot.](media//vm-update-management/2.PNG) 

A Azure Monitor for VMs Map megoldás engedélyezéséhez és a hálózati függőségek betekintéséhez töltse le a **Azure Monitor Dependency Agent**:

   ![A "Home > Marketplace felügyeleti > Hozzáadás az Azure-ból > Azure Monitor Dependency Agent" párbeszédpanel leírja a bővítményt, és letölti a letöltés gombot.](media//vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-hub-vms"></a>Update Management engedélyezése Azure Stack hub-beli virtuális gépekhez
Az alábbi lépéseket követve engedélyezheti Azure Stack hub-beli virtuális gépek frissítésének kezelését.

1. Jelentkezzen be az Azure Stack hub felhasználói portálra.

2. Az Azure Stack hub-felhasználó-portálon nyissa meg a virtuális gépek bővítmények paneljét, amely számára engedélyezni szeretné ezeket a megoldásokat, kattintson a **+ Hozzáadás**gombra, jelölje ki az **Azure Update és a Configuration Management** bővítményt, majd kattintson a **Létrehozás**gombra:

    ![Az "Azure Update and Configuration Management" párbeszédpanel tartalmaz magyarázó információkat, egy létrehozás gombot (kiemelve) a bővítmény hozzáadásához, valamint egy hivatkozást a további információkhoz.](media//vm-update-management/3-sm.PNG "Virtuálisgép-bővítmény panel")

3. Adja meg a korábban létrehozott munkaterület azonosítója és elsődleges kulcsot, hogy összekapcsolja az ügynököt a LogAnalytics munkaterülettel. Ezután kattintson az **OK** gombra a bővítmény telepítéséhez.

   [![A "bővítmény telepítése" párbeszédpanel szövegmezőket tartalmaz az Azure munkaterület azonosítója és a WorkspaceKey.](media//vm-update-management/4-sm.PNG "A munkaterület azonosítója és a kulcs megadása")](media//vm-update-management/4-lg.PNG) 

4. A [Update Management dokumentációjában](/azure/automation/automation-update-management)leírtaknak megfelelően engedélyeznie kell a Update Management megoldást minden felügyelni kívánt virtuális géphez. Ha engedélyezni szeretné a megoldást a munkaterületre jelentést küldő összes virtuális gépre vonatkozóan, válassza a **kezelés**, majd a **számítógépek kezelése**lehetőséget, és jelölje be az **Engedélyezés az összes elérhető és jövőbeli gépen** beállítást.

   [![A gépek kezelése – Update Management párbeszédpanel megjeleníti azokat a gépeket, amelyeken nincs engedélyezve a Update Management. Három engedélyező beállítás van megadva, és az "engedélyezés az összes elérhető és jövőbeli gépen" beállítás ki van választva és ki van emelve. Engedélyezhető gomb.](media//vm-update-management/5-sm.PNG "Update Management megoldás engedélyezése minden gépen")](media//vm-update-management/5-lg.PNG) 

   > [!TIP]
   > Ismételje meg ezt a lépést, hogy minden megoldást engedélyezzen a munkaterületnek jelentést tevő Azure Stack hub virtuális gépek számára. 
  
Ha az Azure Update és a Configuration Management bővítmény engedélyezve van, minden felügyelt virtuális gép esetében naponta kétszer végeznek vizsgálatot. Az API-t 15 percenként hívja meg a rendszer, hogy lekérdezze az utolsó frissítés idejét annak megállapításához, hogy megváltozott-e az állapot. Ha az állapot módosult, a rendszer egy megfelelőségi vizsgálatot indít el.

A virtuális gépek beolvasása után a Update Management megoldásban megjelennek a Azure Automation fiókban: 

   [![A rendszer felsorolja a beolvasott gépeket. A rendszer minden esetben megadja a megfelelőségi állapotot, a platformot, az operációs rendszert és a kritikus hiányzó frissítések számát. Vannak olyan összesített információk, amelyek azt mutatják, hogy hány gép igényel figyelmet, hány hiányzik a frissítés, és így tovább.](media//vm-update-management/6-sm.PNG "Azure Automation fiók a Update Management")](media//vm-update-management/6-lg.PNG) 

> [!IMPORTANT]
> 30 perctől akár 6 óráig is eltarthat, amíg megjelennek a felügyelt számítógépekből származó frissített adatok az irányítópulton.

Az Azure Stack hub virtuális gépek mostantól az Azure-beli virtuális gépekkel együtt is szerepelhetnek az ütemezett frissítések telepítése során.

##  <a name="create-an-update-deployment-schedule"></a>Frissítés központi telepítési ütemtervének létrehozása

A frissítés központi telepítési ütemtervének létrehozásához PowerShell-parancsmagot vagy az Azure REST API kell használnia a gépek továbbításához. A következő PowerShell-példa használatával lekérheti a gépek ütemezését. A **[New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule)** parancsmagot használhatja a `ForUpdateConfiguration` paraméterrel az ütemterv létrehozásához. Ezután használja a **[New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/az.automation/new-azautomationsoftwareupdateconfiguration)** parancsmagot, és adja át a Azure stack hub-gépeket a `NonAzureComputer` paraméternek. A szkript futtatásához a globális [Azure PowerShell az modult](/powershell/azure/)kell használnia.

Az alábbi példa bemutatja, hogyan teheti meg ezt:

```Powershell  
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack-hub"></a>Azure Stack hub-on futó Azure Monitor for VMs engedélyezése
Miután a virtuális gép rendelkezik a **Azure monitor, a frissítés és a konfiguráció kezelésével**, valamint a **Azure monitor Dependency Agent** bővítmények telepítésével, a [Azure monitor for VMS](/azure/azure-monitor/insights/vminsights-overview) megoldásban elkezdi a jelentéskészítési adatgyűjtést. 

> [!TIP]
> A **Azure Monitor Dependency Agent** kiterjesztéshez nincs szükség paraméterre. A Azure Monitor for VMs Map-függőségi ügynök nem küld magába adatokat, és nem igényel semmilyen módosítást a tűzfalakon vagy a portokon. A leképezési adatokat a Log Analytics ügynök mindig továbbítja a Azure Monitor szolgáltatásnak közvetlenül vagy a [OMS-átjárón](/azure/azure-monitor/platform/gateway) keresztül, ha az informatikai biztonsági házirendek nem engedélyezik a hálózaton lévő számítógépek számára az internethez való kapcsolódást.

Azure Monitor for VMs olyan teljesítménymutatókat tartalmaz, amelyek több fő teljesítménymutatót (KPI-ket) céloznak meg, amelyek segítségével meghatározhatja, hogy a virtuális gépek milyen jól működnek. A diagramok egy adott időszakban jelenítik meg az erőforrás-használatot, így azonosíthatja a szűk keresztmetszeteket és a rendellenességeket. Átválthat egy perspektívára is, amely felsorolja az egyes gépeket, hogy megtekintse az erőforrás-használatot a kijelölt metrika alapján. Habár a teljesítmény kezelése során számos szempontot figyelembe kell venni, Azure Monitor for VMs figyeli a processzorral, a memóriával, a hálózati adapterrel és a lemezekkel kapcsolatos fő operációsrendszer-teljesítménymutatókat. A teljesítmény-diagramok kiegészítik az állapotfigyelő funkciót, és segítenek a rendszerösszetevők lehetséges meghibásodását jelző problémák megoldásában. A Azure Monitor for VMs Emellett támogatja a kapacitás megtervezését és finomhangolását, valamint a hatékonyság elérését.

   ![Azure Monitor virtuális gépek teljesítményének lapja](/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

A felderített alkalmazás-összetevők megtekintését Azure Stack hub-on futó Windows-és Linux virtuális gépeken két módon lehet megfigyelni Azure Monitor for VMs. Az első közvetlenül egy virtuális gépről származik, a második pedig a Azure Monitor-ból származó virtuális gépek csoportjai között.
A [Azure monitor for VMS Map használata az App Components megismeréséhez](/azure/azure-monitor/insights/vminsights-maps) című cikk segítséget nyújt a két perspektíva közötti élmény megértésében és a Térkép funkció használatának módjában.

   ![Azure Monitor virtuális gépek térképének lapja](/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)

Ha a [Azure monitor for VMS](/azure/azure-monitor/insights/vminsights-overview) nem jelenít meg teljesítményadatokat, a [LogAnalytics-munkaterület](/azure/azure-monitor/platform/data-sources-performance-counters) speciális beállításainál engedélyeznie kell a Windows és a Linux teljesítményadatok gyűjtését.

## <a name="enable-update-management-using-a-resource-manager-template"></a>Update Management engedélyezése Resource Manager-sablon használatával
Ha nagyszámú Azure Stack hub virtuális gépet használ, a [Azure Resource Manager sablonnal](https://aka.ms/aa6zdzy) egyszerűbben telepítheti a megoldást a virtuális gépeken. A sablon telepíti a Microsoft monitoring Agent bővítményt egy meglévő Azure Stack hub virtuális gépre, és hozzáadja egy meglévő Azure LogAnalytics-munkaterülethez.
 
## <a name="next-steps"></a>További lépések
[SQL Server VM teljesítmény optimalizálása](azure-stack-sql-server-vm-considerations.md)
