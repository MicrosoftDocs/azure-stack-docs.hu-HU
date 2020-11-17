---
title: Több helyek közötti VPN-alagút beállítása Azure Stack hub-ban
description: Ismerje meg, hogyan állíthat be több helyek közötti VPN-alagutat Azure Stack hub-ban.
author: mattbriggs
ms.topic: how-to
ms.date: 11/13/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/13/2020
ms.openlocfilehash: ba0cf59417dfae96bd9c0ed137843eb69a08cc22
ms.sourcegitcommit: c89d8aa6d07d7aec002b58bd07a7976203aa760b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674636"
---
# <a name="how-to-set-up-multiple-site-to-site-vpn-tunnels-in-azure-stack-hub"></a>Több helyek közötti VPN-alagút beállítása Azure Stack hub-ban

Ez a cikk bemutatja, hogyan használható a megoldás a Azure Stack hub Resource Manager-sablonnal. A megoldás több, társított virtuális hálózattal rendelkező erőforráscsoportot hoz létre, és hogyan lehet ezeket a rendszereket összekapcsolni.

A sablonokat az [Azure intelligens Edge Pattern](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) GitHub-tárházában találja. A sablon az **RRAS-GRE-vnet-vnet** mappában található. 

## <a name="scenarios"></a>Forgatókönyvek

![Öt VPN-forgatókönyv diagrammed: két erőforráscsoport között egy adott előfizetésen belül. két csoport között a saját előfizetésében; két csoport között külön stack-példányokban; egy csoport és helyi erőforrás között a helyszínen; és több VPN-alagút.](./media/azure-stack-network-howto-vpn-tunnel/scenarios.svg)

## <a name="create-multiple-vpn-tunnels"></a>Több VPN-alagút létrehozása

![Az ábrán két erőforráscsoport látható, amelyek mindegyike a saját előfizetésében és a stack-példányában található, VPN-kapcsolattal. továbbá a két csoport egyike a helyszíni helyi erőforrásokhoz VPN-kapcsolaton keresztül.](./media/azure-stack-network-howto-vpn-tunnel/azure-stack-network-vpn-tunnel1.svg)

-  Helyezzen üzembe egy háromrétegű alkalmazást, web, app és DB adatbázist.

-  Telepítse az első két sablont külön Azure Stack hub-példányokon.

-  A **Webplatformot** a rendszer a PPE1-on telepíti, és a **AppTier** a PPE2-on lesz telepítve.

-  Kapcsolja össze a **Webplatformot** és a **AppTier** egy IKE-alagúttal.

-  Kapcsolódjon a **AppTier** egy helyszíni rendszerhez, amelyet meg fog hívni a **DBTier**.

## <a name="steps-to-deploy-multiple-vpns"></a>Több virtuális magánhálózatok üzembe helyezésének lépései

Ez egy több lépésből álló folyamat. Ebben a megoldásban az Azure Stack hub portált fogja használni. Azonban használhatja a PowerShellt, az Azure CLI-t vagy más infrastruktúra-kóddal rendelkező eszközöket a kimenetek rögzítéséhez és bemenetként való felhasználásához.

![A diagram öt lépést mutat be a VPN-alagutak két infrastruktúra közötti üzembe helyezéséhez. Az első két lépés két infrastruktúrát hoz létre egy sablonból. A következő két lépés két VPN-alagutat hoz létre egy sablonból, az utolsó lépés pedig összekapcsolja az alagutakat.](./media/azure-stack-network-howto-vpn-tunnel/azure-stack-network-vpn-tunnel2.svg)

## <a name="walkthrough"></a>Útmutatás

### <a name="deploy-web-tier-to-azure-stack-hub-instances-ppe1"></a>Webes rétegek üzembe helyezése Azure Stack hub-példányok PPE1

1. Nyissa meg az Azure Stack hub felhasználói portált, és válassza az **erőforrás létrehozása** lehetőséget.

2. Válassza a **sablon központi telepítése** lehetőséget.

    ![Az "irányítópult > új" párbeszédpanel különböző lehetőségeket mutat be. A "Template deployment" gomb ki van emelve.](./media/azure-stack-network-howto-vpn-tunnel/image3.png)

3. Másolja és illessze be a azuredeploy.jstartalmát az **Azure-intelligens-Edge-Patterns/RRAS-vnet-vpntunnel** adattárból a sablon ablakba. Ekkor megjelenik a sablonban található erőforrások, majd válassza a **Mentés** lehetőséget.

    ![Az "irányítópult > új > üzembe helyezési megoldás sablonja > szerkesztési sablon" párbeszédpanelen található egy ablak, amelyben a azuredeploy.jsfájl beillesztett.](./media/azure-stack-network-howto-vpn-tunnel/image4.png)

