---
title: Azure Stack összekötése az Azure-val a ExpressRoute használatával | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók a virtuális hálózatok Azure Stack az Azure-beli virtuális hálózatokhoz az ExpressRoute használatával.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: d7fa69b632ec6d205eff0ed0c388c1f9ec9b9c41
ms.sourcegitcommit: c196463492732218d2474d3a964f88e995272c80
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71094407"
---
# <a name="connect-azure-stack-to-azure-using-azure-expressroute"></a>Azure Stack összekötése az Azure-val az Azure ExpressRoute

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ez a cikk azt ismerteti, hogyan lehet Azure Stack virtuális hálózatot csatlakoztatni egy Azure-beli virtuális hálózathoz egy [Microsoft Azure ExpressRoute](/azure/expressroute/) közvetlen kapcsolat használatával.

Ez a cikk oktatóanyagként használható, és a példák használatával is beállíthatja ugyanazt a tesztkörnyezet. Vagy használhatja a cikket útmutatóként, amely végigvezeti a saját ExpressRoute-környezet beállításán.

## <a name="overview-assumptions-and-prerequisites"></a>Áttekintés, feltételezések és előfeltételek

Az Azure ExpressRoute segítségével kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy kapcsolati szolgáltató által biztosított privát kapcsolaton keresztül. A ExpressRoute nem VPN-kapcsolat a nyilvános interneten keresztül.

További információ az Azure ExpressRoute: [ExpressRoute – áttekintés](/azure/expressroute/expressroute-introduction).

### <a name="assumptions"></a>Feltételezések

Ez a cikk azt feltételezi, hogy:

* Ön rendelkezik az Azure-ban működő ismeretekkel.
* A Azure Stack alapvető ismeretekkel rendelkezik.
* A hálózatkezelés alapvető ismerete.

### <a name="prerequisites"></a>Előfeltételek

A Azure Stack és az Azure ExpressRoute-vel való összekapcsolásához a következő követelményeknek kell megfelelnie:

* Egy kiépített [ExpressRoute áramkör](/azure/expressroute/expressroute-circuit-peerings) a [kapcsolati szolgáltatón](/azure/expressroute/expressroute-locations)keresztül.
* Azure-előfizetés ExpressRoute-áramkör és virtuális hálózatok létrehozásához az Azure-ban.
* Egy útválasztó, amelynek a következőket kell tennie:
  * A helyek közötti VPN-kapcsolatok támogatása a helyi hálózati adapter és Azure Stack több-bérlős átjáró között.
  * Támogatás több VRFs létrehozásához (virtuális Útválasztás és továbbítás), ha több bérlő van a Azure Stack üzemelő példányában.
* Egy útválasztó, amely a következőket tartalmazta:
  * A ExpressRoute áramkörhöz csatlakozó WAN-port.
  * A Azure Stack több-bérlős átjáróhoz csatlakoztatott LAN-port.

### <a name="expressroute-network-architecture"></a>ExpressRoute hálózati architektúra

Az alábbi ábra a Azure Stack és az Azure-környezeteket mutatja be a ExpressRoute beállításának befejezése után a jelen cikkben szereplő példák alapján:

![ExpressRoute hálózat](media/azure-stack-connect-expressroute/Conceptual.png)

Az alábbi ábra azt mutatja be, hogy több bérlő Hogyan kapcsolódhat a Azure Stack-infrastruktúrából a ExpressRoute-útválasztón keresztül az Azure-ba a Microsoft Edge-ben:

![Több-bérlős kapcsolatok a ExpressRoute](media/azure-stack-connect-expressroute/Architecture.png)

A cikkben szereplő példa ugyanazt a több-bérlős architektúrát használja a diagramon, hogy a Azure Stackt az Azure-hoz a ExpressRoute privát társításával kapcsolódjon. A kapcsolat a két hálózat közötti pont-pont típusú VPN-kapcsolattal történik a Azure Stack virtuális hálózati átjáróról egy ExpressRoute-útválasztóhoz.

A cikkben ismertetett lépések bemutatják, hogyan hozható létre végpontok közötti kapcsolat két virtuális hálózatok két különböző bérlőről Azure Stack az Azure megfelelő virtuális hálózatok. Két bérlő beállítása nem kötelező; ezeket a lépéseket egyetlen bérlő esetében is használhatja.

## <a name="configure-azure-stack"></a>Azure Stack konfigurálása

