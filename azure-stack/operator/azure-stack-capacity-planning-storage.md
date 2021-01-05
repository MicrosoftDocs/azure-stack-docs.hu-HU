---
title: Azure Stack hub tárolási kapacitásának megtervezése
description: Tudnivalók a Azure Stack hub üzemelő példányok tárolási kapacitásának megtervezéséről.
author: PatAltimore
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 69248d5a81cf3d1017f221a57549d75b205f0fc3
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871259"
---
# <a name="azure-stack-hub-storage-capacity-planning"></a>Azure Stack hub tárolási kapacitásának megtervezése

A következő szakaszokban Azure Stack hub tárolási kapacitásának tervezési információi segítenek a megoldás tárolási igényeinek megtervezésében.

## <a name="uses-and-organization-of-storage-capacity"></a>Tárolási kapacitás használata és szervezete

Azure Stack hub hiperkonvergens konfigurációja lehetővé teszi a fizikai tárolóeszközök megosztását. A rendelkezésre álló tárterület három fő részből áll: az infrastruktúra, a bérlői virtuális gépek ideiglenes tárolása, valamint az Azure konzisztens tárolási (ACS) szolgáltatások Blobok, táblák és várólisták biztonsági mentése.

## <a name="storage-spaces-direct-cache-and-capacity-tiers"></a>Közvetlen tárolóhelyek gyorsítótár és a kapacitás szintjei

Az operációs rendszerhez, a helyi naplózáshoz, a memóriaképekhez és az egyéb ideiglenes infrastruktúra-tároláshoz igénybe vett tárolási kapacitás. Ez a helyi tárolókapacitás a Közvetlen tárolóhelyek konfiguráció felügyelete alá tartozó tárolóeszközöktől különálló (eszközök és kapacitás). A többi tárolóeszköz a tárolási kapacitás egyetlen készletében van elhelyezve, a skálázási egységben található kiszolgálók számától függetlenül.

Ezek az eszközök két típusúak: cache és Capacity. Közvetlen tárolóhelyek a gyorsítótár-eszközöket használja a írási és olvasási gyorsítótárazáshoz. A gyorsítótárazott eszközök kapacitása a használat közben nem a formázott virtuális lemezek formázott és "látható" kapacitására van kikötve. Ezzel szemben a Közvetlen tárolóhelyek kapacitási eszközöket használ erre a célra, így biztosítva a felügyelt adat "otthoni helyét".

Az Azure Stack hub-infrastruktúra közvetlenül foglalja le és kezeli az összes tárolókapacitást. Az operátornak nem kell döntéseket hoznia a konfigurációval, a kiosztással és a kapacitás bővítésével kapcsolatban. Azure Stack hub automatizálja ezeket a kialakítási döntéseket a megoldási követelményekhez való igazításhoz a kezdeti telepítés és telepítés vagy a kapacitás bővítése során. Az Azure Stack hub a kialakítás részeként figyelembe veszi a rugalmasságot, a fenntartott kapacitást az újraépítéshez és egyéb részletekhez.

A kezelők választhatnak az *összes Flash* vagy egy *hibrid* tárolási konfiguráció közül:

![Az Azure Storage-kapacitás tervezésének diagramja](media/azure-stack-capacity-planning/storage.png)

A minden Flash-konfigurációban a konfiguráció lehet kétrétegű vagy egyrétegű konfiguráció. Ha a konfiguráció egyrétegű, az összes kapacitási eszköz azonos típusú (például *NVMe* vagy *SATA SSD* vagy *sas SSD*), és a gyorsítótár-eszközök nem használatosak. A kétrétegű összes Flash konfigurációban a tipikus konfiguráció a NVMe, majd a SATA-vagy SAS SSD-k kapacitása.

A hibrid kétrétegű konfigurációban a gyorsítótár a NVMe, a SATA vagy a SAS SSD közül választhat, és a kapacitás HDD.

A Közvetlen tárolóhelyek és Azure Stack hub tárolási konfigurációjának rövid összefoglalása a következő:
- Egy Közvetlen tárolóhelyek készlet/méretezési egység (az összes tárolóeszköz egyetlen készleten belül van konfigurálva).
- A virtuális lemezek három másolási tükrözésként jönnek létre a legjobb teljesítmény és rugalmasság érdekében.
- Minden virtuális lemez ReFS-fájlrendszerként van formázva.
- A virtuális lemez kapacitása kiszámításra kerül, és úgy van kiosztva, hogy a készletben ne legyenek lefoglalva az egyik kapacitási eszköz mennyisége. A kiszolgáló egy kapacitási meghajtójának a megfelelője.
- Minden ReFS-fájlrendszerhez engedélyezve van a BitLocker az adatok Rest titkosítása esetén. 

A virtuális lemezek automatikusan lettek létrehozva, és a kapacitásaik a következők:

|Név|Kapacitás kiszámítása|Leírás|
|-----|-----|-----|
|Helyi/rendszerindító eszköz|Legalább 340 GB<sup>1</sup>|Az operációs rendszer lemezképei és a "helyi" infrastruktúra virtuális gépei egyedi kiszolgálói tárterülete.|
|Infrastruktúra|3,5 TB|Minden Azure Stack hub-infrastruktúra használata.|
|VmTemp|Lásd alább<sup>2</sup>|A bérlői virtuális gépekhez ideiglenes lemez van csatlakoztatva, és ezeket az adatlemezeket tárolja a rendszer.|
|ACS|Lásd az alábbi <sup>3</sup>|Az Azure konzisztens tárolási kapacitása, mely a Blobok, táblák és várólisták kiszolgálására szolgál.|

<sup>1</sup> a Azure stack hub-megoldási partner számára szükséges minimális tárolókapacitás.

<sup>2</sup> a bérlői virtuális gép ideiglenes lemezeihez használt virtuális lemez méretét a kiszolgáló fizikai memóriájának arányában számítjuk ki. Az ideiglenes lemez a virtuális géphez rendelt fizikai memória arányát jelöli. A Azure Stack hub "Temp Disk" tárolójának kiosztása a legtöbb felhasználási esetet rögzíti, de előfordulhat, hogy nem tudja kielégíteni az összes Temp Disk Storage-igényt. Az arány az ideiglenes tárhely elérhetővé tétele és a megoldás tárolási kapacitásának nagy része nem fogyasztható el a csak Temp disk capacity esetében. Kiszolgálónként egy ideiglenes tároló jön létre a skálázási egységben. Az ideiglenes tároló kapacitása nem nő a skálázási egység tárolási készletének teljes rendelkezésre álló tárolókapacitásának 10%-ában. A számítás a következő példához hasonló:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> az ACS általi használatra létrehozott virtuális lemezek a fennmaradó kapacitás egyszerű megosztása. Ahogy azt is említettük, az összes virtuális lemez háromutas tükrözés, és az egyes kiszolgálók kapacitásának egyik kapacitása nem foglalt le. A korábban enumerált különböző virtuális lemezek elsőként vannak lefoglalva, a fennmaradó kapacitás pedig az ACS virtuális lemezek esetében használatos.

## <a name="next-steps"></a>További lépések

További információ az [Azure stack Hub Capacity Plannerról](azure-stack-capacity-planner.md).
