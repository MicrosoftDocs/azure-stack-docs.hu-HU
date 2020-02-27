---
title: Több helyek közötti VPN-alagút beállítása Azure Stack hub-ban
description: Ismerje meg, hogyan állíthat be több helyek közötti VPN-alagutat Azure Stack hub-ban.
author: mattbriggs
ms.topic: how-to
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: c1b48e551a1c94456174cbe5725da72d76f78219
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77636335"
---
# <a name="how-to-set-up-a-multiple-site-to-site-vpn-tunnel-in-azure-stack-hub"></a>Több helyek közötti VPN-alagút beállítása Azure Stack hub-ban

Ez a cikk bemutatja, hogyan használható a megoldás a Azure Stack hub Resource Manager-sablonnal. A megoldás több, társított virtuális hálózattal rendelkező erőforráscsoportot hoz létre, és hogyan lehet ezeket a rendszereket összekapcsolni.

A sablonokat az [Azure intelligens Edge Pattern](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) GitHub-tárházában találja. A sablon az **RRAS-GRE-vnet-vnet** mappában található. 

## <a name="scenarios"></a>Forgatókönyvek

![](./media/azure-stack-network-howto-vpn-tunnel/scenarios.png)

## <a name="create-multiple-vpn-tunnels"></a>Több VPN-alagút létrehozása

![](./media/azure-stack-network-howto-vpn-tunnel/image1.png)

-  Helyezzen üzembe egy háromrétegű alkalmazást, web, app és DB adatbázist.

-  Telepítse az első két sablont külön Azure Stack hub-példányokon.

-  A **Webplatformot** a rendszer a PPE1-on telepíti, és a **AppTier** a PPE2-on lesz telepítve.

-  Kapcsolja össze a **Webplatformot** és a **AppTier** egy IKE-alagúttal.

-  Kapcsolódjon a **AppTier** egy helyszíni rendszerhez, amelyet meg fog hívni a **DBTier**.

## <a name="steps-to-deploy-multiple-vpns"></a>Több virtuális magánhálózatok üzembe helyezésének lépései

Ez egy több lépésből álló folyamat. Ebben a megoldásban az Azure Stack hub portált fogja használni. Azonban használhatja a PowerShellt, az Azure CLI-t vagy más infrastruktúra-kóddal rendelkező eszközöket a kimenetek rögzítéséhez és bemenetként való felhasználásához.

![helyettesítő szöveg](./media/azure-stack-network-howto-vpn-tunnel/image2.png)

## <a name="walkthrough"></a>Útmutatás

### <a name="deploy-web-tier-to-azure-stack-hub-instances-ppe1"></a>Webes rétegek üzembe helyezése Azure Stack hub-példányok PPE1

1.  Nyissa meg az Azure Stack hub felhasználói portált, és válassza az **erőforrás létrehozása**lehetőséget.

2.  Válassza a **sablon központi telepítése**lehetőséget.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image3.png)

3.  Másolja és illessze be a azuredeploy. JSON fájl tartalmát az **Azure-intelligens-Edge-Patterns/RRAS-vnet-vpntunnel** adattárból a sablon ablakba. Ekkor megjelenik a sablonban található erőforrások, majd válassza a **Mentés**lehetőséget.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image4.png)

4.  Adja meg az **erőforráscsoport** nevét, és keresse meg a paramétereket.

    > ! Megjegyzés  
    > A webrétegű címtartomány a **10.10.0.0/16** lesz, és az erőforráscsoport helye a következő lehet: **PPE1**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image5.png)

### <a name="deploy-app-tier-to-the-second-azure-stack-hub-instances"></a>Az alkalmazás szintjeinek üzembe helyezése a második Azure Stack hub-példányon

Ugyanazt a folyamatot használhatja, mint a **Webplatformot** , de az itt látható különböző paramétereket:

> [!Note]  
> A AppTier **-címtartomány 10.20.0.0/16** lesz, és az erőforráscsoport helye a következő lehet: **WestUS2**.

![](./media/azure-stack-network-howto-vpn-tunnel/image6.png)

### <a name="review-the-deployments-for-web-tier-and-app-tier-and-capture-outputs"></a>Tekintse át a webes és az alkalmazási rétegek központi telepítéseit és a rögzítési kimeneteket

1.  Ellenőrizze, hogy az üzemelő példány sikeresen befejeződött-e. Válassza a **kimenetek**lehetőséget.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image7.png)

3.  Másolja az első négy értéket a Jegyzettömb alkalmazásba.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image8.png)

5.  Ismételje meg az **AppTier** üzemelő példányát.

![](./media/azure-stack-network-howto-vpn-tunnel/image9.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image10.png)

### <a name="create-tunnel-from-web-tier-to-app-tier"></a>Alagút létrehozása webes rétegekből az alkalmazás szintjéig

1.  Nyissa meg az Azure Stack hub felhasználói portált, és válassza az **erőforrás létrehozása**lehetőséget.

2.  Válassza a **sablon központi telepítése**lehetőséget.

3.  Illessze be a tartalmat a **azuredeploy. Tunnel. IKE. JSON**fájlból.

4.  Válassza a **Paraméterek szerkesztése**lehetőséget.

![](./media/azure-stack-network-howto-vpn-tunnel/image11.png)

### <a name="create-tunnel-from-app-tier-to-web-tier"></a>Alagút létrehozása az alkalmazás szintjéről a webes szintjére

1.  Nyissa meg az Azure Stack hub felhasználói portált, és válassza az **erőforrás létrehozása**lehetőséget.

2.  Válassza a **Template deployment** lehetőséget.

