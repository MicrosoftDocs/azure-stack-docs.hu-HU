---
title: Azure Stack csatlakozás az Azure-hoz VPN használatával | Microsoft Docs
description: Virtuális hálózatok összekötése az Azure-beli virtuális hálózatokkal a VPN használatával Azure Stack.
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
ms.date: 10/04/2019
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 10/24/2018
ms.openlocfilehash: 844162e4f31a6f543a9fe774aa40bd606dad85b9
ms.sourcegitcommit: f91979c1613ea1aa0e223c818fc208d902b81299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974113"
---
# <a name="connect-azure-stack-to-azure-using-vpn"></a>Azure Stack csatlakozás az Azure-hoz VPN használatával

*Vonatkozik: Azure Stack integrált rendszerek @ no__t-0

Ez a cikk azt ismerteti, hogyan lehet helyek közötti VPN-t létrehozni egy virtuális hálózat Azure Stack egy Azure-beli virtuális hálózatban való összekapcsolásához.

## <a name="before-you-begin"></a>Előkészületek

A kapcsolódási konfiguráció befejezéséhez győződjön meg arról, hogy rendelkezik a következő elemek elkezdése előtt:

* Azure Stack integrált rendszerek (több csomópontos) központi telepítése, amely közvetlenül csatlakozik az internethez. A külső nyilvános IP-címtartományt közvetlenül elérhetőnek kell lennie a nyilvános internetről.
* Érvényes Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, itt hozhat létre [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?b=17.06).

### <a name="vpn-connection-diagram"></a>VPN-kapcsolati diagram

A következő ábra azt mutatja be, hogy a kapcsolódási konfigurációnak hogyan kell kinéznie, amikor elkészült:

![Helyek közötti VPN-kapcsolat konfigurációja](media/azure-stack-connect-vpn/image2.png)

### <a name="network-configuration-example-values"></a>Hálózati konfigurációs példa értékei

A hálózati konfigurációval kapcsolatos példák táblázat a cikkben szereplő példákhoz használt értékeket jeleníti meg. Ezeket az értékeket használhatja, vagy megtekintheti őket, hogy jobban megértse a cikkben szereplő példákat:

|   |Azure Stack|Azure|
|---------|---------|---------|
|Virtuális hálózat neve     |Azs-VNet|AzureVNet |
|Virtuális hálózati címtartomány |10.1.0.0/16|10.100.0.0/16|
|Alhálózat neve     |Előtér|Előtér|
|Alhálózat címtartománya|10.1.0.0/24 |10.100.0.0/24 |
|Átjáróalhálózat     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>Hálózati erőforrások létrehozása az Azure-ban

