---
title: Kötetek létrehozása Azure Stack HCI-ben
description: Kötetek létrehozása Azure Stack HCI-ben a Windows felügyeleti központ és a PowerShell használatával.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 9bb0ff34863f8262d5919e5eae6f735709097bf5
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99570735"
---
# <a name="create-volumes-in-azure-stack-hci"></a>Kötetek létrehozása Azure Stack HCI-ben

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ez a témakör azt ismerteti, hogyan hozhat létre köteteket egy Azure stack HCI-fürtön a Windows felügyeleti központ és a Windows PowerShell használatával, hogyan dolgozhat a köteteken található fájlokkal, valamint hogyan engedélyezhető az deduplikálás és a kötetek tömörítése. A kötetek létrehozásával és a kiterjesztett fürtök replikálásának beállításával kapcsolatos további információkért lásd a [kiterjesztett kötetek létrehozása](create-stretched-volumes.md)című témakört.

## <a name="create-a-three-way-mirror-volume"></a>Háromutas tükrözési kötet létrehozása

Háromutas tükrözési kötet létrehozása a Windows felügyeleti központ használatával:

1. A Windows felügyeleti központban kapcsolódjon egy fürthöz, majd válassza a **kötetek** elemet az **eszközök** ablaktáblán.
2. A **kötetek** lapon válassza a **leltár** lapot, majd válassza a **kötet létrehozása** lehetőséget.
3. A **kötet létrehozása** panelen adja meg a kötet nevét, és hagyja meg a **rugalmasságot** **háromutas tükrözésként**.
4. A **méret a HDD**-n területen határozza meg a kötet méretét. Például 5 TB (terabájt).
5. Válassza a **Létrehozás** lehetőséget.

A mérettől függően a kötet létrehozása eltarthat néhány percig. A jobb felső sarokban található értesítések értesítik a kötet létrehozásakor. Az új kötet megjelenik a leltár listában.

Tekintse meg a háromutas tükrözött kötetek létrehozásával kapcsolatos gyors videót.

