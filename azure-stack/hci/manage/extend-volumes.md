---
title: Kötetek kiterjesztése Azure Stack HCI-ben
description: A kötetek átméretezése Azure Stack HCI-ben a Windows felügyeleti központ és a PowerShell használatával.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: c6f874fb7bd8641933722631d9faac0dc513b5e3
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742290"
---
# <a name="extending-volumes-in-azure-stack-hci"></a>Kötetek kiterjesztése Azure Stack HCI-ben

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Ez a témakör útmutatást nyújt a kötetek átméretezéséhez egy Azure Stack HCI-fürtön a Windows felügyeleti központ használatával.

> [!WARNING]
> **Nem támogatott: az Közvetlen tárolóhelyek által használt mögöttes tárterület átméretezése.** Ha a Közvetlen tárolóhelyekt virtualizált tárolási környezetben, például az Azure-ban futtatja, akkor a virtuális gépek által használt tárolóeszközök jellemzőinek átméretezése vagy módosítása nem támogatott, és az adatok elérhetetlenné válnak. Ehelyett kövesse a [kiszolgálók vagy meghajtók hozzáadása](/windows-server/storage/storage-spaces/add-nodes) szakaszban található utasításokat a kötetek kiterjesztése előtt további kapacitás hozzáadásához.

Tekintse meg a kötetek átméretezésével kapcsolatos gyors videót.