Először hozza létre az Azure hálózati erőforrásait. Az alábbi utasítások bemutatják, hogyan hozhatja létre az erőforrásokat a [Azure Portal](https://portal.azure.com/)használatával.

### <a name="create-the-virtual-network-and-virtual-machine-vm-subnet"></a>A virtuális hálózat és a virtuális gép (VM) alhálózatának létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) az Azure-fiók használatával.

2. A felhasználói portálon válassza az **+ erőforrás létrehozása**lehetőséget.
3. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
4. Válassza ki a **Virtuális hálózatot**.
5. A hálózati konfiguráció tábla információi alapján azonosíthatja az Azure-beli **név**, a **címterület**, az **alhálózat neve**és az **alhálózat címtartomány**értékét.
6. **Erőforráscsoport**esetén hozzon létre egy új erőforráscsoportot, vagy ha már rendelkezik ilyennel, válassza a **meglévő használata**lehetőséget.
7. Válassza ki a VNet **helyét** .  Ha a példában szereplő értékeket használja, válassza az **USA keleti** régiója lehetőséget, vagy használjon másik helyet.
8. Válassza a **Rögzítés az irányítópulton** lehetőséget.
9. Kattintson a **Létrehozás** gombra.

### <a name="create-the-gateway-subnet"></a>Az átjáró alhálózatának létrehozása

1. Nyissa meg a létrehozott virtuális hálózati erőforrást (**AzureVNet**) az irányítópultról.
2. A **Beállítások** szakaszban válassza az **alhálózatok**lehetőséget.
3. Válassza ki az **átjáró alhálózatát** , és adjon hozzá egy átjáró-alhálózatot a virtuális hálózathoz.
4. Az alhálózat neve alapértelmezés szerint **GatewaySubnet**.

   >[!IMPORTANT]
   >Az átjáró-alhálózatok egyediek, és a megfelelő működéshez ezzel az adott névvel kell rendelkezniük.

5. A **címtartomány** mezőben ellenőrizze, hogy a címe **10.100.1.0/24**.
6. Az átjáró-alhálózat létrehozásához kattintson **az OK gombra** .

### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

1. A Azure Portal válassza az **+ erőforrás létrehozása**lehetőséget.

2. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
3. A hálózati erőforrások listájából válassza ki a **virtuális hálózati átjáró**elemet.
4. A név mezőbe írja be az **Azure-GW** **nevet** .
5. Virtuális hálózat kiválasztásához válassza a **virtuális hálózat**lehetőséget. Ezután válassza a **AzureVnet** elemet a listából.
6. Válassza a **Nyilvános IP-cím** elemet. Amikor megnyílik a **nyilvános IP-cím választása** szakasz, válassza az **új létrehozása**lehetőséget.
7. A név mezőbe írja be az **Azure-GW-pip** **nevet** , majd kattintson az **OK gombra**.
8. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Az erőforrást rögzítheti az irányítópulton. Kattintson a **Létrehozás** gombra.

### <a name="create-the-local-network-gateway-resource"></a>A helyi hálózati átjáró erőforrásának létrehozása

1. A Azure Portal válassza az **+ erőforrás létrehozása**lehetőséget.

2. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
3. Az erőforrások listájából válassza a **helyi hálózati átjáró**elemet.
4. A **név** mezőbe írja be a következőt: **AZS-GW**.
5. Az **IP-cím** mezőbe írja be a Azure stack Virtual Network ÁTJÁRÓ nyilvános IP-címét, amely a korábban a hálózati konfigurációs táblában szerepel.
6. A **címterület** mezőbe írja be a (Azure stack) értéket a **AzureVNet** **10.1.0.0/24** és **10.1.1.0/24** címtartomány mezőjébe.
7. Ellenőrizze, hogy az **előfizetés**, az **erőforráscsoport**és a **hely** helyes-e, majd válassza a **Létrehozás**lehetőséget.

## <a name="create-the-connection"></a>A kapcsolat létrehozása

1. A felhasználói portálon válassza az **+ erőforrás létrehozása**lehetőséget.
2. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
3. Az erőforrások listájából válassza a **kapcsolatok**lehetőséget.
4. Az **alapszintű** beállítások szakaszban, a **kapcsolat típusa**területen válassza a **helyek közötti (IPSec)** lehetőséget.
5. Válassza ki az **előfizetést**, az **erőforráscsoportot**és a **helyet**, majd kattintson **az OK gombra**.
6. A **Beállítások** szakaszban válassza ki a **virtuális hálózati átjáró**elemet, majd válassza az **Azure-GW**lehetőséget.
7. Válassza a **helyi hálózati átjáró**lehetőséget, majd válassza a **AZS-GW**lehetőséget.
8. A **kapcsolatok neve**mezőbe írja be az **Azure-AZS**nevet.
9. A **megosztott kulcs (PSK)** mezőbe írja be a **12345**értéket, majd kattintson **az OK gombra**.

   >[!NOTE]
   >Ha más értéket használ a megosztott kulcshoz, ne feledje, hogy meg kell egyeznie a kapcsolatok másik végén létrehozott megosztott kulcs értékével.

10. Tekintse át az **Összefoglalás** szakaszt, majd kattintson **az OK gombra**.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Most hozzon létre egy virtuális GÉPET az Azure-ban, és helyezze a virtuális hálózata virtuálisgép-alhálózatára.

1. A Azure Portal válassza az **+ erőforrás létrehozása**lehetőséget.
2. Lépjen a **piactérre**, majd válassza a **számítás**lehetőséget.
3. A virtuálisgép-rendszerképek listájában válassza ki a **Windows Server 2016 Datacenter eval** rendszerképét.
4. Az **alapismeretek** szakaszban a **név**mezőbe írja be a következőt: **AzureVM**.
5. Írjon be egy érvényes felhasználónevet és jelszót. Ezzel a fiókkal jelentkezhet be a virtuális gépre a létrehozása után.
6. Adja meg az **előfizetést**, az **erőforráscsoportot**és a **helyet**, majd kattintson **az OK gombra**.
7. A **méret** szakaszban válassza ki a virtuális gép méretét ehhez a példányhoz, majd válassza a **kiválasztás**lehetőséget.
8. A **Beállítások** szakaszban az alapértelmezett beállításokat használhatja. **Az OK gombra**kattintva erősítse meg a következőket:

   * A **AzureVnet** virtuális hálózat van kiválasztva.
   * Az alhálózat beállítása **10.100.0.0/24**.

   Kattintson az **OK** gombra.

9. Tekintse át a beállításokat az **Összefoglalás** szakaszban, majd kattintson az **OK gombra**.

## <a name="create-the-network-resources-in-azure-stack"></a>Hálózati erőforrások létrehozása a Azure Stackban

Ezután hozza létre a hálózati erőforrásokat a Azure Stackban.

### <a name="sign-in-as-a-user"></a>Bejelentkezés felhasználóként

A szolgáltatás-rendszergazda bejelentkezhet felhasználóként a felhasználók által használt csomagok, ajánlatok és előfizetések teszteléséhez. Ha még nem rendelkezik ilyennel, [hozzon létre egy felhasználói fiókot a](../operator/azure-stack-add-new-user-aad.md) bejelentkezés előtt.

### <a name="create-the-virtual-network-and-a-vm-subnet"></a>A virtuális hálózat és a virtuálisgép-alhálózat létrehozása

1. Felhasználói fiók használata a felhasználói portálra való bejelentkezéshez.
2. A felhasználói portálon válassza az **+ erőforrás létrehozása**lehetőséget.

    ![Új virtuális hálózat létrehozása](media/azure-stack-connect-vpn/image3.png)

3. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
4. Válassza ki a **Virtuális hálózatot**.
5. A **név**, a **címterület**, az **alhálózat neve**és az **alhálózat címtartomány**mezőben adja meg a hálózati konfiguráció tábla értékeit.
6. Az **előfizetés**területen megjelenik a korábban létrehozott előfizetés.
7. **Erőforráscsoport**esetén létrehozhat egy erőforráscsoportot, vagy ha már rendelkezik ilyennel, válassza a **meglévő használata**lehetőséget.
8. Ellenőrizze az alapértelmezett helyet.
9. Válassza a **Rögzítés az irányítópulton** lehetőséget.
10. Kattintson a **Létrehozás** gombra.

### <a name="create-the-gateway-subnet"></a>Az átjáróalhálózat létrehozása

1. Az irányítópulton nyissa meg a létrehozott AZS-VNet virtuális hálózati erőforrást.
2. A **Beállítások** szakaszban válassza az **alhálózatok**lehetőséget.
3. Ha átjáró-alhálózatot szeretne hozzáadni a virtuális hálózathoz, válassza az **átjáró alhálózat**lehetőséget.

    ![Átjáró-alhálózat hozzáadása](media/azure-stack-connect-vpn/image4.png)

4. Alapértelmezés szerint az alhálózat neve **GatewaySubnet**értékre van állítva. Ahhoz, hogy az átjáró-alhálózatok megfelelően működjenek, a **GatewaySubnet** nevet kell használniuk.
5. A **címtartomány**területen ellenőrizze, hogy a címe **10.1.1.0/24**.
6. Az átjáró-alhálózat létrehozásához kattintson **az OK gombra** .

### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása

1. A Azure Stack portálon válassza az **+ erőforrás létrehozása**lehetőséget.
2. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
3. A hálózati erőforrások listájából válassza ki a **virtuális hálózati átjáró**elemet.
4. A **név**mezőbe írja be a következőt: **AZS-GW**.
5. Válassza a **virtuális** hálózat elemet a virtuális hálózat kiválasztásához. Válassza a **AZS-VNet** elemet a listából.
6. Válassza ki a **nyilvános IP-cím** menüelemet. Amikor megnyílik a **nyilvános IP-cím választása** szakasz, válassza az **új létrehozása**lehetőséget.
7. A **név**mezőbe írja be a **AZS-GW-pip**nevet, majd kattintson **az OK gombra**.
8. Alapértelmezés szerint az **Útválasztás-alapú** beállítás a **VPN-típushoz**van kiválasztva. Tartsa meg az **Útválasztás-alapú** VPN-típust.

9. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Az erőforrást rögzítheti az irányítópulton. Kattintson a **Létrehozás** gombra.

### <a name="create-the-local-network-gateway"></a>A helyi hálózati átjáró létrehozása

Azure Stack egy *helyi hálózati átjáró* fogalma nem azonos egy Azure-környezetben.

Egy Azure-beli üzembe helyezés esetén a helyi hálózati átjáró egy helyszíni (felhasználói hely) fizikai eszköz, amely az Azure-beli virtuális hálózati átjáróhoz csatlakozik. Azure Stack azonban a kapcsolatok mindkét végpontja virtuális hálózati átjáró.

A további általános leírás szerint a helyi hálózati átjáró erőforrás mindig a távoli átjárót jelzi a kapcsolatok másik végén.

### <a name="create-the-local-network-gateway-resource"></a>A helyi hálózati átjáró erőforrásának létrehozása

1. Jelentkezzen be a Azure Stack portálra.
2. A felhasználói portálon válassza az **+ erőforrás létrehozása**lehetőséget.
3. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
4. Az erőforrások listájából válassza a **helyi hálózati átjáró**elemet.
5. A név mezőbe írja be az **Azure-GW** **nevet** .
6. Az **IP-cím** mezőben adja meg a virtuális hálózati átjáróhoz tartozó nyilvános IP-címet az Azure **-GW-pip-** ben. Ez a címe korábban a hálózati konfigurációs táblában jelenik meg.
7. A **címterület** mezőben a létrehozott Azure-VNET címterület mezőjébe írja be a következőt: **10.100.0.0/24** és **10.100.1.0/24**.

8. Győződjön meg arról, hogy az **előfizetés**, az **erőforráscsoport**és a **hely** értékei helyesek, majd válassza a **Létrehozás**lehetőséget.

### <a name="create-the-connection"></a>A kapcsolat létrehozása

1. A felhasználói portálon válassza az **+ erőforrás létrehozása**lehetőséget.
2. Lépjen a **piactérre**, majd válassza a **hálózatkezelés**lehetőséget.
3. Az erőforrások listájából válassza a **kapcsolatok**lehetőséget.
4. Az **alapvető** beállítások szakasz **kapcsolat típusa**területén válassza a **helyek közötti (IPSec)** lehetőséget.
5. Válassza ki az **előfizetést**, az **erőforráscsoportot**és a **helyet**, majd kattintson **az OK gombra**.
6. A **Beállítások** szakaszban válassza ki a **virtuális hálózati átjáró**elemet, majd válassza a **AZS-GW**lehetőséget.
7. Válassza a **helyi hálózati átjáró**lehetőséget, majd válassza az **Azure-GW**lehetőséget.
8. A **kapcsolatok neve**mezőbe írja be a következőt: **AZS-Azure**.
9. A **megosztott kulcs (PSK)** mezőbe írja be a **12345**értéket, majd kattintson **az OK gombra**.

10. Az **Összefoglalás** szakaszban kattintson az **OK gombra**.

### <a name="create-a-vm"></a>Virtuális gép létrehozása

A VPN-kapcsolat vizsgálatához hozzon létre két virtuális gépet: egyet az Azure-ban, és egy Azure Stack. Miután létrehozta ezeket a virtuális gépeket, használhatja őket a VPN-alagúton keresztüli adatküldésre és fogadásra.

1. A Azure Portal válassza az **+ erőforrás létrehozása**lehetőséget.
2. Lépjen a **piactérre**, majd válassza a **számítás**lehetőséget.
3. A virtuálisgép-rendszerképek listájában válassza ki a **Windows Server 2016 Datacenter eval** rendszerképét.
4. Az **alapok** szakaszban, a **név**mezőbe írja be a következőt: **AZS-VM**.
5. Írjon be egy érvényes felhasználónevet és jelszót. Ezzel a fiókkal jelentkezhet be a virtuális gépre a létrehozása után.
6. Adja meg az **előfizetést**, az **erőforráscsoportot**és a **helyet**, majd kattintson **az OK gombra**.
7. A **méret** szakaszban ehhez a példányhoz válassza ki a virtuális gép méretét, majd válassza a **kiválasztás**lehetőséget.
8. A **Beállítások** szakaszban fogadja el az alapértelmezett értékeket. Győződjön meg arról, hogy a **AZS-VNet** virtuális hálózat van kiválasztva. Ellenőrizze, hogy az alhálózat **10.1.0.0/24**értékre van-e állítva. Ezután kattintson az **OK** gombra.

9. Az **Összefoglalás** szakaszban tekintse át a beállításokat, majd kattintson az **OK gombra**.

## <a name="test-the-connection"></a>A kapcsolat tesztelése

A helyek közötti kapcsolat létrejötte után ellenőrizze, hogy mindkét irányban elérhető-e az adatforgalom. A kapcsolódás tesztelésének legegyszerűbb módja a ping teszt:

* Jelentkezzen be a Azure Stack-ben létrehozott virtuális gépre, és Pingelje meg a virtuális gépet az Azure-ban.
* Jelentkezzen be az Azure-ban létrehozott virtuális gépre, és Pingelje a virtuális gépet Azure Stackban.

>[!NOTE]
>Annak ellenőrzéséhez, hogy a helyek közötti kapcsolaton keresztül küld forgalmat, Pingelje a virtuális gép közvetlen IP-címét (DIP) a távoli alhálózaton, ne pedig a VIP-t.

### <a name="sign-in-to-the-user-vm-in-azure-stack"></a>Jelentkezzen be Azure Stack a felhasználói virtuális gépre

1. Jelentkezzen be a Azure Stack portálra.
2. A bal oldali navigációs sávon válassza a **Virtual Machines**lehetőséget.
3. A virtuális gépek listájában keresse meg a korábban létrehozott **AZS-VM** elemet, majd jelölje ki.
4. A virtuális gép szakaszban válassza a **kapcsolat**lehetőséget, majd nyissa meg a AZS-VM. rdp fájlt.

     ![Csatlakozási gomb](media/azure-stack-connect-vpn/image17.png)

5. Jelentkezzen be azzal a fiókkal, amelyet a virtuális gép létrehozásakor konfigurált.
6. Nyisson meg egy rendszergazda jogú Windows PowerShell-parancssort.
7. Írja be az **ipconfig /all** parancsot.
8. A kimenetben keresse meg az **IPv4-címeket**, majd mentse a címeket későbbi használatra. Ez az a címe, amelyet az Azure-ból Pingel. A példában a **10.1.0.4**a címe, de a környezetben eltérő lehet. Ennek a **10.1.0.0/24** alhálózaton belül kell lennie, amelyet korábban hozott létre.
9. A következő PowerShell-parancs futtatásával olyan tűzfalszabály hozható létre, amely lehetővé teszi a virtuális gép számára, hogy válaszoljon a pingelésre:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Jelentkezzen be a bérlői virtuális gépre az Azure-ban

1. Jelentkezzen be az Azure portálra.
2. A bal oldali navigációs sávon válassza a **Virtual Machines**lehetőséget.
3. A virtuális gépek listájából keresse meg a korábban létrehozott **Azure-VM-** t, majd jelölje ki.
4. A virtuális gép szakaszban válassza a **kapcsolat**lehetőséget.
5. Jelentkezzen be azzal a fiókkal, amelyet a virtuális gép létrehozásakor konfigurált.
6. Nyisson meg egy emelt szintű **Windows PowerShell** -ablakot.
7. Írja be az **ipconfig /all** parancsot.
8. Meg kell jelennie egy IPv4-címnek, amely a **10.100.0.0/24**tartományba esik. A példában szereplő környezet **10.100.0.4**, de a címe eltérő lehet.
9. A következő PowerShell-parancs futtatásával olyan tűzfalszabály hozható létre, amely lehetővé teszi a virtuális gép számára, hogy válaszoljon a pingelésre:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

10. Az Azure-beli virtuális gépről Pingelje Azure Stack a virtuális gépet az alagúton keresztül. Ehhez Pingelje a AZS-VM-ről rögzített DIP-t. A példában ez a **10.1.0.4**, de ügyeljen arra, hogy Pingelje a laborban feljegyzett címeket. A következő képernyőfelvételhez hasonló eredményt kell látnia:

    ![Sikeres pingelés](media/azure-stack-connect-vpn/image19b.png)

11. A távoli virtuális gép válasza sikeres tesztet jelez. A virtuális gép ablakát lezárhatja.

Szigorúbb adatátviteli tesztelésre is szükség van (például a különböző méretű fájlok másolására mindkét irányban).

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Adatátviteli statisztika megtekintése az átjárókapcsolaton keresztül

Ha tudni szeretné, hogy mennyi adat halad át a helyek közötti kapcsolaton keresztül, ez az információ a **kapcsolat** szakaszban érhető el. Ez a teszt azt is lehetővé teszi, hogy ellenőrizze, hogy az imént küldött pingelés valóban a VPN-kapcsolaton keresztül járt-e.

1. Amikor bejelentkezett a Azure Stack felhasználói virtuális gépre, a felhasználói fiókkal jelentkezzen be a felhasználói portálra.
2. Nyissa meg az **összes erőforrást**, majd válassza ki a **AZS – Azure-** kapcsolatokat. Megjelenik a **kapcsolatok** .
3. A **kapcsolatok** szakaszban megjelennek az **adatok** és a **kimenő** adatok statisztikái. A következő képernyőfelvételen a nagyméretű számok további fájlátvitelt kapnak. Itt nem nulla értékeket kell látnia.

    ![Be-és kimenő adatterületek](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>További lépések

* [Alkalmazások telepítése az Azure-ba és Azure Stack](azure-stack-solution-pipeline.md)
