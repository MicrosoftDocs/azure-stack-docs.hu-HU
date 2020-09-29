---
title: Azure Stack HCI figyelése Azure Monitor
description: A kiszolgálók figyelése és a riasztások konfigurálása Azure Monitor a Windows felügyeleti központból.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: f721b16d6742cde5e27fae8b81d8d256c7defa2a
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/21/2020
ms.locfileid: "86866807"
---
# <a name="monitor-azure-stack-hci-with-azure-monitor"></a>Azure Stack HCI figyelése Azure Monitor

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

A [Azure monitor](/azure/azure-monitor/overview) a különböző erőforrások, például a Windows-kiszolgálók és a virtuális gépek (VM-EK) telemetria gyűjti, elemzi és végzi el a helyszíni és a felhőben egyaránt. Bár Azure Monitor lekéri az Azure-beli virtuális gépekről és más Azure-erőforrásokból származó adatok lekérését, ez a cikk azt ismerteti, hogyan működik a Azure Monitor a Azure Stack HCI-on futó helyszíni kiszolgálókkal és virtuális gépekkel, különösen a Windows felügyeleti központtal

## <a name="how-does-azure-monitor-work"></a>Hogyan működik Azure Monitor?
:::image type="content" source="media/monitor/azure-monitor-diagram.png" alt-text="a Azure Monitor működésének diagramja" border="false":::
A helyszíni Windows Server-kiszolgálókról generált adatok gyűjtése Azure Monitor Log Analytics munkaterületen történik. A munkaterületen belül különböző figyelési megoldásokat engedélyezhet – olyan logikai készleteket, amelyek egy adott forgatókönyvhöz biztosítanak betekintést. Az Azure Update Management, Azure Security Center és Azure Monitor for VMs például az összes olyan figyelési megoldás, amely a munkaterületen belül engedélyezhető.

Amikor engedélyez egy figyelési megoldást egy Log Analytics munkaterületen, az adott munkaterületnek jelentő összes kiszolgáló elkezdi a megoldásra vonatkozó adatok gyűjtését, így a megoldás a munkaterület összes kiszolgálója számára elemzési eredményeket hozhat.

A helyszíni kiszolgálón található diagnosztikai adatok gyűjtéséhez és a Log Analytics munkaterületre történő leküldéséhez Azure Monitor a Microsoft monitoring Agent (MMA) telepítését igényli. Bizonyos monitorozási megoldásokhoz másodlagos ügynökre is szükség van. A Azure Monitor for VMs például egy ServiceMap-ügynöktől is függ a megoldás által biztosított további funkciókhoz.

Bizonyos megoldások, például az Azure Update Management is függnek a Azure Automationtól, ami lehetővé teszi az Azure-beli és nem Azure-beli környezetek erőforrásainak központi felügyeletét. Az Azure Update Management például a Azure Automation használatával ütemezhet és hangolja össze a frissítések telepítését a környezetében lévő gépek között, központilag, a Azure Portal.

## <a name="what-data-does-azure-monitor-collect"></a>Milyen adatokat gyűjt az Azure Monitor?

Az Azure Monitor által összegyűjtött összes adat két alapvető típus egyikéhez illeszkedik: metrikák és naplók.

