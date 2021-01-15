---
title: Virtuálisgép-terheléselosztás
description: Ebből a témakörből megtudhatja, hogyan konfigurálhatja a virtuális gép terheléselosztási funkcióját Azure Stack HCI-és Windows Server-kiszolgálón.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 1/14/2021
ms.openlocfilehash: 9caf8efdd8db46479cce3c5c4299fb5588c7d37a
ms.sourcegitcommit: 51ce5ba6cf0a377378d25dac63f6f2925339c23d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224642"
---
# <a name="virtual-machine-load-balancing"></a>Virtuálisgép-terheléselosztás

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019; Windows Server 2016

A HCI üzemelő példányok fő szempontja az éles üzembe lépéshez szükséges CapEx. Gyakori, hogy redundancia-hozzáadást biztosítanak, hogy elkerülje a kapacitást az üzemi forgalomban, de ez növeli a CapEx. Ez a redundancia gyakran szükséges, mivel a fürt egyes kiszolgálói több virtuális gépet üzemeltetnek, míg más kiszolgálók használata nem megfelelő.

Alapértelmezés szerint engedélyezve van a Azure Stack HCI, a Windows Server 2019 és a Windows Server 2016 rendszerben, a virtuális gép terheléselosztása olyan szolgáltatás, amely lehetővé teszi a kiszolgálók kihasználtságának optimalizálását a fürtökben. A rendszer a túlterhelt kiszolgálókat azonosítja, és a virtuális gépek élő áttelepítését ezektől a kiszolgálóktól a véglegesített kiszolgálókon. A sikertelen házirendek, például az affinitás, a tartalék tartományok (helyek) és a lehetséges tulajdonosok tiszteletben tartása.

A virtuális gépek terheléselosztása a következő heurisztikus szabályok alapján értékeli ki a kiszolgáló terhelését:

- **Aktuális memória-nyomás:** A memória a Hyper-V-gazdagépek leggyakoribb erőforrás-korlátozása.
- **CPU-kihasználtság átlaga egy öt perces ablakban:** A fürtben lévő bármelyik kiszolgáló túlzott véglegesítésének csökkentése.

## <a name="how-does-vm-load-balancing-work"></a>Hogyan működik a virtuális gépek terheléselosztása?

A virtuális gép terheléselosztása automatikusan megtörténik, amikor új kiszolgálót ad hozzá a fürthöz, és az is beállítható, hogy rendszeres időközönként ismétlődő terheléselosztást végezzen.

### <a name="when-a-new-server-is-added-to-a-cluster"></a>Új kiszolgáló fürthöz való hozzáadásakor

Amikor új kiszolgálót csatlakoztat a fürthöz, a virtuális gép terheléselosztási funkciója automatikusan kiosztja a kapacitást a meglévő kiszolgálókról az újonnan hozzáadott kiszolgálóra a következő sorrendben:

1. A rendszer kiértékeli a memória terhelését és a CPU-kihasználtságot a fürtben lévő meglévő kiszolgálókon.
2. A rendszer minden, a küszöbértéket meghaladó kiszolgálót azonosít.
3. A rendszer a legnagyobb memória-és CPU-kihasználtságú kiszolgálókat azonosítja a kiegyensúlyozás prioritásának megállapításához.
4. A virtuális gépek élő migrálása (leállás nélkül) egy olyan kiszolgálóról, amely meghaladja a küszöbértéket a fürtben lévő újonnan hozzáadott kiszolgálóval.

:::image type="content" source="media/vm-load-balancing/server-added.png" alt-text="A fürtbe felvenni kívánt új kiszolgálót bemutató kép" border="false"::: 

### <a name="recurring-load-balancing"></a>Ismétlődő terheléselosztás

Alapértelmezés szerint a virtuális gépek terheléselosztása az időszakos terheléselosztásra van konfigurálva: a rendszer 30 percenként kiértékeli a memória terhelését és a processzor kihasználtságát a fürt minden egyes kiszolgálóján. Itt látható a lépések folyamata:

