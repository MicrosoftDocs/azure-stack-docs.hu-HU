---
title: VPN gateway beállításairól az Azure Stackhez |} A Microsoft Docs
description: A VPN-átjárók használhatja az Azure Stack beállítások ismertetése.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 83fa2e96a7cd956c050efa33ab6e9564b1834e93
ms.sourcegitcommit: 07c51a03f07a6a3ee2721aa942d31a7a4c6a339b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2019
ms.locfileid: "67028292"
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>VPN gateway konfigurációs beállításairól az Azure Stackhez

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

VPN-átjáró olyan virtuális hálózati átjáró, amely a virtuális hálózat az Azure Stackben és a távoli VPN gateway titkosított adatforgalmat továbbít. A távoli VPN-átjáró lehet az Azure, az adatközpontban található eszköz vagy egy eszközt egy másik helyen. Ha a két végpontok közötti hálózati kapcsolat, a két hálózat közötti biztonságos Site-to-Site (S2S) VPN-kapcsolatot is létrehozhat.

VPN gateway-kapcsolat támaszkodik a konfiguráció több erőforrást, amelyek mindegyike tartalmazza a konfigurálható beállítások. Ez a cikk bemutatja az erőforrások és a Resource Manager-alapú üzemi modellben létrehozott virtuális hálózat VPN-átjáró szoftverközponthoz kapcsolódó beállításokat. Leírások és topológia-diagramok megtalálhatja az összes kapcsolat-megoldás [információk a VPN Gateway az Azure Stack](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>A VPN gateway beállításairól

### <a name="gateway-types"></a>Átjárótípusok

Minden egyes Azure Stack virtuális hálózat egyetlen virtuális hálózati átjáró, amely a típusúnak kell lennie támogatja **Vpn**.  Ez a támogatás az Azure-ban, amely támogatja a további eltér.

A virtuális hálózati átjáró létrehozásakor győződjön meg arról, hogy az átjáró típusa megfeleljen a konfigurációnak. VPN-átjáró szükséges a `-GatewayType Vpn` jelzőt; például:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>Gateway termékváltozatok

A virtuális hálózati átjáró létrehozásakor meg kell adnia az átjáró használni kívánt Termékváltozatot. Válassza ki a számítási feladatok, a teljesítmény, a funkciók és a szolgáltatói szerződés igényeinek megfelelő termékváltozatokat.

Az Azure stackhez VPN-átjáró SKU-k az alábbi táblázatban látható.

| | VPN gateway teljesítménye |VPN-átjáró maximális IPsec-alagútjainak |
|-------|-------|-------|
|**Alapszintű Termékváltozat**  | 100 Mbps  | 20    |
|**A standard Termékváltozat**   | 100 Mbps  | 20 |
|**Nagy teljesítményű Termékváltozat** | 200 Mbps | 10 |

### <a name="resizing-gateway-skus"></a>Az átjárók Termékváltozatainak átméretezése

Az Azure Stack nem támogatja a termékváltozatok között a támogatott örökölt termékváltozatok átméretezése.

Ehhez hasonlóan az Azure Stack nem támogatja egy támogatott régebbi termékváltozatból átméretezése (**alapszintű**, **Standard**, és **HighPerformance**) egy újabb termékváltozatra, Azure által támogatott (**VpnGw1**, **VpnGw2**, és **VpnGw3**).

### <a name="configure-the-gateway-sku"></a>Az átjáró-Termékváltozatot konfigurálása

#### <a name="azure-stack-portal"></a>Az Azure Stack portálon

Ha az Azure Stack portálon használatával a Resource Manager virtuális hálózati átjáró létrehozása, az átjáró-Termékváltozatot is választhat a legördülő lista használatával. A beállítások felelnek meg az átjáró típusa és a VPN-típust választja.

#### <a name="powershell"></a>PowerShell

Az alábbi PowerShell-példa meghatározza az `-GatewaySku` , **VpnGw1**:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>Kapcsolattípusok

A Resource Manager-alapú üzemi modellben az egyes konfigurációkhoz egy adott virtuális hálózati átjárójának kapcsolattípusa van szükség. A `-ConnectionType` lehetséges Resource Manager PowerShell-értékei a következők:

* **IPsec**

   A következő PowerShell-példa egy S2S kapcsolat jön létre, amely az IPsec kapcsolati típust igényli:

   ```powershell
   New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
   -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

### <a name="vpn-types"></a>VPN-típusok

A VPN-átjáró konfigurálása a virtuális hálózati átjáró létrehozásakor meg kell adnia egy VPN-típussal. A VPN-típust választja a létrehozni kívánt kapcsolat topológia függ. Egy VPN-típussal is függ a hardvert használ. S2S-konfigurációk VPN-eszközre van szükség. VPN-eszközök csak egy meghatározott VPN-típust támogatja.

> [!IMPORTANT]  
> Jelenleg az Azure Stack csak az útvonal-alapú VPN-típust támogatja. Ha az eszköz csak a házirend-alapú VPN-eket támogatja, az Azure Stack az adott eszközökre való kapcsolatok nem támogatottak.  
>
> Emellett Azure Stack nem támogatja a házirend alapú Forgalomválasztóinak használatával útvonal-alapú átjárók esetében jelenleg, mert az egyéni IPSec/IKE-házirend konfigurációk nem támogatottak.

* **Házirendalapú**: Házirendalapú VPN-eket titkosítják és irányítják a csomagokat a helyszíni hálózat és az Azure Stack VNet közötti címelőtag-kombinációkkal konfigurált IPsec-házirendek alapján IPsec-alagutakon keresztül. A házirend vagy forgalomválasztó, az általában a VPN-eszköz konfigurálása a hozzáférés-lista.

  >[!NOTE]
  >**Házirendalapú** támogatott az Azure-ban, de nem az Azure Stackben.

* **Útvonalalapú**: Útvonalalapú VPN-eket használja az IP-továbbítás vagy útválasztási táblázat, hogy a csomagokat a megfelelő alagútkapcsolatokhoz irányítsák a konfigurált útvonalak. Az alagútkapcsolatok ezután titkosítják vagy visszafejtik az alagutakba bemenő vagy onnan kijövő csomagokat. A házirend vagy forgalomválasztó, a **RouteBased** VPN-bármely-kként vannak konfigurálva (vagy helyettesítő karakterek használata). Alapértelmezés szerint ezek nem lehet módosítani. Az érték egy **RouteBased** VPN-típus **RouteBased**.

Az alábbi PowerShell-példa meghatározza az `-VpnType` , **RouteBased**. Egy átjáró létrehozásakor meg kell győződnie arról, hogy a `-VpnType` megfeleljen a konfigurációnak.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>Az átjáróra vonatkozó követelmények

A következő táblázat felsorolja a VPN-átjárók követelményei.

| |Házirendalapú alapszintű VPN Gateway | Alapszintű Útvonalalapú VPN-átjáró | Standard Útvonalalapú VPN-átjáró | Útvonalalapú nagy teljesítményű VPN Gateway|
|--|--|--|--|--|
| **Helyek közötti kapcsolat (S2S-kapcsolatokhoz)** | Nem támogatott | Útvonalalapú VPN-konfiguráció | Útvonalalapú VPN-konfiguráció | Útvonalalapú VPN-konfiguráció |
| **Hitelesítési módszer**  | Nem támogatott | Előre megosztott kulcs S2S-kapcsolatokhoz  | Előre megosztott kulcs S2S-kapcsolatokhoz  | Előre megosztott kulcs S2S-kapcsolatokhoz  |
| **S2S-kapcsolatok maximális száma**  | Nem támogatott | 20 | 20| 10|
|**Aktív útválasztás-támogatás (BGP)** | Nem támogatott | Nem támogatott | Támogatott | Támogatott |

### <a name="gateway-subnet"></a>Átjáró alhálózata

Mielőtt létrehozna egy VPN-átjárót, létre kell hoznia egy átjáró-alhálózatot. Az átjáró-alhálózat tartalmaz IP-címek a virtuális hálózati átjáró virtuális gépek és szolgáltatások. A virtuális hálózati átjáró létrehozásakor átjáróként működő virtuális gépekhez az átjáró-alhálózat telepítve és konfigurálva a VPN-átjáró szükséges beállításokat. Ne telepítsen minden más (például további VM-EK) az átjáró-alhálózathoz.

>[!IMPORTANT]
>A megfelelő működéshez az átjáró-alhálózat neve **GatewaySubnet** kell legyen. Az Azure Stack ezt a nevet használja azt az alhálózatot, melyben szeretné üzembe helyezni a virtuális hálózati átjáró virtuális gépek és szolgáltatások azonosításához.

Az átjáróalhálózat létrehozásakor meg kell adnia, hogy hány IP-címet tartalmaz az alhálózat. Az átjáró-alhálózat IP-címekkel kiosztott az átjáróként működő virtuális gépekhez és átjárószolgáltatásokat. Egyes konfigurációknak a többinél nagyobb számú IP-címre van szükségük. Tekintse meg a konfigurációt, amelyet szeretne létrehozni, és győződjön meg arról, hogy szeretne létrehozni az átjáró-alhálózat megfelel ezeknek a követelményeknek megfelelő utasításokat.

Ezenkívül győződjön meg arról, hogy az átjáróalhálózat elég IP-cím további jövőbeni konfigurációk kezelésére van. Is hozhat létre egy átjáró-alhálózat kisebb, akár/29 méretű, azt javasoljuk, hogy hozzon létre egy átjáróalhálózatot/28-as vagy nagyobb (/ 28, / 27, / 26 stb.) Így ha funkciókat adhat a jövőben nem kell az átjáró üzemen, majd törölje és hozza létre újra az átjáró-alhálózat, hogy a további IP-címek.

A következő Resource Manager PowerShell-példa bemutatja egy átjáróalhálózat nevű **GatewaySubnet**. Láthatja, hogy a CIDR-jelölésrendszer/27-es, amely lehetővé teszi, hogy elegendő IP-címet, amely a jelenleg létező legtöbb konfiguráció esetében.

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Amikor átjáró-alhálózatokkal dolgozik, kerülje a hálózati biztonsági csoportok (NSG) társítását az átjáró-alhálózathoz. Ennek az alhálózatnak a hálózati biztonsági csoport társítása okozhat a VPN gateway nem a várt módon működik. Hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [Mi az a hálózati biztonsági csoport?](/azure/virtual-network/virtual-networks-nsg).

### <a name="local-network-gateways"></a>Helyi hálózati átjárók

Egy VPN-átjáró konfigurálása az Azure-beli létrehozásakor az a helyi hálózati átjáró gyakran a helyszíni helyet jelöli. Az Azure Stackben minden olyan távoli VPN-eszköz, amely az Azure Stack kívül jelöli. Ez lehet az Adatközpont (vagy egy távoli adatközpontban) VPN-eszközt, vagy a VPN-átjáró, az Azure-ban.

Nevezze el a helyi hálózati átjárót, a VPN-eszköz nyilvános IP-címét, és adja meg a címelőtagokat a helyszíni helyen található. Az Azure megvizsgálja a hálózati forgalmat a cél-címelőtagjainak, consults szintűre frissül, hogy a helyi hálózati átjáró a megadott konfigurációval, és ennek megfelelően irányítja a csomagokat.

A következő PowerShell-példa egy új helyi hálózati átjárót hoz létre:

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Néha szüksége; a helyi hálózati átjáró beállításainak módosítása például ha felvétele vagy módosítása a címtartomány, vagy ha a VPN-eszköz IP-címe megváltozik. Lásd: [PowerShell-lel a helyi hálózati átjáró beállításainak módosítása](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>IPsec/IKE-paraméterek

Amikor beállít egy VPN-kapcsolatot az Azure Stack, konfigurálnia kell a kapcsolat mindkét végén. Ha konfigurál egy VPN-kapcsolatot az Azure Stack és a egy hardvereszközhöz, például egy kapcsoló vagy egy VPN-átjáróként működő útválasztó között, a eszköz kérheti a további beállításokat.

Azure-ban, amely támogatja a több ajánlattal is egy kezdeményező és egy válaszadó, eltérően az Azure Stack alapértelmezés szerint támogatja a csak egyszer veheti igénybe.  A VPN-eszköz használata a különböző IPSec/IKE-beállítások használatával van szüksége, van-e további beállításokat manuálisan konfigurálhatja a kapcsolat számára elérhető.  További információ: [site-to-site VPN-kapcsolatok konfigurálása IPsec/IKE-házirend](azure-stack-vpn-s2s.md).

### <a name="ike-phase-1-main-mode-parameters"></a>Az IKE 1. fázis (Elsődleges mód) paraméterei

| Tulajdonság              | Érték|
|-|-|
| IKE verziószám           | IKEv2 |
|Diffie-Hellman Group   | 2. csoport (1024 bites) |
| Hitelesítési módszer | Előre megosztott kulcs |
|Titkosító és kivonatoló algoritmus | AES256, SHA256 |
|SA élettartama (Idő)     | 28 800 másodperc|

### <a name="ike-phase-2-quick-mode-parameters"></a>Az IKE 2. fázis (Gyors mód) paraméterei

| Tulajdonság| Érték|
|-|-|
|IKE verziószám |IKEv2 |
|Titkosító és kivonatoló algoritmus (titkosítás)     | GCMAES256|
|Titkosító és kivonatoló algoritmus (hitelesítés) | GCMAES256|
|SA élettartama (Idő)  | 27 000 másodperc  |
|SA élettartama (kilobájt) | 33,553,408     |
|Sérülés utáni titkosságvédelem (PFS) |Egyik sem (lásd az 1. megjegyzés) |
|Kapcsolat megszakadásának észlelése | Támogatott|  

* *1. Megjegyzés:*  1807 verziónál régebbi az Azure Stack esetében a teljes előre sérülés utáni Titkosságvédelmi (PFS) használt PFS2048 érték.

## <a name="next-steps"></a>További lépések

* [Csatlakozás ExpressRoute használatával](../operator/azure-stack-connect-expressroute.md)
