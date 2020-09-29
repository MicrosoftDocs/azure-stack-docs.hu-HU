---
title: A Storage-készlet gyorsítótárának megismerése Azure Stack HCI-ben
description: Az olvasási és írási gyorsítótárazás működése Közvetlen tárolóhelyek és Azure Stack HCI-ben.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/04/2020
ms.openlocfilehash: 9a15b953ffe2229d7f92bea998392b8570f481de
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742519"
---
# <a name="understanding-the-storage-pool-cache-in-azure-stack-hci"></a>A Storage-készlet gyorsítótárának megismerése Azure Stack HCI-ben

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Azure Stack HCI a tárolási teljesítmény maximalizálása érdekében egy beépített kiszolgálóoldali gyorsítótárat tartalmaz. Ez egy nagy, állandó, valós idejű olvasási *és* írási gyorsítótár. A gyorsítótár a Azure Stack HCI telepítésekor automatikusan konfigurálva van. A legtöbb esetben nincs szükség manuális felügyeletre. A gyorsítótár működése a meghajtók típusaitól függ.

Az alábbi videó részletesen ismerteti, hogyan működik a gyorsítótárazás a Közvetlen tárolóhelyek, a mögöttes Storage virtualizációs technológia Azure Stack a HCI mögött, valamint más tervezési szempontokat.

<strong>Tervezési szempontok Közvetlen tárolóhelyek</strong><br>(20 perc)<br>
<iframe src="https://channel9.msdn.com/Blogs/windowsserver/Design-Considerations-for-Storage-Spaces-Direct/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="drive-types-and-deployment-options"></a>Meghajtók típusai és üzembe helyezési lehetőségek

A Azure Stack HCI jelenleg négyféle típusú meghajtóval működik:

| Meghajtó típusa | Leírás |
|----------------------|--------------------------|
|![PMem](media/choose-drives/pmem-100px.png)|A **PMem** az állandó memóriára, egy új, kis késleltetésű, nagy teljesítményű tárterületre utal.|
|![NVMe](media/choose-drives/NVMe-100-px.png)|A **NVMe** (nem felejtő memória expressz) olyan SSD-meghajtókra utal, amelyek közvetlenül a PCIe-buszon ülnek. Az általános alaki tényezők a következők: 2,5 "U. 2, PCIe add-in-Card (AIC) és M. 2. A NVMe magasabb IOPS és IO-átviteli sebességet kínál, amely alacsonyabb késéssel rendelkezik, mint bármely más típusú meghajtó, amelyet ma támogatunk, kivéve a PMem.|
|![SSD](media/choose-drives/SSD-100-px.png)|Az **SSD** olyan SSD-meghajtókra utal, amelyek hagyományos SATA-vagy sas-kapcsolaton keresztül csatlakoznak.|
|![HDD](media/choose-drives/HDD-100-px.png)|A **HDD** olyan rotációs, mágneses merevlemez-meghajtókra utal, amelyek nagy kapacitású tárolókapacitást kínálnak.|

Ezek különböző módokon kombinálhatók, amelyeket két kategóriába csoportosítunk: "All-Flash" és "Hybrid".

### <a name="all-flash-deployment-possibilities"></a>Összes-Flash üzembe helyezési lehetőség

Az összes Flash-telepítés célja, hogy maximalizálja a tárolási teljesítményt, és ne tartalmazzon rotációs merevlemez-meghajtókat (HDD-ket).

![A diagramon az összes Flash üzemelő példány látható, beleértve az N V M e kapacitást, N V M e-t a kapacitáshoz S S D-vel való gyorsítótárra, valamint az s D kapacitást.](media/cache/All-Flash-Deployment-Possibilities.png)

### <a name="hybrid-deployment-possibilities"></a>Hibrid üzembe helyezési lehetőségek

A hibrid üzembe helyezések célja a teljesítmény és a kapacitás elosztása, illetve a kapacitás maximalizálása, valamint a rotációs merevlemez-meghajtók (HDD) kiépítése.

