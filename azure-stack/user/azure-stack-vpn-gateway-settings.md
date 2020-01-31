---
title: A VPN-átjáró beállításainak konfigurálása Azure Stack hubhoz
description: Az Azure Stack hub VPN Gateway-beállításainak ismertetése és konfigurálása.
author: sethmanheim
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: b230c78811e79e7a04114b77a2fcacd1b2a2fc9c
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884119"
---
# <a name="configure-vpn-gateway-settings-for-azure-stack-hub"></a>A VPN-átjáró beállításainak konfigurálása Azure Stack hubhoz

A VPN Gateway olyan virtuális hálózati átjáró, amely titkosított forgalmat küld Azure Stack hub és egy távoli VPN-átjáró között a virtuális hálózat között. A távoli VPN-átjáró lehet az Azure-ban, egy eszköz az adatközpontban vagy egy másik helyen lévő eszköz. Ha van hálózati kapcsolat a két végpont között, létrehozhat egy biztonságos helyek közötti (S2S) VPN-kapcsolatot a két hálózat között.

A VPN Gateway-kapcsolatok több erőforrás konfigurációján alapulnak, amelyek mindegyike konfigurálható beállításokat tartalmaz. Ez a cikk a Resource Manager-alapú üzemi modellben létrehozott virtuális hálózatok VPN Gateway-hez kapcsolódó erőforrásait és beállításait ismerteti. A [VPN Gateway for Azure stack hub](azure-stack-vpn-gateway-about-vpn-gateways.md)-hoz kapcsolódó összes csatlakoztatási megoldáshoz leírásokat és topológiai diagramokat talál.

## <a name="vpn-gateway-settings"></a>VPN-átjáró beállításai

### <a name="gateway-types"></a>Átjárótípusok

Minden Azure Stack hub virtuális hálózat egyetlen virtuális hálózati átjárót támogat, amelynek a **VPN**típusúnak kell lennie. Ez a támogatás eltér az Azure-tól, amely támogatja a további típusokat.

Amikor létrehoz egy virtuális hálózati átjárót, meg kell győződnie arról, hogy az átjáró típusa helyes a konfigurációhoz. A VPN-átjáróhoz a `-GatewayType Vpn` jelző szükséges; például:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

### <a name="gateway-skus"></a>Gateway termékváltozatok

Virtuális hálózati átjáró létrehozásakor meg kell adnia a használni kívánt átjáró-SKU-t. Válassza ki a számítási feladatok, a teljesítmény, a funkciók és a szolgáltatói szerződés igényeinek megfelelő termékváltozatokat.

Azure Stack hub a következő táblázatban látható VPN Gateway SKU-ket kínálja:

| | VPN Gateway átviteli sebesség |VPN-átjáró maximális IPsec-alagutak |
|-------|-------|-------|
|**Alapszintű SKU**  | 100 Mb/s  | 20    |
|**Szabványos SKU**   | 100 Mb/s  | 20 |
|**Nagy teljesítményű SKU** | 200 Mb/s | 10 |

### <a name="resizing-gateway-skus"></a>Átjárók átméretezése

Az Azure Stack hub nem támogatja az SKU-nak a támogatott örökölt SKU-ból való átméretezését.

Hasonlóképpen, Azure Stack hub nem támogatja az átméretezést egy támogatott örökölt SKU-ból (**Alapszintű**, **standard**és **HighPerformance**) az Azure által támogatott újabb SKU-ra (**VpnGw1**, **VpnGw2**és **VpnGw3**).

### <a name="configure-the-gateway-sku"></a>Az átjáró SKU konfigurálása

#### <a name="azure-stack-hub-portal"></a>Azure Stack hub-portál

Ha a Azure Stack hub-portál használatával hoz létre Resource Manager virtuális hálózati átjárót, akkor a legördülő listából kiválaszthatja az átjáró SKU-t. A beállítások az átjáró típusa és a kiválasztott VPN-típusnak felelnek meg.

#### <a name="powershell"></a>PowerShell

A következő PowerShell-példa a `-GatewaySku` paramétert adja meg **standardként**:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>Kapcsolattípusok

A Resource Manager-alapú üzemi modellben minden konfigurációhoz egy adott virtuális hálózati átjáró kapcsolódási típus szükséges. A `-ConnectionType` elérhető Resource Manager PowerShell-értékek az **IPSec**.

A következő PowerShell-példában létrejön egy S2S-kapcsolat, amely az IPsec-kapcsolat típusát igényli:

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>VPN-típusok

Amikor létrehoz egy VPN Gateway-konfigurációhoz tartozó virtuális hálózati átjárót, meg kell adnia egy VPN-típust. A választott VPN-típus a létrehozni kívánt kapcsolati topológiától függ. A VPN-típus az Ön által használt hardvertől is függhet. A S2S-konfigurációkhoz VPN-eszköz szükséges. Egyes VPN-eszközök csak bizonyos VPN-típusokat támogatnak.

