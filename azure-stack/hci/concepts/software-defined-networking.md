---
title: Szoftveresen definiált hálózatkezelés (SDN) Azure Stack HCI-ben
description: Az SDN azon témaköreinek áttekintése, amelyek a Azure Stack HCI szolgáltatásaira vonatkoznak.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/24/2020
ms.openlocfilehash: 34ddaf17a9c90373d76a9b0ce490e2e60a1e221d
ms.sourcegitcommit: 034e61836038ca75199a0180337257189601cd12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91230478"
---
# <a name="sdn-in-azure-stack-hci"></a>SDN Azure Stack HCI-ben

> A következőre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

A szoftveresen definiált hálózatkezelés (SDN) lehetővé teszi a hálózatok és hálózati szolgáltatások központi konfigurálását és kezelését, például a váltást, az útválasztást és a terheléselosztást az adatközpontban. Az SDN használatával dinamikusan hozhatja létre, biztonságossá teheti és összekapcsolhatók a hálózata, hogy megfeleljenek az alkalmazások változó igényeinek. A globálisan méretezhető adatközpont-hálózatok olyan szolgáltatásokhoz, mint például a Microsoft Azure, amelyek minden nap több tízezer hálózati módosítást tesznek lehetővé, csak az SDN miatt lehetséges.

A virtuális hálózati elemek, például a [Hyper-v virtuális kapcsoló](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch), a [Hyper-v hálózati virtualizálás](/windows-server/networking/sdn/technologies/hyper-v-network-virtualization/hyper-v-network-virtualization), a [szoftveres](/windows-server/networking/sdn/technologies/network-function-virtualization/software-load-balancing-for-sdn)terheléselosztás és a [RAS-átjáró](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-for-sdn) az Sdn-infrastruktúra szerves elemeivé vannak kialakítva. Meglévő SDN-kompatibilis eszközeit is használhatja a virtuális hálózatokban és a fizikai hálózaton futó munkaterhelések közötti mélyebb integráció eléréséhez.

A Azure Stack HCI három fő SDN-összetevővel rendelkezik, és kiválaszthatja, hogy melyik szolgáltatást szeretné telepíteni: hálózati vezérlő, szoftveres Load Balancer és átjáró.

## <a name="network-controller"></a>Hálózati vezérlő

A [hálózati vezérlő](/windows-server/networking/sdn/technologies/Software-Defined-Networking-Technologies#network-controller) központosított, programozható automatizálási pontot biztosít az adatközpontban lévő virtuális hálózati infrastruktúra kezeléséhez, konfigurálásához, figyeléséhez és hibakereséséhez. Ez egy nagy mértékben méretezhető kiszolgálói szerepkör, amely a magas rendelkezésre állás biztosítása érdekében Service Fabric használ. A hálózati vezérlőt saját dedikált virtuális gépekre kell telepíteni.

A hálózati vezérlő üzembe helyezése a következő funkciókat biztosítja:

- Virtuális hálózatok és alhálózatok létrehozása és kezelése. Virtuális gépek (VM-EK) összekötése virtuális alhálózatokkal.
- A virtuális hálózatokhoz vagy a hagyományos VLAN-alapú hálózatokhoz csatlakoztatott virtuális gépek mikro-szegmentálásának konfigurálása és kezelése.
- Virtuális készülékek csatlakoztatása a virtuális hálózatokhoz.
- Konfigurálja a szolgáltatásminőség (QoS) házirendeket a virtuális hálózatokhoz vagy a hagyományos VLAN-alapú hálózatokhoz csatolt virtuális gépekhez.

Javasoljuk, hogy Azure Stack HCI-fürt létrehozása után [telepítse a hálózati vezérlőt a PowerShell használatával](../deploy/network-controller-powershell.md) .

## <a name="software-load-balancing"></a>Szoftveres terheléselosztás

A [szoftveres](/windows-server/networking/sdn/technologies/network-function-virtualization/software-load-balancing-for-sdn) terheléselosztás (SLB) használatával egyenletesen terjesztheti az ügyfelek hálózati forgalmát több virtuális gép között. Lehetővé teszi, hogy több kiszolgáló ugyanazt a számítási feladatot működtesse, magas rendelkezésre állást és méretezhetőséget biztosítva. A SLB a [Border Gateway Protocol](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) használatával hirdeti meg a virtuális IP-címeket a fizikai hálózaton.

## <a name="gateway"></a>Átjáró

Az átjárók a virtuális hálózat és egy másik hálózat (helyi vagy távoli) közötti hálózati forgalom útválasztására szolgálnak. Az átjárók a következőket vehetik fel:

- Hozzon létre biztonságos helyek közötti IPsec-kapcsolatokat az SDN virtuális hálózatok és a külső ügyfél-hálózatok között az interneten keresztül.
- Hozzon létre általános útválasztási beágyazási (GRE) kapcsolatokat az SDN virtuális hálózatok és a külső hálózatok között. A helyek közötti kapcsolatok és a GRE-kapcsolatok közötti különbség az, hogy az utóbbi nem titkosított kapcsolat. További információ a GRE-kapcsolati forgatókönyvekről: [gre Tunneling in Windows Server](/windows-server/remote/remote-access/ras-gateway/gre-tunneling-windows-server).
- Hozzon létre 3. rétegbeli kapcsolatot az SDN virtuális hálózatok és a külső hálózatok között. Ebben az esetben az SDN-átjáró egyszerűen útválasztóként működik a virtuális hálózat és a külső hálózat között.

Az átjárók [Border Gateway Protocol](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) a GRE-végpontok reklámozására és pont-pont típusú kapcsolatok létrehozására szolgálnak. Az SDN üzembe helyezése egy alapértelmezett átjárót hoz létre, amely támogatja az összes kapcsolattípust. Ebben a készletben megadhatja, hogy hány átjáró legyen lefoglalva készenléti állapotban, ha egy aktív átjáró meghibásodik.

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Szoftveresen definiált hálózati infrastruktúra megtervezése](plan-software-defined-networking-infrastructure.md)
- [SDN a Windows Serverben – áttekintés](/windows-server/networking/sdn/software-defined-networking)
- [Szoftveralapú hálózati infrastruktúra telepítése parancsprogramokkal](/windows-server/networking/sdn/deploy/deploy-a-software-defined-network-infrastructure-using-scripts)