> [!VIDEO https://www.youtube-nocookie.com/embed/hqyBzipBoTI]

## <a name="extending-volumes-using-windows-admin-center"></a>Kötetek kiterjesztése a Windows felügyeleti központtal

1. A Windows felügyeleti központban kapcsolódjon egy Azure Stack HCI-fürthöz, majd válassza a **kötetek** lehetőséget az **eszközök** ablaktáblán.
2. A **kötetek** lapon válassza a **leltár** fület, majd válassza ki az átméretezni kívánt kötetet.

    A kötet részletei lapon a kötet tárolókapacitása szerepel. A kötetek részletei lapot közvetlenül az irányítópultról is megnyithatja. Az irányítópulton, a riasztások ablaktáblán válassza ki a riasztást, amely értesíti, ha egy köteten kevés a tárolókapacitás, majd válassza a **Ugrás kötetre**lehetőséget.

4. A kötetek részletei lap tetején válassza az **átméretezés**lehetőséget.
5. Adjon meg egy új nagyobb méretet, majd válassza az **átméretezés**lehetőséget.

    A kötetek részletei lapon a kötet nagyobb tárolókapacitása van megadva, és az irányítópulton lévő riasztás törlődik.

## <a name="extending-volumes-using-powershell"></a>Kötetek kiterjesztése a PowerShell használatával

### <a name="capacity-in-the-storage-pool"></a>A tárolási készlet kapacitása

A kötetek átméretezése előtt győződjön meg arról, hogy elegendő kapacitással rendelkezik a Storage-készletben az új, nagyobb helyigény kielégítéséhez. Ha például 1 TB-ról 2 TB-ra átméretezi a háromutas tükrözési kötetet, annak lábnyoma 3 TB-ról 6 TB-ra nő. Ahhoz, hogy az átméretezés sikeres legyen, a tárolóban legalább (6-3) = 3 TB rendelkezésre álló kapacitásra van szükség.

### <a name="familiarity-with-volumes-in-storage-spaces"></a>A tárolóhelyek köteteinek ismerete

Közvetlen tárolóhelyek minden kötet több halmozott objektumból áll: a fürt megosztott kötete (CSV), amely egy kötet; a partíció; a lemez, amely egy virtuális lemez; és egy vagy több tárolási réteg (ha van ilyen). A kötetek átméretezéséhez át kell méreteznie ezeket az objektumokat.

![Ábrán egy kötet rétegei láthatók, beleértve a fürt szegmensének kötetét, a kötetet, a partíciót, a lemezt, a virtuális lemezt és a tárolási rétegeket.](media/extend-volumes/volumes-in-smapi.png)

Ha szeretne megismerkedni velük, próbálja meg a **Get-** with a megfelelő főnév futtatását a PowerShellben.

Például:

```PowerShell
Get-VirtualDisk
```

A veremben lévő objektumok közötti társítások követéséhez az egyik **Get-** parancsmagot a következőre kell bontani.

Tegyük fel például, hogy a virtuális lemezről a kötetre kell beolvasnia:

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-Disk | Get-Partition | Get-Volume
```

### <a name="step-1--resize-the-virtual-disk"></a>1. lépés – a virtuális lemez átméretezése

A virtuális lemez tárolási szinteket használhat, vagy nem, attól függően, hogy hogyan lett létrehozva.

A következő parancsmag futtatásával ellenőrizhető:

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-StorageTier
```

Ha a parancsmag nem ad vissza semmit, a virtuális lemez nem használ tárolási szinteket.

#### <a name="no-storage-tiers"></a>Nincsenek tárolási rétegek

Ha a virtuális lemez nem rendelkezik tárolási rétegekkel, átméretezheti közvetlenül a **Resize-VirtualDisk** parancsmag használatával.

Adja meg az új méretet a **-size** paraméterben.

```PowerShell
Get-VirtualDisk <FriendlyName> | Resize-VirtualDisk -Size <Size>
```

A **VirtualDisk**átméretezése után a **lemez** automatikusan megjelenik, és a mérete is módosul.

![Az animált diagram azt mutatja, hogy egy kötet virtuális lemeze nagyobb lesz, miközben az azonnal megjelenő lemez mérete automatikusan megnő.](media/extend-volumes/Resize-VirtualDisk.gif)

#### <a name="with-storage-tiers"></a>Tárolási rétegek

Ha a virtuális lemez tárolási rétegeket használ, az egyes rétegeket külön átméretezheti a **Resize-StorageTier** parancsmag használatával.

Szerezze be a tárolási rétegek nevét a virtuális lemez társításait követve.

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-StorageTier | Select FriendlyName
```

Ezután minden réteg esetében adja meg az új méretet a **-size** paraméterben.

```PowerShell
Get-StorageTier <FriendlyName> | Resize-StorageTier -Size <Size>
```

> [!TIP]
> Ha a rétegek különböző fizikai adathordozó-típusok (például **MediaType = SSD** és **MediaType = HDD**), akkor biztosítania kell, hogy elegendő kapacitással rendelkezzen a Storage-készletben lévő minden adathordozó-típushoz, hogy megfeleljen az egyes szintek új, nagyobb helyigényének.

Ha átméretezi a **StorageTier**(ka) t, a **VirtualDisk** és a **lemez** automatikusan elvégzi a méretezést, és átméretezi őket.

![Az animált diagram azt mutatja, hogy először egy másik tárolási réteg lesz nagy, miközben a virtuális lemez rétege és a fenti lemez rétege is nagyobb.](media/extend-volumes/Resize-StorageTier.gif)

### <a name="step-2--resize-the-partition"></a>2. lépés – a partíció átméretezése

Ezután méretezze át a partíciót a **Resize-Partition** parancsmag használatával. A virtuális lemeznek két partíciót kell tartalmaznia: az első foglalt, és nem módosítható; az átméretezni kívántnak **PartitionNumber = 2** és **Type = Basic típusúnak**kell lennie.

Adja meg az új méretet a **-size** paraméterben. Javasoljuk, hogy a maximálisan támogatott méretet használja az alább látható módon.

```PowerShell
# Choose virtual disk
$VirtualDisk = Get-VirtualDisk <FriendlyName>

# Get its partition
$Partition = $VirtualDisk | Get-Disk | Get-Partition | Where PartitionNumber -Eq 2

# Resize to its maximum supported size
$Partition | Resize-Partition -Size ($Partition | Get-PartitionSupportedSize).SizeMax
```

A **partíció**átméretezése után a **kötet** és a **ClusterSharedVolume** automatikusan követhető, és a rendszer is átméretezi őket.

![Az animált ábrán látható, hogy a virtuális lemez réteg a kötet alján nő, és az egyes rétegek nagyobb mértékben növekednek.](media/extend-volumes/Resize-Partition.gif)

Ennyi az egész!

> [!TIP]
> A **Get-Volume**futtatásával ellenőrizheti, hogy a köteten van-e új méret.

## <a name="next-steps"></a>Következő lépések

A további alapvető tárolási felügyeleti feladatokkal kapcsolatos részletes útmutatásért lásd még:

- [Kötetek megtervezése](../concepts/plan-volumes.md)
- [Kötetek létrehozása](create-volumes.md)
- [Kötetek törlése](delete-volumes.md)