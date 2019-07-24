---
title: Helyek közötti VPN-kapcsolat létrehozása két virtuális hálózat között különböző Azure Stack Development Kit környezetekben | Microsoft Docs
description: Lépésről lépésre haladó eljárás, amellyel a Cloud Administrator két egycsomópontos Azure Stack Development Kit környezet közötti helyek közötti VPN-kapcsolatot hoz létre.
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
ms.date: 07/16/2019
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 09/12/2018
ROBOTS: NOINDEX
ms.openlocfilehash: f34ed3459ad8346860872a4b63a25e214501a2dd
ms.sourcegitcommit: 4139b507d6da98a086929da48e3b4661b70bc4f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68299468"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Helyek közötti VPN-kapcsolat létrehozása két virtuális hálózat között különböző Azure Stack Development Kit környezetekben

## <a name="overview"></a>Áttekintés

Ez a cikk két különálló Azure Stack Development Kit (ASDK) környezetben két virtuális hálózat közötti helyek közötti VPN-kapcsolat létrehozását ismerteti. A kapcsolatok konfigurálásakor megismerheti, hogyan működik a VPN-átjárók Azure Stack.

### <a name="connection"></a>Kapcsolat

Az alábbi ábra azt mutatja be, hogy a kapcsolódási konfigurációnak hogyan kell kinéznie, ha elkészült.

