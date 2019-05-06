---
title: Azure Stack csatlakozni az Azure ExpressRoute használatával
description: Útmutató az Azure Stackben virtuális hálózatok összekapcsolása virtuális hálózatok az Azure ExpressRoute használatával.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: a039830e01b85ec5e8d9c66a0c815a38d9c694f8
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985731"
---
# <a name="connect-azure-stack-to-azure-using-azure-expressroute"></a>Csatlakozás Azure Stack az Azure ExpressRoute használatával

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ez a cikk bemutatja, hogyan lehet egy Azure Stack virtuális hálózat csatlakoztatása egy Azure virtual network segítségével egy [a Microsoft Azure ExpressRoute](/azure/expressroute/) közvetlen kapcsolat.

Ez a cikk egy oktatóanyag használja, és a példák segítségével ugyanazt a tesztkörnyezet beállításához. Vagy, a cikk egy útmutató, amely végigvezeti a saját ExpressRoute-környezet beállítására is használhatja.

## <a name="overview-assumptions-and-prerequisites"></a>Áttekintés, feltételezéseket és Előfeltételek

Az Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft cloud a kapcsolatszolgáltató által biztosított egy privát kapcsolaton keresztül. Az ExpressRoute nem egy VPN-kapcsolat a nyilvános interneten keresztül.

