---
title: Azure Stack hub Storage-infrastruktúra áttekintése
titleSuffix: Azure Stack
description: Ismerje meg, hogyan kezelheti Azure Stack hub tárolási infrastruktúráját.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 5/5/2020
ms.author: inhenkel
ms.lastreviewed: 5/5/2020
ms.reviewer: jiaha
ms.openlocfilehash: de6da5b42a88d2c1f9689fe8c43d898bc7a0bf5e
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82848166"
---
# <a name="azure-stack-hub-storage-infrastructure-overview"></a>Azure Stack hub Storage-infrastruktúra áttekintése

Ez a cikk áttekintést nyújt Azure Stack hub Storage-infrastruktúráról.

## <a name="understand-drives-and-volumes"></a>Meghajtók és kötetek ismertetése

### <a name="drives"></a>Meghajtók

A Windows Server szoftverrel működő Azure Stack hub Közvetlen tárolóhelyek (S2D) és a Windows Server feladatátvételi fürtszolgáltatás együttes használatával határozza meg a tárolási képességeket. Ez a kombináció egy teljesítményű, méretezhető és rugalmas tárolási szolgáltatást biztosít.

Azure Stack hub integrált rendszerpartnerei számos megoldási variációt kínálnak, beleértve a tárolási rugalmasság széles körét. Jelenleg legfeljebb két meghajtót választhat a három támogatott meghajtó közül: NVMe (nem felejtő memória expressz), SATA/SAS SSD (SSD-meghajtó), HDD (merevlemez). 

Közvetlen tárolóhelyek tartalmaz egy gyorsítótárat a tárolási teljesítmény maximalizálása érdekében. Egy Azure Stack hub-készülék egyetlen meghajtóval (azaz NVMe vagy SSD-vel) minden meghajtót felhasznál a kapacitáshoz. Ha kétféle típusú meghajtó van, a Közvetlen tárolóhelyek automatikusan a "leggyorsabb" (NVMe &gt; SSD &gt; HDD) típusú meghajtókat használja a gyorsítótárazáshoz. A fennmaradó meghajtók szolgálnak a tárolókapacitás biztosítására. A meghajtók a következők egyike lehet: "All-Flash" vagy "hibrid".

![Azure Stack hub Storage-infrastruktúra](media/azure-stack-storage-infrastructure-overview/image1.png)

Az összes Flash-telepítés célja, hogy maximalizálja a tárolási teljesítményt, és ne tartalmazzon rotációs HDD-ket.

![Azure Stack hub Storage-infrastruktúra](media/azure-stack-storage-infrastructure-overview/image2.png)

A hibrid üzembe helyezések célja a teljesítmény és a kapacitás elosztása, illetve a kapacitás maximalizálása, valamint a rotációs HDD-k beépítése.

A gyorsítótár viselkedését a rendszer a gyorsítótárban lévő meghajtók típusa (i) alapján automatikusan meghatározza. SSD-k gyorsítótárazásakor (például SSD-NVMe gyorsítótárazás esetén) csak az írások vannak gyorsítótárazva. Ez csökkenti a kapacitás-meghajtók kopását, csökkenti a kapacitás-meghajtók összesített forgalmát és az élettartamuk kiterjesztését. Addig az olvasások nem vannak gyorsítótárazva. Nincsenek gyorsítótárazva, mert az olvasások nem befolyásolják jelentősen a Flash élettartamát, és mivel az SSD-k univerzálisan alacsony olvasási késést kínálnak. A HDD-k gyorsítótárazása (például SSD-meghajtók gyorsítótárazása) esetén az olvasás és az írás is gyorsítótárazva van, így a Flash-hez hasonló késés (gyakran/~ 10x jobb) is biztosítható mindkettőhöz.

![Azure Stack hub Storage-infrastruktúra](media/azure-stack-storage-infrastructure-overview/image3.png)

A tárterület elérhető konfigurációjának megadásához tekintse meg Azure Stack hub OEM-https://azure.microsoft.com/overview/azure-stack/partners/) partnert (részletes leírást.