4. Adja meg az **erőforráscsoport** nevét, és keresse meg a paramétereket.

    > [!Note]  
    > A webrétegű címtartomány a **10.10.0.0/16** lesz, és az erőforráscsoport helye a következő lehet: **PPE1**

    ![Az "irányítópult > új > a megoldás sablonjának üzembe helyezése > paraméterek" párbeszédpanelen egy "erőforráscsoport" szövegmező és egy választógomb látható. Az "új létrehozása" gomb ki van választva, és a szöveg "webrétegű".](./media/azure-stack-network-howto-vpn-tunnel/image5.png)

### <a name="deploy-app-tier-to-the-second-azure-stack-hub-instances"></a>Az alkalmazás szintjeinek üzembe helyezése a második Azure Stack hub-példányon

Ugyanazt a folyamatot használhatja, mint a **Webplatformot** , de az itt látható különböző paramétereket:

> [!NOTE]  
> A AppTier **-címtartomány 10.20.0.0/16** lesz, és az erőforráscsoport helye a következő lehet: **WestUS2**.

![Az "irányítópult > a megoldás sablonjának központi telepítése > paraméterek" párbeszédpanelen egy "erőforráscsoport" szövegmező és egy választógomb látható. Az "új létrehozása" gomb ki van választva, és a szöveg "AppTier". Nyolc további Kiemelt szövegmező tartalmazza a sablon paramétereit.](./media/azure-stack-network-howto-vpn-tunnel/image6.png)

### <a name="review-the-deployments-for-web-tier-and-app-tier-and-capture-outputs"></a>Tekintse át a webes és az alkalmazási rétegek központi telepítéseit és a rögzítési kimeneteket

1. Ellenőrizze, hogy az üzemelő példány sikeresen befejeződött-e. Válassza a **Kimenetek** lehetőséget.

    ![A kimenetek beállítás ki van emelve az "irányítópult > Microsoft. template – áttekintés" párbeszédpanelen. Az üzenet a következő: "az üzembe helyezés befejeződött", amelyet a Group webplatformhoz tartozó erőforrások listája, a név, a típus, az állapot és a részletekre mutató hivatkozás követ.](./media/azure-stack-network-howto-vpn-tunnel/image7.png)

1. Másolja az első négy értéket a Jegyzettömb alkalmazásba.

    ![A párbeszédpanel "irányítópult > Microsoft. template-outputs". A webes rétegek központi telepítéséről másolandó négy szövegmező kiemelve: LOCALTUNNELENDPOINT, LOCALVNETADDRESSSPACE, LOCALVNETGATEWAY és LOCALTUNNELGATEWAY.](./media/azure-stack-network-howto-vpn-tunnel/image8.png)

1. Ismételje meg az **AppTier** üzemelő példányát.

    ![A kimenetek beállítás ki van emelve az "irányítópult > Microsoft. template – áttekintés" párbeszédpanelen. Az üzenet "az üzembe helyezés befejeződött", amelyet a csoport AppTier tartozó erőforrások listája követ, amelyek mindegyike neve, típusa, állapota és a részletekre mutató hivatkozás.](./media/azure-stack-network-howto-vpn-tunnel/image9.png)

    ![A párbeszédpanel "irányítópult > Microsoft. template-outputs". Az alkalmazási szintű telepítésből másolandó négy szövegmező kiemelve van: LOCALTUNNELENDPOINT, LOCALVNETADDRESSSPACE, LOCALVNETGATEWAY és LOCALTUNNELGATEWAY.](./media/azure-stack-network-howto-vpn-tunnel/image10.png)

### <a name="create-tunnel-from-web-tier-to-app-tier"></a>Alagút létrehozása webes rétegekből az alkalmazás szintjéig

1. Nyissa meg az Azure Stack hub felhasználói portált, és válassza az **erőforrás létrehozása** lehetőséget.

2. Válassza a **sablon központi telepítése** lehetőséget.

3. Illessze be **azuredeploy.tunnel.ike.js** a tartalmát a következőre:.

4. Válassza a **Paraméterek szerkesztése** lehetőséget.

![Az "irányítópult > új > a megoldás sablonjának üzembe helyezése > paraméterek" párbeszédpanelen egy "erőforráscsoport" szövegmező és egy választógomb látható. A "meglévő használata" gomb van kiválasztva, a szöveg pedig "webrétegű". Négy másik kiemelt szövegmező tartalmazza a sablon paramétereit.](./media/azure-stack-network-howto-vpn-tunnel/image11.png)

### <a name="create-tunnel-from-app-tier-to-web-tier"></a>Alagút létrehozása az alkalmazás szintjéről a webes szintjére

1. Nyissa meg az Azure Stack hub felhasználói portált, és válassza az **erőforrás létrehozása** lehetőséget.

2. Válassza a **template Deployment** lehetőséget.

