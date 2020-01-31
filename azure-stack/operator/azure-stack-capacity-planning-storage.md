---
title: Azure Stack hub kapacitás-tervezési tárolója
description: További információ a Azure Stack hub üzemelő példányok kapacitásának megtervezéséről.
author: ihenkel
ms.topic: article
ms.date: 05/31/2019
ms.author: inhenkel
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: db54510b118c688bf74ce5ba69355359d1c0cc85
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76878921"
---
# <a name="azure-stack-hub-storage"></a>Azure Stack hub Storage

A következő szakaszokban Azure Stack hub tárolási kapacitásának tervezési információi segítenek a megoldás tárolási igényeinek megtervezésében.

## <a name="uses-and-organization-of-storage-capacity"></a>Tárolási kapacitás használata és szervezete
Azure Stack hub hiperkonvergens konfigurációja lehetővé teszi a fizikai tárolóeszközök megosztását. A rendelkezésre álló tárterület három fő részből áll: az infrastruktúra, a bérlői virtuális gépek ideiglenes tárolása, valamint az Azure konzisztens tárolási (ACS) szolgáltatások Blobok, táblák és várólisták biztonsági mentése.

## <a name="storage-spaces-direct-cache-and-capacity-tiers"></a>Közvetlen tárolóhelyek gyorsítótár és a kapacitás szintjei
Az operációs rendszerhez, a helyi naplózáshoz, a memóriaképekhez és az egyéb ideiglenes infrastruktúra-tárolási igényekhez tárolási kapacitás van használatban. Ez a helyi tárolókapacitás a Közvetlen tárolóhelyek konfiguráció felügyelete alá tartozó tárolóeszközöktől különálló (eszközök és kapacitás). A tárolóeszközök fennmaradó részét a rendszer a tárolási kapacitás egyetlen készletében helyezi el, a skálázási egységben található kiszolgálók számától függetlenül.

Ezek az eszközök két típusúak: cache és Capacity. Közvetlen tárolóhelyek a gyorsítótár-eszközöket használja a írási és olvasási gyorsítótárazáshoz. A gyorsítótárazott eszközök kapacitása, míg használatban van, nem véglegesítve van a formázott virtuális lemezek "látható" kapacitása alapján. Ezzel szemben a Közvetlen tárolóhelyek kapacitási eszközöket használ erre a célra, így biztosítva a felügyelt adat "otthoni helyét".

Az Azure Stack hub-infrastruktúra közvetlenül foglalja le és kezeli az összes tárolókapacitást. Az operátornak nem kell döntéseket hoznia a konfigurációval, a kiosztással és a kapacitás bővítésével kapcsolatban. Azure Stack hub automatizálja ezeket a kialakítási döntéseket a megoldási követelményekhez való igazításhoz a kezdeti telepítés és telepítés során, vagy a kapacitás bővítését illetően. Az Azure Stack hub a kialakítás részeként figyelembe veszi a rugalmasságot, a fenntartott kapacitást az újraépítéshez és egyéb részletekhez. 

A kezelők választhatnak az *összes Flash* vagy egy *hibrid* tárolási konfiguráció közül:

![Az Azure Storage-kapacitás tervezésének diagramja](media/azure-stack-capacity-planning/storage.png)

A minden Flash-konfigurációban a konfiguráció lehet kétrétegű vagy egyrétegű konfiguráció. Ha a konfiguráció egyrétegű, az összes kapacitási eszköz ugyanolyan típusú (például NVMe vagy SATA SSD vagy SAS SSD), és nem használja a cache-eszközöket. A kétrétegű összes Flash konfigurációban a tipikus konfiguráció a NVMe, majd a SATA-vagy SAS SSD-k kapacitása.

A hibrid, kétrétegű konfigurációban a gyorsítótár a NVMe, a SATA vagy a SAS SSD közül választhat, és a kapacitás HDD. 

A Közvetlen tárolóhelyek és Azure Stack hub tárolási konfigurációjának rövid összefoglalása a következő:
- Egy Közvetlen tárolóhelyek készlet/méretezési egység (az összes tárolóeszköz egyetlen készleten belül van konfigurálva).
- A virtuális lemezek a legjobb teljesítmény és rugalmasság érdekében három másolási tükrözéssel jönnek létre.
- Minden virtuális lemez ReFS-fájlrendszerként van formázva.
- A virtuális lemez kapacitása kiszámításra kerül, és úgy van kiosztva, hogy a készletben ne legyenek lefoglalva az egyik kapacitási eszköz mennyisége. A kiszolgáló egy kapacitási meghajtójának a megfelelője.
- Minden ReFS-fájlrendszerhez engedélyezve van a BitLocker az adatok Rest titkosítása esetén. 

A virtuális lemezek automatikusan lettek létrehozva, és a kapacitásaik a következők:

|Név|Kapacitás kiszámítása|Leírás|
|-----|-----|-----|
|Helyi/rendszerindító eszköz|Legalább 340 GB<sup>1</sup>|Az operációs rendszer lemezképei és a "helyi" infrastruktúra virtuális gépei egyedi kiszolgálói tárterülete.|
|Infrastruktúra|3,5 TB|Minden Azure Stack hub-infrastruktúra használata.|
|VmTemp|Lásd alább<sup>2</sup>|A bérlői virtuális gépekhez ideiglenes lemez van csatlakoztatva, és ezeket a virtuális lemezeket a rendszer tárolja.|
|ACS|Lásd az alábbi <sup>3</sup>|Az Azure konzisztens tárolási kapacitása, mely a Blobok, táblák és várólisták kiszolgálására szolgál.|

<sup>1</sup> a Azure stack hub-megoldási partner számára szükséges minimális tárolókapacitás.

<sup>2</sup> a bérlői virtuális gépekhez használt virtuális lemez méretét a kiszolgáló fizikai memóriájának arányában számítjuk ki. Az ideiglenes lemez a virtuális géphez rendelt fizikai memória arányát jelöli. A Azure Stack hub "Temp Disk" tárolójának kiosztása a legtöbb felhasználási esetet rögzíti, de előfordulhat, hogy nem tudja kielégíteni az összes Temp Disk Storage-igényt. Az arány az ideiglenes tárhely elérhetővé tétele és a megoldás tárolási kapacitásának nagy része nem fogyasztható el a csak Temp disk capacity esetében. Kiszolgálónként egy ideiglenes tároló jön létre a skálázási egységben. Az ideiglenes tároló kapacitása nem nő a skálázási egység tárolási készletének teljes rendelkezésre álló tárolókapacitásának 10%-ában. A számítás a következő példához hasonló:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> az ACS általi használatra létrehozott virtuális lemezek a fennmaradó kapacitás egyszerű megosztása. Ahogy azt már említettük, az összes virtuális lemez háromutas tükrözés, és az egyes kiszolgálók kapacitásainak egyike nem foglalt le. A korábban enumerált különböző virtuális lemezek elsőként vannak lefoglalva, a fennmaradó kapacitás pedig az ACS virtuális lemezek esetében használatos.


## <a name="next-steps"></a>Következő lépések
További információ az [Azure stack Hub Capacity Plannerról](azure-stack-capacity-planner.md).
