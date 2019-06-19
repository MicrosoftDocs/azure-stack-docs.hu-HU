---
title: Tudnivalók a VPN gatewayről az Azure Stackhez |} A Microsoft Docs
description: Ismerje meg, és konfigurálja a VPN-átjárók használhatja az Azure Stack használatával.
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
ms.openlocfilehash: 0df791c6eb9a898c5263b2c628899b512d49601c
ms.sourcegitcommit: c4507a100eadd9073aed0d537d054e394b34f530
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67198648"
---
# <a name="about-vpn-gateway-for-azure-stack"></a>Tudnivalók az Azure Stackhez VPN-átjáró

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Hálózati forgalom küldése az Azure virtuális hálózat és a helyszíni hely között, előtt a virtuális hálózat egy virtuális hálózati átjárót kell létrehoznia.

A VPN-átjáró a virtuális hálózati átjárók egy olyan típusa, amely titkosított adatforgalmat továbbít nyilvános kapcsolaton keresztül. VPN-átjárók segítségével biztonságosan a virtuális hálózat az Azure Stackben és a egy virtuális hálózat között forgalmat küldeni az Azure-ban. Biztonságosan egy virtuális hálózat és a egy VPN-eszközhöz van csatlakoztatva egy másik hálózat közötti forgalmat is küldhet.

Virtuális hálózati átjáró létrehozásakor megadja a létrehozni kívánt átjárótípust. Az Azure Stack is támogatja a virtuális hálózati átjáró egyfajta: a **Vpn** típusa.

Mindegyik virtuális hálózat kettő virtuális hálózati átjáróval rendelkezhet, de típusonként csak eggyel. A kiválasztott beállításoktól függően több kapcsolatot is létrehozhat egyetlen VPN-átjáróhoz. Ilyen például, a többhelyes csatlakozás konfiguráció.

Létrehozása, és az Azure Stackhez VPN-átjárók konfigurálása előtt tekintse át a [Azure Stack hálózati szempontjai](azure-stack-network-differences.md) megtudhatja, hogyan Azure stack-konfigurációk különböznek egymástól, az Azure-ból.

>[!NOTE]
>Az Azure-ban a VPN Gateway SKU úgy dönt, a sávszélesség átviteli sebesség az átjáró csatlakoztatott összes kapcsolat szét lehetnek osztva. Az Azure Stackben azonban a sávszélesség a VPN Gateway Termékváltozat értéken egyes kapcsolati erőforrás, amely az átjáró csatlakozik.
>
> Példa:
>
> * Az Azure-ban az alapszintű VPN gateway SKU körülbelül 100 MB/s összesített átviteli sebességre képes kezelni. Létrehozhat két adott VPN-átjáró kapcsolatot, és a egy kapcsolat használ 50 MB/s sávszélesség, majd 50 MB/s-e a többi kapcsolat számára elérhető.
> * Az Azure Stackben **minden kapcsolat** alapszintű VPN-átjáróhoz Termékváltozat le egyet azon 100 MB/s átviteli sebesség.

## <a name="configuring-a-vpn-gateway"></a>VPN-átjáró konfigurálása

VPN gateway-kapcsolat meghatározott beállításokkal konfigurált számos erőforrás támaszkodik. Ezek az erőforrások többsége külön konfigurálható, de bizonyos esetekben kell ezeket konfigurálni a meghatározott sorrendben.

### <a name="settings"></a>Beállítások

A beállításokat fogja tartalmazni az egyes erőforrások létfontosságúak a sikeres kapcsolat létrehozásához.

Egyéni erőforrások és a VPN Gateway beállításaival kapcsolatos további információkért lásd: [kapcsolatos VPN gateway beállításairól az Azure Stack](azure-stack-vpn-gateway-settings.md). Ez a cikk segít megérteni:

* Átjáró VPN-típusok és kapcsolattípusok.
* Átjáró-alhálózatokkal, a helyi hálózati átjárót és a többi erőforrás-beállítások, amelyeket érdemes figyelembe venni.

### <a name="deployment-tools"></a>Üzembe helyezési eszközök

Létrehozhat és konfigurálhat erőforrásokat egy konfigurációs eszközt, például az Azure portal használatával. Később, előfordulhat, hogy váltson egy másik eszközre, például a PowerShell használatával a további erőforrások konfigurálásához, vagy adott esetekben a létező erőforrások módosításához. Jelenleg minden erőforrást és erőforrás-beállítás nem lehet konfigurálni az Azure Portalon. Az egyes kapcsolati topológiákhoz tartozó cikkekben lévő utasítások egyértelműsítik, hogy mikor van szükség egy speciális konfigurációs eszközre.

## <a name="connection-topology-diagrams"></a>Kapcsolati topológia-diagramok

Nincsenek elérhető VPN-átjárós kapcsolatokhoz különböző konfigurációk. Határozza meg, melyik konfiguráció felel meg a legjobban az igényeinek. Az alábbiakban tekintheti meg a következő VPN gateway-kapcsolatok információt és topológia-diagramokat:

* Elérhető üzemi modell
* Elérhető konfigurációs eszközök
* Hivatkozások, amelyek közvetlenül egy cikkre mutatnak, ha van ilyen

Az ábrák és leírások az alábbi szakaszok segítségével válassza ki az igényeinek megfelelő kapcsolati topológiát. Az ábrák bemutatják a fő alapvető topológiákat, de lehetséges összetettebb konfigurációk használata a diagramokat útmutatásként hozhat létre.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Helyek közötti és többhelyes (IPsec/IKE VPN-alagút)

