---
title: Gazdagép hálózatkezelésének megtervezése Azure Stack HCI-hez
description: Ismerje meg, hogyan tervezheti meg Azure Stack HCI-fürtök gazdagép hálózatkezelését
author: v-dasis
ms.topic: how-to
ms.date: 10/13/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: aedccf6f41c2bf7e21fd98308fd153c2742af8e9
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92179496"
---
# <a name="plan-host-networking-for-azure-stack-hci"></a>Gazdagép hálózatkezelésének megtervezése Azure Stack HCI-hez

> A következőre vonatkozik Azure Stack HCI, Version 20H2

Ez a témakör a gazdagépek hálózatkezelésének tervezésével kapcsolatos szempontokat és követelményeket ismerteti a nem kiterjesztett és a kibővített Azure Stack HCI-fürt környezetében.

## <a name="traffic-types-supported"></a>Támogatott adatforgalmi típusok

Azure Stack HCI a Server Message Block (SMB) protokollt használja. Azure Stack HCI-ben az SMB a következő adatforgalmi típusokat támogatja:

- Storage Bus Layer (SBL) – Közvetlen tárolóhelyek által használt; legmagasabb prioritású forgalom
- Megosztott fürtkötetek (CSV)
- Élő áttelepítés (LM)
- Storage-replika (SR) – a kiterjesztett fürtökben használatos
- Fájlmegosztás (FS) – hagyományos FS-és Scale-Out fájlkiszolgáló (SOFS)
- Fürt szívverése (HB)
- Fürt kommunikációja (node joins, cluster Updates, Registry Updates)

Az SMB-forgalom a következő protokollokon keresztül végezhető el:

- Transport Control Protocol (TCP) – a helyek között használatos
- Távoli közvetlen memória-hozzáférés (RDMA)

## <a name="traffic-bandwidth-allocation"></a>Forgalmi sávszélesség kiosztása

A következő táblázat a különböző forgalmi típusok sávszélesség-elosztását mutatja be, ahol:

- Minden egység a Gbps-ben van
- Az értékek a kiterjesztett és a nem kifeszített fürtökre egyaránt érvényesek
- Az SMB-forgalom a teljes sávszélesség 50%-át kapja meg.
- A Storage Bus Layer/Fürt megosztott kötete (SBL/CSV) forgalma a fennmaradó 50%-os foglalás 70%-át kapja
- A Élő áttelepítés (LM) forgalom a fennmaradó 50%-os foglalás 15%-át kapja
- A Storage-replika (SR) forgalma a fennmaradó 50%-os foglalás 14%-át kapja
- A szívverés (HB) forgalma a fennmaradó 50%-os foglalás 1%-át kapja
- * = a tömörítést a RDMA helyett kell használnia, ha az LM-forgalom sávszélesség-elosztása <5 GB/s

|Hálózati adapter sebessége|Összevont hálózati adapter sebessége|SMB 50% foglalás|SBL/CSV%|SBL/CSV-sávszélesség|LM|LM-sávszélesség|SR |SR-sávszélesség|HB|HB-sávszélesség|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|10|20|10|70%|7|14% *|1,4 *|14%|1.4|2%|0,2|
|25|50|25|70%|17,5|15% *|3,75 *|14%|3.5|1%|0,25|
|40|80| 40|70%|28|15%|6|14%|5,6|1%|0,4|
|50|100|50|70%|35|15%|7,5|14%|7|1%|0,5|
|100|200|100|70%|70|15%|15|14%|14|1%|1|
|200|400|200|70%|140|15%|30|14%|28|1%|2|

## <a name="rdma-considerations"></a>RDMA szempontok

A távoli közvetlen memória-hozzáférés (RDMA) közvetlen memória-hozzáférés az egyik számítógép memóriájában a másikba a számítógép operációs rendszerének bevonása nélkül. Ez lehetővé teszi a nagy átviteli sebességű, kis késleltetésű hálózatkezelést, miközben minimalizálja a CPU-használatot, ami különösen hasznos a fürtökben.

Az összes gazda RDMA-forgalom kihasználja az SMB Direct előnyeit. Az SMB Direct a RDMA-en keresztül továbbított SMB 3,0-es forgalom, amely a 445-es porton keresztül van multiplexen. Legalább két prioritáson alapuló Flow Control (PFC) engedélyezett forgalmi osztályt (TCs) kell használni ahhoz, hogy a RDMA-forgalom kompatibilis legyen a piacon aktuális és jövőbeli fizikai kapcsolók többségével.

Az Internet Wide RDMA Protocol (iWARP) a TCP protokollon keresztül futtatja a RDMA-t, míg a RDMA konvergált Etherneten (RoCE) keresztül elkerüli a TCP használatát, de az azt támogató hálózati adaptereket és fizikai kapcsolókat is igényel. A RoCE-en keresztüli RDMA-ra vonatkozó konvergens hálózati követelményekért tekintse meg a [Windows Server 2016 és a 2019 RDMA telepítési útmutatóját](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx).