> [!Note]  
> Azure Stack hub-készülék hibrid üzembe helyezhető, HDD-és SSD-(vagy NVMe-) meghajtókon is. A gyorsabb típusú meghajtók azonban gyorsítótár-meghajtóként használhatók, és az összes többi meghajtót készletként fogja használni. A bérlői adattárolók (Blobok, táblák, várólisták és lemezek) a kapacitás-meghajtókra kerülnek. A prémium szintű lemezek kiosztása vagy a Premium Storage-fiók típusának kiválasztása nem garantálja, hogy az objektumok az SSD-vagy NVMe-meghajtókon lesznek lefoglalva.

### <a name="volumes"></a>Kötetek

A *Storage szolgáltatás* a rendelkezésre álló tárolót külön kötetekre particionálja, amelyek a rendszer-és a bérlői adattároláshoz vannak lefoglalva. A kötetek a Storage-készletben lévő meghajtókat kombinálva biztosítják a hibatűrést, a méretezhetőséget és a Közvetlen tárolóhelyek teljesítménybeli előnyeit.

![Azure Stack hub Storage-infrastruktúra](media/azure-stack-storage-infrastructure-overview/image4.png)

Az Azure Stack hub Storage-készleten három típusú kötet jön létre:

- Infrastruktúra: Azure Stack hub infrastruktúra-alapú virtuális gépek és alapszolgáltatások által használt gazdagép-fájlok.

- VM Temp: a bérlői virtuális gépekhez csatolt ideiglenes lemezeket üzemelteti, és ezeket az adatlemezeket tárolja.

- Object Store: gazdagép-bérlői adatkarbantartási Blobok, táblák, várólisták és virtuális gépek lemezei.

A többcsomópontos telepítésekben három infrastrukturális kötet látható, míg a virtuális gépek ideiglenes kötetei és az objektum-tároló kötetek száma megegyezik a Azure Stack hub üzemelő példányában lévő csomópontok számával:

- Négy csomópontos üzemelő példányon négy azonos virtuálisgép-Temp kötet és négy egyenlő objektum-tároló kötet található.

- Ha új csomópontot ad hozzá a fürthöz, a rendszer minden létrehozott típushoz új kötetet hoz létre.

- A kötetek száma változatlan marad, még akkor is, ha egy csomópont meghibásodik vagy törlődik.

- Ha a Azure Stack Development Kit használja, egyetlen köteten több megosztás található.

A Közvetlen tárolóhelyekban lévő kötetek rugalmasságot biztosítanak a hardveres problémák, például a meghajtó-vagy kiszolgálóhiba elleni védelemhez. A folyamatos rendelkezésre állást is lehetővé teszik a kiszolgáló karbantartása során, például a szoftverfrissítéseket. Azure Stack hub üzemelő példánya háromutas tükrözéssel biztosítja az adatrugalmasságot. A bérlői adatmennyiség három példánya különböző kiszolgálókra van írva, ahol a gyorsítótárban találhatók:

![Azure Stack hub Storage-infrastruktúra](media/azure-stack-storage-infrastructure-overview/image5.png)

A tükrözések hibatűrést biztosítanak, ha az összes adattal több példányt tart. Az adatszalagos tárolás és a nem triviális, de igaz, hogy a tükrözés használatával tárolt összes adathalmazt többször kell megírni. A rendszer minden egyes példányt különböző fizikai hardverre (különböző kiszolgálókon lévő különböző meghajtókra) ír, amelyeket feltételez, hogy egymástól függetlenül meghibásodik. A háromutas tükrözés képes biztonságosan elviselni legalább két hardveres problémát (meghajtó vagy kiszolgáló) egyszerre. Ha például egy kiszolgáló újraindításakor hirtelen egy másik meghajtó vagy kiszolgáló meghibásodik, az összes adat biztonságban és folyamatosan elérhető marad.

## <a name="next-step"></a>Következő lépés

[Tárolási kapacitás kezelése](azure-stack-manage-storage-shares.md) 