![A diagramon a hibrid központi telepítések láthatók, beleértve az N V M-t, a kapacitáshoz H d d-t tartalmazó gyorsítótárhoz, S D-t a kapacitáshoz h d d-vel, és N V M e a gyorsítótárba, H d d-vel, a kapacitáshoz pedig k S D](media/cache/Hybrid-Deployment-Possibilities.png)

## <a name="cache-drives-are-selected-automatically"></a>A gyorsítótár-meghajtók automatikusan ki vannak választva

A több típusú meghajtóval üzemelő példányok esetében Azure Stack a HCI automatikusan a "leggyorsabb" típusú meghajtókat használja a gyorsítótárazáshoz. A fennmaradó meghajtók szolgálnak a tárolókapacitás biztosítására.

A "leggyorsabb" típus a következő hierarchia alapján van meghatározva.

![Az ábrán az N V. e, S S D, a nem címkézett lemez H D](media/cache/Drive-Type-Hierarchy.png)

Ha például NVMe és SSD-ket tartalmaz, a NVMe gyorsítótárazza az SSD-ket.

Ha SSD-k és HDD-k is vannak, akkor az SSD-k gyorsítótárazva lesznek a HDD-k számára.

   >[!NOTE]
   > A gyorsítótár-meghajtók nem járulnak hozzá a használható tárolási kapacitáshoz. A gyorsítótárban tárolt összes adatmennyiség máshol is tárolva lesz, vagy ha ez a fázis. Ez azt jelenti, hogy az üzemelő példány teljes nyers tárolókapacitása csak a kapacitás-meghajtók összege.

