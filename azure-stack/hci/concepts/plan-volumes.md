---
title: Kötetek megtervezése Azure Stack HCI-ben
description: A tárolási kötetek megtervezése Azure Stack HCI-ben.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: 8125ff0432b16f254f8660e8c5f755f7f4916b12
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572772"
---
# <a name="plan-volumes-in-azure-stack-hci"></a>Kötetek megtervezése Azure Stack HCI-ben

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Ez a témakör útmutatást nyújt a kötetek Azure Stack HCI-ben történő megtervezéséhez, hogy megfeleljenek a számítási feladatok teljesítményének és kapacitásának, például a fájlrendszer, a rugalmasság típusa és a méret kiválasztásával.

## <a name="review-what-are-volumes"></a>Áttekintés: Mik a kötetek?

A köteteken a számítási feladatok szükségesek, például VHD-vagy VHDX-fájlok a Hyper-V virtuális gépekhez. A kötetek egyesítik a Storage-készlet meghajtóit, hogy bevezessék a hibatűrést, a méretezhetőséget és a [közvetlen tárolóhelyek](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)teljesítményének előnyeit, valamint a Azure stack HCI mögötti szoftver által meghatározott tárolási technológiát.

   >[!NOTE]
   > A Közvetlen tárolóhelyek dokumentációjában a "kötet" kifejezést használjuk, hogy közösen hivatkozzon a kötetre és a virtuális lemezre, beleértve a más beépített Windows-funkciók, például a fürt megosztott kötetei (CSV) és a ReFS által nyújtott funkciókat is. A megvalósítási szintű különbségtétel nem szükséges a Közvetlen tárolóhelyek sikeres tervezéséhez és üzembe helyezéséhez.

![A diagram három olyan mappát mutat be, amelyek kötetként címkézett virtuális lemezzel vannak társítva, amelyek mindegyike egy közös tárolóeszközhöz van társítva.](media/plan-volumes/what-are-volumes.png)

Minden kötet a fürt összes kiszolgálója számára elérhető. A létrehozás után a **C:\ClusterStorage \\ ** az összes kiszolgálón megjelennek.

![A képernyőfelvételen egy ClusterStorage nevű fájlkezelő ablak látható, amely a Volume1, a indítása kötet2 és a Volume3 nevű köteteket tartalmazza.](media/plan-volumes/csv-folder-screenshot.png)

## <a name="choosing-how-many-volumes-to-create"></a>A létrehozandó kötetek számának kiválasztása

Azt javasoljuk, hogy a kötetek számát a fürtben lévő kiszolgálók számának többszörösére állítsa. Ha például 4 kiszolgálóval rendelkezik, akkor a 4 teljes kötettel konzisztens teljesítményt fog tapasztalni, mint 3 vagy 5. Ez lehetővé teszi a fürt számára, hogy a "tulajdonos" kötetet (az egyik kiszolgáló kezeli az egyes kötetek metaadat-összehangolása) egyenletesen a kiszolgálók között.

Azt javasoljuk, hogy fürtre korlátozza a kötetek teljes számát 64 kötetre.

## <a name="choosing-the-filesystem"></a>A fájlrendszer kiválasztása

