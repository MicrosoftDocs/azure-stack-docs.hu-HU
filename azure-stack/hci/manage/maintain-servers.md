---
title: Azure Stack HCI-kiszolgáló karbantartás nélküli offline állapotba helyezése
description: Ez a témakör útmutatást nyújt a Azure Stack HCI operációs rendszert futtató kiszolgálók megfelelő szüneteltetéséhez, leürítéséhez és folytatásához a Windows felügyeleti központtal és a PowerShell-lel.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/23/2020
ms.openlocfilehash: 3b2c462ad8e6db96be5968074ce5478f1ca9c870
ms.sourcegitcommit: 6a51687a98c417a004cd4295ad06ae813e1978cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92641023"
---
# <a name="taking-an-azure-stack-hci-server-offline-for-maintenance"></a>Azure Stack HCI-kiszolgáló karbantartás nélküli offline állapotba helyezése

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Azure Stack HCI esetén a kiszolgáló karbantartás céljából való offline állapotba helyezése során a rendszer a fürt összes kiszolgálója között megosztott tárterületet vesz igénybe. Ehhez szüneteltetni kell az offline állapotú kiszolgálót, a szerepköröket és a virtuális gépeket (VM-ket) a fürt többi kiszolgálójára, és ellenőriznie kell, hogy az összes adat elérhető-e a fürt többi kiszolgálóján. Ez a folyamat biztosítja, hogy az adatbiztonság és a karbantartási időszak alatt elérhető maradjon.

A Windows felügyeleti központ vagy a PowerShell használatával offline állapotba állíthatja a kiszolgálót karbantartás céljából. Ez a témakör mindkét módszert magában foglalja.

   > [!IMPORTANT]
   > Ez a témakör azt feltételezi, hogy a fizikai kiszolgálót le kell állítania a karbantartás végrehajtásához, vagy más okból újra kell indítani. Ha a frissítéseket egy Azure Stack HCI-fürtön szeretné telepíteni, tekintse meg a következő témakört: [Azure stack HCI-fürtök frissítése](update-cluster.md), amely ismerteti, hogyan használható a Cluster-Aware Update (Cau) a jelen témakörben ismertetett lépések automatikus elvégzéséhez, miközben szükség esetén frissíti a kiszolgálókat és újraindítja őket.

## <a name="take-a-server-offline-using-windows-admin-center"></a>Kiszolgáló offline állapotba állítása a Windows felügyeleti központtal

A Azure Stack HCI-fürtön a kiszolgálók offline állapotba helyezésének legegyszerűbb módja a Windows felügyeleti központ használata.

### <a name="verify-its-safe-to-take-the-server-offline"></a>Ellenőrizze, hogy biztonságos-e a kiszolgáló offline állapotba állítása

1. A Windows felügyeleti központ használatával kapcsolódjon ahhoz a kiszolgálóhoz, amelyet offline állapotba szeretne helyezni. Válassza az **eszközök** menü **Storage > lemezek** elemét, és ellenőrizze, hogy minden virtuális lemez állapot oszlopa **online** **állapotú** -e.

2. Ezután válassza a **Storage > kötetek** lehetőséget, és ellenőrizze, hogy minden kötet állapot **oszlopa** **kifogástalan** **állapotú** -e, és hogy minden kötet állapota oszlopban az **OK** érték látható-e.

### <a name="pause-and-drain-the-server"></a>A kiszolgáló szüneteltetése és ürítése

A kiszolgálók leállítása vagy újraindítása előtt szüneteltetni kell a kiszolgálót és a leeresztést (a kilépést) a fürtözött szerepkörök, például a rajta futó virtuális gépek számára, hogy a kiszolgáló leállása ne befolyásolja az alkalmazás állapotát. A fürtözött kiszolgálók folyamatos szüneteltetése és leürítése, mielőtt offline állapotba helyezné őket a karbantartás során.

1. A Windows felügyeleti központ használatával kapcsolódjon a fürthöz, majd válassza a cluster Manager **eszközök** menüjének **számítási > csomópontok** elemét.

2. Kattintson a szüneteltetni és lecsapolni kívánt kiszolgáló nevére, majd válassza a **pause (szüneteltetés** ) lehetőséget. A következő üzenetnek kell megjelennie:

   *Ha szünetelteti ezt a csomópontot, a rendszer minden fürtözött szerepkört más csomópontokra helyez át, és nem adhat hozzá szerepköröket a csomóponthoz, amíg újra nem indul. Biztos, hogy szünetelteti a fürt csomópontját?*

