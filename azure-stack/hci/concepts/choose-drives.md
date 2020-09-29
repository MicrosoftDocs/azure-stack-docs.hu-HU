---
title: Meghajtók kiválasztása Azure Stack HCI-hez
description: Meghajtók kiválasztása Azure Stack HCI-hez.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/01/2020
ms.openlocfilehash: a1283982ba04acd8de0b54c02fbc0bb88da9ebc6
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742306"
---
# <a name="choose-drives-for-azure-stack-hci"></a>Meghajtók kiválasztása Azure Stack HCI-hez

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Ez a témakör útmutatást nyújt a meghajtók kiválasztásához a Azure Stack HCI teljesítményére és kapacitására vonatkozó követelmények teljesítéséhez.

## <a name="drive-types"></a>Meghajtótípusok

A Azure Stack HCI jelenleg négyféle típusú meghajtóval működik:

| Meghajtó típusa | Leírás |
|----------------------|--------------------------|
|![PMem](media/choose-drives/pmem-100px.png)|A **PMem** az állandó memóriára, egy új, kis késleltetésű, nagy teljesítményű tárhelyre utal.|
|![NVMe](media/choose-drives/NVMe-100-px.png)|A **NVMe** (nem felejtő memória expressz) olyan SSD-meghajtókra utal, amelyek közvetlenül a PCIe-buszon ülnek. Az általános alaki tényezők a következők: 2,5 "U. 2, PCIe add-in-Card (AIC) és M. 2. A NVMe magasabb IOPS és IO-átviteli sebességet kínál, amely alacsonyabb késéssel rendelkezik, mint bármely más típusú meghajtó, amelyet ma támogatunk, kivéve a PMem.|
|![SSD](media/choose-drives/SSD-100-px.png)|Az **SSD** olyan SSD-meghajtókra utal, amelyek hagyományos SATA-vagy sas-kapcsolaton keresztül csatlakoznak.|
|![HDD](media/choose-drives/HDD-100-px.png)|A **HDD** olyan rotációs, mágneses merevlemez-meghajtókra utal, amelyek nagy kapacitású tárolókapacitást kínálnak.|

## <a name="built-in-cache"></a>Beépített gyorsítótár

Azure Stack a HCI egy beépített kiszolgálóoldali gyorsítótárat tartalmaz. Ez egy nagy, állandó, valós idejű olvasási és írási gyorsítótár. A több típusú meghajtóval üzemelő példányok esetében a rendszer automatikusan konfigurálja a "leggyorsabb" típus összes meghajtójának használatát. A fennmaradó meghajtók szolgálnak a tárolókapacitás biztosítására.

További információkért tekintse meg [a gyorsítótár Azure stack HCI-ben való megismerését ismertető](cache.md)témakört.

## <a name="option-1--maximizing-performance"></a>1. lehetőség – a teljesítmény maximalizálása

Ha az adatok véletlenszerű olvasásakor és írásakor kiszámítható és egységes ezredmásodperces késést szeretne elérni, vagy rendkívül magas IOPS ( [13 000 000](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-new-hci-industry-record-13-7-million-iops-with-windows/ba-p/428314)!) vagy i/o-sebesség elérésére van szükség (több mint 500 GB/s olvasás), akkor az "All-Flash" kifejezést kell elvégeznie.

Több módon is elvégezhető:

![A diagram az üzembe helyezési lehetőségeket mutatja be, beleértve az összes N V M kapacitást, az N V M e-t, a kapacitáshoz S S D-vel, valamint az összes S S D kapacitást.](media/choose-drives/All-Flash-Deployment-Possibilities.png)

