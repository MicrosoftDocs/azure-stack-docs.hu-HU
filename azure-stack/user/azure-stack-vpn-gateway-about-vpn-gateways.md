---
title: VPN-átjárók létrehozása a Azure Stackhoz | Microsoft Docs
description: VPN-átjárók létrehozása és konfigurálása Azure Stackhoz.
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
ms.date: 05/21/2019
ms.author: sethm
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 980d601dd5830d653787fe4cc31f57be3b3f8d00
ms.sourcegitcommit: b3dac698f2e1834491c2f9af56a80e95654f11f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68658674"
---
# <a name="create-vpn-gateways-for-azure-stack"></a>VPN-átjárók létrehozása a Azure Stackhoz

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Hálózati forgalom küldése az Azure virtuális hálózat és a helyszíni hely között, előtt a virtuális hálózat egy virtuális hálózati átjárót kell létrehoznia.

A VPN-átjáró a virtuális hálózati átjárók egy olyan típusa, amely titkosított adatforgalmat továbbít nyilvános kapcsolaton keresztül. VPN-átjárók segítségével biztonságosan a virtuális hálózat az Azure Stackben és a egy virtuális hálózat között forgalmat küldeni az Azure-ban. Biztonságosan egy virtuális hálózat és a egy VPN-eszközhöz van csatlakoztatva egy másik hálózat közötti forgalmat is küldhet.

Virtuális hálózati átjáró létrehozásakor megadja a létrehozni kívánt átjárótípust. Azure Stack támogatja a virtuális hálózati átjárók egy típusát: a **VPN-** típust.

Mindegyik virtuális hálózat kettő virtuális hálózati átjáróval rendelkezhet, de típusonként csak eggyel. A kiválasztott beállításoktól függően több kapcsolatot is létrehozhat egyetlen VPN-átjáróhoz. Ilyen típusú beállítás például egy többhelyes kapcsolat konfigurálása.

A Azure Stack VPN-átjárók létrehozása és konfigurálása előtt tekintse át [Azure stack hálózatkezelés szempontjait](azure-stack-network-differences.md) , hogy megtudja, hogyan különböznek az Azure-beli konfigurációk Azure stack.

>[!NOTE]
>Az Azure-ban a kiválasztott VPN Gateway SKU sávszélességének átviteli sebességét az átjáróhoz csatlakozó összes kapcsolaton át kell osztani. Azure Stack azonban a VPN Gateway SKU sávszélesség-értékét a rendszer az átjáróhoz csatlakozó összes kapcsolati erőforrásra alkalmazza.
>
> Példa:
>
> * Az Azure-ban az alapszintű VPN Gateway SKU körülbelül 100 Mbps összesített adatátviteli kapacitást tud kielégíteni. Ha két kapcsolatot hoz létre a VPN-átjáróhoz, és az egyik kapcsolat 50 Mbps sávszélességet használ, akkor az 50 Mbps elérhető lesz a másik kapcsolat számára.
> * Azure Stack az alapszintű VPN Gateway SKU-val **létesített minden kapcsolat** 100 MB/s adatátviteli sebességet foglal le.

## <a name="configuring-a-vpn-gateway"></a>VPN-átjáró konfigurálása

VPN gateway-kapcsolat meghatározott beállításokkal konfigurált számos erőforrás támaszkodik. Ezek az erőforrások többsége külön konfigurálható, de bizonyos esetekben kell ezeket konfigurálni a meghatározott sorrendben.

### <a name="settings"></a>Beállítások

Az egyes erőforrásokhoz kiválasztott beállítások kritikus fontosságúak a sikeres kapcsolatok létrehozásához.

Egyéni erőforrások és a VPN Gateway beállításaival kapcsolatos további információkért lásd: [kapcsolatos VPN gateway beállításairól az Azure Stack](azure-stack-vpn-gateway-settings.md). Ez a cikk segítséget nyújt a következők megértésében:

* Átjáró VPN-típusok és kapcsolattípusok.
* Átjáró-alhálózatokkal, a helyi hálózati átjárót és a többi erőforrás-beállítások, amelyeket érdemes figyelembe venni.

### <a name="deployment-tools"></a>Üzembe helyezési eszközök

Létrehozhat és konfigurálhat erőforrásokat egy konfigurációs eszközt, például az Azure portal használatával. Később átválthat egy másik eszközre, például a PowerShellre a további erőforrások konfigurálásához, vagy a meglévő erőforrások módosításához, ha van ilyen. Jelenleg nem lehet a minden erőforrást és erőforrás-beállítást az Azure Portalon konfigurálni. Az egyes kapcsolati topológiákhoz tartozó cikkekben lévő utasítások egyértelműsítik, hogy mikor van szükség egy speciális konfigurációs eszközre.

## <a name="connection-topology-diagrams"></a>Kapcsolati topológia-diagramok

A VPN Gateway-kapcsolatokhoz különböző konfigurációk érhetők el. Határozza meg, melyik konfiguráció felel meg a legjobban az igényeinek. Az alábbiakban tekintheti meg a következő VPN gateway-kapcsolatok információt és topológia-diagramokat:

* Elérhető üzemi modell
* Elérhető konfigurációs eszközök
* Hivatkozások, amelyek közvetlenül egy cikkre mutatnak, ha van ilyen

Az ábrák és leírások az alábbi szakaszok segítségével válassza ki az igényeinek megfelelő kapcsolati topológiát. Az ábrák bemutatják a fő alapvető topológiákat, de lehetséges összetettebb konfigurációk használata a diagramokat útmutatásként hozhat létre.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Helyek közötti és többhelyes (IPsec/IKE VPN-alagút)

