---
title: A Azure Stack tárolási infrastruktúrájának kezelése | Microsoft Docs
description: A Azure Stack tárolási infrastruktúrájának kezelése.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: article
ms.date: 08/27/2019
ms.author: mabrigg
ms.lastreviewed: 03/11/2019
ms.reviewer: jiahan
ms.openlocfilehash: c2a61e165ee64c4b0ee91fbe387973aaa5039f46
ms.sourcegitcommit: 9cb82df1eccb0486bcabec0bd674162d4820c00c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060219"
---
# <a name="manage-storage-infrastructure-for-azure-stack"></a>Azure Stack tárolási infrastruktúrájának kezelése

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ez a cikk a Azure Stack tárolási infrastruktúra erőforrásainak állapotát és működési állapotát ismerteti. Ezek az erőforrások a tárolóeszközöket és a köteteket tartalmazzák. Az ebben a témakörben található információk felbecsülhetetlen értékűek a különböző problémák elhárítására tett kísérlet során, például egy meghajtó nem adható hozzá a készlethez.

## <a name="understand-drives-and-volumes"></a>Meghajtók és kötetek ismertetése

### <a name="drives"></a>Meghajtók

A Windows Server szoftvereket használó Azure Stack a tárolási képességeket a Közvetlen tárolóhelyek (S2D) és a Windows Server feladatátvételi fürtszolgáltatás kombinációja határozza meg, hogy egy teljesítményű, méretezhető és rugalmas tárolási szolgáltatást biztosítson.

Azure Stack integrált rendszerpartnerek számos megoldási variációt kínálnak, beleértve a tárolási rugalmasság széles körét. Jelenleg három típusú meghajtó kombinációját választhatjuk ki: NVMe (nem felejtő memória-expressz), SATA/SAS SSD (SSD-meghajtó), HDD (merevlemez-meghajtó).

Közvetlen tárolóhelyek tartalmaz egy gyorsítótárat a tárolási teljesítmény maximalizálása érdekében. A Azure stack berendezésekben, amelyekben egy vagy több típusú meghajtó található, a közvetlen tárolóhelyek automatikusan a "leggyorsabb" (NVMe &gt; SSD &gt; HDD) típusú meghajtókat használja a gyorsítótárazáshoz. A fennmaradó meghajtók a kapacitáshoz használatosak. A meghajtók a következők egyike lehet: "All-Flash" vagy "hibrid".

![Azure Stack Storage-infrastruktúra](media/azure-stack-storage-infrastructure-overview/image1.png)

Az összes Flash-telepítés célja, hogy maximalizálja a tárolási teljesítményt, és ne tartalmazzon rotációs merevlemez-meghajtókat (HDD-ket).

![Azure Stack Storage-infrastruktúra](media/azure-stack-storage-infrastructure-overview/image2.png)

A hibrid üzembe helyezések célja a teljesítmény és a kapacitás elosztása, illetve a kapacitás maximalizálása, valamint a rotációs merevlemez-meghajtók (HDD) kiépítése.

A gyorsítótár viselkedését a rendszer a gyorsítótárban lévő meghajtók típusa (i) alapján automatikusan meghatározza. SSD-meghajtók gyorsítótárazásakor (például SSD-NVMe gyorsítótárazás esetén) csak az írások vannak gyorsítótárazva. Ez csökkenti a kapacitás-meghajtók kopását, csökkenti a kapacitás-meghajtók összesített forgalmát és az élettartamuk kiterjesztését. Addig is, mivel az olvasások nem befolyásolják jelentősen a Flash élettartamát, és mivel a SSD-meghajtók általánosan alacsony olvasási késést kínálnak, az olvasások nem lesznek gyorsítótárazva. A merevlemez-meghajtók (például a HDD-k SSD-gyorsítótárazása) gyorsítótárazásakor a rendszer az olvasási és az írási műveleteket is gyorsítótárazza, így a Flash-hez hasonló késleltetést (gyakran/~ 10x Better) is biztosít mindkettőhöz.

