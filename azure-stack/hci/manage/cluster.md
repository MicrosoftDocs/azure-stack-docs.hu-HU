---
title: Azure Stack HCI-fürtök kezelése – Windows felügyeleti központ
description: Ismerje meg, hogyan kezelheti a fürtöket Azure Stack HCI-ben a Windows felügyeleti központ használatával.
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 01/12/2021
ms.openlocfilehash: 7f77855945ecfb31e223db46be8b2e2e3a012c16
ms.sourcegitcommit: 502df315764bbc4ff6d3de50b957dfd4a6c0043a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98130350"
---
# <a name="manage-azure-stack-hci-clusters-using-windows-admin-center"></a>Azure Stack HCI-fürtök kezelése a Windows felügyeleti központtal

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

A Windows felügyeleti központ használatával felügyelheti a Azure Stack HCI-fürtöket. Pontosabban a Windows felügyeleti központban a cluster Manager funkció használatával kezelheti a fürtöket.

## <a name="view-the-cluster-dashboard"></a>A fürt irányítópultjának megtekintése

A fürt irányítópultja a fürt állapotával és teljesítményével kapcsolatos információkat jeleníti meg.

:::image type="content" source="media/manage-cluster/cluster-dashboard.png" alt-text="A fürt irányítópultjának képernyője" lightbox="media/manage-cluster/cluster-dashboard.png":::

Ezen információk megtekintéséhez válassza ki a fürt nevét az **összes kapcsolat** területen, majd a bal oldali **eszközök** területen válassza az **irányítópult** lehetőséget. A következőket tekintheti meg:

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

## <a name="change-storage-settings"></a>Tárolási beállítások módosítása

Kiválaszthatja, hogy a kiszolgáló memóriáját használja a gyakori olvasások gyorsítótárazásához, és adja meg a kiszolgálónként használandó maximális memóriát. További információ: [a gyorsítótár megismerése Azure stack HCI-ben](../concepts/cache.md).

1. A Windows felügyeleti központban válassza ki a **Fürtfelügyelő** elemet a felső legördülő listából.
1. Az **eszközök** területen válassza a lenti **Beállítások** lehetőséget.
1. Válassza a **memóriában tárolt gyorsítótár** lehetőséget, és adja meg az új nevet.

    :::image type="content" source="media/manage-cluster/cluster-settings-memory.png" alt-text="fürt memóriában tárolt gyorsítótárának képernyője" lightbox="media/manage-cluster/cluster-settings-memory.png":::

1. Módosíthatja Közvetlen tárolóhelyek által használt tárolási készlet nevét. Válassza a **tárolási készletek** lehetőséget, és adja meg az új nevet. Ez a kiterjesztett fürtök esetében alkalmazható.

    :::image type="content" source="media/manage-cluster/cluster-settings-storage-pools.png" alt-text="fürt Storage-készlet képernyője" lightbox="media/manage-cluster/cluster-settings-storage-pools.png":::

1. Módosíthatja Közvetlen tárolóhelyek beállításait. Válassza a **közvetlen tárolóhelyek** lehetőséget, és szükség szerint módosítsa az alábbi beállításokat:

    - **Állandó gyorsítótár** – az állandó gyorsítótár engedélyezése vagy letiltása
    - **HDD-gyorsítótár mód** – HDD-meghajtók gyorsítótáras üzemmódjának módosítása
    - **Gyorsítótáras üzemmód az SSD-hez** – SSD meghajtók gyorsítótárának módosítása

    :::image type="content" source="media/manage-cluster/cluster-settings-storage-spaces-direct.png" alt-text="fürt Közvetlen tárolóhelyek képernyő" lightbox="media/manage-cluster/cluster-settings-storage-spaces-direct.png":::

## <a name="change-cluster-settings"></a>Fürt beállításainak módosítása

A fürtre több általános beállítás is alkalmazható. Itt állíthatja be és kezelheti a hozzáférési pontokat, a csomópont-leállítási viselkedést, a forgalom titkosítását, a virtuális gépek terheléselosztását és a tanúsító fürtöt.

1. A Windows felügyeleti központban válassza ki a **Fürtfelügyelő** elemet a felső legördülő listából.
1. Az **eszközök** területen válassza a **Beállítások** lehetőséget.
1. A fürt nevének módosításához válassza a **hozzáférési pont** lehetőséget, és adja meg az új nevet.

    :::image type="content" source="media/manage-cluster/cluster-settings-access.png" alt-text="Aktív/aktív kiterjesztett fürt forgatókönyve" lightbox="media/manage-cluster/cluster-settings-access.png":::

