---
title: Azure Stack HCI-fürtök figyelése
description: Azure Stack HCI-fürtök,-kiszolgálók, virtuális gépek, meghajtók és kötetek figyelése a Windows felügyeleti központtal és a PowerShell-lel.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 08/12/2020
ms.openlocfilehash: 9ce1dc258243e9e17458c1f70e5a852a0b56996a
ms.sourcegitcommit: a3e042c782a38ecf3baf7a64b1d492a655972f9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202004"
---
# <a name="monitor-azure-stack-hci-clusters"></a>Azure Stack HCI-fürtök figyelése

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Az Azure Stack HCI-fürtök és a mögöttes összetevők figyelésének három módja van: Windows felügyeleti központ, [Azure monitor](azure-monitor.md)és PowerShell.

## <a name="monitor-using-windows-admin-center-dashboard"></a>Figyelés a Windows felügyeleti központ irányítópultjának használatával

[Telepítse a Windows felügyeleti központot](/windows-server/manage/windows-admin-center/deploy/install) egy felügyeleti számítógépre vagy kiszolgálóra, majd vegye fel és kapcsolódjon a figyelni kívánt Azure stack HCI-fürthöz. A kritikus riasztások kiemelten a Windows felügyeleti központ irányítópultjának tetején jelennek meg, amint bejelentkeznek. Az alábbi képernyőkép például azt jelzi, hogy a frissítéseket telepíteni kell, és a fürtnek egy kritikus meghajtóval kapcsolatos hibája van:

:::image type="content" source="media/monitor-cluster/dashboard-alert.png" alt-text="Windows felügyeleti központ irányítópult-riasztások – példa":::

## <a name="monitor-virtual-machines"></a>Virtuális gépek figyelése

Fontos megérteni azon virtuális gépek (VM-EK) állapotát, amelyeken az alkalmazások és az adatbázisok futnak. Ha a virtuális gép nem rendelkezik elegendő PROCESSZORral vagy memóriával a rajta futó munkaterhelésekhez, a teljesítmény lelassulhat, vagy az alkalmazás elérhetetlenné válhat. Ha egy virtuális gép legfeljebb három szívverésre válaszol, öt percen belül vagy annál hosszabb ideig, előfordulhat, hogy probléma merült fel.

Ha a **virtuális gépeket** a Windows felügyeleti központban szeretné figyelni, kattintson a bal oldali **eszközök** menüjében a virtuális gépek elemre. A fürtön futó virtuális gépek teljes leltárának megtekintéséhez kattintson az oldal tetején található **leltár** elemre. Megjelenik egy táblázat, amely az egyes virtuális gépekkel kapcsolatos információkat tartalmazza, beleértve a következőket:

- **Név:** A virtuális gép neve.
- **Állapot:** Azt jelzi, hogy a virtuális gép fut-e vagy le van állítva.
- **Gazdagép kiszolgálója:** Azt jelzi, hogy a fürt melyik kiszolgálóján fut a virtuális gép.
- **CPU-használat:** A fürt teljes CPU-erőforrásainak százalékos aránya, amelyet a virtuális gép használ.
- **Memória nyomása:** A virtuális gép által igénybe vehető rendelkezésre álló memória-erőforrások százalékos aránya.
- **Memória igénye:** A virtuális gép által használt hozzárendelt memória (GB vagy MB) mennyisége.
- **Hozzárendelt memória:** A virtuális géphez hozzárendelt memória teljes mennyisége.
- **Üzemidő:** A virtuális gép futási ideje napokban: óra: perc: másodperc.
- **Szívverés:** Azt jelzi, hogy a fürt tud-e kommunikálni a virtuális géppel.
- Vész- **helyreállítási állapot:** Azt mutatja, hogy a virtuális gép be van-e jelentkezve Azure Site Recoveryba.

## <a name="monitor-servers"></a>Kiszolgálók figyelése

Azure Stack HCI-fürtöt tartalmazó gazdagép-kiszolgálókat közvetlenül a Windows felügyeleti központból figyelheti. Ha a gazdagép-kiszolgálók nem rendelkeznek elegendő CPU-val vagy memóriával, hogy az erőforrások számára szükség van a virtuális gépekre, a teljesítmény szűk keresztmetszetet jelenthet. 