![Azure Stack Storage-infrastruktúra](media/azure-stack-storage-infrastructure-overview/image3.png)

A tárterület elérhető konfigurációjának megadásához Azure stack OEM-partnert (https://azure.microsoft.com/overview/azure-stack/partners/) a részletes specifikációt itt tekintheti meg).

> [!Note]  
> A Azure Stack készülék hibrid üzembe helyezhető, HDD-és SSD-(vagy NVMe-) meghajtókon is. A gyorsabb típusú meghajtók azonban gyorsítótár-meghajtóként használhatók, és az összes többi meghajtót készletként fogja használni. A bérlői adattárolók (Blobok, táblák, várólisták és lemezek) a kapacitás-meghajtókra kerülnek. A prémium szintű lemezek kiosztása vagy a Premium Storage-fiók típusának kiválasztása nem jelenti azt, hogy az objektumok garantáltan SSD-vagy NVMe-meghajtókon vannak lefoglalva, és nagyobb teljesítményt kapnak.

### <a name="volumes"></a>Kötetek

A *Storage szolgáltatás* a rendelkezésre álló tárolót külön kötetekre particionálja, amelyek a rendszer-és a bérlői adattároláshoz vannak lefoglalva. A kötetek egyesítik a tároló meghajtóit, hogy bevezessék a hibatűrést, a méretezhetőséget és a Közvetlen tárolóhelyek teljesítményének előnyeit.

![Azure Stack Storage-infrastruktúra](media/azure-stack-storage-infrastructure-overview/image4.png)

A Azure Stack Storage-készleten három típusú kötet jön létre:

-   Infrastruktúra: Azure Stack infrastruktúra-alapú virtuális gépek és alapszolgáltatások által használt gazdagép-fájlok.

-   VM Temp: a bérlői virtuális gépekhez csatolt ideiglenes lemezeket üzemelteti, és ezeket az adatlemezeket tárolja.

-   Object Store: gazdagép-bérlői adatkarbantartási Blobok, táblák, várólisták és virtuális gépek lemezei.

A többcsomópontos telepítésekben három infrastrukturális kötetet láthat, míg a virtuális gépek ideiglenes kötetei és az objektum-tároló kötetek száma megegyezik a Azure Stack üzemelő példányban található csomópontok számával:

-   Négy csomópontos üzemelő példányon négy azonos virtuálisgép-Temp kötet és négy egyenlő objektum-tároló kötet található.

-   Ha új csomópontot ad hozzá a fürthöz, a rendszer mindkét típushoz új kötetet hoz létre.

-   A kötetek száma változatlan marad, még akkor is, ha egy csomópont meghibásodik vagy törlődik.

-   Ha a Azure Stack fejlesztői készletet használja, akkor egyetlen kötet több megosztással is rendelkezik.

A Közvetlen tárolóhelyekban lévő kötetek rugalmasságot biztosítanak a hardveres problémák, például a meghajtó-vagy kiszolgálói hibák elleni védelemhez, valamint a folyamatos rendelkezésre állás engedélyezéséhez a kiszolgáló karbantartása, például a szoftverfrissítések terén. Azure Stack központi telepítés háromutas tükrözést használ az adatrugalmasság biztosításához. A bérlői adatmennyiség három példánya különböző kiszolgálókra van írva, ahol a gyorsítótárban találhatók:

![Azure Stack Storage-infrastruktúra](media/azure-stack-storage-infrastructure-overview/image5.png)

A tükrözések hibatűrést biztosítanak, ha az összes adattal több példányt tart. Az adatszalagos tárolás és a nem triviális, de teljesen igaz, hogy a tükrözést használó összes tárolt információ teljes egészében, többször is megírásra kerül. A rendszer minden egyes példányt különböző fizikai hardverre (különböző kiszolgálókon lévő különböző meghajtókra) ír, amelyeket feltételez, hogy egymástól függetlenül meghibásodik. A háromutas tükrözés képes biztonságosan elviselni legalább két hardveres problémát (meghajtó vagy kiszolgáló) egyszerre. Ha például egy kiszolgáló újraindításakor hirtelen egy másik meghajtó vagy kiszolgáló meghibásodik, az összes adat biztonságban és folyamatosan elérhető marad.

## <a name="volume-states"></a>Kötetek állapota

A következő PowerShell-parancsokkal megállapíthatja, hogy milyen állapotú kötetek találhatók:

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsVolume -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object VolumeLabel, HealthStatus, OperationalStatus, RepairStatus, Description, Action, TotalCapacityGB, RemainingCapacityGB
```

Íme egy példa egy leválasztott kötetre és egy csökkentett/hiányos kötetre:

| VolumeLabel | HealthStatus | OperationalStatus |
|-------------|--------------|------------------------|
| ObjStore_1 | Ismeretlen | Családi |
| ObjStore_2 | Figyelmeztetés | {Csökkentett teljesítményű, hiányos} |

Az alábbi szakaszban az állapot és a működési állapotok szerepelnek.

### <a name="volume-health-state-healthy"></a>Kötet állapota: Kifogástalan

| Műveleti állapot | Leírás |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| OK | A kötet kifogástalan. |
| Szuboptimális | Az adatlemezek nem kerülnek egyenletesen a meghajtókon.<br> <br>**Művelet** A tárolóban a meghajtó használatának optimalizálásához forduljon az ügyfélszolgálathoz. Mielőtt elkezdené, indítsa el a naplófájlok gyűjtésének folyamatát a https://aka.ms/azurestacklogfiles következő témakör útmutatása alapján:. Előfordulhat, hogy a sikertelen kapcsolódás visszaállítása után vissza kell állítania a biztonsági mentést. |


### <a name="volume-health-state-warning"></a>Kötet állapota: Figyelmeztetés

Ha a kötet figyelmeztetési állapotba kerül, az azt jelenti, hogy az adatai közül egy vagy több másolata nem érhető el, de Azure Stack továbbra is elolvashatja az adatai legalább egy példányát.

| Műveleti állapot | Leírás |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A szolgáltatásban | Azure Stack javítja a kötetet, például egy meghajtó hozzáadásával vagy eltávolításával. A javítás befejezésekor a kötetnek vissza kell térnie az OK állapotára.<br> <br>**Művelet** Várjon, amíg befejeződik a Azure Stack a kötet kijavítása, majd utána az állapot ellenõrzése. |
| Hiányos | A kötet rugalmassága csökken, mert egy vagy több meghajtó meghiúsult vagy hiányzik. A hiányzó meghajtók azonban az adatok naprakész másolatát tartalmazzák.<br> <br>**Művelet** Csatlakoztassa újra a hiányzó meghajtókat, cserélje le a meghibásodott meghajtókat, és kapcsolja online állapotba az összes olyan kiszolgálót, amely offline állapotú. |
| Csökkentett teljesítmény | A kötet rugalmassága csökken, mert egy vagy több meghajtó meghiúsult vagy hiányzik, és az adatai elavult másolattal rendelkeznek ezeken a meghajtókon.<br> <br>**Művelet** Csatlakoztassa újra a hiányzó meghajtókat, cserélje le a meghibásodott meghajtókat, és kapcsolja online állapotba az összes olyan kiszolgálót, amely offline állapotú. |

 

### <a name="volume-health-state-unhealthy"></a>Kötet állapota: Nem kifogástalan

Ha egy kötet nem kifogástalan állapotú, a köteten lévő egyes vagy az összes érték jelenleg nem érhető el.

| Műveleti állapot | Leírás |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nincs redundancia | A kötet elveszítette az adatvesztést, mert túl sok meghajtó meghiúsult.<br> <br>**Művelet** Forduljon az ügyfélszolgálathoz. Mielőtt elkezdené, indítsa el a naplófájlok gyűjtésének folyamatát a https://aka.ms/azurestacklogfiles következő témakör útmutatása alapján:. |


### <a name="volume-health-state-unknown"></a>Kötet állapota: Ismeretlen

A kötet ismeretlen állapotba is helyezhető, ha a virtuális lemez le lett választva.

| Műveleti állapot | Leírás |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Családi | Egy tárolóeszköz meghibásodása meghiúsult, ami miatt a kötet elérhetetlenné válhat. Előfordulhat, hogy egyes adatvesztések megszakadnak.<br> <br>**Művelet** <br>1. Az összes tárolóeszköz fizikai és hálózati kapcsolatának ellenőrzése.<br>2. Ha minden eszköz megfelelően van csatlakoztatva, forduljon az ügyfélszolgálathoz. Mielőtt elkezdené, indítsa el a naplófájlok gyűjtésének folyamatát a https://aka.ms/azurestacklogfiles következő témakör útmutatása alapján:. Előfordulhat, hogy a sikertelen kapcsolódás visszaállítása után vissza kell állítania a biztonsági mentést. |

## <a name="drive-states"></a>Meghajtók állapota

A meghajtók állapotának figyeléséhez használja a következő PowerShell-parancsokat:

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsDrive -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object StorageNode, PhysicalLocation, HealthStatus, OperationalStatus, Description, Action, Usage, CanPool, CannotPoolReason, SerialNumber, Model, MediaType, CapacityGB
```

A következő szakaszok ismertetik, hogy a meghajtó milyen állapotú lehet.

### <a name="drive-health-state-healthy"></a>Meghajtó állapota: Kifogástalan

| Műveleti állapot | Leírás |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| OK | A kötet kifogástalan. |
| A szolgáltatásban | A meghajtó néhány belső takarítási műveletet hajt végre. Ha a művelet befejeződött, a meghajtónak vissza kell térnie az OK állapotára. |

### <a name="drive-health-state-healthy"></a>Meghajtó állapota: Kifogástalan

A figyelmeztetési állapotban lévő meghajtó sikeresen képes olvasni és írni az adatírást, de probléma van.

| Műveleti állapot | Leírás |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Elveszett kommunikáció | A kapcsolat megszakadt a meghajtón.<br> <br>**Művelet** Minden kiszolgáló ismét online állapotba kerül. Ha ez nem oldja meg a javítást, csatlakoztassa újra a meghajtót. Ha ez megtartja a lépést, cserélje le a meghajtót a teljes rugalmasság biztosítására. |
| Prediktív hiba | A meghajtó meghibásodása várhatóan hamarosan megtörténik.<br> <br>**Művelet** A lehető leghamarabb cserélje le a meghajtót a teljes rugalmasság biztosításához. |
| IO-hiba | Ideiglenes Hiba történt a meghajtó elérésekor.<br> <br>**Művelet** Ha ez megtartja a lépést, cserélje le a meghajtót a teljes rugalmasság biztosítására. |
| Átmeneti hiba | Ideiglenes Hiba történt a meghajtóval. Ez általában azt jelenti, hogy a meghajtó nem válaszol, de azt is jelentheti, hogy a Közvetlen tárolóhelyek védelmi partíció helytelenül lett eltávolítva a meghajtóról. <br> <br>**Művelet** Ha ez megtartja a lépést, cserélje le a meghajtót a teljes rugalmasság biztosítására. |
| Rendellenes késés | A meghajtó néha nem válaszol, és a hiba jeleit mutatja.<br> <br>**Művelet** Ha ez megtartja a lépést, cserélje le a meghajtót a teljes rugalmasság biztosítására. |
| Eltávolítás a készletből | Azure Stack folyamatban van a meghajtó eltávolítása a tároló készletéből.<br> <br>**Művelet** Várjon, amíg a Azure Stack befejezi a meghajtó eltávolítását, majd utána az állapotot.<br>Ha az állapot továbbra is fennáll, forduljon az ügyfélszolgálathoz. Mielőtt elkezdené, indítsa el a naplófájlok gyűjtésének folyamatát a https://aka.ms/azurestacklogfiles következő témakör útmutatása alapján:. |
|  |  |
| Karbantartási mód indítása | Azure Stack a meghajtó karbantartási módba helyezése folyamatban van. Ez egy ideiglenes állapot – a meghajtó hamarosan a karbantartási mód állapotában kell lennie.<br> <br>**Művelet** Várjon, amíg a Azure Stack befejezi a folyamatot, majd az állapotot. |
| Karbantartási módban | A meghajtó karbantartási üzemmódban van, és leállítja az olvasásokat és az írásokat a meghajtóról. Ez általában azt jelenti, Azure Stack felügyeleti feladatok, például a PNU vagy a cserélhető üzemmód a meghajtót működteti. A rendszergazda azonban a karbantartási módba is helyezheti a meghajtót.<br> <br>**Művelet** Várjon, amíg a Azure Stack befejezi az adminisztrációs feladatot, majd utána az állapotot.<br>Ha az állapot továbbra is fennáll, forduljon az ügyfélszolgálathoz. Mielőtt elkezdené, indítsa el a naplófájlok gyűjtésének folyamatát a https://aka.ms/azurestacklogfiles következő témakör útmutatása alapján:. |
|  |  |
| Karbantartási mód leállítása | Azure Stack folyamatban van a meghajtó újbóli online állapotba hozása. Ez egy ideiglenes állapot – a meghajtónak hamarosan egy másik állapotban kell lennie – ideális esetben kifogástalan.<br> <br>**Művelet** Várjon, amíg a Azure Stack befejezi a folyamatot, majd az állapotot. |

 

### <a name="drive-health-state-unhealthy"></a>Meghajtó állapota: Nem kifogástalan

A sérült állapotú meghajtók jelenleg nem írhatók vagy nem érhetők el.

| Műveleti állapot | Leírás |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Megosztott | A meghajtó el lett választva a készlettől.<br> <br>**Művelet** Cserélje le a meghajtót egy új lemezre. Ha ezt a lemezt kell használnia, távolítsa el a lemezt a rendszerből, győződjön meg róla, hogy nincs hasznos adat a lemezen, törölje a lemezt, majd helyezze át a lemezt. |
| Nem használható | A fizikai lemez Karanténba kerül, mert a megoldás gyártója nem támogatja. Csak a megoldás számára jóváhagyott lemezek támogatottak, és a megfelelő lemezes belső vezérlőprogram használható.<br> <br>**Művelet** Cserélje le a meghajtót egy olyan lemezre, amely rendelkezik jóváhagyott gyártó és modell számmal a megoldáshoz. |
| Elavult metaadatok | A helyettesítő lemezt korábban használták, és tartalmazhatnak ismeretlen tárolási rendszerből származó adatokkal. A lemez karanténba van helyezve.        <br> <br>**Művelet** Cserélje le a meghajtót egy új lemezre. Ha ezt a lemezt kell használnia, távolítsa el a lemezt a rendszerből, győződjön meg róla, hogy nincs hasznos adat a lemezen, törölje a lemezt, majd helyezze át a lemezt. |
| Ismeretlen metaadatok | Ismeretlen metaadatok találhatók a meghajtón, ami általában azt jelenti, hogy a meghajtón található metaadatok egy másik készletből származnak.<br> <br>**Művelet** Cserélje le a meghajtót egy új lemezre. Ha ezt a lemezt kell használnia, távolítsa el a lemezt a rendszerből, győződjön meg róla, hogy nincs hasznos adat a lemezen, törölje a lemezt, majd helyezze át a lemezt. |
| Sikertelen adathordozó | A meghajtó nem sikerült, és a tárolóhelyek már nem használják.<br> <br>**Művelet** A lehető leghamarabb cserélje le a meghajtót a teljes rugalmasság biztosításához. |
| Hardverhiba az eszközön | Hardverhiba történt a meghajtón. <br> <br>**Művelet** A lehető leghamarabb cserélje le a meghajtót a teljes rugalmasság biztosításához. |
| Belső vezérlőprogram frissítése | Azure Stack frissíti a meghajtót a belső vezérlőprogram számára. Ez egy ideiglenes állapot, amely általában kevesebb mint egy percet tart, és amikor a készletben lévő többi meghajtó kezeli az összes olvasást és írást.<br> <br>**Művelet** Várjon, amíg a Azure Stack befejezi a frissítést, majd az állapotot. |
| Indítás | A meghajtó készen áll a műveletre. Ennek ideiglenes állapotba kell esnie, a meghajtónak eltérő működési állapotba kell térnie.<br> <br>**Művelet** Várjon, amíg a Azure Stack befejeződik a művelet, és utána az állapotot. |
 

## <a name="reasons-a-drive-cant-be-pooled"></a>A meghajtó nem egyesíthető.

Egyes meghajtók egyszerűen nem állnak készen a Azure Stack Storage-készletben való használatra. Megtudhatja, hogy a meghajtó miért nem jogosult a készletezésre, ha a meghajtó CannotPoolReason tulajdonságát keresi. Az alábbi táblázat egy kicsit részletesebben ismerteti az egyes okokat.

| Reason | Leírás |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A hardver nem megfelelő | A meghajtó nem szerepel a Állapotfigyelő szolgáltatás használatával megadott, jóváhagyott tárolási modellek listáján.<br> <br>**Művelet** Cserélje le a meghajtót egy új lemezre. |
| A belső vezérlőprogram nem megfelelő | A fizikai meghajtón található belső vezérlőprogram nem szerepel a jóváhagyott belső vezérlőprogram-változatok listájában a Állapotfigyelő szolgáltatás használatával.<br> <br>**Művelet** Cserélje le a meghajtót egy új lemezre. |
| Használatban fürt szerint | A meghajtót jelenleg egy feladatátvevő fürt használja.<br> <br>**Művelet** Cserélje le a meghajtót egy új lemezre. |
| Cserélhető adathordozó | A meghajtó cserélhető meghajtóként van osztályozva. <br> <br>**Művelet** Cserélje le a meghajtót egy új lemezre. |
| Nem kifogástalan | A meghajtó nem kifogástalan állapotban van, ezért előfordulhat, hogy le kell cserélni.<br> <br>**Művelet** Cserélje le a meghajtót egy új lemezre. |
| Nincs elegendő kapacitás | A meghajtón vannak partíciók a szabad területtel.<br> <br>**Művelet** Cserélje le a meghajtót egy új lemezre. Ha ezt a lemezt kell használnia, távolítsa el a lemezt a rendszerből, győződjön meg róla, hogy nincs hasznos adat a lemezen, törölje a lemezt, majd helyezze át a lemezt. |
| Ellenőrzés folyamatban | A Állapotfigyelő szolgáltatás ellenőrzi, hogy a meghajtó vagy a belső vezérlőprogram engedélyezett-e a használatra.<br> <br>**Művelet** Várjon, amíg a Azure Stack befejezi a folyamatot, majd az állapotot. |
| Az ellenőrzés nem sikerült | A Állapotfigyelő szolgáltatás nem tudta megtekinteni, hogy a meghajtó vagy a belső vezérlőprogram engedélyezett-e a használatra.<br> <br>**Művelet** Forduljon az ügyfélszolgálathoz. Mielőtt elkezdené, indítsa el a naplófájlok gyűjtésének folyamatát a https://aka.ms/azurestacklogfiles következő témakör útmutatása alapján:. |
| Offline | A meghajtó offline állapotban van. <br> <br>**Művelet** Forduljon az ügyfélszolgálathoz. Mielőtt elkezdené, indítsa el a naplófájlok gyűjtésének folyamatát a https://aka.ms/azurestacklogfiles következő témakör útmutatása alapján:. |

## <a name="next-step"></a>Következő lépés

[Tárolási kapacitás kezelése](azure-stack-manage-storage-shares.md) 