A RDMA alapértelmezés szerint engedélyezve van az azonos alhálózaton található fürt csomópontjai közötti összes kelet-/nyugati forgalomhoz. A RDMA le van tiltva, és nem támogatott a különböző alhálózatokon lévő helyek közötti, Észak-vagy dél felé irányuló fürt forgalmához.

A Azure Stack HCI-RDMA vonatkozó követelmények a következők:

- Az alhálózatok és a helyek (kifeszített fürtök) közötti összes forgalomnak WinSock TCP-t kell használnia. A közbenső hálózati ugrások a Azure Stack HCI nézetén és vezérlésén kívül esnek.
- Az alhálózatok és a helyek közötti RDMA nem támogatott. A Kikapcsolások és a több hálózati eszköz használata több meghibásodási pontot jelent, ahol ez instabillá válhat, és nem támogatott.
- Nem szükséges további virtuális hálózati adapterek a kiterjesztett fürtök tárolási replikájának forgalmához. Hibaelhárítási célokra azonban hasznos lehet a helyek közötti és az alhálózatok közötti adatforgalom elválasztása a kelet-nyugati RDMA-forgalomtól. Ha az SMB Direct nem lehet natív módon letiltani a helyek közötti vagy a több alhálózattal rendelkező forgalmat, akkor:
    - Egy vagy több további Vnic kell kiépíteni a tárolási replikához
    - A Storage-replika Vnic a PowerShell [disable-NetAdapterRDMA](https://docs.microsoft.com/powershell/module/netadapter/disable-netadapterrdma) parancsmaggal kell letiltania a RDMA, mert ez a helyek közötti és az alhálózatok közötti kapcsolat.
    - A natív RDMA-adapterek vSwitch-és Vnic-támogatást igényelnek a tárolási replika támogatásához, hogy megfeleljenek a fenti hely/alhálózat követelményeinek
    - A helyszíni RDMA sávszélesség-követelményeit a **forgalmi sávszélesség kiosztása** című szakaszban leírtak szerint meg kell ismerni a sávszélesség százalékos arányát. Ez biztosítja, hogy a megfelelő sávszélesség-fenntartások és korlátok a keleti/nyugati (csomópont-csomópont) forgalomra alkalmazhatók legyenek.
- A Élő áttelepítés és a tárolási replika forgalmának SMB sávszélesség-korlátozottnak kell lennie, ellenkező esetben az összes sávszélességet fel lehet használni, és a magas prioritású tárolási forgalmat is megéhezik. További információ: [set-SmbBandwidthLimit](https://docs.microsoft.com/powershell/module/smbshare/set-smbbandwidthlimit) és [set-SRNetworkConstraint PowerShell-](https://docs.microsoft.com/powershell/module/storagereplica/set-srnetworkconstraint) parancsmagok.

> [!NOTE]
> A parancsmag használatakor a BITS-et bájtra kell konvertálnia `Set-SmbBandwidthLimit` .

## <a name="node-interconnect-requirements"></a>Node Interconnect-követelmények

Ez a szakasz a különböző hálózati követelményeket tárgyalja egy adott hely kiszolgálói csomópontjai között, az úgynevezett kapcsolatokat. A kapcsolót vagy a kapcsoló nélküli csomópont-összekapcsolást lehet használni és támogatni:

- **Váltás:** A kiszolgálói csomópontok általában hálózati kapcsolókat használó Ethernet-hálózatokon keresztül kapcsolódnak egymáshoz. A kapcsolókat megfelelően kell konfigurálni a sávszélesség és a hálózat típusának kezeléséhez. Ha a RoCE protokollt implementáló RDMA használ, a hálózati eszköz és a kapcsoló konfigurációja fontos.
- **Váltás:** A kiszolgáló-csomópontok a közvetlen Ethernet-kapcsolatok kapcsoló nélkül is összekapcsolhatók. Ebben az esetben minden egyes kiszolgáló-csomópontnak közvetlen kapcsolattal kell rendelkeznie az ugyanazon a helyen található többi fürtcsomóponton.

### <a name="interconnects-for-2-3-node-clusters"></a>2-3 csomópontos fürtök összekapcsolása

A két vagy három csomóponttal rendelkező egyhelyes fürtök *minimális* összekötési követelményei. Ezek az egyes kiszolgálók csomópontjaira vonatkoznak:

- Egy vagy több 1 GB-os hálózati adapter, amelyet a felügyeleti funkciókhoz kíván használni
- Egy vagy több 10 GB (vagy gyorsabb) hálózati kártya a tárolási és a számítási feladatok forgalmához
- A redundancia és a teljesítmény érdekében ajánlott két vagy több hálózati kapcsolat az egyes csomópontok között

### <a name="interconnects-for-4-node-and-greater-clusters"></a>4 csomópontos és nagyobb fürtökkel való kapcsolat

Ezek a legalább négy csomóponttal rendelkező fürtök és a nagy teljesítményű fürtök esetében *minimálisan* szükséges összekötők. Ezek az egyes kiszolgálók csomópontjaira vonatkoznak:

- Egy vagy több 1 GB-os hálózati adapter, amelyet a felügyeleti funkciókhoz kíván használni.
- Egy vagy több 25 GB (vagy gyorsabb) hálózati adapter a tárolási és a számítási feladatok forgalmához. A redundancia és a teljesítmény érdekében két vagy több hálózati kapcsolatot ajánlunk.
- Távoli közvetlen memória-hozzáférést (RDMA) támogató hálózati kártyák: iWARP (ajánlott) vagy RoCE.

### <a name="site-to-site-requirements-stretched-cluster"></a>Helyek közötti követelmények (kiterjesztett fürt)

Ha a helyek között csatlakozik a kibővített fürtökhöz, az egyes helyeken lévő összekapcsolási követelmények továbbra is érvényben vannak, és további tárolási replika és Hyper-V élő áttelepítési forgalomra vonatkozó követelményeket kell figyelembe venni:

- Legalább 1 1 GB RDMA vagy Ethernet/TCP kapcsolat a helyek között a szinkron replikáláshoz. A 25 GB-os kapcsolatok használata javasolt.
- A helyek közötti, elegendő sávszélességgel rendelkező hálózat, amely az I/O-írási munkaterhelést, valamint a szinkron replikálás átlagos 5ms vagy alacsonyabb késését tartalmazza. Az aszinkron replikációhoz nem tartozik késési javaslat.
- Ha egyetlen kapcsolatot használ a helyek között, állítsa be a tárolási replika SMB sávszélesség-korlátait a PowerShell használatával. További információ: [set-SmbBandwidthLimit](/powershell/module/smbshare/set-smbbandwidthlimit).
- Ha több kapcsolatot használ a helyek között, válassza a kapcsolatok közötti adatforgalmat. Tegyük fel például, hogy a tárolási replika forgalmát egy különálló hálózaton helyezi el, mint a Hyper-V élő áttelepítési forgalom a PowerShell használatával. További információ: [set-SRNetworkConstraint](/powershell/module/storagereplica/set-srnetworkconstraint).

## <a name="network-port-requirements"></a>Hálózati portokra vonatkozó követelmények

Győződjön meg arról, hogy a megfelelő hálózati portok nyitva vannak az összes kiszolgáló-csomópont között egy helyen és a helyek között (a kifeszített fürtök esetében). Szüksége lesz a megfelelő tűzfal-és útválasztó-szabályokra az ICMP, az SMB (445-es port, a 5445-es port, az SMB Direct esetében) és a WS-MAN (port 5985) kétirányú forgalom engedélyezéséhez a fürt összes kiszolgálója között.

Ha a fürt létrehozásához a Windows felügyeleti központban a fürt létrehozása varázslót használja, a varázsló automatikusan megnyitja a megfelelő tűzfal-portokat a fürt minden kiszolgálóján a feladatátvételi fürtszolgáltatás, a Hyper-V és a tárolási replika számára. Ha az egyes kiszolgálókon eltérő tűzfalat használ, nyissa meg a következő portokat:

### <a name="failover-clustering-ports"></a>Feladatátvételi fürtszolgáltatás portjai

- ICMPv4 és ICMPv6
- 445-es TCP-port
- Dinamikus RPC-portok
- 135-es TCP-port
- 137-es TCP-port
- 3343-es TCP-port
- 3343-es UDP-port

### <a name="hyper-v-ports"></a>Hyper-V-portok

- 135-es TCP-port
- 80-es TCP-port (HTTP-kapcsolat)
- 443-es TCP-port (HTTPS-kapcsolat)
- 6600-es TCP-port
- 2179-es TCP-port
- Dinamikus RPC-portok
- RPC végpontleképező
- 445-es TCP-port

### <a name="storage-replica-ports-stretched-cluster"></a>Tárolási replika portjai (kifeszített fürt)

- 445-es TCP-port
- TCP 5445 (ha iWarp RDMA használ)
- 5985-es TCP-port
- ICMPv4 és ICMPv6 (ha a `Test-SRTopology` PowerShell-parancsmagot használja)

Előfordulhat, hogy a fentiekben nem szereplő további portok szükségesek. Ezek az alapszintű Azure Stack HCI-funkciók portjai.

## <a name="network-switch-requirements"></a>Hálózati kapcsolókra vonatkozó követelmények

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

## <a name="next-steps"></a>Következő lépések

- A feladatátvételi fürtszolgáltatás alapjai. Lásd: a [feladatátvételi fürtszolgáltatás hálózatkezelésének alapjai](https://techcommunity.microsoft.com/t5/failover-clustering/failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09)
- Ecset a SET használatával. Lásd: [távoli közvetlen memória-hozzáférés (RDMA) és Switch Embedded Teaming (set)](https://docs.microsoft.com/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming)
- A telepítéssel kapcsolatban lásd: [fürt létrehozása a Windows felügyeleti központtal](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster)
- A telepítéssel kapcsolatban lásd: [fürt létrehozása a Windows PowerShell használatával](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster-powershell)