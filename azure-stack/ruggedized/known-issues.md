---
title: Azure Stack hub ismert problémái Azure Stack hub strapabíró
description: Ismerje meg Azure Stack hub Azure Stack hub ismert problémáit.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 12/08/2020
ms.openlocfilehash: 4b75024a1ff747d39f4f2194d0d86c3882475dee
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939915"
---
# <a name="known-issues-in-azure-stack-hub-ruggedized"></a>Azure Stack hub ismert problémái – robusztus 

Ez a cikk a Azure Stack hub ismert problémáit sorolja fel. A lista új problémák észlelése esetén frissül.

## <a name="update"></a>Frissítés

Az ismert Azure Stack hub-frissítési problémákkal kapcsolatban lásd: [frissítések hibaelhárítása Azure stack hub-ban](../operator/azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>A frissítés nem tudta telepíteni a Microsoft. AzureStack. számítás. Installer csomagot a CA virtuális gépre.

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a frissítés során egy folyamat zárolja az új tartalmat, amelyet a CA virtuális gépre kell másolni. Ha a frissítés sikertelen, a zárolás fel lesz szabadítva.
- Szervizelés: a frissítés folytatása.
- Előfordulás: ritka

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Rendszergazdai előfizetések

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: az 1804-es verzióval bevezetett két felügyeleti előfizetés nem használható. Az előfizetés típusa a **mérési** előfizetés és a **fogyasztási** előfizetés.
- Szervizelés: Ha a két előfizetésen futó erőforrásokkal rendelkezik, hozza létre újra a felhasználói előfizetésekben.
- Előfordulás: gyakori

## <a name="networking"></a>Hálózat

### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)

#### <a name="denyalloutbound-rule-cannot-be-created"></a>DenyAllOutbound-szabály nem hozható létre

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a virtuális gép létrehozása során nem hozható létre explicit **DenyAllOutbound** -szabály az internetre, mivel ez megakadályozza a virtuális gép telepítésének befejezéséhez szükséges kommunikációt a NSG. Emellett a virtuális gépek üzembe helyezéséhez szükséges két alapvető IP-címet is megtagadja: DHCP IP: 169.254.169.254 és DNS IP: 168.63.129.16.

- Szervizelés: engedélyezze a kimenő adatforgalmat a virtuális gépek létrehozásakor, és módosítsa a NSG, hogy letiltsa a szükséges forgalmat a virtuális gép létrehozása után.
- Előfordulás: gyakori

### <a name="virtual-network-gateway"></a>Virtuális hálózati átjáró

#### <a name="documentation"></a>Dokumentáció

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik.
- Ok: a dokumentáció a Virtual Network Gateway áttekintő oldalán, az Azure Stack hub helyett az Azure-specifikus dokumentációra mutató hivatkozást tartalmaz. Használja az alábbi hivatkozásokat az Azure Stack hub dokumentációjában:

  - [Átjáró-termékváltozatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Magasan elérhető kapcsolatok](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [A BGP konfigurálása Azure Stack hub-on](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [ExpressRoute-kapcsolatcsoportok](../operator/azure-stack-connect-expressroute.md)
  - [Egyéni IPsec/IKE-szabályzatok meghatározása](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>Load Balancer

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer a forgalom átirányítása egy háttérbeli virtuális gépre adott helyzetekben

- Alkalmazható: Ez a probléma az összes támogatott kiadásra vonatkozik. 
- Ok: Ha a **munkamenet-affinitást** egy terheléselosztó esetében engedélyezi, a 2 rekordos kivonat a PA IP-címet (fizikai cím IP) használja a virtuális gépekhez rendelt magánhálózati IP-címek helyett. Olyan esetekben, amikor a terheléselosztó felé irányuló forgalom egy VPN-en keresztül érkezik, vagy ha az összes ügyfél-virtuális gép (forrás IP-címe) ugyanazon a csomóponton található, és a munkamenet-affinitás is engedélyezve van, az összes forgalmat egyetlen háttérbeli virtuális gépre irányítja a rendszer.
- Előfordulás: gyakori
