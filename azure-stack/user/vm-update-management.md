---
title: Virtuális gép frissítése és kezelése az Azure Stack |} A Microsoft Docs
description: Ismerje meg az Azure Monitor használata virtuális gépek, az Update Management, Change Tracking és Inventory megoldások az Azure Automationben Windows és Linux rendszerű virtuális gépek az Azure Stack üzembe helyezett kezeléséhez.
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
ms.date: 03/20/2019
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: fc5466880c7340f5e9a79b7ece79663d12c9a598
ms.sourcegitcommit: 75b13158347963063b7ee62b0ec57894b542c1be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66749017"
---
# <a name="azure-stack-vm-update-and-management"></a>Az Azure Stack VM update és kezelése
A következő Azure Automation-megoldás funkciók segítségével kezelheti a Windows és Linux rendszerű virtuális gépek, amelyek az Azure Stack használatával helyezi üzembe:

- **[Frissítéskezelés](https://docs.microsoft.com/azure/automation/automation-update-management)** . Az Update Management megoldással gyorsan felmérheti az elérhető frissítések minden ügynökszámítógépen állapotát és a folyamat szükséges frissítéseinek telepítése ezen Windows és Linux rendszerű virtuális gépek kezeléséhez.

- **[A változáskövetés](https://docs.microsoft.com/azure/automation/automation-change-tracking)** . Telepített szoftverek, Windows-szolgáltatások, Windows-beállításjegyzék és a fájlok és a figyelt kiszolgálókról a Linux-démonok változásokat küldi el a feldolgozás a felhőben az Azure Monitor szolgáltatáshoz. A fogadott adatokat logikát alkalmaz, és a felhőszolgáltatás-adatait rögzíti. A Change Tracking irányítópultján található információk segítségével könnyen megtekintheti az a kiszolgáló-infrastruktúrájában elvégzett módosítások.

- **[Készlet](https://docs.microsoft.com/azure/automation/automation-vm-inventory)** . A leltár nyomon követése az Azure Stack virtuális gépként és leltár konfigurálása egy böngészőalapú felhasználói felületet biztosít.

- **[A virtuális gépek az Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)** . A virtuális gépek az Azure Monitor figyeli az Azure és az Azure Stack virtuális gépek (VM), és a virtuálisgép-méretezési csoportok ipari méretekben. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket. 

> [!IMPORTANT]
> Ezek a megoldások ugyanazok, mint az Azure virtuális gépek kezelésére szolgáló azokat. Az Azure és az Azure Stack virtuális gépek felügyelt ugyanolyan módon, egy közös felületen, ugyanazokkal az eszközökkel. Az Azure Stack-beli virtuális gépek is díjszabása ugyanaz, mint az Azure virtuális gépek az Azure Stack az Update Management, változáskövetés, leltár és Azure virtuális gépek figyelése megoldások használatakor.

## <a name="prerequisites"></a>Előfeltételek
Néhány előfeltételnek teljesülnie kell, mielőtt használja ezeket a funkciókat, frissítése és az Azure Stack virtuális gépek kezelése. Ezek közé tartozik, amely kell tenni a lépéseket az Azure Portalon, valamint az Azure Stack felügyeleti portálon.

### <a name="in-the-azure-portal"></a>Az Azure Portalon
Az Azure Monitor használata virtuális gépek, a szoftverleltár, a Change Tracking és a Update Management az Azure automation szolgáltatásokat az Azure Stack-beli virtuális gépek, akkor először engedélyeznie kell ezeket a megoldásokat az Azure-ban.

> [!TIP]
> Ha már rendelkezik ezeket a funkciókat az Azure virtuális gépek engedélyezve van, használhatja a már meglévő LogAnalytics munkaterület hitelesítő adatait. Ha már rendelkezik egy LogAnalytics munkaterület azonosítója és az elsődleges kulcs, amelyet használni szeretne, ugorjon előre [a következő szakaszban](./vm-update-management.md#in-the-azure-stack-administration-portal). Ellenkező esetben továbbra is ebben a szakaszban egy új LogAnalytics munkaterületet és automation-fiók létrehozásához.

Az első lépése abban, hogy ezek a megoldások [LogAnalytics munkaterület létrehozása](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) az Azure-előfizetésében. Log Analytics-munkaterület olyan saját adattárházzal, adatforrások és megoldások egyedi az Azure Monitor naplók környezet. Miután létrehozott egy munkaterületet, vegye figyelembe a munkaterület azonosítója és kulcsa. Ezek az információk megtekintéséhez nyissa meg a munkaterület panel, kattintson a **speciális beállítások**, és tekintse át a **munkaterület-Azonosítót** és **elsődleges kulcs** értékeket. 

Ezt követően kell [Automation-fiók létrehozása](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Az Automation-fiók egy tároló az Azure Automation-erőforrások számára. A környezetek elkülönítésére vagy további rendszerezésére az automatizált munkafolyamatokat és erőforrásokat biztosít. Az automation-fiók létrehozása után meg kell ahhoz, hogy a készlet változások követése, és felügyeleti funkciók frissítése. Ehhez kövesse az alábbi lépéseket mindegyik funkciónak az engedélyezéséhez:

1. Az Azure Portalon nyissa meg az Automation-fiókot, amelyet használni szeretne.

2. Válassza ki a megoldás (vagy **készlet**, **Change tracking**, vagy **frissítéskezelés**).

3. Használja a **munkaterület kiválasztása...**  legördülő listában válassza ki a Log Analytics-munkaterület használatára.

4. Győződjön meg arról, hogy helyesen szerepel-e az összes többi adatot, és kattintson **engedélyezése** a megoldás engedélyezéséhez.

5. Ismételje meg a 2 – 4 összes három megoldás engedélyezéséhez. 

   [![](media/vm-update-management/1-sm.PNG "Automation-fiók szolgáltatások engedélyezése")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="enable-azure-monitor-for-vms"></a>Az Azure Monitor-beli virtuális gépek engedélyezése

A virtuális gépek az Azure Monitor figyeli az Azure-beli virtuális gépek (VM), és a virtuálisgép-méretezési csoportok ipari méretekben. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket.

Az Azure Monitor-beli virtuális gépek a megoldás, a teljesítmény- és alkalmazásfüggőségek figyelés, a virtuális gépek, amelyek üzemeltethető a helyszínen vagy egy másik felhőszolgáltatóval támogatását is tartalmazza. Három főbb funkcióiról részletes betekintést biztosít:

1. Windows és Linux rendszerű Azure virtuális gépek logikai összetevőit: Előre konfigurált állapotára vonatkozó feltételek, elleni mérése történik, és azok riasztást küld, ha a kiértékelt feltétel nem teljesül. 

2. Előre definiált felkapott teljesítménydiagramok: A Vendég virtuális gép operációs rendszerről alapvető teljesítmény-mérőszámait jeleníti meg.

3. Függőségi térkép: A virtuális géppel a különböző erőforráscsoportokban és előfizetésekben összekapcsolt összetevőket jeleníti meg.

A Log Analytics-munkaterület létrehozása után kell a munkaterületen, a Linux és Windows-beli virtuális gépek gyűjtemény teljesítményszámlálók engedélyezése, valamint telepítése, és engedélyezze a ServiceMap és InfrastructureInsights megoldást a munkaterületén. Az eljárást a a [-beli virtuális gépek üzembe helyezése az Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-onboard#how-to-enable-azure-monitor-for-vms-preview) útmutató.

### <a name="in-the-azure-stack-administration-portal"></a>Az Azure Stack felügyeleti portálon
Miután engedélyezte az Azure Automation-megoldások az Azure Portalon, ezután kell bejelentkezni az Azure Stack felügyeleti portálon, a felhő rendszergazdájához, és töltse le a **Azure Monitor, az Update és a konfigurációkezelés** és a **Azure Monitor, az Update és a Linux-kezelési** kiterjesztése az Azure Stack piactéren elemek. 

   ![Az Azure Monitor, az update és a konfigurációs felügyeleti bővítmény Piactéri elem](media/vm-update-management/2.PNG) 

Engedélyezze a virtuális gépek Map megoldását az Azure Monitor, és betekintést nyerhet a hálózati függőségeket, kell is letöltheti a **Azure Monitor függőségi ügynök**:

   ![Az Azure Monitor függőségi ügynök](media/vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>Az Update Management engedélyezése a virtuális gépek az Azure Stack
Kövesse az alábbi lépéseket az Azure Stack-beli virtuális gépek az update management engedélyezése.

1. Jelentkezzen be az Azure Stack felhasználói portálon.

2. Az Azure Stack felhasználói portálon, nyissa meg a bővítmények panelt, amelynek meg szeretné engedélyezze ezeket a megoldásokat, kattintson a virtuális gépek **+ Hozzáadás**, jelölje be a **Azure Update és a konfigurációkezelés** bővítmény, és kattintson **létrehozás**:

   [![](media/vm-update-management/3-sm.PNG "VM-bővítmény panel")](media/vm-update-management/3-lg.PNG#lightbox)

3. Adja meg a korábban létrehozott munkaterület azonosítója és az elsődleges kulcsot az ügynököt a LogAnalytics munkaterület összekapcsolása, és kattintson a **OK** a bővítmény telepítéséhez.

   [![](media/vm-update-management/4-sm.PNG "A munkaterület azonosítója és kulcsa")](media/vm-update-management/4-lg.PNG#lightbox) 

4. Leírtak szerint a [automation update management dokumentációja](https://docs.microsoft.com/azure/automation/automation-update-management), engedélyezze a frissítéskezelési megoldás az egyes virtuális Gépekhez, amelyet kezelni szeretne. A megoldás engedélyezéséhez a munkaterületre jelentő összes virtuális gép, jelölje ki **frissítéskezelés**, kattintson a **gépek kezelése**, majd válassza ki a **engedélyezés minden rendelkezésre álló és jövőbeli gépen** lehetőséget.

   [![](media/vm-update-management/5-sm.PNG "A munkaterület azonosítója és kulcsa")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Ismételje meg ezt a lépést minden megoldás engedélyezése az Azure Stack-alapú virtuális gépek, hogy a jelentés a munkaterületre. 
  
Az Azure-frissítés és a konfigurációkezelés bővítmény engedélyezése után történik vizsgálat naponta kétszer minden felügyelt virtuális géphez. Az API neve 15 percenként az utolsó frissítésének időpontját, hogy módosultak-e az állapot meghatározására a lekérdezéshez. Ha a állapota megváltozott, a megfelelőségi vizsgálatot kezdeményez.

Miután a virtuális gépek vizsgálata, ezek megjelennek a frissítéskezelési megoldás az Azure Automation-fiók: 

   [![](media/vm-update-management/6-sm.PNG "A munkaterület azonosítója és kulcsa")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> 30 perc és a felügyelt számítógépekből származó frissített adatok megjelennek az irányítópulton 6 óráig is eltarthat.

Az Azure Stack-beli virtuális gépek és Azure virtuális gépeken ütemezett frissítéstelepítések most már szerepelni.

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack"></a>Az Azure Stacken futó virtuális gépek az Azure Monitor engedélyezése
Miután a virtuális gépen a **Azure Monitor, az Update és a konfigurációkezelés** és a **Azure Monitor függőségi ügynök** telepített kiterjesztéseket, elindul az adatok a reporting a [Azure Monitor a virtuális gépek](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) megoldás. 

> [!TIP]
> A **Azure Monitor függőségi ügynök** bővítmény nincs szükség a paramétereket. Az Azure Monitor, virtuális gépek térkép függőségi ügynök maga adatokat nem továbbít, és nincs szükség tűzfalak és a portok módosítása. A térképadatok mindig továbbítása a Log Analytics-ügynököket, az Azure Monitor szolgáltatásba, vagy közvetlenül vagy keresztül a [OMS-átjáró](https://docs.microsoft.com/azure/azure-monitor/platform/gateway) , ha az informatikai biztonsági szabályzatok nem engedélyezése a számítógépek a hálózat csatlakozik az internethez.

Az Azure Monitor-beli virtuális gépek több fő teljesítménymutatók (KPI-k) segítségével eldöntheti, milyen jól a virtuális gép működik-e a cél teljesítménydiagramok készletét tartalmazza. A diagramok megjelenítése az erőforrás-használatot egy időszakon belül, így azonosíthatja a szűk keresztmetszeteket, a rendellenességeket, vagy váltson át az egyes gépek megtekintése a kiválasztott metrika alapján erőforrás-használat listázása egy perspektíva. Áll számos elemet kell figyelembe venni a teljesítmény esetén, amelyek az Azure Monitor a virtuális gépek figyelők legfontosabb operációs rendszer teljesítménymutatókat kapcsolódó processzor, memória, hálózati adapter és lemezhasználat. Teljesítmény egészíti ki a állapotát a figyelési funkció, és tegye elérhetővé a problémákat, amelyek jelzik, hogy egy lehetséges rendszer összetevő hibája, támogatási finomhangolása és optimalizálási hatékonyság elérése érdekében, vagy támogatja a kapacitástervezés segít.

   ![Az Azure teljesítményének monitorozása lap](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

Megjelenítése a detektált alkalmazás-összetevők Windows és Linux rendszerű virtuális gépeken futó Azure Stack figyelhető kétféle módon az Azure Monitor szolgáltatással a virtuális gépek esetében a virtuális gépről közvetlenül, vagy a virtuális gépek az Azure Monitor csoportokhoz.
A [Azure Monitor használatával virtuális gépek (előzetes verzió) alkalmazás-összetevők ismertetése a térkép](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-maps) a cikk segít megérteni a felhasználói élményt a két perspektíva adatai és a térkép funkció használata között.

   ![Az Azure teljesítményének monitorozása lap](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)


## <a name="enable-update-management-using-a-resource-manager-template"></a>Resource Manager-sablon használatával az Update Management engedélyezése
Ha az Azure Stack virtuális gépek nagy számú, használhatja [ezen Azure Resource Manager-sablon](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) könnyebben üzembe helyezheti a megoldást, a virtuális gépeken. A sablon üzembe helyezi a Microsoft Monitoring Agent bővítményt egy meglévő Azure Stack virtuális Gépet, és hozzáadja azt a meglévő Azure LogAnalytics munkaterülethez.
 
## <a name="next-steps"></a>További lépések
[Az SQL Server rendszerű virtuális gép teljesítményének optimalizálásához](azure-stack-sql-server-vm-considerations.md)