> [!VIDEO https://www.youtube-nocookie.com/embed/o66etKq70N8]

## <a name="create-a-mirror-accelerated-parity-volume"></a>Tükrözött, gyorsított paritású kötet létrehozása

A tükrözött felgyorsított paritás (MAP) csökkenti a köteten lévő adatlábnyomot a HDD-n. Egy háromutas tükrözési kötet például azt jelenti, hogy minden 10 terabájt méretű méret esetén 30 terabájtra lesz szüksége. A lábnyom terhelésének csökkentése érdekében hozzon létre egy kötetet a tükrözött gyorsítású paritással. Ez csökkenti a 30 terabájtos lábnyomot mindössze 22 terabájtra, akár csak 4 kiszolgálóval, az adatok legaktívabb 20 százalékának tükrözésével, valamint a több helyet biztosító paritás használatával, amely a REST tárolását teszi hatékonyabbá. A paritás és a tükrözés arányát módosíthatja úgy, hogy a teljesítmény és a kapacitás kompromisszuma megfelelő legyen a munkaterhelés számára. Például a 90 százalékos paritás és a 10 százalékos tükrözés kevesebb teljesítményt nyújt, de még tovább egyszerűsíti a lábnyomot.

  >[!NOTE]
  >A tükrözött, gyorsított paritású kötetek rugalmas fájlrendszert (ReFS) igényelnek.

Tükrözött gyorsítású paritású kötet létrehozása a Windows felügyeleti központban:

1. A Windows felügyeleti központban kapcsolódjon egy fürthöz, majd válassza a **kötetek** elemet az **eszközök** ablaktáblán.
2. A kötetek lapon válassza a **leltár** lapot, majd válassza a **kötet létrehozása** lehetőséget.
3. A **kötet létrehozása** panelen adja meg a kötet nevét.
4. A **rugalmasság** területen válassza a **tükrözött felgyorsított paritás** lehetőséget.
5. A **paritás százalékában** válassza ki a paritás százalékos arányát.
6. Válassza a **Létrehozás** lehetőséget.

Tekintse meg a tükrözött paritású kötetek létrehozásával kapcsolatos gyors videót.

> [!VIDEO https://www.youtube-nocookie.com/embed/R72QHudqWpE]

## <a name="open-volume-and-add-files"></a>Kötet megnyitása és fájlok hozzáadása

Kötet megnyitása és fájlok hozzáadása a kötethez a Windows felügyeleti központban:

1. A Windows felügyeleti központban kapcsolódjon egy fürthöz, majd válassza a **kötetek** elemet az **eszközök** ablaktáblán.
2. A **kötetek** lapon válassza a **leltár** lapot.
2. A kötetek listájában válassza ki a megnyitni kívánt kötet nevét.

    A kötet részletei lapon láthatja a kötet elérési útját.

4. A lap tetején kattintson a **Megnyitás** elemre. Ezzel elindítja a **fájlok** eszközt a Windows felügyeleti központban.
5. Navigáljon a kötet elérési útjához. Itt böngészheti a köteten található fájlokat.
6. Válassza a **feltöltés** lehetőséget, majd válassza ki a feltölteni kívánt fájlt.
7. A böngésző **vissza** gombjának használatával lépjen vissza a Windows felügyeleti központ **eszközök** ablaktáblájába.

Tekintse meg a kötetek megnyitásával és a fájlok hozzáadásával kapcsolatos gyors videót.

> [!VIDEO https://www.youtube-nocookie.com/embed/j59z7ulohs4]

## <a name="turn-on-deduplication-and-compression"></a>A deduplikálás és a tömörítés bekapcsolása

A rendszer köteten kezeli a deduplikálás és a tömörítést. A deduplikálás és a tömörítés egy utómunka modellt használ, ami azt jelenti, hogy a megtakarítás addig nem fog megjelenni, amíg nem fut. Ha igen, minden fájlon működni fog, még azok is, amelyek korábban voltak.

További információ: a [kötetek titkosításának engedélyezése, a deduplikálás és a tömörítés](volume-encryption-deduplication.md)

## <a name="create-volumes-using-windows-powershell"></a>Kötetek létrehozása a Windows PowerShell használatával

Először indítsa el a Windows PowerShellt a Windows Start menüjéből. Javasoljuk, hogy a **New-Volume** parancsmag használatával hozzon létre köteteket Azure stack HCI számára. Ez biztosítja a leggyorsabb és legegyszerűbb élményt. Ez az egyetlen parancsmag automatikusan létrehozza a virtuális lemezt, particionálja és formázza, létrehozza a kötetet a megfelelő névvel, és hozzáadja a fürt megosztott köteteihez – mindezt egy egyszerű lépésben.

A **New-Volume** parancsmagnak négy paraméterrel kell rendelkeznie, amelyeket mindig meg kell adnia:

- **FriendlyName:** Bármilyen karakterlánc, amelyet szeretne, például *"Volume1"*
- **Fájlrendszer:** Vagy **CSVFS_ReFS** (minden kötet esetében ajánlott, a tükrözött felgyorsult paritású kötetek esetében szükséges) vagy **CSVFS_NTFS**
- **StoragePoolFriendlyName:** A tárolási készlet neve, például *"S2D on ClusterName"*
- **Méret:** A kötet mérete, például *"10 TB"*

   > [!NOTE]
   > A Windows, beleértve a PowerShellt is, a bináris (Base-2) számok használatával számít, míg a meghajtókat gyakran decimális (Base-10) számok használatával címkézik. Ez a cikk azt ismerteti, hogy miért jelenik meg a Windowsban "909 GB" értékkel rendelkező "egy terabájt" meghajtó, amely 1 000 000 000 000 bájtként van meghatározva. Ez a várható eredmény. A kötetek **új köteten** történő létrehozásakor meg kell adnia a **size** paramétert a bináris (Base-2) számokban. Például a "909GB" vagy a "0.909495 TB" megadásával körülbelül 1 000 000 000 000 bájtos kötet jön létre.

### <a name="example-with-2-or-3-servers"></a>Példa: 2 vagy 3 kiszolgálóval

Ha a központi telepítésnek csak két kiszolgálója van, akkor a Közvetlen tárolóhelyek a rugalmasság érdekében automatikusan kétirányú tükrözést használ. Ha a központi telepítés csak három kiszolgálóval rendelkezik, akkor a rendszer automatikusan háromutas tükrözést használ.

```PowerShell
New-Volume -FriendlyName "Volume1" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB
```

### <a name="example-with-4-servers"></a>Példa: 4 + kiszolgálóval

Ha négy vagy több kiszolgálóval rendelkezik, akkor a választható **ResiliencySettingName** paraméterrel kiválaszthatja a rugalmasság típusát.

-   **ResiliencySettingName:** Vagy **tükrözött** vagy **paritásos**.

A következő példában a *"indítása kötet2"* háromutas tükrözést használ, a *"Volume3"* pedig kettős paritást használ (ezt gyakran "törlési kódolásnak" is nevezik).

```PowerShell
New-Volume -FriendlyName "Volume2" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Mirror
New-Volume -FriendlyName "Volume3" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Parity
```

## <a name="using-storage-tiers"></a>Tárolási rétegek használata

A három típusú meghajtóval rendelkező központi telepítések esetében az egyik kötet az SSD-és a HDD-rétegre is kiterjedhet, hogy az egyes példányok részben helyezkednek el. Hasonlóképpen, a négy vagy több kiszolgálóval üzemelő példányok esetében az egyik kötet összekeveri a tükrözést és a kettős paritást, hogy az egyes részekben részlegesen lehessen tárolni.

Az ilyen kötetek létrehozásához Azure Stack HCI alapértelmezett szintű sablonokat biztosít a **MirrorOn * MediaType*** és a **NestedMirrorOn * MediaType*** (teljesítmény), valamint a **ParityOn * MediaType*** és **NestedParityOn * MediaType*** (a kapacitáshoz), ahol a *MediaType* HDD vagy SSD. A sablonok adathordozó-típusok alapján tárolják a tárolási rétegeket, és beágyazzák a definíciókat a gyorsabb kapacitású meghajtókon (ha vannak ilyenek) és a kettős paritáson a lassabb kapacitású meghajtókon (ha vannak ilyenek).

Ezeket úgy tekintheti meg, hogy a **Get-StorageTier** parancsmagot a fürt bármely kiszolgálóján futtatja.

```PowerShell
Get-StorageTier | Select FriendlyName, ResiliencySettingName, PhysicalDiskRedundancy
```

Ha például egy két csomópontos fürt csak a HDD-val rendelkezik, a kimenet a következőhöz hasonló lehet:

```PowerShell
FriendlyName      ResiliencySettingName PhysicalDiskRedundancy
------------      --------------------- ----------------------
NestedParityOnHDD Parity                                     1
Capacity          Mirror                                     1
NestedMirrorOnHDD Mirror                                     3
MirrorOnHDD       Mirror                                     1
```

A többrétegű kötetek létrehozásához a **New-Volume** parancsmag **StorageTierFriendlyNames** és **StorageTierSizes** paramétereit használva hivatkozhat ezekre a réteg-sablonokra. A következő parancsmag például egy olyan kötetet hoz létre, amely háromutas tükrözést és kettős paritást vegyít 30:70-arányban.

```PowerShell
New-Volume -FriendlyName "Volume1" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -StorageTierFriendlyNames MirrorOnHDD, Capacity -StorageTierSizes 300GB, 700GB
```

Több kötet létrehozásához szükség szerint ismételje meg a műveletet.

### <a name="nested-resiliency-volumes"></a>Beágyazott rugalmassági kötetek

Beágyazott rugalmasság csak a két kiszolgálós fürtökre vonatkozik; Ha a fürt három vagy több kiszolgálóval rendelkezik, nem használhat beágyazott rugalmasságot. A beágyazott rugalmasság lehetővé teszi, hogy a két kiszolgálós fürt egyszerre több hardveres hibát ellenálljanak a tárterület rendelkezésre állásának elvesztése nélkül, így a felhasználók, az alkalmazások és a virtuális gépek megszakítás nélkül is futtathatók maradnak. További információért lásd: [kötetek tervezése: a rugalmasság típusának kiválasztása](../concepts/plan-volumes.md#choosing-the-resiliency-type).

#### <a name="create-nested-storage-tiers"></a>Beágyazott tárolási rétegek létrehozása

NestedMirror-rétegek létrehozása:

```PowerShell
New-StorageTier -StoragePoolFriendlyName S2D* -FriendlyName NestedMirror -ResiliencySettingName Mirror -NumberOfDataCopies 4 -MediaType HDD -CimSession 2nodecluster
```

NestedParity-rétegek létrehozása:

```PowerShell
New-StorageTier -StoragePoolFriendlyName S2D* -FriendlyName NestedParity -ResiliencySettingName Parity -NumberOfDataCopies 2 -PhysicalDiskRedundancy 1 -NumberOfGroups 1 -FaultDomainAwareness StorageScaleUnit -ColumnIsolation PhysicalDisk -MediaType HDD -CimSession 2nodecluster
```

#### <a name="create-nested-volumes"></a>Beágyazott kötetek létrehozása

NestedMirror-kötet létrehozása:

```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName MyMirrorNestedVolume -StorageTierFriendlyNames NestedMirror -StorageTierSizes 500GB -CimSession 2nodecluster
```

NestedParity-kötet létrehozása:

```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName MyParityNestedVolume -StorageTierFriendlyNames NestedMirror,NestedParity -StorageTierSizes 200GB, 1TB -CimSession 2nodecluster
```

### <a name="storage-tier-summary-table"></a>Tárolási rétegek összefoglaló táblázata

Az alábbi táblázatok összefoglalják a Azure Stack HCI-ben hozhatók létre, illetve létrehozható tárolási szinteket.

**NumberOfNodes: 2**

| FriendlyName      | MediaType | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | Megjegyzés         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | HDD       | Tükrözött                | 2                  | 1                      | 1              | StorageScaleUnit     | Fizikai lemez    | automatikusan létrehozva |
| MirrorOnSSD       | SSD       | Tükrözött                | 2                  | 1                      | 1              | StorageScaleUnit     | Fizikai lemez    | automatikusan létrehozva |
| MirrorOnSCM       | SCM       | Tükrözött                | 2                  | 1                      | 1              | StorageScaleUnit     | Fizikai lemez    | automatikusan létrehozva |
| NestedMirrorOnHDD | HDD       | Tükrözött                | 4                  | 3                      | 1              | StorageScaleUnit     | Fizikai lemez    | kézi       |
| NestedMirrorOnSSD | SSD       | Tükrözött                | 4                  | 3                      | 1              | StorageScaleUnit     | Fizikai lemez    | kézi       |
| NestedMirrorOnSCM | SCM       | Tükrözött                | 4                  | 3                      | 1              | StorageScaleUnit     | Fizikai lemez    | kézi       |
| NestedParityOnHDD | HDD       | Parity                | 2                  | 1                      | 1              | StorageScaleUnit     | Fizikai lemez    | kézi       |
| NestedParityOnSSD | SSD       | Parity                | 2                  | 1                      | 1              | StorageScaleUnit     | Fizikai lemez    | kézi       |
| NestedParityOnSCM | SCM       | Parity                | 2                  | 1                      | 1              | StorageScaleUnit     | Fizikai lemez    | kézi       |

**NumberOfNodes: 3**

| FriendlyName      | MediaType | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | Megjegyzés         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | HDD       | Tükrözött                | 3                  | 2                      | 1              | StorageScaleUnit     | Fizikai lemez    | automatikusan létrehozva |
| MirrorOnSSD       | SSD       | Tükrözött                | 3                  | 2                      | 1              | StorageScaleUnit     | Fizikai lemez    | automatikusan létrehozva |
| MirrorOnSCM       | SCM       | Tükrözött                | 3                  | 2                      | 1              | StorageScaleUnit     | Fizikai lemez    | automatikusan létrehozva |

**NumberOfNodes: 4 +**

| FriendlyName      | MediaType | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | Megjegyzés         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | HDD       | Tükrözött                | 3                  | 2                      | 1              | StorageScaleUnit     | Fizikai lemez    | automatikusan létrehozva |
| MirrorOnSSD       | SSD       | Tükrözött                | 3                  | 2                      | 1              | StorageScaleUnit     | Fizikai lemez    | automatikusan létrehozva |
| MirrorOnSCM       | SCM       | Tükrözött                | 3                  | 2                      | 1              | StorageScaleUnit     | Fizikai lemez    | automatikusan létrehozva |
| ParityOnHDD       | HDD       | Parity                | 1                  | 2                      | Automatikus           | StorageScaleUnit     | StorageScaleUnit| automatikusan létrehozva |
| ParityOnSSD       | SSD       | Parity                | 1                  | 2                      | Automatikus           | StorageScaleUnit     | StorageScaleUnit| automatikusan létrehozva |
| ParityOnSCM       | SCM       | Parity                | 1                  | 2                      | Automatikus           | StorageScaleUnit     | StorageScaleUnit| automatikusan létrehozva |

## <a name="next-steps"></a>Következő lépések

A kapcsolódó témakörökhöz és az egyéb tárolási felügyeleti feladatokhoz lásd még:

- [A Közvetlen tárolóhelyek áttekintése](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Kötetek megtervezése](../concepts/plan-volumes.md)
- [Kötetek kiterjesztése](extend-volumes.md)
- [Kötetek törlése](delete-volumes.md)