---
title: Azure Stack HCI-fürtök kezelése a Windows felügyeleti központtal
description: Ismerje meg, hogyan kezelheti a fürtöket Azure Stack HCI-ben a Windows felügyeleti központ használatával.
ms.topic: article
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 05/20/2020
ms.openlocfilehash: 55ce577cbedde36d271ab6fc13234b009c825d7e
ms.sourcegitcommit: 7c10a45a8de0c5c7649e5329ca5b69a0791e37b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83729280"
---
# <a name="manage-azure-stack-hci-clusters-using-windows-admin-center"></a>Azure Stack HCI-fürtök kezelése a Windows felügyeleti központtal

> A következőkre vonatkozik: Windows Server 2019

A Windows felügyeleti központ használatával Azure Stack HCI-ben kezelheti a fürtöket. Pontosabban a Windows felügyeleti központban a cluster Manager bővítményt fogja használni a fürtök kezeléséhez.

## <a name="view-the-cluster-dashboard"></a>A fürt irányítópultjának megtekintése

A fürt irányítópultja a fürt állapotával és teljesítményével kapcsolatos információkat jeleníti meg.

:::image type="content" source="media/manage-cluster/cluster-dashboard.png" alt-text="A fürt irányítópultjának képernyője":::

Ezen információk megtekintéséhez válassza ki a fürt nevét az **összes kapcsolat**területen, majd a bal oldali **eszközök** területen válassza az **irányítópult**lehetőséget. A következőket tekintheti meg:

- Fürt eseményeinek riasztásai
- A fürthöz csatlakozó kiszolgálók listája
- A fürtön futó virtuális gépek listája
- A fürtön elérhető lemezmeghajtók listája
- A fürtön elérhető kötetek listája
- Fürt teljes CPU-kihasználtsága a fürthöz
- A fürt összes memóriahasználat a fürtön
- Fürt teljes tárterületének használata
- Teljes fürt bemeneti/kimeneti műveletei/másodperc (IOPS)
- Fürt átlagos késése ezredmásodpercben

## <a name="change-cluster-general-settings"></a>A fürt általános beállításainak módosítása

A fürtre öt általános beállítás alkalmazható.

1. A Windows felügyeleti központban kattintson a felső legördülő nyílra a **Fürtfelügyelő** elemre.
1. Az **eszközök**alatt kattintson a **Beállítások**elemre.
1. A fürt nevének módosításához válassza a **hozzáférési pont** lehetőséget, és adja meg az új nevet.

    :::image type="content" source="media/manage-cluster/cluster-settings-access.png" alt-text="fürt hozzáférési pontjának képernyője":::

1. A csomópont leállítási viselkedésének szabályozásához válassza a **csomópont leállítási viselkedése** lehetőséget, és győződjön meg arról, hogy a jelölőnégyzet engedélyezve Ez először a csomópontról helyezi át a virtuális gépeket, így lehetővé teszi a csomópontok kecses leállítását.

    :::image type="content" source="media/manage-cluster/cluster-settings-shutdown.png" alt-text="fürtcsomópontok leállítási viselkedése képernyő":::

1. A fürtcsomópontok közötti adatküldéshez használt SMB-kapcsolatok titkosításához válassza a **fürt forgalmi titkosítása**lehetőséget, majd válassza a **titkosítás** lehetőséget a legördülő listából a következőkhöz:

   - **Alapvető forgalom** – titkosítja a NetFT (fürtözött virtuális adapter) a 3343-es porton keresztül továbbított forgalmat

   - **Kiszolgálói forgalom** – titkosítja fürt megosztott KÖTETE (CSV) és a Storage Bus Layer (SBL) forgalmát

        :::image type="content" source="media/manage-cluster/cluster-settings-encryption.png" alt-text="fürt fürt forgalmának titkosítása képernyő":::

