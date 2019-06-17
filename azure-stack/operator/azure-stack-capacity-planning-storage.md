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
ms.openlocfilehash: 2845a90f97c1b859269f73333448bf42ff699da9
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131090"
---
# <a name="azure-stack-storage"></a>Az Azure Stack-tároló

A következő szakaszok az Azure Stack tárolókapacitás tervezési információkat a megoldás tárolási szükségletek segítség.

## <a name="uses-and-organization-of-storage-capacity"></a>Használja, és a szervezet a tárolási kapacitás
Az Azure Stack hiperkonvergens konfigurációja lehetővé teszi a fizikai tárolóeszközök megosztása. Vannak, amelyek megoszthatók a rendelkezésre álló tár három fő egységét: az infrastruktúra, a bérlői virtuális gépek ideiglenes tároló és a tárolási, biztonsági a blobok, táblák és üzenetsorok Azure-konzisztens tároló (ACS) szolgáltatást.

## <a name="storage-spaces-direct-cache-and-capacity-tiers"></a>A közvetlen tárolóhelyek gyorsítótárának és a kapacitás tárolórétegek
Nincs a használt tárolási kapacitást az operációs rendszer, a helyi naplózás, a memóriaképek és a többi ideiglenes infrastruktúra tárolási igényeinek megfelelően. A helyi tárolási kapacitása (eszközökön és a kapacitás) a közvetlen tárolóhelyek szolgáltatás konfigurációjának kezelése alatt álló tárolóeszközök elkülönítése. A tárolóeszközök része kerül egy egyetlen készletet a tárolókapacitás, a skálázási egységben kiszolgálók számától függetlenül.

Ezek az eszközök két típusa van: cache és a kapacitás. A közvetlen tárolóhelyek késleltetve visszaírt és olvasási gyorsítótárazás gyorsítótárként használ fel. A kapacitások ilyen gyorsítótár eszközt használja, amíg nem fontos, hogy a formázott virtuális lemezek formázott, "látható" kapacitását. Ellentétben a közvetlen tárolóhelyek használata kapacitáseszközök erre a célra biztosít a felügyelt adatok "otthoni régiója".

Az Azure Stack-infrastruktúra közvetlenül foglalja le és kezeli az összes tárolási kapacitást. Az operátor konfigurációja, a foglalási, egyszerű kapacitásbővítést téve lehetővé megállapításánál, hogy nem szükséges. Az Azure Stack automatizálja az alábbi tervezési döntéseket hozhat, során a kezdeti telepítése és a központi telepítés vagy az egyszerű kapacitásbővítést téve lehetővé a megoldás követelményeinek igazítása. Az Azure Stack szempont, rugalmasságát, fenntartott kapacitás újraépíteni és egyéb részletek figyelembe veszi, a terv részeként. 

Operátorok választhatnak, hogy akár egy *minden flash* vagy egy *hibrid* tárolási konfigurációt:

![Az Azure storage kapacitásának megtervezése ábrája](media/azure-stack-capacity-planning/storage.png)

Az összes flash konfigurációja, az a konfiguráció lehet egy kétrétegű és a egy egyetlen-réteg konfigurációjához. Ha a konfiguráció egyrétegű, minden kapacitáseszközök azonos típusú (például NVMe vagy SATA SSD vagy SAS SSD), és gyorsítótár-eszközök nem használhatók. Egy kétrétegű minden flash konfiguráció, a tipikus konfigurációja NVMe, mint a gyorsítótár-eszközként, és ezután vagy SATA vagy a kapacitás eszközként SAS SSD-kkel.

A hibrid, kétrétegű konfiguráció, a gyorsítótár NVMe, a SATA vagy SAS SSD közötti választást, és a kapacitás HDD. 

Röviden összefoglalva a közvetlen tárolóhelyek és az Azure Stack tárolási konfigurációt a következőképpen történik:
- Egy, a közvetlen tárolóhelyek készlet skálázási egység (az összes tárolóeszköz egyetlen belül vannak konfigurálva).
- A legjobb teljesítmény és rugalmasság három példányt tükör virtuális lemezek jönnek létre.
- Virtuális lemezek vannak formázva, egy ReFS fájlrendszert.
- Virtuális lemez kapacitása kiszámítása és lefoglalt úgy, hogy hagyja meg az adatok kapacitás a készletben lévő szabad kapacitás egy eszköz mennyiségét. Ez megegyezik a Kiszolgálónként egy kapacitás-meghajtó.
- Minden egyes ReFS fájlrendszeren a BitLocker az inaktív adatok titkosítása engedélyezve van. 

A virtuális lemezek jönnek létre automatikusan, és a kapacitás, a következők:

|Name (Név)|A számítási kapacitás|Leírás|
|-----|-----|-----|
|Helyi vagy rendszerindító eszköz|340 GB-os minimális<sup>1</sup>|Egyes kiszolgálói tárhelyet operációsrendszer-lemezképek, a "local" infrastruktúra virtuális gépein.|
|Infrastruktúra|3,5 TB|Minden Azure Stack-infrastruktúra használata.|
|VmTemp|Lásd alább<sup>2</sup>|Bérlői virtuális gépeket egy ideiglenes lemez csatolva van, és ezek a virtuális lemezek tárolja az adatokat.|
|ACS|Lásd alább <sup>3</sup>|Az Azure a konzisztens tárolási kapacitás karbantartáshoz, blobok, táblák és üzenetsorok.|

<sup>1</sup> minimális tárolási kapacitást az Azure Stack megoldás partner szükséges.

<sup>2</sup> a bérlői virtuális gép ideiglenes lemezek használt virtuális lemez mérete számítjuk ki, hogy a kiszolgáló fizikai memória arány. Ideiglenes lemez a virtuális géphez rendelt fizikai memória arány. A hozzárendelés kész "ideiglenes lemez" tárolás az Azure Stackben rögzíti a legtöbb használati esetek, de nem feltétlenül tudja kielégíteni minden ideiglenes lemez tárolási igényeinek megfelelően. Arány, az ideiglenes tároló elérhetővé tétele, és nem használja fel a tárolási kapacitás, a megoldás csak ideiglenes lemez kapacitás többsége között. A skálázási egységben kiszolgálónként egy ideiglenes tárolólemez jön létre. Az ideiglenes tárolási kapacitása 10 százaléka, a teljes elérhető kapacitást a tárolókészletben, a skálázási egység nem nagyobb legyen. A számítása a következőképpen fog kinézni a következő példa:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> az ACS által használni létrehozott virtuális lemezek a fennmaradó kapacitás egy egyszerű osztás. Feljegyzett, az összes virtuális lemez egy háromutas tükrözött, és egy kapacitási meghajtó tekinthető meg minden olyan kiszolgáló kapacitásának le nem foglalt. A korábban felsorolt különböző virtuális lemezek vannak lefoglalva az első, és a fennmaradó kapacitás az ACS tároló virtuális lemezeit használja.


## <a name="next-steps"></a>További lépések
További információ a [az Azure Stack Capacity Planner](azure-stack-capacity-planner.md).