> [!IMPORTANT]  
> A Azure Stack hub jelenleg csak az Útválasztás-alapú VPN-típust támogatja. Ha az eszköz csak a házirend-alapú VPN-eket támogatja, akkor a Azure Stack hub eszközeivel létesített kapcsolatok nem támogatottak.  
>
> Emellett az Azure Stack hub jelenleg nem támogatja a házirend-alapú forgalmi választókat az útvonal-alapú átjárók esetében, mert az egyéni IPSec/IKE-házirend konfigurációja nem támogatott.

* **Házirendalapú**: a házirend-alapú VPN-ek az IPSec-alagutakon keresztül titkosítják és irányítják a csomagokat a helyszíni hálózat és az Azure stack hub VNet közötti címtartomány-kombinációkkal konfigurált IPSec-házirendek alapján. A házirend vagy a forgalmi választó általában egy hozzáférési lista a VPN-eszköz konfigurációjában.

  >[!NOTE]
  >A **házirendalapú** az Azure-ban, de nem Azure stack központban támogatott.

* **Útvonalalapú**: az Útválasztás-alapú VPN-ek az IP-továbbítás vagy az útválasztási táblázatban konfigurált útvonalakat használják a csomagok megfelelő bújtatási interfészekhez való közvetlen küldéséhez. Az alagútkapcsolatok ezután titkosítják vagy visszafejtik az alagutakba bemenő vagy onnan kijövő csomagokat. A **útvonalalapú** VPN-EK házirendje vagy forgalmi választója bármilyen módon van konfigurálva (vagy használjon helyettesítő kártyát). Alapértelmezés szerint nem módosíthatók. A **útvonalalapú** VPN-típus értéke **útvonalalapú**.

A következő PowerShell-példa a **útvonalalapú**`-VpnType` adja meg. Amikor létrehoz egy átjárót, meg kell győződnie arról, hogy a `-VpnType` helyesek a konfigurációhoz.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>Az átjáróra vonatkozó követelmények

A következő táblázat a VPN-átjárók követelményeit sorolja fel.

| |Házirendalapú alapszintű VPN Gateway | Útválasztó-alapú alapszintű VPN Gateway | Útválasztó-alapú standard VPN Gateway | Útválasztó-alapú nagy teljesítményű VPN Gateway|
|--|--|--|--|--|
| **Helyek közötti kapcsolat (S2S-kapcsolat)** | Nem támogatott | Útválasztó-alapú VPN-konfiguráció | Útválasztó-alapú VPN-konfiguráció | Útválasztó-alapú VPN-konfiguráció |
| **Hitelesítési módszer**  | Nem támogatott | Előmegosztott kulcs S2S-kapcsolathoz  | Előmegosztott kulcs S2S-kapcsolathoz  | Előmegosztott kulcs S2S-kapcsolathoz  |
| **S2S-kapcsolatok maximális száma**  | Nem támogatott | 20 | 20| 10|
|**Aktív Útválasztás támogatása (BGP)** | Nem támogatott | Nem támogatott | Támogatott | Támogatott |

### <a name="gateway-subnet"></a>Átjáró alhálózata

A VPN-átjáró létrehozása előtt létre kell hoznia egy átjáró-alhálózatot. Az átjáró-alhálózat azon IP-címekkel rendelkezik, amelyeket a Virtual Network Gateway-beli virtuális gépek és szolgáltatások használnak. A virtuális hálózati átjáró létrehozásakor az átjáróként működő virtuális gépek üzembe helyezése az átjáró alhálózatán történik, és a VPN-átjáró szükséges beállításaival van konfigurálva. Ne helyezzen üzembe mást (például további virtuális gépeket) az átjáró-alhálózatra.

>[!IMPORTANT]
>A megfelelő működéshez az átjáró-alhálózat neve **GatewaySubnet** kell legyen. Azure Stack hub ezzel a névvel azonosítja azt az alhálózatot, amelybe telepíteni kívánja a virtuális hálózati átjáró virtuális gépeket és szolgáltatásokat.

Az átjáróalhálózat létrehozásakor meg kell adnia, hogy hány IP-címet tartalmaz az alhálózat. Az átjáró-alhálózat IP-címei az átjáró virtuális gépei és az átjáró szolgáltatások számára vannak lefoglalva. Egyes konfigurációknak a többinél nagyobb számú IP-címre van szükségük. Tekintse meg a létrehozni kívánt konfiguráció utasításait, és ellenőrizze, hogy a létrehozni kívánt átjáró-alhálózat megfelel-e a követelményeknek.

