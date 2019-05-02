---
title: Site-to-site VPN-kapcsolat létrehozása két virtuális hálózat között eltérő Azure Stack Development Kit környezetek közötti |} A Microsoft Docs
description: Lépésről lépésre haladó eljárás, amely a felhő rendszergazdájának használja a site-to-site VPN-kapcsolat létrehozása két egycsomópontos Azure Stack Development Kit környezetek között.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 09/12/2018
ROBOTS: NOINDEX
ms.openlocfilehash: cee1ca68caa6742eb5d965b53b685746d9057691
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985393"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Site-to-site VPN-kapcsolat létrehozása két virtuális hálózat között eltérő Azure Stack Development Kit környezetek között

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti, hogyan hozhat létre egy helyek közötti VPN-kapcsolat két különálló Azure Stack Development Kit (ASDK) környezetekben a két virtuális hálózat között. Amikor konfigurálja a kapcsolatokat, megtudhatja, hogyan működnek a VPN-átjárók az Azure Stackben.

### <a name="connection"></a>Kapcsolat

A következő ábrán látható, hogy mi a kapcsolat konfigurációját hasonlóan kell kinéznie elkészült.

![Site-to-site VPN-kapcsolat konfigurálása](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Előzetes teendők

Fejezze be a kapcsolat konfigurációját, hogy gondoskodjon arról, hogy a következő elemek megkezdése előtt:

* Két kiszolgálót és egyéb előfeltételeket, amelyek megfelelnek a ASDK hardverkövetelmények leírtak szerint [a rövid útmutató: Az Azure Stack Development Kit kiértékelése](../asdk/asdk-download.md).
* A [Azure Stack Development Kit](https://azure.microsoft.com/overview/azure-stack/try/) központi telepítési csomagot.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Az Azure Stack Development Kit környezetek üzembe helyezése

Fejezze be a kapcsolat konfigurációját, két ASDK környezetet kell telepíteni.

> [!NOTE]
> Minden üzembe helyezett ASDK, kövesse a [üzembe helyezési utasítások](../asdk/asdk-install.md). Ebben a cikkben a ASDK környezetek az úgynevezett **POC1** és **POC2**.

## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Készítse elő a poc1-ben és a POC2-ajánlat

A poc1-ben és a poc2 környezetben készítse elő egy ajánlatra, hogy egy felhasználó előfizetnek az ajánlatra, és a virtuális gépek üzembe helyezése. Az ajánlat létrehozásáról információért lásd: [virtuális gépek az Azure Stack-felhasználók számára elérhetővé](azure-stack-tutorial-tenant-vm.md).

## <a name="review-and-complete-the-network-configuration-table"></a>Tekintse át és fejezze be a hálózati konfiguráció tábla

A következő táblázat összefoglalja a hálózati konfiguráció mindkét ASDK környezetek esetében. Az eljárással, amely akkor jelenik meg a hálózat jellemző külső BGPNAT cím hozzáadásához a táblázat után.

### <a name="network-configuration-table"></a>Hálózati konfiguráció táblázat

|   |POC1|POC2|
|---------|---------|---------|
|Virtuális hálózat neve     |VNET-01|VNET-02 |
|Virtuális hálózat címtere |10.0.10.0/23|10.0.20.0/23|
|Alhálózat neve     |Subnet-01|Subnet-02|
|Alhálózati címtartomány|10.0.10.0/24 |10.0.20.0/24 |
|Átjáró alhálózata     |10.0.11.0/24|10.0.21.0/24|
|External BGPNAT address     |         |         |

> [!NOTE]
> A példakörnyezetben a külső BGPNAT IP-címek: a poc1 10.16.167.195 és for POC2 10.16.169.131. Az alábbi eljárással a BGPNAT IP-címek meghatározása a ASDK gazdagépeket, és adja hozzá az előző táblázatban a hálózati konfiguráció.

### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Az IP-cím beszerzése a NAT virtuális gépének külső adapteréről

1. Jelentkezzen be a POC1 Azure Stack fizikai gépére.
2. Szerkessze a rendszergazdai jelszót cserélje le a következő PowerShell-kódot, és futtassa a kódot a POC gazdagépen:

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```

3. Az IP-cím hozzáadása a hálózati konfiguráció táblához, amely az előző szakaszban jelenik meg.

4. Ennek az eljárásnak a poc2 környezetben.

## <a name="create-the-network-resources-in-poc1"></a>A hálózati erőforrások létrehozása a poc1-ben

Most be kell állítani az átjárók be a poc1-ben hálózati erőforrások is létrehozhat. Az alábbi utasításokat követve bemutatják, hogyan hozhat létre erőforrásokat az Azure Stack felhasználói portál használatával. PowerShell-kód segítségével is az erőforrások létrehozásához.

![A munkafolyamat, amellyel erőforrások létrehozása](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Jelentkezzen be bérlőként

Szolgáltatás-rendszergazda bejelentkezhet bérlőként teszteléséhez a csomagok, ajánlatok és előfizetések, amely a bérlői használhatnak. Ha még nem rendelkezik ilyennel, [hozzon létre egy bérlői fiókot](azure-stack-add-new-user-aad.md) előtt jelentkezzen be.

### <a name="create-the-virtual-network-and-vm-subnet"></a>A virtuális hálózat és a virtuálisgép-alhálózat létrehozása

1. Jelentkezzen be a felhasználói portálra egy bérlői fiókot használni.
2. Válassza ki a felhasználói portálon **+ erőforrás létrehozása**.
3. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
4. Válassza ki a **Virtuális hálózatot**.
5. A **neve**, **címtér**, **alhálózatnév**, és **alhálózati címtartomány**, használja az értékeket, amelyeket korábban a hálózati jelennek meg konfigurációs tábla.
6. A **előfizetés**, a korábban létrehozott előfizetés jelenik meg.
7. A **erőforráscsoport**, hozzon létre egy erőforráscsoportot, vagy ha már rendelkezik ilyennel, válassza ki a **meglévő**.
8. Ellenőrizze az alapértelmezett helyet.
9. Válassza a **Rögzítés az irányítópulton** lehetőséget.
10. Kattintson a **Létrehozás** gombra.

### <a name="create-the-gateway-subnet"></a>Az átjáróalhálózat létrehozása

1. Az irányítópulton nyissa meg a VNET-01 virtuálishálózat-erőforrást, amelyet korábban hozott létre.
2. A **Beállítások** panelen válassza az **Alhálózatok** elemet.
3. Adjon hozzá egy átjáró-alhálózatot a virtuális hálózathoz, jelölje be **átjáró-alhálózat**.

    ![Átjáróalhálózat hozzáadása](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Alapértelmezés szerint az alhálózat neve van beállítva, **GatewaySubnet**. Átjáró-alhálózatok egyediek. A megfelelő működéshez kell használniuk a **GatewaySubnet** nevét.
5. A **címtartomány**, győződjön meg arról, hogy a cím **10.0.11.0/24**.
6. Válassza ki **OK** az átjáró-alhálózat létrehozásához.

### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

1. Az Azure Portalon válassza ki a **+ erőforrás létrehozása**.
2. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
3. Hálózati erőforrások listájából válassza ki a **virtuális hálózati átjáró**.
4. A **neve**, adja meg **GW1**.
5. Válassza ki a **virtuális hálózati** elemet válassza ki a virtuális hálózatot. Válassza ki **VNET-01** a listából.
6. Válassza ki a **nyilvános IP-cím** menüpontot. Ha a **nyilvános IP-cím választása** ablak megnyílik, válassza ki **új létrehozása**.
7. A **neve**, adja meg **GW1-PiP**, majd válassza ki **OK**.
8. Alapértelmezés szerint a **VPN-típust**, **útvonalalapú** van kiválasztva. Tartsa a **útvonalalapú** VPN-típust.
9. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Az erőforrás az irányítópulton is rögzíthet. Kattintson a **Létrehozás** gombra.

### <a name="create-the-local-network-gateway"></a>A helyi hálózati átjáró létrehozása

A *helyi hálózati átjárók* megvalósítása ebben az Azure Stack értékelési telepítésben kissé eltér egy tényleges Azure-telepítéstől.

Azure-környezetben a helyi hálózati átjáró-a helyszíni (a bérlői) fizikai eszközt, amellyel csatlakozhat az Azure-beli virtuális hálózati átjáró jelöli. Az Azure Stack értékelési telepítés esetében a kapcsolat mindkét végén virtuális hálózati átjárók.

Gondoljon erre általánosabb értelemben úgy, hogy a helyi hálózati átjáró erőforrás minden esetben azt jelzi, hogy a kapcsolat másik végén lévő távoli átjárót. Az Azure Stack Development Kit tervezésétől miatt meg kell adnia a külső hálózati adapter IP-címét a hálózati címfordítást (NAT) a más ASDK a helyi hálózati átjáró nyilvános IP-címet a virtuális gép. Ezután hozzon létre NAT-leképezéseket, győződjön meg arról, hogy mindkét végpont megfelelően csatlakozik-e a NAT virtuális gépén.

### <a name="create-the-local-network-gateway-resource"></a>A helyi hálózati átjáró erőforrás létrehozása

1. Jelentkezzen be a POC1 Azure Stack fizikai gépére.
2. Válassza ki a felhasználói portálon **+ erőforrás létrehozása**.
3. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
4. Az erőforrások listájában válassza ki a **helyi hálózati átjáró**.
5. A **neve**, adja meg **POC2-GW**.
6. A **IP-cím**, adja meg a külső BGPNAT címét for POC2. Ez a cím jelenik meg a korábban a hálózati konfiguráció táblázatban.
7. A **címtér**, írja be a POC2 később létrehozott virtuális hálózat címteréhez, **10.0.20.0/23**.
8. Ellenőrizze, hogy a **előfizetés**, **erőforráscsoport**, és **hely** értékek helyesek, és válassza ki **létrehozás**.

### <a name="create-the-connection"></a>A kapcsolat létrehozása

1. Válassza ki a felhasználói portálon **+ erőforrás létrehozása**.
2. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
3. Az erőforrások listájában válassza ki a **kapcsolat**.
4. Az a **alapjai** beállítások panelen a a **kapcsolattípus**, jelölje be **Site-to-site (IPSec)**.
5. Válassza ki a **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
6. Az a **beállítások** panelen válassza ki **virtuális hálózati átjáró**, majd válassza ki **GW1**.
7. Válassza ki **helyi hálózati átjáró**, majd válassza ki **POC2-GW**.
8. A **kapcsolatnevet**, adja meg **POC1-POC2**.
9. A **megosztott kulcs (PSK)**, adja meg **12345**, majd válassza ki **OK**.
10. Az a **összefoglalás** panelen válassza ki **OK**.

### <a name="create-a-vm"></a>Virtuális gép létrehozása

Ellenőrizze az adatokat, a VPN-kapcsolaton keresztül haladó, a virtuális gépek adatokat küldeni és fogadni az egyes Azure Stack Development Kit szükséges. Most már a poc1-ben hozzon létre egy virtuális gépet, és majd a virtuális hálózaton helyezi azt a Virtuálisgép-alhálózatot.

1. Az Azure Portalon válassza ki a **+ erőforrás létrehozása**.
2. Lépjen a **Marketplace**, majd válassza ki **számítási**.
3. A virtuálisgép-rendszerképek listájában válassza ki a **Windows Server 2016 Datacenter próbaverzió** kép.
4. Az a **alapjai** panelen, a **neve**, adja meg **VM01**.
5. Érvényes felhasználónevet és jelszót adjon meg. Ez a fiók használatával jelentkezzen be a virtuális gép létrehozása után.
6. Adjon meg egy **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
7. Az a **mérete** panelen ebben az esetben a virtuálisgép-méretet, majd válassza ki és **kiválasztása**.
8. Az a **beállítások** panelen elfogadhatja az alapértelmezett beállításokat. Ügyeljen arra, hogy a **VNET-01** virtuális hálózat van kiválasztva. Győződjön meg arról, hogy az alhálózat beállítása pedig **10.0.10.0/24**. Ezután kattintson az **OK** gombra.
9. Az a **összefoglalás** panelen tekintse át a beállításokat, majd válassza ki **OK**.

## <a name="create-the-network-resources-in-poc2"></a>A hálózati erőforrások létrehozása a POC2-ben

A következő lépés, hogy a hálózati erőforrások létrehozása for POC2. Az alábbi utasításokat követve bemutatják, hogyan hozhat létre erőforrásokat a felhasználói portál használatával.

### <a name="sign-in-as-a-tenant-again"></a>Jelentkezzen be bérlőként újra

Szolgáltatás-rendszergazda bejelentkezhet bérlőként teszteléséhez a csomagok, ajánlatok és előfizetések, amely a bérlői használhatnak. Ha még nem rendelkezik ilyennel, [hozzon létre egy bérlői fiókot](azure-stack-add-new-user-aad.md) előtt jelentkezzen be.

### <a name="create-virtual-network-and-vm-subnet"></a>Virtuális hálózat és a Virtuálisgép-alhálózat létrehozása

1. Jelentkezzen be a bérlői fiókkal.
2. Válassza ki a felhasználói portálon **+ erőforrás létrehozása**.
3. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
4. Válassza ki a **Virtuális hálózatot**.
5. Korábban a hálózati konfiguráció táblázatban szereplő információk segítségével azonosítsa azokat az értékeket a poc2 **neve**, **címtér**, **alhálózatnév**, és **Alhálózati címtartomány**.
6. A **előfizetés**, a korábban létrehozott előfizetés jelenik meg.
7. A **erőforráscsoport**, hozzon létre egy új erőforráscsoportot, vagy ha már rendelkezik ilyennel, válassza ki a **meglévő**.
8. Az alapértelmezett ellenőrzése **hely**.
9. Válassza a **Rögzítés az irányítópulton** lehetőséget.
10. Kattintson a **Létrehozás** gombra.

### <a name="create-gateway-subnet"></a>Átjáró-alhálózat létrehozása

1. Nyissa meg a létrehozott virtuálishálózat-erőforrást (**VNET-02**) az irányítópultról.
2. A **Beállítások** panelen válassza az **Alhálózatok** elemet.
3. Válassza ki **átjáró-alhálózat** , adjon hozzá egy átjáró-alhálózatot a virtuális hálózathoz.
4. Az alhálózat neve alapértelmezés szerint **GatewaySubnet**. Az átjáró-alhálózatok egyediek, és a megfelelő működéshez ezzel az adott névvel kell rendelkezniük.
5. Az a **címtartomány** mezőben ellenőrizze, hogy a cím **10.0.21.0/24**.
6. Válassza ki **OK** az átjáró-alhálózat létrehozásához.

### <a name="create-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

1. Az Azure Portalon válassza ki a **+ erőforrás létrehozása**.  
2. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
3. Hálózati erőforrások listájából válassza ki a **virtuális hálózati átjáró**.
4. A **neve**, adja meg **GW2**.
5. Virtuális hálózat kiválasztásához jelölje ki a **virtuális hálózati**. Válassza ki **VNET-02** a listából.
6. Válassza a **Nyilvános IP-cím** elemet. Ha a **nyilvános IP-cím választása** panel megnyílik, válassza ki **új létrehozása**.
7. A **neve**, adja meg **GW2-PiP**, majd válassza ki **OK**.
8. Alapértelmezés szerint a **VPN-típust**, **útvonalalapú** van kiválasztva. Tartsa a **útvonalalapú** VPN-típust.
9. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Az erőforrás az irányítópulton is rögzíthet. Kattintson a **Létrehozás** gombra.

### <a name="create-local-network-gateway-resource"></a>Helyi hálózati átjáró erőforrás létrehozása

1. Jelölje be a POC2 felhasználói portálon **+ erőforrás létrehozása**.
2. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
3. Az erőforrások listájában válassza ki a **helyi hálózati átjáró**.
4. A **neve**, adja meg **POC1-GW**.
5. A **IP-cím**, adja meg a külső BGPNAT címét a poc1-ben, amely korábban a hálózati konfiguráció táblázatban szerepel.
6. A **címtér**, írja be a poc1-ben, a **10.0.10.0/23** címtere **VNET-01**.
7. Ellenőrizze, hogy a **előfizetés**, **erőforráscsoport**, és **hely** helyes-e, és válassza ki **létrehozás**.

## <a name="create-connection"></a>Kapcsolat létrehozása

1. Válassza ki a felhasználói portálon **+ erőforrás létrehozása**.
2. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
3. Az erőforrások listájában válassza ki a **kapcsolat**.
4. Az a **alapszintű** beállítások panelen a a **kapcsolattípus**, válassza a **Site-to-site (IPSec)**.
5. Válassza ki a **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
6. Az a **beállítások** panelen válassza ki **virtuális hálózati átjáró**, majd válassza ki **GW2**.
7. Válassza ki **helyi hálózati átjáró**, majd válassza ki **POC1-GW**.
8. A **kapcsolatnevet**, adja meg **POC2-POC1**.
9. A **megosztott kulcs (PSK)**, adja meg **12345**. Ha egy másik értéket választja, ne feledje, hogy meg kell felelnie a poc1-ben létrehozott megosztott kulcs értékének. Kattintson az **OK** gombra.
10. Tekintse át a **összefoglalás** panelen, és válassza ki **OK**.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Most hozzon létre egy virtuális gépet a POC2-ben, és a virtuális hálózat Virtuálisgép-alhálózatára helyezi azt:

1. Az Azure Portalon válassza ki a **+ erőforrás létrehozása**.
2. Lépjen a **Marketplace**, majd válassza ki **számítási**.
3. A virtuálisgép-rendszerképek listájában válassza ki a **Windows Server 2016 Datacenter próbaverzió** kép.
4. Az a **alapjai** panelen a **neve**, adja meg **VM02**.
5. Érvényes felhasználónevet és jelszót adjon meg. Ez a fiók használatával jelentkezzen be a virtuális gép létrehozása után.
6. Adjon meg egy **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
7. Az a **mérete** panelen válassza ki a virtuális gépek mérete ehhez a példányhoz, és válassza **kiválasztása**.
8. Az a **beállítások** panelen elfogadhatja az alapértelmezett értékeket. Ügyeljen arra, hogy a **VNET-02** virtuális hálózat van kiválasztva, és győződjön meg arról, hogy az alhálózat beállítása pedig **10.0.20.0/24**. Kattintson az **OK** gombra.
9. Tekintse át a beállításokat a **összefoglalás** panelen, és válassza ki **OK**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>A NAT virtuális gép konfigurálásához az egyes Azure Stack Development Kit átjáróátjáráshoz

Mivel a ASDK önálló, és elkülönül a fizikai gazdagép, amelyre telepítve van, a *külső* VIP-hálózat, amely az átjárók csatlakoznak, nem ténylegesen külső. A VIP-hálózat helyette, amely végrehajtja a hálózati címfordítás útválasztó mögé van elrejtve.

Az útválasztó nem egy Windows Server virtuális gépet, nevű **AzS-bgpnat01**, az Útválasztás és távelérés szolgáltatást (RRAS) szerepkör, amely futtatja az ASDK infrastruktúra. NAT kell konfigurálnia, hogy a helyek közötti VPN-kapcsolat mindkét végén csatlakozásra AzS-bgpnat01 virtuális gépen.

A VPN-kapcsolat konfigurálásához létre kell hoznia egy statikus NAT térkép útvonalat, amely a BGPNAT virtuális gép külső felületét az edge-átjárókészlet virtuális IP-cím van leképezve. Egy statikus NAT-leképezés útvonalat szükség minden egyes porthoz, a VPN-kapcsolatot.

> [!NOTE]
> Ez a konfiguráció csak az Azure Stack Development Kit környezetben szükség.
>
>

### <a name="configure-the-nat"></a>A NAT konfigurálása

> [!IMPORTANT]
> Ez az eljárás mindkét ASDK környezetben kell végrehajtania.

1. Határozza meg a **belső IP-cím** használatára a következő PowerShell-parancsfájlt. Nyissa meg a virtuális hálózati átjáró (GW1 és GW2), majd a a **áttekintése** panelen mentse az értéket a **nyilvános IP-cím** későbbi használatra.

   ![Belső IP-címe](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)

2. Jelentkezzen be a POC1 Azure Stack fizikai gépére.
3. Másolja, és szerkessze a következő PowerShell-parancsfájlt. Minden egyes Azure Stack Development Kit a NAT konfigurálását, futtassa a szkriptet egy rendszergazda jogú Windows PowerShell ISE-ben. A szkriptben adjon meg értékeket a `External BGPNAT address` és `Internal IP address` helye:

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. Ennek az eljárásnak a poc2 környezetben.

## <a name="test-the-connection"></a>A kapcsolat tesztelése

Most, hogy a helyek közötti kapcsolat létrejöttét követően ellenőrizze, hogy megjelenik a forgalom halad át. Ellenőrzéséhez jelentkezzen be az egyik ASDK környezetben létrehozott virtuális gépek egyikére. Ezt követően pingelni a virtuális gépet, a másik környezetben létrehozott.

Győződjön meg arról, hogy a forgalom átirányítására a helyek közötti kapcsolat küldjön, győződjön meg arról, hogy a távoli alhálózaton, nem a virtuális IP-CÍMEK a virtuális gép közvetlen IP (DIP) címének pingelése. Ehhez keresse meg a kapcsolat másik végén a DIP-címe. A cím későbbi használatra mentse.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Jelentkezzen be a bérlő virtuális Gépre a poc1-ben

1. Jelentkezzen be az Azure Stack fizikai gépre a poc1-ben, és a egy bérlői fiók használatával jelentkezzen be a felhasználói portálra.
2. A bal oldali navigációs sávon válassza **számítási**.
3. Virtuális gépek listáját, keresse meg **VM01** korábban létrehozott, és válassza ki azt.
4. A virtuális gép paneljén kattintson **Connect**, majd nyissa meg a VM01.rdp fájlt.

     ![Csatlakozás gomb](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)

5. Jelentkezzen be a virtuális gép létrehozásakor beállított fiókot.
6. Nyisson meg egy emelt szintű **Windows PowerShell** ablak.
7. Adja meg **ipconfig/all**.
8. A kimenetben keresse meg a **IPv4-cím**, majd mentse a cím későbbi használatra. Ez az a cím, amely fogja pingelni a poc2-ből. Ebben a példakörnyezetben a cím **10.0.10.4**, de az Ön környezetében ettől eltérő lehet. Alá kell tartozniuk a **10.0.10.0/24** alhálózatot, amelyet korábban hozott létre.
9. Hozzon létre egy tűzfalszabályt, amely lehetővé teszi, hogy a virtuális gép pingelésre, futtassa a következő PowerShell-parancsot:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Jelentkezzen be a bérlő virtuális Gépre a POC2-ben

1. Jelentkezzen be az Azure Stack fizikai gépére for POC2, és a egy bérlői fiók használatával jelentkezzen be a felhasználói portálra.
2. A bal oldali navigációs sávon kattintson **számítási**.
3. A virtuális gépek a listában található **VM02** korábban létrehozott, és válassza ki azt.
4. A virtuális gép paneljén kattintson a **Csatlakozás** elemre.
5. Jelentkezzen be a virtuális gép létrehozásakor beállított fiókot.
6. Nyisson meg egy emelt szintű **Windows PowerShell** ablak.
7. Adja meg **ipconfig/all**.
8. IPv4-cím jelenik meg, amely beleesik **10.0.20.0/24**. A példakörnyezetben a cím van **10.0.20.4**, de előfordulhat, hogy a címe különböző.
9. Hozzon létre egy tűzfalszabályt, amely lehetővé teszi, hogy a virtuális gép pingelésre, futtassa a következő PowerShell-parancsot:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

10. A virtuális gépről a poc2 környezetben Pingelje meg a virtuális gépet a poc1-ben, az alagúton keresztül. Ehhez a dedikált IP-CÍMMEL, amely a VM01 rögzített pingelje. Ez a példa a környezetben, **10.0.10.4**, de ügyeljen arra, hogy a tesztkörnyezetében feljegyzett címet pingelje. Az eredmény a következő példához hasonlóan néz ki kell megjelennie:

    ![Sikeres ping](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. A távoli virtuális gépről egy válasz sikeres tesztet jelez. Zárja be a virtuális gép ablakban. A kapcsolat teszteléséhez próbálkozzon más típusú adatforgalom, például a fájl másolatát.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Adatátviteli statisztika megtekintése az átjárókapcsolaton keresztül

Ha szeretné tudni, hogy mennyi adatot továbbítja a helyek közötti kapcsolaton keresztül, ez az információ érhető el a **kapcsolat** panelen. Ez a teszt akkor is győződjön meg arról, hogy az imént elküldött ping tényleg végighaladt a VPN-kapcsolatot egy másik módja.

1. A bérlő virtuális gépre a POC2-ben jelentkezett be, amíg a bérlői fiók használatával jelentkezzen be a felhasználói portál.
2. Lépjen a **összes erőforrás**, majd válassza ki a **POC2-POC1** kapcsolat. **Kapcsolatok** jelenik meg.
3. Az a **kapcsolat** ablakban, a statisztikai adatait **adatok** és **adatforgalom** jelennek meg. Az alábbi képernyőképen a nagy mennyiségű rendelnek a további fájlátvitel. Néhány nem nulla értéket kell megjelennie.

    ![Bejövő és kimenő adatforgalom](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
