---
title: Ismerkedés a Azure Stack HCI és a Windows felügyeleti központ használatába
description: Gyorsan csatlakozhat egy meglévő Azure Stack HCI-fürthöz, és a Windows felügyeleti központ segítségével figyelheti a fürt és a tároló teljesítményét.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/10/2020
ms.openlocfilehash: 3ae8cf313b0b89813bb32efb9fbb45d0fbf05c91
ms.sourcegitcommit: afdae61022037b5dba8345cb264049897e0aca8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97051723"
---
# <a name="get-started-with-azure-stack-hci-and-windows-admin-center"></a>Ismerkedés a Azure Stack HCI és a Windows felügyeleti központ használatába

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Ez a témakör útmutatást nyújt egy Azure Stack HCI-fürthöz való csatlakozáshoz, valamint a fürt és a tároló teljesítményének figyeléséhez. Ha még nem állított be fürtöt, [töltse le Azure stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -t, és tekintse meg a rövid útmutató [: Azure stack HCI-fürt létrehozása és az Azure-ban való regisztrálása](deploy/deployment-quickstart.md) útmutatást.

## <a name="install-windows-admin-center"></a>A Windows felügyeleti központ telepítése

A Windows felügyeleti központ egy helyileg üzembe helyezett, böngészőalapú alkalmazás Azure Stack HCI kezeléséhez. A [Windows felügyeleti központ telepítésének](/windows-server/manage/windows-admin-center/deploy/install) legegyszerűbb módja a helyi felügyeleti számítógép (asztali mód), de telepítheti is a kiszolgálót (szolgáltatási mód).

Ha a Windows felügyeleti központot egy kiszolgálóra telepíti, a CredSSP igénylő feladatokat (például a fürtök létrehozását és a frissítések és bővítmények telepítését) olyan fiókkal kell megkövetelni, amely az átjáró-rendszergazdák csoport tagja a Windows felügyeleti központ kiszolgálóján. További információkért tekintse meg a [felhasználói Access Control és engedélyek konfigurálásának](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions)első két fejezetét.

## <a name="add-and-connect-to-an-azure-stack-hci-cluster"></a>Azure Stack HCI-fürt hozzáadása és kapcsolódás

A Windows felügyeleti központ telepítésének befejezése után hozzáadhat egy fürtöt a felügyelethez a fő Áttekintés lapon.

1. Kattintson a **+ Hozzáadás** elemre az **összes kapcsolat** alatt.

    :::image type="content" source="media/get-started/addcluster.png" alt-text="[Fürt hozzáadása képernyőfelvétel":::

2. Válasszon egy Windows Server-fürtöt:

    :::image type="content" source="media/get-started/chooseconnectiontype.png" alt-text="A kapcsolattípus kiválasztása képernyőfelvétel":::

3. Írja be a kezelendő fürt nevét, és kattintson a **Hozzáadás** gombra. A rendszer hozzáadja a fürtöt a kapcsolatok listájához az Áttekintés oldalon.

4. A **minden kapcsolat** területen kattintson az imént hozzáadott fürt nevére. A Windows felügyeleti központ elindítja a **fürtszolgáltatást** , és közvetlenül az adott fürthöz tartozó Windows felügyeleti központ irányítópultját fogja elkészíteni.

## <a name="monitor-cluster-performance-with-the-windows-admin-center-dashboard"></a>A fürt teljesítményének figyelése a Windows felügyeleti központ irányítópultján

A Windows felügyeleti központ irányítópultján riasztások és állapotadatok találhatók a kiszolgálókról, meghajtókról és kötetekről, valamint a CPU-, memória-és tárterület-használat részleteiről. Az irányítópult alján a fürt teljesítményével kapcsolatos információk, például a IOPS és a késések óra, nap, hét, hónap vagy év szerint jelennek meg.

:::image type="content" source="media/get-started/dashboard.png" alt-text="Windows felügyeleti központ irányítópult képernyőképe":::

## <a name="monitor-performance-of-individual-components"></a>Az egyes összetevők teljesítményének figyelése

Az irányítópult bal oldalán található **eszközök** menü segítségével megtekintheti a fürt bármely összetevőjét a virtuális gépek, kiszolgálók, kötetek és meghajtók összefoglalásának és leltározásának megtekintéséhez.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

A fürtön futó virtuális gépek összegzésének megtekintéséhez kattintson a bal oldali **eszközök** menü **virtuális gépek** elemére.

:::image type="content" source="media/get-started/vms-summary.png" alt-text="Virtuális gép összegzése":::

A fürtön futó virtuális gépek teljes leltára, az állapotuk, a gazdagép, a CPU-használat, a memória terhelése, a memória igénye, a hozzárendelt memória és a rendelkezésre állás lapon kattintson a **leltár** elemre az oldal tetején.

:::image type="content" source="media/get-started/vms-inventory.png" alt-text="Virtuális gépek leltározása":::

### <a name="servers"></a>Kiszolgálók

A fürtben lévő kiszolgálók összegzésének megtekintéséhez kattintson a bal oldali **eszközök** menüjének **kiszolgálók** elemére.

:::image type="content" source="media/get-started/servers-summary.png" alt-text="Kiszolgálók összegzése":::

A fürtben található kiszolgálók teljes leltározásához, beleértve az állapotukat, a üzemidőt, a gyártót, a modellt és a sorozatszámot, kattintson az oldal tetején található **leltár** elemre.

:::image type="content" source="media/get-started/servers-inventory.png" alt-text="Kiszolgálók leltározása":::

### <a name="volumes"></a>Kötetek

A fürtön található kötetek összegzésének megtekintéséhez kattintson a bal oldali **eszközök** menüjének **kötetek** elemére.

:::image type="content" source="media/get-started/volumes-summary.png" alt-text="Kötetek összegzése":::

A fürtön található kötetek teljes leltározásához, beleértve az állapotukat, a fájlrendszert, a rugalmasságot, a méretet, a tárterület-használatot és a IOPS, kattintson a lap tetején található **leltár** elemre.

:::image type="content" source="media/get-started/volumes-inventory.png" alt-text="Kötetek leltározása":::

### <a name="drives"></a>Meghajtók

A fürtben található meghajtók összegzésének megtekintéséhez kattintson a bal oldali **eszközök** menüjének **meghajtók** elemére.

:::image type="content" source="media/get-started/drives-summary.png" alt-text="Meghajtók összegzése":::

A fürtben található meghajtók teljes leltározásához a sorozatszám, az állapot, a modell, a méret, a típus, a használat, a hely, a kiszolgáló és a kapacitás beállításnál kattintson az oldal tetején található **leltár** elemre.

:::image type="content" source="media/get-started/drives-inventory.png" alt-text="Meghajtók leltározása":::

### <a name="virtual-switches"></a>Virtuális kapcsolók

Ha meg szeretné tekinteni egy virtuális kapcsoló beállításait a fürtben, kattintson a bal oldali **eszközök** menü **virtuális kapcsolók** elemére, majd kattintson annak a virtuális kapcsolónak a nevére, amelynek a beállításait meg szeretné jeleníteni. A Windows felügyeleti központ megjeleníti a virtuális kapcsolóhoz társított hálózati adaptereket, beleértve az IP-címüket, a kapcsolati állapotot, a kapcsolat sebességét és a MAC-címet.

:::image type="content" source="media/get-started/virtual-switch-settings.png" alt-text="Virtuális kapcsoló beállításai":::

## <a name="add-counters-with-the-performance-monitor-tool"></a>Számlálók hozzáadása a Teljesítményfigyelő eszközzel

A Teljesítményfigyelő eszközzel valós időben tekintheti meg és hasonlíthatja össze a Windows, az alkalmazások és az eszközök teljesítményszámlálói számlálóit.

1. Válassza a **Teljesítményfigyelő** elemet a bal oldali **eszközök** menüből.
2. Az **üres** munkaterület lehetőségre kattintva új munkaterületet indíthat el, vagy **visszaállíthatja az előzőt** egy korábbi munkaterület visszaállításához.
    :::image type="content" source="media/get-started/performance-monitor.png" alt-text="Teljesítményfigyelő Képernyőfelvétele":::
3. Ha új munkaterületet hoz létre, kattintson a **Számláló hozzáadása** gombra, és válasszon ki egy vagy több figyelni kívánt kiszolgálót, vagy válassza ki a teljes fürtöt.
4. Válassza ki a figyelni kívánt objektumot és példányt, valamint a számláló és a gráf típusát a dinamikus teljesítményadatok megtekintéséhez.
    :::image type="content" source="media/get-started/example-counter.png" alt-text="Példa a Counter képernyőképre":::
5. Mentse a munkaterületet a **> Mentés másként lehetőség** kiválasztásával a felső menüben.

## <a name="use-azure-monitor-for-monitoring-and-alerts"></a>Azure Monitor használata figyeléshez és riasztásokhoz

Az elemzéshez és jelentéskészítéshez [Azure monitor](manage/azure-monitor.md) is használhatja az események és teljesítményszámlálók összegyűjtéséhez, műveleteket hajthat végre egy adott feltétel észlelésekor, és e-mailben fogadhat értesítéseket. Kattintson az **eszközök** menü **Azure monitor** elemére, hogy közvetlenül kapcsolódjon az Azure-hoz a Windows felügyeleti központból.

## <a name="collect-diagnostics-information"></a>Diagnosztikai adatok gyűjtése

Válassza az **eszközök** menü **diagnosztika** elemét, hogy információkat gyűjtsön a fürttel kapcsolatos hibaelhárítási problémákról. Ha Microsoft ügyfélszolgálata hív meg, kérheti ezeket az adatokat.

## <a name="next-steps"></a>Következő lépések

Az Azure Stack HCI-fürtök monitorozásával kapcsolatos további információkért lásd még:

- [Közvetlen tárolóhelyek teljesítményének előzményei](/windows-server/storage/storage-spaces/performance-history)
- [Azure Stack HCI figyelése Azure Monitor](manage/azure-monitor.md)
