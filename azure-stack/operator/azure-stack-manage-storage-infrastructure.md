---
title: Azure Stack hub tárolási infrastruktúrájának kezelése
titleSuffix: Azure Stack
description: Ismerje meg, hogyan kezelheti Azure Stack hub tárolási infrastruktúráját.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 5/4/2020
ms.author: inhenkel
ms.lastreviewed: 5/4/2020
ms.reviewer: jiaha
ms.openlocfilehash: 3fff3c9c7742d22c24ab540d4bf523ade3e7a227
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82848304"
---
# <a name="manage-storage-infrastructure-for-azure-stack-hub"></a>Azure Stack hub tárolási infrastruktúrájának kezelése
Ez a cikk az Azure Stack hub tárolási infrastruktúra erőforrásainak állapotát és működési állapotát ismerteti. Ezek az erőforrások a tárolóeszközöket és a köteteket tartalmazzák. Az ebben a témakörben található információk segítenek a különböző problémák megoldásában, például ha nem lehet meghajtót felvenni a készletbe.

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

Az alábbi szakaszban az állapot és az üzemeltetési állapotok szerepelnek:

### <a name="volume-health-state-healthy"></a>Kötet állapota: kifogástalan

| Működési állapot | Description |
|---|---|
| OK | A kötet kifogástalan. |
| Szuboptimális | Az adatlemezek nem kerülnek egyenletesen a meghajtókon.<br> <br>**Művelet:** Lépjen kapcsolatba az ügyfélszolgálattal a meghajtó használatának optimalizálásához a Storage-készletben. Mielőtt elkezdené, indítsa el a naplófájlok gyűjtésének folyamatát a https://aka.ms/azurestacklogfileskövetkező témakör útmutatása alapján:. Előfordulhat, hogy a sikertelen kapcsolódás visszaállítása után vissza kell állítania a biztonsági mentést. |

### <a name="volume-health-state-warning"></a>Kötet állapota: figyelmeztetés

Ha a kötet figyelmeztetési állapotba kerül, az azt jelenti, hogy az adatai közül egy vagy több másolata nem érhető el, de Azure Stack hub továbbra is olvashatja az adatai legalább egy példányát.

| Működési állapot | Description |
|---|---|
| A szolgáltatásban | Azure Stack hub kijavítja a kötetet, például egy meghajtó hozzáadásával vagy eltávolításával. A javítás befejezésekor a kötetnek vissza kell térnie az OK állapotára.<br> <br>**Művelet:** Várjon, amíg a Azure Stack hub befejezi a kötet javítását, majd utána az állapotot. |
| Hiányos | A kötet rugalmassága csökken, mert egy vagy több meghajtó meghiúsult vagy hiányzik. A hiányzó meghajtók azonban az adatok naprakész másolatát tartalmazzák.<br> <br>**Művelet:** Csatlakoztassa újra a hiányzó meghajtókat, cserélje le a meghibásodott meghajtókat, és kapcsolja online állapotba az összes olyan kiszolgálót, amely offline állapotú. |
| Csökkentett teljesítményű | A kötet rugalmassága egy vagy több meghibásodott vagy hiányzó meghajtó, valamint a meghajtókon tárolt elavult adatmásolatok miatt csökken.<br> <br>**Művelet:** Csatlakoztassa újra a hiányzó meghajtókat, cserélje le a meghibásodott meghajtókat, és kapcsolja online állapotba az összes olyan kiszolgálót, amely offline állapotú. |

### <a name="volume-health-state-unhealthy"></a>Kötet állapotának állapota: sérült

Ha egy kötet nem kifogástalan állapotú, a köteten lévő egyes vagy az összes érték jelenleg nem érhető el.

| Működési állapot | Description |
|---|---|
| Nincs redundancia | A kötet elveszítette az adatvesztést, mert túl sok meghajtó meghiúsult.<br> <br>**Művelet:** Forduljon az ügyfélszolgálathoz. Mielőtt elkezdené, indítsa el a naplófájlok gyűjtésének folyamatát a https://aka.ms/azurestacklogfileskövetkező témakör útmutatása alapján:. |

