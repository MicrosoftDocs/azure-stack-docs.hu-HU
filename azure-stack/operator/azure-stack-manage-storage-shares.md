---
title: A tárolási kapacitás kezelése a Azure Stackban | Microsoft Docs
description: Azure Stack tárolókapacitásának és rendelkezésre állási tárterületének figyelése és kezelése Azure Stackhoz.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 48452a9a5c02bca8e99b7769e90495544bd95c0e
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829504"
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Azure Stack tárolási kapacitásának kezelése 

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Az ebben a cikkben található információk segítenek a felhő-kezelő Azure Stack a Azure Stack üzemelő példány tárolókapacitásának figyelését és kezelését. A Azure Stack Storage-infrastruktúra a **tárolási szolgáltatásokhoz**használt Azure stack üzemelő példány teljes tárolókapacitásának részhalmazát foglalja le. A tárolási szolgáltatások a környezet csomópontjainak megfelelő köteteken, megosztásokban tárolják a bérlői adatokat.

Felhőbeli operátorként korlátozott mennyiségű tárterülettel dolgozhat a szolgáltatással. A tárterület mennyiségét az Ön által megvalósított megoldás határozza meg. A megoldást az OEM-gyártója több csomópontos megoldás használata esetén, vagy azon hardverek esetében adja meg, amelyre telepíti a Azure Stack Development Kit.

