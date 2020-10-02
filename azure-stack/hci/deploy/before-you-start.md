---
title: Azure Stack HCI üzembe helyezése előtt
description: Felkészülés a Azure Stack HCI üzembe helyezésére.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/01/2020
ms.openlocfilehash: 8a4c8557fe708535bfdde383ef30dd78395b1c01
ms.sourcegitcommit: 09572e1442c96a5a1c52fac8ee6b0395e42ab77d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91625872"
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

### <a name="software-defined-networking-requirements"></a>A szoftver által definiált hálózati követelmények

Amikor Azure Stack HCI-fürtöt hoz létre a Windows felügyeleti központban, lehetősége van a hálózati vezérlő üzembe helyezésére a szoftveresen definiált hálózatkezelés (SDN) engedélyezéséhez. Ha az SDN-t Azure Stack HCI-ben szeretné használni:

- Győződjön meg arról, hogy a gazdagép-kiszolgálók legalább 50-100 GB szabad területtel rendelkeznek a hálózati vezérlő virtuális gépei létrehozásához.

- A hálózati vezérlő virtuális gépek létrehozásához át kell másolnia a Azure Stack HCI operációs rendszer virtuális merevlemezét (VHD) a fürt első csomópontjára. A virtuális merevlemezt a [Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview) használatával, vagy a [Convert-WindowsImage](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f) parancsfájl futtatásával alakíthatja át egy VHD-fájlba.

Az SDN Azure Stack HCI-ben való használatának előkészítésével kapcsolatos további információkért lásd: [a szoftver által meghatározott hálózati infrastruktúra megtervezése](../concepts/plan-software-defined-networking-infrastructure.md) és [a hálózati vezérlő üzembe helyezésének megtervezése](../concepts/network-controller.md).

### <a name="domain-requirements"></a>Tartományi követelmények

A Azure Stack HCI esetében nincsenek speciális tartományi működési szintű követelmények – csak a tartományvezérlőhöz tartozó operációsrendszer-verzió, amely továbbra is támogatott. Javasoljuk, hogy a Active Directory Lomtár szolgáltatást általános ajánlott eljárásként kapcsolja be, ha még nem tette meg.

### <a name="interconnect-requirements-between-nodes"></a>Összekötői követelmények csomópontok között

Ez a szakasz a különböző hálózati követelményeket tárgyalja egy adott hely kiszolgálói csomópontjai között, az úgynevezett kapcsolatokat. A kapcsolót vagy a kapcsoló nélküli csomópont-összekapcsolást lehet használni és támogatni:

- **Váltás:** A kiszolgálói csomópontok általában hálózati kapcsolókat használó Ethernet-hálózatokon keresztül kapcsolódnak egymáshoz. A kapcsolókat megfelelően kell konfigurálni a sávszélesség és a hálózat típusának kezeléséhez. Ha a RoCE protokollt implementáló RDMA használ, a hálózati eszköz és a kapcsoló konfigurációja fontos.
- **Váltás:** A kiszolgáló-csomópontok a közvetlen Ethernet-kapcsolatok kapcsoló nélkül is összekapcsolhatók. Ebben az esetben minden egyes kiszolgáló-csomópontnak közvetlen kapcsolattal kell rendelkeznie az ugyanazon a helyen található többi fürtcsomóponton.

#### <a name="interconnects-for-2-3-node-clusters"></a>2-3 csomópontos fürtök összekapcsolása

A két vagy három csomóponttal rendelkező egyhelyes fürtök *minimális* összekötési követelményei. Ezek az egyes kiszolgálók csomópontjaira vonatkoznak:

- Egy vagy több 1 GB-os hálózati adapter, amelyet a felügyeleti funkciókhoz kíván használni
- Egy vagy több 10 GB (vagy gyorsabb) hálózati kártya a tárolási és a számítási feladatok forgalmához
- A redundancia és a teljesítmény érdekében ajánlott két vagy több hálózati kapcsolat az egyes csomópontok között

#### <a name="interconnects-for-4-node-and-greater-clusters"></a>4 csomópontos és nagyobb fürtökkel való kapcsolat

Ezek a legalább négy csomóponttal rendelkező fürtök és a nagy teljesítményű fürtök esetében *minimálisan* szükséges összekötők. Ezek az egyes kiszolgálók csomópontjaira vonatkoznak:

- Egy vagy több 1 GB-os hálózati adapter, amelyet a felügyeleti funkciókhoz kíván használni.
- Egy vagy több 25 GB (vagy gyorsabb) hálózati adapter a tárolási és a számítási feladatok forgalmához. A redundancia és a teljesítmény érdekében két vagy több hálózati kapcsolatot ajánlunk.
- Távoli közvetlen memória-hozzáférést (RDMA) támogató hálózati kártyák: iWARP (ajánlott) vagy RoCE.

