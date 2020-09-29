---
title: A Azure Stack HCI-hez szükséges adatmeghajtó-szimmetria szempontjai
description: Ez a témakör ismerteti a meghajtó-szimmetria korlátozásait, és példákat tartalmaz a támogatott és nem támogatott konfigurációkra.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/01/2020
ms.openlocfilehash: 545a0b90ad938a172a184748780974ba7403f19f
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742400"
---
# <a name="drive-symmetry-considerations-for-azure-stack-hci"></a>A Azure Stack HCI-hez szükséges adatmeghajtó-szimmetria szempontjai

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Azure Stack a HCI akkor működik a legjobban, ha minden kiszolgálón pontosan ugyanazok a meghajtók vannak.

A valóságban Felismertük, hogy ez nem mindig praktikus, mert Azure Stack HCI-t évekig, illetve a szervezet igényeinek megfelelően méretezhetővé tervezték. Ma a tágas 3 TB-os merevlemezeket is vásárolhat; a következő évben előfordulhat, hogy a kis méretű meghajtókat nem lehet megtalálni. Ezért a rendszer a keverés és a megfeleltetés bizonyos mennyiségét várt és támogatott. Ne feledje azonban, hogy a nagyobb szimmetria mindig jobb.

Ez a témakör ismerteti a korlátozásokat, és példákat tartalmaz a támogatott és nem támogatott konfigurációkra.

## <a name="constraints"></a>Korlátozások

### <a name="type"></a>Típus