1. **Minden NVMe.** Az összes NVMe használata páratlan teljesítményt biztosít, beleértve a legelőre jelezhető kis késleltetést. Ha az összes meghajtó ugyanaz a modell, nincs gyorsítótár. Emellett magasabb szintű és alacsonyabb tartósságú NVMe modelleket is felhasználhat, és konfigurálhatja a korábbi gyorsítótár-írási műveleteket az utóbbi számára ([beállítás szükséges](/windows-server/storage/storage-spaces/understand-the-cache#manual-configuration)).

2. **NVMe + SSD.** A NVMe és az SSD-k együttes használatával a NVMe automatikusan gyorsítótárazza az SSD-ket az írásokkal. Ez lehetővé teszi, hogy az írási műveletek a gyorsítótárban legyenek egyesítve, és az SSD-k kopásának csökkentése érdekében csak szükség szerint legyenek elrendezve. Ez a NVMe hasonló írási tulajdonságokat nyújt, az olvasások pedig közvetlenül a szintén gyors SSD-lemezekről származnak.

3. **Minden SSD.** Akárcsak a NVMe esetében, nincs gyorsítótár, ha az összes meghajtó ugyanaz a modell. Ha nagyobb tartósságot és alacsonyabb tartósságú modelleket használ, akkor a korábbi gyorsítótár-írási műveleteket is beállíthatja az utóbbi számára ([beállítás szükséges](/windows-server/storage/storage-spaces/understand-the-cache#manual-configuration)).

   >[!NOTE]
   > A teljes NVMe vagy az összes – SSD és gyorsítótár nélküli használat előnye, hogy minden meghajtón használható tárolási kapacitást kap. A gyorsítótárazás nem rendelkezik "költött" kapacitással, ami kisebb méretekben is vonzó lehet.

## <a name="option-2--balancing-performance-and-capacity"></a>2. lehetőség – a teljesítmény és a kapacitás kiegyensúlyozása

A különböző alkalmazásokkal és számítási feladatokkal rendelkező környezetek esetében, amelyek némelyike szigorú teljesítménybeli követelményekkel és a jelentős tárolókapacitást igénylő egyéb szolgáltatásokkal rendelkezik, a nagyobb HDD-k esetében NVMe vagy SSD-gyorsítótárazással kell eljárnia.

![A diagram az üzembe helyezési lehetőségeket jeleníti meg, beleértve az N V M-t is, a kapacitáshoz H d d-t tartalmazó gyorsítótárhoz, S D-t, a kapacitáshoz h d d-t tartalmazó gyorsítótárhoz, illetve N V M E-re a kapacitáshoz a (z) és H D](media/choose-drives/Hybrid-Deployment-Possibilities.png)

1. **NVMe + HDD**. A NVMe-meghajtók felgyorsítják az olvasást és az írást a gyorsítótárazással is. A gyorsítótárazási olvasási funkció lehetővé teszi, hogy a HDD-k az írásokra összpontosítsanak. A gyorsítótárazási írások elnyelik a töréseket, és lehetővé teszik az írások egyesítését, és csak szükség esetén, mesterségesen szerializált módon, a HDD-IOPS és az i/o-átviteli sebesség maximalizálása érdekében. Ez NVMe-szerű írási jellemzőket biztosít, valamint a gyakran vagy a legutóbb olvasott adatok NVMe is.

2. **SSD és HDD**. A fentiekhez hasonlóan az SSD-k is felgyorsítják az olvasási és írási műveleteket a gyorsítótárazással. Ez biztosítja az SSD-hez hasonló írási tulajdonságokat, valamint az SSD-hez hasonló olvasási jellemzőket a gyakran vagy a legutóbb olvasott adatokhoz.

    Egy további, inkább egzotikus megoldás *: a mindhárom típusú meghajtó* használata.

3. **NVMe + SSD + HDD.** Mindhárom típusú meghajtó esetében a NVMe az SSD-k és a HDD-k esetében is gyorsítótárba kerül. A fellebbezés célja, hogy köteteket hozzon létre az SSD-meghajtókon, valamint a HDD-k köteteit ugyanazon a fürtön egymás mellett, a NVMe gyorsítsa fel. Az előző pontosan ugyanúgy, mint az "összes Flash" üzemelő példányban, és az utóbbi pontosan ugyanúgy, mint a fent ismertetett "hibrid" központi telepítésekben. Ez fogalmi módon úgy viselkedik, mint két készlet, nagymértékben független kapacitás-felügyeleti, meghibásodási és javítási ciklusokkal, és így tovább.

   >[!IMPORTANT]
   > Javasoljuk, hogy az SSD-réteg használatával helyezze el a legtöbb teljesítményre érzékeny munkaterhelést az összes flash-meghajtón.

## <a name="option-3--maximizing-capacity"></a>3. lehetőség – a kapacitás maximalizálása

A nagy kapacitást igénylő és ritkán írható számítási feladatokhoz, például archiváláshoz, biztonsági mentési célokhoz, adattárházak vagy "hideg" tároláshoz össze kell kapcsolni néhány SSD-t, hogy a gyorsítótárazáshoz több nagyobb HDD legyen a kapacitása.

![Üzembe helyezési lehetőségek a kapacitás maximalizálása céljából](media/choose-drives/maximizing-capacity.png)

1. **SSD és HDD**. Az SSD gyorsítótárba helyezi az olvasásokat és az írásokat, és beolvassa a terheléseket, és az SSD-hez hasonló írási teljesítményt nyújt, és a HDD-re optimalizált, a későbbiekben is.

>[!IMPORTANT]
>A csak HDD-val történő konfiguráció nem támogatott. Az SSD-k alacsony tartósságú SSD-k általi gyorsítótárazása nem ajánlott.

## <a name="sizing-considerations"></a>Méretezési szempontok

### <a name="cache"></a>Gyorsítótár

Minden kiszolgálónak legalább két gyorsítótár-meghajtóval kell rendelkeznie (a redundancia minimális követelménye). Azt javasoljuk, hogy a kapacitások számának többszörösét a gyorsítótár-meghajtók száma határozza meg. Ha például 4 gyorsítótár-meghajtóval rendelkezik, a rendszer konzisztens teljesítményt nyújt 8 kapacitású meghajtóval (1:2 arány), mint 7 vagy 9.

A gyorsítótár méretének az alkalmazások és a számítási feladatok munkakészletének, azaz az összes olyan adatnak a megfelelőnek kell lennie, amelyik aktívan olvas és ír. Ezen túlmenően nincs szükség a gyorsítótár méretére. A HDD-vel végzett központi telepítések esetén a valós kiindulási hely a kapacitás 10 százaléka, például ha minden kiszolgáló 4 x 4 TB HDD = 16 TB kapacitással rendelkezik, akkor 2 x 800 GB SSD = 1,6 TB gyorsítótár kiszolgálónként. Az összes Flash-telepítés esetében – különösen a nagyon [nagy teljesítményű](https://techcommunity.microsoft.com/t5/storage-at-microsoft/understanding-ssd-endurance-drive-writes-per-day-dwpd-terabytes/ba-p/426024) SSD-k esetében – előfordulhat, hogy a kapacitás akár 5%-kal is megközelíthető, például ha minden kiszolgálón 24 x 1,2 TB SSD = 28,8 TB kapacitású, akkor 2 x 750 GB NVMe = 1,5 TB gyorsítótár kiszolgálónként. A módosításhoz később bármikor hozzáadhat vagy eltávolíthat gyorsítótár-meghajtókat.

### <a name="general"></a>Általános kérdések

Javasoljuk, hogy a kiszolgáló teljes tárolókapacitását körülbelül 400 terabájtra korlátozza (TB). A több tárolási kapacitás kiszolgálónként, annál hosszabb idő szükséges az adatleállás vagy újraindítás utáni újraszinkronizáláshoz, például a szoftverfrissítések alkalmazásakor. A Storage-készlet jelenlegi maximális mérete 4 petabájt (PB) (4 000 TB) a Windows Server 2019.

## <a name="next-steps"></a>Következő lépések

További információért lásd még:

- [A gyorsítótár megismerése](cache.md)
- [Hardverkövetelmények meghatározása](../deploy/before-you-start.md#determine-hardware-requirements)
- [A meghajtó-szimmetria szempontjai](drive-symmetry-considerations.md)
- [Kötetek megtervezése](plan-volumes.md)
- [Hibatűrés és a tárolás hatékonysága](fault-tolerance.md)
- [Az állandó memória megismerése és üzembe helyezése](/windows-server/storage/storage-spaces/deploy-pmem)