Az első bérlő Azure Stack környezetének beállításához kövesse az alábbi lépéseket útmutatóként. Ha egynél több bérlőt állít be, ismételje meg a következő lépéseket:

>[!NOTE]
>Ezek a lépések bemutatják, hogyan hozhat létre erőforrásokat a Azure Stack portál használatával, de a PowerShellt is használhatja.

![Azure Stack hálózati beállítás](media/azure-stack-connect-expressroute/image2.png)

### <a name="before-you-begin"></a>Előkészületek

A Azure Stack konfigurálásának megkezdése előtt a következőkre lesz szüksége:

* Azure Stack integrált rendszer központi telepítése vagy Azure Stack Development Kit (ASDK) telepítése. További információ a ASDK telepítéséről: [Azure stack Development Kit üzembe helyezési](../asdk/asdk-download.md)útmutató.
* Azure Stack ajánlata, amelyhez a felhasználók előfizethetnek. További információ: [csomagok, ajánlatok és előfizetések](azure-stack-plan-offer-quota-overview.md).

### <a name="create-network-resources-in-azure-stack"></a>Hálózati erőforrások létrehozása a Azure Stackban

A következő eljárásokkal hozhatja létre a szükséges hálózati erőforrásokat a bérlők Azure Stack.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>A virtuális hálózat és a virtuálisgép-alhálózat létrehozása

1. Jelentkezzen be a Azure Stack felhasználói portálra.

2. A portálon válassza az **+ erőforrás létrehozása**lehetőséget.

3. Az **Azure Marketplace**területen válassza a **hálózatkezelés**lehetőséget.

4. A **Kiemelt**területen válassza a **virtuális hálózat**lehetőséget.

5. A **virtuális hálózat létrehozása**területen adja meg az alábbi táblázatban látható értékeket a megfelelő mezőkbe:

   |Mező  |Érték  |
   |---------|---------|
   |Name (Név)     |Tenant1VNet1         |
   |Címtartomány     |10.1.0.0/16|
   |Alhálózat neve     |Tenant1-Sub1|
   |Alhálózat címtartománya     |10.1.1.0/24|

6. Ekkor meg kell jelennie a korábban létrehozott előfizetésnek az **előfizetés** mezőben. A fennmaradó mezőknél:

    * Az **erőforráscsoport**területen válassza az **új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához, vagy ha már rendelkezik ilyennel, válassza a **meglévő használata**lehetőséget.
    * Ellenőrizze az alapértelmezett **helyet**.
    * Kattintson a **Create** (Létrehozás) gombra.
    * Választható Kattintson **a rögzítés az irányítópulton**elemre.

#### <a name="create-the-gateway-subnet"></a>Az átjáróalhálózat létrehozása

1. A **virtuális hálózat**területen válassza a **Tenant1VNet1**lehetőséget.
1. A **BEÁLLÍTÁSOK** területen válassza az **Alhálózatok** elemet.
1. Válassza ki a **+ Gateway alhálózatot** , és adjon hozzá egy átjáró-alhálózatot a virtuális hálózathoz.
1. Az alhálózat neve alapértelmezés szerint **GatewaySubnet**. Az átjáró-alhálózatok egy speciális eset, és ezt a nevet kell használniuk a megfelelő működéshez.
1. Győződjön meg arról, hogy a **címtartomány** **10.1.0.0/24**.
1. Az átjáró-alhálózat létrehozásához kattintson **az OK** gombra.

#### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

1. A Azure Stack felhasználói portálon kattintson az **+ erőforrás létrehozása**elemre.
1. Az **Azure Marketplace**területen válassza a **hálózatkezelés**lehetőséget.
1. A hálózati erőforrások listájában válassza a **Virtuális hálózati átjáró** elemet.
1. A név mezőbe írja be a **GW1** **nevet** .
1. Válassza ki a **Virtuális hálózatot**.
1. A legördülő listából válassza a **Tenant1VNet1** lehetőséget.
1. Válassza a **nyilvános IP-cím**lehetőséget, majd **válassza a nyilvános IP-cím**lehetőséget, majd kattintson az **új létrehozása**lehetőségre.
1. A név mezőbe írja be a **GW1-pip** **nevet** , majd kattintson az **OK**gombra.
1. Az **VPN típusa** mezőben alapértelmezés szerint a **Útvonalalapú** lehetőség van kiválasztva. Ne módosítsa ezt a beállítást.
1. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Kattintson a **Create** (Létrehozás) gombra.

#### <a name="create-the-local-network-gateway"></a>A helyi hálózati átjáró létrehozása

