---
title: Azure Stack hub Storage-infrastruktúra áttekintése
titleSuffix: Azure Stack
description: Ismerje meg, hogyan kezelheti Azure Stack hub tárolási infrastruktúráját.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: inhenkel
ms.lastreviewed: 5/5/2020
ms.reviewer: jiaha
ms.custom: contperf-fy20q4
ms.openlocfilehash: 8633e31ac60dfb091997657b02b633d24d39b29d
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697604"
---
# <a name="azure-stack-hub-storage-infrastructure-overview"></a>Azure Stack hub Storage-infrastruktúra áttekintése

Ez a cikk Azure Stack hub tárolási infrastruktúrájának fogalmait ismerteti. Információkat tartalmaz a meghajtókról és a kötetekről, valamint arról, hogyan használják őket Azure Stack központban.

## <a name="drives"></a>Meghajtók

### <a name="drive-types"></a>Meghajtótípusok

Azure Stack hub integrált rendszerpartnerei számos megoldási variációt kínálnak, beleértve a tárolási rugalmasság széles körét. A három támogatott meghajtó típus közül legfeljebb **két** meghajtót választhat:

1. NVMe (nem felejtő memória expressz)
1. SATA/SAS SSD (SSD-meghajtó)
1. HDD (merevlemez-meghajtó).

### <a name="performance-vs-capacity"></a>Teljesítmény és kapacitás

Azure Stack hub a Windows Server feladatátvételi fürtszolgáltatásával Közvetlen tárolóhelyek (S2D) protokollt használ. Ez a kombináció egy teljesítményű, méretezhető és rugalmas tárolási szolgáltatást biztosít.

Azure Stack üzemelő példányok képesek maximalizálni a tárolási teljesítményt, illetve a teljesítmény és a kapacitás elosztását.

A Közvetlen tárolóhelyek gyorsítótárat használ a tárolási teljesítmény maximalizálása érdekében.

### <a name="how-drive-types-are-used"></a>A meghajtók típusának használata

Ha egy Azure Stack hub-készülék egy meghajtóval rendelkezik, a rendszer minden meghajtót felhasznál a kapacitáshoz.

Ha kétféle típusú meghajtó van, a Közvetlen tárolóhelyek automatikusan a "leggyorsabb" (NVMe &gt; SSD HDD) típusú meghajtókat használja a &gt; gyorsítótárazáshoz. A fennmaradó meghajtók szolgálnak a tárolókapacitás biztosítására.

### <a name="all-flash-or-hybrid"></a>Minden Flash vagy hibrid

A meghajtók egy "teljesen Flash" vagy "hibrid" telepítésbe csoportosíthatók.

Az összes Flash-telepítés célja, hogy maximalizálja a tárolási teljesítményt, és ne tartalmazzon rotációs HDD-ket.

![Az összes Flash üzembe helyezési csoportot megjelenítő diagram.](media/azure-stack-storage-infrastructure-overview/image1.png)


A hibrid üzembe helyezések célja a teljesítmény és a kapacitás elosztása, illetve a kapacitás maximalizálása, valamint a rotációs HDD-k beépítése.

![Egy hibrid üzembe helyezési csoportosítást bemutató diagram.](media/azure-stack-storage-infrastructure-overview/image2.png)

### <a name="caching-behavior"></a>Gyorsítótárazási viselkedés

A gyorsítótár viselkedését automatikusan meghatározza a meghajtók típusa (i) alapján. SSD-k gyorsítótárazásakor (például SSD-NVMe gyorsítótárazás esetén) csak az írások vannak gyorsítótárazva. Ez csökkenti a kapacitás-meghajtók kopását, csökkenti a kapacitás-meghajtók összesített forgalmát és az élettartamuk kiterjesztését.

Az olvasások nem vannak gyorsítótárazva. Nincsenek gyorsítótárazva, mert az olvasások nem befolyásolják jelentősen a Flash élettartamát, és mivel az SSD-k univerzálisan alacsony olvasási késést kínálnak.

A HDD-k gyorsítótárazása (például SSD-meghajtók gyorsítótárazása) esetén az olvasás és az írás is gyorsítótárazva van, így a Flash-hez hasonló késés (gyakran/~ 10x jobb) is biztosítható mindkettőhöz.

![Azure Stack hub tárolási gyorsítótárazási viselkedését bemutató diagram.](media/azure-stack-storage-infrastructure-overview/image3.svg)

