---
title: Az Azure Stack kapacitástervezési storage |} A Microsoft Docs
description: További információ az Azure Stack üzemelő példányok kapacitástervezése.
services: azure-stack
documentationcenter: ''
author: prchint
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 30ce69f96747ab8dbdafd9e20e8cea07026074d5
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2019
ms.locfileid: "66461013"
---
# <a name="azure-stack-storage"></a>Az Azure Stack-tároló

Az alábbi szakaszok az Azure Stack tárolókapacitást biztosítanak tervezési információkat a megoldások tárolási szükségletek segítség.

## <a name="uses-and-organization-of-storage-capacity"></a>Használja, és a szervezet a tárolási kapacitás
Az Azure Stack hiperkonvergens konfigurációja lehetővé teszi a fizikai tárolóeszközök megosztását. A rendelkezésre álló tár három fő egységét vannak, az infrastruktúra, a bérlői virtuális gépek ideiglenes tároló és a tárolási, biztonsági a blobok, táblák és üzenetsorok az Azure-konzisztens tároló (ACS) szolgáltatások között.

## <a name="spaces-direct-cache-and-capacity-tiers"></a>Közvetlen tárolóhelyek gyorsítótárának és kapacitásszintek
Nincs a használt tárolási kapacitást az operációs rendszer, a helyi naplózás, a memóriaképek és a többi ideiglenes infrastruktúra tárolási igényeinek megfelelően. A helyi tárolási kapacitása (eszközökön és a kapacitás) a közvetlen tárolóhelyek szolgáltatás konfigurációjának kezelése alatt álló tárolóeszközök elkülönítése. A tárolóeszközök további része a tárolókapacitást a skálázási egységben kiszolgálók számától függetlenül egyetlen készlet kerül. Ezek az eszközök két típusa van: Gyorsítótár és a kapacitás.  A gyorsítótár-eszközök csak a - gyorsítótár lesznek. Közvetlen tárolóhelyek ezeknek az eszközöknek a késleltetve visszaírt használják, és olvasási gyorsítótárazás. A kapacitások ilyen gyorsítótár eszközt használja, amíg nem kerülnek át a formázott virtuális lemezek formázott, "látható" kapacitását. A kapacitáseszközöknek erre a célra használják, és adja meg a tárolóhelyek szolgáltatás által kezelt adatok "otthoni régiója".

Az összes tárolási kapacitás lefoglalt, és közvetlenül az Azure Stack-infrastruktúra kezel. Az operátor kell kiosztása, a konfigurációval kapcsolatos döntéseket vagy választási lehetőségek kezelése esetén, egyszerű kapacitásbővítést téve lehetővé. Ezek a tervezési döntések vezettük be, hogy összhangba kerüljenek a megoldás követelményeinek, és automatizált vagy kezdeti telepítési/üzembe helyezés során, vagy során egyszerű kapacitásbővítést téve lehetővé. A rugalmasság, újraépíteni fenntartott kapacitás és egyéb részletek részleteit vették a terv részeként. 

Kezelők vagy az összes flash, vagy egy hibrid tárolási konfiguráció közül választhat:

![Az Azure storage kapacitásának megtervezése](media/azure-stack-capacity-planning/storage.png)

Az összes flash konfigurációjában a konfiguráció lehet egy kétrétegű vagy egyrétegű konfigurációt.  Ha a konfiguráció egyrétegű, minden kapacitáseszközök azonos típusú (pl. NVMe vagy SATA SSD vagy SAS SSD) lesz, és gyorsítótárazó eszközöket nem használ. Egy kétrétegű minden flash konfiguráció, a tipikus konfigurációja NVMe, mint a gyorsítótár-eszközként, és ezután vagy SATA vagy a kapacitás eszközként SAS SSD-kkel.

A hibrid, kétrétegű konfiguráció, a gyorsítótár, NVMe, a SATA vagy SAS SSD és a kapacitás választhat HDD. 

Röviden összefoglalva a közvetlen tárolóhelyek és az Azure Stack tárolási konfigurációt a következőképpen történik:
- Egy szóközt tárolókészlet skálázási egység (az összes tárolóeszköz egyetlen belül vannak konfigurálva)
- Virtuális lemezek jönnek létre három példányt tükör a legjobb teljesítmény és rugalmasság
- Minden egyes virtuális lemez vannak formázva, a ReFS fájlrendszer
- Virtuális lemezkapacitás kiszámítása és lefoglalt úgy, hogy hagyja meg az adatok kapacitás a készletben lévő szabad egy kapacitás-eszköz mennyiségét. Ez megegyezik a Kiszolgálónként egy kapacitás-meghajtó.
- Minden egyes ReFS fájlrendszeren lesz engedélyezve az inaktív adatok titkosítása a BitLocker. 

A virtuális – a lemezek automatikusan létrehozza és kapacitásuk a következők:

|Name (Név)|A számítási kapacitás|Leírás|
|-----|-----|-----|
|Helyi vagy rendszerindító eszköz|340 GB-os minimális<sup>1</sup>|Egyes kiszolgálói tárhelyet operációsrendszer-lemezképek, a "local" infrastruktúra virtuális gépeinek|
|Infrastruktúra|3,5 TB|Minden Azure Stack-infrastruktúra használata|
|VmTemp|Lásd alább<sup>2</sup>|Bérlői virtuális gépeket egy ideiglenes lemez csatolva van, és ezek a virtuális lemezek tárolja az adatokat|
|ACS|Lásd alább <sup>3</sup>|Blobok, táblák és üzenetsorok karbantartási Azure konzisztens tárolási kapacitás|

<sup>1</sup> minimális tárolási kapacitást az Azure Stack megoldás partner szükséges.

<sup>2</sup> a bérlői virtuális gép ideiglenes lemezek használt virtuális lemez mérete számítjuk ki, hogy a kiszolgáló fizikai memória arány. Az alábbi táblázatokban az Azure IaaS Virtuálisgép-méretek feljegyzett ideiglenes lemez a virtuális géphez rendelt fizikai memória arány. A hozzárendelés kész "ideiglenes lemez" tárolás az Azure Stackben úgy, hogy a legtöbb használati esetek rögzítése végezheti el, de nem tudják kielégíteni minden ideiglenes lemez tárolási igényeinek megfelelően. Kiválasztott arány, az ideiglenes tároló elérhetővé tétele közben nem használja fel a tárolási kapacitás, a megoldás csak ideiglenes lemez kapacitás többsége között. A skálázási egységben kiszolgálónként egy ideiglenes tárolólemez jön létre. Az ideiglenes tárolási kapacitása 10 % a teljes elérhető kapacitást a tárolókészletben, a skálázási egység nem növekszik. A számítása a következőképpen fog kinézni a következő példa:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> a virtuális – ACS által használni létrehozott lemezei a fennmaradó kapacitás egy egyszerű osztás. Feljegyzett, az összes virtuális lemez egy háromutas tükrözött-e, és a egy kapacitás-meghajtó tekinthető meg minden olyan kiszolgáló kapacitásának le nem foglalt. A különböző virtuális lemezek fent felsorolt először vannak lefoglalva, és a fennmaradó kapacitás az ACS virtuális-lemezeit használja.


## <a name="next-steps"></a>További lépések
További információ a [az Azure Stack Capacity Planner](azure-stack-capacity-planner.md)
