---
title: VPN-átjárók létrehozása Azure Stack hub számára | Microsoft Docs
description: VPN-átjárók létrehozása és konfigurálása Azure Stack hub számára.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: sethm
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: f12cf32a3269a850b7dacda5ed97e0a4c886828b
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75818604"
---
# <a name="create-vpn-gateways-for-azure-stack-hub"></a>VPN-átjárók létrehozása Azure Stack hubhoz

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek és Azure Stack Development Kit*

Mielőtt hálózati forgalmat küldene az Azure-beli virtuális hálózat és a helyszíni hely között, létre kell hoznia egy virtuális hálózati (VPN-) átjárót a virtuális hálózathoz.

A VPN-átjáró a virtuális hálózati átjárók egy olyan típusa, amely titkosított adatforgalmat továbbít nyilvános kapcsolaton keresztül. A VPN-átjárók használatával a Azure Stack hub virtuális hálózata és az Azure-beli virtuális hálózat között biztonságosan küldhet forgalmat. A virtuális hálózat és egy másik, VPN-eszközhöz csatlakozó hálózat között is biztonságosan küldhet forgalmat.

Virtuális hálózati átjáró létrehozásakor megadja a létrehozni kívánt átjárótípust. Azure Stack hub a virtuális hálózati átjárók egy típusát támogatja: a **VPN-** típust.

Mindegyik virtuális hálózat kettő virtuális hálózati átjáróval rendelkezhet, de típusonként csak eggyel. A kiválasztott beállításoktól függően több kapcsolatot is létrehozhat egyetlen VPN-átjáróhoz. Ilyen típusú beállítás például egy többhelyes kapcsolat konfigurálása.

A Azure Stack hub VPN-átjáróinak létrehozása és konfigurálása előtt tekintse át a [Azure stack hub hálózatkezelésével kapcsolatos szempontokat](azure-stack-network-differences.md) , hogy megtudja, hogyan különböznek az Azure-beli konfigurációk a Azure stack hub-től.

>[!NOTE]
>Az Azure-ban a kiválasztott VPN Gateway SKU sávszélességének átviteli sebességét az átjáróhoz csatlakozó összes kapcsolaton át kell osztani. Azure Stack hub-ban azonban a VPN Gateway SKU sávszélesség-értéke az átjáróhoz csatlakozó összes kapcsolati erőforrásra vonatkozik.
>
> Példa:
>
> * Az Azure-ban az alapszintű VPN Gateway SKU körülbelül 100 Mbps összesített adatátviteli kapacitást tud kielégíteni. Ha két kapcsolatot hoz létre a VPN-átjáróhoz, és az egyik kapcsolat 50 Mbps sávszélességet használ, akkor az 50 Mbps elérhető lesz a másik kapcsolat számára.
> * Azure Stack hub-ban az alapszintű VPN Gateway SKU-hoz való minden kapcsolat 100 Mbps átviteli sebességgel van lefoglalva.

## <a name="configuring-a-vpn-gateway"></a>VPN-átjáró konfigurálása

A VPN Gateway-kapcsolatok számos, adott beállításokkal konfigurált erőforrásra támaszkodnak. Ezen erőforrások többsége külön konfigurálható, de bizonyos esetekben ezeket egy adott sorrendben kell konfigurálni.

### <a name="settings"></a>Beállítások

Az egyes erőforrásokhoz kiválasztott beállítások kritikus fontosságúak a sikeres kapcsolatok létrehozásához.

A VPN-átjárók egyes erőforrásaival és beállításaival kapcsolatos információkért lásd: [Azure stack hub VPN Gateway-beállításai](azure-stack-vpn-gateway-settings.md). Ez a cikk segítséget nyújt a következők megértésében:

* Az átjáró típusainak, a VPN-típusoknak és a kapcsolati típusoknak.
* Az átjáró-alhálózatokat, a helyi hálózati átjárókat és az egyéb erőforrás-beállításokat, amelyeket érdemes figyelembe venni.

### <a name="deployment-tools"></a>Üzembe helyezési eszközök

Erőforrásokat egyetlen konfigurációs eszközzel hozhat létre és konfigurálhat, például a Azure Portal. Később átválthat egy másik eszközre, például a PowerShellre a további erőforrások konfigurálásához, vagy a meglévő erőforrások módosításához, ha van ilyen. Jelenleg nem lehet a minden erőforrást és erőforrás-beállítást az Azure Portalon konfigurálni. Az egyes kapcsolati topológiákhoz tartozó cikkekben lévő utasítások egyértelműsítik, hogy mikor van szükség egy speciális konfigurációs eszközre.

## <a name="connection-topology-diagrams"></a>Kapcsolatok topológiájának diagramjai

A VPN Gateway-kapcsolatokhoz különböző konfigurációk érhetők el. Döntse el, melyik konfiguráció felel meg legjobban az igényeinek. A következő szakaszban megtekintheti az alábbi VPN Gateway-kapcsolatokkal kapcsolatos információkat és topológiai diagramokat:

* Elérhető üzemi modell
* Elérhető konfigurációs eszközök
* Hivatkozások, amelyek közvetlenül egy cikkre mutatnak, ha van ilyen

A következő szakaszban található diagramok és leírások segítségével kiválaszthatja az igényeinek megfelelő kapcsolódási topológiát. A diagramok a fő alapkonfigurációkat mutatják be, de a diagramok útmutatóként való használatával összetettebb konfigurációk is létrehozhatók.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Helyek közötti és többhelyes (IPsec/IKE VPN-alagút)

