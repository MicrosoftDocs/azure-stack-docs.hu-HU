---
title: A gyorsítótár megismerése Azure Stack HCI-ben
description: Az olvasási és írási gyorsítótárazás működése Közvetlen tárolóhelyek és Azure Stack HCI-ben.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/28/2020
ms.openlocfilehash: f1fc40a6475b8e51a063491cc120e2c4236cbeea
ms.sourcegitcommit: a77dea675af6500bdad529106f5782d86bec6a34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025666"
---
# <a name="understanding-the-cache-in-azure-stack-hci"></a>A gyorsítótár megismerése Azure Stack HCI-ben

>A következőkre vonatkozik: Windows Server 2019

[Közvetlen tárolóhelyek](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) tartalmaz egy beépített kiszolgálóoldali gyorsítótárat a tárolási teljesítmény maximalizálása érdekében. Ez egy nagy, állandó, valós idejű olvasási *és* írási gyorsítótár. A gyorsítótár automatikusan konfigurálva van, ha Közvetlen tárolóhelyek engedélyezve van. A legtöbb esetben nincs szükség manuális felügyeletre.
A gyorsítótár működése a meghajtók típusaitól függ.

A következő videó részletesen ismerteti, hogyan működik a gyorsítótárazás a Közvetlen tárolóhelyek, valamint más tervezési szempontokat is.

<strong>Tervezési szempontok Közvetlen tárolóhelyek</strong><br>(20 perc)<br>
<iframe src="https://channel9.msdn.com/Blogs/windowsserver/Design-Considerations-for-Storage-Spaces-Direct/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="drive-types-and-deployment-options"></a>Meghajtók típusai és üzembe helyezési lehetőségek

Közvetlen tárolóhelyek jelenleg háromféle tárolóeszközt működik:

<table>
    <tr style="border: 0;">
        <td style="padding: 10px; border: 0; width:70px">
            <img src="media/cache/NVMe-100px.png">
        </td>
        <td style="padding: 10px; border: 0;" valign="middle">
            NVMe (nem felejtő memória expressz)
        </td>
    </tr>
    <tr style="border: 0;">
        <td style="padding: 10px; border: 0; width:70px">
            <img src="media/cache/SSD-100px.png">
        </td>
        <td style="padding: 10px; border: 0;" valign="middle">
            SATA/SAS SSD (SSD-meghajtó)
        </td>
    </tr>
    <tr style="border: 0;">
        <td style="padding: 10px; border: 0; width:70px">
            <img src="media/cache/HDD-100px.png">
        </td>
        <td style="padding: 10px; border: 0;" valign="middle">
            HDD (merevlemez-meghajtó)
        </td>
    </tr>
</table>

Ezek hat módon kombinálhatók egymással, amelyek két kategóriába sorolhatók: "All-Flash" és "Hybrid".

### <a name="all-flash-deployment-possibilities"></a>Összes-Flash üzembe helyezési lehetőség

Az összes Flash-telepítés célja, hogy maximalizálja a tárolási teljesítményt, és ne tartalmazzon rotációs merevlemez-meghajtókat (HDD-ket).

![Összes Flash – üzembe helyezés – lehetőségek](media/cache/All-Flash-Deployment-Possibilities.png)

### <a name="hybrid-deployment-possibilities"></a>Hibrid üzembe helyezési lehetőségek

A hibrid üzembe helyezések célja a teljesítmény és a kapacitás elosztása, illetve a kapacitás maximalizálása, valamint a rotációs merevlemez-meghajtók (HDD) kiépítése.

![Hibrid – üzembe helyezés – lehetőségek](media/cache/Hybrid-Deployment-Possibilities.png)

## <a name="cache-drives-are-selected-automatically"></a>A gyorsítótár-meghajtók automatikusan ki vannak választva

A több típusú meghajtóval üzemelő példányok esetében a Közvetlen tárolóhelyek automatikusan a "leggyorsabb" típusú meghajtókat használja a gyorsítótárazáshoz. A fennmaradó meghajtók a kapacitáshoz használatosak.

A "leggyorsabb" típus a következő hierarchia alapján van meghatározva.

![Meghajtó típusa – hierarchia](media/cache/Drive-Type-Hierarchy.png)

Ha például NVMe és SSD-ket tartalmaz, a NVMe gyorsítótárazza az SSD-ket.

Ha SSD-k és HDD-k is vannak, akkor az SSD-k gyorsítótárazva lesznek a HDD-k számára.

   >[!NOTE]
   > A gyorsítótár-meghajtók nem járulnak hozzá a használható tárolási kapacitáshoz. A gyorsítótárban tárolt összes adatmennyiség máshol is tárolva lesz, vagy ha ez a fázis. Ez azt jelenti, hogy az üzemelő példány teljes nyers tárolókapacitása csak a kapacitás-meghajtók összege.