1. A csomópont leállítási viselkedésének szabályozásához válassza a **csomópont leállítási viselkedése** lehetőséget, és győződjön meg arról, hogy a jelölőnégyzet engedélyezve Ez először a csomópontról helyezi át a virtuális gépeket, így lehetővé teszi a csomópontok kecses leállítását.

    :::image type="content" source="media/manage-cluster/cluster-settings-shutdown.png" alt-text="fürtcsomópontok leállítási viselkedése képernyő" lightbox="media/manage-cluster/cluster-settings-shutdown.png":::

1. A fürtcsomópontok közötti adatküldéshez használt SMB-kapcsolatok titkosításához válassza a **fürt forgalmi titkosítása** lehetőséget, majd válassza a **titkosítás** lehetőséget a legördülő listából a következőkhöz:

   - **Alapvető forgalom** – titkosítja a NetFT (fürtözött virtuális adapter) a 3343-es porton keresztül továbbított forgalmat

   - **Tárolási forgalom** – titkosítja fürt megosztott KÖTETE (CSV) és a Storage Bus Layer (SBL) forgalmát

        :::image type="content" source="media/manage-cluster/cluster-settings-encryption.png" alt-text="fürt fürt forgalmának titkosítása képernyő" lightbox="media/manage-cluster/cluster-settings-encryption.png":::

1. A virtuális gépek fürtön belüli automatikus terheléselosztásához válassza a **virtuális gép terheléselosztása** lehetőséget, és tegye a következőket:

   - A **virtuális gépek egyenlege** beállításnál válassza ki a megfelelő műveletet.
   - Az **agresszivitáshoz** válassza ki a megfelelő viselkedést

     Ennek működéséről a [virtuális gépek terheléselosztásának áttekintése](/windows-server/failover-clustering/vm-load-balancing-overview)című témakörben olvashat bővebben.

        :::image type="content" source="media/manage-cluster/cluster-settings-vm-load.png" alt-text="fürt virtuális gépe terheléselosztási képernyője" lightbox="media/manage-cluster/cluster-settings-vm-load.png":::

1. A határozatképesség típusának kiválasztásához válassza a **tanúsító** lehetőséget, majd a **tanúsító típushoz** válassza a következők egyikét:

   - **Felhőbeli tanúsító** – Azure-beli felhőalapú erőforrás használata tanúsító
   - **Tanúsító lemez** – lemezes erőforrás használata tanúsító (nem használható a kiterjesztett fürtök esetében)
   - **Tanúsító fájlmegosztás** – tanúsító fájlmegosztás használata

        A tanúsító beállításával kapcsolatos részletes információkat a [tanúsító fürt beállítása](../deploy/witness.md)című témakörben talál. Lásd még: [a fürt és a készlet Kvórumának megértése Azure stack HCI-](../concepts/quorum.md)ben.

        :::image type="content" source="media/manage-cluster/cluster-settings-witness.png" alt-text="Tanúsító fürt képernyője" lightbox="media/manage-cluster/cluster-settings-witness.png":::

1. Ha affinitási szabályokat szeretne használni a virtuális gépek elhelyezésére a gazdagépek és a helyek között, válassza az **affinitási szabályok** lehetőséget, majd kattintson a **szabály létrehozása** elemre. A szabályok beállításával kapcsolatos részletes információkért lásd: [kiszolgáló-és hely-affinitási szabályok létrehozása virtuális gépekhez](vm-affinity.md).

    :::image type="content" source="media/manage-cluster/affinity-rules.png" alt-text="fürt affinitási szabályai képernyő" lightbox="media/manage-cluster/affinity-rules.png":::

1. Válassza ki a **diagnosztikai adatait**, majd válassza ki a következők egyikét a diagnosztika számára a Microsoftnak küldendő adatmennyiség kiválasztásához:

    - **Diagnosztikai adatvédelem kikapcsolva (biztonság)** – a rendszer nem küld el semmilyen adattovábbítást
    - **Kötelező (alapszintű)** – a biztonságos és naprakész állapot érdekében elküldhető minimális adatmennyiség
    - Nem **kötelező (teljes)** – az összes vonatkozó adat elküldve

    :::image type="content" source="media/manage-cluster/cluster-diagnostic-data.png" alt-text="a fürt adatdiagnosztika képernyője" lightbox="media/manage-cluster/cluster-diagnostic-data.png":::