### <a name="site-to-site-requirements-stretched-cluster"></a>Helyek közötti követelmények (kiterjesztett fürt)

Ha a helyek között csatlakozik a kibővített fürtökhöz, az egyes helyeken lévő összekapcsolási követelmények továbbra is érvényben vannak, és további tárolási replika és Hyper-V élő áttelepítési forgalomra vonatkozó követelményeket kell figyelembe venni:

- Legalább egy 1 GB-os RDMA vagy Ethernet/TCP-kapcsolat a helyek közötti szinkron replikáláshoz. A 25 GB-os RDMA-kapcsolatok használata javasolt.
- A helyek közötti, elegendő sávszélességgel rendelkező hálózat, amely az I/O-írási munkaterhelést, valamint a szinkron replikálás átlagos 5ms vagy alacsonyabb késését tartalmazza. Az aszinkron replikációhoz nem tartozik késési javaslat.
- Ha egyetlen kapcsolatot használ a helyek között, állítsa be a tárolási replika SMB sávszélesség-korlátait a PowerShell használatával. További információ: [set-SmbBandwidthLimit](/powershell/module/smbshare/set-smbbandwidthlimit).
- Ha több kapcsolatot használ a helyek között, válassza a kapcsolatok közötti adatforgalmat. Tegyük fel például, hogy a tárolási replika forgalmát egy különálló hálózaton helyezi el, mint a Hyper-V élő áttelepítési forgalom a PowerShell használatával. További információ: [set-SRNetworkConstraint](/powershell/module/storagereplica/set-srnetworkconstraint).

### <a name="network-port-requirements"></a>Hálózati portokra vonatkozó követelmények

Győződjön meg arról, hogy a megfelelő hálózati portok nyitva vannak az összes kiszolgáló-csomópont között egy helyen és a helyek között (a kifeszített fürtök esetében). Szüksége lesz a megfelelő tűzfal-és útválasztó-szabályokra az ICMP, az SMB (445-es port, a 5445-es port, az SMB Direct esetében) és a WS-MAN (port 5985) kétirányú forgalom engedélyezéséhez a fürt összes kiszolgálója között.

Ha a fürt létrehozásához a Windows felügyeleti központban a fürt létrehozása varázslót használja, a varázsló automatikusan megnyitja a megfelelő tűzfal-portokat a fürt minden kiszolgálóján a feladatátvételi fürtszolgáltatás, a Hyper-V és a tárolási replika számára. Ha az egyes kiszolgálókon eltérő szoftveres tűzfalat használ, nyissa meg a következő portokat:

#### <a name="failover-clustering-ports"></a>Feladatátvételi fürtszolgáltatás portjai

- ICMPv4 és ICMPv6
- 445-es TCP-port
- Dinamikus RPC-portok
- 135-es TCP-port
- 137-es TCP-port
- 3343-es TCP-port
- 3343-es UDP-port

#### <a name="hyper-v-ports"></a>Hyper-V-portok

- 135-es TCP-port
- 80-es TCP-port (HTTP-kapcsolat)
- 443-es TCP-port (HTTPS-kapcsolat)
- 6600-es TCP-port
- 2179-es TCP-port
- Dinamikus RPC-portok
- RPC végpontleképező
- 445-es TCP-port

#### <a name="storage-replica-ports-stretched-cluster"></a>Tárolási replika portjai (kifeszített fürt)

- 445-es TCP-port
- TCP 5445 (ha iWarp RDMA használ)
- 5985-es TCP-port
- ICMPv4 és ICMPv6 (ha test-SRTopology-t használ)

Előfordulhat, hogy a fentiekben nem szereplő további portok szükségesek. Ezek az alapszintű Azure Stack HCI-funkciók portjai.

### <a name="network-switch-requirements"></a>Hálózati kapcsolókra vonatkozó követelmények

Ez a szakasz a Azure Stack HCI-mel használt fizikai kapcsolókra vonatkozó követelményeket határozza meg. Ezek a követelmények azokat az iparági specifikációkat, szervezeti szabványokat és protokollokat sorolja fel, amelyek az összes Azure Stack HCI-telepítéshez kötelezőek. Ha másként nincs jelezve, a standard utolsó aktív (nem felülírt) verzióját kell megadnia.

Ezek a követelmények biztosítják a Azure Stack HCI-fürtök csomópontjai közötti megbízható kommunikációt. A csomópontok közötti megbízható kommunikáció kritikus fontosságú. A Azure Stack HCI szükséges megbízhatósági szintjének biztosításához a következő kapcsolók szükségesek:

