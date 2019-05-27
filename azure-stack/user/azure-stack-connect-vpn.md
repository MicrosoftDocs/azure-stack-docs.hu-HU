---
title: Csatlakozás Azure Stack Azure-hoz VPN |} A Microsoft Docs
description: Virtuális hálózatok az Azure Stack az Azure-ban a VPN virtuális hálózatokhoz való kapcsolódásának módja.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 10/24/2018
ms.openlocfilehash: f49c6f87ba3f33ae8d9c22068fa9ac4893ab7f0a
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/24/2019
ms.locfileid: "66197405"
---
# <a name="connect-azure-stack-to-azure-using-vpn"></a>Azure Stack csatlakozni az Azure VPN használatával

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk bemutatja, hogyan hozhat létre az Azure Stack egy virtuális hálózat csatlakoztatása egy virtuális hálózatot az Azure-ban a site-to-site VPN.

## <a name="before-you-begin"></a>Előkészületek

A kapcsolat konfigurációját, győződjön meg arról, hogy rendelkezik a következőkkel, mielőtt elkezdené:

* Az Azure Stackkel integrált rendszerek (több csomópontos) központi telepítés, amely közvetlenül csatlakozik az internethez. A külső, nyilvános IP-címtartomány kell lennie a nyilvános interneten keresztül közvetlenül elérhető.
* Egy érvényes Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes Azure-fiók Itt](https://azure.microsoft.com/free/?b=17.06).

### <a name="vpn-connection-diagram"></a>VPN-kapcsolati diagram

A következő ábrán látható, hogy mi a kapcsolat konfigurációját hasonlóan kell kinéznie eredménye:

![Site-to-site VPN-kapcsolat konfigurálása](media/azure-stack-connect-vpn/image2.png)

### <a name="network-configuration-example-values"></a>Hálózati konfiguráció példaértékek

A hálózati konfiguráció példák táblázatban példák ebben a cikkben használt értékeket. Ezeket az értékeket is használhat, vagy olvassa el segítségükkel értelmezheti a cikkben szereplő példákat:

|   |Azure Stack|Azure|
|---------|---------|---------|
|Virtuális hálózat neve     |Azs-VNet|AzureVNet |
|Virtuális hálózat címtere |10.1.0.0/16|10.100.0.0/16|
|Alhálózat neve     |Előtér|Előtér|
|Alhálózat címtartománya|10.1.0.0/24 |10.100.0.0/24 |
|Átjáróalhálózat     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>A hálózati erőforrások létrehozása az Azure-ban

Először hozza létre a hálózati erőforrások az Azure-hoz. A következőkben megtudhatja, hogyan hozhat létre az erőforrások használatával a [az Azure portal](https://portal.azure.com/).

### <a name="create-the-virtual-network-and-virtual-machine-vm-subnet"></a>A virtuális hálózatot és alhálózatot a virtuális gép (VM) létrehozása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) Azure-fiókjával.

2. Válassza ki a felhasználói portálon **+ erőforrás létrehozása**.
3. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
4. Válassza ki a **Virtuális hálózatot**.
5. Azonosítsa azokat az értékeket az Azure-ban a hálózati konfiguráció tábla adatait használja **neve**, **címtér**, **alhálózatnév**, és **alhálózati cím tartomány**.
6. A **erőforráscsoport**, hozzon létre egy új erőforráscsoportot, vagy ha már rendelkezik ilyennel, válassza ki a **meglévő**.
7. Válassza ki a **hely** virtuális hálózat.  A példában szereplő értékeket használja, ha válassza **USA keleti Régiójában** vagy egy másik helyet.
8. Válassza a **Rögzítés az irányítópulton** lehetőséget.
9. Kattintson a **Létrehozás** gombra.

### <a name="create-the-gateway-subnet"></a>Az átjáró-alhálózat létrehozása

1. Nyissa meg a létrehozott virtuális hálózati erőforrást (**AzureVNet**) az irányítópultról.
2. Az a **beállítások** szakaszban jelölje be **alhálózatok**.
3. Válassza ki **átjáró-alhálózat** , adjon hozzá egy átjáró-alhálózatot a virtuális hálózathoz.
4. Az alhálózat neve alapértelmezés szerint **GatewaySubnet**.

   >[!IMPORTANT]
   >Az átjáró-alhálózatok egyediek, és a megfelelő működéshez ezzel az adott névvel kell rendelkezniük.

5. Az a **címtartomány** mezőben ellenőrizze, hogy a cím **10.100.1.0/24**.
6. Válassza ki **OK** az átjáró-alhálózat létrehozásához.

### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

1. Az Azure Portalon válassza ki a **+ erőforrás létrehozása**.

2. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
3. Hálózati erőforrások listájából válassza ki a **virtuális hálózati átjáró**.
4. Az a **neve** mezőbe írja be a **Azure-GW**.
5. Virtuális hálózat kiválasztásához jelölje ki a **virtuális hálózati**. Válassza ki **AzureVnet** a listából.
6. Válassza a **Nyilvános IP-cím** elemet. Ha a **nyilvános IP-cím választása** szakasz megnyílik, válassza ki **új létrehozása**.
7. Az a **neve** mezőbe írja be a **Azure-GW-PiP**, majd válassza ki **OK**.
8. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Az erőforrás az irányítópulton is rögzíthet. Kattintson a **Létrehozás** gombra.

### <a name="create-the-local-network-gateway-resource"></a>A helyi hálózati átjáró erőforrás létrehozása

1. Az Azure Portalon válassza ki a **+ erőforrás létrehozása**.

2. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
3. Az erőforrások listájában válassza ki a **helyi hálózati átjáró**.
4. Az a **neve** mezőbe írja be a **Azs-GW**.
5. Az a **IP-cím** mezőbe írja be a nyilvános IP-cím az Azure Stack virtuális hálózati átjáró, amely korábban a hálózati konfiguráció táblázatban szerepel.
6. Az a **címtér** mezőt, az Azure Stack, írja be a **10.1.0.0/24** és **10.1.1.0/24** címtere **AzureVNet**.
7. Ellenőrizze, hogy a **előfizetés**, **erőforráscsoport**, és **hely** helyes-e, és válassza ki **létrehozás**.

## <a name="create-the-connection"></a>A kapcsolat létrehozása

1. Válassza ki a felhasználói portálon **+ erőforrás létrehozása**.
2. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
3. Az erőforrások listájában válassza ki a **kapcsolat**.
4. Az a **alapszintű** beállítások szakaszban, az a **kapcsolattípus**, válassza a **Site-to-site (IPSec)** .
5. Válassza ki a **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
6. Az a **beállítások** szakaszban jelölje be **virtuális hálózati átjáró**, majd válassza ki **Azure-GW**.
7. Válassza ki **helyi hálózati átjáró**, majd válassza ki **Azs-GW**.
8. A **kapcsolatnevet**, típus **Azure-Azs**.
9. A **megosztott kulcs (PSK)** , típus **12345**, majd **OK**.

   >[!NOTE]
   >Ha egy másik értéket a megosztott kulcsot használ, ne feledje, hogy a megosztott kulcsot, amely hoz létre a kapcsolat másik végén értékét meg kell egyeznie.

10. Tekintse át a **összefoglalás** szakaszt, és válassza ki **OK**.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Most egy virtuális gép létrehozása az Azure-ban, és a virtuális hálózat Virtuálisgép-alhálózatára helyezi azt.

1. Az Azure Portalon válassza ki a **+ erőforrás létrehozása**.
2. Lépjen a **Marketplace**, majd válassza ki **számítási**.
3. A Virtuálisgép-rendszerképek listájában válassza ki a **Windows Server 2016 Datacenter próbaverzió** kép.
4. Az a **alapjai** szakaszban a **neve**, típus **AzureVM**.
5. Írjon be egy érvényes felhasználónevet és jelszót. Ez a fiók használatával jelentkezzen be a virtuális gép létrehozása után.
6. Adjon meg egy **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
7. Az a **mérete** szakaszt, válassza ki a virtuális gép méretét, ehhez a példányhoz, és válassza ki **kiválasztása**.
8. Az a **beállítások** szakaszban használhatja az alapértelmezett beállításokat is. Kiválasztása előtt **OK**, ellenőrizze, hogy:

   * A **AzureVnet** virtuális hálózat van kiválasztva.
   * Az alhálózat beállítása pedig **10.100.0.0/24**.

   Kattintson az **OK** gombra.

9. Tekintse át a beállításokat a **összefoglalás** szakaszt, és válassza ki **OK**.

## <a name="create-the-network-resources-in-azure-stack"></a>A hálózati erőforrások létrehozása az Azure Stackben

Ezután hozza létre a hálózati erőforrások az Azure Stackben.

### <a name="sign-in-as-a-user"></a>Felhasználói bejelentkezés

A szolgáltatás-rendszergazda bejelentkezhet felhasználóként a csomagok, ajánlatok és előfizetések, amely a felhasználók tesztelése. Ha még nem rendelkezik ilyennel, [hozzon létre egy felhasználói fiókot](../operator/azure-stack-add-new-user-aad.md) előtt jelentkezzen be.

### <a name="create-the-virtual-network-and-a-vm-subnet"></a>A virtuális hálózat és a egy Virtuálisgép-alhálózat létrehozása

1. A felhasználói fiók használatával jelentkezzen be a felhasználói portálra.
2. Válassza ki a felhasználói portálon **+ erőforrás létrehozása**.

    ![Új virtuális hálózat létrehozása](media/azure-stack-connect-vpn/image3.png)

3. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
4. Válassza ki a **Virtuális hálózatot**.
5. A **neve**, **címtér**, **alhálózatnév**, és **alhálózati címtartomány**, használja a hálózati konfiguráció táblából az értékeket.
6. A **előfizetés**, a korábban létrehozott előfizetés jelenik meg.
7. A **erőforráscsoport**, hozzon létre egy erőforráscsoportot, vagy ha már rendelkezik ilyennel, válassza ki a **meglévő**.
8. Ellenőrizze az alapértelmezett helyet.
9. Válassza a **Rögzítés az irányítópulton** lehetőséget.
10. Kattintson a **Létrehozás** gombra.

### <a name="create-the-gateway-subnet"></a>Az átjáróalhálózat létrehozása

1. Az irányítópulton nyissa meg a létrehozott Azs-VNet virtuálishálózat-erőforrást.
2. Az a **beállítások** szakaszban jelölje be **alhálózatok**.
3. Adjon hozzá egy átjáró-alhálózatot a virtuális hálózathoz, jelölje be **átjáró-alhálózat**.

    ![Átjáróalhálózat hozzáadása](media/azure-stack-connect-vpn/image4.png)

4. Alapértelmezés szerint az alhálózat neve van beállítva, **GatewaySubnet**. Az átjáró-alhálózatokkal megfelelő működéséhez, kell használniuk a **GatewaySubnet** nevét.
5. A **címtartomány**, győződjön meg arról, hogy a cím **10.1.1.0/24**.
6. Válassza ki **OK** az átjáró-alhálózat létrehozásához.

### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

1. Válassza ki az Azure Stack portálon **+ erőforrás létrehozása**.
2. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
3. Hálózati erőforrások listájából válassza ki a **virtuális hálózati átjáró**.
4. A **neve**, típus **Azs-GW**.
5. Válassza ki a **virtuális hálózati** elemet válassza ki a virtuális hálózatot. Válassza ki **Azs-VNet** a listából.
6. Válassza ki a **nyilvános IP-cím** menüpontot. Ha a **nyilvános IP-cím választása** szakasz megnyílik, válassza ki **új létrehozása**.
7. A **neve**, típus **Azs-GW-PiP**, majd válassza ki **OK**.
8. Alapértelmezés szerint **útvonalalapú** van kiválasztva a **VPN-típust**. Tartsa a **útvonalalapú** VPN-típust.

9. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Az erőforrás az irányítópulton is rögzíthet. Kattintson a **Létrehozás** gombra.

### <a name="create-the-local-network-gateway"></a>A helyi hálózati átjáró létrehozása

A kiszolgálóvirtualizálás egy *helyi hálózati átjáró* az Azure Stackben egy kicsit más, mint Azure-környezetben.

Azure-környezetben a helyi hálózati átjáró jelöli egy (a felhasználó helyét) helyszíni fizikai eszköz az Azure-beli virtuális hálózati átjáró csatlakozik. Az Azure Stackben azonban a kapcsolat mindkét végén virtuális hálózati átjárók találhatók.

Általánosabb érvényűvé leírását, hogy a helyi hálózati átjáró erőforrás minden esetben azt jelzi, hogy a kapcsolat másik végén lévő távoli átjárót.

### <a name="create-the-local-network-gateway-resource"></a>A helyi hálózati átjáró erőforrás létrehozása

1. Jelentkezzen be az Azure Stack portálon.
2. Válassza ki a felhasználói portálon **+ erőforrás létrehozása**.
3. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
4. Az erőforrások listájában válassza ki a **helyi hálózati átjáró**.
5. Az a **neve** mezőbe írja be a **Azure-GW**.
6. Az a **IP-cím** mezőbe írja be a virtuális hálózati átjáró nyilvános IP-címét az Azure-ban **Azure-GW-PiP**. Ez a cím jelenik meg a korábban a hálózati konfiguráció táblázatban.
7. Az a **címtér** mezőjét, az Azure virtuális hálózat létrehozott, írja be a címtér **10.100.0.0/24** és **10.100.1.0/24**.

8. Ellenőrizze, hogy a **előfizetés**, **erőforráscsoport**, és **hely** értékek helyesek, és válassza ki **létrehozás**.

### <a name="create-the-connection"></a>A kapcsolat létrehozása

1. Válassza ki a felhasználói portálon **+ erőforrás létrehozása**.
2. Lépjen a **Marketplace**, majd válassza ki **hálózatkezelés**.
3. Az erőforrások listájában válassza ki a **kapcsolat**.
4. Az a **alapjai** beállítások szakaszban, az a **kapcsolattípus**, jelölje be **Site-to-site (IPSec)** .
5. Válassza ki a **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
6. Az a **beállítások** szakaszban jelölje be **virtuális hálózati átjáró**, majd válassza ki **Azs-GW**.
7. Válassza ki **helyi hálózati átjáró**, majd válassza ki **Azure-GW**.
8. A **kapcsolatnevet**, típus **Azs Azure-beli**.
9. A **megosztott kulcs (PSK)** , típus **12345**, majd válassza ki **OK**.

10. Az a **összefoglalás** szakaszban jelölje be **OK**.

### <a name="create-a-vm"></a>Virtuális gép létrehozása

A VPN-kapcsolat ellenőrzéséhez hozzon létre két virtuális gépet: az Azure-ban, és a egy, az Azure Stackben. Miután létrehozta a virtuális gépeken, használhatja őket a VPN-alagúton keresztül adatokat küldeni és fogadni.

1. Az Azure Portalon válassza ki a **+ erőforrás létrehozása**.
2. Lépjen a **Marketplace**, majd válassza ki **számítási**.
3. A Virtuálisgép-rendszerképek listájában válassza ki a **Windows Server 2016 Datacenter próbaverzió** kép.
4. Az a **alapjai** részben, a **neve**, típus **Azs-VM**.
5. Írjon be egy érvényes felhasználónevet és jelszót. Ez a fiók használatával jelentkezzen be a virtuális gép létrehozása után.
6. Adjon meg egy **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
7. Az a **mérete** szakaszban, ehhez a példányhoz válassza ki a virtuális gép méretét, és válassza ki **kiválasztása**.
8. Az a **beállítások** területen fogadja el az alapértelmezett értéket. Győződjön meg arról, hogy a **Azs-VNet** virtuális hálózat van kiválasztva. Győződjön meg arról, hogy az alhálózat beállítása pedig **10.1.0.0/24**. Ezután kattintson az **OK** gombra.

9. Az a **összefoglalás** szakaszt, tekintse át a beállításokat, és válassza ki **OK**.

## <a name="test-the-connection"></a>Kapcsolat tesztelése

A helyek közötti kapcsolat létrejötte után ellenőrizze, hogy mindkét irányban áramló adatok kérheti. A kapcsolat tesztelése a legegyszerűbb módja,-ping teszt elvégzésével:

* Jelentkezzen be a virtuális gép az Azure Stack- és ping a virtuális gép az Azure-ban létrehozott.
* Jelentkezzen be a virtuális gép Azure-ban létrehozott, és Pingelje meg a virtuális gép az Azure Stackben.

>[!NOTE]
>Ahhoz, hogy a helyek közötti kapcsolaton keresztül a forgalom küldése folyamatban van, pingelje a távoli alhálózaton, nem a virtuális IP-CÍMEK a virtuális gép közvetlen IP (DIP) címét.

### <a name="sign-in-to-the-user-vm-in-azure-stack"></a>Jelentkezzen be a felhasználói virtuális gép az Azure Stackben

1. Jelentkezzen be az Azure Stack portálon.
2. A bal oldali navigációs sávon válassza **virtuális gépek**.
3. Virtuális gépek listáját, keresse meg **Azs-VM** , amelyet korábban létrehozott, és válassza ki azt.
4. A szakasz a virtuális gép, jelölje be **Connect**, majd nyissa meg a Azs-VM.rdp fájlt.

     ![Csatlakozás gomb](media/azure-stack-connect-vpn/image17.png)

5. Jelentkezzen be a virtuális gép létrehozásakor beállított fiókot.
6. Nyisson meg egy rendszergazda jogú Windows PowerShell-parancssorban.
7. Írja be az **ipconfig /all** parancsot.
8. A kimenetben keresse meg a **IPv4-cím**, majd mentse a cím későbbi használatra. Ez az a cím, amely az Azure-ból pingelést. A példakörnyezetben a cím van **10.1.0.4**, de a környezetében ettől eltérő lehet. Alá kell tartozniuk a **10.1.0.0/24** alhálózatot, amelyet korábban hozott létre.
9. Hozzon létre egy tűzfalszabályt, amely lehetővé teszi, hogy a virtuális gép pingelésre, futtassa a következő PowerShell-parancsot:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Jelentkezzen be a bérlői virtuális Géphez az Azure-ban

1. Jelentkezzen be az Azure portálra.
2. A bal oldali navigációs sávon válassza **virtuális gépek**.
3. A virtuális gépek listáját, keresse meg a **Azure-beli virtuális gép** , amelyet korábban létrehozott, és válassza ki azt.
4. A szakasz a virtuális gép, jelölje be **Connect**.
5. Jelentkezzen be a virtuális gép létrehozásakor beállított fiókot.
6. Nyisson meg egy emelt szintű **Windows PowerShell** ablak.
7. Írja be az **ipconfig /all** parancsot.
8. Megtekintheti az IPv4-címmel, amely beleesik **10.100.0.0/24**. A példakörnyezetben a cím van **10.100.0.4**, de előfordulhat, hogy a címe különböző.
9. Hozzon létre egy tűzfalszabályt, amely lehetővé teszi, hogy a virtuális gép pingelésre, futtassa a következő PowerShell-parancsot:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

10. A virtuális gépről az Azure-ban Pingelje meg az Azure Stackben, a virtuális Gépet az alagúton keresztül. Ehhez a virtuális gépről Azs feljegyzett DIP pingelje. Ez a példa a környezetben, **10.1.0.4**, de ügyeljen arra, hogy a tesztkörnyezetében feljegyzett címet pingelje. Az alábbi képernyőfelvétel-készítés hasonló öröklési eredményt kell megjelennie:

    ![Sikeres ping](media/azure-stack-connect-vpn/image19b.png)

11. A távoli virtuális gépről kapott válasz sikeres tesztet jelez. Zárja be a virtuális gép ablakban.

Szigorúbb adatátvitel tesztelése (például eltérő méretű fájlok másolása mindkét irányban) is el kell végeznie.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Adatátviteli statisztika megtekintése az átjárókapcsolaton keresztül

Ha szeretné tudni, hogy mennyi adatot továbbítja a helyek közötti kapcsolaton keresztül, ez az információ érhető el a **kapcsolat** szakaszban. Ez a teszt akkor is egy másik módja az imént elküldött ping tényleg végighaladt a VPN-kapcsolat ellenőrzése.

1. Amíg a felhasználó virtuális Gépet az Azure Stackben bejelentkezett, a felhasználói fiók használatával jelentkezzen be a felhasználói portál.
2. Lépjen a **összes erőforrás**, majd válassza ki a **Azs Azure-beli** kapcsolat. **Kapcsolatok** jelenik meg.
3. Az a **kapcsolat** részben, a statisztikáit **adatok** és **kimenő adatforgalom** jelennek meg. Az alábbi képernyőfelvételen a nagy mennyiségű rendelnek a további fájlátvitel. Néhány nem nulla értéket kell megjelennie.

    ![Bejövő és kimenő adatforgalom](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>További lépések

* [Alkalmazások üzembe helyezése az Azure és az Azure Stackben](azure-stack-solution-pipeline.md)