## <a name="change-hyper-v-settings"></a>Hyper-V-beállítások módosítása

Több Hyper-V-gazdagépre vonatkozó beállítás is alkalmazható a fürtön.

1. A Windows felügyeleti központban válassza ki a **Fürtfelügyelő** elemet a felső legördülő listából.
1. Az **eszközök** területen válassza a **Beállítások** lehetőséget.
1. Válassza az **általános** lehetőséget, majd használja a következő beállításokat:

   - **Virtuális merevlemezek elérési útja** – Itt adhatja meg a virtuális merevlemezek fájljainak tárolására szolgáló alapértelmezett mappát.

   - **Virtual Machines elérési út** – Itt adhatja meg a virtuális gép konfigurációs fájljainak tárolására szolgáló alapértelmezett mappát.

        :::image type="content" source="media/manage-cluster/cluster-settings-hyperv.png" alt-text="fürt Hyper-V általános beállításainak képernyője" lightbox="media/manage-cluster/cluster-settings-hyperv.png":::

1. Ha engedélyezni szeretné a helyi eszközök és erőforrások átirányítását a virtuális gépekről, válassza a **bővített munkamenet mód** lehetőséget. Vegye figyelembe, hogy a kibővített munkamenet-üzemmódú kapcsolatokhoz támogatott vendég operációs rendszer szükséges.

    :::image type="content" source="media/manage-cluster/cluster-settings-session.png" alt-text="fürt Hyper-V bővített munkamenet üzemmódjának képernyője" lightbox="media/manage-cluster/cluster-settings-session.png":::

1. Ha engedélyezni szeretné a virtuális gépek számára a fizikai NUMA-csomópontok kiterjedését, válassza a **NUMA**-átfedés lehetőséget. A nem egységes memória-architektúra (NUMA) átfedésben lévő virtuális gépek több memóriával rendelkezhetnek, mint amit egyetlen NUMA-csomóponton lehet elérni.

    :::image type="content" source="media/manage-cluster/cluster-settings-numa.png" alt-text="fürt NUMA-átfedési képernyője" lightbox="media/manage-cluster/cluster-settings-numa.png":::

1. A futás közben áthelyezhető virtuális gépek számának megadásához válassza a **élő áttelepítés** lehetőséget, jelöljön ki egy számot, majd adja meg a következőket:

   - a **hitelesítési protokoll** beállításnál válassza a **CredSSP** vagy a **Kerberos** lehetőséget.

   - a **teljesítmény beállításainál** válassza a **tömörítés** vagy az **SMB** lehetőséget. A tömörített adat egy TCP/IP-kapcsolaton keresztül lesz elküldve.

   - a **bármely hálózat használata** jelölőnégyzet bejelölésével bármely elérhető hálózat használható a csomóponton az áttelepítés elvégzéséhez

        :::image type="content" source="media/manage-cluster/cluster-settings-live-migration.png" alt-text="fürt Élő áttelepítés képernyő" lightbox="media/manage-cluster/cluster-settings-live-migration.png":::

1. Ha meg szeretné határozni, hogy hány tárterület-áttelepítést lehet végrehajtani egyszerre, válassza a **tárterület áttelepítése** lehetőséget, majd válasszon egy számot.

    :::image type="content" source="media/manage-cluster/cluster-settings-storage-migration.png" alt-text="fürt tárterületének áttelepítése képernyő" lightbox="media/manage-cluster/cluster-settings-storage-migration.png":::

## <a name="register-the-cluster-with-azure"></a>A fürt regisztrálása az Azure-ban

A fürt Azure-beli regisztrálásához vagy regisztrációjának törléséhez válassza **Azure stack HCI regisztráció** lehetőséget. Ennek módjával kapcsolatos részletes információkért lásd: [Azure stack HCI kapcsolódása az Azure-hoz](../deploy/register-with-azure.md).

:::image type="content" source="media/manage-cluster/cluster-registration.png" alt-text="fürt Azure-regisztrációja képernyő" lightbox="media/manage-cluster/cluster-registration.png":::

## <a name="next-steps"></a>Következő lépések

A fürt figyeléséhez tekintse meg a következőt: [Azure stack HCI monitorozása Azure monitor](azure-monitor.md)használatával.