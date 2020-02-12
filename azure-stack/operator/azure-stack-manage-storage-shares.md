---
title: Tárolási kapacitás kezelése Azure Stack központban
description: Megtudhatja, hogyan figyelheti és kezelheti a tárolási kapacitást és a rendelkezésre állást Azure Stack hub-ban.
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: b97d8e6dcb3069ec7d693f9dfc8677947de019fb
ms.sourcegitcommit: 0a3c8b0bf9c116a5caaeca453a2bbc6e7f7cbfb9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77147871"
---
# <a name="manage-storage-capacity-for-azure-stack-hub"></a>Azure Stack hub tárolási kapacitásának kezelése

Ez a cikk segítséget nyújt Azure Stack a központi felhőalapú üzemeltetők számára a Azure Stack hub-telepítés tárolókapacitásának figyelését és kezelését. Az Azure Stack hub Storage-infrastruktúra a Azure Stack hub-telepítés teljes tárolókapacitásának egy részhalmazát foglalja le *tárolási szolgáltatásként*. A Storage Services tárolja a bérlői adattárakban lévő, a központi telepítés csomópontjainak megfelelő köteteken található megosztásokat.

Felhőbeli operátorként korlátozott mennyiségű tárterülettel dolgozhat a szolgáltatással. A tárterület mennyiségét az Ön által megvalósított megoldás határozza meg. A megoldást a SZÁMÍTÓGÉPGYÁRTÓ gyártója adja meg többcsomópontos megoldás használata esetén, vagy azt a hardver adja meg, amelyre telepíti a Azure Stack Development Kit (ASDK).

