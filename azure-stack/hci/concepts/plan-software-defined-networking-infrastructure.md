---
title: Szoftveresen definiált hálózati infrastruktúra megtervezése
description: Ez a témakör a szoftveresen definiált hálózati (SDN) infrastruktúra központi telepítésének megtervezésével kapcsolatos információkat tartalmaz.
manager: grcusanz
ms.topic: conceptual
ms.assetid: ea7e53c8-11ec-410b-b287-897c7aaafb13
ms.author: anpaul
author: AnirbanPaul
ms.date: 09/11/2020
ms.openlocfilehash: 986aba8f7591239abfa5502dc2c335177df2eed1
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91899533"
---
# <a name="plan-a-software-defined-network-infrastructure"></a>Szoftveresen definiált hálózati infrastruktúra megtervezése

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019, Windows Server (féléves csatorna), Windows Server 2016

A szoftveres hálózati (SDN) infrastruktúra központi telepítésének megtervezése, beleértve a hardver-és szoftver-előfeltételeket. Ez a témakör a fizikai és logikai hálózati konfiguráció, az Útválasztás, az átjárók, a hálózati hardverek és egyebek tervezési követelményeit tartalmazza. Emellett az SDN-infrastruktúra kiterjesztésére és a többfázisú üzembe helyezésre vonatkozó megfontolásokat is tartalmaz.

## <a name="prerequisites"></a>Előfeltételek
Az SDN-infrastruktúra több hardver-és szoftver-előfeltételt is magában foglal, beleértve a következőket:
- **Biztonsági csoportok és dinamikus DNS-regisztráció**. Elő kell készítenie az adatközpontot a hálózati vezérlő üzembe helyezéséhez, amelyhez virtuális gépek (VM-EK) készlete szükséges. A hálózati vezérlő üzembe helyezése előtt konfigurálnia kell a biztonsági csoportokat és a dinamikus DNS-regisztrációt.

    Ha többet szeretne megtudni az adatközpont hálózati vezérlő üzembe helyezéséről, olvassa el a [hálózati vezérlő üzembe helyezésének követelményeit](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller)ismertető témakört.

- **Fizikai hálózat**. A virtuális helyi hálózatok (VLAN-ok), az Útválasztás és a Border Gateway Protocol (BGP) konfigurálásához hozzáféréssel kell rendelkeznie a fizikai hálózati eszközökhöz. Ez a témakör útmutatást nyújt a manuális kapcsolók konfigurálásához, valamint a BGP-partneri kapcsolathoz a 3. rétegbeli kapcsolókon/útválasztón, illetve egy útválasztási és távelérési kiszolgáló (RRAS) virtuális gépen.