A kiszolgálók Windows felügyeleti központban való figyeléséhez kattintson a bal oldali **eszközök** menüjének **kiszolgálók** elemére. A fürtben található kiszolgálók teljes leltározásának megtekintéséhez kattintson a lap tetején található **leltár** elemre. Ekkor megjelenik egy tábla, amely az egyes kiszolgálókra vonatkozó információkat tartalmazza, beleértve a következőket:

- **Név:** A fürtben lévő gazda kiszolgáló neve.
- **Állapot:** Azt jelzi, hogy a kiszolgáló fel vagy le van-e állítva.
- **Üzemidő:** Mennyi ideig tart a kiszolgáló.
- **Gyártó:** A kiszolgáló hardveres gyártója.
- **Modell:** A kiszolgáló modellje.
- **Sorozatszám:** A kiszolgáló sorozatszáma.
- **CPU-használat:** A gazda kiszolgáló CPU-kihasználtságának százalékos aránya. A fürtben lévő kiszolgálók esetében a processzor több mint 85 százalékát kell használnia 10 percnél hosszabb ideig. 
- **Memóriahasználat:** A gazdagép-kiszolgáló által használt memória százalékos aránya. Ha egy kiszolgáló 10 percen belül kevesebb, mint 100 MB memóriát tartalmaz, vegye figyelembe a memóriát.

## <a name="monitor-volumes"></a>Kötetek figyelése

A tárolási kötetek gyorsan kitölthetők, ezért fontos, hogy az alkalmazás hatásának elkerülése érdekében rendszeresen figyelje őket. A Windows felügyeleti központban található kötetek figyeléséhez kattintson a bal oldali **eszközök** menüjének **kötetek** elemére. A fürtön lévő tárolási kötetek teljes leltárának megtekintéséhez kattintson az oldal tetején található **leltár** elemre. Ekkor megjelenik egy táblázat, amely az egyes kötetekkel kapcsolatos információkat tartalmazza, beleértve a következőket:

- **Név:** A kötet neve.
- **Állapot:** Az "OK" érték azt jelzi, hogy a kötet kifogástalan állapotú; Ellenkező esetben a rendszer figyelmeztetést vagy hibát jelez.
- **Fájlrendszer:** A kötet fájlrendszere (ReFS, CSVFS).
- **Rugalmasság:** Azt jelzi, hogy a kötet kétirányú tükrözött-e, háromutas tükrözéssel vagy tükrözött felgyorsított paritással.
- **Méret:** A kötet mérete (TB/GB)
- **Tárolási készlet:** A kötethez tartozó tárolási készlet.
- **Tárterület-használat:** A kötet tárolási kapacitásának százalékos aránya.
- **IOPS:** A bemeneti/kimeneti műveletek másodpercenkénti száma

## <a name="monitor-drives"></a>Meghajtók figyelése

Azure Stack HCI olyan módon virtualizálja a tárolót, hogy az egyes meghajtók elvesztése nem befolyásolja jelentősen a fürtöt. A sikertelen meghajtókat azonban le kell [cserélni](replace-drives.md), és a meghajtók a késés kitöltésével vagy bevezetésével befolyásolhatják a teljesítményt. Ha az operációs rendszer nem tud kommunikálni a meghajtóval, lehet, hogy a meghajtó megszakad vagy le van választva, lehet, hogy az összekötője meghiúsult, vagy maga a meghajtó meghiúsult. A Windows 15 perces elveszett kommunikáció után automatikusan újragumiabroncsokat hajt végre. 

A Windows felügyeleti központban található meghajtók figyeléséhez kattintson a bal oldali **eszközök** menüjének **meghajtók** elemére. A fürtön található meghajtók teljes leltárának megtekintéséhez kattintson a lap tetején található **leltár** elemre. Ekkor megjelenik egy táblázat az egyes meghajtókról, beleértve az alábbiakat:

- **Sorozatszám:** A meghajtó sorozatszáma.
- **Állapot:** Az "OK" érték azt jelzi, hogy a meghajtó kifogástalan állapotban van. Ellenkező esetben a rendszer figyelmeztetést vagy hibát jelez.
- **Modell:** A meghajtó modellje.
- **Méret:** A meghajtó teljes kapacitása (TB/GB).
- **Írja be a következőt:** Meghajtó típusa (SSD, HDD).
- **A következőhöz használatos:** Azt jelzi, hogy a meghajtó a gyorsítótárhoz vagy a kapacitáshoz van-e használva.
- **Hely:** Az a tárolási adapter és port, amelyhez a meghajtó csatlakozik.
- **Kiszolgáló:** Annak a kiszolgálónak a neve, amelyhez a meghajtó csatlakozik.
- **Tárolási készlet:** Az a tárolási készlet, amelyhez a meghajtó tartozik.
- **Tárterület-használat:** A meghajtó tárolási kapacitásának százalékos értéke.