Ha az összes meghajtó azonos típusú, a rendszer nem konfigurálja automatikusan a gyorsítótárat. Lehetősége van arra, hogy manuálisan konfigurálja a nagyobb teljesítményű meghajtókat az azonos típusú alacsonyabb tartósságú meghajtók gyorsítótárazásához – lásd a [manuális konfigurációs](#manual-configuration) szakaszt, amelyből megtudhatja, hogyan.

   >[!TIP]
   > A NVMe vagy az összes SSD üzemelő példányban – különösen a nagyon kis méretekben – a gyorsítótárban lévő "elköltött" meghajtók esetében nem lehet hasznos a tárhely hatékonyságának javítása.

## <a name="cache-behavior-is-set-automatically"></a>A gyorsítótár viselkedése automatikusan be van állítva

A gyorsítótár viselkedését a rendszer a gyorsítótárban lévő meghajtók típusa (i) alapján automatikusan meghatározza. SSD-meghajtók gyorsítótárazásakor (például SSD-NVMe gyorsítótárazás esetén) csak az írások vannak gyorsítótárazva. A merevlemez-meghajtók gyorsítótárazása (például SSD-meghajtók gyorsítótárazása) esetén az olvasás és az írás is gyorsítótárazva van.

![Gyorsítótár – olvasási és írási viselkedés](media/cache/Cache-Read-Write-Behavior.png)

### <a name="write-only-caching-for-all-flash-deployments"></a>Írásvédett gyorsítótárazás az összes Flash-telepítéshez

SSD-meghajtók (NVMe vagy SSD-k) gyorsítótárazásakor a rendszer csak az írásokat gyorsítótárazza. Ez csökkenti a kapacitás-meghajtók kopását, mivel számos írás és újraírás egyesíthető a gyorsítótárban, majd csak szükség szerint elvégezhető, így csökkentve a kapacitás-meghajtók összesített forgalmát és az élettartamuk kiterjesztését. Ezért javasoljuk, hogy a gyorsítótárhoz [nagyobb teljesítményű, írásra optimalizált](http://whatis.techtarget.com/definition/DWPD-device-drive-writes-per-day) meghajtók legyenek kiválasztva. Előfordulhat, hogy a kapacitási meghajtók alacsonyabb írási állóképességtel rendelkeznek.

Mivel az olvasások nem befolyásolják jelentősen a Flash élettartamát, és mivel a SSD-meghajtók általánosan alacsony olvasási késést kínálnak, az olvasások nem lesznek gyorsítótárazva: közvetlenül a kapacitás-meghajtókról szolgálnak (kivéve, ha az adatokat a közelmúltban írták, és nem még nem előkészített). Ez lehetővé teszi, hogy a gyorsítótár kizárólag írásra legyen kijelölve, maximalizálva a hatékonyságot.

Ez írási tulajdonságokat (például írási késést) eredményez, amelyet a gyorsítótár-meghajtók diktálnak, míg az olvasási jellemzőket a kapacitási meghajtók diktálják. Mindkettő konzisztens, kiszámítható és egységes.

### <a name="readwrite-caching-for-hybrid-deployments"></a>Írási/olvasási gyorsítótárazás a hibrid üzembe helyezésekhez

A merevlemez-meghajtók (HDD-k) gyorsítótárazásakor a rendszer a beolvasást *és* az írást is gyorsítótárazza, így a Flash-hez hasonló késleltetést (gyakran ~ 10x jobbat) is biztosít mindkettőhöz. Az olvasási gyorsítótár a közelmúltban és gyakran olvassa be az adatokat a gyors elérés érdekében, és a HDD-re való véletlenszerű adatforgalom minimalizálására. (A keresési és a rotációs késések miatt jelentős a HDD-vel való véletlenszerű hozzáférés esetén felmerülő késés és idő.) Az írások a gyorsítótárba kerülnek, így az írásokat és a korábbikat is összevontuk

Közvetlen tárolóhelyek olyan algoritmust valósít meg, amely egy véletlenszerű írást végez, mielőtt a rendszer elkészíti őket, egy i/o-mintát a lemezre, amely a számítási feladatból (például a virtuális gépekből) származó tényleges IO-t (pl. Virtual machines) véletlenszerűen hozza létre. Ez maximalizálja a IOPS és az átviteli sebességet a HDD-k számára.

### <a name="caching-in-deployments-with-drives-of-all-three-types"></a>A központi telepítések gyorsítótárazása mindhárom típusú meghajtóval

Ha mindhárom típusú meghajtó megtalálható, a NVMe-meghajtók gyorsítótárazást biztosítanak az SSD-k és a HDD-k számára egyaránt. A viselkedés a fent leírtak szerint történik: csak a (z) rendszerű meghajtók gyorsítótárazva vannak az SSD-k számára A HDD-k gyorsítótárazásának terhét egyenletesen osztják el a gyorsítótár-meghajtók között.

## <a name="summary"></a>Összegzés

Ez a táblázat összefoglalja, hogy mely meghajtók használatosak a kapacitáshoz, és hogy a gyorsítótárazás milyen viselkedést biztosít az egyes telepítési lehetőségekhez.

| Környezet     | Gyorsítótár-meghajtók                        | Kapacitás-meghajtók | Gyorsítótár viselkedése (alapértelmezett)  |
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

Mivel a Közvetlen tárolóhelyek rugalmassága legalább kiszolgálói szintű (vagyis az adatmásolatok mindig különböző kiszolgálókra vannak írva; kiszolgálónként legfeljebb egy másolat), a gyorsítótárban lévő adatok ugyanolyan rugalmassággal rendelkeznek, mint a gyorsítótárban lévő adatok.

![Gyorsítótár – kiszolgálóoldali architektúra](media/cache/Cache-Server-Side-Architecture.png)

Például, ha háromutas tükrözést használ, az összes adattal kapcsolatos három másolat különböző kiszolgálókra kerül, ahol a gyorsítótárba kerülnek. Függetlenül attól, hogy későbbiek-e vagy sem, három másolat mindig létezik.

## <a name="drive-bindings-are-dynamic"></a>A meghajtó kötései dinamikusak

A gyorsítótár és a kapacitás-meghajtók közötti kötés bármilyen arányban lehet, 1:1 és 1:12 között. Dinamikusan módosítja a meghajtók hozzáadását vagy eltávolítását, például a hibák felskálázásakor vagy a meghibásodások után. Ez azt jelenti, hogy a gyorsítótár-meghajtók vagy a kapacitás-meghajtók egymástól függetlenül vehetők fel, ha szeretné.

![Dinamikus kötés](media/cache/Dynamic-Binding.gif)

Azt javasoljuk, hogy a kapacitások számának többszöröse legyen a gyorsítótár-meghajtók száma a szimmetria szempontjából. Ha például 4 gyorsítótár-meghajtóval rendelkezik, még nagyobb teljesítményt fog tapasztalni 8 kapacitású meghajtóval (1:2 arány), mint 7 vagy 9.

## <a name="handling-cache-drive-failures"></a>Gyorsítótár-meghajtók hibáinak kezelése

Ha egy gyorsítótár-meghajtó meghibásodik, a rendszer az összes olyan írást elveszíti, amely még nem lett elküldve *a helyi kiszolgálónak*, azaz csak a többi példányon (más kiszolgálókon) található. Ugyanúgy, mint bármely más meghajtó meghibásodása után, a tárolóhelyek és az automatikus helyreállítás a túlélő másolatokkal folytatott tanácsadással történik.

Egy rövid ideig az elveszett gyorsítótár-meghajtóhoz kötött kapacitási meghajtók nem Kifogástalan állapotba kerülnek. Ha a gyorsítótár-újrakötés bekövetkezett (automatikus), és az adatjavítás befejeződött (automatikus), a rendszer Kifogástalan állapotba helyezi.

Ennek a forgatókönyvnek az az oka, hogy a teljesítmény megőrzése érdekében a kiszolgálón legalább két gyorsítótár-meghajtó szükséges.

![Kezelési hiba](media/cache/Handling-Failure.gif)

Ezt követően a gyorsítótár-meghajtót ugyanúgy helyettesítheti, mint bármely más meghajtó cseréjét.

   >[!NOTE]
   > Előfordulhat, hogy le kell kapcsolnia a NVMe, hogy biztonságosan cserélje le a kiegészítő kártya (AIC) vagy az M. 2.

## <a name="relationship-to-other-caches"></a>Kapcsolat más gyorsítótárokhoz

A Windows szoftveresen definiált tárolási veremben számos más nem kapcsolódó gyorsítótár található. Ilyenek például a tárolóhelyek írási és olvasási gyorsítótára, valamint a memóriában tárolt olvasási gyorsítótár Fürt megosztott kötete (CSV).

A Közvetlen tárolóhelyek a tárolóhelyek írási visszatartási gyorsítótára nem módosítható az alapértelmezett viselkedése alapján. Például a **New-Volume** parancsmagban található **-WriteCacheSize** paraméterek nem használhatók.

Dönthet úgy, hogy a CSV-gyorsítótárat használja, vagy sem. Alapértelmezés szerint ki van kapcsolva Közvetlen tárolóhelyekban, de nem ütközik az ebben a témakörben ismertetett új gyorsítótárral bármilyen módon. Bizonyos helyzetekben értékes teljesítménnyel kapcsolatos nyereségeket biztosíthat. További információ: [a CSV-gyorsítótár engedélyezése](/windows-server/failover-clustering/failover-cluster-csvs#enable-the-csv-cache-for-read-intensive-workloads-optional).

## <a name="manual-configuration"></a>Manuális konfigurálás

A legtöbb telepítés esetében nincs szükség manuális konfigurálásra. Ha szüksége van rá, tekintse meg a következő részeket. 

Ha a telepítés után módosítania kell a gyorsítótár-eszköz modelljét, szerkessze a Állapotfigyelő szolgáltatás támogatási összetevőinek dokumentumát a [állapotfigyelő szolgáltatás áttekintése](/windows-server/failover-clustering/health-service-overview#supported-components-document)című témakörben leírtak szerint.

### <a name="specify-cache-drive-model"></a>Gyorsítótár-meghajtó típusának meghatározása

Olyan központi telepítések esetén, ahol az összes meghajtó ugyanabba a típusba tartozik, például a NVMe vagy az összes SSD üzemelő példányok esetében, nincs konfigurálva a gyorsítótár, mert a Windows nem tudja megkülönböztetni az olyan tulajdonságokat, mint az azonos típusú meghajtók esetében az írási kitartás.

Ha nagyobb teljesítményű meghajtókat kíván használni az azonos típusú alacsonyabb tartósságú meghajtók gyorsítótárazásához, megadhatja, hogy melyik meghajtó modellt használja az **enable-ClusterS2D** parancsmag **-CacheDeviceModel** paraméterrel. A Közvetlen tárolóhelyek engedélyezése után a rendszer a modell összes meghajtóját fogja használni a gyorsítótárazáshoz.

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

![Egzotikus – üzembe helyezés – lehetőségek](media/cache/Exotic-Deployment-Possibilities.png)

### <a name="set-cache-behavior"></a>Gyorsítótár viselkedésének beállítása

A gyorsítótár alapértelmezett viselkedését felül lehet bírálni. Megadhatja például, hogy az olvasások gyorsítótárba helyezése egy teljes Flash-telepítés esetén is megtörténjen. A viselkedés módosítását csak akkor tudjuk megakadályozni, ha nem biztos benne, hogy az alapértelmezett érték nem felel meg a munkaterhelésnek.

A viselkedés felülbírálásához használja a **set-ClusterStorageSpacesDirect** parancsmagot és a **-CacheModeSSD** és a **-CacheModeHDD** paramétert. A **CacheModeSSD** paraméter beállítja a gyorsítótár viselkedését a szilárdtest-meghajtók gyorsítótárazásakor. A **CacheModeHDD** paraméter a gyorsítótár viselkedését állítja be a merevlemez-meghajtók gyorsítótárazásakor. Ezt a Közvetlen tárolóhelyek engedélyezése után bármikor megteheti.

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

A Windows beépített teljesítményfigyelő (PerfMon. exe) segédprogramjának használatával ellenőrizheti a gyorsítótárbeli elmaradás sebességét. Pontosabban összehasonlíthatja a **gyorsítótár-kihagyott olvasási sebesség/mp** elemet a **fürt Storage hibrid lemez** számlálójának a telepítés általános olvasási IOPS. Minden "hibrid lemez" egy kapacitási meghajtónak felel meg.

A 4 kapacitású meghajtókhoz tartozó 2 gyorsítótár-meghajtók például kiszolgálónként 4 "hibrid lemez" objektumot eredményeznek.

![Teljesítmény – figyelő](media/cache/PerfMon.png)

Nincs univerzális szabály, de ha túl sok olvasási érték hiányzik a gyorsítótárból, akkor elképzelhető, hogy a gyorsítótár kibontásához érdemes megfontolnia a gyorsítótár-meghajtók hozzáadását. A gyorsítótár-meghajtók vagy a kapacitás-meghajtók egymástól függetlenül is hozzáadhatók.

## <a name="next-steps"></a>Következő lépések

További tárolási ismeretekért lásd még:

- [Hibatűrés és a tárolás hatékonysága](fault-tolerance.md)
- [Fürt és készlet kvóruma](quorum.md)
