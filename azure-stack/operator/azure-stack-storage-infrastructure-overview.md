---
title: Tároló-infrastruktúra kezelése az Azure Stackhez |} A Microsoft Docs
description: Tároló-infrastruktúra kezelése az Azure Stackhez.
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
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 03/11/2019
ms.reviewer: jiahan
ms.openlocfilehash: c7ae7f0c8fa510b0f2b55e458266065544e1bd5e
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828213"
---
# <a name="manage-storage-infrastructure-for-azure-stack"></a>Azure stack-beli tároló-infrastruktúra kezelése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ez a cikk bemutatja az egészségügyi és a tárolási infrastruktúra-erőforrások Azure Stack működési állapotát. Ilyen erőforrások többek között a tárolóeszközöket és köteteket. Ebben a témakörben található információk felbecsülhetetlen értékű lehet, ha különböző problémák megoldásához, mint például a meghajtó nem adható hozzá a készlethez.

## <a name="understand-drives-and-volumes"></a>Megismerheti a meghajtók és kötetek

### <a name="drives"></a>Meghajtók

Működteti, a Windows Servert, az Azure Stack a közvetlen tárolóhelyek (S2D) és a Windows Server feladatátvételi fürtszolgáltatási biztosít a nagy teljesítményű, méretezhető és rugalmas tárolási szolgáltatás kombinációjával tárolási képességeinek határozza meg.

Az Azure Stackkel integrált rendszer partnerei számos megoldás változata, beleértve számos különféle tárolási rugalmasságot kínálnak. Háromféle meghajtó kombinációja jelenleg választhat: NVMe (Express nem felejtő memória), SSD (SSD-meghajtóra), SATA/SAS HDD (merevlemez-meghajtó).

A közvetlen tárolóhelyek szolgáltatást a tárolási teljesítmény maximalizálása érdekében gyorsítótárat. Az Azure Stack-készülék egy vagy több típusú meghajtókon, a közvetlen tárolóhelyek automatikusan használni az összes meghajtó a "leggyorsabb" (NVMe &gt; SSD &gt; HDD) írja be a gyorsítótárazáshoz. A fennmaradó meghajtók szolgálnak a tárolókapacitás. A meghajtók csoportosítható "teljes mértékben flash" vagy "hibrid" központi telepítés:

![Az Azure Stack tárolási infrastruktúra](media/azure-stack-storage-infrastructure-overview/image1.png)

Teljes mértékben flash maximalizálhatja a tárolási teljesítmény központi telepítések célja, és nem forgó merevlemezes (HDD) meghajtók tartalmazzák.

![Az Azure Stack tárolási infrastruktúra](media/azure-stack-storage-infrastructure-overview/image2.png)

Hibrid telepítések célja a teljesítmény és kapacitás kiegyenlítése, illetve kapacitásának maximalizálása érdekében, és forgó merevlemezes (HDD) meghajtók tartalmazzák.

A gyorsítótár viselkedését a meghajtó, amely alatt a gyorsítótárban típusainak automatikusan alapján határozza meg. Ha a gyorsítótárazás a tartós állapotú meghajtókhoz (például a gyorsítótárazás NVMe SSD-kkel), csak írási gyorsítótárazza a rendszer. Ez csökkenti a kapacitást meghajtókon, az összesített adatforgalom csökkentése a kapacitás-meghajtók és kiterjesztése élettartamuk motorkopási. Addig is mert olvasási jelentősen nem befolyásolják a gyűjteményszintű flash-, és mivel SSD-meghajtókra univerzálisan ajánlat alacsony olvasási késés, olvasási nincsenek gyorsítótárazva. Ha a merevlemez-meghajtók (például HDD-gyorsítótárazás SSD), mind az olvasási és írási műveletek lettek gyorsítótárazva, a flash-hez hasonló késést tudjon biztosítani a gyorsítótárazás (gyakran ~ 10 x nagyobb) esetében is.