### <a name="site-to-site"></a>Helyek közötti

A *helyek közötti* (S2S) VPN Gateway-kapcsolat IPSec/IKE (IKEV2) VPN-alagúton keresztüli kapcsolat. Az ilyen típusú kapcsolathoz olyan VPN-eszköz szükséges, amely a helyszínen található, és egy nyilvános IP-címet kap. Ez az eszköz nem található a NAT mögötti. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók.

![Site-to-site VPN kapcsolat konfigurációja – példa](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Többhelyes

A többhelyes kapcsolat a helyek közötti kapcsolat egy változata. A virtuális hálózati átjáróról több VPN-kapcsolatot hoz létre, amelyek általában több helyszíni helyhez csatlakoznak. Több kapcsolat használata esetén egy Route-alapú VPN-típust (más néven dinamikus átjárót) kell használnia a klasszikus virtuális hálózatok használata esetén. Mivel minden virtuális hálózat csak egy VPN-átjáróval rendelkezhet, az átjárón keresztüli összes kapcsolat osztozik a rendelkezésre álló sávszélességen.

![Azure VPN Gateway többhelyes kapcsolat – példa](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>Gateway termékváltozatok

Az Azure stack-beli virtuális hálózati átjáró létrehozásakor meg kell adnia a használni kívánt Termékváltozatot. VPN gateway a következő termékváltozatok támogatottak:

* Alapszintű
* Standard
* Nagy teljesítmény

Ha egy magasabb átjárós SKU-t választ, például a standard szintű vagy a standard szintű vagy az alapszintű teljesítményt, a rendszer több processzort és hálózati sávszélességet rendel az átjáróhoz. Ennek eredményeképpen az átjáró nagyobb hálózati átviteli sebesség a virtuális hálózat is támogatja.

A Azure Stack nem támogatja az ultra Performance Gateway SKU-t, amely kizárólag az expressz útvonalon használható.

Az SKU kiválasztásakor vegye figyelembe a következőket:

* A Azure Stack nem támogatja a házirend-alapú átjárókat.
* A Border Gateway Protocol (BGP) nem támogatott az alapszintű SKU-ban.
* ExpressRoute – a VPN-átjárók egyidejű konfigurációi Azure Stack nem támogatottak.

## <a name="gateway-availability"></a>Átjáró rendelkezésre állása

A magas rendelkezésre állási forgatókönyvek csak a **nagy teljesítményű átjáró** -kapcsolatok SKU-ban konfigurálhatók. Az Azure-val ellentétben, amely az aktív/aktív és az aktív/passzív konfigurációkon keresztül biztosítja a rendelkezésre állást, Azure Stack csak az aktív/passzív konfigurációt támogatja. 

### <a name="failover"></a>Feladatátvétel

A Azure Stackban három több-bérlős átjáró-infrastruktúra található. Két virtuális gép aktív üzemmódban van, a harmadik pedig redundáns módban van. Az aktív virtuális gépek lehetővé teszik a VPN-kapcsolatok létrehozását, és a redundáns virtuális gép csak akkor fogadja a VPN-kapcsolatokat, ha feladatátvétel történik. Ha egy aktív átjárót használó virtuális gép elérhetetlenné válik, a VPN-kapcsolat a kapcsolat elvesztése után rövid idő (néhány másodperc) elteltével átadja a virtuális gép feladatátvételét.

## <a name="estimated-aggregate-throughput-by-sku"></a>A termékváltozat becsült összesített átviteli sebessége

A következő táblázat az átjárók típusát és az átjáró SKU által becsült összesített átviteli sebességet mutatja:

|| VPN Gateway teljesítménye *(1)* | VPN Gateway maximális IPsec-alagútjainak *(2)* |
|-------|-------|-------|
|**Alapszintű Termékváltozat** ***(3)*** | 100 Mbps | 20 |
|**A standard Termékváltozat** | 100 Mbps | 20 |
|**Nagy teljesítményű SKU** | 200 Mbps | 10 |

**Tábla megjegyzések:**

*Megjegyzés (1)* – a VPN-átviteli sebesség nem garantált átviteli sebesség az interneten keresztül létesített létesítmények közötti kapcsolatokhoz. Legyen a maximális mérhető teljesítmény.  
*Megjegyzés (2)* – az összes előfizetésre vonatkozóan Azure stack üzemelő példányok maximális száma.  
*Megjegyzés (3)* – az alapszintű Termékváltozat esetén a BGP-útválasztás nem támogatott.

>[!NOTE]
>Két Azure Stack üzemelő példány között csak egy helyek közötti VPN-kapcsolat hozható létre. Ennek oka a platform olyan korlátozása, amely csak egyetlen VPN-kapcsolat használatát teszi lehetővé ugyanahhoz az IP-címhez. Mivel Azure Stack kihasználja a több-bérlős átjárót, amely egyetlen nyilvános IP-címet használ a Azure Stack rendszer összes VPN-átjárója számára, csak egy VPN-kapcsolat lehet két Azure Stack rendszer között. Ez a korlátozás arra az esetre is vonatkozik, ha egynél több helyek közötti VPN-kapcsolatot csatlakoztat egy olyan VPN-átjáróhoz, amely egyetlen IP-címet használ. Azure Stack nem engedélyezi egynél több helyi hálózati átjáró erőforrás létrehozását ugyanazzal az IP-címmel.

## <a name="next-steps"></a>További lépések

* [VPN gateway konfigurációs beállításairól az Azure Stackhez](azure-stack-vpn-gateway-settings.md)