### <a name="site-to-site"></a>Helyek közötti kapcsolat

A *site-to-site* (S2S) VPN gateway-kapcsolat nem kapcsolat (IKEv2) IPsec/IKE VPN-alagúton keresztül. Ez a kapcsolattípus egy VPN-eszköz, amely a helyszínen található, és van rendelve egy nyilvános IP-címet igényel. Ez az eszköz nem található a NAT mögötti. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók.

![Site-to-site VPN kapcsolat konfigurációja – példa](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Többhelyes kapcsolat

A *többhelyes* kapcsolat a helyek közötti kapcsolat egy változata. A virtuális hálózati átjáróról több VPN-kapcsolatot hoz létre, amelyek általában több helyszíni helyhez csatlakoznak. Ha több kapcsolattal dolgozik, egy útvonalalapú VPN-típust (más néven dinamikus átjáró klasszikus virtuális hálózatok használatakor) kell használnia. Mivel minden virtuális hálózat csak egy VPN-átjáróval rendelkezhet, az átjárón keresztüli összes kapcsolat osztozik a rendelkezésre álló sávszélességen.

![Azure VPN Gateway többhelyes kapcsolat – példa](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>Gateway termékváltozatok

Az Azure stack-beli virtuális hálózati átjáró létrehozásakor meg kell adnia a használni kívánt Termékváltozatot. VPN gateway a következő termékváltozatok támogatottak:

* Alapszintű
* Standard
* Nagy teljesítmény

Amikor kiválaszt egy átjáró magasabb szintű Termékváltozatát, például az alapszintű Standard vagy nagy teljesítményű Standard vagy Basic keresztül, az átjáró kiosztott több processzort és hálózati sávszélességet. Ennek eredményeképpen az átjáró nagyobb hálózati átviteli sebesség a virtuális hálózat is támogatja.

Az Azure Stack nem támogatja az Ultranagy teljesítményű átjáró-Termékváltozatot, kizárólag az Express Route használt.

A Termékváltozat kiválasztásakor, vegye figyelembe a következőket:

* Az Azure Stack nem támogatja a házirendalapú átjárók.
* Az alapszintű termékváltozat nem támogatott a Border Gateway Protocol (BGP).
* Az ExpressRoute-VPN-átjáró egyidejű konfigurációk nem támogatottak az Azure Stackben.

## <a name="gateway-availability"></a>Átjáró rendelkezésre állása

Magas rendelkezésre állási forgatókönyvek esetén csak akkor konfigurálható, az a **nagy teljesítményű átjáróhoz** kapcsolat Termékváltozat. Ellentétben az Azure-ban, mind az aktív/aktív, mind az aktív/passzív konfigurációt keresztül a rendelkezésre állást biztosít, amely az Azure Stack csak az aktív/passzív konfigurációt támogatja. 

### <a name="failover"></a>Feladatátvétel

Nincsenek 3 több-bérlős átjáró infrastruktúra virtuális gépein az Azure Stackben. Ezek a virtuális gépek két aktív módban van, és a harmadik redundáns módban van. Aktív virtuális gépek engedélyezze azokat a VPN-kapcsolatok létrehozása, és a redundáns virtuális gépek csak feladatátvétel esetén a VPN-kapcsolatok fogad el. Ha egy aktív átjáróként működő virtuális gép elérhetetlenné válik, a VPN-kapcsolat átadja a feladatokat a redundáns virtuális Gépet rövid időn (néhány másodperces) kapcsolat veszteség.

## <a name="estimated-aggregate-throughput-by-sku"></a>A termékváltozat becsült összesített átviteli sebessége

A következő táblázatban az átjárótípusok és a becsült összesített átviteli átjárók Termékváltozata alapján:

|| VPN Gateway teljesítménye *(1)* | VPN Gateway maximális IPsec-alagútjainak *(2)* |
|-------|-------|-------|
|**Alapszintű Termékváltozat** ***(3)*** | 100 Mbps | 20 |
|**A standard Termékváltozat** | 100 Mbps | 20 |
|**Nagy teljesítményű Termékváltozat** | 200 Mbps | 10 |

**Tábla megjegyzések:**

*Megjegyzés: (1)* – VPN átviteli sebesség nem egy garantált átviteli létesítmények közötti kapcsolatok az interneten keresztül. Ez a maximális mérhető teljesítmény.  
*Megjegyzés (2)* -alagutak maximális száma az összes összes előfizetéshez tartozó Azure Stack üzemelő példányonként.  
*Megjegyzés (3)* – az alapszintű Termékváltozat nem támogatja a BGP-útválasztás.

>[!NOTE]
>Csak egy helyek közötti VPN-kapcsolat hozható létre az Azure Stack két telepítése között. Azt a platformot, csak lehetővé teszi, hogy az azonos IP-cím egyetlen VPN-kapcsolat korlátozása miatt nem lehetséges. Azure Stack kihasználja a több-bérlős átjáró, amely az összes VPN-átjárók egyetlen nyilvános IP-cím használja az Azure Stack rendszerben, mert csak egy VPN-kapcsolatot két Azure Stack-rendszereket között lehet. Ez a korlátozás is vonatkozik egynél több helyek közötti VPN-kapcsolat csatlakozik minden VPN-átjáró, amely egyetlen IP-címet használ. Az Azure Stack nem engedélyez egynél több helyi hálózati átjáró erőforrás létrehozandó azonos IP-cím használatával.

## <a name="next-steps"></a>További lépések

* [VPN gateway konfigurációs beállításairól az Azure Stackhez](azure-stack-vpn-gateway-settings.md)