Emellett győződjön meg arról, hogy az átjáró-alhálózat elegendő IP-címmel rendelkezik a további jövőbeli konfigurációk kezeléséhez. Bár létrehozhat egy átjáró-alhálózatot kisebb as/29-ként is, javasoljuk, hogy hozzon létre egy/28 vagy nagyobb (/28,/27,/26) átjáró-alhálózatot, és így tovább.) Így ha a jövőben új funkciókat ad hozzá, nem kell lebontania az átjárót, majd törölnie és újból létre kell hoznia az átjáró-alhálózatot, hogy több IP-címet engedélyezzen.

A következő Resource Manager PowerShell-példa egy **GatewaySubnet**nevű átjáró-alhálózatot mutat be. Láthatja, hogy a CIDR jelölése egy/27, amely elegendő IP-címet biztosít a jelenleg létező konfigurációkhoz.

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Amikor átjáró-alhálózatokkal dolgozik, kerülje a hálózati biztonsági csoportok (NSG) társítását az átjáró-alhálózathoz. Ha hálózati biztonsági csoportot társít ehhez az alhálózathoz, akkor a VPN-átjáró a várt módon leállhat. A hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [Mi az a hálózati biztonsági csoport?](/azure/virtual-network/virtual-networks-nsg).

### <a name="local-network-gateways"></a>Helyi hálózati átjárók

Amikor VPN Gateway-konfigurációt hoz létre az Azure-ban, a helyi hálózati átjáró gyakran a helyszíni helyet jelöli. Azure Stack hub-ban minden olyan távoli VPN-eszközt képvisel, amely a Azure Stack hub-on kívül található. Ez az eszköz lehet VPN-eszköz a saját adatközpontjában (vagy egy távoli adatközpontban) vagy egy Azure-beli VPN-átjárón.

Adja meg a helyi hálózati átjáró nevét, a VPN-eszköz nyilvános IP-címét, és adja meg a helyszíni helyen található címek előtagjait. Az Azure a hálózati forgalomhoz tartozó célcím előtagjait tekinti át, a helyi hálózati átjáróhoz megadott konfigurációt, és ennek megfelelően irányítja a csomagokat.

Ez a PowerShell-példa egy új helyi hálózati átjárót hoz létre:

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Előfordulhat, hogy módosítania kell a helyi hálózati átjáró beállításait; például a címtartomány hozzáadásakor vagy módosításakor, vagy a VPN-eszköz IP-címének megváltozásakor. További információ: [helyi hálózati átjáró beállításainak módosítása a PowerShell használatával](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>IPsec/IKE-paraméterek

Ha Azure Stack hub VPN-kapcsolatát állítja be, mindkét végponton konfigurálnia kell a kapcsolódást. Ha VPN-kapcsolatot konfigurál Azure Stack hub és egy hardvereszköz, például egy olyan kapcsoló vagy útválasztó között, amely VPN-átjáróként működik, akkor az eszköz további beállítások megadását kéri.

Az Azure-tól eltérően, amely több ajánlatot is támogat kezdeményezőként és válaszadóként, Azure Stack hub alapértelmezés szerint csak egy ajánlatot támogat. Ha más IPSec/IKE-beállításokat kell használnia a VPN-eszköz használatához, több beállítás is elérhető a kapcsolat manuális konfigurálásához. További információ: [IPSec/IKE-szabályzat konfigurálása helyek közötti VPN-kapcsolatokhoz](azure-stack-vpn-s2s.md).

### <a name="ike-phase-1-main-mode-parameters"></a>Az IKE 1. fázis (Elsődleges mód) paraméterei

| Tulajdonság              | Value (Díj)|
|-|-|
| IKE verziószám           | IKEv2 |
|Diffie-Hellman Group   | ECP384 |
| Hitelesítési módszer | Előre megosztott kulcs |
|Titkosító és kivonatoló algoritmus | AES256, SHA384 |
|SA élettartama (Idő)     | 28 800 másodperc|

### <a name="ike-phase-2-quick-mode-parameters"></a>Az IKE 2. fázis (Gyors mód) paraméterei

| Tulajdonság| Value (Díj)|
|-|-|
|IKE verziószám |IKEv2 |
|Titkosítási & kivonatoló algoritmusok (titkosítás)     | GCMAES256|
|Titkosítási & kivonatolási algoritmusok (hitelesítés) | GCMAES256|
|SA élettartama (Idő)  | 27 000 másodperc  |
|SA élettartama (kilobájt) | 33 553 408     |
|Sérülés utáni titkosságvédelem (PFS) | ECP384 |
|Kapcsolat megszakadásának észlelése | Támogatott|  

## <a name="next-steps"></a>Következő lépések

* [Összekapcsolás a ExpressRoute használatával](../operator/azure-stack-connect-expressroute.md)