- Meg kell felelnie a vonatkozó iparági előírásoknak, szabványoknak és protokolloknak
- A kapcsoló által támogatott specifikációk, szabványok és protokollok láthatóságának biztosítása
- Adja meg azokat az információkat, amelyeken engedélyezve vannak a képességek

Ha a kapcsoló támogatja a következőt, ügyeljen arra, hogy megkérdezze a kapcsoló gyártóját:

#### <a name="standard-ieee-8021q"></a>Standard: IEEE 802.1 Q

Az Ethernet-kapcsolóknak meg kell felelniük a VLAN-okat definiáló IEEE 802.1 Q specifikációnak. A VLAN-ok a Azure Stack HCI számos aspektusához szükségesek, és minden esetben szükségesek.

#### <a name="standard-ieee-8021qbb"></a>Standard: IEEE 802.1 Qbb

Az Ethernet-kapcsolóknak meg kell felelniük az IEEE 802.1 Qbb specifikációnak, amely meghatározza a prioritási folyamat vezérlését (PFC). A PFC megadása szükséges az adatközpont-áthidalás (DCB) használata esetén. Mivel a DCB használható mind a RoCE, mind a iWARP RDMA-forgatókönyvben, minden esetben 802.1 Qbb szükséges. Legalább három szolgáltatási (CoS) prioritásra van szükség a kapcsolói képességek vagy a portok sebességének visszalépése nélkül.

#### <a name="standard-ieee-8021qaz"></a>Standard: IEEE 802.1 Qaz

Az Ethernet-kapcsolóknak meg kell felelniük az IEEE 802.1 Qaz specifikációnak, amely meghatározza a bővített átvitel kiválasztása (ETS) beállítást. Az ETS-t a DCB használata esetén kell megadni. Mivel a DCB használható mind a RoCE, mind a iWARP RDMA-forgatókönyvben, minden esetben 802.1 Qaz szükséges. A kapcsolók vagy a portok sebességének visszalépése nélkül legalább három CoS-prioritás szükséges.

#### <a name="standard-ieee-8021ab"></a>Standard: IEEE 802.1 AB

Az Ethernet-kapcsolóknak meg kell felelniük az IEEE 802.1 AB specifikációnak, amely meghatározza a link Layer Discovery protokollt (LLDP). A LLDP szükséges ahhoz, hogy a Windows felderítse a kapcsolók konfigurációját. A LLDP-típus (TLVs) konfigurációjának dinamikusan engedélyezettnek kell lennie. Ezek a kapcsolók nem igényelnek további konfigurálást.

Például, ha a 3. altípust engedélyezi, a 3-as érték automatikusan Hirdessen minden olyan VLAN-t, 802,1 amely elérhető a kapcsoló portjain

#### <a name="tlv-requirements"></a>TLV-követelmények

A LLDP lehetővé teszi, hogy a szervezetek definiálják és kódolják saját egyéni TLVs. Ezeket Szervezetian meghatározott TLVs nevezzük. Minden Szervezetian megadott TLVs LLDP TLV típusú, 127-as értékkel kezdődik. Az alábbi táblázat azt mutatja be, hogy mely szervezeti specifikus egyéni TLV (TLV típusú 127) altípusok szükségesek, és melyek nem kötelezőek:

|Condition (Állapot)|Szervezet|TLV altípus|
|-|-|-|
|Kötelező|IEEE 802,1|VLAN neve (altípus = 3)|
|Kötelező|IEEE 802,3|Keret maximális mérete (altípus = 4)|
|Választható|IEEE 802,1|Port VLAN-azonosítója (altípus = 1)|
|Választható|IEEE 802,1|Port és protokoll VLAN-azonosítója (altípus = 2)|
|Választható|IEEE 802,1|Csatolás összesítése (altípus = 7)|
|Választható|IEEE 802,1|Torlódási értesítés (altípus = 8)|
|Választható|IEEE 802,1|ETS-konfiguráció (altípus = 9)|
|Választható|IEEE 802,1|ETS-javaslat (altípus = A)|
|Választható|IEEE 802,1|PFC-konfiguráció (altípus = B)|
|Választható|IEEE 802,1|EVB (altípus = D)|
|Választható|IEEE 802,3|Csatolás összesítése (altípus = 3)|

> [!NOTE]
> Előfordulhat, hogy a felsorolt választható funkciók némelyike a jövőben szükséges.

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

## <a name="next-steps"></a>További lépések

A következő cikkből megtudhatja, hogyan helyezheti üzembe a Azure Stack HCI operációs rendszert.
> [!div class="nextstepaction"]
> [A Azure Stack HCI operációs rendszer üzembe helyezése](operating-system.md)