3. Illessze be **azuredeploy.tunnel.ike.js** a tartalmát a következőre:.

4. Válassza a **Paraméterek szerkesztése** lehetőséget.

![Az "irányítópult > új > a megoldás sablonjának üzembe helyezése > paraméterek" párbeszédpanelen egy "erőforráscsoport" szövegmező és egy választógomb látható. A "meglévő használata" gomb van kiválasztva, a szöveg pedig "AppTier". Négy másik kiemelt szövegmező tartalmazza a sablon paramétereit.](./media/azure-stack-network-howto-vpn-tunnel/image12.png)

### <a name="viewing-tunnel-deployment"></a>Alagút központi telepítésének megtekintése

Ha megtekinti az egyéni parancsfájl-bővítmény kimenetét, megtekintheti a létrehozott alagutat, és az állapotot is meg kell jelenítenie. Látni fogja, hogy az egyik, a másik oldalhoz való **csatlakozásra** vár, és a másik oldalon az üzembe **helyezés után fog** megjelenni.

![A párbeszédpanel "irányítópult > erőforráscsoportok > webréteg-> webréteg-RRAS-bővítmények". Két bővítmény szerepel a következőkben: CustomScriptExtension, amely ki van emelve, és InstallRRAS. A kiépítés állapota is sikeres volt.](./media/azure-stack-network-howto-vpn-tunnel/image13.png)