- **Fizikai számítási gazdagépek**. Ezek a gazdagépek Hyper-V-t futtatnak, és az SDN-infrastruktúra és a bérlői virtuális gépek üzemeltetéséhez szükségesek. A legjobb teljesítmény érdekében adott hálózati hardverre van szükség ezekben a gazdagépeken, a [hálózati hardver](#network-hardware) részben leírtak szerint.

## <a name="physical-and-logical-network-configuration"></a>Fizikai és logikai hálózati konfiguráció
Minden fizikai számítási gazdagép hálózati kapcsolatot igényel egy fizikai kapcsoló porthoz csatlakoztatott hálózati adapteren keresztül. A 2. rétegbeli [VLAN](https://en.wikipedia.org/wiki/Virtual_LAN) több logikai hálózati szegmensre osztott hálózatokat támogat.

>[!TIP]
>Használja a VLAN 0 logikai hálózatokhoz hozzáférési módban vagy címkézetlen.

>[!IMPORTANT]
>A Windows Server 2016 szoftver által meghatározott hálózatkezelés támogatja az IPv4-címzést az alátéthez és az átfedésekhez. Az IPv6 nem támogatott. A Windows Server 2019 az IPv4-és IPv6-címzést is támogatja.

### <a name="logical-networks"></a>Logical networks
Ez a szakasz a felügyeleti logikai hálózat és a Hyper-V hálózati virtualizálási (HNV) szolgáltató logikai hálózatának SDN infrastruktúra-tervezési követelményeit ismerteti. További logikai hálózatok kiépítésének részleteit tartalmazza az átjárók és a szoftveres Load Balancer (SLB), valamint a minta hálózati topológiák használatához.

#### <a name="management-and-hnv-provider"></a>Felügyeleti és HNV szolgáltató
Minden fizikai számítási gazdagépnek hozzá kell férnie a felügyeleti logikai hálózathoz és a HNV-szolgáltató logikai hálózathoz. Az IP-címek megtervezéséhez minden egyes fizikai számítási gazdagépnek rendelkeznie kell legalább egy, a felügyeleti logikai hálózatból hozzárendelt IP-címmel. A hálózati vezérlőhöz egy fenntartott IP-cím szükséges a hálózatról, amely a reprezentációs állapot átvitelének (REST) IP-címének szolgál.

A HNV-szolgáltatói hálózat a mögöttes fizikai hálózat (belső belső) bérlői forgalom, Észak-/déli (külső belső) bérlői forgalom, valamint a BGP-peering információinak a fizikai hálózattal való cseréje.

A DHCP-kiszolgálók automatikusan rendelhetnek IP-címeket a felügyeleti hálózathoz, vagy manuálisan is hozzárendelhet statikus IP-címeket. Az SDN-verem automatikusan IP-címeket rendel hozzá a HNV-szolgáltató logikai hálózathoz az egyes Hyper-V-gazdagépekhez egy IP-címkészlet alapján. A hálózati vezérlő meghatározza és kezeli az IP-címkészletet.

>[!NOTE]
>A hálózati vezérlő csak azt követően rendel hozzá egy HNV-szolgáltatói IP-címet egy fizikai számítási gazdagéphez, hogy a hálózati házirendet egy adott bérlői virtuális gép számára fogadja.

| Ha...                                                    | Ezután...                                               |
| :------------------------------------------------------- | :---------------------------------------------------- |
| A logikai hálózatok VLAN-okat használnak,                          | a fizikai számítási gazdagépnek csatlakoznia kell egy olyan, a VLAN-hoz hozzáférő, kifelé irányuló kapcsoló porthoz. Fontos megjegyezni, hogy a gazdagépen található fizikai hálózati adapterek nem rendelkezhetnek aktív VLAN-szűréssel.|
| Switched-Embedded-összevonást (SET) használ, és több hálózati adapterrel (NIC) rendelkező csapattagtal (például hálózati adapterekkel) rendelkezik.| az adott gazdagéphez tartozó összes NIC-csapattagot ugyanahhoz a 2. rétegbeli szórási tartományhoz kell kötni.|
| A fizikai számítási gazdagép további infrastruktúra-alapú virtuális gépeket, például hálózati vezérlőt, SLB/multiplexert (MUX) vagy átjárót futtat. | Győződjön meg arról, hogy a felügyeleti logikai hálózat megfelelő IP-címmel rendelkezik az egyes üzemeltetett virtuális gépekhez. Győződjön meg arról is, hogy a HNV-szolgáltató logikai hálózatának elegendő IP-címmel kell rendelkeznie az egyes SLB-/MUX-és átjáró-infrastruktúra virtuális gépekhez való lefoglaláshoz. Habár a hálózati vezérlő felügyeli az IP-foglalást, az új IP-cím nem érhető el, mert a szolgáltatás nem érhető el, ezért duplikált IP-címeket eredményezhet a hálózaton.|

A Microsoft SDN-környezetekben található hálózatok virtualizálása érdekében a Hyper-V hálózati virtualizálási (HNV) szolgáltatással kapcsolatos információkért lásd: [Hyper-v hálózati virtualizálás](/windows-server/networking/sdn/technologies/hyper-v-network-virtualization/hyper-v-network-virtualization).

#### <a name="gateways-and-the-software-load-balancer-slb"></a>Átjárók és a szoftver Load Balancer (SLB)
Az átjárók és a SLB használatához további logikai hálózatokat kell létrehoznia és kiépíteni. Győződjön meg arról, hogy megszerezte a megfelelő IP-előtagokat, a VLAN-azonosítókat és az átjáró IP-címeit a hálózatokhoz.

|                                |                     |
| :----------------------------- | :------------------ |
| **Nyilvános VIP logikai hálózat** | A nyilvános virtuális IP-(VIP-) logikai hálózatnak olyan IP-alhálózati előtagokat kell használnia, amelyek a felhőalapú környezeten kívülre irányíthatók (általában az internetre irányítható). Ezek azok az előtér-IP-címek, amelyeket a külső ügyfelek a virtuális hálózatok erőforrásainak elérésére használnak, beleértve a helyek közötti átjáróhoz tartozó előtéri VIP-t is. Ehhez a hálózathoz nem kell VLAN-t rendelnie. |
| **Privát VIP logikai hálózat** | A privát VIP logikai hálózatnak nem szükséges a felhőn kívüli irányíthatónak lennie. Ennek az az oka, hogy csak a belső Felhőbeli ügyfelektől elérhető virtuális IP-címek használják, például a privát szolgáltatások. Ehhez a hálózathoz nem kell VLAN-t rendelnie. |
| **GRE VIP logikai hálózat** | Az általános útválasztási beágyazás (GRE) VIP-hálózat olyan alhálózat, amely kizárólag a virtuális IP-címek definiálására szolgál. A virtuális IP-címek az SDN-hálón futó átjáró virtuális gépekhez vannak rendelve helyek közötti (S2S) GRE-kapcsolati típushoz. Nem kell előre konfigurálnia ezt a hálózatot a fizikai kapcsolókon vagy útválasztón, vagy hozzá kell rendelnie egy VLAN-t. |

#### <a name="sample-network-topology"></a>Példa hálózati topológia
Módosítsa a környezethez tartozó minta IP-alhálózati előtagokat és VLAN-azonosítókat.

| Hálózati név | Alhálózat | Maszk | VLAN-azonosító a trönkön | Átjáró | Foglalás (példák) |
| :----------------------- | :------------ | :------- | :---------------------------- | :-------------- | :------------------------------------------- |
| Kezelés              | 10.184.108.0 |    24   |          7                   | 10.184.108.1   | 10.184.108.1 – útválasztó<br> 10.184.108.4 – hálózati vezérlő<br> 10.184.108.10 – 1. számítási gazdagép<br> 10.184.108.11 – 2. számítási gazdagép<br> 10.184.108. X – számítási gazdagép X |
| HNV-szolgáltató             |  10.10.56.0  |    23    |          11                |  10.10.56.1    | 10.10.56.1 – útválasztó<br> 10.10.56.2 – SLB/MUX1<br> 10.10.56.5 - Gateway1 |
| Nyilvános VIP               |  41.40.40.0  |    27    |          NA                |  41.40.40.1    | 41.40.40.1 – útválasztó<br> 41.40.40.3 – IPSec S2S VPN VIP |
| Privát VIP              |  20.20.20.0  |    27    |          NA                |  20.20.20.1    | 20.20.20.1 – alapértelmezett GW (útválasztó) |
| GRE VIP                  |  31.30.30.0  |    24    |          NA                |  31.30.30.1    | 31.30.30.1 – alapértelmezett GW |

## <a name="routing-infrastructure"></a>Útválasztási infrastruktúra
Az útválasztási adatokat, \( például a következő ugrást \) a VIP-alhálózatokhoz, a SLB/mux és a távelérési kiszolgáló (RAS) átjárói a belső BGP-társítás használatával hirdetik a fizikai hálózatot. A VIP logikai hálózatok nem rendelkeznek VLAN-hálózattal, és nincsenek előre konfigurálva a 2. rétegbeli kapcsolóban (például a felső és a rack kapcsolóban).

Létre kell hoznia egy BGP-társat az útválasztón, amelyet az SDN-infrastruktúra használ a SLB/MUXes és RAS-átjárók által hirdetett VIP logikai hálózatok útvonalának fogadására. A BGP-társításnak csak egy módon kell történnie (a SLB/MUX vagy a RAS-átjáróról a külső BGP-társra). Az Útválasztás első rétege felett statikus útvonalakat vagy más dinamikus útválasztási protokollt használhat, például megnyithatja a legrövidebb útvonalat (OSPF). Ahogy azt korábban már említettük, a VIP logikai hálózatokhoz tartozó IP-alhálózati előtagnak irányíthatónak kell lennie a fizikai hálózatról a külső BGP-társnak.

A BGP-társítás általában egy felügyelt kapcsolóban vagy útválasztóban van konfigurálva a hálózati infrastruktúra részeként. A BGP-társ a csak útválasztási módban telepített RAS-szerepkörrel rendelkező Windows Serveren is konfigurálható. A hálózati infrastruktúrában a BGP-útválasztó társának úgy kell konfigurálnia, hogy a saját autonóm rendszerszámait (ASN) használja, és az SDN-összetevők \( SLB/mux és RAS-átjáróhoz rendelt ASN-vel való társítását engedélyezze \) .

A következő információkat kell beszereznie a fizikai útválasztóról vagy a hálózati rendszergazdától az útválasztó vezérléséhez:
- Az útválasztó ASN-je
- Útválasztó IP-címe

>[!NOTE]
>A SLB/MUX nem támogatja a négy bájtos ASN. Két bájtos ASN kell lefoglalni a SLB/MUX és az útválasztóhoz, amelyhez csatlakozik. A környezetben máshol négy bájtos ASN is használhat.

Önnek vagy a hálózati rendszergazdának úgy kell konfigurálnia a BGP-útválasztó partnert, hogy fogadja a RAS-átjáró és a SLB MUXes által használt HNV-szolgáltató logikai hálózat ASN-és IP-címének vagy alhálózati címének kapcsolatait.

További információ: [Border Gateway Protocol (BGP)](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp).

## <a name="default-gateways"></a>Alapértelmezett átjárók
A több hálózathoz való kapcsolódásra konfigurált gépek, például a fizikai gazdagépek, a SLB/MUX és az átjáró virtuális gépei csak egy alapértelmezett átjáróval rendelkeznek. Használja a következő alapértelmezett átjárókat a gazdagépekhez és az infrastruktúra-virtuális gépekhez:
- Hyper-V-gazdagépek esetén használja a felügyeleti hálózatot alapértelmezett átjáróként.
- A hálózati vezérlő virtuális gépek esetében a felügyeleti hálózatot használja alapértelmezett átjáróként.
- SLB-vagy MUX-alapú virtuális gépek esetén használja a felügyeleti hálózatot alapértelmezett átjáróként.
- Az átjáróként működő virtuális gépek esetében a HNV-szolgáltatói hálózatot használja alapértelmezett átjáróként. Ezt az átjáró virtuális gépek előtér-hálózati adapterén kell beállítani.

## <a name="network-hardware"></a>Hálózati hardver
Ez a szakasz a hálózati hardver központi telepítési követelményeit ismerteti a hálózati adapterek és fizikai kapcsolók számára.

### <a name="network-interface-cards-nics"></a>Hálózati kártyák (NIC)
A Hyper-V-gazdagépek és a tároló-gazdagépek által használt hálózati adapterek speciális képességeket igényelnek a legjobb teljesítmény eléréséhez.

A távoli közvetlen memória-hozzáférés (RDMA) egy kernel-megkerülési módszer, amely lehetővé teszi nagy mennyiségű adat átvitelét a gazda CPU használata nélkül, így a CPU más munkákat is elvégezhet. A Switch beágyazott összevonása (SET) egy másik hálózati adapterek összevonására szolgáló megoldás, amelyet a Hyper-V és az SDN-verem közé tartozó környezetekben használhat. A SET a hálózati adapterek összevonásának funkcióit integrálja a Hyper-V virtuális kapcsolóba.

További információkért lásd: [távoli közvetlen memória-hozzáférés (RDMA) és Switch Embedded Teaming (set)](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).

Ahhoz, hogy a bérlői virtuális hálózati forgalom a VXLAN vagy NVGRE beágyazási fejlécek által okozott terhelést figyelembe lehessen venni, a 2. rétegbeli háló (kapcsolók és gazdagépek) maximális átviteli egységének (MTU) értékének 1674 bájtnál nagyobbnak vagy azzal egyenlőnek kell lennie, \( beleértve a 2. rétegbeli Ethernet-fejléceket \) .

Az új *EncapOverhead* speciális adaptert támogató hálózati adapterek automatikusan az MTU-t a hálózati vezérlő gazdagépének ügynökén keresztül állíthatják be. Az új *EncapOverhead* -kulcsszót nem támogató hálózati adapterek esetében az MTU-méretet manuálisan kell beállítani az egyes fizikai gazdagépeken a *JumboPacket* \( vagy azzal egyenértékű \) kulcsszó használatával.

### <a name="switches"></a>Switchek
Ha a környezethez fizikai kapcsolót és útválasztót választ, győződjön meg arról, hogy az a következő funkciókat támogatja:
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

### <a name="switch-configuration-examples"></a>Kapcsolók konfigurációs példái
A fizikai kapcsoló vagy útválasztó konfigurálásának elősegítése érdekében a [Microsoft Sdn GitHub-tárházban](https://github.com/microsoft/SDN/tree/master/SwitchConfigExamples)számos különböző kapcsoló modellhez és szállítóhoz tartozó konfigurációs fájl is elérhető. A megadott kapcsolókhoz tartozó részletes readme és tesztelt parancssori felületi (CLI) parancsok is elérhetők.

## <a name="compute"></a>Compute
Minden Hyper-V-gazdagépen telepítve kell lennie a megfelelő operációs rendszernek, engedélyezni kell a Hyper-V-t, és egy külső Hyper-V virtuális kapcsolót kell használnia legalább egy, a felügyeleti logikai hálózathoz csatlakoztatott fizikai adapterrel. A gazdagépnek elérhetőnek kell lennie a felügyeleti gazdagép vNIC rendelt felügyeleti IP-címen keresztül.

A Hyper-V, a Shared vagy a local szolgáltatással kompatibilis tárolási típusokat is használhat.

> [!TIP]
> Az összes virtuális kapcsoló esetében célszerű ugyanazt a nevet használni, de nem kötelező. Ha a telepítéshez parancsfájlokat kíván használni, tekintse meg a `vSwitchName` config.psd1 fájlban lévő változóhoz tartozó megjegyzést.

### <a name="host-compute-requirements"></a>Gazdagép számítási követelményei
A következő példában a központi telepítésben használt négy fizikai gazdagép minimális hardver-és szoftver-követelményei láthatók.

Gazda|Hardverkövetelmények|Szoftverkövetelmények|
--------|-------------------------|-------------------------
|Fizikai Hyper-V-gazdagép|4 magos 2,66 GHz-es processzor<br> 32 GB RAM<br> 300 GB lemezterület<br> 1 GB/s (vagy gyorsabb) fizikai hálózati adapter|Operációs rendszer:<br> a jelen témakör elején a "vonatkozik" kifejezésre.<br> Hyper-V szerepkör telepítve|

### <a name="sdn-infrastructure-vm-role-requirements"></a>Az SDN-infrastruktúra virtuálisgép-szerepkörére vonatkozó követelmények
Az alábbiakban a virtuálisgép-szerepkörökre vonatkozó követelmények láthatók.

Szerepkör|vCPU-követelmények|Memóriakövetelmények|Lemezkövetelmények|
--------|-----------------------------|-----------------------|--------------------------
|Hálózati vezérlő (három csomópont)|4 vCPU|legalább 4 GB<br> (8 GB ajánlott)|75 GB operációs rendszer meghajtója
|SLB/MUX (három csomópont)|8 vCPU|8 GB ajánlott|75 GB operációs rendszer meghajtója
|RAS-átjáró<br> (három csomópont egy készlete<br> átjárók, két aktív, egy passzív)|8 vCPU|8 GB ajánlott|75 GB operációs rendszer meghajtója
|RAS Gateway BGP-útválasztó<br> SLB/MUX-társítás esetén<br> (másik lehetőségként használja a ToR kapcsolót<br> BGP-útválasztóként)|2 vCPU|2 GB|75 GB operációs rendszer meghajtója|

Ha System Center-Virtual Machine Manager (VMM)-t használ az üzembe helyezéshez, további infrastruktúra-virtuálisgép-erőforrások szükségesek a VMM és más nem SDN-infrastruktúrához. További információt a [System Center Virtual Machine Manager rendszerkövetelményei](/system-center/vmm/system-requirements?preserve-view=true&view=sc-vmm-2019)című témakörben talál.

## <a name="extending-your-infrastructure"></a>Az infrastruktúra kiterjesztése
Az infrastruktúra méretezési és erőforrás-követelményei az üzemeltetni kívánt bérlői munkaterhelés virtuális gépektől függenek. Az infrastruktúra-virtuális gépek CPU-, memória-és lemez-követelményeit (például: hálózati vezérlő, SLB, átjáró stb.) az előző táblázatban határozzák meg. Szükség esetén további infrastrukturális virtuális gépeket is hozzáadhat a méretezéshez. A Hyper-V-gazdagépeken futó összes bérlői virtuális gépnek azonban saját CPU-, memória-és lemez-követelményeit kell figyelembe vennie.

Ha a bérlői munkaterhelési virtuális gépek túl sok erőforrást használnak a fizikai Hyper-V-gazdagépeken, az infrastruktúrát további fizikai gazdagépek hozzáadásával is kiterjesztheti. A Windows felügyeleti központ, a VMM vagy a PowerShell-parancsfájlok használatával új kiszolgálói erőforrásokat hozhat létre a hálózati vezérlőn keresztül. A használandó módszer attól függ, hogy miként telepítette az infrastruktúrát. Ha további IP-címeket kell hozzáadnia a HNV-szolgáltatói hálózathoz, létrehozhat új logikai alhálózatokat (a megfelelő IP-készletekkel), amelyeket a gazdagépek használhatnak.

## <a name="phased-deployment"></a>Többfázisú üzembe helyezés
A követelmények alapján előfordulhat, hogy az SDN-infrastruktúra egy részhalmazát kell telepítenie. Ha például csak az adatközpontban lévő ügyfél-munkaterheléseket szeretné üzemeltetni, és a külső kommunikációra nincs szükség, akkor telepítheti a hálózati vezérlőt, és kihagyhatja a SLB/MUX és az átjáró virtuális gépek üzembe helyezését. Az alábbi, az SDN-infrastruktúra többfázisú üzembe helyezésére vonatkozó infrastrukturális követelmények a következők.

Szolgáltatás|Üzembe helyezésre vonatkozó követelmények|A hálózatra vonatkozó követelmények|
--------|-------------------------|-------------------------
|Logikai hálózatok kezelése<br> Hozzáférés-vezérlési listák (ACL-ek) (VLAN-alapú hálózatokhoz)<br> Szolgáltatásminőség (QoS) (VLAN-alapú hálózatokhoz)<br>|Hálózati vezérlő|Nincsenek|
|Virtuális hálózatkezelés<br> Felhasználó által megadott Útválasztás<br> ACL-ek (virtuális hálózathoz)<br> Titkosított alhálózatok<br> QoS (virtuális hálózatokhoz)<br> Virtuális hálózati társviszony|Hálózati vezérlő|HNV PA VLAN, alhálózat, útválasztó|
|Bejövő/kimenő NAT<br> Terheléselosztás|Hálózati vezérlő<br> SLB/MUX|BGP a HNV PA-hálózaton<br> Magán-és nyilvános VIP-alhálózatok|
|GRE-átjáró kapcsolatai|Hálózati vezérlő<br> Átjáró|BGP a HNV PA-hálózaton<br> GRE VIP-alhálózat|
|IPSec-átjáró kapcsolatai|Hálózati vezérlő<br> SLB/MUX<br> Átjáró|BGP a HNV PA-hálózaton<br> Nyilvános VIP-alhálózat|
|L3-átjáró kapcsolatai|Hálózati vezérlő<br> Átjáró|Bérlői VLAN, alhálózat, útválasztó<br> A BGP a bérlői VLAN-on nem kötelező.|

## <a name="next-steps"></a>Következő lépések
A kapcsolódó információkkal kapcsolatban lásd még:
- [A hálózati vezérlő üzembe helyezésére vonatkozó követelmények](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller)
- [SDN Azure Stack HCI-ben](./software-defined-networking.md)