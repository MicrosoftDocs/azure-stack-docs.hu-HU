---
title: VPN-átjáró beállítása Azure Stack hubhoz
description: Megtudhatja, hogyan állíthatja be a VPN-átjárót Azure Stack hubhoz.
author: mattbriggs
ms.topic: how-to
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: 75138fa2b74666fee4149bf444558c98c250b4c8
ms.sourcegitcommit: 9557a5029cf329599f5b523c68e8305b876108d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88965160"
---
# <a name="set-up-vpn-gateway-for-azure-stack-hub-using-fortigate-nva"></a>VPN Gateway beállítása Azure Stack hubhoz a FortiGate NVA használatával

Ez a cikk azt ismerteti, hogyan hozható létre VPN-kapcsolat az Azure Stack hub-hoz. A VPN Gateway olyan virtuális hálózati átjáró, amely titkosított forgalmat küld Azure Stack hub és egy távoli VPN-átjáró között a virtuális hálózat között. Az alábbi eljárás az egyik VNET üzembe helyezi egy FortiGate-NVA, egy hálózati virtuális berendezéssel, egy erőforráscsoporton belül. Emellett útmutatást nyújt az IPSec VPN beállításához a FortiGate-NVA.

## <a name="prerequisites"></a>Előfeltételek

-  Hozzáférés a megoldáshoz szükséges számítási, hálózati és erőforrás-követelmények üzembe helyezéséhez rendelkezésre álló kapacitással rendelkező Azure Stack hub integrált rendszerekhez. 

    > [!NOTE]  
    > Ezek az utasítások a ASDK hálózati korlátozásai miatt **nem** fognak működni Azure stack Development Kit (ASDK). További információ: [ASDK-követelmények és szempontok](../asdk/asdk-deploy-considerations.md).

