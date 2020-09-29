---
title: Azure Stack HCI-fürtök kezelése – Windows felügyeleti központ
description: Ismerje meg, hogyan kezelheti a fürtöket Azure Stack HCI-ben a Windows felügyeleti központ használatával.
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 07/21/2020
ms.openlocfilehash: d1b8556908da268bbd99c7aa9341128c9dc5be36
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573785"
---
# <a name="manage-azure-stack-hci-clusters-using-windows-admin-center"></a>Azure Stack HCI-fürtök kezelése a Windows felügyeleti központtal

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

A Windows felügyeleti központ használatával felügyelheti a Azure Stack HCI-fürtöket. Pontosabban a Windows felügyeleti központban a cluster Manager bővítményt fogja használni a fürtök kezeléséhez.

## <a name="view-the-cluster-dashboard"></a>A fürt irányítópultjának megtekintése

A fürt irányítópultja a fürt állapotával és teljesítményével kapcsolatos információkat jeleníti meg.

:::image type="content" source="media/manage-cluster/cluster-dashboard.png" alt-text="A fürt irányítópultjának képernyője" lightbox="media/manage-cluster/cluster-dashboard.png":::

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

## <a name="change-cluster-storage-settings"></a>A fürt tárolási beállításainak módosítása

Két olyan beállítás módosítható, amely a fürtre alkalmazható Közvetlen tárolóhelyekre vonatkozik.

