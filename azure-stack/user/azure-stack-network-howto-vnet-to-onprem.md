---
title: VPN-átjáró beállítása Azure Stack hubhoz
description: Megtudhatja, hogyan állíthatja be a VPN-átjárót Azure Stack hubhoz.
author: mattbriggs
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: be0457eaa1c8c72984e995a690e5d5c6c53f198f
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81660839"
---
# <a name="set-up-vpn-gateway-for-azure-stack-hub-using-fortigate-nva"></a>VPN Gateway beállítása Azure Stack hubhoz a FortiGate NVA használatával

Ez a cikk azt ismerteti, hogyan hozható létre VPN-kapcsolat az Azure Stack hub-hoz. A VPN Gateway olyan virtuális hálózati átjáró, amely titkosított forgalmat küld Azure Stack hub és egy távoli VPN-átjáró között a virtuális hálózat között. Az alábbi eljárás az egyik VNET üzembe helyezi egy FortiGate-NVA, egy hálózati virtuális berendezéssel, egy erőforráscsoporton belül. Emellett útmutatást nyújt az IPSec VPN beállításához a FortiGate-NVA.

## <a name="prerequisites"></a>Előfeltételek

-  Hozzáférés a megoldáshoz szükséges számítási, hálózati és erőforrás-követelmények üzembe helyezéséhez rendelkezésre álló kapacitással rendelkező Azure Stack hub integrált rendszerekhez. 

    > [!Note]  
    > Ezek az utasítások a ASDK hálózati korlátozásai miatt **nem** fognak működni Azure stack Development Kit (ASDK). További információ: [ASDK-követelmények és szempontok](https://docs.microsoft.com/azure-stack/asdk/asdk-deploy-considerations).

-  Hozzáférés az Azure Stack hub integrált rendszerét futtató helyszíni hálózatban található VPN-eszközhöz. Az eszköznek létre kell hoznia egy IPSec-alagutat, amely megfelel a [központi telepítési paraméterekben](#deployment-parameters)leírt paramétereknek.

-  A Azure Stack hub piactéren elérhető hálózati virtuális berendezés (NVA) megoldás. Az NVA vezérli a peremhálózat hálózati forgalmának áramlását más hálózatokra vagy alhálózatokra. Ez az eljárás a [Fortinet FortiGate következő generációs tűzfalának egyetlen virtuálisgép-megoldását](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm)használja.

    > [!Note]  
    > Ha nem rendelkezik a **Fortinet FortiGate-VM for Azure BYOL** és **FortiGate NGFW – egyetlen virtuálisgép-telepítés (BYOL)** a Azure stack hub piactéren, forduljon a felhő üzemeltetőjéhez.

-  A FortiGate-NVA aktiválásához szüksége lesz legalább egy elérhető FortiGate-fájlra. A licencek beszerzésével kapcsolatos információkat lásd: a Fortinet-dokumentum [regisztrálása és a licenc letöltése](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

    Ez az eljárás az [egyetlen FortiGate-VM-telepítést](ttps://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment)használja. A FortiGate-NVA a helyszíni hálózatban lévő, a Azure Stack hub VNET való összekapcsolásával kapcsolatban talál további lépéseket.

    A FortiGate-megoldás aktív-passzív (HA) beállítással történő üzembe helyezésével kapcsolatos további információkért tekintse meg az [Azure-beli FortiGate-VM](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure)Fortinet dokumentációjában található részleteket.

## <a name="deployment-parameters"></a>Központi telepítési paraméterek

A következő táblázat összefoglalja a központi telepítésekben használt paramétereket.

| Paraméter | Érték |
|-----------------------------------|---------------------------|
| FortiGate-példány neve | forti1 |
| BYOL-licenc/-verzió | 6.0.3 |
| FortiGate rendszergazdai Felhasználónév | fortiadmin |
| Erőforráscsoport neve | forti1-rg1 |
| Virtuális hálózat neve | forti1vnet1 |
| VNET címterület | 172.16.0.0/16 * |
| Nyilvános VNET alhálózatának neve | forti1-PublicFacingSubnet |
| Nyilvános VNET-címek előtagja | 172.16.0.0/24 * |
| VNET alhálózatának neve | forti1-InsideSubnet |
| VNET alhálózati előtagon belül | 172.16.1.0/24 * |
| FortiGate NVA virtuális gép mérete | Standard F2s_v2 |
| Nyilvános IP-cím | forti1-publicip1 |
| Nyilvános IP-cím típusa | Statikus |

> [!Note]
> \*Ha `172.16.0.0/16` átfedésben van a helyszíni hálózattal vagy az Azure stack hub VIP-készlettel, válasszon másik címtartományt és alhálózati előtagokat.

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>A FortiGate NGFW Marketplace-elemek üzembe helyezése

1. Nyissa meg az Azure Stack hub felhasználói portált.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image5.png)

1. Válassza az **erőforrás létrehozása** elemet, és `FortiGate`keresse meg a következőt:.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image6.png)

2. Válassza ki a **FORTIGATE NGFW** , és válassza a **Létrehozás**lehetőséget.

3. A [központi telepítési paraméterek](#deployment-parameters) táblázat paramétereinek használatával végezheti el az **alapvető tudnivalókat** .

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image7.png)

1. Kattintson az **OK** gombra.

2. Adja meg a virtuális hálózatot, az alhálózatokat és a virtuális gép méretét a [telepítési paraméterek](#deployment-parameters) táblázat használatával.

    > [!Warning] 
    > Ha a helyszíni hálózat átfedésben van az IP-tartománnyal `172.16.0.0/16`, ki kell választania és be kell állítania egy másik hálózati tartományt és alhálózatot. Ha más neveket és tartományokat kíván használni a [telepítési paraméterek](#deployment-parameters) táblázatban, akkor olyan paramétereket használjon, amelyek **nem** ütköznek a helyszíni hálózattal. Ügyeljen arra, hogy a VNET IP-címtartomány és alhálózati tartományok beállítása a VNET belül történjen. Nem szeretné, hogy a tartomány átfedésben legyen a helyszíni hálózatban található IP-tartományokkal.

3. Kattintson az **OK** gombra.

4. Konfigurálja a FortiGate NVA tartozó nyilvános IP-címet:

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image8.png)

5. Kattintson az **OK** gombra. Majd válassza az **OK** elemet.

6. Kattintson a **Létrehozás** gombra.

    Az üzembe helyezés körülbelül 10 percet vesz igénybe.

## <a name="configure-routes-udr-for-the-vnet"></a>Útvonalak konfigurálása (UDR) a VNET

1. Nyissa meg az Azure Stack hub felhasználói portált.

2. Válassza az Erőforráscsoportok lehetőséget. Írja `forti1-rg1` be a szűrőt, majd kattintson duplán a forti1-rg1 erőforráscsoport elemre.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image9.png)

2. Válassza ki az "forti1-forti1-InsideSubnet-Routes-xxxx" erőforrást.

3. Válassza az **útvonalak** elemet a **Beállítások**területen.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image10.png)

4. Törölje a **to-Internet** útvonalat.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image11.png)

