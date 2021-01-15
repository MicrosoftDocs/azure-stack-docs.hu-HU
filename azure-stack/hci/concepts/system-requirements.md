---
title: Az Azure Stack HCI rendszerkövetelményei
description: Kiszolgálók, tárolók és hálózatkezelési összetevők kiválasztása Azure Stack HCI-hez.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 01/14/2021
ms.openlocfilehash: 3b29dfb35bad91ef02feebc22255b89116da1505
ms.sourcegitcommit: 8526f642ef859b0006c3991d966f93608a87288a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2021
ms.locfileid: "98243459"
---
# <a name="system-requirements-for-azure-stack-hci"></a>Az Azure Stack HCI rendszerkövetelményei

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ez a témakör a kiszolgálók, a tárolók és a hálózatkezelés rendszerkövetelményeit vizsgálja Azure Stack HCI-hez.

## <a name="server-requirements"></a>Kiszolgálókövetelmények

Egy standard Azure Stack HCI-fürthöz legalább két kiszolgáló és legfeljebb 16 kiszolgáló szükséges; a fürtök azonban a fürtök használatával kombinálhatók több száz csomópontból álló HCI-platform létrehozásához.

Tartsa szem előtt az alábbiakat a különböző típusú Azure Stack HCI-példányok esetén:

- A kifeszített fürtök két különálló helyen kell telepíteni a kiszolgálókat. A helyek lehetnek különböző országokban, különböző városokban, különböző szinteken vagy különböző helyiségekben. A kifeszített fürthöz legalább 4 kiszolgáló szükséges (2 hely) és legfeljebb 16 kiszolgáló (8/hely).

- Azt javasoljuk, hogy minden kiszolgáló azonos gyártó és modell legyen, a 64-bites Intel Nehalem-fokozat, az AMD EPYC-fokozat vagy a később kompatibilis processzorok használata második szintű címfordítással (LÉC). Az Intel Optane DC állandó memóriájának támogatásához egy második generációs Intel Xeon skálázható processzor szükséges. A processzoroknak legalább 1,4 GHz-es értékkel kell rendelkezniük, és kompatibilisnek kell lenniük az x64-es utasításkészlet.

- Győződjön meg arról, hogy a kiszolgálók legalább 32 GB RAM memóriával vannak ellátva a kiszolgálói operációs rendszer, a virtuális gépek és más alkalmazások vagy munkaterhelések számára. Továbbá engedélyezze a gyorsítótár-meghajtó kapacitásának 4 GB-os RAM-ot (TB) az egyes kiszolgálókon Közvetlen tárolóhelyek metaadatokhoz.

- Ellenőrizze, hogy a virtualizálási támogatás be van-e kapcsolva a BIOS-ban vagy az UEFI-ben:
    - Hardveres támogatású virtualizálás. Ez olyan processzorokban érhető el, amelyek tartalmazzák a virtualizációs lehetőséget, különösen az Intel Virtualization Technology (Intel VT) vagy az AMD Virtualization (AMD-V) technológiával rendelkező processzorokat.
    - Az Adatvégrehajtás megakadályozása (DEP) hardveres támogatással funkciónak rendelkezésre kell állnia és elérhetőnek kell lennie. Az Intel Systems esetében ez az XD bit (végrehajtás letiltása bit). Az AMD-rendszerek esetében ez az NX bit (nincs végrehajtás bit).