Ha az összes meghajtó azonos típusú, a rendszer nem konfigurálja automatikusan a gyorsítótárat. Lehetősége van arra, hogy manuálisan konfigurálja a nagyobb teljesítményű meghajtókat az azonos típusú alacsonyabb tartósságú meghajtók gyorsítótárazásához – lásd a [manuális konfigurációs](#manual-configuration) szakaszt, amelyből megtudhatja, hogyan.

   >[!TIP]
   > A NVMe vagy az összes SSD üzemelő példányban – különösen a nagyon kis méretekben – a gyorsítótárban lévő "elköltött" meghajtók esetében nem lehet hasznos a tárhely hatékonyságának javítása.

## <a name="cache-behavior-is-set-automatically"></a>A gyorsítótár viselkedése automatikusan be van állítva

A gyorsítótár viselkedését a rendszer a gyorsítótárban lévő meghajtók típusa (i) alapján automatikusan meghatározza. SSD-meghajtók gyorsítótárazásakor (például SSD-NVMe gyorsítótárazás esetén) csak az írások vannak gyorsítótárazva. A merevlemez-meghajtók gyorsítótárazása (például SSD-meghajtók gyorsítótárazása) esetén az olvasás és az írás is gyorsítótárazva van.

![Az összes Flash esetében a gyorsítótárazást hasonlító diagram, amely az írásokat gyorsítótárazza, és az olvasások nem, hibrid, ahol az olvasások és az írások is gyorsítótárazva vannak.](media/cache/Cache-Read-Write-Behavior.png)

### <a name="write-only-caching-for-all-flash-deployments"></a>Írásvédett gyorsítótárazás az összes Flash-telepítéshez

SSD-meghajtók (NVMe vagy SSD-k) gyorsítótárazásakor a rendszer csak az írásokat gyorsítótárazza. Ez csökkenti a kapacitás-meghajtók kopását, mivel számos írás és újraírás egyesíthető a gyorsítótárban, majd csak szükség szerint elvégezhető, így csökkentve a kapacitás-meghajtók összesített forgalmát és az élettartamuk kiterjesztését. Ezért javasoljuk, hogy a gyorsítótárhoz [nagyobb teljesítményű, írásra optimalizált](http://whatis.techtarget.com/definition/DWPD-device-drive-writes-per-day) meghajtók legyenek kiválasztva. Előfordulhat, hogy a kapacitási meghajtók alacsonyabb írási állóképességtel rendelkeznek.

Mivel az olvasások nem befolyásolják jelentősen a Flash élettartamát, és mivel a szilárdtest-meghajtók univerzálisan alacsony olvasási késést kínálnak, az olvasások nem kerülnek a gyorsítótárba: közvetlenül a kapacitás-meghajtókról lesznek kézbesítve (kivéve, ha az adatokat a közelmúltban írták, de még nem állították be). Ez lehetővé teszi, hogy a gyorsítótár kizárólag írásra legyen kijelölve, maximalizálva a hatékonyságot.

Ez írási tulajdonságokat (például írási késést) eredményez, amelyet a gyorsítótár-meghajtók diktálnak, míg az olvasási jellemzőket a kapacitási meghajtók diktálják. Mindkettő konzisztens, kiszámítható és egységes.

### <a name="readwrite-caching-for-hybrid-deployments"></a>Írási/olvasási gyorsítótárazás a hibrid üzembe helyezésekhez

A merevlemez-meghajtók (HDD-k) gyorsítótárazásakor a rendszer a beolvasást *és* az írást is gyorsítótárazza, így a Flash-hez hasonló késleltetést (gyakran ~ 10x jobbat) is biztosít mindkettőhöz. Az olvasási gyorsítótár a közelmúltban és gyakran olvassa be az adatokat a gyors elérés érdekében, és a HDD-re való véletlenszerű adatforgalom minimalizálására. (A keresési és a rotációs késések miatt jelentős a HDD-vel való véletlenszerű hozzáférés esetén felmerülő késés és idő.) Az írások a gyorsítótárba kerülnek, így az írásokat és a korábbikat is összevontuk

A Azure Stack HCI olyan algoritmust valósít meg, amely az írások előtt, de véletlenszerűen írja elő őket, hogy egy i/o-mintát emuláljon a lemezre, amely akkor is a szekvenciálisnak tűnik, ha a számítási feladat (például a virtuális gépek) tényleges IO-értéke véletlenszerű. Ez maximalizálja a IOPS és az átviteli sebességet a HDD-k számára.

### <a name="caching-in-deployments-with-nvme-ssd-and-hdd"></a>Gyorsítótárazás a NVMe, SSD és HDD környezetekben üzemelő példányokban

Ha mindhárom típusú meghajtó megtalálható, a NVMe-meghajtók gyorsítótárazást biztosítanak az SSD-k és a HDD-k számára egyaránt. A viselkedés a fent leírtak szerint történik: csak a (z) rendszerű meghajtók gyorsítótárazva vannak az SSD-k számára A HDD-k gyorsítótárazásának terhét egyenletesen osztják el a gyorsítótár-meghajtók között.

## <a name="summary"></a>Összefoglalás

Ez a táblázat összefoglalja, hogy mely meghajtók használatosak a kapacitáshoz, és hogy a gyorsítótárazás milyen viselkedést biztosít az egyes telepítési lehetőségekhez.

| Üzembe helyezés     | Gyorsítótár-meghajtók                        | Kapacitás-meghajtók | Gyorsítótár viselkedése (alapértelmezett)  |
| -------------- | ----------------------------------- | --------------- | ------------------------- |
| Minden NVMe         | Nincs (nem kötelező: konfigurálás manuálisan) | NVMe            | Csak írható (ha be van állítva)  |
| Minden SSD          | Nincs (nem kötelező: konfigurálás manuálisan) | SSD             | Csak írható (ha be van állítva)  |
| NVMe + SSD       | NVMe                                | SSD             | Csak írható                  |
| NVMe + HDD       | NVMe                                | HDD             | Olvasás és írás                |
| SSD ÉS HDD        | SSD                                 | HDD             | Olvasás és írás                |
| NVMe + SSD + HDD | NVMe                                | SSD ÉS HDD       | Olvasás + írás a HDD-hez, csak írható SSD-hez  |

## <a name="server-side-architecture"></a>Kiszolgálóoldali architektúra

A gyorsítótár a meghajtó szintjén van megvalósítva: az egyik kiszolgálón belüli egyedi gyorsítótár-meghajtók egy vagy több, ugyanazon a kiszolgálón belüli kapacitású meghajtóhoz vannak kötve.

Mivel a gyorsítótár nem éri el a Windows szoftver által definiált tárolási verem többi részét, nincs szükség sem arra, hogy tisztában legyenek a fogalmakkal, például a tárolóhelyekkel vagy a hibatűréssel. Azt is megteheti, hogy "hibrid" (részben Flash, részben lemezes) meghajtókat hoz létre, amelyeket aztán a Windows megmutat. A tényleges hibrid meghajtókhoz hasonlóan a fizikai adathordozó gyorsabb és lassabb részei között a gyors és a meleg adatok valós idejű áthelyezése szinte láthatatlan a külvilágtól.

Mivel a Azure Stack HCI rugalmassága legalább kiszolgálói szintű (vagyis az adatmásolatok mindig a különböző kiszolgálókra vannak írva; a kiszolgálón legfeljebb egy másolat) a gyorsítótárban lévő adatok a gyorsítótárban lévő adatokkal azonos rugalmassággal rendelkeznek.

![A diagram három, a tárolóhelyek rétegében található háromutas tükrözéssel összekapcsolt kiszolgálót jelöl, amely a nem címkézett kapacitású meghajtók eléréséhez hozzáférő N V M e meghajtók gyorsítótári rétegéhez fér hozzá.](media/cache/Cache-Server-Side-Architecture.png)

Például, ha háromutas tükrözést használ, az összes adattal kapcsolatos három másolat különböző kiszolgálókra kerül, ahol a gyorsítótárba kerülnek. Függetlenül attól, hogy későbbiek-e vagy sem, három másolat mindig létezik.

## <a name="drive-bindings-are-dynamic"></a>A meghajtó kötései dinamikusak

A gyorsítótár és a kapacitás-meghajtók közötti kötés bármilyen arányban lehet, 1:1 és 1:12 között. Dinamikusan módosítja a meghajtók hozzáadását vagy eltávolítását, például a hibák felskálázásakor vagy a meghibásodások után. Ez azt jelenti, hogy a gyorsítótár-meghajtók vagy a kapacitás-meghajtók egymástól függetlenül vehetők fel, ha szeretné.

![Az animált ábrán két N V M e gyorsítótár-meghajtó dinamikusan van hozzárendelve az első négyhez, majd hat, majd nyolc kapacitású meghajtóhoz.](media/cache/Dynamic-Binding.gif)

Azt javasoljuk, hogy a kapacitások számának többszöröse legyen a gyorsítótár-meghajtók száma a szimmetria szempontjából. Ha például 4 gyorsítótár-meghajtóval rendelkezik, még nagyobb teljesítményt fog tapasztalni 8 kapacitású meghajtóval (1:2 arány), mint 7 vagy 9.

## <a name="handling-cache-drive-failures"></a>Gyorsítótár-meghajtók hibáinak kezelése

Ha egy gyorsítótár-meghajtó meghibásodik, a rendszer az összes olyan írást elveszíti, amely még nem lett elküldve *a helyi kiszolgálónak*, azaz csak a többi példányon (más kiszolgálókon) található. Ugyanúgy, mint bármely más meghajtó meghibásodása után, a tárolóhelyek és az automatikus helyreállítás a túlélő másolatokkal folytatott tanácsadással történik.

Egy rövid ideig az elveszett gyorsítótár-meghajtóhoz kötött kapacitási meghajtók nem Kifogástalan állapotba kerülnek. Ha a gyorsítótár-újrakötés bekövetkezett (automatikus), és az adatjavítás befejeződött (automatikus), a rendszer Kifogástalan állapotba helyezi.

Ennek a forgatókönyvnek az az oka, hogy a teljesítmény megőrzése érdekében a kiszolgálón legalább két gyorsítótár-meghajtó szükséges.

![Az animált ábrán két, legfeljebb hat kapacitású meghajtóra leképezett gyorsítótár-meghajtó látható, amíg az egyik gyorsítótár-meghajtó meghibásodik, ami miatt mind a hat meghajtó le lesz képezve a fennmaradó gyorsítótár-meghajtóra.](media/cache/Handling-Failure.gif)

Ezt követően a gyorsítótár-meghajtót ugyanúgy helyettesítheti, mint bármely más meghajtó cseréjét.

   >[!NOTE]
   > Előfordulhat, hogy le kell kapcsolnia a NVMe, hogy biztonságosan cserélje le a kiegészítő kártya (AIC) vagy az M. 2.

## <a name="relationship-to-other-caches"></a>Kapcsolat más gyorsítótárokhoz

A Windows szoftveresen definiált tárolási veremben számos más nem kapcsolódó gyorsítótár található. Ilyenek például a tárolóhelyek írási és olvasási gyorsítótára, valamint a memóriában tárolt olvasási gyorsítótár Fürt megosztott kötete (CSV).

Azure Stack HCI esetén a tárolóhelyek írási visszatartási gyorsítótára nem módosítható az alapértelmezett viselkedése alapján. Például a **New-Volume** parancsmagban található **-WriteCacheSize** paraméterek nem használhatók.

Dönthet úgy, hogy a CSV-gyorsítótárat használja, vagy sem. Alapértelmezés szerint Azure Stack HCI-ben van, de nem ütközik az ebben a témakörben ismertetett gyorsítótárral semmilyen módon. Bizonyos helyzetekben értékes teljesítménnyel kapcsolatos nyereségeket biztosíthat. További információ: [a CSV memóriában tárolt olvasási gyorsítótár használata Azure stack HCI használatával](../manage/use-csv-cache.md).

## <a name="manual-configuration"></a>Manuális konfigurálás

A legtöbb telepítés esetében nincs szükség manuális konfigurálásra. Ha szüksége van rá, tekintse meg a következő részeket.

Ha a telepítés után módosítania kell a gyorsítótár-eszköz modelljét, szerkessze a Állapotfigyelő szolgáltatás támogatási összetevőinek dokumentumát a [állapotfigyelő szolgáltatás áttekintése](/windows-server/failover-clustering/health-service-overview#supported-components-document)című témakörben leírtak szerint.

### <a name="specify-cache-drive-model"></a>Gyorsítótár-meghajtó típusának meghatározása

Olyan központi telepítések esetén, ahol az összes meghajtó ugyanabba a típusba tartozik, például a NVMe vagy az összes SSD üzemelő példányok esetében, nincs konfigurálva a gyorsítótár, mert a Windows nem tudja megkülönböztetni az olyan tulajdonságokat, mint az azonos típusú meghajtók esetében az írási kitartás.

Ha nagyobb teljesítményű meghajtókat kíván használni az azonos típusú alacsonyabb tartósságú meghajtók gyorsítótárazásához, megadhatja, hogy melyik meghajtó modellt használja az **enable-ClusterS2D** parancsmag **-CacheDeviceModel** paraméterrel. A rendszer a modell összes meghajtóját fogja használni a gyorsítótárazáshoz.

   >[!TIP]
   > A modell sztringjét pontosan úgy kell megfeleltetni, ahogy az a **Get-fizikai lemez**kimenetében megjelenik.

####  <a name="example"></a>Példa

Először szerezze be a fizikai lemezek listáját:

```PowerShell
Get-PhysicalDisk | Group Model -NoElement
```

Íme néhány példa a kimenetre:

```
Count Name
----- ----
    8 FABRIKAM NVME-1710
   16 CONTOSO NVME-1520
```

Ezután adja meg a következő parancsot a gyorsítótár-eszköz modell megadásával:

```PowerShell
Enable-ClusterS2D -CacheDeviceModel "FABRIKAM NVME-1710"
```

Megtekintheti, hogy a kívánt meghajtók gyorsítótárazása folyamatban van-e a PowerShellben a **Get-vérlemez** futtatásával, és annak ellenőrzése, hogy a **használati** tulajdonság a **"Journal"** kifejezést használja-e.

### <a name="manual-deployment-possibilities"></a>Manuális üzembe helyezési lehetőségek

A manuális konfigurálás a következő üzembe helyezési lehetőségeket teszi lehetővé:

![A diagram az üzembe helyezési lehetőségeket jeleníti meg, beleértve az N V M-t is a gyorsítótár és a kapacitás tekintetében, S D a gyorsítótárhoz és a kapacitáshoz, valamint az s s d-t a gyorsítótárhoz, és a kapacitáshoz a d és a H d d](media/cache/Exotic-Deployment-Possibilities.png)

### <a name="set-cache-behavior"></a>Gyorsítótár viselkedésének beállítása

A gyorsítótár alapértelmezett viselkedését felül lehet bírálni. Megadhatja például, hogy az olvasások gyorsítótárba helyezése egy teljes Flash-telepítés esetén is megtörténjen. A viselkedés módosítását csak akkor tudjuk megakadályozni, ha nem biztos benne, hogy az alapértelmezett érték nem felel meg a munkaterhelésnek.

A viselkedés felülbírálásához használja a **set-ClusterStorageSpacesDirect** parancsmagot és a **-CacheModeSSD** és a **-CacheModeHDD** paramétert. A **CacheModeSSD** paraméter beállítja a gyorsítótár viselkedését a szilárdtest-meghajtók gyorsítótárazásakor. A **CacheModeHDD** paraméter a gyorsítótár viselkedését állítja be a merevlemez-meghajtók gyorsítótárazásakor.

A **Get-ClusterStorageSpacesDirect** használatával ellenőrizheti, hogy a viselkedés be van-e állítva.

#### <a name="example"></a>Példa

Először kérje le a Közvetlen tárolóhelyek beállításait:

```PowerShell
Get-ClusterStorageSpacesDirect
```

Íme néhány példa a kimenetre:

```
CacheModeHDD : ReadWrite
CacheModeSSD : WriteOnly
```

Ezután tegye a következőket:

```PowerShell
Set-ClusterStorageSpacesDirect -CacheModeSSD ReadWrite

Get-ClusterS2D
```

Íme néhány példa a kimenetre:

```
CacheModeHDD : ReadWrite
CacheModeSSD : ReadWrite
```

## <a name="sizing-the-cache"></a>A gyorsítótár méretezése

A gyorsítótár méretének úgy kell lennie, hogy megfeleljen a munkakészletnek (az adatok aktívan olvashatók vagy írhatók egy adott időpontban) az alkalmazások és a munkaterhelések számára.

Ez különösen fontos a merevlemez-meghajtókkal történő hibrid üzembe helyezésekben. Ha az aktív munkakészlet meghaladja a gyorsítótár méretét, vagy ha az aktív munkakészlet túl gyorsan sodródik, akkor a gyorsítótárbeli leállások olvasási aránya megnő, és az írásokat még agresszíven el kell végezni, ami az általános teljesítményt is megsértheti.

A Windows beépített teljesítményfigyelő (PerfMon.exe) segédprogramjának használatával ellenőrizheti a gyorsítótárbeli kimaradás sebességét. Pontosabban összehasonlíthatja a **gyorsítótár-kihagyott olvasási sebesség/mp** elemet a **fürt Storage hibrid lemez** számlálójának a telepítés általános olvasási IOPS. Minden "hibrid lemez" egy kapacitási meghajtónak felel meg.

A 4 kapacitású meghajtókhoz tartozó 2 gyorsítótár-meghajtók például kiszolgálónként 4 "hibrid lemez" objektumot eredményeznek.

![Teljesítmény – figyelő](media/cache/PerfMon.png)

Nincs univerzális szabály, de ha túl sok olvasási érték hiányzik a gyorsítótárból, akkor elképzelhető, hogy a gyorsítótár kibontásához érdemes megfontolnia a gyorsítótár-meghajtók hozzáadását. A gyorsítótár-meghajtók vagy a kapacitás-meghajtók egymástól függetlenül is hozzáadhatók.

## <a name="next-steps"></a>Következő lépések

További tárolási ismeretekért lásd még:

- [Hibatűrés és a tárolás hatékonysága](fault-tolerance.md)
- [Fürt és készlet kvóruma](quorum.md)