5. Válassza az *Igen* lehetőséget.

6. Új útvonal hozzáadásához válassza a **Hozzáadás** lehetőséget.

7. Nevezze el az `to-onprem`útvonalat.

8. Adja meg az IP-hálózati tartományt, amely meghatározza annak a helyszíni hálózatnak a hálózati tartományát, amelyhez a VPN csatlakozni fog.

9. Válassza a **virtuális berendezés** lehetőséget a **következő ugrási típushoz** és `172.16.1.4`. Ha más IP-címtartományt használ, használja az IP-címtartományt.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image12.png)

10. Kattintson a **Mentés** gombra.

## <a name="activate-the-fortigate-nva"></a>A FortiGate-NVA aktiválása

Aktiválja a FortiGate NVA, és állítson be egy IPSec VPN-kapcsolatot az egyes NVA.

Az egyes FortiGate-NVA aktiválásához érvényes licencfájl szükséges a Fortinet-től. A NVA addig **nem** fog működni, amíg be nem aktiválja az egyes NVA. A licencfájl beszerzésével és a NVA aktiválásához szükséges lépésekkel kapcsolatos további információkért tekintse meg a Fortinet-dokumentum [regisztrálásával és a licenc letöltésével](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)foglalkozó cikket.

Miután aktiválta a NVA, hozzon létre egy IPSec VPN-alagutat a NVA.