-  Hozzáférés az Azure Stack hub integrált rendszerét futtató helyszíni hálózatban található VPN-eszközhöz. Az eszköznek létre kell hoznia egy IPSec-alagutat, amely megfelel a [központi telepítési paraméterekben](#deployment-parameters)leírt paramétereknek.

-  A Azure Stack hub piactéren elérhető hálózati virtuális berendezés (NVA) megoldás. Az NVA vezérli a peremhálózat hálózati forgalmának áramlását más hálózatokra vagy alhálózatokra. Ez az eljárás a [Fortinet FortiGate következő generációs tűzfalának egyetlen virtuálisgép-megoldását](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm)használja.

    > [!NOTE]  
    > Ha nem rendelkezik a **Fortinet FortiGate-VM for Azure BYOL** és **FortiGate NGFW – egyetlen virtuálisgép-telepítés (BYOL)** a Azure stack hub piactéren, forduljon a felhő üzemeltetőjéhez.

-  A FortiGate-NVA aktiválásához szüksége lesz legalább egy elérhető FortiGate-fájlra. A licencek beszerzésével kapcsolatos információkat lásd: a Fortinet-dokumentum [regisztrálása és a licenc letöltése](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

    Ez az eljárás az [egyetlen FortiGate-VM-telepítést](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment)használja. A FortiGate-NVA a helyszíni hálózatban lévő, a Azure Stack hub VNET való összekapcsolásával kapcsolatban talál további lépéseket.

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

> [!NOTE]
> \* Ha `172.16.0.0/16` átfedésben van a helyszíni hálózattal vagy az Azure stack hub VIP-készlettel, válasszon másik címtartományt és alhálózati előtagokat.

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>A FortiGate NGFW Marketplace-elemek üzembe helyezése

1. Nyissa meg az Azure Stack hub felhasználói portált.

    ![A kezdőképernyő megjelenik a "+ erőforrás létrehozása" kijelöléssel a bal oldali panelen.](./media/azure-stack-network-howto-vnet-to-onprem/image5.png)

1. Válassza az **erőforrás létrehozása** elemet, és keresse meg a következőt: `FortiGate` .

    ![A keresési eredmények listája a FortiGate NGFW – egyetlen virtuálisgép-telepítést jeleníti meg.](./media/azure-stack-network-howto-vnet-to-onprem/image6.png)

1. Válassza ki a **FORTIGATE NGFW** , és válassza a **Létrehozás**lehetőséget.

1. A [központi telepítési paraméterek](#deployment-parameters) táblázat paramétereinek használatával végezheti el az **alapvető tudnivalókat** .

    ![Az alapvető beállítások képernyő a listában és a szövegmezőben megadott telepítési paraméterek táblából származó értékeket tartalmaz.](./media/azure-stack-network-howto-vnet-to-onprem/image7.png)

1. Válassza az **OK** lehetőséget.

1. Adja meg a virtuális hálózatot, az alhálózatokat és a virtuális gép méretét a [telepítési paraméterek](#deployment-parameters) táblázat használatával.

    > [!Warning] 
    > Ha a helyszíni hálózat átfedésben van az IP-tartománnyal `172.16.0.0/16` , ki kell választania és be kell állítania egy másik hálózati tartományt és alhálózatot. Ha más neveket és tartományokat kíván használni a [telepítési paraméterek](#deployment-parameters) táblázatban, akkor olyan paramétereket használjon, amelyek **nem** ütköznek a helyszíni hálózattal. Ügyeljen arra, hogy a VNET IP-címtartomány és alhálózati tartományok beállítása a VNET belül történjen. Nem szeretné, hogy a tartomány átfedésben legyen a helyszíni hálózatban található IP-tartományokkal.

1. Válassza az **OK** lehetőséget.

1. Konfigurálja a FortiGate NVA tartozó nyilvános IP-címet:

    ![Az IP-hozzárendelés párbeszédpanel a "nyilvános IP-cím" forti1 és a "nyilvános IP-cím" típushoz statikus "publicip1" értéket jeleníti meg.](./media/azure-stack-network-howto-vnet-to-onprem/image8.png)

1. Válassza az **OK** lehetőséget. Majd válassza az **OK** elemet.

1. Kattintson a **Létrehozás** gombra.

    Az üzembe helyezés körülbelül 10 percet vesz igénybe.

## <a name="configure-routes-udr-for-the-vnet"></a>Útvonalak konfigurálása (UDR) a VNET

1. Nyissa meg az Azure Stack hub felhasználói portált.

2. Válassza az erőforráscsoportok lehetőséget. Írja be `forti1-rg1` a szűrőt, majd kattintson duplán a forti1-rg1 erőforráscsoport elemre.

    ![Tíz erőforrás szerepel a forti1-rg1 erőforráscsoport listájában.](./media/azure-stack-network-howto-vnet-to-onprem/image9.png)

2. Válassza ki az "forti1-forti1-InsideSubnet-Routes-xxxx" erőforrást.

3. Válassza az **útvonalak** elemet a **Beállítások**területen.

    ![Az útvonalak gomb a beállítások párbeszédpanelen van kiválasztva.](./media/azure-stack-network-howto-vnet-to-onprem/image10.png)

4. Törölje a **to-Internet** útvonalat.

    ![A to-Internet útvonal az egyetlen felsorolt útvonal, és ki van választva. Van egy DELETE gomb.](./media/azure-stack-network-howto-vnet-to-onprem/image11.png)

5. Válassza az *Igen* lehetőséget.

6. Új útvonal hozzáadásához válassza a **Hozzáadás** lehetőséget.

7. Nevezze el az útvonalat `to-onprem` .

8. Adja meg az IP-hálózati tartományt, amely meghatározza annak a helyszíni hálózatnak a hálózati tartományát, amelyhez a VPN csatlakozni fog.

9. Válassza a **virtuális berendezés** lehetőséget a **következő ugrási típushoz** és `172.16.1.4` . Ha más IP-címtartományt használ, használja az IP-címtartományt.

    ![Az útvonal hozzáadása párbeszédpanel megjeleníti a szövegmezőben megadott négy értéket.](./media/azure-stack-network-howto-vnet-to-onprem/image12.png)

10. Kattintson a **Mentés** gombra.

## <a name="activate-the-fortigate-nva"></a>A FortiGate-NVA aktiválása

Aktiválja a FortiGate NVA, és állítson be egy IPSec VPN-kapcsolatot az egyes NVA.

Az egyes FortiGate-NVA aktiválásához érvényes licencfájl szükséges a Fortinet-től. A NVA addig **nem** fog működni, amíg be nem aktiválja az egyes NVA. A licencfájl beszerzésével és a NVA aktiválásához szükséges lépésekkel kapcsolatos további információkért tekintse meg a Fortinet-dokumentum [regisztrálásával és a licenc letöltésével](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)foglalkozó cikket.

Miután aktiválta a NVA, hozzon létre egy IPSec VPN-alagutat a NVA.

1. Nyissa meg az Azure Stack hub felhasználói portált.

2. Válassza az erőforráscsoportok lehetőséget. Írja be `forti1` a szűrőt, és kattintson duplán a forti1-erőforráscsoport elemre.

3. Kattintson duplán a **forti1** virtuális gépre az erőforráscsoport paneljén található erőforrástípusok listájában.

    ![A forti1 virtuális gép áttekintő lapja a forti1, például az "erőforráscsoport" és az állapot értékeket jeleníti meg.](./media/azure-stack-network-howto-vnet-to-onprem/image13.png)

4. Másolja a hozzárendelt IP-címet, nyisson meg egy böngészőt, és illessze be az IP-címet a címsorba. Előfordulhat, hogy a hely figyelmeztetést vált ki, hogy a biztonsági tanúsítvány nem megbízható. Folytatás mindenképpen.

5. Adja meg az üzembe helyezés során megadott FortiGate rendszergazdai felhasználónevet és jelszót.

    ![A Bejelentkezés párbeszédpanelen a felhasználó és a jelszó szövegmezők és a bejelentkezési gomb is látható.](./media/azure-stack-network-howto-vnet-to-onprem/image14.png)

6. Válassza **a**  >  **rendszerbelső vezérlőprogram**lehetőséget.

7. Jelölje be a legújabb belső vezérlőprogram, például a következőt: `FortiOS v6.2.0 build0866` .

    ![A belső vezérlőprogram párbeszédpanelen a "FortiOS v 6.2.0 build0866" belső vezérlőprogram-azonosító látható. Létezik egy hivatkozás a kibocsátási megjegyzésekre, és két gomb: "a Backup konfiguráció és a frissítés", és a frissítés.](./media/azure-stack-network-howto-vnet-to-onprem/image15.png)

8. Válassza **a biztonsági mentési konfiguráció és a frissítés**  >  **folytatása**lehetőséget.

9. A NVA frissíti a belső vezérlőprogramot a legújabb buildekre és újraindításokra. A folyamat körülbelül öt percet vesz igénybe. Jelentkezzen be újra a FortiGate webkonzolra.

10. Kattintson a **VPN**  >  **IPSec varázsló**elemre.

11. Adja meg a VPN nevét, például `conn1` a **VPN-létrehozási varázslóban**.

12. Válassza **ezt a helyet a NAT mögött**.

    ![A VPN-létrehozási varázsló képernyőképe azt mutatja, hogy az első lépésben, a VPN-beállításban kell lennie. A következő értékek vannak kiválasztva: "hely – hely" a sablon típusa, "FortiGate" a távoli eszköz típusa, és "Ez a hely a NAT mögött" a NAT-konfigurációhoz.](./media/azure-stack-network-howto-vnet-to-onprem/image16.png)

13. Kattintson a **Tovább** gombra.

14. Adja meg annak a helyszíni VPN-eszköznek a távoli IP-címét, amelyhez csatlakozni fog.

15. A **kimenő illesztőfelületként**válassza a **port1** lehetőséget.

16. Válassza az **előmegosztott kulcs** lehetőséget, és adja meg (és jegyezze fel) az előmegosztott kulcsot. 

    > [!NOTE]  
    > Erre a kulcsra szüksége lesz a kapcsolat beállításához a helyszíni VPN-eszközön, azaz *pontosan*meg kell egyeznie.

    ![A VPN-létrehozási varázsló képernyőképe azt mutatja, hogy a második lépésben, a hitelesítésen és a kiválasztott értékek kiemelése megtörténik.](./media/azure-stack-network-howto-vnet-to-onprem/image17.png)

17. Kattintson a **Tovább** gombra.

18. Válassza a **port2** lehetőséget a **helyi adapterhez**.

19. Adja meg a helyi alhálózat tartományát:
    - forti1:172.16.0.0/16
    - forti2:172.17.0.0/16

    Ha más IP-címtartományt használ, használja az IP-címtartományt.

20. Adja meg a helyszíni hálózatot jelölő megfelelő távoli alhálózatot, amelyet a helyszíni VPN-eszközön keresztül fog csatlakozni.

    ![A VPN-létrehozási varázsló képernyőképe azt mutatja, hogy a harmadik lépés, a házirend & az Útválasztás. A kiválasztott és a beírt értékeket jeleníti meg.](./media/azure-stack-network-howto-vnet-to-onprem/image18.png)

21. Kattintson a **Létrehozás** elemre.

22. Válassza a **hálózati**  >  **adapterek**lehetőséget.

    ![Az illesztőfelületek listáján két interfész látható: port1, amely konfigurálva van, és port2, amely még nem. Az illesztőfelületek létrehozásához, szerkesztéséhez és törléséhez gombokat is létrehozhat.](./media/azure-stack-network-howto-vnet-to-onprem/image19.png)

23. Kattintson duplán a **port2**elemre.

24. Válassza a **helyi hálózat** lehetőséget a **szerepkör** listában és a **DHCP-** ben a címzési mód beállításnál.

25. Válassza az **OK** lehetőséget.

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

1. A forti1 FortiGate webkonzolon lépjen az **Monitor**  >  **IPSec-figyelő**figyelése elemre.

    ![Megjelenik a VPN-kapcsolat conn1 figyelő. Ez a beállítás nem jelenik meg, mint a megfelelő 2. fázis választó.](./media/azure-stack-network-howto-vnet-to-onprem/image20.png)

2. Jelölje **ki a** **conn1** , és válassza ki a  >  **2. fázist**.

    ![A figyelő és a 2. fázis választó is megjelenik.](./media/azure-stack-network-howto-vnet-to-onprem/image21.png)

## <a name="test-and-validate-connectivity"></a>Kapcsolat tesztelése és érvényesítése

A helyszíni VPN-eszköz használatával átirányíthatja a VNET hálózatot és a helyszíni hálózatot.

A kapcsolódás ellenőrzése:

1. Hozzon létre egy virtuális gépet a Azure Stack hub virtuális hálózatok és egy rendszer a helyszíni hálózaton. A virtuális gép létrehozásával kapcsolatos útmutatást a következő témakörben találja [: Windows Server rendszerű virtuális gép létrehozása az Azure stack hub Portalon](./azure-stack-quick-windows-portal.md).

2. Az Azure Stack hub-beli virtuális gép létrehozásakor és a helyszíni rendszer előkészítésekor a következőt kell ellenőriznie:

-  Az Azure Stack hub virtuális gép a VNET **InsideSubnet** kerül.

-  A helyszíni rendszer a megadott IP-tartományon belüli helyszíni hálózatra kerül, az IPSec-konfigurációban meghatározottak szerint. Győződjön meg arról is, hogy a helyszíni VPN-eszköz helyi adapterének IP-címe a helyszíni rendszer számára olyan útvonalként van megadva, amely elérheti az Azure Stack hub VNET-hálózatot, például: `172.16.0.0/16` .

-  A létrehozáskor **ne** alkalmazzon nsg az Azure stack hub virtuális gépre. Előfordulhat, hogy el kell távolítania a NSG, amely alapértelmezés szerint fel lesz véve, ha a virtuális gépet a portálról hozza létre.

-  Győződjön meg arról, hogy a helyszíni rendszeroperációs rendszer és a Azure Stack hub VM operációs rendszer nem rendelkezik olyan operációsrendszer-tűzfalszabályok, amelyek tiltják a kapcsolat teszteléséhez használni kívánt kommunikációt. Tesztelési célból javasoljuk, hogy a tűzfalat teljes mértékben tiltsa le mindkét rendszer operációs rendszerén belül.

## <a name="next-steps"></a>Következő lépések

[A Azure Stack hub hálózatkezelésével kapcsolatos különbségek és megfontolások](azure-stack-network-differences.md)  
[Hálózati megoldás nyújtása Azure Stack központban a Fortinet FortiGate](../operator/azure-stack-network-solutions-enable.md)  
