---
title: Kezelheti a tárolási kapacitást az Azure Stackben |} A Microsoft Docs
description: Figyelheti, és az Azure Stack-tárolási kapacitás és a rendelkezésre állási tárterület kezelése az Azure Stackhez.
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
ms.date: 03/29/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 8b367811622eafdc62b47aa85eda47bc6a81be14
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985129"
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Az Azure stack-beli tárolási kapacitás kezelése 

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ebben a cikkben található információk segítséget nyújt az Azure Stack felhő operátor figyelője és kezelése az Azure Stack üzemelő példányához tárolókapacitását. Az Azure Stack tárolási infrastruktúra foglalja le a teljes tárolási kapacitás használható az Azure Stack üzembe egy részhalmazát **tárolószolgáltatások**. A storage szolgáltatásokat a köteteken, amelyek megfelelnek a csomópontok a központi telepítési megosztás egy bérlők adatainak tárolása.

Felhő-felelősként korlátozott mennyiségű tárhely használata rendelkezik. A megoldás megvalósítása tárterület mérete határozza meg. A megoldás az OEM beszállítói egy több csomópontos megoldás használata esetén, vagy a hardvert, amelyre telepíti az Azure Stack Development Kit biztosítunk.

Mivel az Azure Stack nem támogatja a tárolási kapacitás bővítése, fontos, hogy [figyelő](#monitor-shares) hatékony, hogy a rendelkezésre álló tár karbantartása.  

Amikor egy megosztás fennmaradó szabad kapacitása korlátozott, tervezi, hogy [címtér kezelésére](#manage-available-space) , hogy elkerülje a megosztások kapacitás futását.

A kapacitás kezelése lehetőségek a következők:
- Kapacitás visszaigényléséhez
- Tároló áttelepítése

100 %-os használt fel, a storage-megosztás esetén a szolgáltatás már nem használt megosztást a functions. Segítségkérés a visszaállítási műveleteket a megosztás, forduljon a Microsoft ügyfélszolgálatához.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Kötetek és megosztások, tárolók és a lemezek ismertetése
### <a name="volumes-and-shares"></a>Kötetek és megosztások
A *társzolgáltatás* particionálja a rendelkezésre álló tár be különálló és egyenlő bérlői adatok tárolásához kiosztott köteteket. A kötetek száma egyenlő a csomópontok számát az Azure Stack üzemelő példányban:

- Egy négy csomópontos üzemelő példányon nincsenek négy köteteket. Minden kötetnek egyetlen megosztása. A több csomópontos központi telepítést, a megosztások száma nem csökken, ha egy csomópont eltávolított vagy hibásan.
- Az Azure Stack fejlesztői készletének használja, ha van egy kötetet, és egyetlen megosztása.

Mivel a storage szolgáltatás megosztások tárolási szolgáltatások kizárólagos használatát, meg kell közvetlenül nem módosítását, adja hozzá vagy távolít el a fájlokat, ha a megosztásokkal. Csak a tárolási szolgáltatások is működnie kell az ezeken a köteteken tárolt fájlokról.

Megosztások, kötetek bérlői adatok tárolásához. Bérlők adatainak magában foglalja a lapblobokat, blokkblobokat, hozzáfűző blobok, táblák, üzenetsorok, adatbázisok és kapcsolatos metaadat-tárolók. Egyetlen megosztása külön-külön tartalmazza a tárolási objektum (blobok, stb.), mert minden egyes objektum maximális mérete nem haladhatja meg a fájlmegosztás méretét. Új objektumok maximális mérete attól függ, hogy a kapacitás, amely az új objektum létrehozásakor, fel nem használt területe olyan megosztáson marad.

Ha egy megosztást alacsony a szabad terület és műveletek [VISSZAIGÉNYLÉSE](#reclaim-capacity) terület nem sikeres, illetve érhető el, az Azure Stack-felhő üzemeltetője telepíthet át a blob-tárolók egy megosztást a másikra.

- Bérlő felhasználók miként használják a blob storage, az Azure Stack használatával kapcsolatos információkért lásd: [Azure Stack tárolószolgáltatások](/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services).


### <a name="containers"></a>Containers
Bérlő felhasználók létrehozása, amely majd Blobadatok tárolására szolgáló tárolókat. Amíg a felhasználó úgy dönt, hogy melyik tárolóban helyezhető el a blobokat, a storage szolgáltatás segítségével egy olyan algoritmust határozza meg, melyik köteten helyezi a tárolót. Az algoritmus általában úgy dönt, a kötet a legtöbb szabad területtel rendelkező.  

Miután egy blobot egy tárolóban kerül, a, hogy a blob nagyobb területet növelhető. Amikor új blobok és a meglévő blobok növekszik, a kötet, amely tartalmazza a tároló zsugorítja rendelkezésre álló területet.  

Tárolók az egyetlen megosztása nem korlátozódnak. A kombinált blob-tárolóban lévő adatokat használat 80 %-os vagy több, a rendelkezésre álló nő, ha beírja-e a tároló *túlcsordulás* mód. Túlcsordulás módban bármely új blobok létrehozott a tárolóban kiosztott elegendő szabad hellyel rendelkező másik kötetet. Idővel túlcsordulás módban egy tároló lehet a blobokat, amelyekre több kötet vannak elosztva.

80 %-át, és 90 %-a rendelkezésre álló területet a kötet használata esetén a rendszer riasztást küld az Azure Stack rendszergazdai portálon. Felhő üzemeltetői kell tekintse át a rendelkezésre álló tár kapacitása, és a tartalom újraegyensúlyozására megtervezése. A storage szolgáltatás leáll, ha egy lemez, 100 %-át használja, és nincsenek további riasztások aktiválódnak.

### <a name="disks"></a>Lemezek
Virtuálisgép-lemezek bérlők által hozzáadott tárolókhoz, és tartalmazza az operációsrendszer-lemez. Virtuális gépek egy vagy több adatlemezt is lehet. Mindkét típusú lemezek lap blobként vannak tárolva. Az útmutató a bérlők számára, hogy helyezze el az egyes lemezek teljesítményét a virtuális gép egy külön tárolóba.
- Minden tároló, amely egy virtuális gép lemeze (lapblob) egy csatlakoztatott tároló, a virtuális géphez, a lemez birtokló számít.
- Egy tároló, amely nem tartalmaz minden olyan lemezt egy virtuális gépről egy ingyenes tároló számít.

Szabadítson fel lemezterületet egy csatlakoztatott tárolón beállítás [korlátozódnak](#move-vm-disks).
> [!TIP]  
> Felhő üzemeltetői nem kezel közvetlenül lemezek, virtuális gépek (VM), amely a bérlők adhat hozzá egy tárolóba van csatolva. Azonban megtervezésekor kezelni a területet a storage-megosztásokat, érdemes lehet tudni, hogyan tárolók és -megosztások kapcsolódnak-e a lemezek használatának.

## <a name="monitor-shares"></a>A figyelő megosztások
Használja a Powershellt vagy a felügyeleti portálon figyelése a megosztásokat, így megismerheti, hogy ha a szabad terület korlátozva. Ha a portál, megosztások, amelyek kevés a lemezterület kapcsolatos riasztásokat kap.    

### <a name="use-powershell"></a>A PowerShell használata
Felhő-felelősként, figyelemmel kísérheti a tárolási kapacitás, a PowerShell-lel megosztás **Get-AzsStorageShare** parancsmagot. A Get-AzsStorageShare parancsmag ad vissza, az egyes a megosztásokat az összes, a lefoglalt és a szabad lemezterület (bájt).   
![Példa: Szabad terület a megosztások visszaadása](media/azure-stack-manage-storage-shares/free-space.png)

- **Teljes kapacitás** a teljes területe, amely elérhető a megosztáson (bájt). Ez a terület az adat- és a tárolási szolgáltatások által kezelt metaadat szolgál.
- **Használt kapacitás** , amennyit a a fájlokat, a bérlői adatok és kapcsolódó metaadatok összes egységek által használt adatok (bájt).

### <a name="use-the-administrator-portal"></a>A felügyeleti portál
Felhő-felelősként minden megosztás tárkapacitása megtekintéséhez használhatja a felügyeleti portálon.

1. Jelentkezzen be a [felügyeleti portál](https://adminportal.local.azurestack.external).
2. Válassza ki **minden szolgáltatás** > **tárolási** > **fájlmegosztások** , ahol megtekintheti a használati adatok fájl megosztási listájának megnyitásához. 

    ![Példa: Tárolófájl-megosztások](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **TELJES** a teljes területe, amely elérhető a megosztáson (bájt). Ez a terület az adat- és a tárolási szolgáltatások által kezelt metaadat szolgál.
   - **HASZNÁLT** , amennyit a a fájlokat, a bérlői adatok és kapcsolódó metaadatok összes egységek által használt adatok (bájt).

### <a name="storage-space-alerts"></a>Tárolási hely riasztások
Ha a felügyeleti portál, megosztások, amelyek kevés a lemezterület kapcsolatos riasztásokat kap.

> [!IMPORTANT]
> A felhő felelősnek megosztások megtartása teljes használat eléri. 100 %-os használt fel, a storage-megosztás esetén a szolgáltatás már nem használt megosztást a functions. Szabad hely helyreállítása és visszaállítási műveletek egy megosztáson, amely 100 %-os használt fel, forduljon a Microsoft ügyfélszolgálatához.

**Figyelmeztetés**: Amikor egy fájlmegosztás több mint 80 %-os használt fel, kap egy *figyelmeztetés* riasztás a felügyeleti portálon: ![Példa: Figyelmeztető riasztás](media/azure-stack-manage-storage-shares/alert-warning.png)


**Kritikus fontosságú**: Amikor egy fájlmegosztás több mint 90 %-os használt fel, kap egy *kritikus* riasztás a felügyeleti portálon: ![Példa: Kritikus riasztás](media/azure-stack-manage-storage-shares/alert-critical.png)

**A részletek megtekintéséhez**: A felügyeleti portálon nyissa meg egy riasztás megtekintése a kockázatcsökkentési lehetőségek részleteit: ![Példa: Riasztás részleteinek megjelenítése](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>Rendelkezésre álló hely kezelése
Ha azt megosztáson lévő szabad hely szükséges, először használja a legkevésbé invazív módszerek. Például próbál jusson úgy dönt, hogy a tároló áttelepítése előtt.  

### <a name="reclaim-capacity"></a>Kapacitás visszaigényléséhez
*Ez a beállítás is több csomópontos központi telepítések, és az Azure Stack Development Kit vonatkozik.*

A törölt bérlő fiókok által használt kapacitás is visszaigényelhetők. Ez a kapacitás a program automatikusan vissza, ha az adatok [megőrzési időszak](azure-stack-manage-storage-accounts.md#set-the-retention-period) elérése vagy felszabadítani, azonnal működhet.

További információkért lásd: [kapacitás visszaigényléséhez](azure-stack-manage-storage-accounts.md#reclaim) a tárolási erőforrások kezelése.

### <a name="migrate-a-container-between-volumes"></a>Tároló áttelepítése kötetek között
*Ez a beállítás csak a több csomópontos központi telepítések vonatkozik.*

Bérlői használati mintákat, mert bérlői megosztások használja, mint a többi több helyet. Az eredmény egy futó alacsony előtti más a megosztások, viszonylag nem használt megosztást is lehet.

Próbálkozzon egy másik megosztásba néhány blobtárolók manuális áttelepítésével színvonalát megosztáson hely felszabadítása érdekében. Több kisebb méretű tároló, amely kapacitása az összes tárolásához egyetlen megosztásra áttelepítheti. Áttelepítés használatával áthelyezése *ingyenes* tárolók. Ingyenes tárolók olyan tárolók, amelyek nem tartalmaznak egy lemezt egy virtuális géphez.   

Áttelepítés az új megosztásban lévő összes tárolók blob összesíti.

- Ha egy tároló túlcsordulás üzemmódba lépett, és a további kötetek helyezte a blobok, az új megosztás minden a tároló áttelepítése a blobok tárolására elegendő kapacitással kell rendelkeznie. Ez magában foglalja a további megosztások lévő blobokat.

- A PowerShell-parancsmag *Get-AzsStorageContainer* csak egy tárolót a kezdeti kötetén használt lemezterület azonosítja. A parancsmag nem határozza meg fűzendő további kötetek blobok által használt területet. A tároló teljes méretét, ezért nem feltétlenül nyilvánvaló. Akkor lehet, hogy egy tárolót, egy új megosztást összevonása küldhet, hogy új megosztás túlcsordult állapotot, ahol további megosztások se mají data helyezi. Ennek eredményeképpen előfordulhat, hogy kell megosztások újraegyensúlyozására újra.

- Ha nem rendelkezik engedélyekkel az erőforráscsoport és a PowerShell nem használható a további kötetek túlcsordulás adatok lekérdezésére, együttműködve tulajdonosának migrálása adatok migrálása előtt ezeket a erőforráscsoportok és tárolók megismerheti az adatok teljes mérete.  

> [!IMPORTANT]
> Egy tároló-blobok az áttelepítése nem offline művelet, amely a PowerShell használata szükséges. Áttelepítés befejeződéséig áttelepítésekor a tároló összes blobok offline állapotban maradnak, és nem használható. Emellett ne frissítése az Azure Stack, mindaddig, amíg az összes folyamatban lévő migrálása befejeződött.

#### <a name="to-migrate-containers-using-powershell"></a>PowerShell-lel a tárolók áttelepítéséhez
1. Győződjön meg arról, hogy [Azure PowerShell telepítését és konfigurálását](https://azure.microsoft.com/documentation/articles/powershell-install-configure/). További információ: [Az Azure PowerShell használata az Azure Resource Manager eszközzel](https://go.microsoft.com/fwlink/?LinkId=394767).
2. Vizsgálja meg a tároló, milyen adatokat a megosztáson található, amely az áttelepíteni kívánt van. A legjobb jelöltek tárolók egy kötet az áttelepítéshez azonosításához használja a **Get-AzsStorageContainer** parancsmagot:

   ```powershell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   Majd megvizsgálja a $containers:

   ```powershell
   $containers
   ```

   ![Példa: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3. Határozza meg, amely tárolja a tároló áttelepítése a legjobb cél megosztások:

   ```powershell
   $destinationshares = Get-AzsStorageShare -SourceShareName
   $shares[0].ShareName -Intent ContainerMigration
   ```

   Majd megvizsgálja a $destinationshares:

   ```powershell 
   $destinationshares
   ```

   ![Példa: $destination megosztások](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Indítsa el áttelepítési tárolóhoz. Az áttelepítés akkor aszinkron. Ha az első áttelepítés befejezése előtt további tárolókat migrálásának indul el, használja a feladat azonosítója egyes állapotának nyomon követését.

   ```powershell
   $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
   ```

   Majd megvizsgálja a $jobId. A következő példában cserélje le a *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* meg szeretné vizsgálni a feladatazonosító:

   ```powershell
   $jobId
   d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
   ```

5. Használja a Feladatazonosító az áttelepítési feladat állapotának ellenőrzése. Ha a tároló áttelepítése befejeződött, **MigrationStatus** értékre van állítva **Complete**.

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![Példa: Migrálás állapota](media/azure-stack-manage-storage-shares/migration-status1.png)

6. Egy folyamatban lévő migrálási feladat megszakítása Áttelepítési feladatok feldolgozása aszinkron módon megszakította. Megszakítás $jobid használatával követheti nyomon:

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![Példa: Visszaállítás állapota](media/azure-stack-manage-storage-shares/rollback.png)

7. A parancs a 6. lépés ismét futtathatja, amíg az állapot megerősíti, hogy az áttelepítési feladat **megszakított**:  

    ![Példa: Megszakított állapota](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Helyezze át a Virtuálisgép-lemezek
*Ez a beállítás csak a több csomópontos központi telepítések vonatkozik.*

Címtér kezelésére rendkívüli módszernél az áthelyezés virtuálisgép-lemezek. Mivel áthelyezése egy csatlakoztatott tároló (egy Virtuálisgép-lemez tartalmazó) összetett, forduljon a Microsoft Support Ez a művelet elvégzéséhez.

## <a name="next-steps"></a>További lépések
Tudjon meg többet [kínál a virtuális gépeket a felhasználók számára](azure-stack-tutorial-tenant-vm.md).