Javasoljuk, hogy az új, [rugalmas fájlrendszert (ReFS)](/windows-server/storage/refs/refs-overview) használja a közvetlen tárolóhelyekhoz. A ReFS a Premier fájlrendszer, amely a virtualizáció számára készült, és számos előnnyel jár, többek között a drámai teljesítmény-gyorsítás és az adatsérülés elleni beépített védelem. Szinte minden fontos NTFS-funkciót támogat, beleértve a Windows Server 1709-es és újabb verzióiban az demásolást is. A részletekért tekintse meg a ReFS [funkcióinak összehasonlító táblázatát](/windows-server/storage/refs/refs-overview#feature-comparison) .

Ha a számítási feladathoz olyan szolgáltatásra van szükség, amelyet a ReFS még nem támogat, használhat NTFS helyet.

   > [!TIP]
   > A különböző fájlrendszerrel rendelkező kötetek ugyanabban a fürtben is létezhetnek.

## <a name="choosing-the-resiliency-type"></a>A rugalmasság típusának kiválasztása

A Közvetlen tárolóhelyekban lévő kötetek rugalmasságot biztosítanak a hardveres problémák, például a meghajtó-vagy kiszolgálói hibák elleni védelemhez, valamint a folyamatos rendelkezésre állás engedélyezéséhez a kiszolgáló karbantartása, például a szoftverfrissítések terén.

   > [!NOTE]
   > A választható rugalmassági típusok függetlenek egymástól, hogy milyen típusú meghajtókat használ.

### <a name="with-two-servers"></a>Két kiszolgálóval

A fürt két kiszolgálójának használatával kétirányú tükrözést használhat, vagy használhat beágyazott rugalmasságot is.

A kétirányú tükrözés az összes adattal két példányt tart, egy példányt az egyes kiszolgálókon lévő meghajtókon. A tároló hatékonysága 50%; 1 TB-nyi adat írásához legalább 2 TB fizikai tárolási kapacitásra van szükség a Storage-készletben. A kétirányú tükrözések biztonságosan tolerálják az egyik hardverhiba (egy kiszolgáló vagy meghajtó) számára.

![A diagramon látható, hogy az adatmennyiség és a másolás körkörös nyíllal történt, és mindkét kötet társítva van a kiszolgálók egyik bankjának a kötetei közé.](media/plan-volumes/two-way-mirror.png)

A beágyazott rugalmasság biztosítja az adatrugalmasságot a kiszolgálók között kétirányú tükrözéssel, majd rugalmasságot biztosít a kiszolgálón a kétirányú tükrözéssel vagy a tükrözött gyorsítású paritással. A beágyazás az adatrugalmasságot is biztosítja, még akkor is, ha az egyik kiszolgáló újraindul vagy elérhetetlenné válik. A tároló hatékonysága 25%, beágyazott, kétutas tükrözéssel és körülbelül 35-40%-kal a beágyazott tükrözött felgyorsított paritáson. A beágyazott rugalmasság a két hardverhiba (két meghajtó, a kiszolgáló és a megmaradt kiszolgáló meghajtója) esetében képes biztonságosan tolerálni. A hozzáadott adatrugalmasság miatt javasoljuk, hogy beágyazott rugalmasságot használjon két kiszolgálóból álló fürtök éles üzembe helyezéséhez. További információ: [beágyazott rugalmasság](/windows-server/storage/storage-spaces/nested-resiliency).

![A diagram a beágyazott tükrözött felgyorsított paritást jeleníti meg, kétirányú tükrözéssel az egyes kiszolgálókon belül az egyes kiszolgálók paritási rétegéhez tartozó kétirányú tükrözéssel.](media/plan-volumes/nested-mirror-accelerated-parity.png)

### <a name="with-three-servers"></a>Három kiszolgálóval

Három kiszolgáló esetén a jobb hibatűrés és teljesítmény érdekében háromutas tükrözést kell használni. A háromutas tükrözés az összes adattal három példányt tart, egy példányt az egyes kiszolgálókon található meghajtókon. A tárterület hatékonysága 33,3 százalék – 1 TB adat írásához legalább 3 TB fizikai tárolási kapacitásra van szükség a Storage-készletben. A háromutas tükrözés képes biztonságosan elviselni [legalább két hardveres problémát (meghajtó vagy kiszolgáló)](/windows-server/storage/storage-spaces/storage-spaces-fault-tolerance#examples)egyszerre. Ha 2 csomópont elérhetetlenné válik, a tároló elveszíti a kvórumot, mivel a lemezek 2/3 nem érhető el, és a virtuális lemezek nem lesznek elérhetők. A csomópontok azonban leállíthatók, és egy vagy több lemez egy másik csomóponton meghiúsulhat, és a virtuális lemezek online állapotban maradnak. Ha például egy kiszolgáló újraindításakor hirtelen egy másik meghajtó vagy kiszolgáló meghibásodik, az összes adat biztonságban és folyamatosan elérhető marad.

![A diagramon egy, a fizikai lemezeket tartalmazó kiszolgálóhoz társított minden kötethez tartozó, a kötet címkével ellátott és a két címkével ellátott másolat látható.](media/plan-volumes/three-way-mirror.png)

### <a name="with-four-or-more-servers"></a>Négy vagy több kiszolgálóval

Négy vagy több kiszolgáló esetén az egyes kötetek esetében választhat, hogy a háromutas tükrözést, a kettős paritást (más néven "törlési kódolást") használja-e, vagy a kettőt a tükrözött gyorsítású paritással keveri.

A kettős paritás ugyanolyan hibatűrést biztosít, mint a háromutas tükrözés, de a jobb tárolási hatékonyságot. Négy kiszolgáló esetén a tároló hatékonysága 50,0%; 2 TB-nyi adat tárolásához a Storage-készletben 4 TB fizikai tárolókapacitás szükséges. Ez 66,7 százalékkal növeli a tárolási hatékonyságot hét kiszolgálóval, és akár 80,0%-os tárolási hatékonyságot is biztosít. A kompromisszum az, hogy a paritásos kódolás nagyobb számítási igényű, ami korlátozhatja a teljesítményét.

![A diagramon két, a fizikai lemezeket tartalmazó kiszolgálóhoz társított kötettel rendelkező, két kötet címkézett adatai és két címkézett paritás látható.](media/plan-volumes/dual-parity.png)

A használni kívánt rugalmassági típus a munkaterhelés igényeitől függ. Az alábbi táblázat összefoglalja, hogy mely számítási feladatok alkalmasak minden rugalmassági típushoz, valamint az egyes rugalmassági típusok teljesítményének és tárolásának hatékonyságát.

| Rugalmasság típusa | Kapacitás hatékonysága | Sebesség | Számítási feladatok |
| ------------------- | ----------------------  | --------- | ------------- |
| **Tükrözött**         | ![A tárterület hatékonysága 33%-ot mutat](media/plan-volumes/3-way-mirror-storage-efficiency.png)<br>Háromutas tükrözés: 33% <br>Kétirányú tükrözés: 50%     |![Teljesítmény, amely a 100%-ot mutatja](media/plan-volumes/three-way-mirror-perf.png)<br> Legmagasabb teljesítmény  | Virtualizált munkaterhelések<br> Adatbázisok<br>Egyéb nagy teljesítményű számítási feladatok |
| **Tükrözött, gyorsított paritás** |![A tárolási hatékonyság mintegy 50%-ot mutat](media/plan-volumes/mirror-accelerated-parity-storage-efficiency.png)<br> A tükrözés és a paritás arányának függvénye | ![Körülbelül 20%-os teljesítmény](media/plan-volumes/mirror-accelerated-parity-perf.png)<br>Sokkal lassabb, mint a tükrözés, de akár kétszer is gyorsabb a kettős paritás<br> A legjobb nagy sorszámú írási és olvasási műveletekhez | Archiválás és biztonsági mentés<br> Virtualizált asztali infrastruktúra     |
| **Kettős paritású**               | ![A tárolási hatékonyság mintegy 80%-ot mutat](media/plan-volumes/dual-parity-storage-efficiency.png)<br>4 kiszolgáló: 50% <br>16 kiszolgáló: legfeljebb 80% | ![Körülbelül 10%-os teljesítmény](media/plan-volumes/dual-parity-perf.png)<br>Maximális I/O-késés & CPU-használat az írásokban<br> A legjobb nagy sorszámú írási és olvasási műveletekhez | Archiválás és biztonsági mentés<br> Virtualizált asztali infrastruktúra  |

#### <a name="when-performance-matters-most"></a>Ha a teljesítménnyel kapcsolatos kérdések többsége

A szigorú késési követelményekkel rendelkező vagy nagy mennyiségű kevert véletlenszerű IOPS igénylő munkaterhelések, például SQL Server adatbázisok vagy a teljesítményre érzékeny Hyper-V virtuális gépek, a tükrözést használó köteteken futnak a teljesítmény maximalizálása érdekében.

   >[!TIP]
   > A tükrözés gyorsabb, mint bármely más rugalmassági típus. Tükrözést használunk szinte minden teljesítménybeli példához.

#### <a name="when-capacity-matters-most"></a>A kapacitással kapcsolatos kérdések

A ritkábban, például adattárházak vagy "hideg" tárolást igénylő munkaterhelések a tárolás hatékonyságának maximalizálása érdekében a kettős paritást használó köteteken futnak. Bizonyos egyéb munkaterhelések, például a hagyományos fájlkiszolgálók, a virtuális asztali infrastruktúra (VDI) vagy mások, amelyek nem hoznak létre nagy mennyiségű, gyorsan sodródó, véletlenszerű IO-forgalmat és/vagy nem igénylik a legjobb teljesítményt, akár kettős paritást is használhatnak. A paritás elkerülhetetlenül növeli a CPU-kihasználtságot és az i/o-késést, különösen az írások esetében, a tükrözéshez képest.

#### <a name="when-data-is-written-in-bulk"></a>Az adatmennyiség tömeges írása

Az olyan számítási feladatok, amelyek nagy mennyiségű, szekvenciális (például archiválási vagy biztonsági mentési) műveletekre írnak, egy másik lehetőséggel rendelkeznek: az egyik kötet tükröző és kettős paritást is tartalmazhat. Először a tükrözött részbe írja a földet, és később fokozatosan áthelyezi a paritási részbe. Ez felgyorsítja a betöltést, és csökkenti az erőforrás-használatot, ha a nagyméretű írások megérkeznek, így a nagy számítási igényű paritásos kódolás hosszabb idő alatt megtörténik. A részek méretezése során vegye figyelembe, hogy az egyszerre megjelenő írási mennyiség (például egy napi biztonsági mentés) kényelmesen illeszkedik a tükrözési részhez. Ha például naponta egyszer betölti a 100 GB-ot, vegye fontolóra a tükrözést az 150 GB-ról 200 GB-ra, a REST-hez pedig kettős paritást használjon.

Az eredményül kapott tárolási hatékonyság a választott aránytól függ. [Ebben a bemutatóban](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s) talál néhány példát.

   > [!TIP]
   > Ha az írás teljesítményének hirtelen csökkenését tapasztalja az adatok betöltésének partway keresztül, akkor azt jelezheti, hogy a tükrözött rész nem elég nagy, vagy hogy a tükrözött gyorsítású paritás nem megfelelő a használati esethez. Ha például az írási teljesítmény 400 MB/s-ról 40 MB/s-ra csökken, érdemes lehet kibővíteni a tükrözési részt, vagy váltani a háromutas tükrözésre.

### <a name="about-deployments-with-nvme-ssd-and-hdd"></a>NVMe-, SSD-és HDD-alapú telepítések

A kétféle meghajtóval üzemelő példányok esetében a gyorsabb meghajtók biztosítják a gyorsítótárazást, miközben a lassabb meghajtók kapacitást biztosítanak. Ez automatikusan megtörténik – további információért lásd: [a gyorsítótár megismerése közvetlen tárolóhelyekban](/windows-server/storage/storage-spaces/understand-the-cache). Ilyen központi telepítések esetén az összes kötet ugyanazon típusú meghajtókon, a kapacitás-meghajtókon található.

Az üzemelő példányok mindhárom típusú meghajtóval csak a leggyorsabb meghajtókon (NVMe) biztosítanak gyorsítótárazást, így a kapacitás biztosításához két típusú meghajtó (SSD és HDD) is rendelkezésre áll. Minden kötet esetében kiválaszthatja, hogy a teljes egészében az SSD-rétegen, teljes egészében a HDD-szinten található-e, vagy a kettőre terjed ki.

   > [!IMPORTANT]
   > Javasoljuk, hogy az SSD-réteg használatával helyezze el a legtöbb teljesítményre érzékeny munkaterhelést az összes flash-meghajtón.

## <a name="choosing-the-size-of-volumes"></a>A kötetek méretének kiválasztása

Javasoljuk, hogy az egyes kötetek méretét 64 TB-ra korlátozza a Windows Server 2019-ben.

   > [!TIP]
   > Ha olyan biztonsági mentési megoldást használ, amely a Kötet árnyékmásolata szolgáltatás (VSS) és a VolSnap szoftver szolgáltatóján alapul – akárcsak a Fájlkiszolgálói munkaterhelések esetében –, a kötet méretének 10 TB-ra való korlátozása növeli a teljesítményt és a megbízhatóságot. A Hyper-V RCT API-t és/vagy ReFS-blokkoló és/vagy a natív SQL backup API-kat használó biztonsági mentési megoldások 32 TB-os és újabb rendszereken is elvégezhetők.

### <a name="footprint"></a>Lábnyom

A kötetek mérete a felhasználható kapacitásra, az általa tárolt adatmennyiségre utal. Ezt a **New-Volume** parancsmag **-size** paraméter adja meg, majd a **Size (méret** ) tulajdonságban jelenik meg, amikor futtatja a **Get-Volume** parancsmagot.

A méret különbözik a kötet *lábnyomával*, a tárterület teljes fizikai tárolási kapacitásával. A lábnyom a rugalmasság típusától függ. A háromutas tükrözést használó kötetek esetében például a méretük háromszorosa a lábnyomnak.

A kötetek lábnyomait el kell helyezni a tárolóban.

![A diagram egy 2 TB-os kötetet mutat be a Storage-készletben lévő 6 TB-os adatforgalomhoz képest, három megadott szorzóval.](media/plan-volumes/size-versus-footprint.png)

### <a name="reserve-capacity"></a>Kapacitás foglalása

Ha a tárolóban lévő egyes kapacitások nem vannak lefoglalva, a meghajtók meghibásodása után a kötetek lemezterületet biztosítanak a "helyben" javításához, ami javítja az adatbiztonságot és a teljesítményt. Ha elegendő kapacitás áll rendelkezésre, azonnali, helyi, párhuzamos javítással a kötetek teljes rugalmassága állítható vissza, még a hibás meghajtók lecserélése előtt is. Ez automatikusan megtörténik.

Javasoljuk, hogy kiszolgálóként, legfeljebb 4 meghajtón egy kapacitású meghajtó megfelelőjét őrizze meg. Saját belátása szerint továbbra is fenntarthatja magát, de ez a minimális javaslat azonnali, helyi, párhuzamos javítást tesz elérhetővé a meghajtó meghibásodása után.

![A diagram egy tárolóban található több lemezhez társított kötetet, valamint a tartalékként megjelölt nem társított lemezeket jeleníti meg.](media/plan-volumes/reserve.png)

Ha például 2 kiszolgálóval rendelkezik, és 1 TB kapacitású meghajtókat használ, a készletbe 2 x 1 = 2 TB-ot kell kivonni tartalékként. Ha 3 kiszolgálóval és 1 TB kapacitású meghajtóval rendelkezik, állítsa 3 x 1 = 3 TB tartalékként. Ha 4 vagy több kiszolgálóval és 1 TB kapacitású meghajtóval rendelkezik, állítsa be a 4 x 1 = 4 TB-ot tartalékként.

   >[!NOTE]
   > A mindhárom típusú (NVMe + SSD + HDD) meghajtóval rendelkező fürtök esetében javasoljuk, hogy kiszolgálóként egy SSD-t és egy HDD-t, vagy akár 4 meghajtót is kiszolgáljon.

## <a name="example-capacity-planning"></a>Példa: kapacitás megtervezése

Gondolja át a 1 4-Server fürtöt. Mindegyik kiszolgálón van néhány gyorsítótár-meghajtó, valamint 16 TB-os meghajtó a kapacitáshoz.

```
4 servers x 16 drives each x 2 TB each = 128 TB
```

Ebből a 128 TB-ból a tárolóban négy meghajtót vagy 8 TB-ot állítottunk fel, így a helyi javítások a meghibásodások nélkül is megtörténhetnek, és nem kell lecserélniük a meghajtókat. Ez 120 TB-nyi fizikai tárolási kapacitást hagy a készletben, amelyből kötetek hozhatók létre.

```
128 TB – (4 x 2 TB) = 120 TB
```

Tegyük fel, hogy üzembe helyezésünk néhány nagy teljesítményű Hyper-V virtuális gép üzemeltetésére van szükség, de a sok hideg tárterület – a régi fájlok és a biztonsági mentések is megmaradnak. Mivel négy kiszolgálónk van, hozzunk létre négy kötetet.

Helyezzük a virtuális gépeket az első két kötetre, a *Volume1* és a *indítása kötet2*. A teljesítmény maximalizálása érdekében a ReFS-t (a gyorsabb létrehozás és az ellenőrzőpontok esetében) és a kétirányú tükrözést is választjuk. Helyezze a hideg tárolót a másik két kötetre, a *3. kötetre* és a *4. kötetre*. Az NTFS fájlrendszert (az deduplikálás esetében) és a kettős paritás rugalmasságát a kapacitás maximalizálása érdekében választjuk.

Nem szükséges, hogy az összes kötet azonos méretű legyen, de az egyszerűség kedvéért tegyük fel, hogy mind a 12 TB-ot.

A *Volume1* és a *indítása KÖTET2* a 12 TB x 33,3 százalékos hatékonyságot = 36 TB fizikai tárolókapacitást foglalja maguk után.

A *Volume3* és a *VOLUME4* egyaránt 12 TB x 50,0 százalékos hatékonyságot foglalnak magukban: 24 TB fizikai tárolási kapacitással.

```
36 TB + 36 TB + 24 TB + 24 TB = 120 TB
```

A négy kötet pontosan illeszkedik a készletben elérhető fizikai tárolási kapacitáshoz. Tökéletes!

![A diagramon a 2 12 TB-os háromutas tükrözött kötetek láthatók 120, amelyek mindegyike a 36 TB tárterülethez és a 2 12 TB-os kettős paritású kötetekhez kapcsolódik, amelyek mindegyike 24 TB-ra van társítva.](media/plan-volumes/example.png)

   >[!TIP]
   > Nem kell azonnal létrehoznia az összes kötetet. Később bármikor kiterjesztheti a köteteket, vagy új köteteket hozhat létre.

Az egyszerűség kedvéért ez a példa decimális (Base-10) egységeket használ az egészben, ami azt jelenti, hogy 1 TB = 1 000 000 000 000 bájt. A Windows tárolási mennyiségei azonban bináris (Base-2) egységben jelennek meg. A 2 TB-os meghajtó például 1,82 TiB-ként fog megjelenni a Windows rendszerben. Hasonlóképpen, az 128 TB-os tárolási készlet a 116,41 TiB-ként fog megjelenni. Ez a várható eredmény.

## <a name="usage"></a>Használat

Lásd: [kötetek létrehozása Azure stack HCI-ben](../manage/create-volumes.md).

## <a name="next-steps"></a>További lépések

További információért lásd még:

- [Meghajtók kiválasztása Közvetlen tárolóhelyekhoz](choose-drives.md)
- [Hibatűrés és a tárolás hatékonysága](fault-tolerance.md)