1. Nyissa meg az Azure Stack hub felhasználói portált.

2. Válassza az Erőforráscsoportok lehetőséget. Írja `forti1` be a szűrőt, és kattintson duplán a forti1-erőforráscsoport elemre.

3. Kattintson duplán a **forti1** virtuális gépre az erőforráscsoport paneljén található erőforrástípusok listájában.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image13.png)

4. Másolja a hozzárendelt IP-címet, nyisson meg egy böngészőt, és illessze be az IP-címet a címsorba. Előfordulhat, hogy a hely figyelmeztetést vált ki, hogy a biztonsági tanúsítvány nem megbízható. Folytatás mindenképpen.

5. Adja meg az üzembe helyezés során megadott FortiGate rendszergazdai felhasználónevet és jelszót.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image14.png)

6.  > Válassza **a****rendszerbelső vezérlőprogram**lehetőséget.

7. Jelölje be a legújabb belső vezérlőprogram, például a következőt `FortiOS v6.2.0 build0866`:.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image15.png)

8. Válassza **a biztonsági mentési konfiguráció és a frissítés** > **folytatása**lehetőséget.

9. A NVA frissíti a belső vezérlőprogramot a legújabb buildekre és újraindításokra. A folyamat körülbelül öt percet vesz igénybe. Jelentkezzen be újra a FortiGate webkonzolra.

10. Kattintson a **VPN** > **IPSec varázsló**elemre.

11. Adja meg a VPN nevét, például `conn1` a VPN- **létrehozási varázslóban**.

12. Válassza **ezt a helyet a NAT mögött**.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image16.png)

13. Kattintson a **Tovább** gombra.

14. Adja meg annak a helyszíni VPN-eszköznek a távoli IP-címét, amelyhez csatlakozni fog.

15. A **kimenő illesztőfelületként**válassza a **port1** lehetőséget.

16. Válassza az **előmegosztott kulcs** lehetőséget, és adja meg (és jegyezze fel) az előmegosztott kulcsot. 

    > [!Note]  
    > Erre a kulcsra szüksége lesz a kapcsolat beállításához a helyszíni VPN-eszközön, azaz *pontosan*meg kell egyeznie.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image17.png)

17. Kattintson a **Tovább** gombra.

18. Válassza a **port2** lehetőséget a **helyi adapterhez**.

19. Adja meg a helyi alhálózat tartományát:
    - forti1:172.16.0.0/16
    - forti2:172.17.0.0/16

    Ha más IP-címtartományt használ, használja az IP-címtartományt.

20. Adja meg a helyszíni hálózatot jelölő megfelelő távoli alhálózatot, amelyet a helyszíni VPN-eszközön keresztül fog csatlakozni.

    [](./media/azure-stack-network-howto-vnet-to-onprem/image18.png)

21. **Létrehozás** kiválasztása

22. Válassza a **hálózati** > **adapterek**lehetőséget.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image19.png)

23. Kattintson duplán a **port2**elemre.

24. Válassza a **helyi hálózat** lehetőséget a **szerepkör** listában és a **DHCP-** ben a címzési mód beállításnál.

25. Kattintson az **OK** gombra.