Mivel Azure Stack hub nem támogatja a tárolókapacitás bővítését, fontos, hogy [Figyelje](#monitor-shares) a rendelkezésre álló tárolót a hatékony műveletek fenntartása érdekében.

Ha a megosztás fennmaradó szabad kapacitása korlátozott lesz, tervezze meg [a rendelkezésre álló terület felügyeletét](#manage-available-space) , hogy megakadályozza a megosztások kapacitásának kihasználását.

A kapacitás felügyeletének lehetőségei a következők:
- Kapacitás visszaigénylése.
- Tároló áttelepítése.

A megosztás 100%-os kihasználtsága esetén a Storage szolgáltatás már nem működik az adott megosztásban. Ha segítséget szeretne kérni a megosztási műveletek visszaállításához, forduljon a Microsoft ügyfélszolgálatához.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>A kötetek és megosztások, tárolók és lemezek ismertetése
### <a name="volumes-and-shares"></a>Kötetek és megosztások
A *Storage szolgáltatás* particionálja a rendelkezésre álló tárolót különálló, egyenlő kötetekre, amelyek a bérlői adattároláshoz vannak lefoglalva. A kötetek száma megegyezik a Azure Stack hub üzemelő példányában lévő csomópontok számával:

- Négy csomópontos üzemelő példányban négy kötet található. Minden kötet egyetlen megosztással rendelkezik. Többcsomópontos telepítés esetén a megosztások száma nem csökken, ha egy csomópont el lett távolítva, vagy ha meghibásodik.
- Ha a ASDK használja, egyetlen kötet egyetlen megosztással rendelkezik.

Mivel a Storage szolgáltatás megosztása a tárolási szolgáltatások kizárólagos használatára szolgál, nem szabad közvetlenül módosítani, hozzáadni vagy eltávolítani a megosztásokon lévő fájlokat. Csak a tárolási szolgáltatások működhetnek az ezekben a kötetekben tárolt fájlokban.

A kötetek megosztása a bérlői adatforgalomban. A bérlői adatok közé tartoznak az oldal-Blobok, a Blobok, a Blobok, a táblák, a várólisták, az adatbázisok és a kapcsolódó metaadatok. Mivel a tárolási objektumok (Blobok stb.) egyenként egyetlen megosztáson belül találhatók, az egyes objektumok maximális mérete nem haladhatja meg a megosztás méretét. Az új objektum maximális mérete attól függ, hogy az új objektum létrehozásakor a megosztásban maradó tárterület nem használható.

Ha egy megosztás kevés a szabad területtel, és nem sikerül a lemezterület felszabadítása, vagy nem [érhető el,](#reclaim-capacity) Azure stack hub-felhő operátorai áttelepíthetik a blob-tárolókat egyik megosztásról a másikra.

További információ arról, hogy a bérlői felhasználók hogyan működnek a Azure Stack hub blob Storage [szolgáltatásával: Azure stack hub Storage Services](/azure-stack/user/azure-stack-storage-overview).

### <a name="containers"></a>Tárolók
A bérlői felhasználók a Blobok tárolására szolgáló tárolókat hoznak létre. Bár a felhasználók határozzák meg a Blobok elhelyezését, a Storage szolgáltatás algoritmus használatával határozza meg, hogy melyik köteten helyezi el a tárolót. Az algoritmus általában kiválasztja a legnagyobb szabad területtel rendelkező kötetet.  

Miután egy blobot elhelyez egy tárolóban, a blob több helyet is felhasználhat. Amikor új blobokat ad hozzá, és a meglévő Blobok növekednek, a köteten lévő szabad terület csökken.  

A tárolók nem korlátozódnak egyetlen megosztásra. Ha a tárolóban lévő kombinált blob-adatmennyiség a rendelkezésre álló terület 80%-át vagy annál nagyobb hányadát használja, a tároló *túlcsordulási* módba kerül. Ha a tároló túlcsordulási módban van, a tárolóban létrehozott összes új blobot egy másik, elegendő lemezterülettel rendelkező kötethez rendeli a rendszer. Az idő múlásával egy túlcsordulási módban lévő tároló tartalmazhat több köteten terjesztett blobokat.

Ha a köteten rendelkezésre álló terület 80 százalékát (majd 90 százalékát) használja, a rendszer riasztásokat küld a Azure Stack hub felügyeleti portálján. A felhőalapú operátoroknak át kell tekinteniük a rendelkezésre álló tárolókapacitást, és meg kell tervezniük a tartalom újraelosztását. A tárolási szolgáltatás nem működik, ha a lemez 100%-ban használatban van, és nincs további riasztás.

### <a name="disks"></a>Lemezek
A virtuális gép (VM) lemezeit a rendszer a bérlők számára adja hozzá a tárolóhoz, és egy operációsrendszer-lemezt is tartalmaz. A virtuális gépek egy vagy több adatlemezt is tartalmazhatnak. Mindkét típusú lemez blobként van tárolva. A bérlőknek szóló útmutatás az egyes lemezek külön tárolóba helyezése a virtuális gép teljesítményének növelése érdekében.

- Minden olyan tároló, amely tartalmaz egy lemezt vagy egy oldal blobot, egy virtuális gépről egy csatlakoztatott tárolónak tekintendő a lemezt birtokló virtuális gép számára.
- Egy olyan tároló, amely nem tárol lemezeket egy virtuális gépről, ingyenes tárolónak minősül.

A rögzített tárolók lemezterületének felszabadítására vonatkozó beállítások korlátozottak. További információ: VM- [lemezek áthelyezése](#move-vm-disks).

>[!TIP]  
> A Felhőbeli operátorok nem kezelik közvetlenül a lemezeket, amelyek olyan virtuális gépekhez vannak csatolva, amelyeket a bérlők hozzáadhatnak a tárolóhoz. Ha azonban a tárolási megosztások tárterületét tervezi kezelni, hasznos lehet megérteni, hogy a lemezek hogyan kapcsolódnak a tárolók és a megosztásokhoz.

## <a name="monitor-shares"></a>Megosztások figyelése
A megosztások figyeléséhez használja a Azure PowerShell vagy a felügyeleti portált, így megismerheti, hogy a szabad terület korlátozott-e. A portál használata esetén riasztást kap a kevés lemezterülettel rendelkező megosztásokról.

### <a name="use-powershell"></a>A PowerShell használata
Felhőbeli kezelőként a PowerShell `Get-AzsStorageShare` parancsmag használatával figyelheti a megosztás tárolási kapacitását. A parancsmag az egyes megosztásokon a teljes, lefoglalt és szabad területet adja vissza bájtban.

![Példa: a megosztások szabad területének visszaküldése](media/azure-stack-manage-storage-shares/free-space.png)

- **Teljes kapacitás**: a megosztáson elérhető teljes tárterület (bájt). Ez a terület a tárolási szolgáltatások által karbantartott adatok és metaadatok esetében használatos.
- **Felhasznált kapacitás**: az adatmennyiség (bájtban), amelyet a bérlői adatokat tároló és a hozzájuk társított metaadatokat tartalmazó fájlok összes egysége használ.

### <a name="use-the-administrator-portal"></a>A felügyeleti portál használata
Felhőbeli operátorként a felügyeleti portálon megtekintheti az összes megosztás tárolási kapacitását.

1. Jelentkezzen be a [felügyeleti portálra](https://adminportal.local.azurestack.external).
2. Válassza a **minden szolgáltatás** > **Storage** > **fájlmegosztás** lehetőséget a fájlmegosztás listájának megnyitásához, ahol megtekintheti a használati adatokat.

    ![Példa: Storage file shares in Azure Stack hub felügyeleti portál](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **Összesen**: a megosztáson elérhető teljes tárterület (bájtban). Ez a terület a tárolási szolgáltatások által karbantartott adatok és metaadatok esetében használatos.
   - **Használatban**: az adatmennyiség bájtban kifejezve, amelyet a bérlői adatokat és a hozzájuk társított metaadatokat tároló fájlokból származó összes egység használ.

### <a name="storage-space-alerts"></a>Tárolóhelyek – riasztások
A felügyeleti portál használata esetén riasztást kap a kevés lemezterülettel rendelkező megosztásokról.

> [!IMPORTANT]
> Felhőbeli operátorként meg kell akadályozni, hogy a megosztások teljes körű használatot érjenek el. A megosztás 100%-os kihasználtsága esetén a Storage szolgáltatás már nem működik az adott megosztásban. A szabad terület és a visszaállítási műveletek a 100 százalékos kihasználtságú megosztáson való helyreállításához forduljon a Microsoft ügyfélszolgálatához.

* **Figyelmeztetés**: Ha egy fájlmegosztás több mint 80%-ot használ, *figyelmeztető* riasztás jelenik meg a felügyeleti portálon:

  ![Példa: figyelmeztető riasztás a Azure Stack hub felügyeleti portálján](media/azure-stack-manage-storage-shares/alert-warning.png)

* **Kritikus**: Ha egy fájlmegosztás több mint 90%-ot használ, *kritikus* riasztást kap a felügyeleti portálon:

  ![Példa: kritikus riasztás az Azure Stack hub felügyeleti portálján](media/azure-stack-manage-storage-shares/alert-critical.png)

* **Részletek megtekintése**: a felügyeleti portálon megnyithatja a riasztások részleteit a kockázatcsökkentő beállítások megtekintéséhez:

  ![Példa: riasztás részleteinek megtekintése az Azure Stack hub felügyeleti portálján](media/azure-stack-manage-storage-shares/alert-details.png)

## <a name="manage-available-space"></a>Szabad terület kezelése
Ha tárterületet szabadít fel egy megosztáson, először a legkevésbé invazív metódusokat használja. Próbálja ki például, hogy a tároló átmigrálása előtt próbálkozzon a terület visszaigénylésével.  

### <a name="reclaim-capacity"></a>Kapacitás visszaigénylése
*Ez a beállítás a többcsomópontos telepítésekre és a Azure Stack Development Kit is vonatkozik.*

A törölt bérlői fiókok által használt kapacitást visszaállíthatja. A rendszer automatikusan visszaállítja ezt a kapacitást az [adatmegőrzési időszak](azure-stack-manage-storage-accounts.md#set-the-retention-period) elérésekor, vagy azonnal visszaigényelheti azt.

További információkért tekintse meg a [Azure stack hub Storage-fiókok kezelése](azure-stack-manage-storage-accounts.md#reclaim)című témakör "kapacitás visszaigénylése" című szakaszát.

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
1. Győződjön meg arról, hogy van [Azure PowerShell telepítve és konfigurálva](https://azure.microsoft.com/documentation/articles/powershell-install-configure/). További információ: Azure- [erőforrások kezelése Azure PowerShell használatával](https://go.microsoft.com/fwlink/?LinkId=394767).
2. Vizsgálja meg a tárolót, és Ismerje meg, hogy az áttelepíteni kívánt megosztáson milyen adatelemek találhatók. A köteten áttelepítéshez legmegfelelőbb jelölt tárolók azonosításához használja a `Get-AzsStorageContainer` parancsmagot:

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

5. Az áttelepítési feladatok állapotának vizsgálatához használja a feladatok AZONOSÍTÓját. A tároló áttelepítése után a **MigrationStatus** a *Befejezés*értékre van állítva.

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![Példa: áttelepítési állapot](media/azure-stack-manage-storage-shares/migration-status1.png)

6. Egy folyamatban lévő áttelepítési feladatot is megszakíthat. A megszakított áttelepítési feladatok aszinkron módon lesznek feldolgozva. A lemondásokat a $jobid használatával követheti nyomon:

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![Példa: visszaállítási állapot](media/azure-stack-manage-storage-shares/rollback.png)

7. Az áttelepítési állapot *megszakítása*előtt a 6. lépésből is futtathatja a parancsot:  

    ![Példa: megszakított állapot](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>VM-lemezek áthelyezése
*Ez a beállítás csak Azure Stack hub integrált rendszerekre vonatkozik.*

A tárhely kezelésének legszélsőségesebb módszere a virtuálisgép-lemezek áthelyezését jelenti. Mivel egy csatolt tároló (amely virtuálisgép-lemezt tartalmaz) áthelyezése összetett, a művelet végrehajtásához forduljon a Microsoft támogatási szolgálatához.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a virtuális gépek felhasználók számára történő felajánlásáról, tekintse meg a [Azure stack hub tárterület-kapacitásának kezelése](azure-stack-tutorial-tenant-vm.md)című témakört.