![Helyek közötti VPN-kapcsolat konfigurációja](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Előkészületek

A kapcsolódási konfiguráció befejezéséhez győződjön meg arról, hogy a Kezdés előtt a következő elemek vannak:

* Két kiszolgáló és egyéb előfeltételek, amelyek megfelelnek a ASDK hardverkövetelmények követelményeinek, a rövid [útmutatóban leírtak szerint: Értékelje ki](../asdk/asdk-download.md)a Azure stack Development Kit.
* A [Azure stack Development Kit](https://azure.microsoft.com/overview/azure-stack/try/) központi telepítési csomag.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>A Azure Stack Development Kit környezetek üzembe helyezése

A kapcsolódási konfiguráció befejezéséhez két ASDK környezetet kell telepítenie.

> [!NOTE]
> Minden egyes telepített ASDK kövesse a [telepítési utasításokat](../asdk/asdk-install.md). Ebben a cikkben a ASDK-környezetek neve **POC1** és **POC2**.

## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Ajánlat előkészítése a POC1 és a POC2

A POC1 és a POC2 egyaránt előkészíti az ajánlatot, így a felhasználók előfizethetnek az ajánlatra, és üzembe helyezhetik a virtuális gépeket. Az ajánlatok létrehozásával kapcsolatos információkért lásd: [virtuális gépek elérhetővé tétele a Azure stack felhasználók](azure-stack-tutorial-tenant-vm.md)számára.

## <a name="review-and-complete-the-network-configuration-table"></a>A hálózati konfigurációs táblázat áttekintése és befejezése

A következő táblázat összefoglalja a ASDK-környezetek hálózati konfigurációját. A tábla után megjelenő eljárással adja hozzá a hálózatra jellemző külső BGPNAT-címeket.

### <a name="network-configuration-table"></a>Hálózati konfigurációs tábla

|   |POC1|POC2|
|---------|---------|---------|
|Virtuális hálózat neve     |VNET-01|VNET-02 |
|Virtuális hálózati címtartomány |10.0.10.0/23|10.0.20.0/23|
|Alhálózat neve     |Subnet-01|Subnet-02|
|Alhálózati címtartomány|10.0.10.0/24 |10.0.20.0/24 |
|Átjáró alhálózata     |10.0.11.0/24|10.0.21.0/24|
|Külső BGPNAT-címe     |         |         |

> [!NOTE]
> A példában a külső IP-BGPNAT 10.16.167.195 a POC1 és a 10.16.169.131 a POC2. A következő eljárással határozhatja meg a ASDK-gazdagépek külső BGPNAT IP-címeit, majd hozzáadhatja őket az előző hálózati konfigurációs táblához.

### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Az IP-cím beszerzése a NAT virtuális gépének külső adapteréről

1. Jelentkezzen be a Azure Stack fizikai gépre a POC1.
2. Szerkessze a következő PowerShell-kódot a rendszergazda jelszavának hozzáadásához, majd futtassa a kódot a POC-gazdagépen:

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

3. Adja hozzá az IP-címet az előző szakaszban megjelenő hálózati konfigurációs táblához.

4. Ismételje meg ezt az eljárást a POC2.

## <a name="create-the-network-resources-in-poc1"></a>Hálózati erőforrások létrehozása a POC1-ben

Most létrehozhatja az átjárók beállításához szükséges POC1-hálózati erőforrásokat. Az alábbi utasítások azt ismertetik, hogyan hozhatja létre az erőforrásokat a Azure Stack felhasználói portál használatával. Az erőforrások létrehozásához PowerShell-kód is használható.

![Erőforrások létrehozására szolgáló munkafolyamat](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Bejelentkezés bérlőként

A szolgáltatás-rendszergazda bejelentkezhet bérlőként a bérlők által használt csomagok, ajánlatok és előfizetések teszteléséhez. Ha még nem rendelkezik ilyennel, [hozzon létre egy bérlői fiókot](azure-stack-add-new-user-aad.md) a bejelentkezés előtt.

### <a name="create-the-virtual-network-and-vm-subnet"></a>A virtuális hálózat és a virtuálisgép-alhálózat létrehozása

1. Egy bérlői fiók használatával jelentkezzen be a felhasználói portálra.
2. A felhasználói portálon válassza az **+ erőforrás létrehozása**lehetőséget.
3. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
4. Válassza ki a **Virtuális hálózatot**.
5. A **név**, a **címterület**, az **alhálózat neve**és az alhálózati **címtartomány**mezőben a hálózati konfigurációs táblában korábban megjelenő értékeket használhatja.
6. Az **előfizetés**területen megjelenik a korábban létrehozott előfizetés.
7. **Erőforráscsoport**esetén létrehozhat egy erőforráscsoportot, vagy ha már rendelkezik ilyennel, válassza a **meglévő használata**lehetőséget.
8. Ellenőrizze az alapértelmezett helyet.
9. Válassza a **Rögzítés az irányítópulton** lehetőséget.
10. Kattintson a **Létrehozás** gombra.

### <a name="create-the-gateway-subnet"></a>Az átjáróalhálózat létrehozása

1. Az irányítópulton nyissa meg a korábban létrehozott VNET-01 virtuális hálózati erőforrást.
2. A **Beállítások** panelen válassza az **Alhálózatok** elemet.
3. Ha átjáró-alhálózatot szeretne hozzáadni a virtuális hálózathoz, válassza az **átjáró alhálózat**lehetőséget.

    ![Átjáró-alhálózat hozzáadása](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Alapértelmezés szerint az alhálózat neve **GatewaySubnet**értékre van állítva. Az átjáró-alhálózatok speciálisak. A megfelelő működéshez a **GatewaySubnet** nevet kell használnia.
5. A **címtartomány**területen ellenőrizze, hogy a címe **10.0.11.0/24**.
6. Az átjáró-alhálózat létrehozásához kattintson **az OK gombra** .

### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

1. A Azure Portal válassza az **+ erőforrás létrehozása**lehetőséget.
2. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
3. A hálózati erőforrások listájából válassza ki a **virtuális hálózati átjáró**elemet.
4. A **név**mezőben adja meg a **GW1**.
5. Válassza a **virtuális** hálózat elemet a virtuális hálózat kiválasztásához. Válassza ki a **VNET-01** elemet a listából.
6. Válassza ki a **nyilvános IP-cím** menüelemet. Amikor megnyílik a **nyilvános IP-cím választása** ablak, válassza az **új létrehozása**lehetőséget.
7. A **név**mezőben adja meg a **GW1-pip**nevet, majd kattintson **az OK gombra**.
8. Alapértelmezés szerint a **VPN-** típushoz **útvonal-alapú** beállítás van kiválasztva. Tartsa meg az **Útválasztás-alapú** VPN-típust.
9. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Az erőforrást rögzítheti az irányítópulton. Kattintson a **Létrehozás** gombra.

### <a name="create-the-local-network-gateway"></a>A helyi hálózati átjáró létrehozása

A *helyi hálózati átjárók* megvalósítása ebben az Azure Stack értékelési telepítésben kissé eltér egy tényleges Azure-telepítéstől.

Egy Azure-beli üzembe helyezés esetén a helyi hálózati átjáró egy helyszíni (bérlői) fizikai eszközre vonatkozik, amelyet az Azure-beli virtuális hálózati átjáróhoz való kapcsolódáshoz használ. Ebben a Azure Stack kiértékelési környezetben a kapcsolódás mindkét végpontja virtuális hálózati átjáró.

Erre általánosságban úgy gondolhat, hogy a helyi hálózati átjáró erőforrás mindig a távoli átjárót jelzi a Kapcsolódás másik végén. A Azure Stack Development Kit tervezésének módja miatt meg kell adnia a külső hálózati adapter IP-címét a másik ASDK hálózati címfordítási (NAT) virtuális gépén, a helyi hálózati átjáró nyilvános IP-címével. Ezután NAT-hozzárendeléseket hozhat létre a NAT virtuális gépen, hogy mindkét végpont megfelelően legyen csatlakoztatva.

### <a name="create-the-local-network-gateway-resource"></a>A helyi hálózati átjáró erőforrásának létrehozása

1. Jelentkezzen be a Azure Stack fizikai gépre a POC1.
2. A felhasználói portálon válassza az **+ erőforrás létrehozása**lehetőséget.
3. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
4. Az erőforrások listájából válassza a **helyi hálózati átjáró**elemet.
5. A **név**mezőben adja meg a **POC2-GW**értéket.
6. Az **IP-cím**mezőben adja meg a POC2 külső BGPNAT-címét. Ez a címe korábban a hálózati konfigurációs táblában jelenik meg.
7. A POC2-VNET később létrehozott címterület esetében adja meg a **10.0.20.0/23**értéket.
8. Győződjön meg arról, hogy az **előfizetés**, az **erőforráscsoport**és a **hely** értékei helyesek, majd válassza a **Létrehozás**lehetőséget.

### <a name="create-the-connection"></a>A kapcsolat létrehozása

1. A felhasználói portálon válassza az **+ erőforrás létrehozása**lehetőséget.
2. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
3. Az erőforrások listájából válassza a **kapcsolatok**lehetőséget.
4. Az **alapvető** beállítások panel **kapcsolat típusa**területén válassza a **helyek közötti (IPSec)** lehetőséget.
5. Válassza ki az előfizetést, az **erőforráscsoportot**és a **helyet**, majd kattintson **az OK gombra**.
6. A **Beállítások** panelen válassza a **virtuális hálózati átjáró**elemet, majd válassza a **GW1**lehetőséget.
7. Válassza a **helyi hálózati átjáró**lehetőséget, majd válassza a **POC2-GW**lehetőséget.
8. A **kapcsolatok neve**mezőben adja meg a **POC1-POC2**értéket.
9. A **megosztott kulcs (PSK)** mezőben adja meg a **12345**értéket, majd kattintson **az OK gombra**.
10. Az **Összefoglalás** panelen kattintson az **OK gombra**.

### <a name="create-a-vm"></a>Virtuális gép létrehozása

A VPN-kapcsolaton keresztül áthaladó adatellenőrzéshez szüksége lesz a virtuális gépekre az egyes Azure Stack Development Kitokban való adatküldéshez és fogadáshoz. Most hozzon létre egy virtuális gépet a POC1-ben, majd a virtuális hálózatán helyezze el a virtuálisgép-alhálózatán:

1. A Azure Portal válassza az **+ erőforrás létrehozása**lehetőséget.
2. Lépjen a **piactérre**, majd válassza a **számítás**lehetőséget.
3. A virtuálisgép-lemezképek listájában válassza ki a **Windows Server 2016 Datacenter eval** rendszerképét.
4. Az **alapvető beállítások** panelen, a **név**mezőben adja meg a **VM01**nevet.
5. Érvényes felhasználónevet és jelszót adjon meg. Ezzel a fiókkal jelentkezhet be a virtuális gépre a létrehozása után.
6. Adja meg az előfizetést, az **erőforráscsoportot**és a **helyet**, majd kattintson **az OK gombra**.
7. A **méret** panelen válassza ki a virtuális gép méretét, majd válassza a **kiválasztás**lehetőséget.
8. A **Beállítások** panelen fogadja el az alapértelmezett értékeket. Győződjön meg arról, hogy a **VNET-01** virtuális hálózat van kiválasztva. Ellenőrizze, hogy az alhálózat **10.0.10.0/24**értékre van-e állítva. Ezután kattintson az **OK** gombra.
9. Az **Összefoglalás** panelen tekintse át a beállításokat, majd kattintson az **OK gombra**.

## <a name="create-the-network-resources-in-poc2"></a>Hálózati erőforrások létrehozása a POC2-ben

A következő lépés a POC2 hálózati erőforrásainak létrehozása. Az alábbi utasítások bemutatják, hogyan hozhatja létre az erőforrásokat a felhasználói portál használatával.

### <a name="sign-in-as-a-tenant-again"></a>Jelentkezzen be újra bérlőként

A szolgáltatás-rendszergazda bejelentkezhet bérlőként a bérlők által használt csomagok, ajánlatok és előfizetések teszteléséhez. Ha még nem rendelkezik ilyennel, [hozzon létre egy bérlői fiókot](azure-stack-add-new-user-aad.md) a bejelentkezés előtt.

### <a name="create-virtual-network-and-vm-subnet"></a>Virtuális hálózat és virtuálisgép-alhálózat létrehozása

1. Jelentkezzen be egy bérlői fiók használatával.
2. A felhasználói portálon válassza az **+ erőforrás létrehozása**lehetőséget.
3. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
4. Válassza ki a **Virtuális hálózatot**.
5. A hálózati konfigurációs táblában korábban megjelenő információk alapján azonosíthatja a POC2, a **címtartomány**, az **alhálózat neve**és az  **alhálózat címtartomány**értékét.
6. Az **előfizetés**területen megjelenik a korábban létrehozott előfizetés.
7. **Erőforráscsoport**esetén hozzon létre egy új erőforráscsoportot, vagy ha már rendelkezik ilyennel, válassza a **meglévő használata**lehetőséget.
8. Ellenőrizze az alapértelmezett **helyet**.
9. Válassza a **Rögzítés az irányítópulton** lehetőséget.
10. Kattintson a **Létrehozás** gombra.

### <a name="create-gateway-subnet"></a>Átjáró-alhálózat létrehozása

1. Nyissa meg a létrehozott virtuális hálózati erőforrást (**VNET-02**) az irányítópultról.
2. A **Beállítások** panelen válassza az **Alhálózatok** elemet.
3. Válassza ki az **átjáró** alhálózatát, és adjon hozzá egy átjáró-alhálózatot a virtuális hálózathoz.
4. Az alhálózat neve alapértelmezés szerint **GatewaySubnet**. Az átjáró-alhálózatok egyediek, és a megfelelő működéshez ezzel az adott névvel kell rendelkezniük.
5. A **címtartomány** mezőben ellenőrizze, hogy a címe **10.0.21.0/24**.
6. Az átjáró-alhálózat létrehozásához kattintson **az OK gombra** .

### <a name="create-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

1. A Azure Portal válassza az **+ erőforrás létrehozása**lehetőséget.  
2. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
3. A hálózati erőforrások listájából válassza ki a **virtuális hálózati átjáró**elemet.
4. A **név**mezőben adja meg a **GW2**.
5. Virtuális hálózat kiválasztásához válassza a **virtuális hálózat**lehetőséget. Ezután válassza a **VNET-02** elemet a listából.
6. Válassza a **Nyilvános IP-cím** elemet. Amikor megnyílik a **nyilvános IP-cím választása** panel, válassza az **új létrehozása**lehetőséget.
7. A **név**mezőben adja meg a **GW2-pip**nevet, majd kattintson **az OK gombra**.
8. Alapértelmezés szerint a **VPN-** típushoz **útvonal-alapú** beállítás van kiválasztva. Tartsa meg az **Útválasztás-alapú** VPN-típust.
9. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Az erőforrást rögzítheti az irányítópulton. Kattintson a **Létrehozás** gombra.

### <a name="create-local-network-gateway-resource"></a>Helyi hálózati átjáró erőforrásának létrehozása

1. A POC2 felhasználói portálon válassza az **+ erőforrás létrehozása**lehetőséget.
2. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
3. Az erőforrások listájából válassza a **helyi hálózati átjáró**elemet.
4. A **név**mezőben adja meg a **POC1-GW**értéket.
5. Az **IP-cím**mezőben adja meg a POC1 külső BGPNAT-címét, amely a korábban a hálózati konfigurációs táblában szerepel.
6. A **címterület**terület POC1 részén adja meg a **VNET-01** **10.0.10.0/23** címtartományt.
7. Ellenőrizze, hogy az **előfizetés**, az **erőforráscsoport**és a **hely** helyes-e, majd válassza a **Létrehozás**lehetőséget.

## <a name="create-connection"></a>Kapcsolat létrehozása

1. A felhasználói portálon válassza az **+ erőforrás létrehozása**lehetőséget.
2. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
3. Az erőforrások listájából válassza a **kapcsolatok**lehetőséget.
4. Az alapszintű beállítások panelen, a **kapcsolat típusa mezőben**válassza a **helyek közötti (IPSec)** lehetőséget.
5. Válassza ki az előfizetést, az **erőforráscsoportot**és a **helyet**, majd kattintson **az OK gombra**.
6. A **Beállítások** panelen válassza a **virtuális hálózati átjáró**elemet, majd válassza a **GW2**lehetőséget.
7. Válassza a **helyi hálózati átjáró**lehetőséget, majd válassza a **POC1-GW**lehetőséget.
8. A **kapcsolatok neve**mezőben adja meg a **POC2-POC1**értéket.
9. A **megosztott kulcs (PSK)** mezőben adja meg a **12345**értéket. Ha más értéket választ, ne feledje, hogy meg kell egyeznie a POC1 létrehozott megosztott kulcs értékével. Kattintson az **OK** gombra.
10. Tekintse át az **Összefoglalás** panelt, majd kattintson **az OK gombra**.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Most hozzon létre egy virtuális gépet a POC2-ben, és helyezze a virtuális hálózata virtuálisgép-alhálózatára:

1. A Azure Portal válassza az **+ erőforrás létrehozása**lehetőséget.
2. Lépjen a **piactérre**, majd válassza a **számítás**lehetőséget.
3. A virtuálisgép-lemezképek listájában válassza ki a **Windows Server 2016 Datacenter eval** rendszerképét.
4. Az **alapvető beállítások** panelen, a **név**mezőbe írja be a következőt: **VM02**.
5. Érvényes felhasználónevet és jelszót adjon meg. Ezzel a fiókkal jelentkezhet be a virtuális gépre a létrehozása után.
6. Adja meg az előfizetést, az **erőforráscsoportot**és a **helyet**, majd kattintson **az OK gombra**.
7. A **méret** panelen válasszon ki egy virtuálisgép-méretet ehhez a példányhoz, majd válassza a **kiválasztás**lehetőséget.
8. A **Beállítások** panelen elfogadhatja az alapértelmezett értékeket. Győződjön meg arról, hogy a **VNET-02** virtuális hálózat van kiválasztva, és ellenőrizze, hogy az alhálózat **10.0.20.0/24**értékre van-e állítva. Kattintson az **OK** gombra.
9. Tekintse át a beállításokat az **Összefoglalás** panelen, majd kattintson az **OK gombra**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>A NAT virtuális gép konfigurálása minden Azure Stack Development Kit az átjáró bejárásához

Mivel a ASDK önálló és elkülönített a fizikai gazdagépet futtató hálózattól, a *külső* virtuális hálózat, amelyhez az átjárók csatlakoznak, nem a külső virtuális gép. Ehelyett a virtuális IP-hálózat el van rejtve egy olyan útválasztó mögött, amely elvégzi a hálózati címfordítást.

Az útválasztó egy **AzS-bgpnat01**nevű Windows Server rendszerű virtuális gép, amely az Útválasztás és távelérés szolgáltatás (RRAS) szerepkört futtatja a ASDK-infrastruktúrában. A AzS-bgpnat01 virtuális gépen konfigurálnia kell a NAT-t, hogy a helyek közötti VPN-kapcsolat mindkét végén csatlakozhasson.

A VPN-kapcsolat konfigurálásához létre kell hoznia egy statikus NAT-leképezési útvonalat, amely a BGPNAT virtuális gép külső felületét leképezi a peremhálózati átjáró készletének VIP-címére. A VPN-kapcsolat minden portján statikus NAT-leképezési útvonal szükséges.

> [!NOTE]
> Ez a konfiguráció csak Azure Stack Development Kit környezetekhez szükséges.

### <a name="configure-the-nat"></a>A NAT konfigurálása

> [!IMPORTANT]
> Ezt az eljárást mindkét ASDK-környezet esetében végre kell hajtania.

1. Határozza meg a következő PowerShell-parancsfájlban használandó **belső IP-címet** . Nyissa meg a virtuális hálózati átjárót (GW1 és GW2), majd az **Áttekintés** panelen mentse a **nyilvános IP-cím** értékét későbbi használatra.

   ![Belső IP-cím](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)

2. Jelentkezzen be a Azure Stack fizikai gépre a POC1.
3. Másolja és szerkessze a következő PowerShell-szkriptet. A NAT konfigurálásához az egyes Azure Stack Development Kiteken futtassa a parancsfájlt egy emelt szintű Windows PowerShell integrált parancsprogram-kezelési környezet. A szkriptben adja hozzá az értékeket a `External BGPNAT address` és `Internal IP address` a helyőrzőhöz:

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

4. Ismételje meg ezt az eljárást a POC2.

## <a name="test-the-connection"></a>A kapcsolat tesztelése

Most, hogy a helyek közötti kapcsolat létrejött, ellenőriznie kell, hogy a forgalom átáramlik-e. Az ellenőrzéshez jelentkezzen be a ASDK-környezetben létrehozott egyik virtuális gépre. Ezután Pingelje a másik környezetben létrehozott virtuális gépet.

Annak biztosítása érdekében, hogy a forgalmat a helyek közötti kapcsolaton keresztül küldje el, győződjön meg arról, hogy a távoli alhálózaton a virtuális gép közvetlen IP-címe (DIP) nem a VIP. Ehhez keresse meg a Kapcsolódás másik végén található DIP-címeket. Mentse a címeket későbbi használatra.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Jelentkezzen be a bérlői virtuális gépre a POC1-ben

1. Jelentkezzen be a Azure Stack fizikai gépre a POC1, majd egy bérlői fiók használatával jelentkezzen be a felhasználói portálra.
2. A bal oldali navigációs sávon válassza a **számítás**lehetőséget.
3. A virtuális gépek listájában keresse meg a korábban létrehozott **VM01** , majd jelölje ki.
4. A virtuális gép paneljén kattintson a **kapcsolat**elemre, majd nyissa meg a VM01. rdp fájlt.

     ![Csatlakozási gomb](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)

5. Jelentkezzen be azzal a fiókkal, amelyet a virtuális gép létrehozásakor konfigurált.
6. Nyisson meg egy emelt szintű **Windows PowerShell** -ablakot.
7. Adja meg az **ipconfig/all parancsot**.
8. A kimenetben keresse meg az **IPv4-címeket**, majd mentse a címeket későbbi használatra. Ez az a címe, amelyet a POC2 fog pingelni. Ebben a példakörnyezetben a cím **10.0.10.4**, de az Ön környezetében ettől eltérő lehet. Ennek a **10.0.10.0/24** alhálózaton belül kell lennie, amelyet korábban hozott létre.
9. A következő PowerShell-parancs futtatásával hozhat létre olyan tűzfalszabály, amely lehetővé teszi, hogy a virtuális gép válaszoljon a pingelésre:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Jelentkezzen be a bérlői virtuális gépre a POC2-ben

1. Jelentkezzen be a Azure Stack fizikai gépre a POC2, majd egy bérlői fiók használatával jelentkezzen be a felhasználói portálra.
2. A bal oldali navigációs sávon kattintson a **számítás**elemre.
3. A virtuális gépek listájából keresse meg a korábban létrehozott **VM02** , majd jelölje ki.
4. A virtuális gép paneljén kattintson a **Csatlakozás** elemre.
5. Jelentkezzen be azzal a fiókkal, amelyet a virtuális gép létrehozásakor konfigurált.
6. Nyisson meg egy emelt szintű **Windows PowerShell** -ablakot.
7. Adja meg az **ipconfig/all parancsot**.
8. Megjelenik egy IPv4-címe, amely a **10.0.20.0/24**tartományba esik. A példában szereplő környezet **10.0.20.4**, de a címe eltérő lehet.
9. A következő PowerShell-parancs futtatásával hozhat létre olyan tűzfalszabály, amely lehetővé teszi, hogy a virtuális gép válaszoljon a pingelésre:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

10. A POC2 virtuális gépről Pingelje a virtuális gépet a POC1, az alagúton keresztül. Ehhez Pingelje a VM01-ből rögzített DIP-t. A példában ez a **10.0.10.4**, de ügyeljen arra, hogy Pingelje a laborban feljegyzett címeket. A következő példához hasonló eredményt kell látnia:

    ![Sikeres pingelés](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. A távoli virtuális gép válasza sikeres tesztet jelez. A virtuális gép ablakát is lezárhatja. A kapcsolatok teszteléséhez más típusú adatátviteleket is kipróbálhat, például egy fájl másolását.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Adatátviteli statisztika megtekintése az átjárókapcsolaton keresztül

Ha tudni szeretné, hogy mennyi adat halad át a helyek közötti kapcsolaton keresztül, ez az információ a **kapcsolat** panelen érhető el. Ez a teszt azt is lehetővé teszi, hogy az imént elküldött pingelés valóban a VPN-kapcsolaton keresztül történjen.

1. Amikor bejelentkezett a POC2-ben a bérlői virtuális gépre, a bérlői fiókkal jelentkezzen be a felhasználói portálra.
2. Nyissa meg az **összes erőforrást**, majd válassza ki a **POC2-POC1-** kapcsolatokat. Megjelenik a **kapcsolatok** .
3. A **kapcsolatok** ablakban megjelenik az **adatok** és a **kimenő** adatok statisztikája. A következő képernyőképen a nagyméretű számok további fájlátviteli lehetőséget kapnak. Itt nem nulla értékeket kell látnia.

    ![Be-és kimenő adatterületek](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