![Az Azure Stack tárolási infrastruktúra](media/azure-stack-storage-infrastructure-overview/image3.png)

A storage rendelkezésre álló konfigurációt, az Azure Stack OEM-partner ellenőrizheti (https://azure.microsoft.com/overview/azure-stack/partners/) a részletes leírásban.

> [!Note]  
> A hibrid telepítéssel is HDD és SSD (vagy NVMe) meghajtók Azure Stack-berendezés lehet nyújtani. De gyorsabb típusú meghajtókat használni kívánt gyorsítótár meghajtóként, és a kapacitás meghajtóként, mint a készlet összes többi meghajtón használni kívánt. A bérlő adatainak (blobok, táblák, üzenetsorok és lemezek) kerül a kapacitás meghajtókon. Ezért prémium szintű lemezek kiépítése, illetve a premium storage-fiók típusa nem jelenti azt, hogy az objektumok kijelölése garantáltan kiosztását az SSD vagy NVMe-meghajtók és jobb teljesítményt nyújt nyereség.

### <a name="volumes"></a>Kötetek

A *társzolgáltatás* particionálja a rendelkezésre álló tár be különálló system és a bérlői adatok tárolásához kiosztott köteteket. Kötetek össze a meghajtókat a tárolókészletben bevezetni a hibatűrést, a méretezhetőség és a közvetlen tárolóhelyek teljesítmény előnyeit.

![Az Azure Stack tárolási infrastruktúra](media/azure-stack-storage-infrastructure-overview/image4.png)

Az Azure Stack-tárolókészlethez létrehozott kötetek három típusa van:

-   Infrastruktúra: gazdagép fájlokat az Azure Stack-infrastruktúra virtuális gépeinek és alapvető szolgáltatások által használt.

-   VM Temp: gazdagép az ideiglenes lemezek kapcsolódó bérlő virtuális gépek, és az adatokat tárolja ezeket a lemezeket.

-   Objektum Store: a blobok, táblák, üzenetsorok és Virtuálisgép-lemezek karbantartási gazdagép bérlői adatok.

A több csomópontos központi telepítés három infrastruktúra kötetek, miközben a virtuális gép Temp és objektum Store kötetek száma egyenlő a csomópontok számát az Azure Stack üzemelő jelennének meg:

-   Egy négy csomópontos üzemelő példányon nincsenek négy azonos virtuális gép Temp és négy egyenlő objektum Store kötetek.

-   Ha új csomópontot ad hozzá a fürtöt, lenne egy új kötetet az mindkét fajta lehet létrehozni.

-   A kötetek száma továbbra is az azonos még akkor is, ha egy csomópont hibásan működik, vagy a rendszer eltávolítja.

-   Az Azure Stack fejlesztői készletének használja, ha nincs több megosztások egy kötetet.

A közvetlen tárolóhelyek kötetek biztosítható a rugalmasság meghajtó vagy kiszolgáló hibák, például a hardverhibák ellen, és engedélyezze a folyamatos rendelkezésre állást kiszolgáló karbantartás, például a szoftverfrissítések során. Az Azure Stack üzembe helyezése az háromirányú tükrözés segítségével adatrugalmasság érdekében. Bérlői adatok három másolatát különböző kiszolgálókra, ahol a gyorsítótárban látogatnak írt:

![Az Azure Stack tárolási infrastruktúra](media/azure-stack-storage-infrastructure-overview/image5.png)

Tükrözés biztosítja a hibatűrést az adatok több példányát tárolja. Adott adatok csíkozott és elhelyezett nem triviális (lásd az ebben a blogbejegyzésben további), de ez egyáltalán tegyük fel, hogy a tükrözés használata tárolt összes adatot írt, ebben az esetben igaz értéket többször. Minden példány különböző fizikai írt hardver (különböző kiszolgálókon található különböző meghajtókon), amely a rendszer feltételezi, hogy egymástól függetlenül sikertelen. Háromutas tükrözés biztonságosan tűri (meghajtó vagy kiszolgáló) legalább két hardverproblémák egyszerre. Például ha egy kiszolgáló, amikor hirtelen, még újraindítás egy másik meghajtó vagy kiszolgáló sikertelen lesz, minden adatok biztonságos, és folyamatosan elérhető marad.

## <a name="volume-states"></a>Kötet állapotok

Ismerje meg, hogy állapot kötetek Mik a, használja a következő PowerShell-parancsokat:

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsVolume -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object VolumeLabel, HealthStatus, OperationalStatus, RepairStatus, Description, Action, TotalCapacityGB, RemainingCapacityGB
```

Íme egy példa kimenet megjelenítése a leválasztott kötet és a egy állapota nem megfelelő vagy nem teljes kötet:

| VolumeLabel | HealthStatus | OperationalStatus |
|-------------|--------------|------------------------|
| ObjStore_1 | Ismeretlen | Leválasztott |
| ObjStore_2 | Figyelmeztetés | {Degraded, Incomplete} |

Az alábbi szakaszok az egészségügyi és működési állapotok listája.

### <a name="volume-health-state-healthy"></a>Kötet állapota: Kifogástalan

| működési állapot | Leírás |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| OK | A kötet állapota kifogástalan. |
| Optimálisnál rosszabb | Adatok egyenletesen nem írt meghajtókon.<br> <br>**Művelet:** Forduljon a támogatási optimalizálja a tárolókészlet meghajtóinak használatát. Mielőtt elkezdené, a fájl naplógyűjtési folyamat a útmutatásának https://aka.ms/azurestacklogfiles. Előfordulhat, hogy biztonsági másolatból történő visszaállítását a sikertelen kapcsolódás visszaállítása után. |


### <a name="volume-health-state-warning"></a>Kötet állapota: Figyelmeztetés

Amikor kötet figyelmeztetési állapotban van, az azt jelenti, hogy az adatokat egy vagy több példányban nem érhető el, de az Azure Stack keresztül továbbra is olvashatók az adatok legalább egy példányát.

| működési állapot | Leírás |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A szolgáltatásban | Az Azure Stack javítja a kötetet, például hozzáadása vagy eltávolítása a meghajtó után. A javítás befejeződése után térjen vissza a kötet állapota OK.<br> <br>**Művelet:** Várjon, amíg befejeződik a kötet javítása az Azure Stack, és ezt követően ellenőrizze az állapotát. |
| Hiányos | Az ezzel járó rugalmasságot, a kötet csökken, mert egy vagy több meghajtó nem sikerült, vagy hiányzik. Azonban a hiányzó meghajtókat tartalmaz az adatok legfrissebb másolatát.<br> <br>**Művelet:** Csatlakoztassa újra a hiányzó meghajtóknak, cserélje le a sikertelen meghajtóknak és online állapotba helyezés egyetlen olyan kiszolgálót sem, amely a kapcsolat nélküli üzemmódban van. |
| Csökkentett teljesítmény | Az ezzel járó rugalmasságot, a kötet csökken, mert egy vagy több meghajtó nem sikerült, vagy hiányzik, és az adatokat, ezek a meghajtók elavult példányban vannak.<br> <br>**Művelet:** Csatlakoztassa újra a hiányzó meghajtóknak, cserélje le a sikertelen meghajtóknak és online állapotba helyezés egyetlen olyan kiszolgálót sem, amely a kapcsolat nélküli üzemmódban van. |

 

### <a name="volume-health-state-unhealthy"></a>Kötet állapota: Nem kifogástalan

Amikor egy kötet állapota nem kifogástalan állapotban van, vagy azok egy részét az adatokat a köteten jelenleg nem érhető el.

| működési állapot | Leírás |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Redundancia | A mennyiségi adatok megszakadt, mert túl sok meghajtók nem sikerült.<br> <br>**Művelet:** Forduljon a támogatási. Mielőtt elkezdené, a fájl naplógyűjtési folyamat a útmutatásának https://aka.ms/azurestacklogfiles. |


### <a name="volume-health-state-unknown"></a>Kötet állapota: Ismeretlen

Ha a virtuális lemez le lesznek választva a kötet is lehet állapota ismeretlen.

| működési állapot | Leírás |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Leválasztott | Egy tárolóeszköz meghibásodása történt vezethet a kötet nem lesz elérhető. Lehet, hogy néhány adat elveszett.<br> <br>**Művelet:** <br>1. Ellenőrizze a fizikai és a hálózati kapcsolat minden tárolóeszköz.<br>2. Ha minden eszköz megfelelően van csatlakoztatva, kérjük forduljon az ügyfélszolgálathoz. Mielőtt elkezdené, a fájl naplógyűjtési folyamat a útmutatásának https://aka.ms/azurestacklogfiles. Előfordulhat, hogy biztonsági másolatból történő visszaállítását a sikertelen kapcsolódás visszaállítása után. |

## <a name="drive-states"></a>Meghajtó állapota

A következő PowerShell-parancsok használatával meghajtók állapotának figyelése:

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsDrive -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object StorageNode, PhysicalLocation, HealthStatus, OperationalStatus, Description, Action, Usage, CanPool, CannotPoolReason, SerialNumber, Model, MediaType, CapacityGB
```

A következő szakaszok ismertetik az állapotokat egy meghajtót is lehet.

### <a name="drive-health-state-healthy"></a>Meghajtó állapota: Kifogástalan

| működési állapot | Leírás |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| OK | A kötet állapota kifogástalan. |
| A szolgáltatásban | A meghajtó néhány belső karbantartás műveleteket hajt végre. A művelet befejeződése után térjen vissza a meghajtó állapota OK. |

### <a name="drive-health-state-healthy"></a>Meghajtó állapota: Kifogástalan

A figyelmeztetési állapot használatával meghajtót olvassa el, és sikeresen ír adatokat, de egy hibát tartalmaz.

| működési állapot | Leírás |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Megszakadt a kommunikáció | Kapcsolat megszakadt a meghajtóban.<br> <br>**Művelet:** Minden kiszolgáló ismét online állapotba. Ha ez nem oldja, csatlakoztassa újra a meghajtót. Ez tartja történik, cserélje le a teljes rugalmasságának biztosításához a meghajtó. |
| Hiba várható | A meghajtó hiba várhatóan hamarosan fordulhat elő.<br> <br>**Művelet:** Cserélje le a meghajtó, amint lehetséges teljes rugalmasságának biztosításához. |
| IO-hiba | A meghajtó eléréséhez egy átmeneti hiba történt.<br> <br>**Művelet:** Ez tartja történik, cserélje le a teljes rugalmasságának biztosításához a meghajtó. |
| Átmeneti hiba | A meghajtó ideiglenes hiba történt. Ez általában azt jelenti, hogy a meghajtó nem volt, de azt is jelentheti, hogy a közvetlen tárolóhelyek védelmi partíció nem megfelelően el lett távolítva a meghajtó. <br> <br>**Művelet:** Ez tartja történik, cserélje le a teljes rugalmasságának biztosításához a meghajtó. |
| Rendellenes késés | A meghajtó néha nem válaszol, és jelenik meg a hiba jelentkezik.<br> <br>**Művelet:** Ez tartja történik, cserélje le a teljes rugalmasságának biztosításához a meghajtó. |
| Eltávolítás a készletből | Az Azure Stack a tárolókészletből a meghajtó eltávolítása folyamatban van.<br> <br>**Művelet:** Várjon, amíg a meghajtó eltávolításának befejezéséhez az Azure Stack, és ezt követően ellenőrizze az állapotát.<br>Ha az állapot marad, forduljon az ügyfélszolgálatához. Mielőtt elkezdené, a fájl naplógyűjtési folyamat a útmutatásának https://aka.ms/azurestacklogfiles. |
|  |  |
| Kezdő karbantartási mód | Az Azure Stack folyamatban a meghajtó elhelyezése karbantartási üzemmódban van. Ez csak átmeneti állapot – a meghajtó hamarosan a karbantartási mód állapotban kell lennie.<br> <br>**Művelet:** Várjon, amíg a folyamat befejezéséhez az Azure Stack, és ezt követően ellenőrizze az állapotát. |
| A karbantartási mód | A meghajtó karbantartási módban van, Leállítás, olvasási, és írja a meghajtóról. Ez általában azt jelenti, hogy az Azure Stack-felügyeleti feladatok, például PNU vagy FRU üzemel-e a meghajtó. De a rendszergazda is tudta elhelyezni a meghajtó a karbantartási módban.<br> <br>**Művelet:** Várjon, amíg befejeződik a feladat az Azure Stack, és ezt követően ellenőrizze az állapotát.<br>Ha az állapot marad, forduljon az ügyfélszolgálatához. Mielőtt elkezdené, a fájl naplógyűjtési folyamat a útmutatásának https://aka.ms/azurestacklogfiles. |
|  |  |
| Karbantartási mód leállítása | Az Azure Stack a meghajtó online állapotba történő visszaállítását folyamatban van. Ez csak átmeneti állapot – a meghajtó hamarosan állapotban kell lennie egy másik – ideális kifogástalan állapotú.<br> <br>**Művelet:** Várjon, amíg a folyamat befejezéséhez az Azure Stack, és ezt követően ellenőrizze az állapotát. |

 

### <a name="drive-health-state-unhealthy"></a>Meghajtó állapota: Nem kifogástalan

A meghajtó nem kifogástalan állapotú jelenleg nem írt vagy érhető el.

| működési állapot | Leírás |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Felosztás | A meghajtó kivált válnak a készletből.<br> <br>**Művelet:** Cserélje le a meghajtó egy új lemezt. Ezt a lemezt kell használnia, ha a lemez eltávolítása a rendszerből, győződjön meg arról, hogy a lemez nem tartoznak a hasznos adatok, és a lemez törlése, majd távolítsa el a lemezt. |
| Nem használható | A fizikai lemez karanténba van helyezve, mert a megoldás gyártója által nem támogatott. Csak olyan lemezeket, amelyek a megoldás jóváhagyással, és rendelkezik a megfelelő lemezfirmware támogatja.<br> <br>**Művelet:** Cserélje le a meghajtó egy lemezt, amely rendelkezik egy jóváhagyott gyártó és modellszám a megoldáshoz. |
| Elavult metaadatok | A cserelemezt korábban használt, és a egy ismeretlen tárolórendszer származó adatokat is tartalmaznak. A lemez karanténba van helyezve.        <br> <br>**Művelet:** Cserélje le a meghajtó egy új lemezt. Ezt a lemezt kell használnia, ha a lemez eltávolítása a rendszerből, győződjön meg arról, hogy a lemez nem tartoznak a hasznos adatok, és a lemez törlése, majd távolítsa el a lemezt. |
| Ismeretlen metaadatok | Ismeretlen metaadatok a meghajtón, amely általában azt jelenti, hogy rendelkezik-e a meghajtó metaadatok, különböző készletből található.<br> <br>**Művelet:** Cserélje le a meghajtó egy új lemezt. Ezt a lemezt kell használnia, ha a lemez eltávolítása a rendszerből, győződjön meg arról, hogy a lemez nem tartoznak a hasznos adatok, és a lemez törlése, majd távolítsa el a lemezt. |
| Meghibásodott adathordozó | A meghajtó nem sikerült, és többé nem használják a tárolóhelyek.<br> <br>**Művelet:** Cserélje le a meghajtó, amint lehetséges teljes rugalmasságának biztosításához. |
| Eszköz hardverhiba | Hiba történt a hardver ezen a meghajtón. <br> <br>**Művelet:** Cserélje le a meghajtó, amint lehetséges teljes rugalmasságának biztosításához. |
| Belső vezérlőprogram frissítése | Az Azure Stack a meghajtó belső vezérlőprogramjának frissítése folyamatban van. Ez csak egy ideiglenes állapot, amely általában kevesebb mint egy percig tart, és amely során a készlet más meghajtóinak kezeléséhez idő olvasási és írási.<br> <br>**Művelet:** Várjon, amíg a frissítés befejezéséhez az Azure Stack, és ezt követően ellenőrizze az állapotát. |
| Indítás | A meghajtó hamarosan használatra kész. Ez egy átmeneti állapot – Miután végzett, a meghajtó átmenet különböző működési állapotot kell legyen.<br> <br>**Művelet:** Várjon, amíg a művelet befejezéséhez az Azure Stack, és ezt követően ellenőrizze az állapotát. |
 

## <a name="reasons-a-drive-cant-be-pooled"></a>A meghajtó nem vonható készletbe okok

Bizonyos meghajtókat csak nem áll készen az Azure Stack-tárolókészletben lehet. Talál egy meghajtó nem alkalmas a készletezésre megnézzük a CannotPoolReason tulajdonsága egy meghajtó. Az alábbi táblázat áttekintést nyújt a részletesebben egyes az okokat.

| Reason | Leírás |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nem megfelelő hardver | A meghajtó nem szerepel a listán, a jóváhagyott tárolási modellek az állapotfigyelő szolgáltatás használatával.<br> <br>**Művelet:** Cserélje le a meghajtó egy új lemezt. |
| Belső vezérlőprogram nem megfelelő | A fizikai meghajtó belső vezérlőprogramját a listában nem szereplő jóváhagyott belső vezérlőprogramok, az állapotfigyelő szolgáltatás használatával.<br> <br>**Művelet:** Cserélje le a meghajtó egy új lemezt. |
| Fürt által használt | A feladatátvevő fürtök jelenleg használja a meghajtót.<br> <br>**Művelet:** Cserélje le a meghajtó egy új lemezt. |
| Cserélhető adathordozó | A meghajtó cserélhető meghajtóként van besorolva. <br> <br>**Művelet:** Cserélje le a meghajtó egy új lemezt. |
| Nem kifogástalan állapotú | A meghajtó nem kifogástalan állapotú, és előfordulhat, hogy kell helyettesíteni.<br> <br>**Művelet:** Cserélje le a meghajtó egy új lemezt. |
| Nincs elegendő kapacitás | Nincsenek fel a szabad lemezterület a meghajtón a partíciók.<br> <br>**Művelet:** Cserélje le a meghajtó egy új lemezt. Ezt a lemezt kell használnia, ha a lemez eltávolítása a rendszerből, győződjön meg arról, hogy a lemez nem tartoznak a hasznos adatok, és a lemez törlése, majd távolítsa el a lemezt. |
| Ellenőrzés folyamatban | Az állapotfigyelő szolgáltatás ellenőrzi, ha a meghajtó vagy a meghajtó belső vezérlőprogramját használatra jóváhagyott megtekintéséhez.<br> <br>**Művelet:** Várjon, amíg a folyamat befejezéséhez az Azure Stack, és ezt követően ellenőrizze az állapotát. |
| Sikertelen ellenőrzés | Az állapotfigyelő szolgáltatás nem tudta ellenőrizze, hogy ha a meghajtó vagy a meghajtó belső vezérlőprogramját használatra jóvá.<br> <br>**Művelet:** Forduljon a támogatási. Mielőtt elkezdené, a fájl naplógyűjtési folyamat a útmutatásának https://aka.ms/azurestacklogfiles. |
| Offline | A meghajtó nem lesz elérhető. <br> <br>**Művelet:** Forduljon a támogatási. Mielőtt elkezdené, a fájl naplógyűjtési folyamat a útmutatásának https://aka.ms/azurestacklogfiles. |

## <a name="next-step"></a>Következő lépés

[Tárolási kapacitás kezelése](azure-stack-manage-storage-shares.md) 