Mivel Azure Stack nem támogatja a tárolási kapacitás bővítését, fontos a rendelkezésre álló tár [figyelése](#monitor-shares) a hatékony műveletek fenntartása érdekében.  

Ha a megosztás fennmaradó szabad kapacitása korlátozott, tervezze meg a [tárterületet](#manage-available-space) , hogy megakadályozza a megosztások kapacitásának kifogyása.

A kapacitás kezelésének lehetőségei a következők:
- Kapacitás visszaigénylése
- Tároló migrálása

A megosztás 100%-os kihasználtsága esetén a Storage szolgáltatás már nem működik az adott megosztásban. Ha segítséget szeretne kérni a megosztási műveletek visszaállításához, forduljon a Microsoft ügyfélszolgálatához.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>A kötetek és megosztások, tárolók és lemezek ismertetése
### <a name="volumes-and-shares"></a>Kötetek és megosztások
A *Storage szolgáltatás* particionálja a rendelkezésre álló tárolót különálló és egyenlő kötetekre, amelyek a bérlői adattároláshoz vannak lefoglalva. A kötetek száma megegyezik a Azure Stack üzemelő példányban található csomópontok számával:

- Négy csomópontos üzemelő példányon négy kötet található. Minden kötet egyetlen megosztással rendelkezik. Több csomópontos telepítés esetén a megosztások száma nem csökken, ha egy csomópontot eltávolítanak vagy meghibásodnak.
- Ha a Azure Stack fejlesztői készletet használja, egyetlen, egyetlen megosztással rendelkező kötet van.

Mivel a Storage szolgáltatás megosztása a tárolási szolgáltatások kizárólagos használatára szolgál, nem szabad közvetlenül módosítani, hozzáadni vagy eltávolítani a megosztásokon lévő fájlokat. Csak a tárolási szolgáltatások működhetnek az ezekben a kötetekben tárolt fájlokban.

A kötetek megosztása a bérlői adatforgalomban. A bérlői adatok közé tartoznak az oldal-Blobok, a Blobok, a Blobok, a táblák, a várólisták, az adatbázisok és a kapcsolódó metaadatok. Mivel a Storage-objektumok (Blobok stb.) egyenként egyetlen megosztáson belül találhatók, az egyes objektumok maximális mérete nem haladhatja meg a megosztás méretét. Az új objektumok maximális mérete attól függ, hogy az új objektum létrehozásakor a megosztásban maradó kapacitás nem használt területnek minősül-e.

Ha a megosztás kevés a szabad területtel, és a [lemezterületet](#reclaim-capacity) nem sikerült vagy nem sikerül elérni, akkor a Azure stack Cloud operátor áttelepítheti a blob-tárolókat egyik megosztásból a másikba.

- További információ arról, hogy a bérlői felhasználók hogyan működnek a blob Storage-ban Azure Stackban: [Azure stack Storage Services](/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services).


### <a name="containers"></a>Containers
A bérlői felhasználók a Blobok tárolására szolgáló tárolókat hoznak létre. Míg a felhasználó dönti el, hogy mely tárolót helyezi el a Blobok, a Storage szolgáltatás algoritmus használatával határozza meg, hogy melyik kötetre helyezi a tárolót. Az algoritmus általában kiválasztja a legnagyobb szabad területtel rendelkező kötetet.  

Miután egy blobot elhelyez egy tárolóban, a blob több helyet is felhasználhat. Amikor új blobokat ad hozzá, és a meglévő Blobok növekednek, a köteten lévő szabad terület csökken.  

A tárolók nem korlátozódnak egyetlen megosztásra. Ha a tárolóban lévő kombinált blob-adatmennyiség növekszik a rendelkezésre álló terület 80%-a vagy több, akkor a tároló *túlcsordulási* módba kerül. Túlcsordulási módban a tárolóban létrehozott új Blobok egy másik, elegendő lemezterülettel rendelkező kötethez vannak rendelve. Az idő múlásával egy túlcsordulási módban lévő tároló tartalmazhat több köteten terjesztett blobokat.

Ha 80%-ot, majd a kötetben lévő szabad terület 90%-át használja, a rendszer riasztásokat küld a Azure Stack felügyeleti portálon. A Felhőbeli operátoroknak át kell tekinteniük a rendelkezésre álló tárolókapacitást, és meg kell tervezniük a tartalom újraelosztását. A tárolási szolgáltatás nem működik, ha egy lemez 100%-os használatban van, és nincs további riasztás.

### <a name="disks"></a>Lemezek
A virtuálisgép-lemezeket a rendszer bérlők számára adja hozzá a tárolóhoz, és egy operációsrendszer-lemezt is tartalmaz. A virtuális gépek egy vagy több adatlemezt is tartalmazhatnak. Mindkét típusú lemez blobként van tárolva. A bérlőknek szóló útmutatás az egyes lemezek külön tárolóba helyezése a virtuális gép teljesítményének növelése érdekében.
- Minden olyan tároló, amely egy virtuális gépről tartalmaz egy lemezt (blobot), egy csatlakoztatott tárolónak tekintendő a lemezt birtokló virtuális géphez.
- Egy olyan tároló, amely nem tartalmaz lemezeket egy virtuális gépről, ingyenes tárolónak minősül.

A rögzített tárolók lemezterületének felszabadítására vonatkozó beállítások [korlátozottak](#move-vm-disks).
> [!TIP]  
> A Felhőbeli operátorok nem kezelik közvetlenül a virtuális gépekhez (VM) csatolt lemezeket, amelyeket a bérlők hozzáadhatnak a tárolóhoz. A tárolási megosztások tárterületének megtervezése során azonban érdemes lehet megérteni, hogy a lemezek hogyan kapcsolódnak a tárolók és a megosztásokhoz.

## <a name="monitor-shares"></a>Megosztások figyelése
A PowerShell vagy a felügyeleti portál használatával figyelheti a megosztásokat, így megtudhatja, hogy a szabad terület korlátozott-e. A portál használata esetén riasztást kap a kevés lemezterülettel rendelkező megosztásokról.    

### <a name="use-powershell"></a>A PowerShell használata
Felhőbeli kezelőként a PowerShell **Get-AzsStorageShare** parancsmag használatával figyelheti a megosztás tárolási kapacitását. A Get-AzsStorageShare parancsmag az egyes megosztásokban lévő teljes, lefoglalt és szabad területet adja vissza bájtban.   
![Példa: Szabad terület visszaadása a @ no__t-0 megosztáshoz

- A **teljes kapacitás** a megosztáson elérhető teljes tárterület. Ez a terület a tárolási szolgáltatások által karbantartott adatok és metaadatok esetében használatos.
- A **felhasznált kapacitás** a bérlői adatokat és a hozzájuk tartozó metaadatokat tároló fájlokból származó összes mennyiség bájtban kifejezett adat mennyisége.

### <a name="use-the-administrator-portal"></a>A felügyeleti portál használata
Felhőbeli operátorként a felügyeleti portálon megtekintheti az összes megosztás tárolási kapacitását.

1. Jelentkezzen be a [felügyeleti portálra](https://adminportal.local.azurestack.external).
2. Válassza a **minden szolgáltatás** > **Storage** > **fájlmegosztás** elemet a fájlmegosztás listájának megnyitásához, ahol megtekintheti a használati adatokat. 

    ![Példa: Storage-fájlmegosztás](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - Az **összeg** a megosztáson elérhető teljes tárterület. Ez a terület a tárolási szolgáltatások által karbantartott adatok és metaadatok esetében használatos.
   - A **használatban** lévő adatok mennyisége bájtban kifejezve, amelyet a bérlői adatokat tároló fájlok és a hozzájuk társított metaadatok által használt összes egység használ.

### <a name="storage-space-alerts"></a>Tárolóhelyek – riasztások
A felügyeleti portál használata esetén riasztást kap a kevés lemezterülettel rendelkező megosztásokról.

> [!IMPORTANT]
> Felhőbeli operátorként a megosztásokat a teljes használat eléréséhez tartsa. A megosztás 100%-os kihasználtsága esetén a Storage szolgáltatás már nem működik az adott megosztásban. A szabad terület és a visszaállítási műveletek a 100%-os kihasználtságú megosztáson való helyreállításához forduljon a Microsoft ügyfélszolgálatához.

**Figyelmeztetés**: Ha egy fájlmegosztás több mint 80%-ot használ, a felügyeleti portálon *figyelmeztető* riasztás jelenik meg: @no__t – 0Example: Figyelmeztető riasztás @ no__t-0


**Kritikus**: Ha egy fájlmegosztás több mint 90%-ot használ, *kritikus* riasztás jelenik meg a felügyeleti portálon: @no__t – 0Example: Kritikus riasztás @ no__t-0

**Részletek megtekintése**: A felügyeleti portálon megnyithatja a riasztások részleteit a kockázatcsökkentő beállítások megtekintéséhez: @no__t – 0Example: Riasztás részleteinek megtekintése @ no__t-0


## <a name="manage-available-space"></a>Szabad terület kezelése
Ha szükség van egy megosztás szabad területére, először a legkevésbé invazív metódusokat használja. Próbálja ki például, hogy a tároló átmigrálása előtt próbálkozzon a terület visszaigénylésével.  

### <a name="reclaim-capacity"></a>Kapacitás visszaigénylése
*Ez a beállítás a több csomópontos üzemelő példányokra és a Azure Stack Development Kit is vonatkozik.*

A törölt bérlői fiókok által használt kapacitást visszaállíthatja. A rendszer automatikusan visszaállítja ezt a kapacitást az [adatmegőrzési időszak](azure-stack-manage-storage-accounts.md#set-the-retention-period) elérésekor, vagy azonnal visszaigényelheti azt.

További információ: [rejogcím kapacitása](azure-stack-manage-storage-accounts.md#reclaim) a Storage-erőforrások kezelése.

### <a name="migrate-a-container-between-volumes"></a>Tároló áttelepítése kötetek között
*Ez a beállítás csak a több csomópontos üzemelő példányokra vonatkozik.*

A bérlői használati szokások miatt egyes bérlői megosztások a többinél több területet használnak. Az eredmény olyan megosztás lehet, amely a viszonylag nem használt más megosztások előtt alacsony lemezterületet futtat.

Megpróbálhat lemezterületet felszabadítani egy túlhasználatos megosztáson azáltal, hogy manuálisan áttelepíti néhány BLOB-tárolót egy másik megosztásra. Több kisebb tárolót is áttelepíthet egyetlen megosztásra, amely képes az összes tárolására. Az áttelepítést az *ingyenes* tárolók áthelyezésére is használhatja. Az ingyenes tárolók olyan tárolók, amelyek nem tartalmaznak lemezt a virtuális gép számára.   

Az áttelepítés összevonja az új megosztás összes tárolójának blobját.

- Ha egy tároló túlcsordulási módot adott meg, és a blobokat további kötetekre helyezte, az új megosztásnak elegendő kapacitással kell rendelkeznie az áttelepített tároló összes blobjának tárolásához. Ide tartoznak a további megosztásokon található Blobok.

- A *Get-AzsStorageContainer PowerShell-* parancsmag csak a tároló kezdeti kötetén használt helyet azonosítja. A parancsmag nem azonosítja a további kötetekre helyezett Blobok által használt területet. Ezért előfordulhat, hogy a tárolók teljes mérete nem egyértelmű. Lehetséges, hogy egy tároló új megosztáson való összevonása az új megosztást olyan túlcsordulási állapotba tudja küldeni, amelyben további megosztásokra helyezi az adattárolást. Ennek eredményeképpen előfordulhat, hogy újra meg kell osztania a megosztásokat.

- Ha nincs engedélye egy erőforráscsoport számára, és a PowerShell használatával nem tudja lekérdezni a további köteteket a túlcsordulási adatforgalomhoz, akkor az ilyen erőforráscsoportok és tárolók tulajdonosával együtt kell megismernie az áttelepíteni kívánt adatmennyiséget az áttelepítés előtt.  

> [!IMPORTANT]
> A Blobok tárolóba történő áttelepítése egy offline művelet, amely a PowerShell használatát igényli. Amíg az áttelepítés befejeződik, az áttelepíteni kívánt tároló összes blobja offline állapotban marad, és nem használható. A Azure Stack frissítését is el kell végezni, amíg az összes folyamatban lévő áttelepítés be nem fejeződik.

#### <a name="to-migrate-containers-using-powershell"></a>Tárolók áttelepíthetők a PowerShell használatával
1. Győződjön meg arról, hogy van [Azure PowerShell telepítve és konfigurálva](https://azure.microsoft.com/documentation/articles/powershell-install-configure/). További információ: [Az Azure PowerShell használata az Azure Resource Manager eszközzel](https://go.microsoft.com/fwlink/?LinkId=394767).
2. Vizsgálja meg a tárolót, és Ismerje meg, hogy az áttelepíteni kívánt megosztáson milyen adatelemek találhatók. A köteten áttelepítéshez legmegfelelőbb jelölt tárolók azonosításához használja a **Get-AzsStorageContainer** parancsmagot:

   ```powershell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   Ezután vizsgálja meg $containers:

   ```powershell
   $containers
   ```

   ![Példa: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3. Azonosítsa az áttelepíteni kívánt tárolóra vonatkozó legjobb célhelyeket:

   ```powershell
   $destinationshare = ($shares | Sort-Object FreeCapacity -Descending)[0]
   ```

   Ezután vizsgálja meg $destinationshares:

   ```powershell 
   $destinationshares
   ```

   ![Példa: $destination megosztások](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Egy tároló áttelepítésének megkezdése. Az áttelepítés aszinkron módon történik. Ha a további tárolók áttelepítését az első áttelepítés befejeződése előtt indítja el, a feladattípus segítségével nyomon követheti az egyes állapotok állapotát.

   ```powershell
   $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
   ```

   Ezután vizsgálja meg $jobId. Az alábbi példában cserélje le a *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* -t a vizsgálni kívánt feladattípusra:

   ```powershell
   $jobId
   d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
   ```

5. Az áttelepítési feladatok állapotának vizsgálatához használja a feladatok AZONOSÍTÓját. A tároló áttelepítése után a **MigrationStatus** a **Befejezés**értékre van állítva.

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![Példa: Migrálás állapota](media/azure-stack-manage-storage-shares/migration-status1.png)

6. Egy folyamatban lévő áttelepítési feladatot is megszakíthat. A megszakított áttelepítési feladatok aszinkron módon lesznek feldolgozva. A lemondást a $jobid használatával követheti nyomon:

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![Példa: Visszaállítás állapota](media/azure-stack-manage-storage-shares/rollback.png)

7. A **6. lépésből is**futtathatja a parancsot, amíg az állapot megerősíti az áttelepítési feladatot:  

    ![Példa: Megszakított állapot](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>VM-lemezek áthelyezése
*Ez a beállítás csak a több csomópontos üzemelő példányokra vonatkozik.*

A terület kezelésére legszélsőségesebb módszer a virtuális gépek lemezeinek áthelyezése. Mivel egy csatolt tároló (amely egy virtuálisgép-lemezt tartalmaz) áthelyezése összetett, a művelet végrehajtásához forduljon Microsoft ügyfélszolgálatahoz.

## <a name="next-steps"></a>További lépések
További információ a [virtuális gépek felhasználók számára történő felajánlásáról](azure-stack-tutorial-tenant-vm.md).