## <a name="add-performance-counters"></a>Teljesítményszámlálók hozzáadása

A Windows felügyeleti központban a Teljesítményfigyelő eszköz segítségével valós időben tekintheti meg és hasonlíthatja össze a Windows, az alkalmazások és az eszközök teljesítményszámlálói számlálóit.

1. Válassza a **Teljesítményfigyelő** elemet a bal oldali **eszközök** menüből.
1. Az **üres** munkaterület lehetőségre kattintva új munkaterületet indíthat el, vagy **visszaállíthatja az előzőt** egy korábbi munkaterület visszaállításához.
1. Ha új munkaterületet hoz létre, kattintson a **Számláló hozzáadása** gombra, és válasszon ki egy vagy több figyelni kívánt kiszolgálót, vagy válassza ki a teljes fürtöt.
1. Válassza ki a figyelni kívánt objektumot és példányt, valamint a számláló és a gráf típusát a dinamikus teljesítményadatok megtekintéséhez.
1. Mentse a munkaterületet a **> Mentés másként lehetőség** kiválasztásával a felső menüben.
 
Az alábbi képernyőképen például egy "memóriahasználat" nevű teljesítményszámláló látható, amely a memóriával kapcsolatos információkat jeleníti meg egy két csomópontos fürtben.

:::image type="content" source="media/monitor-cluster/performance-monitor.png" alt-text="Valós idejű teljesítményszámláló – példa a Windows felügyeleti központban":::

## <a name="query-and-process-performance-history-with-powershell"></a>A teljesítmény előzményeinek lekérdezése és feldolgozása a PowerShell-lel

Azure Stack HCI-fürtöket olyan PowerShell-parancsmagok használatával is figyelheti, amelyek a fürtre és annak összetevőire vonatkozó információkat adnak vissza. Tekintse [meg a közvetlen tárolóhelyek teljesítményének előzményeit](/windows-server/storage/storage-spaces/performance-history).

## <a name="use-the-health-service-feature"></a>A Állapotfigyelő szolgáltatás funkció használata

Meg kell vizsgálni a fürtön Állapotfigyelő szolgáltatás összes hibáját. A jelentések futtatásának és a hibák azonosításának megismeréséhez tekintse meg [állapotfigyelő szolgáltatás a Windows Server](/windows-server/failover-clustering/health-service-overview) rendszerben című témakört.

## <a name="troubleshoot-health-and-operational-states"></a>Állapot-és működési állapotok megoldása

A tárolási készletek, virtuális lemezek és meghajtók állapotának és működési állapotának megismeréséhez tekintse meg a [tárolóhelyek és a közvetlen tárolóhelyek állapotának és működési állapotának hibakeresését](/windows-server/storage/storage-spaces/storage-spaces-states)ismertető témakört.

## <a name="monitor-performance-using-storage-qos"></a>Teljesítmény figyelése a tárolási QoS használatával

A tárolási szolgáltatásminőség (QoS) segítségével központilag figyelheti és kezelheti a virtuális gépek tárolási I/O-szolgáltatásait a zajos szomszédos problémák enyhítése és a konzisztens teljesítmény biztosítása érdekében. Lásd: [tárolási szolgáltatásminőség](/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="set-up-alerts-in-azure-monitor"></a>Riasztások beállítása Azure Monitorban

Azure Stack a HCI integrálható Azure Monitorekkel, így a felhasználók [riasztásokat állíthatnak be](azure-monitor.md#setting-up-alerts-using-windows-admin-center) , és értesítést kaphatnak arról, ha a CPU, a lemez kapacitása és a memória kihasználtsági küszöbértéke túllépve, ha a rendszer nem ad vissza virtuális szívverést, vagy ha a rendszerkritikus hiba vagy az állapotfigyelő szolgáltatás hibája van.

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Ismerkedés a Azure Stack HCI és a Windows felügyeleti központ használatába](../get-started.md)
- [Azure Stack HCI figyelése Azure Monitor](azure-monitor.md)