1. A Windows felügyeleti központban válassza ki a **Fürtfelügyelő** elemet a felső legördülő listából.
1. Az **eszközök**területen válassza a lenti **Beállítások** lehetőséget.
1. A tárolási gyorsítótár konfigurálásához válassza a **közvetlen tárolóhelyek**lehetőséget, majd konfigurálja a következőket:

   - az **állandó gyorsítótár**beállításnál válassza az **engedélyezve** vagy a **Letiltva** lehetőséget.

   - a **HDD gyorsítótáras üzemmódja**esetén válassza a **csak olvasás**, csak **írás**vagy írás **/olvasás** lehetőséget.

   - az **SSD gyorsítótár-üzemmódja**esetében válassza az írásvédett, csak **olvasás**, **írás**vagy **írás/** olvasás lehetőséget.

        :::image type="content" source="media/manage-cluster/cluster-settings-ssd.png" alt-text="fürt Közvetlen tárolóhelyek képernyő" lightbox="media/manage-cluster/cluster-settings-ssd.png":::

1. Ha a kiszolgálói memóriát a gyakori olvasások gyorsítótárazására szeretné használni, válassza a **memóriában tárolt gyorsítótár** lehetőséget, és adja meg a kiszolgálónként használandó maximális memóriát. Lásd még: [a gyorsítótár megismerése Azure stack HCI-ben](../concepts/cache.md).

    :::image type="content" source="media/manage-cluster/cluster-settings-memory.png" alt-text="fürt memóriában tárolt gyorsítótárának képernyője" lightbox="media/manage-cluster/cluster-settings-memory.png":::

## <a name="change-cluster-general-settings"></a>A fürt általános beállításainak módosítása

A fürtre öt általános beállítás alkalmazható. Itt állíthatja be és kezelheti a hozzáférési pontokat, a csomópont-leállítási viselkedést, a forgalom titkosítását, a virtuális gépek terheléselosztását és a tanúsító fürtöt.

1. A Windows felügyeleti központban válassza ki a **Fürtfelügyelő** elemet a felső legördülő listából.
1. Az **eszközök**területen válassza a **Beállítások**lehetőséget.
1. A fürt nevének módosításához válassza a **hozzáférési pont** lehetőséget, és adja meg az új nevet.

    :::image type="content" source="media/manage-cluster/cluster-settings-access.png" alt-text="Aktív/aktív kiterjesztett fürt forgatókönyve" lightbox="media/manage-cluster/cluster-settings-access.png":::

1. A csomópont leállítási viselkedésének szabályozásához válassza a **csomópont leállítási viselkedése** lehetőséget, és győződjön meg arról, hogy a jelölőnégyzet engedélyezve Ez először a csomópontról helyezi át a virtuális gépeket, így lehetővé teszi a csomópontok kecses leállítását.

    :::image type="content" source="media/manage-cluster/cluster-settings-shutdown.png" alt-text="fürtcsomópontok leállítási viselkedése képernyő" lightbox="media/manage-cluster/cluster-settings-shutdown.png":::

1. A fürtcsomópontok közötti adatküldéshez használt SMB-kapcsolatok titkosításához válassza a **fürt forgalmi titkosítása**lehetőséget, majd válassza a **titkosítás** lehetőséget a legördülő listából a következőkhöz:

   - **Alapvető forgalom** – titkosítja a NetFT (fürtözött virtuális adapter) a 3343-es porton keresztül továbbított forgalmat

   - **Kiszolgálói forgalom** – titkosítja fürt megosztott KÖTETE (CSV) és a Storage Bus Layer (SBL) forgalmát

        :::image type="content" source="media/manage-cluster/cluster-settings-encryption.png" alt-text="fürt fürt forgalmának titkosítása képernyő" lightbox="media/manage-cluster/cluster-settings-encryption.png":::

1. A virtuális gépek fürtön belüli automatikus terheléselosztásához válassza a **virtuális gép terheléselosztása**lehetőséget, és tegye a következőket:

   - A **virtuális gépek egyenlege**beállításnál válassza ki a megfelelő műveletet.
   - Az **agresszivitáshoz**válassza ki a megfelelő viselkedést

     Ennek működéséről a [virtuális gépek terheléselosztásának áttekintése](/windows-server/failover-clustering/vm-load-balancing-overview)című témakörben olvashat bővebben.

        :::image type="content" source="media/manage-cluster/cluster-settings-vm-load.png" alt-text="fürt virtuális gépe terheléselosztási képernyője" lightbox="media/manage-cluster/cluster-settings-vm-load.png":::

1. A határozatképesség típusának kiválasztásához válassza a **tanúsító**lehetőséget, majd válasszon a következők közül:

   - **Felhőbeli tanúsító** – Azure-beli felhőalapú erőforrás használata tanúsító
   - **Tanúsító lemez** – lemezes erőforrás használata tanúsító (nem használható a kiterjesztett fürtök esetében)
   - **Tanúsító fájlmegosztás** – tanúsító fájlmegosztás használata

        További információ: [a fürt és a készlet Kvórumának ismertetése Azure stack HCI](../concepts/quorum.md)-ben.

        :::image type="content" source="media/manage-cluster/cluster-settings-witness.png" alt-text="Tanúsító fürt képernyője" lightbox="media/manage-cluster/cluster-settings-witness.png":::

## <a name="change-cluster-hyper-v-settings"></a>A fürt Hyper-V beállításainak módosítása

A fürtre öt Hyper-V gazdagép-beállítás alkalmazható.

1. A Windows felügyeleti központban válassza ki a **Fürtfelügyelő** elemet a felső legördülő listából.
1. Az **eszközök**területen válassza a **Beállítások**lehetőséget.
1. Válassza az **általános** lehetőséget, majd használja a következő beállításokat:

   - **Virtuális merevlemezek elérési útja** – Itt adhatja meg a virtuális merevlemezek fájljainak tárolására szolgáló alapértelmezett mappát.

   - **Virtual Machines elérési út** – Itt adhatja meg a virtuális gép konfigurációs fájljainak tárolására szolgáló alapértelmezett mappát.

   - **Hypervisor Scheduler típusa** – válassza a **Core Scheduler** vagy a **klasszikus ütemező**lehetőséget. Ez határozza meg, hogy a hypervisor hogyan ütemezze a virtuális folyamatokat olyan fizikai processzorokon, amelyek egyidejű többszálas (más néven SMT vagy Hyper-Threading) szolgáltatást használnak.

        :::image type="content" source="media/manage-cluster/cluster-settings-hyperv.png" alt-text="fürt Hyper-V általános beállításainak képernyője" lightbox="media/manage-cluster/cluster-settings-hyperv.png":::

1. Ha engedélyezni szeretné a helyi eszközök és erőforrások átirányítását a virtuális gépekről, válassza a **bővített munkamenet mód**lehetőséget. Vegye figyelembe, hogy a kibővített munkamenet-üzemmódú kapcsolatokhoz támogatott vendég operációs rendszer szükséges.

    :::image type="content" source="media/manage-cluster/cluster-settings-session.png" alt-text="fürt Hyper-V bővített munkamenet üzemmódjának képernyője" lightbox="media/manage-cluster/cluster-settings-session.png":::

1. Ha engedélyezni szeretné a virtuális gépek számára a fizikai NUMA-csomópontok kiterjedését, válassza a **NUMA**-átfedés lehetőséget. A nem egységes memória-architektúra (NUMA) átfedésben lévő virtuális gépek több memóriával rendelkezhetnek, mint amit egyetlen NUMA-csomóponton lehet elérni.

    :::image type="content" source="media/manage-cluster/cluster-settings-numa.png" alt-text="fürt NUMA-átfedési képernyője" lightbox="media/manage-cluster/cluster-settings-numa.png":::

1. A futás közben áthelyezhető virtuális gépek számának megadásához válassza a **élő áttelepítés**lehetőséget, jelöljön ki egy számot, majd adja meg a következőket:

   - a **hitelesítési protokoll**beállításnál válassza a **CredSSP** vagy a **Kerberos**lehetőséget.

   - a **teljesítmény beállításnál**válassza a **tömörítés** vagy az **SMB**lehetőséget. A tömörített adat egy TCP/IP-kapcsolaton keresztül lesz elküldve.

   - a **bármely hálózat használata** jelölőnégyzet bejelölésével bármely elérhető hálózat használható a csomóponton az áttelepítés elvégzéséhez

        :::image type="content" source="media/manage-cluster/cluster-settings-liv-migration.png" alt-text="fürt Élő áttelepítés képernyő" lightbox="media/manage-cluster/cluster-settings-liv-migration.png":::

1. Ha meg szeretné határozni, hogy hány tárterület-áttelepítést lehet végrehajtani egyszerre, válassza a **tárterület áttelepítése**lehetőséget, majd válasszon egy számot.

    :::image type="content" source="media/manage-cluster/cluster-settings-sto-migration.png" alt-text="fürt tárterületének áttelepítése képernyő" lightbox="media/manage-cluster/cluster-settings-sto-migration.png":::

## <a name="next-steps"></a>Következő lépések

- A fürt figyeléséhez tekintse meg a következőt: [Azure stack HCI monitorozása Azure monitor](azure-monitor.md)használatával.