1. A rendszer kiértékeli a memória terhelését és a CPU-kihasználtságot a fürt összes kiszolgálóján.
2. A küszöbértéket meghaladó és a küszöbérték alatti összes kiszolgáló azonosítható.
3. A rendszer a legnagyobb memória-és CPU-kihasználtságú kiszolgálókat azonosítja a kiegyensúlyozás prioritásának megállapításához.
4. A virtuális gépek élő áttelepítését (leállás nélkül) egy olyan kiszolgálóról, amely meghaladja a küszöbértéket egy másik, a minimális küszöbérték alatti kiszolgálóra.

:::image type="content" source="media/vm-load-balancing/periodic-balancing.png" alt-text="Az élő fürtöt automatikusan újra kiegyensúlyozó képet ábrázoló kép" border="false"::: 

## <a name="configure-vm-load-balancing-using-windows-admin-center"></a>A virtuális gépek terheléselosztásának konfigurálása a Windows felügyeleti központban

A virtuális gépek terheléselosztásának legegyszerűbb módja a Windows felügyeleti központ használata. 

:::image type="content" source="media/vm-load-balancing/vm-load-balancing.png" alt-text="A virtuális gépek terheléselosztásának konfigurálása a Windows felügyeleti központtal" lightbox="media/vm-load-balancing/vm-load-balancing.png":::

1. Kapcsolódjon a fürthöz, és lépjen az **eszközök > beállítások menüpontra**.

2. A **Beállítások** területen válassza a **virtuális gép terheléselosztás** elemet.

3. A **virtuális gépek egyenlege** területen válassza a **mindig** a terheléselosztás a kiszolgáló csatlakoztatása és 30 percenként lehetőséget, a **kiszolgáló** a terheléselosztást csak a kiszolgáló csatlakozásakor, vagy **Soha ne** tiltsa le a virtuális gép terheléselosztási funkcióját. Az alapértelmezett beállítás **mindig**.

4. Az **agresszivitás** alatt válassza az **alacsony** értéket a virtuális gépek élő áttelepítése esetén, ha a kiszolgáló több mint 80%-os betöltést, **közepes** áttelepítést végez, ha a kiszolgáló meghaladja a 70 **%-ot, illetve a** fürtben lévő kiszolgálók átlagát, és ha a kiszolgáló több mint 5%-kal meghaladja az átlagot. Az alapértelmezett beállítás **alacsony**.

## <a name="configure-vm-load-balancing-using-windows-powershell"></a>A virtuális gépek terheléselosztásának konfigurálása a Windows PowerShell használatával

Beállíthatja, hogy a terheléselosztás mikor és mikor történjen a fürt közös tulajdonságának használatával `AutoBalancerMode` . A fürt egyensúlyának szabályozásához futtassa a következőt a PowerShellben, és helyettesítse be az alábbi táblázat értékét:

```PowerShell
(Get-Cluster).AutoBalancerMode = <value>
```

|AutoBalancerMode |Működés|
|-----------------|-----------|
| 0 | Disabled (Letiltva) |
| 1 | Terheléselosztás a kiszolgáló csatlakoztatása után |
| 2 (alapértelmezett) | Terheléselosztás a kiszolgáló csatlakoztatása és 30 percenként |

A fürt általános tulajdonságának használatával is konfigurálhatja a kiegyensúlyozás agresszivitását `AutoBalancerLevel` . Az agresszivitás küszöbértékének szabályozásához futtassa a következőt a PowerShellben, és helyettesítse be az alábbi táblázat értékét:

```PowerShell
(Get-Cluster).AutoBalancerLevel = <value>
```

| AutoBalancerLevel | Agresszivitás | Működés |
|-------------------|----------------|----------|
| 1 (alapértelmezés) | Alacsony | Áthelyezés, ha a gazdagép 80%-nál nagyobb terhelést mutat |
| 2 | Közepes | Áthelyezés, ha a gazdagép 70%-nál nagyobb terhelést mutat |
| 3 | Magas | A fürtben lévő átlagos kiszolgálók, amelyek akkor mozognak, amikor a gazdagép meghaladja az átlagnál 5%-ot |

A `AutoBalancerLevel` és a `AutoBalancerMode` Tulajdonságok beállításának megtekintéséhez futtassa a következőt a PowerShellben:

```PowerShell
Get-Cluster | fl AutoBalancer*
```

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Virtuális gépek kezelése](vm.md)
- [Virtuális gépek kezelése a PowerShell-lel](vm-powershell.md)
- [VM-affinitási szabályok létrehozása](vm-affinity.md)
