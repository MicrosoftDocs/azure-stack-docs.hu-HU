---
title: Azure Stack hub-VNET létrehozása a Fortinet FortiGate NVA való VNET-kapcsolathoz
description: Megtudhatja, hogyan hozhat létre VNET VNET-kapcsolat létesítéséhez a Fortinet FortiGate-NVA rendelkező Azure Stack központban.
author: mattbriggs
ms.topic: how-to
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: d9d76b848ed30521fb5a7ba983ef930e19b93866
ms.sourcegitcommit: 8ffa29f71d69191534d42f86f49f719b4198a097
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92355101"
---
# <a name="establish-a-vnet-to-vnet-connection-in-azure-stack-hub-with-fortinet-fortigate-nva"></a>Hozzon létre egy VNET a VNET-kapcsolathoz a Fortinet FortiGate NVA használatával Azure Stack központban.

Ebben a cikkben egy VNET csatlakoztat egy Azure Stack hubhoz egy másik Azure Stack hub egyik VNET a Fortinet FortiGate NVA, egy hálózati virtuális berendezés használatával.

Ez a cikk az aktuális Azure Stack hub-korlátozást ismerteti, amely lehetővé teszi, hogy a bérlők csak egy VPN-kapcsolaton keresztül állítsanak be két környezetet. A felhasználók megtudhatják, hogyan állíthat be egy egyéni átjárót egy Linux rendszerű virtuális gépen, amely több VPN-kapcsolatot is lehetővé tesz a különböző Azure Stack hubok között. A cikkben ismertetett eljárás két virtuális hálózatok helyez üzembe egy FortiGate-NVA az egyes VNET: egy központi telepítés Azure Stack hub-környezetben. Emellett részletesen ismerteti a két virtuális hálózatok közötti IPSec VPN beállításához szükséges módosításokat is. Az ebben a cikkben ismertetett lépéseket az egyes Azure Stack hubok minden VNET meg kell ismételni. 

## <a name="prerequisites"></a>Előfeltételek

-  Hozzáférés a megoldáshoz szükséges számítási, hálózati és erőforrás-követelmények üzembe helyezéséhez rendelkezésre álló kapacitással rendelkező Azure Stack hub integrált rendszerekhez. 

    > [!NOTE]  
    > Ezek az utasítások a ASDK hálózati korlátai miatt **nem** fognak működni Azure stack Development Kit (ASDK). További információ: [ASDK-követelmények és szempontok](../asdk/asdk-deploy-considerations.md).

-  Egy hálózati virtuális berendezés (NVA) megoldást töltöttek le és tesznek közzé az Azure Stack hub piactéren. Az NVA vezérli a peremhálózat hálózati forgalmának áramlását más hálózatokra vagy alhálózatokra. Ez az eljárás a [Fortinet FortiGate következő generációs tűzfalának egyetlen virtuálisgép-megoldását](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm)használja.

-  Legalább két elérhető FortiGate a FortiGate-NVA aktiválásához. A licencek beszerzésével kapcsolatos információkért tekintse meg a Fortinet a [regisztráció és a licenc letöltése](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)című cikket.

    Ez az eljárás az [egyetlen FortiGate-VM-telepítést](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment)használja. A FortiGate-NVA a helyszíni hálózatban lévő, a Azure Stack hub VNET való összekapcsolásával kapcsolatban talál további lépéseket.

    A FortiGate-megoldás aktív-passzív (HA) beállítással történő üzembe helyezésével kapcsolatos további információkért tekintse meg az [Azure-beli FortiGate-VM](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure)Fortinet dokumentációs cikkét.

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
> \* Válasszon másik címtartományt és alhálózati előtagokat, ha a fentiek bármilyen módon átfedésben vannak a helyszíni hálózati környezettel, beleértve a Azure Stack hub VIP-készletét. Győződjön meg arról is, hogy a címtartományok nem fedik egymást egymással. * *

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>A FortiGate NGFW Marketplace-elemek üzembe helyezése