3. Válassza az **Igen** lehetőséget a kiszolgáló szüneteltetéséhez és a kiürítési folyamat elindításához. A fürt csomópontjának állapota **kiürítésnek** fog megjelenni, és a szerepkörök, például a Hyper-V és a virtuális gépek azonnal megkezdik az élő áttelepítést a fürt többi kiszolgálójára. Ez eltarthat néhány percig.

   > [!NOTE]
   > Ha megfelelően szünetelteti és leállítja a kiszolgálót, Azure Stack a HCI automatikus biztonsági ellenőrzést végez annak biztosításához, hogy biztonságos legyen a folytatás. Ha sérült kötetek vannak, a rendszer leállítja és figyelmezteti, hogy nem biztonságos a folytatás.

### <a name="shut-down-the-server"></a>A kiszolgáló leállítása

Miután a kiszolgáló elvégezte a kiürítést, a Windows felügyeleti központban **szüneteltetve** állapot jelenik meg. Mostantól biztonságosan leállíthatja a kiszolgálót karbantartás céljából, vagy újraindíthatja.

### <a name="resume-the-server"></a>A kiszolgáló folytatása

Ha készen áll arra, hogy a-kiszolgáló megkezdje a fürtözött szerepkörök és virtuális gépek futtatását, egyszerűen kapcsolja be a kiszolgálót, várja meg, amíg elindult, majd folytassa a kiszolgálót a következő lépésekkel.

1. A Fürtfelügyelőben válassza a bal oldali **eszközök** menüjében a **számítási > csomópontok** lehetőséget.

2. Válassza ki a folytatni kívánt kiszolgáló nevét, majd kattintson a **Folytatás** gombra. A következő üzenetnek kell megjelennie:

   *Biztosan folytatja a fürt csomópontjának folytatását?*

3. A legtöbb esetben jelölje be azt a jelölőnégyzetet, amely a *fürtözött szerepköröknek a csomópontra való visszaállítását* mondja. A kiszolgáló folytatásához válassza az **Igen** lehetőséget.

Ha bejelölte a fenti 3. lépésben szereplő jelölőnégyzetet, a fürtözött szerepkörök és virtuális gépek azonnal megkezdik az élő áttelepítést a kiszolgálóra. Ez eltarthat néhány percig.

### <a name="wait-for-storage-to-resync"></a>Várakozás a tárterület újraszinkronizálására

A kiszolgáló újraindításakor minden olyan új írást, amely akkor történt, amikor nem volt elérhető, újra kell szinkronizálni. Ez automatikusan történik, az intelligens változások követésével. Az *összes* adatot nem szükséges beszkennelni vagy szinkronizálni; csak a módosítások. Ez a folyamat az éles számítási feladatokra gyakorolt hatás mérséklését szabályozza. Attól függően, hogy mennyi ideig volt szüneteltetve a kiszolgáló, és mennyi az új adatmennyiség, több percet is igénybe vehet.

   > [!IMPORTANT]
   > A fürtben lévő más kiszolgálók offline állapotba helyezése előtt meg kell várnia, hogy a rendszer újraszinkronizálja a szinkronizálást.

Ha szeretné megnézni, hogy befejeződött-e az újraszinkronizálás, kapcsolódjon a kiszolgálóhoz a Windows felügyeleti központban, és válassza a **tároló > kötetek** lehetőséget a bal oldali **eszközök** menüből, majd válassza ki a **kötetek** elemet az oldal tetején. Ha az **összes kötet állapot** oszlopa **kifogástalan** állapotú, és az összes kötet **állapota** oszlopa **rendben van** , akkor az újraszinkronizálás befejeződött, és mostantól biztonságos a fürt más kiszolgálóinak offline állapotba helyezése.

## <a name="take-a-server-offline-using-powershell"></a>Kiszolgáló offline állapotba állítása a PowerShell használatával

A következő eljárásokkal a kiszolgálók megfelelő szüneteltetését, leürítését és folytatását hajthatja Azure Stack HCI-fürtön a PowerShell használatával.

### <a name="verify-its-safe-to-take-the-server-offline"></a>Ellenőrizze, hogy biztonságos-e a kiszolgáló offline állapotba állítása

Annak ellenőrzéséhez, hogy az összes kötet kifogástalan állapotú-e, futtassa a következő parancsmagot rendszergazdaként:

```PowerShell
Get-VirtualDisk
```

Íme egy példa arra, hogy a kimenet a következőképpen néz ki:

```
FriendlyName              ResiliencySettingName FaultDomainRedundancy OperationalStatus HealthStatus    Size FootprintOnPool StorageEfficiency
------------              --------------------- --------------------- ----------------- ------------    ---- --------------- -----------------
Mirror II                 Mirror                1                     OK                Healthy         4 TB         8.01 TB            49.99%
Mirror-accelerated parity                                             OK                Healthy      1002 GB         1.96 TB            49.98%
Mirror                    Mirror                1                     OK                Healthy         1 TB            2 TB            49.98%
ClusterPerformanceHistory Mirror                1                     OK                Healthy        24 GB           49 GB            48.98%
```

