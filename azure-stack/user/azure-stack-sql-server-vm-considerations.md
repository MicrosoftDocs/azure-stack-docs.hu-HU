---
title: Használja az SQL Server ajánlott eljárásokat és az Azure Stack virtuális gépek teljesítményének növelése érdekében |} A Microsoft Docs
description: Ez a cikk az SQL server ajánlott eljárások a teljesítmény növelése és az Azure Stack-beli virtuális gépeken az SQL Server optimalizálása.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 77e7e994e382e5e359ba133ccc9e5a9b35a74153
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985585"
---
# <a name="sql-server-best-practices-to-optimize-performance-in-azure-stack"></a>SQL server – gyakorlati tanácsok az Azure Stackben teljesítményének optimalizálása

Ez a cikk az SQL server ajánlott eljárások az SQL Server optimalizálása és a Microsoft Azure Stack virtuális gépek teljesítményének növelése érdekében. Amikor az Azure Stack virtuális gépeken futó SQL Server, az azonos adatbázis teljesítmény-finomhangolási beállításokat használják alkalmazható az SQL Server egy helyi kiszolgálói környezetben. A relációs adatbázisok az Azure Stack-felhőben a teljesítmény számos tényezőtől függ. Tényezők közé tartozik a családba tartozó virtuális gépek és az adatlemezek konfigurációja.