1. A virtuális gépek fürtön belüli automatikus terheléselosztásához válassza a **virtuális gép terheléselosztása**lehetőséget, és tegye a következőket:

   - A **virtuális gépek egyenlege**beállításnál válassza ki a megfelelő műveletet.
   - Az **agresszivitáshoz**válassza ki a megfelelő viselkedést

     Ennek működéséről a [virtuális gépek terheléselosztásának áttekintése](https://docs.microsoft.com/windows-server/failover-clustering/vm-load-balancing-overview)című témakörben olvashat bővebben.

        :::image type="content" source="media/manage-cluster/cluster-settings-vm-load.png" alt-text="fürt virtuális gépe terheléselosztási képernyője":::

1. A határozatképesség típusának kiválasztásához válassza a **tanúsító**lehetőséget, majd válasszon a következők közül:

   - **Felhőbeli tanúsító** – Azure-beli felhőalapú erőforrás használata tanúsító
   - **Tanúsító lemez** – lemezes erőforrás használata tanúsító
   - **Tanúsító fájlmegosztás** – tanúsító fájlmegosztás használata

        További információ: a [kvórum konfigurálása és kezelése](https://docs.microsoft.com/windows-server/failover-clustering/manage-cluster-quorum).

        :::image type="content" source="media/manage-cluster/cluster-settings-witness.png" alt-text="Tanúsító fürt képernyője":::

## <a name="change-cluster-hyper-v-settings"></a>A fürt Hyper-V beállításainak módosítása

A fürtre öt Hyper-V gazdagép-beállítás alkalmazható.

1. A Windows felügyeleti központban kattintson a felső legördülő nyílra a **Fürtfelügyelő** elemre.
1. Az **eszközök**alatt kattintson a **Beállítások**elemre.
1. Válassza az **általános** lehetőséget, majd használja a következő beállításokat:

   - **Virtuális merevlemezek elérési útja** – Itt adhatja meg a virtuális merevlemezek fájljainak tárolására szolgáló alapértelmezett mappát.

   - **Virtual Machines elérési út** – Itt adhatja meg a virtuális gép konfigurációs fájljainak tárolására szolgáló alapértelmezett mappát.

   - **Hypervisor Scheduler típusa** – válassza a **Core Scheduler** vagy a **klasszikus ütemező**lehetőséget. Ez határozza meg, hogy a hypervisor hogyan ütemezze a virtuális folyamatokat olyan fizikai processzorokon, amelyek egyidejű többszálas (más néven SMT vagy Hyper-Threading) szolgáltatást használnak.

        :::image type="content" source="media/manage-cluster/cluster-settings-hyperv.png" alt-text="fürt Hyper-V általános beállításainak képernyője":::

1. Ha engedélyezni szeretné a helyi eszközök és erőforrások átirányítását a virtuális gépekről, válassza a **bővített munkamenet mód**lehetőséget. Vegye figyelembe, hogy a kibővített munkamenet-üzemmódú kapcsolatokhoz támogatott vendég operációs rendszer szükséges.

    :::image type="content" source="media/manage-cluster/cluster-settings-session.png" alt-text="fürt Hyper-V bővített munkamenet üzemmódjának képernyője":::

1. Ha engedélyezni szeretné a virtuális gépek számára a fizikai NUMA-csomópontok kiterjedését, válassza a **NUMA**-átfedés lehetőséget. A nem egységes memória-architektúra (NUMA) átfedésben lévő virtuális gépek több memóriával rendelkezhetnek, mint amit egyetlen NUMA-csomóponton lehet elérni.

    :::image type="content" source="media/manage-cluster/cluster-settings-numa.png" alt-text="fürt NUMA-átfedési képernyője":::

1. A futás közben áthelyezhető virtuális gépek számának megadásához válassza a **élő áttelepítés**lehetőséget, jelöljön ki egy számot, majd adja meg a következőket:, válassza a **élő áttelepítés**lehetőséget, jelöljön ki egy számot, majd adja meg a következőket:

   - a **hitelesítési protokoll**beállításnál válassza a **CredSSP** vagy a **Kerberos**lehetőséget.

   - a **teljesítmény beállításnál**válassza a **tömörítés** vagy az **SMB**lehetőséget. A tömörített adat egy TCP/IP-kapcsolaton keresztül lesz elküldve.

   - a **bármely hálózat használata** jelölőnégyzet bejelölésével bármely elérhető hálózat használható a csomóponton az áttelepítés elvégzéséhez

        :::image type="content" source="media/manage-cluster/cluster-settings-liv-migration.png" alt-text="fürt Élő áttelepítés képernyő":::

1. Ha meg szeretné határozni, hogy hány tárterület-áttelepítést lehet végrehajtani egyszerre, válassza a **tárterület áttelepítése**lehetőséget, majd válasszon egy számot.

    :::image type="content" source="media/manage-cluster/cluster-settings-sto-migration.png" alt-text="fürt tárterületének áttelepítése képernyő":::

## <a name="change-cluster-storage-settings"></a>A fürt tárolási beállításainak módosítása

Két olyan beállítás módosítható, amely a fürtre alkalmazható Közvetlen tárolóhelyekre vonatkozik.

1. A Windows felügyeleti központban kattintson a felső legördülő nyílra a **Fürtfelügyelő** elemre.
1. Az **eszközök**alatt kattintson a lenti **Beállítások** elemre.
1. A tárolási gyorsítótár konfigurálásához válassza a **közvetlen tárolóhelyek**lehetőséget, majd konfigurálja a következőket:

   - az **állandó gyorsítótár**beállításnál válassza az **engedélyezve** vagy a **Letiltva** lehetőséget.

   - a **HDD gyorsítótáras üzemmódja**esetén válassza a **csak olvasás**, csak **írás**vagy írás **/olvasás** lehetőséget.

   - az **SSD gyorsítótár-üzemmódja**esetében válassza az írásvédett, csak **olvasás**, **írás**vagy **írás/** olvasás lehetőséget.

        :::image type="content" source="media/manage-cluster/cluster-settings-ssd.png" alt-text="fürt Közvetlen tárolóhelyek képernyő":::

1. Ha a kiszolgálói memóriát a gyakori olvasások gyorsítótárazására szeretné használni, válassza a **memóriában tárolt gyorsítótár** lehetőséget, és adja meg a kiszolgálónként használandó maximális memóriát. Lásd még: [közvetlen tárolóhelyek használata a CSV memóriában tárolt olvasási gyorsítótárban](https://docs.microsoft.com/windows-server/storage/storage-spaces/csv-cache).

    :::image type="content" source="media/manage-cluster/cluster-settings-memory.png" alt-text="fürt memóriában tárolt gyorsítótárának képernyője":::

## <a name="next-steps"></a>Következő lépések

- A fürt teljesítményének figyeléséhez lásd: a [fürt teljesítményének figyelése](https://docs.microsoft.com/azure-stack/hci/get-started#monitor-cluster-performance-with-the-windows-admin-center-dashboard)