Minden kiszolgálónak azonos [típusú meghajtóval](choose-drives.md#drive-types)kell rendelkeznie.

Ha például egy kiszolgáló rendelkezik NVMe, akkor *mindegyiknek* rendelkeznie kell NVMe.

### <a name="number"></a>Szám

Minden kiszolgálónak azonos számú meghajtóval kell rendelkeznie az egyes típusokhoz.

Ha például egy kiszolgáló hat SSD-vel rendelkezik, akkor *mind* a hat SSD-t kell tartalmaznia.

   > [!NOTE]
   > A meghajtók számának a meghibásodások esetén átmenetileg eltérőnek kell lennie, illetve meghajtók hozzáadásakor vagy eltávolításakor.

### <a name="model"></a>Modell

Ha lehetséges, javasoljuk, hogy ugyanazt a modellt és belső vezérlőprogram-verziót használó meghajtókat használja. Ha nem tudja, gondosan válassza ki a lehető leghasonló meghajtókat. Az egyazon típusú keverési és megfeleltetési meghajtókat nem vesszük igénybe élesen eltérő teljesítmény-vagy tartóssági jellemzőkkel (kivéve, ha az egyik gyorsítótár, a másik pedig a kapacitás), mivel Azure Stack HCI egyenletesen osztja el az IO-t, és nem tesz különbséget a modell alapján.

   > [!NOTE]
   > A hasonló SATA-és SAS-meghajtók összekeverése nem megfelelő.

### <a name="size"></a>Méret

Azt javasoljuk, hogy ha lehetséges, használjon azonos méretű meghajtókat. A különböző méretű kapacitás-meghajtók használata nem használható kapacitást eredményezhet, a különböző méretű gyorsítótár-meghajtók használata azonban nem javítja a gyorsítótár teljesítményét. A részleteket a következő szakaszban találhatja meg.

   > [!WARNING]
   > Az eltérő kapacitású meghajtók mérete a kiszolgálók között a kisodort kapacitást eredményezheti.

## <a name="understand-capacity-imbalance"></a>Megismerés: kapacitás-egyensúlyhiány

Azure Stack a HCI robusztus a meghajtók és a kiszolgálók között a kapacitás kiegyensúlyozatlansága terén. Még ha az egyensúlyhiány is súlyos, minden továbbra is működni fog. Számos tényezőtől függően azonban előfordulhat, hogy az egyes kiszolgálókon nem elérhető kapacitás nem használható.

Ha szeretné megtudni, hogy ez miért történik, vegye figyelembe az alábbi egyszerűsített illusztrációt. Mindegyik színes doboz a tükrözött adatmásolatot jelöli. Például az "a", "A" és A "" karakterrel jelölt mezők az azonos adatmennyiség három másolatát jelentik. A kiszolgáló hibatűrésének tiszteletben tartásához ezeket *a másolatokat* különböző kiszolgálókon kell tárolni.

### <a name="stranded-capacity"></a>Sodort kapacitás

A leírtak szerint a Server 1 (10 TB) és a 2. kiszolgáló (10 TB) megtelt. A 3. kiszolgáló nagyobb meghajtókkal rendelkezik, ezért a teljes kapacitása nagyobb (15 TB). Ahhoz azonban, hogy a 3. kiszolgálón több háromutas tükrözési adat legyen tárolva, az 1. és a 2. kiszolgáló is megkövetelheti, hogy a példányok már megteltek legyenek. A 3. kiszolgálón található fennmaradó 5 TB-os kapacitás nem használható – *"sodort"* kapacitás.

![Háromutas tükrözés, három kiszolgáló, sodort kapacitás](media/drive-symmetry-considerations/Size-Asymmetry-3N-Stranded.png)

### <a name="optimal-placement"></a>Optimális elhelyezés

Ezzel szemben a 10 TB-os, 10 TB-os, 10 TB-os és 15 TB-os kapacitású, valamint a háromutas tükrözési rugalmassággal rendelkező négy kiszolgáló esetén a másolatok olyan *formában is* érvényesek, amely az összes rendelkezésre álló kapacitást használja. Ha ez lehetséges, a Közvetlen tárolóhelyek lefoglalás megkeresi és felhasználja az optimális elhelyezést, így nem marad a rendelkezésre állási kapacitás.

![Háromutas tükrözés, négy kiszolgáló, nincs átadott kapacitás](media/drive-symmetry-considerations/Size-Asymmetry-4N-No-Stranded.png)

A kiszolgálók száma, a rugalmasság, a kapacitás-egyensúlyhiány súlyossága és más tényezők befolyásolják, hogy vannak-e a rendelkezésre álló kapacitások. **A legkörültekintőebb általános szabály azt feltételezi, hogy csak az összes kiszolgálón elérhető kapacitás használható.**

## <a name="understand-cache-imbalance"></a>Megismerés: gyorsítótár-egyensúlyhiány

Azure Stack HCI robusztus a meghajtók és a kiszolgálók közötti egyensúlyhiányok gyorsítótárazására. Még ha az egyensúlyhiány is súlyos, minden továbbra is működni fog. Emellett Azure Stack HCI mindig az összes elérhető gyorsítótárat használja a legteljesebb mértékben.

A különböző méretű gyorsítótár-meghajtók használata azonban nem javítja a gyorsítótár teljesítményét egységesen vagy kiszámíthatóan: a nagyobb gyorsítótár-meghajtókhoz tartozó [kötések](cache.md#server-side-architecture) esetében csak az IO lehet jobb teljesítményt látni. Azure Stack HCI egyenletesen osztja el az IO-t a kötések között, és nem tesz különbséget a gyorsítótár – kapacitás arány alapján.

![Gyorsítótár-egyensúlyhiány](media/drive-symmetry-considerations/Cache-Asymmetry.png)

   > [!TIP]
   > A gyorsítótár-kötésekkel kapcsolatos további tudnivalókért tekintse meg [a gyorsítótár ismertetése](cache.md) című témakört.

## <a name="example-configurations"></a>Példa konfigurációk

Íme néhány támogatott és nem támogatott konfiguráció:

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-models-between-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: Támogatott: különböző modellek a kiszolgálók között

Az első két kiszolgáló a "X" NVMe modellt használja, de a harmadik kiszolgáló a "Z" NVMe modellt használja, ami nagyon hasonló.

| 1. kiszolgáló                    | 2. kiszolgáló                    | 3. kiszolgáló                    |
|-----------------------------|-----------------------------|-----------------------------|
| 2 x NVMe-modell X (gyorsítótár)    | 2 x NVMe-modell X (gyorsítótár)    | 2 x NVMe Model Z (gyorsítótár)    |
| 10 x SSD-modell Y (kapacitás) | 10 x SSD-modell Y (kapacitás) | 10 x SSD-modell Y (kapacitás) |

Ez támogatott.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-models-within-server"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: Támogatott: különböző modellek a kiszolgálón belül

Minden kiszolgáló az "Y" és a "Z" HDD-modellek eltérő kombinációját használja, amelyek nagyon hasonlóak. Minden kiszolgáló 10 teljes HDD-t tartalmaz.

| 1. kiszolgáló                   | 2. kiszolgáló                   | 3. kiszolgáló                   |
|----------------------------|----------------------------|----------------------------|
| 2 x SSD-modell X (gyorsítótár)    | 2 x SSD-modell X (gyorsítótár)    | 2 x SSD-modell X (gyorsítótár)    |
| 7 x HDD-modell Y (kapacitás) | 5 x HDD-modell Y (kapacitás) | 1 x HDD-modell Y (kapacitás) |
| 3 x HDD Model Z (kapacitás) | 5 x HDD Model Z (kapacitás) | 9 x HDD Model Z (kapacitás) |

Ez támogatott.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-sizes-across-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: Támogatott: különböző méretek a kiszolgálók között

Az első két kiszolgáló 4 TB-os HDD-t használ, de a harmadik kiszolgáló nagyon hasonló 6 TB-os HDD-t használ.

| 1. kiszolgáló                | 2. kiszolgáló                | 3. kiszolgáló                |
|-------------------------|-------------------------|-------------------------|
| 2 x 800 GB NVMe (gyorsítótár) | 2 x 800 GB NVMe (gyorsítótár) | 2 x 800 GB NVMe (gyorsítótár) |
| 4 x 4 TB-os HDD (kapacitás) | 4 x 4 TB-os HDD (kapacitás) | 4 x 6 TB HDD (kapacitás) |

Ez támogatott, bár a művelet a kiszolgált kapacitást eredményezi.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-sizes-within-server"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: Támogatott: különböző méretek a kiszolgálón belül

Minden kiszolgáló a 1,2 TB és a nagyon hasonló 1,6 TB SSD kombinációt használja. Minden kiszolgáló 4 teljes SSD-t tartalmaz.

| 1. kiszolgáló                 | 2. kiszolgáló                 | 3. kiszolgáló                 |
|--------------------------|--------------------------|--------------------------|
| 3 x 1,2 TB SSD (gyorsítótár)   | 2 x 1,2 TB SSD (gyorsítótár)   | 4 x 1,2 TB SSD (gyorsítótár)   |
| 1 x 1,6 TB SSD (gyorsítótár)   | 2 x 1,6 TB SSD (gyorsítótár)   | -                        |
| 20 x 4 TB-os HDD (kapacitás) | 20 x 4 TB-os HDD (kapacitás) | 20 x 4 TB-os HDD (kapacitás) |

Ez támogatott.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationsunsupportedpng-borderfalse-not-supported-different-types-of-drives-across-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/unsupported.png" border="false"::: Nem támogatott: különböző típusú meghajtók a kiszolgálók között

Az 1. kiszolgáló rendelkezik NVMe, de a többi nem.

| 1. kiszolgáló            | 2. kiszolgáló            | 3. kiszolgáló            |
|---------------------|---------------------|---------------------|
| 6 x NVMe (gyorsítótár)    | -                   | -                   |
| -                   | 6 x SSD (gyorsítótár)     | 6 x SSD (gyorsítótár)     |
| 18 x HDD (kapacitás) | 18 x HDD (kapacitás) | 18 x HDD (kapacitás) |

Ez a funkció nem támogatott. A meghajtók típusának azonosnak kell lennie minden kiszolgálón.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationsunsupportedpng-borderfalse-not-supported-different-number-of-each-type-across-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/unsupported.png" border="false"::: Nem támogatott: eltérő számú egyes típus a kiszolgálók között

A 3. kiszolgáló több meghajtót tartalmaz, mint a többi.

| 1. kiszolgáló            | 2. kiszolgáló            | 3. kiszolgáló            |
|---------------------|---------------------|---------------------|
| 2 x NVMe (gyorsítótár)    | 2 x NVMe (gyorsítótár)    | 4 x NVMe (gyorsítótár)    |
| 10 x HDD (kapacitás) | 10 x HDD (kapacitás) | 20 x HDD (kapacitás) |

Ez a funkció nem támogatott. Az egyes típusú meghajtók számának azonosnak kell lennie minden kiszolgálón.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationsunsupportedpng-borderfalse-not-supported-only-hdd-drives"></a>:::image type="icon" source="media/drive-symmetry-considerations/unsupported.png" border="false"::: Nem támogatott: csak HDD-meghajtók

Minden kiszolgálón csak a HDD-meghajtók vannak csatlakoztatva.

|1. kiszolgáló|2. kiszolgáló|3. kiszolgáló|
|-|-|-|
|18 x HDD (kapacitás) |18 x HDD (kapacitás)|18 x HDD (kapacitás)|

Ez a funkció nem támogatott. Hozzá kell adnia legalább két gyorsítótár-meghajtót (NvME vagy SSD) az egyes kiszolgálókhoz.

## <a name="summary"></a>Összefoglalás

Az újraküldéshez a fürtben lévő összes kiszolgálónak azonos típusú meghajtóval és azonos számú típussal kell rendelkeznie. A fenti szempontok alapján a meghajtó modelljeinek és a szükséges méreteknek a használata támogatott.

| Korlátozás | Állapot |
|--|--|
| Azonos típusú meghajtók minden kiszolgálón | **Kötelező** |
| Az összes kiszolgáló azonos számú típusa | **Kötelező** |
| Minden kiszolgáló azonos meghajtó-modelljei | Ajánlott |
| Azonos méretű meghajtók minden kiszolgálón | Ajánlott |

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Azure Stack HCI üzembe helyezése előtt](../deploy/before-you-start.md)
- [Meghajtók kiválasztása](choose-drives.md)
