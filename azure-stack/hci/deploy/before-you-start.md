---
title: Azure Stack HCI üzembe helyezése előtt
description: Felkészülés a Azure Stack HCI üzembe helyezésére.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/14/2020
ms.openlocfilehash: 4ff495aba1f46824a6ab47c95601687402d24edb
ms.sourcegitcommit: 8122672409954815e472a5b251bb7319fab8f951
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92060106"
---
# <a name="before-you-deploy-azure-stack-hci"></a>Azure Stack HCI üzembe helyezése előtt

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ebben a útmutatóban a következőket sajátíthatja el:

- Annak megállapítása, hogy a hardver megfelel-e a standard (egy hely) vagy a kifeszített (Kéthelyes) Azure Stack HCI-fürtök alapkövetelményeinek
- Győződjön meg arról, hogy nem lépi túl a támogatott hardverek maximális számát
- A sikeres üzembe helyezéshez szükséges információk összegyűjtése
- A Windows felügyeleti központ telepítése felügyeleti számítógépen vagy kiszolgálón

Az Azure Kubernetes Service Azure Stack HCI-követelményekkel kapcsolatban lásd: az [AK-követelmények Azure stack HCI](../../aks-hci/overview.md#what-you-need-to-get-started)-ben.

## <a name="determine-hardware-requirements"></a>Hardverkövetelmények meghatározása

A Microsoft azt javasolja, hogy a partnereinktől kapott hitelesített Azure Stack HCI hardver/szoftver megoldást. Ezeket a megoldásokat a kompatibilitási és megbízhatósági megoldások kialakításához, összeállításához és ellenőrzéséhez kell beállítani, hogy a rendszer gyorsan elkészüljön. Győződjön meg arról, hogy a használt rendszerek, összetevők, eszközök és illesztőprogramok a Windows Server Catalog szolgáltatásban a Windows Server 2019 Certified minősítéssel rendelkeznek. Látogasson el az [Azure stack HCI Solutions](https://azure.microsoft.com/overview/azure-stack/hci) webhelyére az ellenőrzött megoldásokhoz.

### <a name="server-requirements"></a>Kiszolgálókövetelmények

- Egy standard Azure Stack HCI-fürthöz legalább 2 kiszolgáló és legfeljebb 16 kiszolgáló szükséges; a fürtök azonban a fürtök használatával kombinálhatók több száz csomópontból álló HCI-platform létrehozásához.
- A kifeszített fürtök két különálló helyen kell telepíteni a kiszolgálókat. A helyek lehetnek különböző országokban, különböző városokban, különböző szinteken vagy különböző helyiségekben. A kifeszített fürthöz legalább 4 kiszolgáló szükséges (2 hely) és legfeljebb 16 kiszolgáló (8/hely).
- Azt javasoljuk, hogy minden kiszolgáló azonos gyártó és modell legyen, a 64-bites Intel Nehalem-fokozat, az AMD EPYC-fokozat vagy a később kompatibilis processzorok használata második szintű címfordítással (LÉC). Az Intel Optane DC állandó memóriájának támogatásához egy második generációs Intel Xeon skálázható processzor szükséges. A processzoroknak legalább 1,4 GHz-es értékkel kell rendelkezniük, és kompatibilisnek kell lenniük az x64-es utasításkészlet.
- Győződjön meg arról, hogy a kiszolgálók legalább 32 GB RAM memóriával vannak ellátva a kiszolgálói operációs rendszer, a virtuális gépek és más alkalmazások vagy munkaterhelések számára. Továbbá engedélyezze a gyorsítótár-meghajtó kapacitásának 4 GB-os RAM-ot (TB) az egyes kiszolgálókon Közvetlen tárolóhelyek metaadatokhoz.
- Ellenőrizze, hogy a virtualizálási támogatás be van-e kapcsolva a BIOS-ban vagy az UEFI-ben:
    - Hardveres támogatású virtualizálás. Ez olyan processzorokban érhető el, amelyek tartalmazzák a virtualizációs lehetőséget, különösen az Intel Virtualization Technology (Intel VT) vagy az AMD Virtualization (AMD-V) technológiával rendelkező processzorokat.
    - Az Adatvégrehajtás megakadályozása (DEP) hardveres támogatással funkciónak rendelkezésre kell állnia és elérhetőnek kell lennie. Az Intel Systems esetében ez az XD bit (végrehajtás letiltása bit). Az AMD-rendszerek esetében ez az NX bit (nincs végrehajtás bit).
- Használhatja a Windows Server által támogatott bármely rendszerindító eszközt, amely [már tartalmazza a SATADOM](https://cloudblogs.microsoft.com/windowsserver/2017/08/30/announcing-support-for-satadom-boot-drives-in-windows-server-2016/). A RAID 1 tükrözés **nem** szükséges, de a rendszerindítás támogatott. A 200 GB-os minimális méret ajánlott.
- A Hyper-V szolgáltatással kapcsolatos további követelményekért lásd: a [Windows Server rendszeren futó Hyper-v rendszerkövetelményei](/windows-server/virtualization/hyper-v/system-requirements-for-hyper-v-on-windows).

### <a name="networking-requirements"></a>Hálózati követelmények

Egy Azure Stack HCI-fürtnek megbízható, nagy sávszélességű, kis késleltetésű hálózati kapcsolatra van szüksége az egyes kiszolgálói csomópontok között. Ellenőrizze a következőket:

- Győződjön meg arról, hogy legalább egy hálózati adapter elérhető, és dedikált a fürt felügyeletéhez.
- Győződjön meg arról, hogy a hálózati fizikai kapcsolók úgy vannak konfigurálva, hogy engedélyezzék a forgalom használatát minden olyan VLAN-on, amelyet használni fog.

Több típusú kommunikáció zajlik a kiszolgálói csomópontok között:

- Fürt kommunikációja (node joins, cluster Updates, Registry Updates)
- Fürt Szívverései
- Fürt megosztott kötete (CSV) átirányított forgalom
- Virtuális gépek élő áttelepítési forgalma

A Közvetlen tárolóhelyek további hálózati forgalmat is figyelembe kell venni:

- Storage Bus Layer (SBL) – egységek vagy adategységek a csomópontok között
- Állapot – objektumok figyelése és kezelése (csomópontok, meghajtók, hálózati kártyák, fürtszolgáltatás)

A többhelyes fürtök esetében a helyek között további tárolási replika forgalom is áramlik. A Storage Bus Layer (SBL) és a Fürt megosztott kötete (CSV) forgalom nem megy át a helyek között, csak az egyes helyeken belüli kiszolgálói csomópontok között.

A gazdagép hálózatkezelésének tervezési szempontjait és követelményeit lásd: [gazdagép hálózatkezelésének Megtervezése Azure stack HCI](../concepts/plan-host-networking.md)számára.

### <a name="software-defined-networking-requirements"></a>A szoftver által definiált hálózati követelmények

Amikor Azure Stack HCI-fürtöt hoz létre a Windows felügyeleti központban, lehetősége van a hálózati vezérlő üzembe helyezésére a szoftveresen definiált hálózatkezelés (SDN) engedélyezéséhez. Ha az SDN-t Azure Stack HCI-ben szeretné használni:

- Győződjön meg arról, hogy a gazdagép-kiszolgálók legalább 50-100 GB szabad területtel rendelkeznek a hálózati vezérlő virtuális gépei létrehozásához.

- A hálózati vezérlő virtuális gépek létrehozásához át kell másolnia a Azure Stack HCI operációs rendszer virtuális merevlemezét (VHD) a fürt első csomópontjára. A virtuális merevlemezt a [Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview) használatával, vagy a [Convert-WindowsImage](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f) parancsfájl futtatásával alakíthatja át egy VHD-fájlba.

Az SDN Azure Stack HCI-ben való használatának előkészítésével kapcsolatos további információkért lásd: [a szoftver által meghatározott hálózati infrastruktúra megtervezése](../concepts/plan-software-defined-networking-infrastructure.md) és [a hálózati vezérlő üzembe helyezésének megtervezése](../concepts/network-controller.md).

### <a name="domain-requirements"></a>Tartományi követelmények

A Azure Stack HCI esetében nincsenek speciális tartományi működési szintű követelmények – csak a tartományvezérlőhöz tartozó operációsrendszer-verzió, amely továbbra is támogatott. Javasoljuk, hogy a Active Directory Lomtár szolgáltatást általános ajánlott eljárásként kapcsolja be, ha még nem tette meg.

### <a name="storage-requirements"></a>Tárolási követelmények

- Azure Stack HCI közvetlenül csatlakoztatott SATA-, SAS-, NVMe-vagy állandó memória-meghajtókkal működik, amelyek fizikailag csak egy kiszolgálóhoz csatlakoznak.
- A fürt minden kiszolgálójának azonos típusú meghajtóval és azonos számú típussal kell rendelkeznie. Emellett ajánlott (de nem kötelező), hogy a meghajtók mérete és típusa azonos legyen. A meghajtók belsőek lehetnek a kiszolgálón, vagy egy olyan külső bekerítésen, amely csak egy kiszolgálóhoz csatlakozik. További információért lásd a [meghajtó-szimmetria szempontjait](../concepts/drive-symmetry-considerations.md).
- A fürt minden kiszolgálójának dedikált kötetekkel kell rendelkeznie a naplókhoz, és legalább annyit kell tennie, mint az adattárolási tároló. A kifeszített fürtökhöz legalább két kötet szükséges: egyet a replikált adatként, egyet pedig a naplózási adatként.
- A bővítőhelyek hozzárendeléséhez és azonosításához SCSI ház-szolgáltatások (SES) szükségesek. Minden külső bekerítésnek egyedi azonosítót (egyedi azonosítót) kell tartalmaznia. **nem támogatott:** RAID-vezérlő kártyák vagy SAN (szálcsatorna, iSCSI, FCoE) tárolás, megosztott SAS-házak több kiszolgálóhoz csatlakoztatva, vagy a többutas IO (MPIO) bármilyen formája, ahol a meghajtók több elérési úttal is elérhetők. Az HBA-kártyáknak egyszerű átmenő üzemmódot kell alkalmazniuk.
- További segítségért tekintse meg a [meghajtók kiválasztása](../concepts/choose-drives.md) vagy a [közvetlen tárolóhelyek hardverkövetelmények](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements)című témakört.
- A kötetek és a rugalmasság elérhető lehetőségeiért tekintse meg a [kötetek tervezése közvetlen tárolóhelyek](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type) témakört.
- Ha a virtuális gépeket és a virtualizált tárolókat is telepíti, tekintse meg [a közvetlen tárolóhelyek használata a vendég virtuálisgép-fürtökben](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm)című témakört.

## <a name="review-maximum-supported-hardware-specifications"></a>A támogatott hardverek maximális specifikációjának áttekintése

Azure Stack a következő specifikációkat meghaladó HCI-telepítések nem támogatottak:

| Erőforrás                     | Maximum |
| ---------------------------- | --------|
| Fizikai kiszolgálók/fürt | 16      |
| Virtuális gépek száma gazdagépen                 | 1 024   |
| Lemezek száma virtuális gépenként (SCSI)          | 256     |
| Tárterület/fürt          | 4 PB    |
| Tárterület kiszolgálónkénti bontásban           | 400 TB  |
| Logikai processzorok egy gazdagépen  | 512     |
| RAM/gazdagép                 | 24 TB   |
| RAM virtuális gépenként                   | 12 TB (2. generációs VM) vagy 1 TB (1. generáció)|
| Virtuális processzorok egy gazdagépen  | 2048   |
| Virtuális processzorok száma virtuális GÉPENként    | 240 (2. generációs VM) vagy 64 (1. generáció)|

## <a name="gather-information"></a>Adatgyűjtés

Az üzembe helyezés előkészítéséhez a következő adatokat kell összegyűjtenie a környezettel kapcsolatban:

- **Kiszolgálók nevei:** Ismerkedjen meg a szervezete számítógépekkel, fájlokkal, elérési utakkal és egyéb erőforrásokkal kapcsolatos elnevezési házirendjeivel. Több kiszolgálót kell kiépítenie, amelyek mindegyike egyedi névvel rendelkezik.
- **Tartománynév:** Ismerkedjen meg a szervezet szabályzatával a tartománynév-és a tartományhoz való csatlakozáshoz. A kiszolgálókat a tartományhoz kell csatlakoztatnia, és meg kell adnia a tartománynevet.
- **Statikus IP-címek:** Azure Stack HCI statikus IP-címeket igényel a tárolási és a számítási feladatok (VM) forgalmához, és nem támogatja a dinamikus IP-címek hozzárendelését a DHCP-n keresztül ehhez a nagy sebességű hálózathoz. DHCP-t használhat a felügyeleti hálózati adapterhez, hacsak nem használ kettőt egy csapatban, ebben az esetben újra statikus IP-címeket kell használnia. Tekintse meg a hálózati rendszergazdát a fürt minden kiszolgálóján használni kívánt IP-címről.
- **RDMA hálózatkezelés:** Kétféle RDMA-protokoll létezik: iWarp és RoCE. Vegye figyelembe, hogy a hálózati adapterek közül az egyiket használja, és ha a RoCE is, jegyezze fel a verziót (v1 vagy v2). A RoCE a Top-of-rack switch modelljét is jegyezze fel.
- **VLAN-azonosító:** Jegyezze fel a kiszolgálók hálózati adapteréhez használni kívánt VLAN-azonosítót, ha van ilyen. Ezt a hálózati rendszergazdától tudhatja meg.
- **Helyek nevei:** A többhelyes fürtök esetében a rendszer két helyet használ a vész-helyreállításhoz. A webhelyeket Active Directory tartományi szolgáltatások használatával is beállíthatja, vagy a fürt létrehozása varázsló automatikusan beállíthatja őket. A helyek beállításával kapcsolatban forduljon a tartományi rendszergazdához. További információ: [Active Directory tartományi szolgáltatások Overview (áttekintés](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)).

## <a name="install-windows-admin-center"></a>A Windows felügyeleti központ telepítése

A Windows felügyeleti központ egy helyileg üzembe helyezett, böngészőalapú alkalmazás Azure Stack HCI kezeléséhez. A [Windows felügyeleti központ telepítésének](/windows-server/manage/windows-admin-center/deploy/install) legegyszerűbb módja a helyi felügyeleti számítógép (asztali mód), de telepítheti is a kiszolgálót (szolgáltatási mód).

Ha a Windows felügyeleti központot egy kiszolgálóra telepíti, a CredSSP igénylő feladatokat (például a fürtök létrehozását és a frissítések és bővítmények telepítését) olyan fiókkal kell megkövetelni, amely az átjáró-rendszergazdák csoport tagja a Windows felügyeleti központ kiszolgálóján. További információkért tekintse meg a [felhasználói Access Control és engedélyek konfigurálásának](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions)első két fejezetét.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan helyezheti üzembe a Azure Stack HCI operációs rendszert.
> [!div class="nextstepaction"]
> [A Azure Stack HCI operációs rendszer üzembe helyezése](operating-system.md)