A tárterület elérhető konfigurációjának megadásához tekintse meg Azure Stack hub OEM-partnert ( https://azure.microsoft.com/overview/azure-stack/partners/) részletes leírást.

> [!NOTE]
> Az Azure Stack hub készülék hibrid üzembe helyezhető, HDD-és SSD-(vagy NVMe-) meghajtókon is. A gyorsabb típusú meghajtók azonban gyorsítótár-meghajtóként használhatók, és az összes többi meghajtót készletként fogja használni. A bérlői adattárolók (Blobok, táblák, várólisták és lemezek) a kapacitás-meghajtókra kerülnek. A prémium szintű lemezek kiosztása vagy a Premium Storage-fiók típusának kiválasztása nem garantálja, hogy az objektumok az SSD-vagy NVMe-meghajtókon lesznek lefoglalva.

## <a name="volumes"></a>Kötetek

A *Storage szolgáltatás* a rendelkezésre álló tárolót külön kötetekre particionálja, amelyek a rendszer-és a bérlői adattároláshoz vannak lefoglalva. A kötetek a Storage-készletben lévő meghajtókat kombinálva biztosítják a hibatűrést, a méretezhetőséget és a Közvetlen tárolóhelyek teljesítménybeli előnyeit.

![Azure Stack hub Storage szolgáltatás partícióit megjelenítő diagram.](media/azure-stack-storage-infrastructure-overview/image4.svg)

### <a name="volume-types"></a>Kötetek típusai

Az Azure Stack hub Storage-készleten három típusú kötet jön létre:

1. A Azure Stack hub-infrastruktúra virtuális gépei és az alapszolgáltatások által használt **infrastruktúra** -kötetek gazdagép-fájljai.
1. A **virtuális gép** ideiglenes kötetei a bérlői virtuális gépekhez csatolt ideiglenes lemezeket tárolják, és ezeket az adatlemezeket tárolják.
1. Az **Object Store** -kötetek a bérlői adatkarbantartási blobokat, táblákat, várólistákat és virtuálisgép-lemezeket tartalmazzák.

### <a name="volumes-in-a-multi-node-deployment"></a>Kötetek egy több csomópontos üzemelő példányban

A többcsomópontos telepítésekben három infrastrukturális kötet található.

A virtuális gépek ideiglenes kötetei és az objektum-tároló kötetek száma megegyezik a Azure Stack hub üzemelő példányában található csomópontok számával:

- Négy csomópontos üzemelő példányon négy azonos virtuálisgép-Temp kötet és négy egyenlő objektum-tároló kötet található.

- Ha új csomópontot ad hozzá a fürthöz, a rendszer minden létrehozott típushoz új kötetet hoz létre.

- A kötetek száma változatlan marad, még akkor is, ha egy csomópont meghibásodik vagy törlődik.

> [!NOTE]
> Ha a [Azure stack Development Kit (ASDK)](../asdk/index.yml)használja, akkor egyetlen kötet több [megosztással](azure-stack-manage-storage-shares.md)is rendelkezik.

### <a name="fault-tolerance-and-mirroring"></a>Hibatűrés és tükrözés

A Közvetlen tárolóhelyekban lévő kötetek rugalmasságot biztosítanak a hardveres problémák, például a meghajtó-vagy kiszolgálóhiba elleni védelemhez. Lehetővé teszik a folyamatos rendelkezésre állást a kiszolgáló karbantartása során, például a szoftverfrissítéseket.

A tükrözések hibatűrést biztosítanak, ha az összes adattal több példányt tart. Az adatszalagos tárolás és a nem triviális, de a tükrözést használó összes tárolt információ többször is szerepel. A rendszer minden egyes példányt különböző fizikai hardverre (különböző kiszolgálókon lévő különböző meghajtókra) ír, amelyeket feltételez, hogy egymástól függetlenül meghibásodik. 

Azure Stack hub üzemelő példánya háromutas tükrözéssel biztosítja az adatrugalmasságot. A háromutas tükrözés képes biztonságosan elviselni legalább két hardveres problémát (meghajtó vagy kiszolgáló) egyszerre. Ha például egy kiszolgáló újraindításakor hirtelen egy másik meghajtó vagy kiszolgáló meghibásodik, az összes adat biztonságban és folyamatosan elérhető marad.

A bérlői adatmennyiség három példánya különböző kiszolgálókra van írva, ahol a gyorsítótárban találhatók:

![Diagram, amely azt mutatja, hogy a bérlői adatmennyiség három példánya hogyan íródik különböző kiszolgálókra.](media/azure-stack-storage-infrastructure-overview/image5.png)

## <a name="next-step"></a>Következő lépés

[Tárolási kapacitás kezelése](azure-stack-manage-storage-shares.md) 
