---
title: Két virtuális hálózat összekötése ugyanabban a Azure Stack környezetben
description: Ismerje meg, hogyan csatlakoztathat két virtuális hálózatot ugyanabban a Azure Stack hub-környezetben a Fortinet FortiGate használatával.
author: mattbriggs
ms.topic: how-to
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: d4589e4ed9bc7850ce095456a27d3a9a642a1664
ms.sourcegitcommit: 8ffa29f71d69191534d42f86f49f719b4198a097
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92354516"
---
# <a name="vnet-peering-with-fortigate"></a>VNET-társítás a FortiGate

Ez a cikk azt ismerteti, hogyan hozható létre kapcsolat két virtuális hálózat között ugyanabban a környezetben. A kapcsolatok beállításakor megtudhatja, hogyan működik a VPN-átjárók Azure Stack hub-ban. Két virtuális hálózatok összekötése ugyanazon a Azure Stack hub-környezeten belül a Fortinet FortiGate használatával. Ez az eljárás két virtuális hálózatok helyez üzembe egy FortiGate NVA, egy hálózati virtuális berendezéssel, mindegyik VNET külön erőforráscsoporthoz. Emellett részletesen ismerteti a két virtuális hálózatok közötti IPSec VPN beállításához szükséges módosításokat is. Ismételje meg a cikkben ismertetett lépéseket az egyes VNET-telepítések esetében.

## <a name="prerequisites"></a>Előfeltételek

-   Hozzáférés a rendelkezésre álló kapacitással rendelkező rendszerekhez a megoldáshoz szükséges számítási, hálózati és erőforrás-követelmények telepítéséhez.

-  Egy hálózati virtuális berendezés (NVA) megoldást töltöttek le és tesznek közzé az Azure Stack hub piactéren. Az NVA vezérli a peremhálózat hálózati forgalmának áramlását más hálózatokra vagy alhálózatokra. Ez az eljárás a [Fortinet FortiGate következő generációs tűzfalának egyetlen virtuálisgép-megoldását](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm)használja.

-  Legalább két elérhető FortiGate a FortiGate-NVA aktiválásához. A licencek beszerzésével kapcsolatos információkért tekintse meg a Fortinet a [regisztráció és a licenc letöltése](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)című cikket.

    Ez az eljárás az [egyetlen FortiGate-VM-telepítést](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment)használja. A FortiGate-NVA a helyszíni hálózatban lévő, a Azure Stack hub VNET való összekapcsolásával kapcsolatban talál további lépéseket.

    A FortiGate-megoldás aktív-passzív (HA) beállítással történő üzembe helyezésével kapcsolatos további információkért tekintse meg az [Azure-beli FortiGate-VM](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure)Fortinet dokumentációjában található részleteket.

## <a name="deployment-parameters"></a>Központi telepítési paraméterek

Az alábbi táblázat összefoglalja a központi telepítésekben használt paramétereket a következő hivatkozással:

### <a name="deployment-one-forti1"></a>Központi telepítés: Forti1

| FortiGate-példány neve | Forti1 |
|-----------------------------------|---------------------------|
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

### <a name="deployment-two-forti2"></a>Kettő üzembe helyezése: Forti2

| FortiGate-példány neve | Forti2 |
|-----------------------------------|---------------------------|
| BYOL-licenc/-verzió | 6.0.3 |
| FortiGate rendszergazdai Felhasználónév | fortiadmin |
| Erőforráscsoport neve | forti2-rg1 |
| Virtuális hálózat neve | forti2vnet1 |
| VNET címterület | 172.17.0.0/16 * |
| Nyilvános VNET alhálózatának neve | forti2-PublicFacingSubnet |
| Nyilvános VNET-címek előtagja | 172.17.0.0/24 * |
| VNET alhálózatának neve | Forti2-InsideSubnet |
| VNET alhálózati előtagon belül | 172.17.1.0/24 * |
| FortiGate NVA virtuális gép mérete | Standard F2s_v2 |
| Nyilvános IP-cím | Forti2-publicip1 |
| Nyilvános IP-cím típusa | Statikus |

> [!NOTE]
> \* Válasszon másik címtartományt és alhálózati előtagokat, ha a fentiek bármilyen módon átfedésben vannak a helyszíni hálózati környezettel, beleértve a Azure Stack hub VIP-készletét. Győződjön meg arról is, hogy a címtartományok nem fedik át egymást egymással.