A helyi hálózati átjáró erőforrás a VPN-kapcsolat másik végén található távoli átjárót azonosítja. Ebben a példában a kapcsolat távoli vége a ExpressRoute útválasztó helyi hálózati adaptere. Az előző diagram 1. bérlője esetében a távoli 10.60.3.255.

1. Jelentkezzen be a Azure Stack felhasználói portálra, és válassza az **+ erőforrás létrehozása**lehetőséget.
1. Az **Azure Marketplace**területen válassza a **hálózatkezelés**lehetőséget.
1. Az erőforrások listájában válassza a **Helyi hálózati átjáró** elemet.
1. A **név** mezőbe írja be a következőt: **er-router-GW**.
1. Az **IP-cím** mezőben tekintse meg az előző ábrát. Az 1. Bérlő ExpressRoute útválasztó helyi hálózati adapterének IP-címe 10.60.3.255. A saját környezetében adja meg az útválasztó megfelelő felületének IP-címét.
1. A **címterület** mezőben adja meg azt a virtuális hálózatok, amelyhez csatlakozni szeretne az Azure-ban. Az 1. Bérlő alhálózatai a következők:

   * a 192.168.2.0/24 a hub VNet az Azure-ban.
   * a 10.100.0.0/16 a küllős VNet az Azure-ban.

   > [!IMPORTANT]
   > Ez a példa azt feltételezi, hogy statikus útvonalakat használ a helyek közötti VPN-kapcsolathoz a Azure Stack átjáró és a ExpressRoute-útválasztó között.

1. Ellenőrizze, hogy az **előfizetés**, az **erőforráscsoport**és a **hely** helyes-e. Ezután kattintson a **Létrehozás** elemre.

#### <a name="create-the-connection"></a>A kapcsolat létrehozása

1. A Azure Stack felhasználói portálon válassza az **+ erőforrás létrehozása**lehetőséget.
1. Az **Azure Marketplace**területen válassza a **hálózatkezelés**lehetőséget.
1. Az erőforrások listájában válassza a **Kapcsolat** elemet.
1. Az **alapok**területen válassza a **helyek közötti (IPSec)** lehetőséget a **kapcsolat típusaként**.
1. Válassza kiaz előfizetést, az **erőforráscsoportot**és a **helyet**. Kattintson az **OK** gombra.
1. A **Beállítások**területen válassza ki a **virtuális hálózati átjáró**elemet, majd válassza a **GW1**lehetőséget.
1. Válassza a **helyi hálózati átjáró**lehetőséget, majd válassza az **er router GW**lehetőséget.
1. A **kapcsolatok neve** mezőbe írja be a **ConnectToAzure**nevet.
1. A **megosztott kulcs (PSK)** mezőben adja meg a **abc123** , majd kattintson **az OK gombra**.
1. Az **Összefoglalás**területen kattintson **az OK gombra**.

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>A virtuális hálózati átjáró nyilvános IP-címének beolvasása

A virtuális hálózati átjáró létrehozása után beszerezheti az átjáró nyilvános IP-címét. Jegyezze fel ezt a internetcímet, ha később szüksége lesz rá az üzemelő példányhoz. A központi telepítéstől függően ez a cím lesz a **belső IP-cím**.

1. A Azure Stack felhasználói portálon válassza a **minden erőforrás**elemet.
1. A **minden erőforrás**területen válassza ki a virtuális hálózati átjárót, amely a példában **GW1** .
1. A **virtuális hálózati átjáró**területen válassza az **Áttekintés** lehetőséget az erőforrások listájából. Másik lehetőségként a **Tulajdonságok**elemet is kiválaszthatja.
1. A megjegyezni kívánt IP-cím a **nyilvános IP-cím**területen jelenik meg. A példa konfigurációjának 192.68.102.1 Ez a címe.

#### <a name="create-a-virtual-machine-vm"></a>Virtuális gép (VM) létrehozása

A VPN-kapcsolaton keresztüli adatforgalom teszteléséhez virtuális gépekre van szükség az adatok küldéséhez és fogadásához a Azure Stack VNet. Hozzon létre egy virtuális GÉPET, és telepítse azt a virtuális hálózat virtuálisgép-alhálózatán.