Ellenőrizze, hogy az összes kötet **órákban kifejezve** tulajdonsága **kifogástalan** állapotú-e, és a **OperationalStatus** az OK gombot jeleníti meg.

### <a name="pause-and-drain-the-server"></a>A kiszolgáló szüneteltetése és ürítése

Futtassa a következő parancsmagot rendszergazdaként a kiszolgáló szüneteltetéséhez és kiürítéséhez:

```PowerShell
Suspend-ClusterNode -Drain
```

### <a name="shut-down-the-server"></a>A kiszolgáló leállítása

Miután a kiszolgáló elvégezte a kiürítést, a rendszer a PowerShellben **szüneteltetve** jelenik meg.

Mostantól biztonságosan leállíthatja a kiszolgálót, vagy újraindíthatja azt a `Stop-Computer` vagy a `Restart-Computer` PowerShell-parancsmagok használatával.

   > [!NOTE]
   > A fürtszolgáltatást leállító `Get-VirtualDisk` vagy indító vagy leállító kiszolgálókon futtatott parancsok futtatásakor a kiszolgáló működési állapota hiányos vagy csökkentett teljesítményű lehet, és az Állapot oszlopban figyelmeztetés jelenhet meg. Ez normális, és nem okoz problémát. Az összes kötet online és elérhető marad.

### <a name="resume-the-server"></a>A kiszolgáló folytatása

Futtassa a következő parancsmagot rendszergazdaként a kiszolgáló fürtön való folytatásához. A kiszolgálón korábban futó fürtözött szerepkörök és virtuális gépek visszaadásához használja az opcionális feladat **-visszavétel** jelzőt:

```PowerShell
Resume-ClusterNode –Failback Immediate
```

A kiszolgáló újraindítása **után a** PowerShellben jelenik meg.

### <a name="wait-for-storage-to-resync"></a>Várakozás a tárterület újraszinkronizálására

A kiszolgáló újraindítása után meg kell várnia, hogy a rendszer a fürt többi kiszolgálójának offline állapotba helyezése előtt megvárja a szinkronizálást.

A folyamat figyeléséhez futtassa a következő parancsmagot rendszergazdaként:

```PowerShell
Get-StorageJob
```

Ha az újraszinkronizálás már befejeződött, a kimenet nem jelenik meg.

Íme néhány példa az újraszinkronizálási (javítási) feladatok futtatására, amelyek továbbra is futnak:

```
Name   IsBackgroundTask ElapsedTime JobState  PercentComplete BytesProcessed BytesTotal
----   ---------------- ----------- --------  --------------- -------------- ----------
Repair True             00:06:23    Running   65              11477975040    17448304640
Repair True             00:06:40    Running   66              15987900416    23890755584
Repair True             00:06:52    Running   68              20104802841    22104819713
```

A **BytesTotal** oszlopban látható, hogy mennyi tárterületet kell újraszinkronizálni. A **KészültségiSzint paraméter értéke** oszlop a folyamat előrehaladását jeleníti meg.

   > [!WARNING]
   > A javítási feladatok befejezéséig nem lehet a másik kiszolgálót offline állapotba helyezni.

Ebben az időszakban a kötetek a **órákban kifejezve** alatt továbbra is **figyelmeztetésként** jelennek meg, ami normális.

Ha például a parancsmagot használja a `Get-VirtualDisk` tároló újraszinkronizálásakor, a következő kimenet jelenhet meg:

```
FriendlyName ResiliencySettingName OperationalStatus HealthStatus IsManualAttach Size
------------ --------------------- ----------------- ------------ -------------- ----
MyVolume1    Mirror                InService         Warning      True           1 TB
MyVolume2    Mirror                InService         Warning      True           1 TB
MyVolume3    Mirror                InService         Warning      True           1 TB
```

A feladatok befejeződése után ellenőrizze, hogy a kötetek **újra megjelennek-** e a `Get-VirtualDisk` parancsmag használatával. Íme néhány példa a kimenetre:

```
FriendlyName ResiliencySettingName OperationalStatus HealthStatus IsManualAttach Size
------------ --------------------- ----------------- ------------ -------------- ----
MyVolume1    Mirror                OK                Healthy      True           1 TB
MyVolume2    Mirror                OK                Healthy      True           1 TB
MyVolume3    Mirror                OK                Healthy      True           1 TB
```

Most már biztonságos a fürt többi kiszolgálójának szüneteltetése és újraindítása.

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Közvetlen tárolóhelyek áttekintése](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Azure Stack HCI-fürtök frissítése](update-cluster.md)