## <a name="deploy-the-fortigate-ngfw"></a>A FortiGate-NGFW üzembe helyezése

1.  Nyissa meg az Azure Stack hub felhasználói portált.

2.  Válassza az **erőforrás létrehozása** elemet, és keresse meg a következőt: `FortiGate` .

    ![A keresési eredmények listája a FortiGate NGFW – egyetlen virtuálisgép-telepítést jeleníti meg.](./media/azure-stack-network-howto-vnet-to-onprem/image6a.png)

3.  Válassza ki a **FORTIGATE NGFW** , és válassza a **Létrehozás**lehetőséget.

4.  Fejezze be az **alapismereteket** a [telepítési paraméterek](#deployment-parameters) táblázat paramétereinek használatával.

    ![Az alapvető beállítások képernyő a kiválasztott központi telepítési paraméterek értékeit tartalmazza, és a listában és a szövegmezőben szerepel.](./media/azure-stack-network-howto-vnet-to-onprem/image7a.png)

5.  Válassza az **OK** lehetőséget.

6.  Adja meg a virtuális hálózatot, az alhálózatokat és a virtuális gép méretét a [telepítési paraméterek](#deployment-parameters) táblázat használatával.

    > [!Warning] 
    > Ha a helyszíni hálózat átfedésben van az IP-tartománnyal `172.16.0.0/16` , ki kell választania és be kell állítania egy másik hálózati tartományt és alhálózatot. Ha más neveket és tartományokat kíván használni a [telepítési paraméterek](#deployment-parameters) táblázatban, akkor olyan paramétereket használjon, amelyek **nem** ütköznek a helyszíni hálózattal. Ügyeljen arra, hogy a VNET IP-címtartomány és alhálózati tartományok beállítása a VNET belül történjen. Nem szeretné, hogy a tartomány átfedésben legyen a helyszíni hálózatban található IP-tartományokkal.

7.  Válassza az **OK** lehetőséget.

8.  Konfigurálja a FortiGate NVA tartozó nyilvános IP-címet:

    ![Az IP-hozzárendelés párbeszédpanel a "nyilvános IP-cím" forti1 és a "nyilvános IP-cím" típushoz statikus "publicip1" értéket jeleníti meg.](./media/azure-stack-network-howto-vnet-to-onprem/image8a.png)

9.  Válassza az **OK** lehetőséget. Majd válassza az **OK** elemet.

10.  Válassza a **Létrehozás** lehetőséget.

Az üzembe helyezés körülbelül 10 percet vesz igénybe.

## <a name="configure-routes-udrs-for-each-vnet"></a>Útvonalak (UDR) konfigurálása az egyes VNET

Hajtsa végre ezeket a lépéseket mindkét központi telepítéshez, a forti1-rg1 és a forti2-rg1-hez.

1. Nyissa meg az Azure Stack hub felhasználói portált.

1. Válassza az erőforráscsoportok lehetőséget. Írja be `forti1-rg1` a szűrőt, majd kattintson duplán a forti1-rg1 erőforráscsoport elemre.

    ![Tíz erőforrás szerepel a forti1-rg1 erőforráscsoport listájában.](./media/azure-stack-network-howto-vnet-to-onprem/image9a.png)

1. Válassza ki a **forti1-forti1-InsideSubnet-Routes-XXXX** erőforrást.

1. Válassza az **útvonalak** elemet a **Beállítások**területen.

    ![Az útvonalak gomb a beállítások párbeszédpanelen van kiválasztva.](./media/azure-stack-network-howto-vnet-to-onprem/image10a.png)

1. Törölje a **to-Internet** útvonalat.

    ![A to-Internet útvonal az egyetlen felsorolt útvonal, és ki van választva. Van egy DELETE gomb.](./media/azure-stack-network-howto-vnet-to-onprem/image11a.png)

1. Válassza az *Igen* lehetőséget.

1. Új útvonal hozzáadásához válassza a **Hozzáadás** lehetőséget.

1. Nevezze el az útvonalat `to-onprem` .

1. Adja meg az IP-hálózati tartományt, amely meghatározza annak a helyszíni hálózatnak a hálózati tartományát, amelyhez a VPN csatlakozni fog.

1. Válassza a **virtuális berendezés** lehetőséget a **következő ugrási típushoz** és `172.16.1.4` . Ha más IP-címtartományt használ, használja az IP-címtartományt.

    ![Az útvonal hozzáadása párbeszédpanel a négy kiválasztott és a szövegmezőben megadott értékeket jeleníti meg.](./media/azure-stack-network-howto-vnet-to-onprem/image12a.png)

1. Válassza a **Mentés** lehetőséget.

Az egyes FortiGate-NVA aktiválásához szüksége lesz egy érvényes Fortinet-fájlra. A NVA addig **nem** fog működni, amíg be nem aktiválja az egyes NVA. A licencfájl beszerzésével és a NVA aktiválásához szükséges lépésekkel kapcsolatos további információkért tekintse meg a Fortinet-dokumentum [regisztrálásával és a licenc letöltésével](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)foglalkozó cikket.

Két licencfájl beszerzése szükséges – egyet az egyes NVA.

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>IPSec VPN létrehozása a két NVA között

A NVA aktiválása után a következő lépésekkel hozzon létre egy IPSec VPN-t a két NVA között.

A forti1 NVA és a forti2 NVA az alábbi lépéseket követve:

1. A hozzárendelt nyilvános IP-cím lekéréséhez navigáljon a fortiX VM – áttekintés oldalra:

    ![A forti1 virtuális gép áttekintő lapja a forti1, például az "erőforráscsoport" és az állapot értékeket jeleníti meg.](./media/azure-stack-network-howto-vnet-to-vnet/image13a.png)

1. Másolja a hozzárendelt IP-címet, nyisson meg egy böngészőt, és illessze be a címet a címsorba. Előfordulhat, hogy a böngésző figyelmezteti, hogy a biztonsági tanúsítvány nem megbízható. Folytatás mindenképpen.

1. Adja meg az üzembe helyezés során megadott FortiGate rendszergazdai felhasználónevet és jelszót.

    ![A Bejelentkezés párbeszédpanelen a felhasználó és a jelszó szövegmezők és a bejelentkezési gomb is látható.](./media/azure-stack-network-howto-vnet-to-vnet/image14a.png)

1. Válassza **a**  >  **rendszerbelső vezérlőprogram**lehetőséget.

1. Jelölje be a legújabb belső vezérlőprogram, például a következőt: `FortiOS v6.2.0 build0866` .

    ![A belső vezérlőprogram párbeszédpanel a "FortiOS v 6.2.0 build0866" belső vezérlőprogram-azonosítót, egy kibocsátási megjegyzésekre mutató hivatkozást és két gombot tartalmaz: "Backup config and Upgrade" és upgrade.](./media/azure-stack-network-howto-vnet-to-vnet/image15a.png)

1. Válassza **a biztonsági mentési konfiguráció és a frissítés**  >  **folytatása**lehetőséget.

1. A NVA frissíti a belső vezérlőprogramot a legújabb buildekre és újraindításokra. A folyamat körülbelül öt percet vesz igénybe. Jelentkezzen be újra a FortiGate webkonzolra.

1. Kattintson a **VPN**  >  **IPSec varázsló**elemre.

1. Adja meg a VPN nevét, például `conn1` a **VPN-létrehozási varázslóban**.

1. Válassza **ezt a helyet a NAT mögött**.

    ![A VPN-létrehozási varázsló képernyőképe azt mutatja, hogy az első lépésben, a VPN-beállításban kell lennie. A következő értékek vannak kiválasztva: "hely – hely" a sablon típusa, "FortiGate" a távoli eszköz típusa, és "Ez a hely a NAT mögött" a NAT-konfigurációhoz.](./media/azure-stack-network-howto-vnet-to-vnet/image16a.png)

1. Kattintson a **Tovább** gombra.

1. Adja meg annak a helyszíni VPN-eszköznek a távoli IP-címét, amelyhez csatlakozni fog.

1. A **kimenő illesztőfelületként**válassza a **port1** lehetőséget.

1. Válassza az **előmegosztott kulcs** lehetőséget, és adja meg (és jegyezze fel) az előmegosztott kulcsot. 

    > [!NOTE]  
    > Erre a kulcsra szüksége lesz a kapcsolat beállításához a helyszíni VPN-eszközön, azaz *pontosan*meg kell egyeznie.

    ![A VPN-létrehozási varázsló képernyőképe azt mutatja, hogy a második lépésben, a hitelesítésen és a kiválasztott értékek kiemelése megtörténik.](./media/azure-stack-network-howto-vnet-to-vnet/image17a.png)

1. Kattintson a **Tovább** gombra.

1. Válassza a **port2** lehetőséget a **helyi adapterhez**.

1. Adja meg a helyi alhálózat tartományát:
    - forti1:172.16.0.0/16
    - forti2:172.17.0.0/16

    Ha más IP-címtartományt használ, használja az IP-címtartományt.

1. Adja meg a helyszíni hálózatot jelölő megfelelő távoli alhálózatot, amelyet a helyszíni VPN-eszközön keresztül fog csatlakozni.
    - forti1:172.16.0.0/16
    - forti2:172.17.0.0/16

    Ha más IP-címtartományt használ, használja az IP-címtartományt.

    ![A VPN-létrehozási varázsló képernyőképe azt mutatja, hogy a harmadik lépés, a házirend & az Útválasztás. A kiválasztott és a beírt értékeket jeleníti meg.](./media/azure-stack-network-howto-vnet-to-vnet/image18a.png)

1. Kattintson a **Létrehozás** elemre.

1. Válassza a **hálózati**  >  **adapterek**lehetőséget.

    ![Az illesztőfelületek listáján két interfész látható: port1, amely konfigurálva van, és port2, amely még nem. Az illesztőfelületek létrehozásához, szerkesztéséhez és törléséhez gombokat is létrehozhat.](./media/azure-stack-network-howto-vnet-to-vnet/image19a.png)

1. Kattintson duplán a **port2**elemre.

1. Válassza a **helyi hálózat** lehetőséget a **szerepkör** listában és a **DHCP-** ben a címzési mód beállításnál.

1. Válassza az **OK** lehetőséget.

Ismételje meg a többi NVA lépéseit.

## <a name="bring-up-all-phase-2-selectors"></a>A 2. fázis összes Választójának felébresztése 

Miután a fentiek befejeződtek *mindkét* NVA esetében:

1.  A forti2 FortiGate webkonzolon válassza az **Monitor**  >  **IPSec-figyelő**figyelése lehetőséget. 

    ![Megjelenik a VPN-kapcsolat conn1 figyelő. Ez a beállítás nem jelenik meg, mint a megfelelő 2. fázis választó.](./media/azure-stack-network-howto-vnet-to-vnet/image20a.png)

2.  Jelölje ki `conn1` és válassza **Bring Up**ki az  >  **összes fázis 2 választók**lehetőséget.

    ![A figyelő és a 2. fázis választó is megjelenik.](./media/azure-stack-network-howto-vnet-to-vnet/image21a.png)

## <a name="test-and-validate-connectivity"></a>Kapcsolat tesztelése és érvényesítése

A FortiGate-NVA keresztül most már képesnek kell lennie az egyes VNET közötti útválasztásra. A kapcsolódás ellenőrzéséhez hozzon létre egy Azure Stack hub virtuális gépet az egyes VNET InsideSubnet. Azure Stack hub-alapú virtuális gép létrehozása a portálon, a CLI-n vagy a PowerShellen keresztül végezhető el. Virtuális gépek létrehozásakor:

-   Az Azure Stack hub virtuális gépek az egyes VNET **InsideSubnet** lesznek elhelyezve.

-   A létrehozáskor semmilyen NSG **nem** alkalmaz a virtuális gépre (azaz eltávolítja az alapértelmezés szerint hozzáadott NSG, ha létrehozza a virtuális gépet a portálról.

-   Győződjön meg arról, hogy a virtuális gépek tűzfalszabályok lehetővé teszik a kapcsolat teszteléséhez használni kívánt kommunikációt. Tesztelési célból javasoljuk, hogy a tűzfalat teljes mértékben tiltsa le az operációs rendszeren belül, ha lehetséges.

## <a name="next-steps"></a>Következő lépések

[A Azure Stack hub hálózatkezelésével kapcsolatos különbségek és megfontolások](azure-stack-network-differences.md)  
[Hálózati megoldás nyújtása Azure Stack központban a Fortinet FortiGate](../operator/azure-stack-network-solutions-enable.md)  