## <a name="configure-the-on-premises-vpn"></a>A helyszíni VPN konfigurálása

Az IPSec VPN-alagút létrehozásához konfigurálni kell a helyszíni VPN-eszközt. A következő táblázat tartalmazza a helyszíni VPN-eszköz beállításához szükséges paramétereket. A helyszíni VPN-eszköz konfigurálásával kapcsolatos információkért tekintse meg a TP az eszköz dokumentációját.

| Paraméter | Érték |
| --- | --- |
| Távoli átjáró IP-címe | Forti1 rendelt nyilvános IP-cím – lásd: [az FortiGate-NVA aktiválása](#activate-the-fortigate-nva). |
| Távoli IP-hálózat | 172.16.0.0/16 (ha az IP-címtartományt használja a VNET található utasításokban). |
| Auth. Method = előmegosztott kulcs (PSK) | A 16. lépésből.
| IKE verziószám | 1 |
| IKE mód | Main (azonosító védelem) |
| 1. fázisú javaslati algoritmusok | AES128-SHA256, AES256-SHA256, AES128-SHA1, AES256-SHA1 |
| Diffie-Hellman csoportok | 14, 5 |

## <a name="create-the-vpn-tunnel"></a>A VPN-alagút létrehozása

A helyszíni VPN-eszköz megfelelő konfigurálását követően a VPN-alagút már létrehozható.

A FortiGate NVA:

1. A forti1 FortiGate webkonzolon lépjen az**IPSec-figyelő** **figyelése** > elemre.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image20.png)

2. Jelölje **ki a** >  **conn1** , és válassza ki a**2. fázist**.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image21.png)

## <a name="test-and-validate-connectivity"></a>Kapcsolat tesztelése és érvényesítése

A helyszíni VPN-eszköz használatával átirányíthatja a VNET hálózatot és a helyszíni hálózatot.

A kapcsolódás ellenőrzése:

1. Hozzon létre egy virtuális gépet a Azure Stack hub virtuális hálózatok és egy rendszer a helyszíni hálózaton. A virtuális gép létrehozásával kapcsolatos útmutatást a következő témakörben találja [: Windows Server rendszerű virtuális gép létrehozása az Azure stack hub Portalon](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal).

2. Az Azure Stack hub-beli virtuális gép létrehozásakor és a helyszíni rendszer előkészítésekor a következőt kell ellenőriznie:

-  Az Azure Stack hub virtuális gép a VNET **InsideSubnet** kerül.

-  A helyszíni rendszer a megadott IP-tartományon belüli helyszíni hálózatra kerül, az IPSec-konfigurációban meghatározottak szerint. Győződjön meg arról is, hogy a helyszíni VPN-eszköz helyi adapterének IP-címe a helyszíni rendszer számára olyan útvonalként van megadva, amely elérheti az Azure Stack hub VNET-hálózatot, `172.16.0.0/16`például:.

-  A létrehozáskor **ne** alkalmazzon nsg az Azure stack hub virtuális gépre. Előfordulhat, hogy el kell távolítania a NSG, amely alapértelmezés szerint fel lesz véve, ha a virtuális gépet a portálról hozza létre.

-  Győződjön meg arról, hogy a helyszíni rendszeroperációs rendszer és a Azure Stack hub VM operációs rendszer nem rendelkezik olyan operációsrendszer-tűzfalszabályok, amelyek tiltják a kapcsolat teszteléséhez használni kívánt kommunikációt. Tesztelési célból javasoljuk, hogy a tűzfalat teljes mértékben tiltsa le mindkét rendszer operációs rendszerén belül.

## <a name="next-steps"></a>További lépések

[A Azure Stack hub hálózatkezelésével kapcsolatos különbségek és megfontolások](azure-stack-network-differences.md)  
[Hálózati megoldás nyújtása Azure Stack központban a Fortinet FortiGate](../operator/azure-stack-network-solutions-enable.md)  