- Használhatja a Windows Server által támogatott bármely rendszerindító eszközt, amely [már tartalmazza a SATADOM](https://cloudblogs.microsoft.com/windowsserver/2017/08/30/announcing-support-for-satadom-boot-drives-in-windows-server-2016/). A RAID 1 tükrözés **nem** szükséges, de a rendszerindítás támogatott. A 200 GB-os minimális méret ajánlott.

- A Hyper-V szolgáltatással kapcsolatos további követelményekért lásd: a [Windows Server rendszeren futó Hyper-v rendszerkövetelményei](/windows-server/virtualization/hyper-v/system-requirements-for-hyper-v-on-windows).

## <a name="storage-requirements"></a>Tárolási követelmények

Azure Stack HCI közvetlenül csatlakoztatott SATA-, SAS-, NVMe-vagy állandó memória-meghajtókkal működik, amelyek fizikailag csak egy kiszolgálóhoz csatlakoznak.

A legjobb eredmények érdekében tartsa be a következőket:

- A fürt minden kiszolgálójának azonos típusú meghajtóval és azonos számú típussal kell rendelkeznie. Emellett ajánlott (de nem kötelező), hogy a meghajtók mérete és típusa azonos legyen. A meghajtók belsőek lehetnek a kiszolgálón, vagy egy olyan külső bekerítésen, amely csak egy kiszolgálóhoz csatlakozik. További információért lásd a [meghajtó-szimmetria szempontjait](drive-symmetry-considerations.md).

- A fürt minden kiszolgálójának dedikált kötetekkel kell rendelkeznie a naplókhoz, és legalább annyit kell tennie, mint az adattárolási tároló. A kifeszített fürtökhöz legalább két kötet szükséges: egyet a replikált adatként, egyet pedig a naplózási adatként.

- A bővítőhelyek hozzárendeléséhez és azonosításához SCSI ház-szolgáltatások (SES) szükségesek. Minden külső bekerítésnek egyedi azonosítót (egyedi azonosítót) kell tartalmaznia. 

   > [!IMPORTANT]
   > **nem támogatott:** RAID-vezérlő kártyák vagy SAN (szálcsatorna, iSCSI, FCoE) tárolás, megosztott SAS-házak több kiszolgálóhoz csatlakoztatva, vagy a többutas IO (MPIO) bármilyen formája, ahol a meghajtók több elérési úttal is elérhetők. Az HBA-kártyáknak egyszerű átmenő üzemmódot kell alkalmazniuk.

## <a name="networking-requirements"></a>Hálózati követelmények

Egy Azure Stack HCI-fürtnek megbízható, nagy sávszélességű, kis késleltetésű hálózati kapcsolatra van szüksége az egyes kiszolgálói csomópontok között.

- Győződjön meg arról, hogy legalább egy hálózati adapter elérhető, és dedikált a fürt felügyeletéhez.
- Győződjön meg arról, hogy a hálózati fizikai kapcsolók úgy vannak konfigurálva, hogy engedélyezzék a forgalom használatát minden olyan VLAN-on, amelyet használni fog.

A gazdagép hálózatkezelési szempontjait és követelményeit lásd: a [gazdagép hálózati követelményei](host-network-requirements.md).

## <a name="software-defined-networking-sdn-requirements"></a>A szoftver által meghatározott hálózatkezelési (SDN) követelmények

Amikor Azure Stack HCI-fürtöt hoz létre a Windows felügyeleti központban, lehetősége van a hálózati vezérlő üzembe helyezésére a szoftveresen definiált hálózatkezelés (SDN) engedélyezéséhez. Ha az SDN-t Azure Stack HCI-ben szeretné használni:

- Győződjön meg arról, hogy a gazdagép-kiszolgálók legalább 50-100 GB szabad területtel rendelkeznek a hálózati vezérlő virtuális gépei létrehozásához.

- A hálózati vezérlő virtuális gépek létrehozásához át kell másolnia a Azure Stack HCI operációs rendszer virtuális merevlemezét (VHD) a fürt első csomópontjára. A virtuális merevlemezt a [Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview) használatával, vagy a [Convert-WindowsImage](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f) parancsfájl futtatásával alakíthatja át egy VHD-fájlba.

Az SDN Azure Stack HCI-ben való használatának előkészítésével kapcsolatos további információkért lásd: [a szoftver által meghatározott hálózati infrastruktúra megtervezése](plan-software-defined-networking-infrastructure.md) és [a hálózati vezérlő üzembe helyezésének megtervezése](../concepts/network-controller.md).

   > [!NOTE]
   > Az SDN nem támogatott a kiterjesztett (többhelyes) fürtökön.

### <a name="sdn-hardware-requirements"></a>SDN hardver-követelmények

Ez a szakasz a hálózati adapterek és fizikai kapcsolók hálózati hardverekre vonatkozó követelményeit ismerteti az SDN-környezet tervezésekor.

#### <a name="network-interface-cards-nics"></a>Hálózati adapterek (NIC)

A Hyper-V-gazdagépek és a tároló-gazdagépek által használt hálózati adapterek speciális képességeket igényelnek a legjobb teljesítmény eléréséhez.

A távoli közvetlen memória-hozzáférés (RDMA) egy kernel-megkerülési módszer, amely lehetővé teszi nagy mennyiségű adat átvitelét a gazda CPU használata nélkül, így a CPU más munkákat is elvégezhet. A Switch beágyazott összevonása (SET) egy másik hálózati adapterek összevonására szolgáló megoldás, amelyet a Hyper-V és az SDN-verem közé tartozó környezetekben használhat. A SET a hálózati adapterek összevonásának funkcióit integrálja a Hyper-V virtuális kapcsolóba.

További információkért lásd: [távoli közvetlen memória-hozzáférés (RDMA) és Switch Embedded Teaming (set)](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).

Ahhoz, hogy a bérlői virtuális hálózati forgalom a VXLAN vagy NVGRE beágyazási fejlécek által okozott terhelést figyelembe lehessen venni, a 2. rétegbeli háló (kapcsolók és gazdagépek) maximális átviteli egységének (MTU) értékének 1674 bájtnál nagyobbnak vagy azzal egyenlőnek kell lennie, \( beleértve a 2. rétegbeli Ethernet-fejléceket \) .

Az új *EncapOverhead* speciális adaptert támogató hálózati adapterek automatikusan az MTU-t a hálózati vezérlő gazdagépének ügynökén keresztül állíthatják be. Az új *EncapOverhead* -kulcsszót nem támogató hálózati adapterek esetében az MTU-méretet manuálisan kell beállítani az egyes fizikai gazdagépeken a *JumboPacket* \( vagy azzal egyenértékű \) kulcsszó használatával.

#### <a name="switches-and-routers"></a>Kapcsolók és útválasztók

Az SDN-környezet fizikai kapcsolójának és útválasztójának kiválasztásakor győződjön meg arról, hogy az a következő funkciókat támogatja:
- Switchport MTU-beállítások megadása \( kötelező\)
- Az MTU értéke >= 1674 bájt, \( beleértve a L2-Ethernet fejlécet\)
- L3 protokollok \( szükségesek\)
- Azonos árú, több útvonalos (ECMP) Útválasztás
- BGP \( IETF RFC 4271- \) \- alapú ECMP

A megvalósításoknak támogatniuk kell a következő IETF-szabványokban szereplő kötelező utasításokat:
- RFC 2545: [BGP-4 többprotokollos bővítmény IPv6 Inter-Domain útválasztáshoz](https://tools.ietf.org/html/rfc2545)
- RFC 4760: [többprotokollos bővítmények a BGP-4-hez](https://tools.ietf.org/html/rfc4760)
- RFC 4893: [BGP-támogatás négy oktettnél](https://tools.ietf.org/html/rfc4893)
- RFC 4456: [BGP Route Reflection: alternatíva a teljes Mesh belső BGP (IBGP)](https://tools.ietf.org/html/rfc4456)
- RFC 4724: [kecses újraindítási mechanizmus BGP-hez](https://tools.ietf.org/html/rfc4724)

A következő címkézési protokollok szükségesek:
- VLAN – különböző típusú forgalom elkülönítése
- 802.1 q törzs

A következő elemek biztosítják a hivatkozás vezérlőelemet:
- A szolgáltatás szolgáltatásminőség-ellenőrzésének minősége \( \) \( csak a RoCE használata esetén szükséges\)
- Továbbfejlesztett forgalom kiválasztásának \( 802.1 Qaz\)
- Prioritáson alapuló Flow Control (PFC) \( 802.1 p/Q és 802.1 Qbb\)

A következő elemek biztosítják a rendelkezésre állást és a redundanciát:
- Kapcsoló rendelkezésre állása (kötelező)
- Az átjáró függvények végrehajtásához egy nagy rendelkezésre állású útválasztó szükséges. Ezt megadhatja egy többplatformos switch\router vagy olyan technológiával, mint a Virtual router redundancia protokoll (VRRP).

### <a name="domain-requirements"></a>Tartományi követelmények

A Azure Stack HCI esetében nincsenek speciális tartományi működési szintű követelmények – csak a tartományvezérlőhöz tartozó operációsrendszer-verzió, amely továbbra is támogatott. Javasoljuk, hogy a Active Directory Lomtár szolgáltatást általános ajánlott eljárásként kapcsolja be, ha még nem tette meg. További információ: [Active Directory tartományi szolgáltatások Overview (áttekintés](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)).

## <a name="maximum-supported-hardware-specifications"></a>Támogatott hardverek maximális száma

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

## <a name="next-steps"></a>További lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Meghajtók kiválasztása](choose-drives.md)
- [Hardverkövetelmények Közvetlen tárolóhelyek](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements)