1. A Azure Stack felhasználói portálon válassza az **+ erőforrás létrehozása**lehetőséget.
1. Az **Azure Marketplace**területen válassza a **számítás**lehetőséget.
1. A virtuálisgép-rendszerképek listájában válassza ki a **Windows Server 2016 Datacenter eval** rendszerképét.

   >[!NOTE]
   >Ha az ehhez a cikkhez használt rendszerkép nem érhető el, kérje meg a Azure Stack operátort, hogy adjon meg egy másik Windows Server-rendszerképet.

1. A **virtuális gép létrehozása**területen válasszaaz alapismeretek lehetőséget, majd írja be a **VM01** **nevet**.
1. Érvényes felhasználónevet és jelszót adjon meg. Ezt a fiókot fogja használni a virtuális gépre való bejelentkezéshez a létrehozás után.
1. Adja megaz előfizetést, az **erőforráscsoportot**és a **helyet**. Kattintson az **OK** gombra.
1. A **méret kiválasztása**területen válasszon egy virtuálisgép-méretet ehhez a példányhoz, majd válassza a **kiválasztás**lehetőséget.
1. A **Beállítások**területen ellenőrizze, hogy:

   * A virtuális hálózat **Tenant1VNet1**.
   * Az alhálózat beállítása **10.1.1.0/24**.

   Használja az alapértelmezett beállításokat, majd kattintson **az OK gombra**.

1. Az **Összefoglalás**alatt tekintse át a virtuális gép konfigurációját, majd kattintson **az OK**gombra.

További bérlők hozzáadásához ismételje meg a következő szakaszban leírt lépéseket:

* [A virtuális hálózat és a virtuálisgép-alhálózat létrehozása](#create-the-virtual-network-and-vm-subnet)
* [Az átjáró alhálózatának létrehozása](#create-the-gateway-subnet)
* [A virtuális hálózati átjáró létrehozása](#create-the-virtual-network-gateway)
* [A helyi hálózati átjáró létrehozása](#create-the-local-network-gateway)
* [A kapcsolat létrehozása](#create-the-connection)
* [Virtuális gép létrehozása](#create-a-virtual-machine)

Ha például a 2. bérlőt használja, ne felejtse el módosítani az IP-címeket az átfedések elkerülése érdekében.

### <a name="configure-the-nat-vm-for-gateway-traversal"></a>NAT virtuális gép konfigurálása átjáró bejárásához

> [!IMPORTANT]
> Ez a szakasz csak a ASDK üzemelő példányok esetében használható. A többcsomópontos üzemelő példányok esetében a NAT nem szükséges.

A ASDK önálló és elkülönített a fizikai gazdagépet telepítő hálózattól. A VIP-hálózat, amelyhez az átjárók csatlakoztatva vannak, nem külső; egy hálózati címfordítást (NAT) végrehajtó útválasztó mögött van.

Az útválasztó az Útválasztás és távelérés szolgáltatás (RRAS) szerepkört futtató ASDK-gazdagép. Konfigurálnia kell a NAT-t a ASDK-gazdagépen ahhoz, hogy a helyek közötti VPN-kapcsolat mindkét végén csatlakozhasson.

#### <a name="configure-the-nat"></a>A NAT konfigurálása

1. Jelentkezzen be a Azure Stack gazdagépre a rendszergazdai fiókjával.
1. Futtassa a parancsfájlt egy emelt szintű PowerShell ISE-ben. Ez a szkript visszaadja a **külső BGPNAT-címeit**.

   ```powershell
   Get-NetNatExternalAddress
   ```

1. A NAT konfigurálásához másolja és szerkessze a következő PowerShell-szkriptet. Szerkessze a szkriptet, `External BGPNAT address` és `Internal IP address` cserélje le a következő példában szereplő értékeket:

   * *Külső BGPNAT-címek* esetén használja a 10.10.0.62
   * *Belső IP-cím* használata 192.168.102.1

   Futtassa a következő parancsfájlt egy emelt szintű PowerShell ISE-ből:

   ```powershell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501
   Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
   Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500
   ```

## <a name="configure-azure"></a>Az Azure konfigurálása

Miután befejezte a Azure Stack konfigurálását, üzembe helyezheti az Azure-erőforrásokat. Az alábbi ábrán egy példa látható egy bérlői virtuális hálózatra az Azure-ban. A VNet az Azure-ban bármilyen nevet és címzési sémát használhat. Az Azure-ban és a Azure Stack virtuális hálózatok azonban egyedinek kell lenniük, és nem lehetnek átfedésben:

![Azure-virtuális hálózatok](media/azure-stack-connect-expressroute/AzureArchitecture.png)

Az Azure-ban üzembe helyezett erőforrások hasonlóak az Azure Stack-ban telepített erőforrásokhoz. A következő összetevőket kell telepítenie:

* Virtuális hálózatok és alhálózatok
* Átjáró-alhálózat
* Virtuális hálózati átjáró
* Egy-egy kapcsolatok
* Egy ExpressRoute áramkör

Az Azure hálózati infrastruktúra példája az alábbiak szerint van konfigurálva:

* Egy standard hub (192.168.2.0/24) és küllő (10.100.0.0./16) VNet-modell. A sugaras hálózati topológiával kapcsolatos további információkért lásd: [sugaras hálózati topológia implementálása az Azure-ban](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
* A rendszer üzembe helyezi a munkaterheléseket a küllős VNet, és a ExpressRoute áramkör csatlakozik a hub VNet.
* A két virtuális hálózatok a VNet-társítás használatával csatlakozik.

### <a name="configure-the-azure-vnets"></a>Az Azure-virtuális hálózatok konfigurálása

1. Jelentkezzen be az Azure Portalba az Azure-beli hitelesítő adataival.
1. Hozza létre a hub VNet a 192.168.2.0/24 címtartomány használatával.
1. Hozzon létre egy alhálózatot a 192.168.2.0/25 címtartomány használatával, és adjon hozzá egy átjáró-alhálózatot a 192.168.2.128/27 címtartomány használatával.
1. Hozza létre a küllős VNet és alhálózatot a 10.100.0.0/16 címtartomány használatával.

A virtuális hálózatok Azure-beli létrehozásával kapcsolatos további információkért lásd: [virtuális hálózat létrehozása](/azure/virtual-network/manage-virtual-network#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>ExpressRoute áramkör konfigurálása

1. Tekintse át a ExpressRoute előfeltételeit a [ExpressRoute előfeltételek & ellenőrzőlista](/azure/expressroute/expressroute-prerequisites)lapon.

1. Kövesse a ExpressRoute- [áramkör létrehozása és módosítása](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) a ExpressRoute-áramkör létrehozásához az Azure-előfizetése segítségével című témakör lépéseit.

   >[!NOTE]
   >Adja meg az áramkör szolgáltatási kulcsát a szolgáltatás számára, hogy az ExpressRoute-áramkört a végén lehessen beállítani.

1. A [ExpressRoute-áramkör létrehozásához és módosításához](/azure/expressroute/expressroute-howto-routing-portal-resource-manager) kövesse az ExpressRoute-áramkör privát társításának konfigurálásához szükséges lépéseket.

### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

A [ExpressRoute virtuális hálózati átjárójának konfigurálása a PowerShell használatával](/azure/expressroute/expressroute-howto-add-gateway-resource-manager) című szakasz lépéseit követve hozzon létre egy virtuális hálózati átjárót a ExpressRoute számára a hub VNet.

### <a name="create-the-connection"></a>A kapcsolat létrehozása

Ha a ExpressRoute áramkört a hub VNet szeretné kapcsolni, kövesse a [virtuális hálózat csatlakoztatása egy ExpressRoute-áramkörhöz](/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)című témakör lépéseit.

### <a name="peer-the-vnets"></a>A virtuális hálózatok társítása

A hub és a küllő virtuális hálózatok a [virtuális hálózati társítás létrehozása a Azure Portal használatával](/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal)című témakör lépéseit követve. A VNet-társítás konfigurálásakor ügyeljen arra, hogy a következő beállításokat használja:

* A hub és a küllő között **engedélyezze az átjáró átvitelét**.
* A küllő és a hub között **használja a távoli átjárót**.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A számítási feladatok virtuális gépei üzembe helyezése a küllős VNet.

Ismételje meg ezeket a lépéseket minden további bérlői virtuális hálózatok, amelyet az Azure-ban a megfelelő ExpressRoute-áramkörökkel szeretne csatlakozni.

## <a name="configure-the-router"></a>Az útválasztó konfigurálása

A ExpressRoute-útválasztó konfigurálásához használhatja a következő ExpressRoute útválasztó-konfigurációs diagramot. Ez az ábra két bérlőt mutat be (1. Bérlő és 2. Bérlő) a megfelelő ExpressRoute-áramkörökkel. Minden bérlő saját VRF (virtuális útválasztással és továbbítással) van társítva a ExpressRoute-útválasztó LAN-és WAN-oldalán. Ez a konfiguráció biztosítja a két bérlő közötti végpontok közötti elkülönítést. Jegyezze fel az útválasztói felületeken használt IP-címeket a konfigurációs példa követése során.

![ExpressRoute útválasztó konfigurációja](media/azure-stack-connect-expressroute/EndToEnd.png)

Bármely olyan útválasztót használhat, amely támogatja a IKEv2 VPN-t és BGP-t a két hálózat közötti pont-pont típusú VPN-kapcsolat megszakításához Azure Stack. Ugyanez az útválasztó használja az Azure-hoz való kapcsolódásra egy ExpressRoute áramkör használatával.

A következő Cisco Site Recovery 1000 sorozat-összesítő szolgáltatások útválasztó-konfigurációs példája támogatja a *ExpressRoute-útválasztó konfigurációs* diagramjában látható hálózati infrastruktúrát.

```shell
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2
description IKEv2 proposal for Tenant 1
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2
description IKEv2 proposal for Tenant 2
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2
description IKEv2 Policy for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Kapcsolat tesztelése

A kapcsolat tesztelése a helyek közötti kapcsolat és a ExpressRoute áramkör létrehozása után.

Hajtsa végre a következő ping-teszteket:

* Jelentkezzen be az Azure-VNet egyik virtuális gépére, és Pingelje a Azure Stackban létrehozott virtuális gépet.
* Jelentkezzen be az Azure Stack-ben létrehozott egyik virtuális gépre, és Pingelje meg az Azure VNet létrehozott virtuális gépet.

>[!NOTE]
>Annak biztosításához, hogy a helyek közötti és a ExpressRoute kapcsolaton keresztül küldje el a forgalmat, a virtuális gép dedikált IP-címének (DIP) a két végponton kell megfelelnie, és nem a virtuális gép VIP-címét.

### <a name="allow-icmp-in-through-the-firewall"></a>ICMP engedélyezése a tűzfalon keresztül

Alapértelmezés szerint a Windows Server 2016 nem engedélyezi a bejövő ICMP-csomagokat a tűzfalon keresztül. Minden olyan virtuális gép esetében, amelyet a ping tesztek esetében használ, engedélyeznie kell a bejövő ICMP-csomagokat. Az ICMP-hez készült tűzfalszabály létrehozásához futtassa a következő parancsmagot egy emelt szintű PowerShell-ablakban:

```powershell
# Create ICMP firewall rule.
New-NetFirewallRule `
  -DisplayName "Allow ICMPv4-In" `
  -Protocol ICMPv4
```

### <a name="ping-the-azure-stack-vm"></a>A Azure Stack virtuális gép pingelése

1. Jelentkezzen be a Azure Stack felhasználói portálra.

1. Keresse meg a létrehozott virtuális gépet, és válassza ki.

1. Kattintson a **Csatlakozás** gombra.

1. Egy emelt szintű Windows-vagy PowerShell-parancssorba írja be az **ipconfig/all**parancsot. Jegyezze fel a kimenetben visszaadott IPv4-címet.

1. Pingelje az IPv4-címeket az Azure-VNet található virtuális gépről.

   A példában szereplő környezetekben az IPv4-címek a 10.1.1. x/24 alhálózatból származnak. A környezetben előfordulhat, hogy a címe eltérő lehet, de a bérlői VNet alhálózathoz létrehozott alhálózatban kell lennie.

### <a name="view-data-transfer-statistics"></a>Adatátviteli statisztika megtekintése

Ha tudni szeretné, hogy mekkora forgalom halad át a kapcsolatban, akkor a Azure Stack felhasználói portálon találhatja meg ezeket az információkat. Az adatátviteli statisztikák megtekintése szintén jó módszer arra, hogy megtudja, hogy a ping teszt adatai átmentek-e a VPN-és ExpressRoute-kapcsolatokon keresztül:

1. Jelentkezzen be a Azure Stack felhasználói portálra, és válassza az **összes erőforrás**lehetőséget.
1. Navigáljon a VPN Gateway erőforráscsoporthoz, és válassza ki a **kapcsolódási** objektum típusát.
1. Válassza ki a **ConnectToAzure** -összekötőt a listából.
1. A **kapcsolatok** > **áttekintése**területen megtekintheti az **adatok** és az **adatok kimenő**statisztikáit. Néhány nem nulla értéket kell látnia.

   ![És kimenő adatbevitel](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>További lépések

[Alkalmazások telepítése az Azure-ba és Azure Stack](../user/azure-stack-solution-pipeline.md )
