---
title: SQL Server ajánlott eljárásokat a Azure Stack teljesítményének optimalizálásához. | Microsoft Docs
description: Ez a cikk az SQL Server ajánlott eljárásait ismerteti a teljesítmény növeléséhez és a SQL Server Azure Stack virtuális gépeken való optimalizálásához.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: eca886314388f404e7a26a22f7a3b03294ff0577
ms.sourcegitcommit: 5e53eb5d43d28ab07b4f84891dd269bbfcf65622
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71311294"
---
# <a name="sql-server-best-practices-to-optimize-performance-in-azure-stack"></a>Az SQL Server ajánlott eljárásai a teljesítmény optimalizálása érdekében Azure Stack

Ez a cikk az SQL Server ajánlott eljárásait ismerteti a SQL Server optimalizálásához és a teljesítmény javításához Microsoft Azure Stack virtuális gépeken (VM). SQL Server Azure Stack virtuális gépeken való futtatásakor ugyanazt az adatbázis-teljesítmény-hangolási lehetőséget kell használnia, mint a helyszíni kiszolgálói környezetben SQL Server. A Azure Stack-felhőben található rokoni adatbázisok teljesítménye számos tényezőtől függ, többek között a virtuális gép méretétől és az adatlemezek konfigurációjától.

SQL Server lemezképek létrehozásakor [érdemes lehet virtuális gépeket kiépíteni a Azure stack portálon](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Töltse le az SQL IaaS bővítményt a piactér-felügyeletből a Azure Stack felügyeleti portálon, és töltse le az SQL VM virtuális merevlemezek (VHD-k) közül. Ilyenek például a SQL2014SP2, a SQL2016SP1 és a SQL2017.

> [!NOTE]  
> Míg a cikk leírja, hogyan kell kiépíteni egy SQL Server VMt a globális Azure Portal használatával, az útmutató a Azure Stackra is vonatkozik a következő eltérésekkel: Az SSD nem érhető el az operációs rendszer lemeze számára, a felügyelt lemezek nem érhetők el, és kisebb különbségek vannak a tárolási konfigurációban.

A Azure Stack virtuális gépek SQL Server *legjobb* teljesítményének beszerzése a jelen cikk középpontjában áll. Ha a számítási feladat kevésbé igényes, előfordulhat, hogy nem igényel minden javasolt optimalizálást. A javaslatok kiértékelése során vegye figyelembe a teljesítményre vonatkozó igényeket és a számítási feladatok mintáit.

> [!NOTE]  
> Az Azure-beli virtuális gépek SQL Server teljesítményével kapcsolatos útmutatásért tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="checklist-for-sql-server-best-practices"></a>Ellenőrzőlista az SQL Serverhez – ajánlott eljárások

A következő ellenőrzőlista a SQL Server Azure Stack virtuális gépeken való optimális teljesítményére szolgál:


|Terület|Optimalizálás|
|-----|-----|
|Virtuális gép mérete |[DS3](azure-stack-vm-sizes.md) vagy újabb SQL Server Enterprise kiadáshoz.<br><br>[DS2](azure-stack-vm-sizes.md) vagy újabb a SQL Server Standard Edition és a Web Edition rendszerhez.|
|Storage |Olyan virtuálisgép-családot használjon, amely támogatja a [Premium Storage](azure-stack-acs-differences.md)-ot.|
|Lemezek |Használjon legalább két adatlemezt (egyet a naplófájlokhoz, egyet az adatfájlhoz és a TempDB), és a kapacitás igényei alapján válassza ki a lemez méretét. Állítsa az alapértelmezett adatfájl-tárolóhelyeket ezekre a lemezekre a SQL Server telepítése során.<br><br>Ne használjon operációs rendszert vagy ideiglenes lemezeket az adatbázis-tároláshoz vagy a naplózáshoz.<br>Több Azure-adatlemezt is felhasználhat, hogy a tárolóhelyek használatával megnövelt IO-átviteli sebességet kapjon.<br><br>Dokumentált kiosztási méretekkel rendelkező formátum.|
|I/O|Az adatfájlok azonnali inicializálásának engedélyezése.<br><br>Az adatbázisok automatikus növekedésének korlátozása ésszerűen kis rögzített növekményekkel (64 MB-256 MB).<br><br>Az AutoShrink letiltása az adatbázison.<br><br>Állítsa be az alapértelmezett biztonsági mentési és adatbázisfájlok helyét az adatlemezeken, nem az operációs rendszer lemezét.<br><br>Zárolt lapok engedélyezése.<br><br>Alkalmazza a SQL Server szervizcsomagokat és az összesítő frissítéseket.|
|Szolgáltatás-specifikus|Biztonsági mentés közvetlenül a blob Storage-ba (ha a használatban lévő SQL Server verzió támogatja).|
|||

Az optimalizálási *útmutatóval* és azokkal kapcsolatos további információkért tekintse át a következő részben ismertetett *részleteket és* útmutatást.

## <a name="vm-size-guidance"></a>VIRTUÁLIS gépek méretére vonatkozó útmutató

A teljesítményre érzékeny alkalmazások esetében a következő virtuálisgép- [méretek](azure-stack-vm-sizes.md) ajánlottak:

- **SQL Server Enterprise Edition:** DS3 vagy újabb

- **SQL Server Standard Edition és Web Edition:** DS2 vagy újabb

Azure Stack esetében nincs teljesítménybeli különbség a DS és a DS_v2 virtuálisgép-termékcsalád között.

## <a name="storage-guidance"></a>Storage – útmutató

A Azure Stack DS-sorozatú virtuális gépek (a DSv2 sorozattal együtt) biztosítják az operációsrendszer-lemez és az adatlemez maximális átviteli sebességét (IOPS). A DS-vagy DSv2-sorozatú virtuális gépek akár 1 000 IOPS biztosítanak az operációs rendszer lemeze számára, és az adatlemezek legfeljebb 2 300 IOPS, a kiválasztott lemez típusától vagy méretétől függetlenül.

Az adatlemez átviteli sebességét a virtuálisgép-család adatsorozata alapján egyedi módon határozzák meg. A [cikkből](azure-stack-vm-sizes.md) megtudhatja, hogyan azonosíthatja az adatlemezek átviteli sebességét virtuálisgép-termékcsaládon.

> [!NOTE]  
> Éles számítási feladatokhoz válasszon egy DS sorozatú vagy DSv2-sorozatú virtuális gépet, amely megadja az operációs rendszer lemezének és adatlemezének maximális lehetséges IOPS.

Amikor Azure Stackban hoz létre egy Storage-fiókot, a Geo-replikáció beállításnak nincs hatása, mivel ez a funkció nem érhető el a Azure Stackban.

## <a name="disks-guidance"></a>Útmutató a lemezekhez

A Azure Stack virtuális gépeken három fő lemez létezik:

- **Operációs rendszer lemeze:** Azure Stack virtuális gép létrehozásakor a platform legalább egy lemezt ( **C** meghajtóként címkézett) helyez el a virtuális géphez az operációs rendszer lemeze számára. Ez a lemez egy, a tárolóban blobként tárolt VHD.

- **Ideiglenes lemez:** Azure Stack virtuális gépek egy másik lemezt tartalmaznak, amely az ideiglenes lemez (a **D** meghajtóként van megjelölve). Ez egy lemez a csomóponton, amely felhasználható a tárhelyhez.

- **Adatlemezek:** Adatlemezként további lemezeket is csatlakoztathat a virtuális géphez, és ezeket a lemezeket a Storage-ban blobként tárolja a rendszer.

A következő szakaszok a különböző lemezek használatára vonatkozó javaslatokat ismertetik.

### <a name="operating-system-disk"></a>Operációsrendszer-lemez

Az operációsrendszer-lemez olyan virtuális merevlemez, amely az operációs rendszer futó verziójaként indítható el és csatlakoztatható, és **C** meghajtóként van megjelölve.

### <a name="temporary-disk"></a>Ideiglenes lemez

A **D** meghajtóként megjelölt ideiglenes tároló meghajtó nem állandó. Ne tároljon semmilyen olyan adat, amelyet el szeretne veszíteni a **D** meghajtón. Ez magában foglalja a felhasználói adatbázis fájljait és a felhasználói tranzakció naplófájljait.

Azt javasoljuk, hogy adatlemezként tárolja a TempDB, mivel az adatlemezek legfeljebb 2 300 IOPS biztosítanak.

### <a name="data-disks"></a>Adatlemezek

- **Adatlemezek használata az adatfájlokhoz és a naplófájlokhoz.** Ha nem használ lemezes csíkozást, használjon két adatlemezt egy Premium Storage-t támogató virtuális gépről, ahol az egyik lemez tartalmazza a naplófájlokat, a másik pedig tartalmazza az adatok és a TempDB fájljait. Az egyes adatlemezek a virtuálisgép-családtól függően több IOPS biztosítanak, a [Azure stack által támogatott](azure-stack-vm-sizes.md)virtuálisgép-méretek című cikkben leírtak szerint. Ha lemezes csíkozási technikát (például tárolóhelyeket) használ, helyezze az összes adatfájlt és naplófájlt ugyanarra a meghajtóra (beleértve a TempDB is). Ez a konfiguráció biztosítja a SQL Server számára elérhető IOPS maximális számát, függetlenül attól, hogy mely fájlokra van szükség egy adott időpontban.

> [!NOTE]  
> Amikor kiépít egy SQL Server VM a portálon, lehetősége van a tárolási konfiguráció szerkesztésére. A konfigurációtól függően Azure Stack egy vagy több lemezt konfigurál. Több lemez egyetlen tárolási készletbe van összevonva. Ebben a konfigurációban az adatfájlok és a naplófájlok is együtt találhatók.

- **Lemezek csíkozása:** További átviteli sebesség esetén további adatlemezeket adhat hozzá, és a lemezek csíkozását is használhatja. A szükséges adatlemezek számának meghatározásához elemezze a naplófájlokhoz szükséges IOPS, valamint az adatai és a TempDB fájljait. Figyelje meg, hogy az IOPS-korlátok a virtuálisgép-sorozat családján alapuló adatlemezek, és nem a virtuális gép méretétől függenek. A hálózati sávszélesség korlátai azonban a virtuális gép méretétől függenek. További részletekért tekintse meg a [virtuális gépek méreteit Azure stack](azure-stack-vm-sizes.md) táblázatban. Használja az alábbi irányelveket:

  - A Windows Server 2012-es vagy újabb verzióiban a [tárolóhelyek](https://technet.microsoft.com/library/hh831739.aspx) a következő irányelvekkel használhatók:

    1. Az adattárház-feldolgozási feladatokhoz adja meg az adattárolási munkaterhelések (OLTP) és a 256 KB (262 144 bájt) közötti 65 536 64 átfedést az adatraktározási számítási feladatok esetében, hogy elkerülje a teljesítményre gyakorolt hatást a partíció helytelen igazítása miatt. Ezt a PowerShell-lel kell beállítani.

    2. Oszlopok számának beállítása = fizikai lemezek száma. Több mint nyolc lemez (nem a Kiszolgálókezelő felhasználói felülete) konfigurálásához használja a PowerShellt.

       A következő PowerShell például létrehoz egy új tárolót, amelynek a mérete 64 KB, az oszlopok száma pedig 2:

       ```powershell  
       $PoolCount = Get-PhysicalDisk -CanPool $True
       $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

       New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple -UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
       ```

- Határozza meg a tárolási készlethez társított lemezek számát a betöltési várakozások alapján. Ne feledje, hogy a különböző virtuálisgép-méretek különböző számú csatlakoztatott adatlemezt tesznek lehetővé. További információ: [Azure stack által támogatott VM-méretek](azure-stack-vm-sizes.md).
- Az adatlemezek maximális lehetséges IOPS érdekében a javasolt érték a virtuálisgép- [méret](azure-stack-vm-sizes.md) által támogatott adatlemezek maximális számának és a lemezes sávok használatának a megadása.
- **NTFS-foglalási egység mérete:** Az adatlemez formázásakor javasoljuk, hogy az adatfájlok és a naplófájlok, valamint a TempDB esetében használjon 64 KB-os kiosztási egység méretet.
- **Lemezkezelés gyakorlata:** Adatlemez eltávolításakor állítsa le a SQL Server szolgáltatást a módosítás során. Ne módosítsa a lemez gyorsítótár-beállításait is, mivel az nem biztosít teljesítménybeli változásokat.

> [!WARNING]  
> Ha nem sikerül leállítani az SQL-szolgáltatást ezen műveletek során, az adatbázis sérülését okozhatja.

## <a name="io-guidance"></a>I/O-útmutatás

- Érdemes lehet engedélyezni az azonnali fájl inicializálását, hogy csökkentse a kezdeti fájl lefoglalásához szükséges időt. Az azonnali fájl inicializálásának kihasználásához adja meg az SQL Server (MSSQLSERVER) szolgáltatásfiókot a **SE_MANAGE_VOLUME_NAME** , és vegye fel a **kötet-karbantartási feladatok elvégzése** biztonsági szabályzatba. Ha SQL Server platform-rendszerképet használ az Azure-hoz, a rendszer nem adja hozzá az alapértelmezett szolgáltatásfiókot (**NT Service\MSSQLSERVER**) a **kötet-karbantartási feladatok végrehajtása** biztonsági házirendhez. Más szóval az azonnali fájl inicializálása nincs engedélyezve egy SQL Server Azure platform-rendszerképben. Miután hozzáadta a SQL Server szolgáltatásfiókot a **kötet-karbantartási feladatok elvégzése** biztonsági házirendhez, indítsa újra a SQL Server szolgáltatást. Ennek a funkciónak a használatához biztonsági megfontolásokat lehet használni. További információ: [adatbázisfájlok inicializálása](https://msdn.microsoft.com/library/ms175935.aspx).
- Az **automatikus növekedés váratlan** növekedést mutat. Az automatikus növekedéssel nem kezelheti az adatait, és napi rendszerességgel elvégezheti a naplózást. Ha az automatikus növekedés használatban van, a **méret** kapcsoló használatával növelje a fájl előzetes növekedését.
- Ügyeljen arra, hogy a szükségtelen terhelés elkerülése érdekében az **AutoShrink** le legyen tiltva, ami negatív hatással lehet a teljesítményre.
- Alapértelmezett biztonsági mentési és adatbázisfájl-tárolóhelyek beállítása. Használja az ebben a cikkben szereplő javaslatokat, és végezze el a módosításokat a kiszolgáló tulajdonságai ablakban. Útmutatásért lásd: az adatfájlok [és naplófájlok alapértelmezett helyeinek megtekintése vagy módosítása (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). A következő képernyőképen látható, hol végezheti el a módosításokat:

    > ![Az alapértelmezett helyszínek megtekintése és módosítása](./media/sql-server-vm-considerations/image1.png)

- Az i/o és a lapozási tevékenységek csökkentéséhez engedélyezze a zárolt lapokat. További információ: [a zárolási lapok engedélyezése a memóriában (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Vegye fontolóra az adatfájlok tömörítését Azure Stack, beleértve a biztonsági mentéseket.

## <a name="feature-specific-guidance"></a>Szolgáltatás-specifikus útmutató

Néhány üzemelő példány további teljesítménybeli előnyöket érhet el fejlettebb konfigurációs módszerek használatával. Az alábbi lista néhány olyan SQL Server funkciót mutat be, amelyek segíthetnek a jobb teljesítmény elérésében:

- **Biztonsági mentés az Azure-** ba **tárterület.** Ha Azure Stack virtuális gépeken futó SQL Server biztonsági mentést készít, az URL-címre SQL Server biztonsági mentést használhat. Ez a funkció SQL Server 2012 SP1 CU2 kezdődően érhető el, és ajánlott a csatlakoztatott adatlemezekre történő biztonsági mentéshez.

    Ha az Azure Storage szolgáltatással készít biztonsági mentést vagy visszaállítást, kövesse az [SQL Server biztonsági mentés az URL-címekkel kapcsolatos ajánlott eljárásokat, valamint](https://msdn.microsoft.com/library/jj919149.aspx) a [Microsoft Azure tárolt biztonsági](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017)másolatokból történő hibaelhárítási és visszaállítási javaslatokat. Ezeket a biztonsági mentéseket a [SQL Server Azure-beli virtuális gépeken történő automatikus biztonsági mentésével](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)is automatizálhatja.

-   **Biztonsági mentés Azure Stack tárterületre.** Az Azure Storage-ba történő biztonsági mentéshez hasonló módon készíthet biztonsági mentést Azure Stack tárolóba. Ha SQL Server Management Studioon (SSMS) belül készít biztonsági másolatot, manuálisan kell megadnia a konfigurációs adatokat. A SSMS nem használható a tároló vagy a közös hozzáférési aláírás létrehozásához. A SSMS csak az Azure-előfizetésekhez csatlakozik, Azure Stack előfizetésekhez nem. Ehelyett létre kell hoznia a Storage-fiókot, a tárolót és a közös hozzáférési aláírást a Azure Stack-portálon vagy a PowerShell-lel.


    ![SQL Server biztonsági mentés](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > A közös hozzáférésű aláírás a Azure Stack portál SAS-jogkivonata, a vezető "?" nélkül a karakterláncban. Ha a portálon a másolás funkciót használja, törölnie kell a vezetőt (?) ahhoz, hogy a token SQL Serveron belül működjön.

    Miután beállította és konfigurálta a biztonsági mentési célhelyet SQL Serverban, biztonsági mentést készíthet a Azure Stack blob Storage-ba.

## <a name="next-steps"></a>További lépések

[Szolgáltatások használata vagy alkalmazások kiépítése Azure Stack](azure-stack-considerations.md)