### <a name="site-to-site"></a>Helyek közötti kapcsolat

A *helyek közötti* (S2S) VPN Gateway-kapcsolat IPSec/IKE (IKEV2) VPN-alagúton keresztüli kapcsolat. Az ilyen típusú kapcsolathoz olyan VPN-eszköz szükséges, amely a helyszínen található, és egy nyilvános IP-címet kap. Ez az eszköz nem helyezhető el NAT mögött. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók.

![Helyek közötti VPN-kapcsolat konfigurációjának példája](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>több hely

A *többhelyes* kapcsolat a helyek közötti kapcsolat egy változata. A virtuális hálózati átjáróról több VPN-kapcsolatot hoz létre, amelyek általában több helyszíni helyhez csatlakoznak. Több kapcsolat használata esetén egy Route-alapú VPN-típust (más néven dinamikus átjárót) kell használnia a klasszikus virtuális hálózatok használata esetén. Mivel minden virtuális hálózat csak egy VPN-átjáróval rendelkezhet, az átjárón keresztüli összes kapcsolat osztozik a rendelkezésre álló sávszélességen.

![Azure VPN Gateway többhelyes kapcsolat – példa](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>Gateway termékváltozatok

Azure Stack hub virtuális hálózati átjárójának létrehozásakor meg kell adnia a használni kívánt átjáró-SKU-t. A következő VPN Gateway SKU-ket támogatja:

* Basic
* Standard
* Kiemelkedő teljesítmény

Ha egy magasabb átjárós SKU-t választ, például a standard szintű vagy a standard szintű vagy az alapszintű teljesítményt, a rendszer több processzort és hálózati sávszélességet rendel az átjáróhoz. Ennek eredményeképpen az átjáró képes támogatni a virtuális hálózat nagyobb hálózati átviteli sebességét.

Azure Stack hub nem támogatja az ultra Performance Gateway SKU-t, amely kizárólag az expressz útvonalon használható.

Az SKU kiválasztásakor vegye figyelembe a következőket:

* Azure Stack hub nem támogatja a házirend-alapú átjárókat.
* A Border Gateway Protocol (BGP) nem támogatott az alapszintű SKU-ban.
* ExpressRoute – a VPN-átjárók egyidejű konfigurációi Azure Stack központban nem támogatottak.

## <a name="gateway-availability"></a>Átjáró rendelkezésre állása

A magas rendelkezésre állási forgatókönyvek csak a **nagy teljesítményű átjáró** -kapcsolatok SKU-ban konfigurálhatók. Az Azure-tól eltérően, amely aktív/aktív és aktív/passzív konfigurációkon keresztül biztosítja a rendelkezésre állást, Azure Stack hub csak az aktív/passzív konfigurációt támogatja.

### <a name="failover"></a>Feladatátvétel

Az Azure Stack központban három több-bérlős átjáró-infrastruktúra található. Két virtuális gép aktív üzemmódban van, a harmadik pedig redundáns módban van. Az aktív virtuális gépek lehetővé teszik a VPN-kapcsolatok létrehozását, és a redundáns virtuális gép csak akkor fogadja a VPN-kapcsolatokat, ha feladatátvétel történik. Ha egy aktív átjárót használó virtuális gép elérhetetlenné válik, a VPN-kapcsolat a kapcsolat elvesztése után rövid idő (néhány másodperc) elteltével átadja a virtuális gép feladatátvételét.

## <a name="estimated-aggregate-throughput-by-sku"></a>A termékváltozat becsült összesített átviteli sebessége

A következő táblázat az átjárók típusát és az átjáró SKU által becsült összesített átviteli sebességet mutatja:

|| VPN Gateway teljesítménye (1) | VPN Gateway IPsec-alagútjainak maximális száma (2) |
|-------|-------|-------|
|**Alapszintű SKU** **(3)** | 100 Mb/s | 20 |
|**Szabványos SKU** | 100 Mb/s | 20 |
|**Nagy teljesítményű SKU** | 200 Mb/s | 10 |

### <a name="table-notes"></a>Tábla megjegyzései

**(1)** – a VPN-átviteli sebesség nem garantált átviteli sebesség az interneten keresztül létesített létesítmények közötti kapcsolatokhoz. Ez a maximális mérhető teljesítmény.  
**(2)** – a maximális alagutak az összes előfizetésre vonatkozóan Azure stack központilag üzemelő példányok száma.  
**(3)** – a BGP-útválasztás nem támogatott az alapszintű SKU esetében.

>[!NOTE]
>Két Azure Stack hub üzemelő példány között csak egy helyek közötti VPN-kapcsolat hozható létre. Ennek oka a platform olyan korlátozása, amely csak egyetlen VPN-kapcsolat használatát teszi lehetővé ugyanahhoz az IP-címhez. Mivel Azure Stack hub kihasználja a több-bérlős átjárót, amely egyetlen nyilvános IP-címet használ a Azure Stack hub rendszer összes VPN-átjárója számára, csak egy VPN-kapcsolat lehet két Azure Stack hub-rendszer között. Ez a korlátozás arra az esetre is vonatkozik, ha egynél több helyek közötti VPN-kapcsolatot csatlakoztat egy olyan VPN-átjáróhoz, amely egyetlen IP-címet használ. Azure Stack hub nem teszi lehetővé, hogy egynél több helyi hálózati átjáró-erőforrást hozzon létre ugyanazzal az IP-címmel.

## <a name="next-steps"></a>Következő lépések

* [Azure Stack hub VPN Gateway konfigurációs beállításai](azure-stack-vpn-gateway-settings.md)