![A párbeszédpanel a következő: irányítópult > erőforráscsoportok > webréteg > webréteg-RRAS-Extensions > CustomScriptExtension ". A részletes állapot értéke "kiépítés sikeres".](./media/azure-stack-network-howto-vpn-tunnel/image14.png)

![A AppTier és a webréteghez készült egyéni szkriptek bővítményeinek kimenete egymás melletti Jegyzettömb-ablakokban látható. A AppTier a csatlakozás bújtatási állapotát jeleníti meg. A webrétegű csatlakozik.](./media/azure-stack-network-howto-vpn-tunnel/image15.png)

### <a name="troubleshooting-on-the-rras-vm"></a>Hibaelhárítás az RRAS virtuális gépen

1. Módosítsa a távoli asztal (RDP) szabályát a **Megtagadás** értékről **engedélyezésre**.

1. Kapcsolódjon a rendszerhez egy RDP-ügyféllel az üzembe helyezés során beállított hitelesítő adatok használatával.

1. Nyissa meg a PowerShellt egy emelt szintű parancssorral, és futtassa a parancsot `get-VpnS2SInterface` .

    ![A PowerShell-ablak a Get-VpnS2SInterface parancs végrehajtását jeleníti meg, amely a helyek közötti kapcsolat részleteit jeleníti meg. A ConnectionState csatlakoztatva van.](./media/azure-stack-network-howto-vpn-tunnel/image16.png)

1. A **remoteaccess** -parancsmagok használatával kezelheti a rendszereket.

    ![Az alkalmazási szinten fut egy cmd.exe ablak, amely az ipconfig parancs kimenetét jeleníti meg. Két ablak jelenik meg átfedésként: egy RDP-kapcsolati ablak a webes réteghez, valamint egy Windows biztonsági ablak, amely a webes réteghez csatlakozik.](./media/azure-stack-network-howto-vpn-tunnel/image17.png)

### <a name="install-rras-on-an-on-premises-vm-db-tier"></a>Az RRAS telepítése helyszíni virtuálisgép-adatbázis-rétegre

1. A cél egy Windows 2016-rendszerkép.

1. Ha átmásolja a `Add-Site2SiteIKE.ps1` szkriptet a tárházból, és helyileg futtatja, a parancsfájl telepíti a **WindowsFeature** és a **remoteaccess**.

    > [!NOTE]
    > A környezettől függően előfordulhat, hogy újra kell indítania a rendszerét.

    A hivatkozásokat a helyszíni számítógép hálózati konfigurációjában találja.

    ![Egy cmd.exe ablak jelenik meg, amely az ipconfig kimenetet, valamint egy hálózati és megosztási központ ablakot jelenít meg.](./media/azure-stack-network-howto-vpn-tunnel/image18.png)

1. Futtassa a szkriptet a AppTier-sablon központi telepítésében rögzített **kimeneti** paraméterek hozzáadásával.

    ![A Add-Site2SiteIKE. PSA parancsfájl egy PowerShell-ablakban van végrehajtva, és a kimenet megjelenik.](./media/azure-stack-network-howto-vpn-tunnel/image19.png)

1. Az alagút konfigurálva van, és várakozik a AppTier-csatlakozásra.

### <a name="configure-app-tier-to-db-tier"></a>Az alkalmazási rétegek konfigurálása az adatbázis-szinten

1. Nyissa meg az Azure Stack hub felhasználói portált, és válassza az **erőforrás létrehozása** lehetőséget.

2. Válassza a **template Deployment** lehetőséget.

3. Illessze be **azuredeploy.tunnel.ike.js** a tartalmát a következőre:.

4. Válassza a **Paraméterek szerkesztése** lehetőséget.

    ![Az "irányítópult > új > a megoldás sablonjának üzembe helyezése > paraméterek" párbeszédpanelen egy "erőforráscsoport" szövegmező és egy választógomb látható. A "meglévő használata" gomb van kiválasztva, a szöveg pedig "AppTier". Három másik kiemelt szövegmező tartalmazza a sablon paramétereit.](./media/azure-stack-network-howto-vpn-tunnel/image20.png)

5. Győződjön meg arról, hogy a AppTier van kiválasztva, és a távoli belső hálózat be van állítva a következő helyen: 10.99.0.1.

### <a name="confirm-tunnel-between-app-tier-and-db-tier"></a>Alagút megerősítése az alkalmazás szintjei és az adatbázis szintje között

1. Ha a virtuális gépre való bejelentkezés nélkül szeretné ellenőriznie az alagutat, futtasson egy egyéni parancsfájl-kiterjesztést.

2. Nyissa meg az RRAS virtuális gépet (a AppTier).

3. Válassza a **bővítmények** és az egyéni R-**szkriptek bővítménye** elemet.

4. Keresse meg a parancsfájlok könyvtárat az **Azure-intelligens-Edge-Patterns/RRAS-vnet-vpntunnel** adattárban. Válassza a **Get-VPNS2SInterfaceStatus.ps1** lehetőséget.

    ![A párbeszédpanel "irányítópult > erőforráscsoportok > AppTier > AppTier-RRAS-Extensions > CustomScriptExtension". A részletes állapot értéke "kiépítés sikerült". A részletek egy jegyzettömb-ablakban vannak átfedésben.](./media/azure-stack-network-howto-vpn-tunnel/image21.png)

5. Ha engedélyezi az RDP-t, és bejelentkezik, nyissa meg a PowerShellt, és futtassa `get-vpns2sinterface` a parancsot, és láthatja, hogy az alagút csatlakoztatva van.

    **DBTier**

    ![A DBTier-ben a PowerShell-ablak a Get-VpnS2SInterface parancs végrehajtását jeleníti meg, amely a helyek közötti kapcsolat részleteit jeleníti meg. A ConnectionState csatlakoztatva van.](./media/azure-stack-network-howto-vpn-tunnel/image22.png)

    **AppTier**

    ![A AppTier-ben a PowerShell-ablak a Get-VpnS2SInterface parancs végrehajtását jeleníti meg, amely a helyek közötti kapcsolat részleteit jeleníti meg. A ConnectionState két célhelyhez van csatlakoztatva.](./media/azure-stack-network-howto-vpn-tunnel/image23.png)

    > [!NOTE]  
    > Az RDP-t az egyik gépről a másodikra is tesztelheti, a másodiktól az elsőig.

    > [!NOTE]  
    > A megoldás helyszíni megvalósításához a Azure Stack hub távoli hálózatára útvonalakat kell telepítenie az infrastruktúra vagy a minimálisan meghatározott virtuális gépek esetében.

### <a name="deploying-a-gre-tunnel"></a>GRE-alagút üzembe helyezése

Ebben a sablonban ez a forgatókönyv az [IKE-sablont](network-howto-vpn-tunnel-ipsec.md)használta. Azonban a [gre-alagutat](network-howto-vpn-tunnel-gre.md)is üzembe helyezheti. Ez az alagút nagyobb átviteli sebességet biztosít.

A folyamat majdnem azonos. Ha azonban az alagút sablonját a meglévő infrastruktúrára telepíti, akkor az első három bemenethez a másik rendszer kimeneteit kell használnia. Tudnia kell, hogy milyen **LOCALTUNNELGATEWAY** kell telepíteni, és nem azt az erőforráscsoportot, amelyhez csatlakozni próbál.

![Az "irányítópult > új > a megoldás sablonjának üzembe helyezése > paraméterek" párbeszédpanelen egy "erőforráscsoport" szövegmező és egy választógomb látható. A "meglévő használata" gomb van kiválasztva, a szöveg pedig "AppTier". Négy másik kiemelt szövegmező a webrétegű kimenetből származó adatokat tartalmaz.](./media/azure-stack-network-howto-vpn-tunnel/image24.png)

## <a name="next-steps"></a>Következő lépések

[A Azure Stack hub hálózatkezelésével kapcsolatos különbségek és megfontolások](azure-stack-network-differences.md)  
[VPN-alagút létrehozása a GRE használatával](network-howto-vpn-tunnel-gre.md)  
[VPN-alagút létrehozása az IPSEC használatával](network-howto-vpn-tunnel-ipsec.md)