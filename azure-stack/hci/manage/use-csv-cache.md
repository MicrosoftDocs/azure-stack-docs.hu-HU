---
title: A CSV memóriában tárolt olvasási gyorsítótár használata Azure Stack HCI használatával
description: Ez a témakör azt ismerteti, hogyan használható a rendszermemória a teljesítmény növelése érdekében.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/16/2020
ms.openlocfilehash: 47bfa8c656a2581c9dc125b1bd99379b9012e448
ms.sourcegitcommit: 301e571626f8e85556d9eabee3f385d0b81fdef4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2020
ms.locfileid: "92157631"
---
# <a name="use-the-csv-in-memory-read-cache-with-azure-stack-hci"></a>A CSV memóriában tárolt olvasási gyorsítótár használata Azure Stack HCI használatával

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Ez a témakör azt ismerteti, hogyan használható a rendszermemória a Azure Stack HCI teljesítményének növelésére a gyakori olvasások gyorsítótárazásával. Az írásokat nem lehet gyorsítótárazni a memóriában.

Azure Stack HCI kompatibilis a Fürt megosztott kötete (CSV) memóriában tárolt olvasási gyorsítótárral. A rendszermemória a gyorsítótárba történő olvasásával javíthatja a teljesítményt olyan alkalmazások esetében, mint például a Hyper-V, amely nem pufferelt I/O-t használ a VHD-vagy VHDX-fájlok eléréséhez. (A nem pufferelt I/o-műveletek minden olyan művelet, amelyet a Windows cache Manager nem gyorsítótáraz.)

Mivel a memóriában lévő gyorsítótár kiszolgáló-helyi, javítja az adatkörnyezetet: a legutóbbi olvasások a memóriában vannak gyorsítótárazva ugyanazon a gazdagépen, amelyen a virtuális gép fut, így csökkentve az olvasási műveletek gyakoriságát a hálózaton. Ez alacsonyabb késést és jobb tárolási teljesítményt eredményez.

Vegye figyelembe, hogy a CSV memóriában tárolt olvasási gyorsítótára eltér a Azure Stack HCI-ben lévő [Storage-készlet gyorsítótárába](../concepts/cache.md) .

## <a name="planning-considerations"></a>Tervezési szempontok

A memóriában tárolt olvasási gyorsítótár a leghatékonyabb a nagy olvasási igényű számítási feladatokhoz, például a virtuális asztali infrastruktúrához (VDI). Ezzel szemben, ha a munkaterhelés rendkívül írási igényű, a gyorsítótár nagyobb terhelést eredményezhet, mint az érték, és le kell tiltani.

A CSV memóriában tárolt olvasási gyorsítótárának teljes fizikai memóriája akár 80%-át is felhasználhatja. Ügyeljen arra, hogy hagyjon elég memóriát a virtuális gépek számára.

  > [!NOTE]
  > Bizonyos teljesítményteszt-eszközök, például a DISKSPD és a [VM-flották](https://github.com/Microsoft/diskspd/tree/master/Frameworks/VMFleet) , a memóriában lévő CSV-olvasási gyorsítótárban is rosszabb eredményeket eredményezhetnek, mint a nélkül. Alapértelmezés szerint a VM-flotta 1 10 GiB VHDX hoz létre virtuális gépenként – körülbelül 1 TiB-összeget 100 virtuális gépekhez –, majd *egységesen véletlenszerű* olvasást és írást végez. A valós számítási feladatoktól eltérően az olvasások nem követnek kiszámítható vagy ismétlődő mintát, így a memórián belüli gyorsítótár nem hatékony, és csak a terhelés.

## <a name="configuring-the-in-memory-read-cache"></a>A memóriában tárolt olvasási gyorsítótár konfigurálása

A memóriában tárolt CSV-olvasási gyorsítótár a Azure Stack HCI, a Windows Server 2019 és a Windows Server 2016 szolgáltatásban is elérhető ugyanazzal a funkcióval. Azure Stack HCI-ben és a Windows Server 2019-ben alapértelmezés szerint 1 gibibájtnak (GiB) van lefoglalva. A Windows Server 2016-ben alapértelmezés szerint ki van kapcsolva.

| Operációs rendszer verziója          | Alapértelmezett CSV-gyorsítótár mérete |
|---------------------|------------------------|
| Azure Stack HCI     | 1 GiB                  |
| Windows Server 2019 | 1 GiB                  |
| Windows Server 2016 | 0 (letiltva)           |

A gyorsítótár Windows felügyeleti központon keresztüli konfigurálásához válassza a bal oldali **eszközök** menü alján található **Beállítások** elemet. Ezután lépjen a **Storage > memóriában tárolt gyorsítótárba**. A jelölőnégyzet bejelölésével engedélyezheti vagy letilthatja a gyorsítótárat, valamint megadhatja a gyorsítótár számára lefoglalt maximális memóriát is. A módosítások végrehajtása után kattintson a lap alján található **Mentés** gombra.

Ha szeretné megtekinteni, hogy mennyi memóriát foglal le a PowerShell használatával, futtassa a következőt:

```PowerShell
(Get-Cluster).BlockCacheSize
```

A visszaadott érték kiszolgálónként mebibytes (MiB). Például az `1024` 1 GIB jelöli.

A memória lefoglalásának módosításához módosítsa ezt az értéket a PowerShell használatával. Ha például kiszolgálónként 2 GiB-t szeretne lefoglalni, futtassa a következőt:

```PowerShell
(Get-Cluster).BlockCacheSize = 2048
```

Ahhoz, hogy a módosítások azonnal érvénybe lépjenek, szüneteltesse, majd folytassa a CSV-köteteket, vagy helyezze át őket a kiszolgálók között. Ezzel a PowerShell-kódrészlettel például az egyes CSV-ket áthelyezheti egy másik kiszolgáló-csomópontra, és ismét visszatérhet:

```PowerShell
Get-ClusterSharedVolume | ForEach {
    $Owner = $_.OwnerNode
    $_ | Move-ClusterSharedVolume
    $_ | Move-ClusterSharedVolume -Node $Owner
}
```

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [A Storage-készlet gyorsítótárának ismertetése](../concepts/cache.md)
- [Fürt megosztott köteteinek használata feladatátvevő fürtben](/windows-server/failover-clustering/failover-cluster-csvs#enable-the-csv-cache-for-read-intensive-workloads-optional)