1. A [metrikák](/azure/azure-monitor/platform/data-platform#metrics) olyan numerikus értékek, amelyek egy adott rendszer bizonyos aspektusait írják le egy adott időpontban. Ezek egyszerűek és a közel valós idejű forgatókönyvek támogatására alkalmasak. Azure Monitor közvetlenül gyűjtött adatokat a Azure Portal **Áttekintés** oldalán találja.

    :::image type="content" source="media/monitor/metrics.png" alt-text="a mérőszámok Explorerben betöltött mérőszámok képe" border="false":::

2. A [naplók](/azure/azure-monitor/platform/data-platform#logs) különböző típusú adatokat tárolnak a rekordokban, amelyek mindegyike különböző tulajdonságokkal rendelkezik. A telemetria, például az eseményeket és a nyomkövetési naplókat a teljesítményadatok mellett naplófájlként tárolja a rendszer, így az elemzéshez is egyesíthető. A Azure Monitor által gyűjtött naplózási adatokat elemezheti a [lekérdezésekkel](/azure/azure-monitor/log-query/log-query-overview) , így gyorsan lekérheti, összevonhatja és elemezheti az összegyűjtött adatokat. A Azure Portal [log Analytics](/azure/azure-monitor/log-query/portals) használatával létrehozhat és tesztelheti a lekérdezéseket, majd közvetlenül elemezheti ezeket az eszközöket, vagy a [vizualizációk](/azure/azure-monitor/visualizations) vagy a [riasztási szabályok](/azure/azure-monitor/platform/alerts-overview)használatával mentheti a lekérdezéseket.

    :::image type="content" source="media/monitor/logs.png" alt-text="a log Analytics szolgáltatásban betöltött naplók képe" border="false":::

## <a name="how-does-windows-admin-center-enable-you-to-use-azure-monitor"></a>Hogyan teszi lehetővé a Windows felügyeleti központ a Azure Monitor használatát?

A Windows felügyeleti központban három figyelési megoldást engedélyezhet:

- [Azure Monitor fürtökhöz](#onboard-your-cluster-using-windows-admin-center)
- [Azure Update Management](/windows-server/manage/windows-admin-center/azure/azure-update-management) (a **frissítések** eszközben)
- Azure Monitor for VMs (a kiszolgáló beállításaiban), a. k. a virtuális gép által észlelt

A Azure Monitor használatának megkezdéséhez használja az alábbi eszközök bármelyikét. Ha még soha nem használta Azure Monitor korábban, a Windows felügyeleti központ automatikusan kiépít egy Log Analytics-munkaterületet (és szükség esetén Azure Automation fiókot), majd telepíti és konfigurálja az MMA-t a célkiszolgálón. Ezután telepíti a megfelelő megoldást a munkaterületre.

Ha például először a **frissítések** eszközre lép az Azure Update Management telepítéséhez, a Windows felügyeleti központ a következőket teszi:

1. Telepítse az MMA-t a gépre.
2. Hozza létre a Log Analytics munkaterületet és a Azure Automation fiókot (mert ebben az esetben szükség van egy Azure Automation fiókra).
3. Telepítse a Update Management megoldást az újonnan létrehozott munkaterületen.

Ha egy másik figyelési megoldást szeretne hozzáadni a Windows felügyeleti központban ugyanazon a kiszolgálón, a Windows felügyeleti központ egyszerűen telepíti ezt a megoldást arra a meglévő munkaterületre, amelyhez a kiszolgáló csatlakoztatva van. A Windows felügyeleti központ emellett a többi szükséges ügynököt is telepíti.

Ha egy másik kiszolgálóhoz csatlakozik, de már beállított egy Log Analytics munkaterületet (akár a Windows felügyeleti központban, akár manuálisan a Azure Portalban), akkor az MMA-t is telepítheti a kiszolgálóra, és csatlakozhat egy meglévő munkaterülethez. Amikor egy kiszolgálót munkaterülethez kapcsol, a automatikusan elindítja az adatok gyűjtését és a jelentéskészítést az adott munkaterületen telepített megoldásoknak.

## <a name="azure-monitor-for-virtual-machines-virtual-machine-insights"></a>Azure Monitor virtuális gépekhez (virtuális gépi bepillantást)

Amikor beállítja a Azure Monitor for VMs a **kiszolgáló beállításaiban**, a Windows felügyeleti központ engedélyezi a Azure monitor for VMS megoldást, más néven a virtuális gépi bepillantást. Ez a megoldás lehetővé teszi a kiszolgáló állapotának és eseményeinek figyelését, e-mail-riasztások létrehozását, a kiszolgáló teljesítményének összevont nézetét a környezetében, valamint az adott kiszolgálóhoz kapcsolódó alkalmazások, rendszerek és szolgáltatások megjelenítését.

> [!NOTE]
> A neve ellenére a virtuális gép elemzése fizikai kiszolgálók és virtuális gépek esetében is működik.

A Azure Monitor ingyenes 5 GB adat/hónap/ügyfél támogatásával egyszerűen kipróbálhatja ezt a kiszolgálót vagy kettőt, és nem kell fizetnie. Olvassa el a következő témakört: a kiszolgálók Azure Monitorba való bevezetésének további előnyei, például a rendszerek teljesítményének összevont nézetének beolvasása a környezetben található kiszolgálókon.

## <a name="onboard-your-cluster-using-windows-admin-center"></a>A fürt előkészítése a Windows felügyeleti központtal

A fürt Azure Monitorba való bevezetésének legegyszerűbb módja a Windows felügyeleti központban lévő automatikus munkafolyamat használata, amely a Állapotfigyelő szolgáltatás és a Log Analyticst konfigurálja, majd telepíti az MMA-t.

:::image type="content" source="media/monitor/onboarding.gif" alt-text="rendszerkép a bevezetési fürt Azure Monitor":::

A kiszolgálói kapcsolatok áttekintés lapján kattintson az új gombra a **riasztások kezelése**lehetőségre, vagy lépjen a **kiszolgáló beállításai > figyelés és riasztások**elemre. Ezen az oldalon belül a kiszolgálót a **telepítés és a** Befejezés gombra kattintva Azure monitor. A felügyeleti központ gondoskodik az Azure Log Analytics munkaterület üzembe helyezéséről, a szükséges ügynök telepítéséről, valamint a virtuális gép bepillantást biztosító megoldásának konfigurálásáról. Ha elkészült, a kiszolgáló a teljesítményszámláló adatait Azure Monitorba küldi, így a kiszolgáló alapján megtekintheti és létrehozhatja az e-mail-riasztásokat a Azure Portal.

## <a name="onboard-your-cluster-manually-using-powershell"></a>A fürt manuális előkészítése a PowerShell használatával

Ha manuálisan szeretné előkészíteni a fürtöt, kövesse az alábbi lépéseket.

### <a name="configure-health-service"></a>Állapotfigyelő szolgáltatás konfigurálása

Az első teendő, hogy konfigurálja a fürtöt. Amint tudja, a [állapotfigyelő szolgáltatás](/windows-server/failover-clustering/health-service-overview) javítja a közvetlen tárolóhelyekt futtató fürtök napi szintű figyelését és működési élményét.

Ahogy láttuk, Azure Monitor gyűjti a naplókat az egyes csomópontokról, amelyek a fürtön futnak. Ezért a Állapotfigyelő szolgáltatást úgy kell konfigurálni, hogy az esemény-csatornára írjon, ami a következő:

```
Event Channel: Microsoft-Windows-Health/Operational
Event ID: 8465
```

A Állapotfigyelő szolgáltatás konfigurálásához futtassa a következőt:

```PowerShell
get-storagesubsystem clus* | Set-StorageHealthSetting -Name "Platform.ETW.MasTypes" -Value "Microsoft.Health.EntityType.Subsystem,Microsoft.Health.EntityType.Server,Microsoft.Health.EntityType.PhysicalDisk,Microsoft.Health.EntityType.StoragePool,Microsoft.Health.EntityType.Volume,Microsoft.Health.EntityType.Cluster"
```

A fenti parancsmag futtatásával megtudhatja, hogy az állapot beállítás megkezdje az események írását a *Microsoft-Windows-Health/Operational* Event Channel szolgáltatásba.

### <a name="configure-log-analytics"></a>A Log Analytics konfigurálása

Most, hogy beállította a megfelelő naplózást a fürtön, a következő lépés a Log Analytics megfelelő konfigurálása.

Az [Azure log Analytics](/azure/azure-monitor/platform/agent-windows) áttekintheti az adatokat közvetlenül az adatközpontban vagy más felhőalapú környezetben lévő fizikai vagy virtuális Windows rendszerű számítógépekről egyetlen tárházba, részletes elemzés és korreláció céljából.

A támogatott konfiguráció megismeréséhez tekintse meg a [támogatott Windows operációs rendszereket](/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) és a [hálózati tűzfalkonfigurációkat](/azure/azure-monitor/platform/log-analytics-agent#network-firewall-requirements) ismertető részt.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

#### <a name="log-in-to-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be a Azure Portalba a következő címen: [https://portal.azure.com](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

#### <a name="create-a-workspace"></a>Munkaterület létrehozása

Az alább felsorolt lépésekről az [Azure monitor dokumentációjában](/azure/azure-monitor/learn/quick-collect-windows-computer)talál további információt.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.

    :::image type="content" source="media/monitor/azure-portal-01.png" alt-text="Azure Portal":::

2. Kattintson a **Létrehozás** parancsra, majd válassza ki a következő elemek beállításait:

   * Adja meg az új **log Analytics munkaterület**nevét, például *DefaultLAWorkspace*.
   * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
   * Az **Erőforráscsoport** beállításnál válasszon ki egy meglévő erőforráscsoportot, amely egy vagy több Azure-beli virtuális gépet tartalmaz.

    :::image type="content" source="media/monitor/create-loganalytics-workspace-02.png" alt-text="Log Analytics-erőforrás létrehozása panel":::

3. Miután a **Log Analytics-munkaterület** panelen megadta a szükséges adatokat, kattintson az **OK** gombra.

Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet.

#### <a name="obtain-workspace-id-and-key"></a>A munkaterület-azonosító és -kulcs lekérése
Az MMA for Windows telepítéséhez a munkaterület AZONOSÍTÓját és kulcsát kell megadnia a Log Analytics munkaterülethez.  Ezekre az adatokra a telepítővarázslónak lesz szüksége az ügynök konfigurálásához és a Log Analyticsszel való sikeres kommunikációjának biztosításához.

1. Az Azure Portal bal felső sarkában kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. A Log Analytics-munkaterületek listájában válassza ki a korábban létrehozott *DefaultLAWorkspace* elemet.
3. Válassza ki a **Speciális beállítások** elemet.
    :::image type="content" source="media/monitor/log-analytics-advanced-settings-01.png" alt-text="Log Analytics speciális beállításai":::
4. Válassza ki a **Csatlakoztatott források**, majd a **Windowsos kiszolgálók** elemet.
5. A **Munkaterület-azonosító** és az **Elsődleges kulcs** jobb oldalán lévő érték. Mentse ideiglenesen a másolást és beillesztést is a kedvenc szerkesztőjébe az idő alatt.

### <a name="installing-the-mma-on-windows"></a>Az MMA telepítése Windows rendszeren
A Microsoft monitoring Agent telepítése és konfigurálása a következő lépésekkel végezhető el.

> [!IMPORTANT]
> Ügyeljen arra, hogy az ügynököt a fürt minden kiszolgálójára telepítse, és jelezze, hogy az ügynököt a Windows indításakor szeretné futtatni.

1. A **Windowsos kiszolgálók** lapon válassza ki a Windows operációs rendszer processzorarchitektúrájának megfelelő verziójú **Windows-ügynök letöltése** lehetőséget.
2. Futtassa a telepítőt, és telepítse az ügynököt a számítógépre.
3. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.
4. A **Licencfeltételek** oldalon olvassa el és fogadja el a licencet, majd kattintson az **Elfogadom** gombra.
5. A **Célmappa** lapon fogadja el az alapértelmezett telepítési mappát, vagy adjon meg egy másikat, majd kattintson a **Tovább** gombra.
6. **Az ügynök telepítésének beállításai** lapon csatlakoztassa az ügynököt az Azure Log Analyticshez, majd kattintson a **Tovább** gombra.
7. Az **Azure log Analytics** lapon illessze be a korábban átmásolt **munkaterület-azonosítót** és a **munkaterület kulcsát (elsődleges kulcs)** . Ha a számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, kattintson a **Speciális** gombra, majd adja meg a proxykiszolgáló URL-címét és portszámát. Ha a proxykiszolgáló hitelesítést igényel, írja be a felhasználónevet és jelszót a proxykiszolgálóval való hitelesítéshez, majd kattintson a **Tovább** gombra.
8. A szükséges konfigurációs beállítások megadása után kattintson a **Tovább** gombra.
    :::image type="content" source="media/monitor/log-analytics-mma-setup-laworkspace.png" alt-text="illessze be a Munkaterület-azonosítót és az Elsődleges kulcsot":::
9. A **Telepítésre kész** oldalon ellenőrizze a beállításokat, majd kattintson a **Telepítés** elemre.
10. **A konfigurálás sikeresen befejeződött** lapon kattintson a **Befejezés** gombra.

Ennek végeztével a **Microsoft Monitoring Agent** megjelenik a **Vezérlőpulton**. Áttekintheti a konfigurációt, és ellenőrizheti, hogy az ügynök csatlakozik-e a Log Analyticshez. Ha csatlakozik, az **Azure Log Analytics** lapon az ügynök megjeleníti a következő üzenetet: **A Microsoft Monitoring Agent sikeresen csatlakozott a Microsoft Log Analytics szolgáltatáshoz**.

:::image type="content" source="media/monitor/log-analytics-mma-laworkspace-status.png" alt-text="MMA kapcsolati állapota a Log Analytics felé":::

A támogatott konfiguráció megismeréséhez tekintse meg a [támogatott Windows operációs rendszereket](/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) és a [hálózati tűzfalkonfigurációkat](/azure/azure-monitor/platform/log-analytics-agent#network-firewall-requirements) ismertető részt.

## <a name="setting-up-alerts-using-windows-admin-center"></a>Riasztások beállítása a Windows felügyeleti központtal

Miután csatlakoztatta a kiszolgálót a Azure Monitorhoz, a **beállítások > figyelés és riasztások** lapon található intelligens hiperhivatkozások segítségével navigáljon a Azure Portal. A Windows felügyeleti központban egyszerűen konfigurálhatja az alapértelmezett riasztásokat, amelyek a Log Analytics munkaterület összes kiszolgálójára érvényesek lesznek. A Windows felügyeleti központ automatikusan lehetővé teszi a teljesítményszámlálók összegyűjtését, így [létrehozhat egy új riasztást](/azure/azure-monitor/platform/alerts-log) , ha testreszabja az előre definiált lekérdezések egyikét, vagy megírhatja a sajátját.

:::image type="content" source="media/monitor/setup1.gif" alt-text="Riasztások konfigurálása képernyőfelvétel":::

Ezek a riasztások és azok alapértelmezett feltételei, amelyeket a következőkre használhat:

| Riasztás neve                | Alapértelmezett feltétel                                  |
|---------------------------|----------------------------------------------------|
| Processzorhasználat           | 10 percnél több mint 85%                            |
| Lemezhasználat kihasználtsága | 10 percnél több mint 85%                            |
| Memória kihasználtsága        | 100 MB-nál kisebb rendelkezésre álló memória 10 percen belül   |
| Szívverés                 | 5 percnél kevesebb mint 2 ütés                   |
| Kritikus hiba a rendszeren     | Bármely kritikus riasztás a fürtrendszer eseménynaplójában |
| Állapotfigyelő szolgáltatás riasztása      | Bármely állapotfigyelő szolgáltatás hibája a fürtön            |

Miután konfigurálta a riasztásokat a Windows felügyeleti központban, megtekintheti a riasztásokat a Log Analytics munkaterületen az Azure-ban.

:::image type="content" source="media/monitor/setup2.gif" alt-text="Riasztások megtekintése képernyőkép":::

### <a name="collecting-event-and-performance-data"></a>Esemény-és teljesítményadatok gyűjtése

A Log Analytics képes események gyűjtésére a Windows eseménynaplóból, illetve a hosszabb távú elemzésekhez és jelentéskészítéshez megadott teljesítményszámlálókból, és valamilyen művelettel reagálni arra, ha észleli egy adott feltétel meglétét. A következő lépésekkel konfigurálhatja az események gyűjtését a Windows eseménynaplóból, illetve (kezdetnek) egyes gyakran használt teljesítményszámlálókból.

1. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. Válassza ki a **Speciális beállítások** elemet.
    :::image type="content" source="media/monitor/log-analytics-advanced-settings-01.png" alt-text="Log Analytics speciális beállítások":::
3. Válassza az **Adatok**, majd a **Windows Eseménynaplók** lehetőséget.
4. Itt adja hozzá a Állapotfigyelő szolgáltatás Event csatornát az alábbi név beírásával, majd kattintson a plusz jelre **+** .
   ```
   Event Channel: Microsoft-Windows-Health/Operational
   ```
5. A táblázatban jelölje be a **Hiba** és **Figyelmeztetés** súlyossági szintet.
6. A konfiguráció mentéséhez kattintson az oldal tetején található **Mentés** parancsra.
7. Válassza a **Windows-teljesítményszámlálók** lehetőséget a teljesítményszámlálók Windows-számítógépen való gyűjtésének engedélyezéséhez.
8. Amikor először konfigurálja egy új Log Analytics-munkaterület Windows-teljesítményszámlálóit, akkor gyorsan létrehozhat több gyakran használt számlálót. Ezek mindegyike mellett egy jelölőnégyzet található.
    :::image type="content" source="media/monitor/windows-perfcounters-default.png" alt-text="[Alapértelmezett Windows-teljesítményszámlálók kiválasztva":::
    Kattintson **A kijelölt teljesítményszámlálók felvétele** elemre.  A rendszer hozzáadja a kiválasztott számlálókat, és mindegyikhez beállít egy tíz másodperces mintagyűjtési időszakot.
9. A konfiguráció mentéséhez kattintson az oldal tetején található **Mentés** parancsra.

## <a name="create-queries-and-alerts-based-on-log-data"></a>Lekérdezések és riasztások létrehozása a naplófájlok alapján

Ha eddig ezt megtette, a fürtnek a naplókat és a teljesítményszámlálók Log Analytics kell küldenie. A következő lépés olyan riasztási szabályok létrehozása, amelyek rendszeres időközönként automatikusan futtatják a naplóbeli kereséseket. Ha a napló keresési eredményei megfelelnek az adott feltételeknek, a rendszer riasztást küld, amely e-mailben vagy SMS-ben értesíti Önt. Vizsgáljuk meg az alábbi lépéseket.

### <a name="create-a-query"></a>Lekérdezés létrehozása

Először nyissa meg a **napló keresési** portálját.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Monitorozás** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Monitorozás** lehetőséget.
2. A navigáció **figyelése** menüben válassza a **log Analytics** lehetőséget, majd válasszon ki egy munkaterületet.

A feldolgozható adatok lekérdezésének leggyorsabb módja az egyszerű lekérdezés, amely egy tábla összes rekordját visszaadja. Írja be a következő lekérdezéseket a keresőmezőbe, és kattintson a Keresés gombra.

```
Event
```

A rendszer az alapértelmezett listanézetben adja vissza az adatokat, és megjeleníti, hogy összesen hány rekord lett visszaadva.

:::image type="content" source="media/monitor/log-analytics-portal-eventlist-01.png" alt-text="Egyszerű lekérdezés képernyőképe":::

A képernyő bal oldalán található a szűrő ablaktábla, amellyel szűrőt adhat a lekérdezéshez anélkül, hogy közvetlenül módosítaná azt.  Egy adott rekordtípushoz több rekordtulajdonság is megjelenik, és egy vagy több tulajdonságértéket is kiválaszthat a keresési eredmények szűkítése érdekében.

Jelölje be a **hiba** melletti jelölőnégyzetet a **EVENTLEVELNAME** alatt, vagy írja be a következőt, hogy az eredményeket a hiba eseményeire korlátozza.

```
Event | where (EventLevelName == "Error")
```

:::image type="content" source="media/monitor/log-analytics-portal-eventlist-02.png" alt-text="Képernyőfelvétel szűrése":::

Miután elvégezte a megfelelő lekérdezéseket a fontos eseményekről, mentse azokat a következő lépéshez.

### <a name="create-alerts"></a>Riasztások létrehozása
Most tekintsük át a riasztások létrehozásának példáját.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. Új riasztás létrehozásához a bal oldali panelen válassza a **Riasztások** elemet, majd kattintson az oldal tetején található **Új riasztási szabály** elemre.
    :::image type="content" source="media/monitor/alert-rule-02.png" alt-text="Új riasztási szabály létrehozása Képernyőkép":::
3. Az első lépés a **riasztás létrehozása** szakaszban válassza ki a log Analytics munkaterületet erőforrásként, mert ez egy log-alapú riasztási jel.  Az eredmények szűréséhez válassza ki az adott **előfizetést** a legördülő listából, ha egynél több van, amely a korábban létrehozott log Analytics munkaterületet tartalmazza.  Szűrje az **erőforrástípusokat** a **Log Analytics** a legördülő menüből való kiválasztásával.  Végül válassza az **Erőforrás** **DefaultLAWorkspace** elemet, majd kattintson a **Kész** gombra.
    :::image type="content" source="media/monitor/alert-rule-03.png" alt-text="Új riasztási szabály létrehozása – 1. lépés képernyőkép":::
4. A **riasztási feltételek**szakaszban kattintson a **feltétel hozzáadása** elemre a mentett lekérdezés kiválasztásához, majd adja meg azt a logikát, amelyet a riasztási szabály követ.
5. Konfigurálja a riasztást az alábbi információkkal: a. A legördülő lista **alapján** válassza a **metrika mértékét**.  A metrikamérés egy riasztást hoz létre a lekérdezés minden egyes objektumához, amelynek értéke meghaladja a megadott küszöbértéket.
   b. A **feltétel**esetében válassza a **nagyobb, mint** lehetőséget, és adjon meg egy küszöbértéket.
   c. Ezután adja meg a riasztás aktiválásának idejét. Például kiválaszthatja az **egymást követő szabálysértéseket** , és a legördülő listából válassza a 3-as értéknél **nagyobb** értéket.
   d. A kiértékelés alatt szakasz alapján módosítsa az **időszak** értékét **30** percre, a **gyakoriságot** pedig 5-re. A szabály öt percenként fog futni, és visszaadja azokat a rekordokat, amelyek az aktuális időhöz képest harminc percen belül jöttek létre.  Ha az időtartamot szélesebb időközre állítja, figyelembe veheti az esetleges adatkésést, és biztosíthatja, hogy a lekérdezés adatokat ad vissza, így nem kap téves negatív választ, ha a figyelmeztetés soha nem aktiválódik.
6. Kattintson a **Kész** gombra a riasztási szabály létrehozásának befejezéséhez.
    :::image type="content" source="media/monitor/alert-signal-logic-02.png" alt-text="Riasztási jel beállítása képernyőfelvétel":::
7. Most lépjen a második lépésre, adja meg a riasztás nevét a **riasztási szabály neve** mezőben, például a **riasztást az összes hiba eseménynél**.  Adjon meg egy **Leírást**, ahol részletezi a riasztás tulajdonságait, majd a **Súlyosság** értéknél a megadott lehetőségek közül válassza a **Kritikus (0. súlyosság)** lehetőséget.
8. Ha a létrehozásakor azonnal aktiválni kívánja a riasztási szabályt, fogadja el a **Szabály engedélyezése a létrehozásakor** alapértelmezett értékét.
9. A harmadik és egyben végső lépésben adjon meg egy **műveletcsoportot**, amely biztosítja, hogy minden alkalommal, amikor egy riasztás aktiválódik ugyanazok a műveletek lesznek végrehajtva, és amely mindegyik meghatározott szabályra használható. Hozzon létre egy új műveletcsoportot a következő információkkal: a. Válassza az **Új műveletcsoport** lehetőséget, és megnyílik a **Műveletcsoport hozzáadása** panel.
   b. A **Műveletcsoport neve** részben adjon meg egy nevet, például **IT Operations - Notify** és egy **Rövid nevet** például **itops-n**.
   c. Ellenőrizze, hogy az alapértelmezett értékek helyesek-e az **Előfizetés** és az **Erőforráscsoport** mezőben. Ha nem, válassza ki a megfelelőt a legördülő listából.
   d. A Műveletek részben adja meg a művelet nevét, például **E-mail küldése**, és a **Művelettípus** alatti legördülő menüben válassza az **E-mail/SMS/Leküldéses/Hang** lehetőséget. A jobb oldalon megnyílik az **E-mail/SMS/Leküldéses/Hang** tulajdonságait tartalmazó panel, amely további információkat biztosít.
   e. Az **e-mail/SMS/leküldés/hang** ablaktáblán válassza ki és állítsa be a kívánt beállítást. Engedélyezze például az **e-mailek** küldését, és adjon meg egy érvényes e-mail SMTP-címet az üzenet kézbesítéséhez.
   f. Kattintson az **OK** gombra a módosítások mentéséhez.<br><br>

    :::image type="content" source="media/monitor/action-group-properties-01.png" alt-text="Új műveleti csoport létrehozása – képernyőfelvétel":::

10. A műveletcsoport elkészítéséhez kattintson az **OK** gombra.
11. Kattintson a **Riasztási szabály létrehozás** gombra a riasztási szabály létrehozásának befejezéséhez. Azonnal el fog indulni.
    :::image type="content" source="media/monitor/alert-rule-01.png" alt-text="Új riasztási szabály létrehozásának befejezése képernyőkép":::

### <a name="example-alert"></a>Példa riasztásra

Ez a példa az Azure-beli riasztásokra hasonlít.

:::image type="content" source="media/monitor/alert.gif" alt-text="Azure-riasztás képernyőképe":::

Alább látható egy példa arra az e-mailre, amelyet a Azure Monitor fog elküldeni:

:::image type="content" source="media/monitor/warning.png" alt-text="Értesítő e-mail – példa képernyőképre":::

## <a name="create-custom-kusto-queries-in-log-analytics"></a>Egyéni Kusto-lekérdezések létrehozása a Log Analyticsban

Azure Monitor a Kusto lekérdezési nyelv használatával is [írhat egyéni naplók lekérdezéseit](/azure/azure-monitor/log-query/get-started-queries) egy vagy több virtuális gépről származó adatok gyűjtéséhez.

## <a name="get-a-consolidated-view-across-multiple-servers"></a>Összevont nézet beolvasása több kiszolgáló között

Ha több kiszolgálót is bevezet egyetlen Log Analytics munkaterületre a Azure Monitoron belül, a Azure Monitoron belül az [Virtual Machines](/azure/azure-monitor/insights/vminsights-overview) -betekintő megoldásból összevont nézeteket kaphat az összes kiszolgálóról. (Vegye figyelembe, hogy a Azure Monitor Virtual Machines elemzése csak a teljesítmény-és Maps-lapokon működik a helyszíni kiszolgálókkal – a Health (állapot) lapon csak Azure-beli virtuális gépek használhatók.) Ha meg szeretné tekinteni ezt a Azure Portalban, lépjen a **Azure Monitor > Virtual Machines** (az áttekintések alatt), és navigáljon a **teljesítmény** vagy a **térképek** lapra.

## <a name="visualize-connected-services"></a>Csatlakoztatott szolgáltatások megjelenítése

Ha a Windows felügyeleti központ a Azure Monitoron belüli virtuálisgép-elemzési megoldásba beépíti a kiszolgálót, akkor egy [Service Map](/azure/azure-monitor/insights/service-map)nevű képességet is kigyullad. Ez a funkció automatikusan feltérképezi az alkalmazás-összetevőket, és leképezi a szolgáltatások közötti kommunikációt, így egyszerűen megjelenítheti a kapcsolatokat a kiszolgálók között, amelyek részletesen ismertetik a Azure Portal. Ezt úgy érheti el, ha a Azure Portalra kattint, majd kiválasztja **Azure Monitor > Virtual Machines** (az észlelések alatt), majd a **Maps (Térkép** ) lapra navigál.

> [!NOTE]
> A Azure Monitor virtuális gépekkel kapcsolatos bepillantást a jelenleg hat nyilvános régióban kínáljuk.  A legfrissebb információkért olvassa el a [Azure monitor for VMS dokumentációját](/azure/azure-monitor/insights/vminsights-onboard#log-analytics). Az Log Analytics munkaterületet az egyik támogatott régióban kell telepítenie, hogy megkapja a fent ismertetett virtuálisgép-megállapítási megoldás által biztosított további előnyöket.

## <a name="disabling-monitoring"></a>Figyelés letiltása

Ha teljes mértékben le szeretné bontani a kiszolgálót a Log Analytics munkaterületről, távolítsa el az MMA-t. Ez azt jelenti, hogy ez a kiszolgáló többé nem küld adatokat a munkaterületre, és az abban a munkaterületen telepített összes megoldás már nem gyűjti és nem dolgozza fel az adott kiszolgáló adatait. Ez azonban nem érinti a munkaterületet; a munkaterületnek jelentett összes erőforrás továbbra is így lesz. Ha el szeretné távolítani az MMA-ügynököt a Windows felügyeleti központban, kapcsolódjon a kiszolgálóhoz, és lépjen a **telepített alkalmazások**elemre, keresse meg a Microsoft monitoring Agent ügynököt, majd válassza az **Eltávolítás**lehetőséget.

Ha egy adott megoldást szeretne kikapcsolni egy munkaterületen belül, akkor el kell [távolítania a figyelési megoldást a Azure Portal](/azure/azure-monitor/insights/solutions#remove-a-management-solution). A figyelési megoldás eltávolítása azt jelenti, hogy az adott megoldás által létrehozott adatvizsgálatok többé nem jönnek _létre az adott_ munkaterületre jelentett kiszolgálókon. Ha például eltávolítja a Azure Monitor for VMs megoldást, többé nem jelenik meg a virtuális gép vagy a kiszolgáló teljesítményére vonatkozó információk a munkaterülethez csatlakoztatott bármely számítógépről.

## <a name="next-steps"></a>Következő lépések

A kapcsolódó témaköröket lásd még:

- [További információ az Azure-integrációról a Windows felügyeleti központtal](/windows-server/manage/windows-admin-center/azure/)
- [Azure Monitor használata az e-mailek küldéséhez Állapotfigyelő szolgáltatás hibákhoz](/windows-server/storage/storage-spaces/configure-azure-monitor)