Az Azure ExpressRoute kapcsolatos további információkért lásd: [ExpressRoute overview]((/azure/expressroute/expressroute-introduction).

### <a name="assumptions"></a>Előfeltételek

Ez a cikk feltételezi, hogy:

* Rendelkezik Azure gyakorlati ismerete.
* Azure Stack alapvető ismeretekkel rendelkezik.
* Hálózatkezelés alapvető ismeretekkel rendelkezik.

### <a name="prerequisites"></a>Előfeltételek

Az Azure Stacket és az Azure ExpressRoute-tal tudjon csatlakozni a következő követelményeknek kell megfelelnie:

* A kiépített [ExpressRoute circuit]((/azure/expressroute/expressroute-circuit-peerings) keresztül [kapcsolat provider]((/azure/expressroute/expressroute-locations).
* Azure-előfizetéssel, hozzon létre egy ExpressRoute-Kapcsolatcsoportok és virtuális hálózatok az Azure-ban.
* Egy útválasztó, amely kell:
  * Támogatja a helyek közötti VPN-kapcsolatok a LAN-kapcsolat és az Azure Stack több-Bérlős átjáró között.
  * Támogatja a több VRFs (virtuális útválasztást és a továbbítás) létrehozása, ha egynél több bérlőhöz van az Azure Stack üzemelő példányban.
* Útválasztó, amely rendelkezik:
  * Egy WAN port az ExpressRoute-kapcsolatcsoporthoz csatlakozik.
  * LAN-porthoz csatlakozik az Azure Stack több-Bérlős átjáró.

### <a name="expressroute-network-architecture"></a>Az ExpressRoute hálózati architektúra

Az alábbi ábrán látható az Azure Stack és az Azure-környezetek ExpressRoute beállításának befejezése után ebben a cikkben a példák segítségével:

*1. ábra Az ExpressRoute hálózati*

![Az ExpressRoute hálózati](media/azure-stack-connect-expressroute/Conceptual.png)

Az alábbi ábrán látható, hogyan több bérlő elérését az Azure Stack-infrastruktúra az ExpressRoute-útválasztón keresztül az Azure-ba, a Microsoft Edge-ben:

*2. ábra Több-bérlős kapcsolatok*

![Az expressroute-tal több-bérlős kapcsolatok](media/azure-stack-connect-expressroute/Architecture.png)

Ebben a cikkben a példában a 2. ábrán látható ugyanazon több-bérlős architektúra használatával csatlakozzon az Azure Stack az Azure-ban, ExpressRoute privát társviszony-létesítés. A kapcsolat történik az Azure Stack használatával a site-to-site VPN-kapcsolatot a virtuális hálózati átjáró egy ExpressRoute-útválasztó.

A jelen cikkben ismertetett lépések bemutatják, hogyan hozzon létre megfelelő virtuális hálózatokhoz az Azure-ban két különböző bérlőn, az Azure Stack két virtuális hálózat között teljes körű kapcsolatot. Két bérlőjéhez beállítása nem kötelező megadni. egyetlen bérlő számára is használhatja ezeket a lépéseket.

## <a name="configure-azure-stack"></a>Az Azure Stack konfigurálása

Az Azure Stack-környezet az első bérlő számára, használja a lépéseket az alábbi ábrán alapján. Ha egynél több bérlőhöz beállítása, ismételje meg ezeket a lépéseket:

>[!NOTE]
>Ezek a lépések bemutatják, hogyan hozhat létre erőforrásokat az Azure Stack portálon használja, de a PowerShell is használható.

![Az Azure Stack hálózati beállítása](media/azure-stack-connect-expressroute/image2.png)

### <a name="before-you-begin"></a>Előzetes teendők

A kezdéshez konfigurálása az Azure Stack lesz szüksége:

* Az Azure Stackkel integrált rendszer központi telepítésének vagy az Azure Stack Development Kit (ASDK) központi telepítés. A ASDK központi telepítésével kapcsolatos információkért lásd: a [Azure Stack fejlesztői készletének telepítése – rövid útmutató](../asdk/asdk-download.md).
* Egy ajánlatot az Azure Stack, hogy a felhasználók előfizethetnek. További információkért lásd: [csomagok, ajánlatok és előfizetések](azure-stack-plan-offer-quota-overview.md).

### <a name="create-network-resources-in-azure-stack"></a>Hálózati erőforrások létrehozása az Azure Stackben

Az alábbi eljárásokkal a szükséges hálózati erőforrások létrehozása az Azure Stack egy bérlő.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>A virtuális hálózat és a virtuálisgép-alhálózat létrehozása

1. Jelentkezzen be a felhasználói portál (bérlő) felhasználói fiókkal.

2. Válassza a portál **+ erőforrás létrehozása**.

3. A **Azure Marketplace-en**válassza **hálózatkezelés**.

4. A **kiemelt**válassza **virtuális hálózati**.

5. A **virtuális hálózat létrehozása**, adja meg a megfelelő mezőkbe az alábbi táblázatban látható értékeket:

   |Mező  |Érték  |
   |---------|---------|
   |Name (Név)     |Tenant1VNet1         |
   |Címtér     |10.1.0.0/16|
   |Alhálózat neve     |Tenant1-Sub1|
   |Alhálózati címtartomány     |10.1.1.0/24|

6. A korábban létrehozott előfizetést kell megjelennie a **előfizetés** mező. A többi mezőhöz:

    * Alatt **erőforráscsoport**, jelölje be **új létrehozása** hozzon létre egy új erőforráscsoportot, vagy ha már rendelkezik ilyennel, válassza ki a **meglévő**.
    * Az alapértelmezett ellenőrzése **hely**.
    * Kattintson a **Create** (Létrehozás) gombra.
    * (Nem kötelező) Kattintson a **rögzítés az irányítópulton**.

#### <a name="create-the-gateway-subnet"></a>Az átjáróalhálózat létrehozása

1. A **virtuális hálózati**válassza **Tenant1VNet1**.
1. A **BEÁLLÍTÁSOK** területen válassza az **Alhálózatok** elemet.
1. Válassza ki **+ átjáró-alhálózat** , adjon hozzá egy átjáró-alhálózatot a virtuális hálózathoz.
1. Az alhálózat neve alapértelmezés szerint **GatewaySubnet**. Átjáró-alhálózatokkal különleges esetet, és ezt a nevet kell használni a megfelelő működéshez.
1. Ellenőrizze, hogy a **címtartomány** van **10.1.0.0/24**.
1. Kattintson a **OK** az átjáró-alhálózat létrehozásához.

#### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

1. Az Azure Stack felhasználói portálon kattintson a **+ erőforrás létrehozása**.
1. A **Azure Marketplace-en**válassza **hálózatkezelés**.
1. A hálózati erőforrások listájában válassza a **Virtuális hálózati átjáró** elemet.
1. Az a **neve** írja be a következőt **GW1**.
1. Válassza ki a **Virtuális hálózatot**.
1. Válassza ki **Tenant1VNet1** a legördülő listából.
1. Válassza ki **nyilvános IP-cím**, majd **nyilvános IP-cím választása**, és kattintson a **új létrehozása**.
1. Az a **neve** mezőbe írja be a **GW1-PiP**, és kattintson a **OK**.
1. Az **VPN típusa** mezőben alapértelmezés szerint a **Útvonalalapú** lehetőség van kiválasztva. Ne módosítsa ezt a beállítást.
1. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Kattintson a **Create** (Létrehozás) gombra.

#### <a name="create-the-local-network-gateway"></a>A helyi hálózati átjáró létrehozása

A helyi hálózati átjáró erőforrás azonosítja a VPN-kapcsolat másik végén lévő távoli átjárót. Ebben a példában a kapcsolat a távoli vég a LAN-al kapcsolat az ExpressRoute-útválasztó. A távoli cím 10.60.3.255 bérlői 1, 2. ábrán látható.

1. Jelentkezzen be az Azure Stack felhasználói portálon, a felhasználói fiókjával, és válassza ki **+ erőforrás létrehozása**.
1. A **Azure Marketplace-en**válassza **hálózatkezelés**.
1. Az erőforrások listájában válassza a **Helyi hálózati átjáró** elemet.
1. Az a **neve** mezőbe írja be a **ER-útválasztó-GW**.
1. Az a **IP-cím** mezőben lásd a 2. ábra. Az ExpressRoute útválasztó helyi hálózati alárendelt kezelőfelület bérlői 1 IP-címe 10.60.3.255. Adja meg a megfelelő az útválasztó-illesztő IP-címét a saját környezetben.
1. Az a **címtér** mezőben adja meg a virtuális hálózatokkal, amelyhez csatlakozni kíván az Azure-ban, a címtér. A bérlő az 1 alhálózatok *2. ábra* az alábbiak szerint:

   * 192.168.2.0/24 az agyi virtuális hálózat az Azure-ban.
   * 10.100.0.0/16 a küllő virtuális hálózat az Azure-ban.

   > [!IMPORTANT]
   > Ez a példa feltételezi, hogy statikus útvonalakat használ a Site-to-Site VPN-kapcsolat az Azure Stack-átjáró és az ExpressRoute-útválasztó között.

1. Ellenőrizze, hogy a **előfizetés**, **erőforráscsoport**, és **hely** helyes-e. Ezután kattintson a **Létrehozás** elemre.

#### <a name="create-the-connection"></a>A kapcsolat létrehozása

1. Az Azure Stack felhasználói portálon, válassza ki a **+ erőforrás létrehozása**.
1. A **Azure Marketplace-en**válassza **hálózatkezelés**.
1. Az erőforrások listájában válassza a **Kapcsolat** elemet.
1. A **alapjai**, válassza a **Site-to-site (IPSec)** , a **kapcsolattípus**.
1. Válassza ki a **előfizetés**, **erőforráscsoport**, és **hely**. Kattintson az **OK** gombra.
1. A **beállítások**válassza **virtuális hálózati átjáró**, majd válassza ki **GW1**.
1. Válassza ki **helyi hálózati átjáró**, majd válassza ki **ER útválasztó Átjáró**.
1. Az a **kapcsolatnevet** írja be a következőt **ConnectToAzure**.
1. Az a **megosztott kulcs (PSK)** írja be a következőt **abc123** majd **OK**.
1. A **összefoglalás**válassza **OK**.

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>A virtuális hálózati átjáró nyilvános IP-cím beszerzése

A virtuális hálózati átjáró létrehozása után megtekintheti az átjáró nyilvános IP-címet. Jegyezze fel ezt a címet, arra az esetre, szükség esetén később az üzembe helyezéshez. A telepítéstől függően ez lesz a **belső IP-cím**.

1. Az Azure Stack felhasználói portálon, válassza ki a **összes erőforrás**.
1. A **összes erőforrás**, válassza ki a virtuális hálózati átjáró, amely **GW1** példában.
1. A **virtuális hálózati átjáró**válassza **áttekintése** az erőforrások listájából. Másik lehetőségként kiválaszthatja **tulajdonságok**.
1. Az IP-címet, vegye figyelembe kívánt részen **nyilvános IP-cím**. A példában a konfigurációhoz a címe 192.68.102.1.

#### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A VPN-kapcsolaton keresztüli adatforgalom teszteléséhez küldhet és fogadhat adatokat az Azure Stack virtuális hálózat virtuális gépeket kell. Hozzon létre egy virtuális gépet, és helyezze üzembe azt a Virtuálisgép-alhálózatot a virtuális hálózat.

1. Az Azure Stack felhasználói portálon, válassza ki a **+ erőforrás létrehozása**.
1. A **Azure Marketplace-en**válassza **számítási**.
1. A virtuálisgép-rendszerképek listájában válassza ki a **Windows Server 2016 Datacenter próbaverzió** kép.

   >[!NOTE]
   >Ha nem érhető el az ebben a cikkben használt lemezképet, kérje meg, adjon meg egy másik Windows Server-lemezképet az Azure Stack-operátorokról.

1. A **hozzon létre virtuális gépet**, jelölje be **alapjai**, majd írja be a **VM01** , a **neve**.
1. Adjon meg egy érvényes felhasználónevet és jelszót. Ezt a fiókot fogja használni a létrehozásuk után a virtuális gépre bejelentkezni.
1. Adjon meg egy **előfizetés**, **erőforráscsoport**, és a egy **hely**. Kattintson az **OK** gombra.
1. A **méret kiválasztása**, a példány virtuálisgép-méretet, majd válassza ki és **kiválasztása**.
1. A **beállítások**, ellenőrizze, hogy:

   * A virtuális hálózat **Tenant1VNet1**.
   * Az alhálózat beállítása pedig **10.1.1.0/24**.

   Az alapértelmezett beállításokat használja, és kattintson a **OK**.

1. A **összefoglalás**, tekintse át a virtuális gép konfigurációjához, majd kattintson **OK**.

További bérlők hozzáadásához ismételje meg az ezekben a szakaszokban követte a lépéseket:

* [A virtuális hálózat és a Virtuálisgép-alhálózat létrehozása](#create-the-virtual-network-and-vm-subnet)
* [Az átjáró-alhálózat létrehozása](#create-the-gateway-subnet)
* [A virtuális hálózati átjáró létrehozása](#create-the-virtual-network-gateway)
* [A helyi hálózati átjáró létrehozása](#create-the-local-network-gateway)
* [A kapcsolat létrehozása](#create-the-connection)
* [Virtuális gép létrehozása](#create-a-virtual-machine)

Ha például a bérlő 2 használ, ne felejtse el módosítani az IP-címek átfedés elkerülése érdekében.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Konfigurálja a NAT virtuális gépet átjáróátjáráshoz

> [!IMPORTANT]
> Ez a szakasz csak az Azure Stack Development Kit (ASDK) üzembe helyezések szól. A NAT több csomópontos rendszerekhez nem szükséges.

Az Azure Stack Development Kit az önálló és elkülönül a hálózaton, ahol a fizikai gazdagépen üzemel. A VIP-hálózat, amely az átjárók csatlakoznak, nem külső; rejtett hajt végre a hálózati címfordítás (NAT) használatával egy útválasztó mögé.

Az útválasztó az Útválasztás és távelérés szolgáltatást (RRAS) szerepkör fut a Windows Server virtuális gép (AzS-BGPNAT01). NAT kell konfigurálnia ahhoz, hogy a helyek közötti VPN-kapcsolat mindkét végén csatlakozásra AzS-BGPNAT01 virtuális gépen.

#### <a name="configure-the-nat"></a>A NAT konfigurálása

1. Jelentkezzen be rendszergazdai fiókjával az Azure Stack gazdagépen.
1. Másolja, és szerkessze a következő PowerShell-parancsfájlt. Cserélje le `your administrator password` az a rendszergazdai jelszót, és futtassa a parancsfájlt egy rendszergazda jogú PowerShell ISE-ben. Ez a szkript adja vissza a **külső BGPNAT cím**.

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "your administrator password" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```

1. Adja meg a NAT, másolja, és szerkessze a következő PowerShell-parancsfájlt. Szerkessze a szkriptet, cserélje le a `External BGPNAT address` és `Internal IP address` az alábbi példa értékekkel:

   * A *külső BGPNAT cím* 10.10.0.62 használata
   * A *belső IP-cím* 192.168.102.1 használata

   Futtassa a következő parancsfájl egy rendszergazda jogú PowerShell ISE-ben:

   ```powershell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501}
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Az Azure konfigurálása

Azure Stack konfigurálása után telepítheti az Azure-erőforrások. Az alábbi ábrán egy példa egy bérlői virtuális hálózathoz az Azure-ban. Bármilyen nevet és a címzési séma használhatja a virtuális hálózat az Azure-ban. Az Azure és az Azure Stack a virtuális hálózat címtartománya azonban egyedinek kell lennie, és nem lehetnek egymással átfedésben:

*3. ábra Azure virtuális hálózatok*

![Azure virtuális hálózatok](media/azure-stack-connect-expressroute/AzureArchitecture.png)

Az erőforrások központi telepítése az Azure-ban hasonlóak az Azure Stackben helyezett erőforrásokat. A következő összetevőket telepít:

* Virtuális hálózatok és alhálózatok
* Egy átjáró-alhálózat
* A virtuális hálózati átjáró
* A kapcsolat
* Az ExpressRoute-Kapcsolatcsoportok

A példában az Azure hálózati infrastruktúra konfigurációja a következő:

* Standard szintű központ (192.168.2.0/24) és (10.100.0.0./16) küllő virtuális hálózatok közötti modell. Küllős hálózati topológia kapcsolatos további információkért lásd: [küllős hálózati topológia implementálása az Azure-ban](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
* A munkaterhelések vannak üzembe helyezve a küllő virtuális hálózat és az agyi virtuális hálózat az ExpressRoute-kapcsolatcsoporthoz csatlakozik.
* A két virtuális hálózat virtuális hálózatok közötti társviszony-nel csatlakoztatott.

### <a name="configure-the-azure-vnets"></a>Az Azure virtuális hálózatok konfigurálása

1. Jelentkezzen be Azure hitelesítő adatait az Azure Portalon.
1. Hozzon létre az agyi virtuális hálózat a 192.168.2.0/24 címtartomány használatával.
1. Hozzon létre egy alhálózatot a 192.168.2.0/25 címtartományt használja, és adjon hozzá egy átjáró-alhálózatot a 192.168.2.128/27 címtartomány használatával.
1. Hozzon létre a küllő virtuális hálózat és alhálózat használatával a 10.100.0.0/16-címtartományt.

Az Azure-beli virtuális hálózatok létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy virtuális hálózatot](/azure/virtual-network/manage-virtual-network#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>ExpressRoute-kapcsolatcsoport konfigurálása

1. Tekintse át az ExpressRoute-Előfeltételek [ExpressRoute-Előfeltételek & checklist]((/azure/expressroute/expressroute-prerequisites).

1. Kövesse az [létrehozása és módosítása egy ExpressRoute circuit]((/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) egy ExpressRoute-kapcsolatcsoporttal az Azure-előfizetés létrehozásához.

   >[!NOTE]
   >Adjon a kulcs a kapcsolatcsoport a szolgáltatáshoz, hogy azok állíthat be az ExpressRoute-kapcsolatcsoport a végén.

1. Kövesse az [létrehozása és módosítása egy ExpressRoute circuit]((/azure/expressroute/expressroute-howto-routing-portal-resource-manager) konfigurálása a privát társviszony-létesítést az ExpressRoute-kapcsolatcsoport a társviszony-létesítést.

### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

Kövesse az [PowerShell]((/azure/expressroute/expressroute-howto-add-gateway-resource-manager) segítségével az agyi virtuális hálózat az ExpressRoute virtuális hálózati átjáró létrehozása az expressroute virtuális hálózati átjáró konfigurálása.

### <a name="create-the-connection"></a>A kapcsolat létrehozása

Az agyi virtuális hálózat az ExpressRoute-kapcsolatcsoport társítson, kövesse a [Csatlakozás az ExpressRoute circuit]((/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager) egy virtuális hálózatot.

### <a name="peer-the-vnets"></a>A virtuális hálózatok társítása

Az eseményközpont és a küllő virtuális hálózatok használata a lépések [egy virtuális hálózati társviszony-létesítés létrehozása az Azure portal használatával](/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal). Virtuális hálózatok közötti társviszony konfigurálásakor győződjön meg arról, hogy a következő beállításokkal:

* A központi menü küllőhöz **átjárótranzit engedélyezése**.
* A küllőkben a hubhoz való **távoli átjáró használata**.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A küllő virtuális hálózat üzembe helyezése a munkaterhelési virtuális gépeihez.

Ismételje meg ezeket a lépéseket minden olyan további bérlői virtuális hálózatok, amelyhez csatlakozni az Azure-ban a megfelelő ExpressRoute-Kapcsolatcsoportok keresztül.

## <a name="configure-the-router"></a>Az útválasztó konfigurálása

A következő ExpressRoute-útválasztó konfigurációs diagram segítségképp használható az ExpressRoute-útválasztó konfigurálása. Az ábrán két bérlő (1 bérlői és bérlői 2) a megfelelő ExpressRoute-Kapcsolatcsoportok az. Mindegyik bérlő saját VRF (virtuális útválasztást és a továbbítás) az ExpressRoute-útválasztó LAN és WAN oldalán van csatolva. Ez a konfiguráció biztosítja, hogy a két bérlőjéhez végpontok közötti elkülönítését. Jegyezze fel a példakonfigurációt végrehajtásával lehetősége van az útválasztó felületei használt IP-címeket.

*4. ábra Az ExpressRoute-útválasztó konfigurációját*

![Az ExpressRoute-útválasztó konfigurációját](media/azure-stack-connect-expressroute/EndToEnd.png)

Használhat bármilyen útválasztó, amely támogatja az IKEv2 VPN és BGP leállítja az Azure Stack site-to-site VPN-kapcsolatot. Az azonos útválasztó csatlakoztatása az Azure ExpressRoute-kapcsolatcsoporttal történő szolgál.

Az alábbi példa a Cisco ASR 1000 sorozatú összesítési szolgáltatás útválasztó konfiguráció támogatja a hálózati infrastruktúra, ahogyan a *ExpressRoute útválasztó-konfigurálási* diagramja.

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

## <a name="test-the-connection"></a>A kapcsolat tesztelése

A helyek közötti kapcsolat és az ExpressRoute-kapcsolatcsoport létrehozása után a kapcsolat teszteléséhez.

Hajtsa végre a következő ping-vizsgálatok:

* Jelentkezzen be az Azure virtuális hálózat virtuális gépei közül, és pingelni a virtuális gép az Azure Stack létrehozott.
* Jelentkezzen be a virtuális gépek az Azure Stack, és a virtuális gép az Azure virtuális hálózat létrehozott ping létrehozott egyik.

>[!NOTE]
>Ahhoz, hogy a helyek közötti és ExpressRoute-kapcsolatok keresztül küld forgalmat, kell pingelje egyaránt végén a virtuális gép dedikált IP (DIP)-címét és a virtuális gép nem a virtuális IP-címét.

### <a name="allow-icmp-in-through-the-firewall"></a>Az ICMP engedélyezése a tűzfalon keresztül

Alapértelmezés szerint a Windows Server 2016 nem engedélyezi a bejövő ICMP-csomagokat a tűzfalon keresztül. Minden virtuális gép használhat olyan ping teszteknél engedélyeznie kell a bejövő ICMP-csomagokat. Hozzon létre egy tűzfalszabályt az ICMP, futtassa a következő parancsmagot egy emelt szintű PowerShell-ablakban:

```powershell
# Create ICMP firewall rule.
New-NetFirewallRule `
  -DisplayName "Allow ICMPv4-In" `
  -Protocol ICMPv4
```

### <a name="ping-the-azure-stack-virtual-machine"></a>Az Azure Stack-virtuális gép pingelése

1. Jelentkezzen be az Azure Stack felhasználói portálra egy bérlői fiókkal.

1. Keresse meg a létrehozott virtuális gépet, és válassza ki a virtuális gépet.

1. Kattintson a **Csatlakozás** gombra.

1. Adja meg egy rendszergazda jogú Windows vagy a PowerShell-parancssort, **ipconfig/all**. Megjegyzés: a parancs kimenetében található IPv4-címet.

1. Pingelje az IPv4-címet a virtuális gépről az Azure virtuális hálózat.

   A példában a környezetben az IPv4-cím van 10.1.1.x/24 az alhálózatról. A környezetben előfordulhat, hogy a címe különböző, de a bérlői virtuális hálózat alhálózatához létrehozta az alhálózaton kell lennie.

### <a name="view-data-transfer-statistics"></a>Adatok adatátviteli statisztika megtekintése

Ha szeretné tudni, hogy mekkora forgalom, ha a kapcsolaton keresztül továbbítja, megtalálhatja ezeket az adatokat az Azure Stack felhasználói portálon. Ez az is jó módszer talál-e a ping teszt adatai végighaladt-e a VPN és ExpressRoute-kapcsolatok:

1. Jelentkezzen be az Azure Stack felhasználói portálon, a bérlő fiókjával, és válassza ki **összes erőforrás**.
1. A VPN-átjáró esetében keresse meg az erőforráscsoportot, és válassza ki a **kapcsolat** objektumtípus.
1. Válassza ki a **ConnectToAzure** kapcsolat a listából.
1. A **kapcsolatok** > **áttekintése**, megtekintheti a statisztikákat **adatok** és **adatforgalom**. Néhány nem nulla értéket kell megjelennie.

   ![Az adatok és a kimenő adatforgalom](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>További lépések

[Alkalmazások üzembe helyezése az Azure és az Azure Stackben](../user/azure-stack-solution-pipeline.md )