Az SQL Server-rendszerképeken létrehozásakor [fontolja meg az Azure Stack portálon a virtuális gépek kiépítése](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Töltse le az SQL IaaS-bővítményt a Marketplace-kezelés az Azure Stack felügyeleti portálon, és töltse le a választott SQL virtuális gép virtuális merevlemezek (VHD). Ezek közé tartozik a SQL2014SP2 SQL2016SP1 és SQL2017.

> [!NOTE]  
> A cikk azt ismerteti, hogyan helyezhet üzembe egy SQL Server virtuális gépet a globális Azure portal használatával, miközben az útmutatót is vonatkozik az Azure Stack a következő eltérésekkel: SSD az operációsrendszer-lemez nem érhető el, nem érhetők el a felügyelt lemezek és tárolási konfigurációt kisebb különbségek vannak.

Bevezetés a *ajánlott* Azure Stack virtuális gépeken az SQL Server teljesítményének Ez a cikk a fókuszt. Ha a számítási feladatok nem szabadulnak, minden ajánlott optimalizálási nem feltétlenül igényelnek. A teljesítményigények és terhelési mintázatok megfontolnia, kiértékelheti, hogy ezekkel az ajánlásokkal.

> [!NOTE]  
> Teljesítmény kapcsolatos útmutató az SQL Server Azure-beli virtuális gépeken, tekintse meg [Ez a cikk](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="checklist-for-sql-server-best-practices"></a>Ellenőrzőlista az SQL server – gyakorlati tanácsok

Az alábbi ellenőrzőlista az Azure Stack virtuális gépeken az SQL Server az optimális teljesítmény van:


|Terület|Optimalizálási lehetőségek|
|-----|-----|
|Virtuális gép mérete |[DS3](azure-stack-vm-sizes.md) vagy újabb verziója az SQL Server Enterprise Edition.<br><br>[DS2](azure-stack-vm-sizes.md) vagy újabb SQL Server Standard edition és a Web edition.|
|Storage |Használjon, amely támogatja a virtuális gépek családjába [prémium szintű storage](azure-stack-acs-differences.md).|
|Lemezek |Legalább két adatlemezt (egy a naplófájlok) és a egy adatfájlt és a TempDB használja, és válassza ki a lemez méretét a kapacitásigények alapján. Állítsa az alapértelmezett fájlhelyek adatok ezeket a lemezeket az SQL Server telepítése során.<br><br>Ne használja az operációs rendszer vagy az ideiglenes lemezek adatbázistár vagy a naplózás.<br>STRIPE-több Azure-adatlemezek beolvasni a tárolóhelyek segítségével nagyobb IO átviteli sebesség.<br><br>Dokumentált foglalási méretű formátumban.|
|I/O|Engedélyezze az adatfájlok azonnali fájl inicializálása.<br><br>Korlátozza a viszonylag kis rögzített lépésekkel (64 MB - 256 MB) adatbázisok automatikus növekedése.<br><br>Tiltsa le a többletterheléshez az adatbázison.<br><br>Alapértelmezett biztonsági mentés és az adatbázis fájlhelyek az adatlemezeket, nem az operációsrendszer-lemez beállításához.<br><br>Zárolt lapok engedélyezése.<br><br>Az SQL Server szervizcsomagot és az összegző frissítések alkalmazása.|
|A szolgáltatás-specifikus|Készítsen biztonsági másolatot közvetlenül a blob storage (Ha használja az SQL Server-verzió által támogatott).|
|||

További információ a *hogyan* és *miért* ahhoz, hogy ezek az optimalizációk, tekintse át a részletek és útmutatás a következő szakaszok ismertetik.

## <a name="virtual-machine-size-guidance"></a>Útmutató a virtuális gép mérete

Teljesítmény-és nagybetűket alkalmazások, a következő [virtuálisgép-méretek](azure-stack-vm-sizes.md) ajánlott:

- **Az SQL Server Enterprise edition:** DS3 vagy újabb

- **SQL Server Standard edition és a Web edition:** DS2 vagy újabb

Az Azure Stack a DS, DS_v2 és virtuális gép családi sorozat közötti teljesítménybeli különbségek nem.

## <a name="storage-guidance"></a>Storage útmutatója

DS-sorozat (valamint a DSv2-sorozat) virtuális gépek az Azure Stack az operációs rendszer maximális lemez- és lemezteljesítményét (IOPS) biztosítják. A DS vagy DSv2 sorozatú virtuális gépet biztosít legfeljebb 1000 IOPS, az operációsrendszer-lemez és 2,300 Lemezenként adatokat, függetlenül attól, hogy a típus vagy a választott lemez mérete legfeljebb.

Adatok lemezteljesítmény egyedileg családba tartozó virtuálisgép-sorozat alapján határozza meg. Is [ebben a cikkben](azure-stack-vm-sizes.md) virtuálisgép-család sorozat adatátviteli sebességet az adatok azonosításához.

> [!NOTE]  
> Az éles számítási feladatokhoz válasszon egy DS vagy DSv2 sorozatú virtuális gépet úgy, hogy a lehetséges maximális iops értéke az operációs rendszer adja meg a lemez és adatlemezek.

Tárfiók létrehozása az Azure Stackben, ha a georeplikáció beállítás nem befolyásolja, mert ez a funkció nem érhető el az Azure Stackben.

## <a name="disks-guidance"></a>Lemezek útmutató

Az Azure Stack virtuális gépen három fő lemez típusa van:

- **Operációsrendszer-lemez:** Az Azure Stack-virtuális gép létrehozásakor a platform csatolja legalább egy lemezt (kerülhetnek a **C** meghajtó) az operációsrendszer-lemez a virtuális géphez. Ez a lemez egy storage-ban lapblobként tárolt VHD.

- **Ideiglenes lemez:** Az Azure Stack virtuális gépek tartalmazhat egy újabb lemezt az ideiglenes lemez neve (kerülhetnek a **D** meghajtó). Ez az egy lemezt a csomóponton, amelyek ideiglenes terület is használható.

- **Adatlemezek:** Csatlakoztathat további lemezeket a virtuális gép, adatlemez, és ezek a lemezek lapblobként storage szolgáltatásban tárolódnak.

A következő szakaszok ismertetik a különböző lemezek használatához.

### <a name="operating-system-disk"></a>Operációsrendszer-lemez

Operációsrendszer-lemez, amelyek rendszerindító és a egy futó operációs rendszer verziója, csatlakoztassa a VHD-t és a rendszer a következő címkét: **C** meghajtó.

### <a name="temporary-disk"></a>Ideiglenes lemez

Az ideiglenes meghajtó, a következő címkét: a **D** meghajtó, nincs megőrizve. Ne tárolja a bármely hajtja megszakad, például a felhasználói adatbázis-fájlokat vagy a felhasználó tranzakciós naplófájlokat, az adatok a **D** meghajtót.

Azt javasoljuk, hogy az egyes adatlemezek biztosít az adatok akár 2,300 Lemezenként maximum való tárolásának TempDB adatlemezt.

### <a name="data-disks"></a>Adatlemezek

- **Adatlemezek használata az adatok és a naplófájlok.** Nem használ lemez szétosztottsága befolyásolhatja, ha a virtuális gépről, amely támogatja a Premium storage, ahol egy lemezen található a naplófájlokat, és tartalmazza, az adatok és a TempDB-fájlok a másik két adatlemezek használata. Az egyes adatlemezek számos IOPS és sávszélesség (MB/s) függően a virtuálisgép-család leírtak szerint [virtuális gépek méretei az Azure Stackben támogatott](azure-stack-vm-sizes.md). Ha egy lemez-csíkozást technika, például a tárolóhelyek használ, helyezze el az összes adathoz és naplófájlhoz ugyanazon a meghajtón (beleértve a TempDB adatbázis). Ez a konfiguráció lehetővé teszi az SQL Server feldolgozható, függetlenül attól, hogy melyik fájlt kell őket egy adott időpontban elérhető IOPS maximális számát.

> [!NOTE]  
> Amikor üzembe helyez egy SQL Server virtuális gépet a portálon, lehetősége van a tárolókonfiguráció szerkesztésekor. A konfigurációtól függően az Azure Stack egy vagy több lemezt konfigurál. Több lemez mostantól egyetlen tárolókészlet. Az adathoz és naplófájlhoz együtt találhatók ebben a konfigurációban.

- **Lemez szétosztottsága befolyásolhatja:** Nagyobb átviteli sebességet adjon hozzá további lemezeket, és használja a lemez szétosztottsága befolyásolhatja. A több adatlemez van szüksége, IOPS és sávszélesség szükséges a naplófájlok és az adatok és a TempDB-fájlok számának meghatározásához. Figyelje meg, hogy IOPS-korlátok egy adatlemezt a virtuális gép sorozatú alapul, és nem a virtuális gép mérete alapján. Hálózati sávszélesség-korlátozások, azonban a virtuális gép méretén alapulnak. Tekintse meg a táblák [virtuális gép méretét az Azure Stackben](azure-stack-vm-sizes.md) további részleteket talál. Kövesse az alábbi irányelveket:

  - Windows Server 2012 vagy újabb, illetve használjon [tárolóhelyek](https://technet.microsoft.com/library/hh831739.aspx) az alábbi útmutatókat:

    1. 64 KB-os (65 536 bájt) az online tranzakció-feldolgozási (OLTP) munkaterhelések és a 256 KB-os (262 144 bájt) az adattárházak munkaterhelése esetében a sávegységek miatt teljesítményromlás megelőzése érdekében a szektorszórás (stripe méret) értékre. Ez a PowerShell használatával kell beállítani.

    2. Állítsa be az oszlopok száma = fizikai lemezek számát. PowerShell-lel való konfigurálásakor, legfeljebb nyolc lemezek (nem a Server Manager felhasználói felületén).

       Például a következő PowerShell-a szektorszórás mérete 64 KB és 2. az oszlopok száma egy új tárolókészletet hoz létre:

       ```powershell  
       $PoolCount = Get-PhysicalDisk -CanPool $True
       $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

       New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple -UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
       ```

- A betöltés elvárásainak alapuló tárolókészlet társított lemezek számát határozza meg. Ne feledje, hogy a különböző virtuálisgép-méretek lehetővé teszik a különböző számú csatlakoztatott adatlemezekkel. További információkért lásd: [virtuális gépek méretei az Azure Stackben támogatott](azure-stack-vm-sizes.md).
- Annak érdekében, hogy a lehetséges maximális IOPS adatlemezek lekéréséhez a javaslat által támogatott az adatlemezek maximális száma hozzáadandó a [virtuálisgép-méret](azure-stack-vm-sizes.md) és lemez szétosztottsága befolyásolhatja.
- **NTFS foglalási egységek mérete:** Az adatlemez formázásakor javasoljuk, hogy használja-e egy 64 KB-os foglalási egységek mérete az adatok és a naplófájlok, valamint a TempDB.
- **Lemez felügyeleti eljárások:** Amikor egy adatlemez eltávolítása, állítsa le az SQL Server szolgáltatás módosítása során. Ezenkívül nem változnak a gyorsítótár beállításait a lemezeken, nem biztosít semmilyen teljesítménybeli javulást.

> [!WARNING]  
> Ezek a műveletek során az SQL-szolgáltatás leállítása sikertelen adatbázis-sérülést okozhat.

## <a name="io-guidance"></a>I/o-útmutató

- Érdemes megfontolni a azonnali fájl inicializálása a kezdeti fájl kiosztható szükséges idő csökkentése érdekében. Azonnali fájl inicializálása kihasználásához, az SQL Server (MSSQLSERVER) szolgáltatás fiók számára biztosítson **SE_MANAGE_VOLUME_NAME** , és adja azt hozzá a **kötet-karbantartási feladatok végrehajtása** biztonsági a házirend. Ha egy SQL Server platform-lemezképet használ az Azure-hoz, az alapértelmezett szolgáltatásfiók (**NT Service\MSSQLSERVER**) nem adódik hozzá a **kötet-karbantartási feladatok végrehajtása** biztonsági házirend. Más szóval azonnali fájl inicializálása nincs engedélyezve az SQL Server Azure platformlemezkép. Az SQL Server szolgáltatási fiókja való hozzáadása után a **kötet-karbantartási feladatok végrehajtása** biztonsági házirend, indítsa újra az SQL Server szolgáltatást. Ez a funkció használatára vonatkozó biztonsági szempontokat is lehet. További információkért lásd: [adatbázis fájl inicializálása](https://msdn.microsoft.com/library/ms175935.aspx).
- **Automatikus növekedésre** váratlan növekedés okokból van. Az adat- és naplófájlok növekedésének napi elszámolással, az adatbázisnál nem felügyeli. Automatikus növekedésre használatos, ha előre növelheti a fájl használatával a **mérete** váltani.
- Győződjön meg arról, hogy **többletterheléshez** elkerülése érdekében a felesleges terhelését, amelyek hátrányosan befolyásolhatja a teljesítményt le van tiltva.
- A telepítő alapértelmezett biztonsági mentés és adatbázis-fájlok helye. A javaslatok használata ebben a cikkben és a kiszolgálói tulajdonságok ablakában végezze el a módosításokat. Útmutatásért lásd: [megtekinteni vagy módosítani az alapértelmezett helyek az adat- és naplófájlok (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Az alábbi képernyőfelvételen látható, hogy ezek a változások hol mutat be:

    > ![Megtekinteni vagy módosítani az alapértelmezett helyek](./media/sql-server-vm-considerations/image1.png)

- Zárolt lapok csökkenteni az i/o- és lapozási tevékenységeket engedélyezése. További információkért lásd: [engedélyezése a memórialapok zárolása a memória beállítás (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Vegye figyelembe, hogy bármilyen adat fájlok tömörítése a bejövő és kimenő átviteléhez az Azure Stack biztonsági másolatokkal együtt.

## <a name="feature-specific-guidance"></a>Szolgáltatásspecifikus útmutató

Egyes központi telepítések további teljesítménybeli előnyök speciális konfigurációs technikákkal elérése érdekében. Az alábbi listában néhány SQL Server szolgáltatást, amely segítenek jobb teljesítményt emeli ki:

- **Azure biztonsági mentés** **storage.** Amikor a biztonsági mentések végrehajtásához az Azure Stack virtuális gépeken futó SQL Server, SQL Server biztonsági másolat URL-címre is használhatja. Ez a funkció az SQL Server 2012 SP1 CU2 kezdődően elérhető, és a biztonsági mentés a csatlakoztatott adatlemezekkel ajánlott.

    Ha a biztonsági mentési vagy visszaállítási használatával az Azure storage, kövesse a ajánlásai [SQL Server biztonsági másolat URL-cím ajánlott eljárások és hibaelhárítás](https://msdn.microsoft.com/library/jj919149.aspx) és [visszaállítani a biztonsági mentések Azure-ban tárolt Microsoft](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). Ezeket a biztonsági másolatokat használatával is automatizálható [automatikus biztonsági mentés az SQL Server az Azure Virtual machines gépeken](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Készítsen biztonsági másolatot az Azure Stack-tárolóba.** Készíthető az Azure Stack storage hasonlóan végezhető, mint a biztonsági mentés az Azure Storage. SQL Server Management Studio (SSMS) belüli biztonsági másolat létrehozásakor meg kell manuálisan adja meg a konfigurációs adatokat. Nem használható az SSMS a storage-tárolóba vagy a közös hozzáférésű Jogosultságkód létrehozása. SSMS csak csatlakozik Azure-előfizetések, nem az Azure Stack-előfizetést. Ehelyett szeretne létrehozni a tárfiókot, a tároló és a közös hozzáférésű Jogosultságkód vagy a PowerShell-lel az Azure Stack portálon.

    Amikor helyezze el az adatokat az SQL Server biztonsági másolat párbeszédpanel:

    ![Az SQL Server biztonsági másolat](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > A közös hozzáférésű Jogosultságkód az Azure Stack portálon, a vezető nélkül a SAS-token "?" a karakterláncban. Ha használja a másolási funkcióhoz a portálról, a vezető törölnie kell "?" a jogkivonat az SQL Server-kiszolgálón belül működik.

    A biztonsági mentési cél beállítása után, és az SQL Server konfigurálva, majd biztonsági másolatot készíthet az Azure Stack blob Storage.

## <a name="next-steps"></a>További lépések

[Szolgáltatások használatával, vagy alkalmazások készítéséhez az Azure Stackhez](azure-stack-considerations.md)