3.  Illessze be a tartalmat a **azuredeploy. Tunnel. IKE. JSON**fájlból.

4.  Válassza a **Paraméterek szerkesztése**lehetőséget.

![](./media/azure-stack-network-howto-vpn-tunnel/image12.png)

### <a name="viewing-tunnel-deployment"></a>Alagút központi telepítésének megtekintése

Ha megtekinti az egyéni parancsfájl-bővítmény kimenetét, megtekintheti a létrehozott alagutat, és az állapotot is meg kell jelenítenie. Látni fogja, hogy az egyik, a másik oldalhoz való **csatlakozásra** vár, és a másik oldalon az üzembe **helyezés után fog** megjelenni.

![](./media/azure-stack-network-howto-vpn-tunnel/image13.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image14.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image15.png)

### <a name="troubleshooting-on-the-rras-vm"></a>Hibaelhárítás az RRAS virtuális gépen

1.  Az RDP-szabály módosításának **engedélyezéséhez**módosítsa a **tiltást** .

2.  Kapcsolódjon a rendszerhez egy távoli asztali (DRP) ügyféllel az üzembe helyezés során beállított hitelesítő adatokkal.

3.  Nyissa meg a PowerShellt egy emelt szintű parancssorral, és futtassa `get-VPNS2SInterface`.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image16.png)

5.  A **remoteaccess** -parancsmagok használatával kezelheti a rendszereket.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image17.png)

### <a name="install-rras-on-a-on-premises-vm-db-tier"></a>Az RRAS telepítése helyszíni virtuálisgép-ADATBÁZISra

1.  A cél egy Windows 2016-rendszerkép.

2.  Ha a `Add-Site2SiteIKE.ps1` parancsfájlt a tárházból másolja, és helyileg futtatja, a parancsfájl telepíti a **WindowsFeature** és a **remoteaccess**.

    > [!Note]
    > A környezettől függően előfordulhat, hogy újra kell indítania a rendszerét.

    A hivatkozásokat a helyszíni számítógép hálózati konfigurációjában találja.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image18.png)

3.  Futtassa a szkriptet a AppTier-sablon központi telepítésében rögzített **kimeneti** paraméterek hozzáadásával.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image19.png)

5.  Az alagút konfigurálva van, és várakozik a AppTier-csatlakozásra.

### <a name="configure-app-tier-to-db-tier"></a>Az alkalmazási rétegek konfigurálása az adatbázis-szinten

1.  Nyissa meg az Azure Stack hub felhasználói portált, és válassza az **erőforrás létrehozása**lehetőséget.

2.  Válassza a **Template deployment** lehetőséget.

3.  Illessze be a tartalmat a **azuredeploy. Tunnel. IKE. JSON**fájlból.

4.  Válassza a **Paraméterek szerkesztése**lehetőséget.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image20.png)

5.  Győződjön meg arról, hogy a AppTier van kiválasztva, és a távoli belső hálózat be van állítva a következő helyen: 10.99.0.1.

### <a name="confirm-tunnel-between-app-tier-and-db-tier"></a>Alagút megerősítése az alkalmazás szintjei és az adatbázis szintje között

1.  Ha a virtuális gépre való bejelentkezés nélkül szeretné ellenőriznie az alagutat, futtasson egy egyéni parancsfájl-kiterjesztést.

2.  Nyissa meg az RRAS virtuális gépet (a AppTier).

3.  Válassza a **bővítmények** és az egyéni R-**szkriptek bővítménye**elemet.

4.  Keresse meg a parancsfájlok könyvtárat az **Azure-intelligens-Edge-Patterns/RRAS-vnet-vpntunnel** adattárban. Válassza a **Get-VPNS2SInterfaceStatus. ps1**elemet.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image21.png)

5.  Ha engedélyezi az RDP-t és a bejelentkezést, nyissa meg a PowerShellt, és futtassa a `get-vpns2sinterface`t, és láthatja, hogy az alagút csatlakoztatva van.

    **DBTier**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image22.png)

    **AppTier**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image23.png)

    > [!Note]  
    > Az RDP-t az egyik gépről a másodikra is tesztelheti, a másodiktól az elsőig.

    > [!Note]  
    > A megoldás helyszíni megvalósításához a Azure Stack hub távoli hálózatára útvonalakat kell telepítenie az infrastruktúra vagy a minimálisan meghatározott virtuális gépek esetében.

### <a name="deploying-a-gre-tunnel"></a>GRE-alagút üzembe helyezése

Ebben a sablonban ez a forgatókönyv az [IKE-sablont](network-howto-vpn-tunnel-ipsec.md)használta. Azonban a [gre-alagutat](network-howto-vpn-tunnel-gre.md)is üzembe helyezheti. Ez az alagút nagyobb átviteli sebességet biztosít.

A folyamat majdnem azonos. Ha azonban az alagút sablonját a meglévő infrastruktúrára telepíti, akkor az első három bemenethez a másik rendszer kimeneteit kell használnia. Tudnia kell, hogy milyen **LOCALTUNNELGATEWAY** kell telepíteni, és nem azt az erőforráscsoportot, amelyhez csatlakozni próbál.

![](./media/azure-stack-network-howto-vpn-tunnel/image24.png)

## <a name="next-steps"></a>Következő lépések

[A Azure Stack hub hálózatkezelésével kapcsolatos különbségek és megfontolások](azure-stack-network-differences.md)  
[VPN-alagút létrehozása a GRE használatával](network-howto-vpn-tunnel-gre.md)  
[VPN-alagút létrehozása az IPSEC használatával](network-howto-vpn-tunnel-ipsec.md)