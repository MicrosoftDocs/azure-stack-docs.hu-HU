---
title: Tárolási kapacitás kezelése Azure Stack központban
description: Megtudhatja, hogyan figyelheti és kezelheti a tárolási kapacitást és a rendelkezésre állást Azure Stack hub-ban.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: inhenkel
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2020
ms.openlocfilehash: bbced92ca9eb275ed1599ff7422bde1601be11c0
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545499"
---
# <a name="manage-storage-capacity-for-azure-stack-hub"></a>Azure Stack hub tárolási kapacitásának kezelése

Ez a cikk segítséget nyújt Azure Stack a központi felhőalapú üzemeltetők számára a Azure Stack hub-telepítés tárolókapacitásának figyelését és kezelését. Az Azure Stack hub Storage-infrastruktúra a Azure Stack hub-telepítés teljes tárolókapacitásának egy részhalmazát foglalja le tárolási szolgáltatásként. A Storage Services tárolja a bérlői adattárakban lévő, a központi telepítés csomópontjainak megfelelő köteteken található megosztásokat.

Felhőbeli operátorként korlátozott mennyiségű tárterülettel dolgozhat a szolgáltatással. A tárterület mennyiségét az Ön által megvalósított megoldás határozza meg. A megoldást a SZÁMÍTÓGÉPGYÁRTÓ gyártója adja meg többcsomópontos megoldás használata esetén, vagy azt a hardver adja meg, amelyre telepíti a Azure Stack Development Kit (ASDK).

Az Azure Stack hub csak a tárolási kapacitás bővítését támogatja további skálázási egység csomópontjainak hozzáadásával. További információ: [további skálázási egységekkel rendelkező csomópontok hozzáadása Azure stack hub-ban](azure-stack-add-scale-node.md). A fizikai lemezek a csomópontokhoz való hozzáadása nem bővíti a tárolási kapacitást.

