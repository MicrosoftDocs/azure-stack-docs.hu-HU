---
title: Configure VPN gateway settings for Azure Stack | Microsoft Docs
description: Learn about and configure VPN gateways settings for Azure Stack.
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
ms.date: 10/03/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: e6d7f2d46a578bbbc8527a5e69f441ec12f38b01
ms.sourcegitcommit: ac7d98a2b58442e82798022d69ebfae6616a225f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239308"
---
# <a name="configure-vpn-gateway-settings-for-azure-stack"></a>Configure VPN gateway settings for Azure Stack

*Applies to: Azure Stack integrated systems and Azure Stack Development Kit*

A VPN gateway is a type of virtual network gateway that sends encrypted traffic between your virtual network in Azure Stack and a remote VPN gateway. The remote VPN gateway can be in Azure, a device in your datacenter, or a device on another site. If there is network connectivity between the two endpoints, you can establish a secure Site-to-Site (S2S) VPN connection between the two networks.

A VPN gateway connection relies on the configuration of multiple resources, each of which contains configurable settings. This article describes the resources and settings that relate to a VPN gateway for a virtual network that you create in the Resource Manager deployment model. You can find descriptions and topology diagrams for each connection solution in [About VPN Gateway for Azure Stack](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>VPN gateway settings

### <a name="gateway-types"></a>Átjárótípusok

Each Azure Stack virtual network supports a single virtual network gateway, which must be of the type **Vpn**.  This support is different from Azure, which supports additional types.

When you create a virtual network gateway, you must make sure that the gateway type is correct for your configuration. A VPN gateway requires the `-GatewayType Vpn` flag; for example:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

### <a name="gateway-skus"></a>Gateway termékváltozatok

When you create a virtual network gateway, you must specify the gateway SKU that you want to use. Válassza ki a számítási feladatok, a teljesítmény, a funkciók és a szolgáltatói szerződés igényeinek megfelelő termékváltozatokat.

Azure Stack offers the VPN gateway SKUs shown in the following table:

| | VPN gateway throughput |VPN gateway maximum IPsec tunnels |
|-------|-------|-------|
|**Basic SKU**  | 100 Mb/s  | 20    |
|**Standard SKU**   | 100 Mb/s  | 20 |
|**High Performance SKU** | 200 Mb/s | 10 |

### <a name="resizing-gateway-skus"></a>Resizing gateway SKUs

Azure Stack does not support a resize of SKUs between the supported legacy SKUs.

Similarly, Azure Stack does not support a resize from a supported legacy SKU (**Basic**, **Standard**, and **HighPerformance**) to a newer SKU supported by Azure (**VpnGw1**, **VpnGw2**, and **VpnGw3**).

### <a name="configure-the-gateway-sku"></a>Configure the gateway SKU

#### <a name="azure-stack-portal"></a>Azure Stack portal

If you use the Azure Stack portal to create a Resource Manager virtual network gateway, you can select the gateway SKU by using the dropdown list. The options correspond to the gateway type and VPN type that you select.

#### <a name="powershell"></a>PowerShell

The following PowerShell example specifies the `-GatewaySku` parameter as **Standard**:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>Kapcsolattípusok

In the Resource Manager deployment model, each configuration requires a specific virtual network gateway connection type. The available Resource Manager PowerShell values for `-ConnectionType` are **IPsec**.

In the following PowerShell example, a S2S connection is created that requires the IPsec connection type:

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>VPN-típusok

When you create the virtual network gateway for a VPN gateway configuration, you must specify a VPN type. The VPN type that you choose depends on the connection topology that you want to create. A VPN type can also depend on the hardware that you're using. S2S configurations require a VPN device. Some VPN devices only support a certain VPN type.

> [!IMPORTANT]  
> Currently, Azure Stack only supports the route-based VPN type. If your device only supports policy-based VPNs, then connections to those devices from Azure Stack are not supported.  
>
> In addition, Azure Stack does not support using policy-based traffic selectors for route-based gateways at this time, because custom IPSec/IKE policy configurations are not supported.

* **PolicyBased**: Policy-based VPNs encrypt and direct packets through IPsec tunnels based on the IPsec policies that are configured with the combinations of address prefixes between your on-premises network and the Azure Stack VNet. The policy, or traffic selector, is usually an access list in the VPN device configuration.

  >[!NOTE]
  >**PolicyBased** is supported in Azure, but not in Azure Stack.

* **RouteBased**: Route-based VPNs use routes that are configured in the IP forwarding or routing table to direct packets to their corresponding tunnel interfaces. Az alagútkapcsolatok ezután titkosítják vagy visszafejtik az alagutakba bemenő vagy onnan kijövő csomagokat. The policy, or traffic selector, for **RouteBased** VPNs are configured as any-to-any (or use wild cards). By default, they cannot be changed. The value for a **RouteBased** VPN type is **RouteBased**.

The following PowerShell example specifies the `-VpnType` as **RouteBased**. When you create a gateway, you must make sure that the `-VpnType` is correct for your configuration.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>Az átjáróra vonatkozó követelmények

The following table lists the requirements for VPN gateways.

| |Házirendalapú alapszintű VPN Gateway | Útválasztó-alapú alapszintű VPN Gateway | Útválasztó-alapú standard VPN Gateway | Útválasztó-alapú nagy teljesítményű VPN Gateway|
|--|--|--|--|--|
| **Site-to-Site connectivity (S2S connectivity)** | Nem támogatott | Útválasztó-alapú VPN-konfiguráció | Útválasztó-alapú VPN-konfiguráció | Útválasztó-alapú VPN-konfiguráció |
| **Authentication method**  | Nem támogatott | Pre-shared key for S2S connectivity  | Pre-shared key for S2S connectivity  | Pre-shared key for S2S connectivity  |
| **Maximum number of S2S connections**  | Nem támogatott | 20 | 20| 10|
|**Active routing support (BGP)** | Nem támogatott | Nem támogatott | Támogatott | Támogatott |

### <a name="gateway-subnet"></a>Átjáró alhálózata

Before you create a VPN gateway, you must create a gateway subnet. The gateway subnet has the IP addresses that the virtual network gateway VMs and services use. When you create your virtual network gateway, gateway VMs are deployed to the gateway subnet and configured with the required VPN gateway settings. Don't deploy anything else (for example, additional VMs) to the gateway subnet.

>[!IMPORTANT]
>A megfelelő működéshez az átjáró-alhálózat neve **GatewaySubnet** kell legyen. Azure Stack uses this name to identify the subnet to which to deploy the virtual network gateway VMs and services.

Az átjáróalhálózat létrehozásakor meg kell adnia, hogy hány IP-címet tartalmaz az alhálózat. The IP addresses in the gateway subnet are allocated to the gateway VMs and gateway services. Egyes konfigurációknak a többinél nagyobb számú IP-címre van szükségük. Look at the instructions for the configuration that you want to create and verify that the gateway subnet you want to create meets those requirements.

Additionally, you should make sure your gateway subnet has enough IP addresses to handle additional future configurations. Although you can create a gateway subnet as small as /29, we recommend you create a gateway subnet of /28 or larger (/28, /27, /26, and so on.) That way, if you add functionality in the future, you do not have to tear down your gateway, then delete and recreate the gateway subnet to allow for more IP addresses.

The following Resource Manager PowerShell example shows a gateway subnet named **GatewaySubnet**. You can see the CIDR notation specifies a /27, which allows for enough IP addresses for most configurations that currently exist.

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Átjáróalhálózatokkal való munka esetén ne társítsa a hálózati biztonsági csoportot (NSG) az átjáróalhálózathoz. Associating a network security group to this subnet can cause your VPN gateway to stop functioning as expected. For more information about network security groups, see [What is a network security group?](/azure/virtual-network/virtual-networks-nsg).

### <a name="local-network-gateways"></a>Helyi hálózati átjárók

When creating a VPN gateway configuration in Azure, the local network gateway often represents your on-premises location. In Azure Stack, it represents any remote VPN device that sits outside Azure Stack. This device could be a VPN device in your datacenter (or a remote datacenter), or a VPN gateway in Azure.

You give the local network gateway a name, the public IP address of the VPN device, and specify the address prefixes that are on the on-premises location. Azure looks at the destination address prefixes for network traffic, consults the configuration that you've specified for your local network gateway, and routes packets accordingly.

This PowerShell example creates a new local network gateway:

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Sometimes you need to modify the local network gateway settings; for example, when you add or modify the address range, or if the IP address of the VPN device changes. For more info, see [Modify local network gateway settings using PowerShell](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>IPsec/IKE parameters

When you set up a VPN connection in Azure Stack, you must configure the connection at both ends. If you're configuring a VPN connection between Azure Stack and a hardware device such as a switch or router that is acting as a VPN gateway, that device might ask you for additional settings.

Unlike Azure, which supports multiple offers as both an initiator and a responder, Azure Stack supports only one offer by default. If you need to use different IPSec/IKE settings to work with your VPN device, there are more settings available to you to configure your connection manually. For more information, see [Configure IPsec/IKE policy for site-to-site VPN connections](azure-stack-vpn-s2s.md).

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
|Encryption & Hashing Algorithms (Encryption)     | GCMAES256|
|Encryption & Hashing Algorithms (Authentication) | GCMAES256|
|SA élettartama (Idő)  | 27 000 másodperc  |
|SA Lifetime (Kilobytes) | 33,553,408     |
|Sérülés utáni titkosságvédelem (PFS) | ECP384 |
|Kapcsolat megszakadásának észlelése | Támogatott|  

## <a name="next-steps"></a>Következő lépések

* [Connect using ExpressRoute](../operator/azure-stack-connect-expressroute.md)