Ismételje meg ezeket a lépéseket Azure Stack hub-környezetek esetében is. 

1. Nyissa meg az Azure Stack hub felhasználói portált. Ügyeljen arra, hogy olyan hitelesítő adatokat használjon, amelyek legalább közreműködői jogokkal rendelkeznek egy előfizetéshez.

1. Válassza az **erőforrás létrehozása** elemet, és keresse meg a következőt: `FortiGate` .

    ![A képernyőképen a "FortiGate" kifejezésből származó eredmények egyetlen sorát láthatja. Az észlelt elem neve "FortiGate NGFW-Single VM Deployment (BYOL)".](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image6.png)

1. Válassza ki a **FORTIGATE NGFW** , és válassza a **Létrehozás**lehetőséget.

1. A [központi telepítési paraméterek](#deployment-parameters) táblázat paramétereinek használatával végezheti el az **alapvető tudnivalókat** .

    Az űrlapnak a következő információkat kell tartalmaznia:

    ![Az alapvető beállítások párbeszédpanel szövegmezői (például a példány neve és a BYOL-licenc) a központi telepítési táblából származó értékekkel lettek kitöltve.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image7.png)

1. Válassza az **OK** lehetőséget.

1. Adja meg a virtuális hálózatot, az alhálózatokat és a virtuális gép méretét a [telepítési paraméterektől](#deployment-parameters).

    Ha más neveket és tartományokat kíván használni, ügyeljen arra, hogy ne használjon olyan paramétereket, amelyek ütköznek a többi Azure Stack hub-környezet más VNET és FortiGate erőforrásaival. Ez különösen igaz, ha a VNET IP-tartományát és az alhálózati tartományokat a VNET belül állítja be. Győződjön meg arról, hogy nem fedik át a létrehozott többi VNET IP-tartományait.

1. Válassza az **OK** lehetőséget.

1. Konfigurálja a FortiGate-NVA használni kívánt nyilvános IP-címet:

    ![Az IP-hozzárendelés párbeszédpanel "nyilvános IP-cím neve" szövegmezője a "forti1-publicip1" értéket jeleníti meg (a telepítési táblából).](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image8.png)

1. Válassza **az OK** , majd **az OK**gombot.

1. Válassza a **Létrehozás** lehetőséget.

Az üzembe helyezés körülbelül 10 percet vesz igénybe. Most megismételheti a lépéseket, hogy létrehozza a másik FortiGate-NVA és VNET-telepítést a másik Azure Stack hub-környezetben.

## <a name="configure-routes-udrs-for-each-vnet"></a>Útvonalak (UDR) konfigurálása az egyes VNET

Hajtsa végre ezeket a lépéseket mindkét központi telepítéshez, a forti1-rg1 és a forti2-rg1-hez.

1. Navigáljon a forti1-rg1 erőforráscsoporthoz az Azure Stack hub portálon.

    ![Ez az forti1-rg1 erőforráscsoport erőforrásainak listáját bemutató képernyőkép.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image9.png)

2. Válassza az "forti1-forti1-InsideSubnet-Routes-xxxx" erőforrást.

3. Válassza az **útvonalak** elemet a **Beállítások**területen.

    ![A képernyőképen a beállítások kijelölt útvonalak eleme látható.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image10.png)

4. Törölje a **to-Internet** útvonalat.

    ![A képernyőképen a Kiemelt internetes útvonal látható. Van egy DELETE gomb.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image11.png)

5. Válassza az **Igen** lehetőséget.

6. Válassza a **Hozzáadás** elemet.

7. Nevezze el az **útvonalat** `to-forti1` vagy a nevet `to-forti2` . Ha más IP-címtartományt használ, használja az IP-címtartományt.

8. Adja meg a következőt:
    - forti1: `172.17.0.0/16`  
    - forti2: `172.16.0.0/16`  

    Ha más IP-címtartományt használ, használja az IP-címtartományt.

9. Válassza ki a **virtuális készüléket** a **következő ugrási típushoz**.
    - forti1: `172.16.1.4`  
    - forti2: `172.17.0.4`  

    Ha más IP-címtartományt használ, használja az IP-címtartományt.

    ![A to-forti2 útvonal szerkesztése párbeszédpanel tartalmaz értékeket tartalmazó szövegmezőket. A "172.17.0.0/16" "a következő ugrás típusa" a "virtuális berendezés", a "Next hop-címe" pedig 172.16.1.4.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image12.png)

10. Válassza a **Mentés** lehetőséget.

Ismételje meg a lépéseket minden egyes **InsideSubnet** útvonalon.

## <a name="activate-the-fortigate-nvas-and-configure-an-ipsec-vpn-connection-on-each-nva"></a>Aktiválja a FortiGate NVA, és konfigurálja az IPSec VPN-kapcsolatokat az egyes NVA

 Az egyes FortiGate-NVA aktiválásához a Fortinet érvényes licencfájl szükséges. A NVA addig **nem** fog működni, amíg be nem aktiválja az egyes NVA. A licencfájl beszerzésével és a NVA aktiválásához szükséges lépésekkel kapcsolatos további információkért tekintse meg a Fortinet-dokumentum [regisztrálásával és a licenc letöltésével](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)foglalkozó cikket.

Két licencfájl beszerzése szükséges – egyet az egyes NVA.

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>IPSec VPN létrehozása a két NVA között

A NVA aktiválása után a következő lépésekkel hozzon létre egy IPSec VPN-t a két NVA között.

A forti1 NVA és a forti2 NVA az alábbi lépéseket követve:

1. A hozzárendelt nyilvános IP-cím lekéréséhez navigáljon a fortiX VM – áttekintés oldalra:

    ![Az forti1 áttekintése oldalon látható az erőforráscsoport, az állapot stb.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image13.png)

1. Másolja a hozzárendelt IP-címet, nyisson meg egy böngészőt, és illessze be a címet a címsorba. Előfordulhat, hogy a böngésző figyelmezteti, hogy a biztonsági tanúsítvány nem megbízható. Folytatás mindenképpen.

1. Adja meg az üzembe helyezés során megadott FortiGate rendszergazdai felhasználónevet és jelszót.

    ![A képernyőkép a bejelentkezési képernyő, amely a felhasználónévhez és a jelszóhoz tartozó bejelentkezési gombbal és szövegmezővel rendelkezik.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image14.png)

1. Válassza **a**  >  **rendszerbelső vezérlőprogram**lehetőséget.

1. Jelölje be a legújabb belső vezérlőprogram, például a következőt: `FortiOS v6.2.0 build0866` .

    ![A "FortiOS v 6.2.0 build0866" belső vezérlőprogram képernyőképe a kibocsátási megjegyzésekre mutató hivatkozást, valamint két gombot tartalmaz: "Backup config and Upgrade" és "upgrade".](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image15.png)

1. Válassza **a biztonsági mentés konfiguráció és frissítés** lehetőséget, és ha a rendszer kéri, folytassa.

1. A NVA frissíti a belső vezérlőprogramot a legújabb buildekre és újraindításokra. A folyamat körülbelül öt percet vesz igénybe. Jelentkezzen be újra a FortiGate webkonzolra.

1. Kattintson a **VPN**  >  **IPSec varázsló**elemre.

1. Adja meg a VPN nevét, például `conn1` a **VPN-létrehozási varázslóban**.

1. Válassza **ezt a helyet a NAT mögött**.

    ![A VPN-létrehozási varázsló képernyőképe azt mutatja, hogy az első lépésben, a VPN-beállításban kell lennie. A következő értékek vannak kiválasztva: "hely – hely" a sablon típusa, "FortiGate" a távoli eszköz típusa, és "Ez a hely a NAT mögött" a NAT-konfigurációhoz.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image16.png)

1. Kattintson a **Tovább** gombra.

1. Adja meg annak a helyszíni VPN-eszköznek a távoli IP-címét, amelyhez csatlakozni fog.

1. A **kimenő illesztőfelületként**válassza a **port1** lehetőséget.

1. Válassza az **előmegosztott kulcs** lehetőséget, és adja meg (és jegyezze fel) az előmegosztott kulcsot. 

    > [!NOTE]  
    > Erre a kulcsra szüksége lesz a kapcsolat beállításához a helyszíni VPN-eszközön, azaz *pontosan*meg kell egyeznie.

    ![A VPN-létrehozási varázsló képernyőképe azt mutatja, hogy a második lépésben, a hitelesítésen és a kiválasztott értékek kiemelése megtörténik.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image17.png)

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

    ![A VPN-létrehozási varázsló képernyőképe azt mutatja be, hogy a harmadik lépésben, a házirend & az útválasztásban, a kiválasztott és a beírt értékeket jeleníti meg.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image18.png)

1. Kattintson a **Létrehozás** elemre.

1. Válassza a **hálózati**  >  **adapterek**lehetőséget.  

    ![Az illesztőfelületek listáján két interfész látható: port1, amely konfigurálva van, és port2, amely még nem. Az illesztőfelületek létrehozásához, szerkesztéséhez és törléséhez gombokat is létrehozhat.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image19.png)

1. Kattintson duplán a **port2**elemre.

1. Válassza a **helyi hálózat** lehetőséget a **szerepkör** listában és a **DHCP-** ben a címzési mód beállításnál.

1. Válassza az **OK** lehetőséget.

Ismételje meg a többi NVA lépéseit.


## <a name="bring-up-all-phase-2-selectors"></a>A 2. fázis összes Választójának felébresztése 

Miután a fentiek befejeződtek **mindkét** NVA esetében:

1.  A forti2 FortiGate webkonzolon válassza az **Monitor**  >  **IPSec-figyelő**figyelése lehetőséget. 

    ![Megjelenik a VPN-kapcsolat conn1 figyelő. Ez a beállítás nem jelenik meg, mint a megfelelő 2. fázis választó.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image20.png)

2.  Jelölje ki `conn1` és válassza **Bring Up**ki az  >  **összes fázis 2 választók**lehetőséget.

    ![A figyelő és a 2. fázis választó is megjelenik.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image21.png)


## <a name="test-and-validate-connectivity"></a>Kapcsolat tesztelése és érvényesítése

A FortiGate-NVA keresztül most már képesnek kell lennie az egyes VNET közötti útválasztásra. A kapcsolódás ellenőrzéséhez hozzon létre egy Azure Stack hub virtuális gépet az egyes VNET InsideSubnet. Azure Stack hub-alapú virtuális gép létrehozása a portálon, a CLI-n vagy a PowerShellen keresztül végezhető el. Virtuális gépek létrehozásakor:

-   Az Azure Stack hub virtuális gépek az egyes VNET **InsideSubnet** lesznek elhelyezve.

-   A létrehozáskor semmilyen NSG **nem** alkalmaz a virtuális gépre (azaz ELTÁVOLÍTJA a NSG, amely alapértelmezés szerint hozzá lesz adva a virtuális gép a portálról való létrehozásakor.

-   Győződjön meg arról, hogy a virtuálisgép-tűzfalszabályok lehetővé teszik a kapcsolat teszteléséhez használni kívánt kommunikációt. Tesztelési célból javasoljuk, hogy a tűzfalat teljes mértékben tiltsa le az operációs rendszeren belül, ha lehetséges.

## <a name="next-steps"></a>Következő lépések

[A Azure Stack hub hálózatkezelésével kapcsolatos különbségek és megfontolások](azure-stack-network-differences.md)  
[Hálózati megoldás nyújtása Azure Stack központban a Fortinet FortiGate](../operator/azure-stack-network-solutions-enable.md)  