Fontos, hogy [Figyelje](#monitor-shares) a rendelkezésre álló tárolót a hatékony műveletek fenntartása érdekében. Ha a kötet fennmaradó szabad kapacitása korlátozott, tervezze meg a [rendelkezésre álló terület felügyeletét](#manage-available-space) , hogy megakadályozza a megosztások kapacitásának kifogyása.

A kapacitás felügyeletének lehetőségei a következők:


- Kapacitás visszaigénylése.
- Tárolási objektumok áttelepítése.

Ha az objektum-tároló kötete 100%-ban van kihasználva, a tárolási szolgáltatás már nem működik az adott köteten. Ha segítséget szeretne kérni a kötet műveleteinek visszaállításához, forduljon a Microsoft támogatási szolgálatához.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>A kötetek és megosztások, tárolók és lemezek ismertetése

### <a name="volumes-and-shares"></a>Kötetek és megosztások

A *Storage szolgáltatás* particionálja a rendelkezésre álló tárolót különálló, egyenlő kötetekre, amelyek a bérlői adattároláshoz vannak lefoglalva. További információ az Azure Stack hub köteteiről: [a Azure stack hub tárolási infrastruktúrájának kezelése](azure-stack-manage-storage-infrastructure.md).

Az objektum-tároló kötetei rendelkeznek bérlői adattal. A bérlői adatok közé tartoznak az oldal-Blobok, a Blobok, a Blobok, a táblák, a várólisták, az adatbázisok és a kapcsolódó metaadatok. Az objektum-tároló köteteinek száma megegyezik a Azure Stack hub üzemelő példányában lévő csomópontok számával:

- Négy csomópontos üzemelő példányon négy objektum-tároló kötet található. Többcsomópontos telepítés esetén a kötetek száma nem csökken, ha egy csomópontot eltávolítanak vagy meghibásodnak.
- Ha a ASDK használja, egyetlen kötet egyetlen megosztással rendelkezik.

Az Object Store-kötetek a tárolási szolgáltatások kizárólagos használatára szolgálnak. Nem szabad közvetlenül módosítani, hozzáadni vagy eltávolítani a köteteken található fájlokat. Csak a tárolási szolgáltatások működhetnek az ezekben a kötetekben tárolt fájlokban.

Mivel a tárolási objektumok (Blobok stb.) egyenként egyetlen köteten belül találhatók, az egyes objektumok maximális mérete nem haladhatja meg a kötet méretét. Az új objektumok maximális mérete attól függ, hogy az új objektum létrehozásakor nem használt terület marad-e a köteten.

Ha egy objektum-tároló kötete kevés a szabad területnél, és [a lemezterület felszabadítására irányuló](#reclaim-capacity) műveletek nem sikeresek vagy nem érhetők el, Azure stack a központi felhőalapú operátorok áttelepíthetik a tárolási objektumokat az egyik kötetről a másikra.

További információ arról, hogy a bérlői felhasználók hogyan működnek a Azure Stack hub blob Storage [szolgáltatásával: Azure stack hub Storage Services](../user/azure-stack-storage-overview.md).

### <a name="containers"></a>Tárolók

A bérlői felhasználók a Blobok tárolására szolgáló tárolókat hoznak létre. Bár a felhasználók határozzák meg a Blobok elhelyezését, a Storage szolgáltatás algoritmus használatával határozza meg, hogy melyik köteten helyezi el a tárolót. Az algoritmus általában kiválasztja a legnagyobb szabad területtel rendelkező kötetet.  

Miután egy blobot elhelyez egy tárolóban, a blob több helyet is felhasználhat. Amikor új blobokat ad hozzá, és a meglévő Blobok növekednek, a köteten lévő szabad terület csökken. 

A tárolók nem korlátozódnak egyetlen kötetre. Ha a tárolóban lévő kombinált blob-adatmennyiség a rendelkezésre álló terület 80%-át vagy többét használja, a tároló *túlcsordulási* módba kerül. Túlcsordulási módban a tárolóban létrehozott új Blobok egy másik, elegendő lemezterülettel rendelkező kötethez vannak rendelve. Az idő múlásával egy túlcsordulási módban lévő tároló tartalmazhat több köteten terjesztett blobokat.

Ha 80%-ot (majd 90%) a kötet rendelkezésre álló területének használatakor a rendszer [riasztásokat](#storage-space-alerts) küld a Azure stack hub felügyeleti portálján. A felhőalapú operátoroknak át kell tekinteniük a rendelkezésre álló tárolókapacitást, és meg kell tervezniük a tartalom újraelosztását. A tárolási szolgáltatás nem működik, ha egy lemez 100%-os használatban van, és nincs további riasztás.

### <a name="disks"></a>Lemezek

Azure Stack hub támogatja a felügyelt lemezek és a nem felügyelt lemezek használatát a virtuális gépeken, mint az operációs rendszer (OS) és az adatlemezek.

A **felügyelt lemezek** leegyszerűsítik az Azure IaaS virtuális gépek lemezes kezelését a virtuálisgép-lemezekhez társított Storage-fiókok kezelésével. Csak a szükséges lemez méretét kell megadnia, és Azure Stack hub hozza létre és felügyeli a lemezt. További információ: [Managed Disks Overview (áttekintés](/azure/virtual-machines/windows/managed-disks-overview)).

Javasoljuk, hogy a virtuális gépekhez Managed Disks használjon a könnyebb felügyelet és a kapacitás elosztása érdekében. A Managed Disks használata előtt nem kell előkészítenie a Storage-fiókot és-tárolókat. Több felügyelt lemez létrehozásakor a lemezek több kötetre oszlanak, ami segít a kötetek kapacitásának kiegyensúlyozásában.  

A nem **felügyelt lemezek** az Azure Storage-fiókokban blobként tárolt VHD-fájlok. A bérlők által létrehozott blobokat virtuálisgép-lemeznek nevezzük, és tárolókban tárolódnak a Storage-fiókokban. Javasoljuk, hogy a nem felügyelt lemezeket csak olyan virtuális gépek esetében használja, amelyeknek kompatibilisnek kell lenniük a harmadik féltől származó eszközökkel, csak Azure-Unmanaged lemezeket támogatnak.

A bérlőknek szóló útmutatás az egyes lemezek külön tárolóba helyezése a virtuális gép teljesítményének növelése érdekében.

- Minden olyan tároló, amely tartalmaz egy lemezt vagy egy oldal blobot, egy virtuális gépről egy csatlakoztatott tárolónak tekintendő a lemezt birtokló virtuális gép számára.
- Egy olyan tároló, amely nem tárol lemezeket egy virtuális gépről, ingyenes tárolónak minősül.

A rögzített tárolók lemezterületének felszabadítására vonatkozó beállítások korlátozottak. További információ: nem [felügyelt lemezek terjesztése](#distribute-unmanaged-disks).

>[!TIP]  
> A Felhőbeli operátorok nem működnek közvetlenül a nem felügyelt lemezekkel, amelyek olyan virtuális gépekhez vannak csatolva, amelyeket a bérlők hozzáadhatnak a tárolóhoz. Ha azonban a tárolási megosztások tárterületét tervezi kezelni, hasznos lehet megérteni, hogy a nem felügyelt lemezek hogyan kapcsolódnak a tárolók és a megosztásokhoz.

::: moniker range="<azs-2002"
## <a name="monitor-shares"></a>Megosztások figyelése

A megosztások figyeléséhez használja a Azure PowerShell vagy a felügyeleti portált, így megismerheti, hogy a szabad terület korlátozott-e. A portál használata esetén riasztást kap a kevés lemezterülettel rendelkező megosztásokról.

### <a name="use-powershell"></a>A PowerShell használata

Felhőbeli kezelőként a megosztás tárolási kapacitását a PowerShell-parancsmag használatával figyelheti `Get-AzsStorageShare` . A parancsmag az egyes megosztásokon a teljes, lefoglalt és szabad területet adja vissza bájtban.

![Példa: a megosztások szabad területének visszaküldése](media/azure-stack-manage-storage-shares/free-space.png)

- **Teljes kapacitás** : a megosztáson elérhető teljes tárterület (bájt). Ez a terület a tárolási szolgáltatások által karbantartott adatok és metaadatok esetében használatos.
- **Felhasznált kapacitás** : az adatmennyiség (bájtban), amelyet a bérlői adatokat tároló és a hozzájuk társított metaadatokat tartalmazó fájlok összes egysége használ.

### <a name="use-the-administrator-portal"></a>A felügyeleti portál használata

Felhőbeli operátorként a felügyeleti portálon megtekintheti az összes megosztás tárolási kapacitását.

1. Jelentkezzen be a felügyeleti portálra `https://adminportal.local.azurestack.external` .
2. Válassza a **minden szolgáltatás** > **tárolási** > **fájlmegosztás** lehetőséget a fájlmegosztás listájának megnyitásához, ahol megtekintheti a használati adatokat.

    ![Példa: Storage file shares in Azure Stack hub felügyeleti portál](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **Összesen** : a megosztáson elérhető teljes tárterület (bájtban). Ez a terület a tárolási szolgáltatások által karbantartott adatok és metaadatok esetében használatos.
   - **Használatban** : az adatmennyiség bájtban kifejezve, amelyet a bérlői adatokat és a hozzájuk társított metaadatokat tároló fájlokból származó összes egység használ.

::: moniker-end
::: moniker range=">=azs-2002"

## <a name="monitor-volumes"></a>Kötetek figyelése

A PowerShell vagy a felügyeleti portál használatával figyelheti a köteteket, így megismerheti, ha a szabad terület korlátozott. A portál használata esetén riasztást kap a kevés lemezterülettel rendelkező kötetekről.

### <a name="use-powershell"></a>A PowerShell használata

Felhőbeli kezelőként a PowerShell-parancsmag használatával figyelheti a kötetek tárolási kapacitását `Get-AzsVolume` . A parancsmag minden köteten a teljes és szabad területet adja vissza gigabájtban (GB).

![Példa: szabad terület visszaadása a kötetek számára](media/azure-stack-manage-storage-shares/listvolumespowershell.png)

- **Teljes kapacitás** : a megosztáson elérhető, GB-ban lévő teljes terület. Ez a terület a tárolási szolgáltatások által karbantartott adatok és metaadatok esetében használatos.
- **Fennmaradó kapacitás** : a bérlői adatok és a hozzájuk tartozó metaadatok tárolására szolgáló szabad lemezterület (GB).

### <a name="use-the-administrator-portal"></a>A felügyeleti portál használata

Felhőbeli operátorként használhatja a felügyeleti portált az összes kötet tárolókapacitásának megtekintéséhez.

1. Jelentkezzen be az Azure Stack hub felügyeleti portálra ( `https://adminportal.local.azurestack.external` ).
2. Válassza a **minden szolgáltatás** > **tárolási** > **kötetek** lehetőséget a kötetek listájának megnyitásához, ahol megtekintheti a használati adatokat.

    ![Példa: tárolási kötetek a Azure Stack hub felügyeleti portálján](media/azure-stack-manage-storage-shares/listvolumes.png)

   - **Összesen** : a köteten elérhető teljes terület. Ez a terület a tárolási szolgáltatások által karbantartott adatok és metaadatok esetében használatos.
   - **Használatban** lévő: az a mennyiség, amelyet a bérlői adatokat tároló és a hozzá tartozó metaadatokat tároló összes egysége használ.

::: moniker-end

### <a name="storage-space-alerts"></a>Tárolóhelyek – riasztások

A felügyeleti portál használata esetén riasztást kap a kevés lemezterülettel rendelkező kötetekről.

> [!IMPORTANT]
> Felhőbeli operátorként meg kell akadályozni, hogy a megosztások teljes körű használatot érjenek el. A megosztás 100%-os kihasználtsága esetén a Storage szolgáltatás már nem működik az adott megosztásban. A szabad terület és a visszaállítási műveletek a 100%-os kihasználtságú megosztáson való helyreállításához kapcsolatba kell lépnie a Microsoft ügyfélszolgálatával.

* **Figyelmeztetés** : Ha egy fájlmegosztás több mint 80%-ot használ, a felügyeleti portálon *figyelmeztető* riasztás jelenik meg:

  ![Példa: figyelmeztető riasztás a Azure Stack hub felügyeleti portálján](media/azure-stack-manage-storage-shares/alert-warning.png)

* **Kritikus** : Ha egy fájlmegosztás több mint 90%-ot használ, *kritikus* riasztást kap a felügyeleti portálon:

  ![Példa: kritikus riasztás az Azure Stack hub felügyeleti portálján](media/azure-stack-manage-storage-shares/alert-critical.png)

* **Részletek megtekintése** : a felügyeleti portálon megnyithatja a riasztások részleteit a kockázatcsökkentő beállítások megtekintéséhez:

  ![Példa: riasztás részleteinek megtekintése az Azure Stack hub felügyeleti portálján](media/azure-stack-manage-storage-shares/alert-details.png)

## <a name="manage-available-space"></a>A rendelkezésre álló terület kezelése

Ha szükség van a kötet szabad területére, először a legkevésbé invazív metódusokat használja. A felügyelt lemez átmigrálása előtt próbálkozzon például a terület visszaigénylésével.  

### <a name="reclaim-capacity"></a>Kapacitás visszaigénylése

A törölt bérlői fiókok által használt kapacitást visszaállíthatja. A rendszer automatikusan visszaállítja ezt a kapacitást az [adatmegőrzési időszak](azure-stack-manage-storage-accounts.md#set-the-retention-period) elérésekor, vagy azonnal visszaigényelheti azt.

További információkért tekintse meg a [Azure stack hub Storage-fiókok kezelése](azure-stack-manage-storage-accounts.md#reclaim)című témakör "kapacitás visszaigénylése" című szakaszát.

::: moniker range="<azs-1910"

### <a name="migrate-a-container-between-volumes"></a>Tároló áttelepítése kötetek között

*Ez a beállítás csak Azure Stack hub integrált rendszerekre vonatkozik.*

A bérlői használati szokások miatt egyes bérlői megosztások a többinél több területet használnak. Ez azt eredményezheti, hogy egyes megosztások alacsonyan futnak a helyen, mielőtt más, viszonylag nem használt megosztások is rendelkezésre állnak.

Lemezterületet szabadíthat fel egy túlhasználatos megosztáson a blob-tárolók egy másik megosztásra történő manuális áttelepítésével. Több kisebb tárolót is áttelepíthet egyetlen megosztásra, amely képes az összes tárolására. Az áttelepítéssel *ingyenes* tárolókat helyezhet át. Az ingyenes tárolók olyan tárolók, amelyek nem tartalmaznak lemezt a virtuális gép számára.

Az áttelepítés összevonja a tároló összes blobját az új megosztáson.

- Ha egy tároló túlcsordulási módot adott meg, és a blobokat további kötetekre helyezte, az új megosztásnak elegendő kapacitással kell rendelkeznie az áttelepített tároló összes blobjának tárolásához. Ide tartoznak a további megosztásokon található Blobok.

- A PowerShell-parancsmag `Get-AzsStorageContainer` csak a tároló kezdeti kötetén használt helyet azonosítja. A parancsmag nem azonosítja a további kötetekre helyezett Blobok által használt területet. Ezért előfordulhat, hogy a tárolók teljes mérete nem egyértelmű. Lehetséges, hogy egy tároló új megosztáson való összevonása az új megosztást túlcsordulási állapotba tudja küldeni, ahol további megosztásokra helyezheti az adatmennyiséget. Ennek eredményeképpen előfordulhat, hogy újra kell osztania a megosztásokat.

- Ha nem rendelkezik bizonyos erőforráscsoportok engedélyeivel, és nem tud a PowerShell használatával lekérdezni a további köteteket a túlcsordulási információkhoz, akkor az ezen erőforráscsoportok és tárolók tulajdonosaként működjön együtt az áttelepíteni kívánt összes adatmennyiség megismerése érdekében.  

> [!IMPORTANT]
> A Blobok tárolóba való áttelepítése egy offline művelet, amely a PowerShell használatát igényli. Amíg az áttelepítés befejeződik, az áttelepíteni kívánt tároló összes blobja offline állapotban marad, és nem használható. Az Azure Stack hub frissítését is el kell végezni, amíg az összes folyamatban lévő áttelepítés be nem fejeződik.

#### <a name="migrate-containers-by-using-powershell"></a>Tárolók migrálása a PowerShell használatával

1. Győződjön meg arról, hogy van [Azure PowerShell telepítve és konfigurálva](/powershell/azure/). További információ: Azure- [erőforrások kezelése Azure PowerShell használatával](/azure/azure-resource-manager/management/manage-resources-powershell).
2. Vizsgálja meg a tárolót, és Ismerje meg, hogy az áttelepíteni kívánt megosztáson milyen adatelemek találhatók. A köteten áttelepítéshez legmegfelelőbb jelölt tárolók azonosításához használja a következő `Get-AzsStorageContainer` parancsmagot:

   ```powershell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   Ezután vizsgálja meg $containers:

   ```powershell
   $containers
   ```

   ![Példa: $containers](media/azure-stack-manage-storage-shares/containers.png)

3. Azonosítsa az áttelepíteni kívánt tároló számára a legjobb cél megosztásokat:

   ```powershell
   $destinationshare = ($shares | Sort-Object FreeCapacity -Descending)[0]
   ```

   Ezután vizsgálja meg $destinationshares:

   ```powershell 
   $destinationshares
   ```

   ![Példa: $destination megosztások](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Egy tároló áttelepítésének megkezdése. Az áttelepítés aszinkron módon történik. Ha az első áttelepítés befejezése előtt elindítja a további tárolók áttelepítését, a feladattípus segítségével nyomon követheti az egyes állapotok állapotát.

   ```powershell
   $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
   ```

   Ezután vizsgálja meg $jobId. Az alábbi példában cserélje le a *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* -t a vizsgálni kívánt feladattípusra:

   ```powershell
   $jobId
   d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
   ```

5. Az áttelepítési feladatok állapotának vizsgálatához használja a feladatok AZONOSÍTÓját. A tároló áttelepítése után a **MigrationStatus** a *Befejezés* értékre van állítva.

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![Az áttelepítési állapotot bemutató képernyőkép.](media/azure-stack-manage-storage-shares/migration-status1.png)

6. Egy folyamatban lévő áttelepítési feladatot is megszakíthat. A megszakított áttelepítési feladatok aszinkron módon lesznek feldolgozva. A lemondásokat a $jobid használatával követheti nyomon:

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![Példa: visszaállítási állapot](media/azure-stack-manage-storage-shares/rollback.png)

7. Az áttelepítési állapot *megszakítása* előtt a 6. lépésből is futtathatja a parancsot:  

    ![A megszakított áttelepítési állapotra vonatkozó példát bemutató képernyőkép.](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>VM-lemezek áthelyezése

*Ez a beállítás csak Azure Stack hub integrált rendszerekre vonatkozik.*

A tárhely kezelésének legszélsőségesebb módszere a virtuálisgép-lemezek áthelyezését jelenti. Mivel egy csatolt tároló (amely virtuálisgép-lemezt tartalmaz) áthelyezése összetett, a művelet végrehajtásához forduljon a Microsoft támogatási szolgálatához.

::: moniker-end
::: moniker range=">=azs-1910"

### <a name="migrate-a-managed-disk-between-volumes"></a>Felügyelt lemez migrálása kötetek között

*Ez a beállítás csak Azure Stack hub integrált rendszerekre vonatkozik.*

A bérlői használati szokások miatt egyes bérlői kötetek több területet használnak, mint a többi. Az eredmény lehet egy olyan kötet, amely kevés a szabad területtel a viszonylag felhasználatlan kötetek előtt.

Lemezterületet szabadíthat fel egy túlhasznált köteten, ha egy felügyelt lemezt manuálisan telepít át egy másik kötetre. Több felügyelt lemezt is áttelepíthet egyetlen olyan kötetre, amely képes az összes tárolására. A *kapcsolat nélküli* felügyelt lemezek áthelyezéséhez használja az áttelepítést. A kapcsolat nélküli felügyelt lemezek olyan lemezek, amelyek nem csatlakoznak a virtuális géphez.

> [!IMPORTANT]
> A felügyelt lemezek áttelepítése egy offline művelet, amely a PowerShell használatát igényli. Az áttelepítési feladatok megkezdése előtt le kell választania a tagjelölt lemezeket a tulajdonos virtuális gépről. (az áttelepítési feladatok elvégzése után újra csatolhatja őket). Amíg az áttelepítés befejeződik, az áttelepíteni kívánt összes felügyelt lemeznek offline állapotban kell maradnia, és nem használható, ellenkező esetben az áttelepítési feladat leáll, és az összes nem áttelepített lemez továbbra is az eredeti köteteken van. Az Azure Stack hub frissítését is el kell végezni, amíg minden folyamatban lévő áttelepítés be nem fejeződik.

#### <a name="to-migrate-managed-disks-using-powershell"></a>Felügyelt lemezek migrálása a PowerShell-lel

1. Győződjön meg arról, hogy van Azure PowerShell telepítve és konfigurálva. A PowerShell-környezet konfigurálásával kapcsolatos útmutatásért lásd: a [PowerShell telepítése Azure stack hubhoz](azure-stack-powershell-install.md). Azure Stack központba való bejelentkezéshez lásd: [az operátori környezet konfigurálása és bejelentkezés Azure stack hubhoz](azure-stack-powershell-configure-admin.md).
2. Vizsgálja meg a felügyelt lemezeket, és Ismerje meg, hogy az áttelepíteni kívánt köteten milyen lemezek vannak. A köteten áttelepítéshez legmegfelelőbb tagjelölt lemezek azonosításához használja a következő `Get-AzsDisk` parancsmagot:

   ```powershell  
   $ScaleUnit = (Get-AzsScaleUnit)[0]
   $StorageSubSystem = (Get-AzsStorageSubSystem -ScaleUnit $ScaleUnit.Name)[0]
   $Volumes = (Get-AzsVolume -ScaleUnit $ScaleUnit.Name -StorageSubSystem $StorageSubSystem.Name | Where-Object {$_.VolumeLabel -Like "ObjStore_*"})
   $SourceVolume  = ($Volumes | Sort-Object RemainingCapacityGB)[0]
   $VolumeName = $SourceVolume.Name.Split("/")[2]
   $VolumeName = $VolumeName.Substring($VolumeName.IndexOf(".")+1)
   $MigrationSource = "\\SU1FileServer."+$VolumeName+"\SU1_"+$SourceVolume.VolumeLabel
   $Disks = Get-AzsDisk -Status All -SharePath $MigrationSource | Select-Object -First 10
   ```
   Ezután vizsgálja meg $disks:

   ```powershell
   $Disks
   ```

   ![Példa: $Disks](media/azure-stack-manage-storage-shares/disks.png)

3. Azonosítsa a legjobb cél kötetet az áttelepített lemezek tárolásához:

   ```powershell
   $DestinationVolume  = ($Volumes | Sort-Object RemainingCapacityGB -Descending)[0]
   $VolumeName = $DestinationVolume.Name.Split("/")[2]
   $VolumeName = $VolumeName.Substring($VolumeName.IndexOf(".")+1)
   $MigrationTarget = "\\SU1FileServer."+$VolumeName+"\SU1_"+$DestinationVolume.VolumeLabel
   ```

4. A felügyelt lemezek áttelepítésének megkezdése. Az áttelepítés aszinkron módon történik. Ha az első áttelepítés befejeződése előtt megkezdi a további lemezek áttelepítését, akkor a feladatok neve alapján követheti nyomon az egyes állapotok állapotát.

   ```powershell
   $jobName = "MigratingDisk"
   Start-AzsDiskMigrationJob -Disks $Disks -TargetShare $MigrationTarget -Name $jobName
   ```

5. Az áttelepítési feladatok állapotának vizsgálatához használja a feladatok nevét. A lemez áttelepítése után a **MigrationStatus** a **Befejezés** értékre van állítva.

   ```powershell 
   $job = Get-AzsDiskMigrationJob -Name $jobName
   ```

   ![Példa: áttelepítési állapot](media/azure-stack-manage-storage-shares/diskmigrationjob.png)
   
   Ha egy áttelepítési feladatban több felügyelt lemezt telepít át, akkor a feladat alfeladatait is megtekintheti.

   ```powershell 
   $job.Subtasks
   ```

   ![Példa: áttelepítési Alfeladat állapota](media/azure-stack-manage-storage-shares/diskmigrationsubtask.png)

6. Egy folyamatban lévő áttelepítési feladatot is megszakíthat. A megszakított áttelepítési feladatok aszinkron módon lesznek feldolgozva. A megszakítást a feladat neve segítségével követheti nyomon, amíg az állapot megerősíti az áttelepítési **feladatot:**

   ```powershell
   Stop-AzsDiskMigrationJob -Name $jobName
   ```

   ![Példa: megszakított állapot](media/azure-stack-manage-storage-shares/diskmigrationstop.png)

### <a name="distribute-unmanaged-disks"></a>Nem felügyelt lemezek terjesztése

*Ez a beállítás csak Azure Stack hub integrált rendszerekre vonatkozik.*

A terület kezelésének legszélsőségesebb módszere a nem felügyelt lemezek áthelyezését jelenti. Ha a bérlő nem felügyelt lemezeket ad hozzá egy tárolóhoz, a tároló összes felhasznált kapacitása meghaladja a kötetnek azt a rendelkezésre álló kapacitását, amelyet a tároló a *túlcsordulási* módba való belépés előtt megtart. Annak elkerülése érdekében, hogy egyetlen tároló kiürítse a kötetek területét, a bérlő egy tároló meglévő, nem felügyelt lemezeit terjesztheti különböző tárolókra. Mivel egy csatolt tároló (amely virtuálisgép-lemezt tartalmaz) terjesztése összetett, a művelet végrehajtásához forduljon Microsoft ügyfélszolgálatahoz.

::: moniker-end

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a virtuális gépek felhasználók számára történő felajánlásáról, tekintse meg a [Azure stack hub tárterület-kapacitásának kezelése](./tutorial-offer-services.md?view=azs-2002)című témakört.