### <a name="volume-health-state-unknown"></a>Kötet állapota: ismeretlen

A kötet ismeretlen állapotba is helyezhető, ha a virtuális lemez le lett választva.

| Működési állapot | Description |
|---|---|
| Családi | Egy tárolóeszköz meghibásodása meghiúsult, ami miatt a kötet elérhetetlenné válhat. Előfordulhat, hogy egyes adatvesztések megszakadnak.<br> <br>**Művelet** <br>1. az összes tárolóeszköz fizikai és hálózati kapcsolatának ellenőrzése.<br>2. Ha minden eszköz megfelelően van csatlakoztatva, forduljon az ügyfélszolgálathoz. Mielőtt elkezdené, indítsa el a naplófájlok gyűjtésének folyamatát a https://aka.ms/azurestacklogfileskövetkező témakör útmutatása alapján:. Előfordulhat, hogy a sikertelen kapcsolódás visszaállítása után vissza kell állítania a biztonsági mentést. |

## <a name="drive-states"></a>Meghajtók állapota

A meghajtók állapotának figyeléséhez használja a következő PowerShell-parancsokat:

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsDrive -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object StorageNode, PhysicalLocation, HealthStatus, OperationalStatus, Description, Action, Usage, CanPool, CannotPoolReason, SerialNumber, Model, MediaType, CapacityGB
```

A következő szakaszok ismertetik a meghajtó állapotának állapotát:

### <a name="drive-health-state-healthy"></a>Meghajtó állapota: kifogástalan

| Működési állapot | Description |
|---|---|
| OK | A kötet kifogástalan. |
| A szolgáltatásban | A meghajtó egy belső takarítási műveletet hajt végre. Ha a művelet befejeződött, a meghajtónak vissza kell térnie az OK állapotára. |

### <a name="drive-health-state-healthy"></a>Meghajtó állapota: kifogástalan

A figyelmeztetési állapotban lévő meghajtó sikeresen képes olvasni és írni az adatírást, de probléma van.

| Működési állapot | Description |
|---|---|
| Elveszett kommunikáció | A kapcsolat megszakadt a meghajtón.<br> <br>**Művelet:** Minden kiszolgáló ismét online állapotba kerül. Ha ez nem oldja meg a javítást, csatlakoztassa újra a meghajtót. Ha ez az állapot tartósan fennáll, cserélje le a meghajtót a teljes rugalmasság biztosítására. |
| Prediktív hiba | A meghajtó meghibásodása várhatóan hamarosan megtörténik.<br> <br>**Művelet:** A lehető leghamarabb cserélje le a meghajtót a teljes rugalmasság biztosításához. |
| IO-hiba | Ideiglenes Hiba történt a meghajtó elérésekor.<br> <br>**Művelet:** Ha ez az állapot tartósan fennáll, cserélje le a meghajtót a teljes rugalmasság biztosítására. |
| Átmeneti hiba | Ideiglenes Hiba történt a meghajtóval. Ez a hiba általában azt jelenti, hogy a meghajtó nem válaszol, de azt is jelentheti, hogy a Közvetlen tárolóhelyek védelmi partíció helytelenül lett eltávolítva a meghajtóról. <br> <br>**Művelet:** Ha ez az állapot tartósan fennáll, cserélje le a meghajtót a teljes rugalmasság biztosítására. |
| Rendellenes késés | A meghajtó néha nem válaszol, és a hiba jeleit mutatja.<br> <br>**Művelet:** Ha ez az állapot tartósan fennáll, cserélje le a meghajtót a teljes rugalmasság biztosítására. |
| Eltávolítás a készletből | Azure Stack hub folyamatban van a meghajtó eltávolítása a tároló készletéből.<br> <br>**Művelet:** Várjon, amíg a Azure Stack hub befejezi a meghajtó eltávolítását, majd utána az állapotot.<br>Ha az állapot továbbra is fennáll, forduljon az ügyfélszolgálathoz. Mielőtt elkezdené, indítsa el a naplófájlok gyűjtésének folyamatát a https://aka.ms/azurestacklogfileskövetkező témakör útmutatása alapján:. |
| Karbantartási mód indítása | Azure Stack hub folyamatban van a meghajtó karbantartási módba helyezése. Ez az állapot ideiglenes – a meghajtónak hamarosan karbantartási üzemmód állapotban kell lennie.<br> <br>**Művelet:** Várjon, amíg a Azure Stack hub befejezi a folyamatot, és utána kérdezze le az állapotot. |
| Karbantartási módban | A meghajtó karbantartási üzemmódban van, és leállítja az olvasásokat és az írásokat a meghajtóról. Ez az állapot általában azt jelenti, Azure Stack hub felügyeleti feladatait, például a PNU vagy a cserélhető eszközt a meghajtón működnek. A rendszergazda azonban a meghajtót karbantartási módba is helyezheti.<br> <br>**Művelet:** Várjon, amíg a hub Azure Stack hub befejezi az adminisztrációs feladatot, majd utána az állapotot.<br>Ha az állapot továbbra is fennáll, forduljon az ügyfélszolgálathoz. Mielőtt elkezdené, indítsa el a naplófájlok gyűjtésének folyamatát a https://aka.ms/azurestacklogfileskövetkező témakör útmutatása alapján:. |
| Karbantartási mód leállítása | Azure Stack hub folyamatban van a meghajtó újbóli online állapotba hozása. Ez az állapot ideiglenes – a meghajtónak hamarosan egy másik állapotban kell lennie, ideális esetben kifogástalan.<br> <br>**Művelet:** Várjon, amíg a Azure Stack hub befejezi a folyamatot, és utána kérdezze le az állapotot. |

### <a name="drive-health-state-unhealthy"></a>Meghajtó állapota: sérült

A sérült állapotú meghajtók jelenleg nem írhatók vagy nem érhetők el.

| Működési állapot | Description |
|---|---|
| Felosztás | A meghajtó el lett választva a készlettől.<br> <br>**Művelet:** Cserélje le a meghajtót egy új lemezre. Ha ezt a lemezt kell használnia, távolítsa el a lemezt a rendszerből, ügyeljen arra, hogy ne legyen hasznos adat a lemezen, törölje a lemezt, majd helyezze át a lemezt. |
| Nem használható | A fizikai lemez Karanténba kerül, mert a megoldás gyártója nem támogatja. Csak a megoldás számára jóváhagyott lemezek támogatottak, és a megfelelő lemezes belső vezérlőprogram használható.<br> <br>**Művelet:** Cserélje le a meghajtót egy olyan lemezre, amely rendelkezik jóváhagyott gyártó és modell számmal a megoldáshoz. |
| Elavult metaadatok | A helyettesítő lemezt korábban használták, és tartalmazhatnak ismeretlen tárolási rendszerből származó adatokkal. A lemez karanténba van helyezve. <br> <br>**Művelet:** Cserélje le a meghajtót egy új lemezre. Ha ezt a lemezt kell használnia, távolítsa el a lemezt a rendszerből, ügyeljen arra, hogy ne legyen hasznos adat a lemezen, törölje a lemezt, majd helyezze át a lemezt. |
| Ismeretlen metaadatok | Ismeretlen metaadatok találhatók a meghajtón, ami általában azt jelenti, hogy a meghajtón található metaadatok egy másik készletből származnak.<br> <br>**Művelet:** Cserélje le a meghajtót egy új lemezre. Ha ezt a lemezt kell használnia, távolítsa el a lemezt a rendszerből, ügyeljen arra, hogy ne legyen hasznos adat a lemezen, törölje a lemezt, majd helyezze át a lemezt. |
| Sikertelen adathordozó | A meghajtó nem sikerült, és a tárolóhelyek már nem használják.<br> <br>**Művelet:** A lehető leghamarabb cserélje le a meghajtót a teljes rugalmasság biztosításához. |
| Hardverhiba az eszközön | Hardverhiba történt a meghajtón. <br> <br>**Művelet:** A lehető leghamarabb cserélje le a meghajtót a teljes rugalmasság biztosításához. |
| A belső vezérlőprogram frissítése | Azure Stack hub frissíti a belső vezérlőprogramot a meghajtón. Ez az állapot ideiglenes, és általában kevesebb mint egy percet tart, és amikor a készletben lévő többi meghajtó kezeli az összes olvasást és írást.<br> <br>**Művelet:** Várjon, amíg a Azure Stack hub befejezi a frissítést, és utána kérdezze le az állapotot. |
| Indítás | A meghajtó készen áll a műveletre. Ennek az állapotnak ideiglenesnek kell lennie – Ha elkészült, a meghajtónak egy másik működési állapotba kell térnie.<br> <br>**Művelet:** Várjon, amíg a Azure Stack hub befejezi a műveletet, és utána kérdezze le az állapotot. |

## <a name="reasons-a-drive-cant-be-pooled"></a>A meghajtó nem egyesíthető.

Egyes meghajtók egyszerűen nem állnak készen Azure Stack hub Storage-készletben való használatra. Azt is megtudhatja, hogy a meghajtó miért nem jogosult a készletezésre a `CannotPoolReason` meghajtó tulajdonságának megtekintésével. Az alábbi táblázat egy kicsit részletesebben ismerteti az egyes okokat.

| Ok | Description |
|---|---|
| A hardver nem megfelelő | A meghajtó nem szerepel a Állapotfigyelő szolgáltatás használatával megadott, jóváhagyott tárolási modellek listáján.<br> <br>**Művelet:** Cserélje le a meghajtót egy új lemezre. |
| A belső vezérlőprogram nem megfelelő | A fizikai meghajtón található belső vezérlőprogram nem szerepel a jóváhagyott belső vezérlőprogram-változatok listájában a Állapotfigyelő szolgáltatás használatával.<br> <br>**Művelet:** Cserélje le a meghajtót egy új lemezre. |
| Használatban fürt szerint | A meghajtót jelenleg egy feladatátvevő fürt használja.<br> <br>**Művelet:** Cserélje le a meghajtót egy új lemezre. |
| Cserélhető adathordozó | A meghajtó cserélhető meghajtóként van osztályozva. <br> <br>**Művelet:** Cserélje le a meghajtót egy új lemezre. |
| Nem kifogástalan | A meghajtó nem kifogástalan állapotban van, ezért előfordulhat, hogy le kell cserélni.<br> <br>**Művelet:** Cserélje le a meghajtót egy új lemezre. |
| Nincs elegendő kapacitás | A meghajtón vannak partíciók a szabad területtel.<br> <br>**Művelet:** Cserélje le a meghajtót egy új lemezre. Ha ezt a lemezt kell használnia, távolítsa el a lemezt a rendszerből, ügyeljen arra, hogy ne legyen hasznos adat a lemezen, törölje a lemezt, majd helyezze át a lemezt. |
| Ellenőrzés folyamatban | A Állapotfigyelő szolgáltatás ellenőrzi, hogy a meghajtó vagy a belső vezérlőprogram engedélyezett-e a használatra.<br> <br>**Művelet:** Várjon, amíg a Azure Stack hub befejezi a folyamatot, majd utána az állapotot. |
| Az ellenőrzés nem sikerült | A Állapotfigyelő szolgáltatás nem tudta megtekinteni, hogy a meghajtó vagy a belső vezérlőprogram engedélyezett-e a használatra.<br> <br>**Művelet:** Forduljon az ügyfélszolgálathoz. Mielőtt elkezdené, indítsa el a naplófájlok gyűjtésének folyamatát a https://aka.ms/azurestacklogfileskövetkező témakör útmutatása alapján:. |
| Offline | A meghajtó offline állapotban van. <br> <br>**Művelet:** Forduljon az ügyfélszolgálathoz. Mielőtt elkezdené, indítsa el a naplófájlok gyűjtésének folyamatát a https://aka.ms/azurestacklogfileskövetkező témakör útmutatása alapján:. |