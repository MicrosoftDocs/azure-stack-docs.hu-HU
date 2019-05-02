---
title: Hibrid felhő-kapcsolat konfigurálása az Azure és az Azure Stack |} A Microsoft Docs
description: 'Útmutató: hibrid felhő-kapcsolat konfigurálása az Azure és az Azure Stack.'
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 845bc2a04fe3153d0385a75445b28b51264c6b6b
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64301934"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Oktatóanyag: hibrid felhő-kapcsolat konfigurálása az Azure és az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A globális Azure-ban és a hibrid kapcsolat minta használatával az Azure Stack biztonsági hozzáférhetnek az erőforrásokhoz.

Ebben az oktatóanyagban egy mintául szolgáló környezet fog létrehozni:

> [!div class="checklist"]
> - Tartsa meg a helyi adatvédelmi és a szabályozási követelményeknek megfelelő, de a globális Azure-erőforrásokhoz való hozzáférése.
> - Egy korábbi rendszer karbantartása globális Azure-beli felhőalapú méretezett az alkalmazás üzembe helyezése és az erőforrások használata során.

> [!Tip]  
> ![hibrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> A Microsoft Azure Stack az Azure bővítménye. Az Azure Stack hatékonyságával és innovációjával a felhőalapú számítástechnika a helyszíni környezetben, és a egyetlen olyan hibrid felhő, amely lehetővé teszi, hogy létrehozása és üzembe helyezése bárhol a hibrid alkalmazások engedélyezésével biztosítható.  
> 
> A tanulmány [hibrid alkalmazások kapcsolatos kialakítási szempontok](https://aka.ms/hybrid-cloud-applications-pillars) tervezése, telepítése és a hibrid működő a szoftverminőség alappillérei (elhelyezését, a méretezhetőség, rugalmasság, kezelhetőségi és biztonsági) felülvizsgálatai az alkalmazások. A kialakítási szempontokat segítséget nyújt a hibrid alkalmazások kialakítását, minimálisra csökkentik az éles környezetben kihívások optimalizálása.


## <a name="prerequisites"></a>Előfeltételek

Néhány összetevő egy hibrid kapcsolat központi telepítés létrehozásához szükséges. Ezek az összetevők némelyike időt vesz igénybe előkészítése, így kell tervezni.

**Azure Stack**

Az OEM-hardver partnere üzembe helyezhet egy éles környezetben az Azure Stack, és az összes felhasználó telepítheti az Azure Stack Development Kit (ASDK).

**Az Azure Stack-összetevők**

Az Azure Stack-operátorokról kell az App Service üzembe helyezése, csomag és ajánlat létrehozása, hozzon létre egy bérlői előfizetést és a Windows Server 2016-lemezképének hozzáadása. Ha már rendelkezik néhány összetevőt, győződjön meg arról, hogy azok megfelelnek a követelményeinek, ez az oktatóanyag megkezdése előtt.

Ez az oktatóanyag feltételezi, hogy néhány alapvető ismeretek az Azure és az Azure Stack. Az oktatóanyag megkezdése előtt további tudnivalókért olvassa el a következő cikkeket:

 - [Bevezetés az Azure-bA](https://azure.microsoft.com/overview/what-is-azure/)
 - [Az Azure Stack főbb fogalmak](../operator/azure-stack-overview.md)

### <a name="azure"></a>Azure

 - Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy  [ingyenes](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)  fiókot.
 - Hozzon létre egy [Web App](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts) az Azure-ban. Jegyezze meg a webes alkalmazás URL-címe, mivel az oktatóanyagban szüksége lesz rá.

### <a name="azure-stack"></a>Azure Stack

 - Használja az éles üzemben futó Azure Stack, vagy üzembe helyezése az Azure Stack Development Kit a https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >A ASDK telepítése is 7 órán belül, így ennek megfelelően tervezze.

 - Üzembe helyezése [App Service-ben](../operator/azure-stack-app-service-deploy.md) PaaS-szolgáltatások az Azure Stackhez.
 - [Csomag és ajánlat létrehozása](../operator/azure-stack-plan-offer-quota-overview.md) az Azure Stack-környezetben.
 - [Hozzon létre a bérlő előfizetés](../operator/azure-stack-subscribe-plan-provision-vm.md) belül az Azure Stack-környezet.

### <a name="before-you-begin"></a>Előzetes teendők

Győződjön meg arról, hogy a hibrid felhőalapú kapcsolat beállítása előtt megfelelnek a következő feltételeknek:

 - Egy kifelé irányuló, nyilvános IPv4-cím a VPN-eszközhöz van szükség. Az IP-cím nem található a NAT mögötti.
 - Az azonos régió/hely összes erőforrás helyezik üzembe.

#### <a name="tutorial-example-values"></a>Az oktatóanyag példaértékek

Ebben az oktatóanyagban szereplő példák a következő értékeket használja. Ezek az értékek használatával létrehozhat egy tesztkörnyezetet, vagy tekintse meg ezeket a példákat tartalmazó szeretné jobban megismerni. További információ a VPN-átjáró beállításainak általában látják [tudnivalók a VPN Gateway beállításairól](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Kapcsolat-leírások:

 - **VPN-típust**: Útvonalalapú
 - **Kapcsolat típusa**: Helyek közötti (IPsec)
 - **Átjáró típusa**: VPN
 - **Azure-kapcsolat neve**: Az Azure-átjáró-AzureStack-S2SGateway (a portál automatikusan ki lesz ez az érték)
 - **Az Azure Stack kapcsolat neve**: 1.2.9-es-átjáró – Azure-S2SGateway (a portál automatikusan ki lesz ez az érték)
 - **Megosztott kulcs**: Minden kompatibilis a VPN-hardverre a megfelelő értékeket a kapcsolat mindkét oldalán
 - **Előfizetés**: Minden olyan előnyben részesített előfizetés
 - **Erőforráscsoport**: Test-Infra

Hálózat és alhálózat IP-címek:

| Azure/Azure Stack Connection | Name (Név) | Alhálózat | IP-cím |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Az Azure virtuális hálózat | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Az Azure Stack-vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Az Azure virtuális hálózati átjáró | Az Azure-átjáró |  |  |
| Az Azure Stack virtuális hálózati átjáró | AzureStack-Gateway |  |  |
| Azure Public IP | Azure-GatewayPublicIP |  | Határozza meg a létrehozásakor |
| Azure Stack Public IP | AzureStack-GatewayPublicIP |  | Határozza meg a létrehozásakor |
| Az Azure helyi hálózati átjáró | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Az Azure Stack nyilvános IP-cím érték |
| Az Azure Stack helyi hálózati átjáró | Azure-S2SGateway<br>10.100.102.0/23 |  | Az Azure nyilvános IP-cím érték |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Virtuális hálózat létrehozása a globális Azure és az Azure Stackben

A következő lépések segítségével hozzon létre egy virtuális hálózatot a portálon. E [példaértékeket](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) használata ebben a cikkben oktatóanyag keretében. De ha ez a cikk konfigurálásához éles környezetben használja, cserélje le a például szolgáló beállításokban a saját értékeire.

> [!IMPORTANT]
> Biztosítania kell, hogy nincs-e IP-címek az Azure-ban virtuális hálózat címterei átfedés.

VNet létrehozása az Azure-ban:

1. A böngésző használatával kapcsolódhat a [az Azure portal](https://portal.azure.com/) , és jelentkezzen be az Azure-fiókjával.
2. Válassza ki **erőforrás létrehozása**. Az a **keresés a piactéren** írja be a következőt `virtual network`meghatározással. Keresés **virtuális hálózati** a találatok listájában, és válassza ki a **virtuális hálózat**.
3. Az a **telepítési modell kiválasztása** listájáról válassza ki a **Resource Manager**, majd válassza ki **létrehozás**.
4. A **virtuális hálózat létrehozása**, konfigurálja a VNet beállításait. A kötelező mezők nevei piros csillag előtaggal van.  Egy érvényes értéket ad meg, ha a csillag módosítja egy zöld pipa.

VNet létrehozása az Azure Stack:

* Ismételje az előző lépést (1-4) az Azure Stack használatával **bérlői portál**.

## <a name="add-a-gateway-subnet"></a>Átjáróalhálózat hozzáadása

Mielőtt csatlakoztatja virtuális hálózatát egy átjáróhoz, kell hoznia az átjáróalhálózatot a virtuális hálózat, amelyhez csatlakozni kíván. Az Átjárószolgáltatások az átjáróalhálózatban megadott IP-címeket használják.

Az a [az Azure portal](https://portal.azure.com/), keresse meg a Resource Manager virtuális hálózatot, ahol szeretné a virtuális hálózati átjáró létrehozása.

1. Válassza ki a virtuális hálózat számára, hogy nyissa meg a **virtuális hálózati** lapot.
2. A **beállítások**válassza **alhálózatok**.
3. Az a **alhálózatok** lapon jelölje be **+ átjáró-alhálózat** megnyitásához a **alhálózat hozzáadása** lapot.

    ![Átjáróalhálózat hozzáadása](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. A **neve** a rendszer automatikusan kitölti az alhálózat "GatewaySubnet" érték. Ez az érték megadása kötelező az Azure felismerje az alhálózat egy átjáró alhálózata.
5. Módosítsa a **címtartomány** értékeket, amelyet megfeleljenek a konfigurációs követelményeinek, és válassza ki **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>A virtuális hálózati átjáró létrehozása az Azure és az Azure Stackben

Az alábbi lépések segítségével egy virtuális hálózati átjáró létrehozása az Azure-ban.

1. A portáloldal bal oldalán válassza ki a **+**  és a keresőmezőbe írja be a "virtuális hálózati átjáró".
2. A **eredmények**válassza **virtuális hálózati átjáró**.
3. A **virtuális hálózati átjáró**válassza **létrehozás** megnyitásához a **virtuális hálózati átjáró létrehozása** lap.
4. A **virtuális hálózati átjáró létrehozása**, adja meg a hálózati átjáró értékeit, ahogyan az **oktatóanyag példaértékeket**, és az alábbi értékeket:

   - **Termékváltozat**: alapszintű
   - **Virtuális hálózat**: Válassza ki a korábban létrehozott virtuális hálózatot. A létrehozott átjáróalhálózat automatikusan ki van jelölve.
   - **Első IP-konfiguráció**:  Ez a nyilvános IP-címe az átjárót.
     - Válassza ki **átjáró IP-konfigurációjának létrehozása**, amely veszi, hogy a **nyilvános IP-cím választása** lapot.
     - Válassza ki **+ új létrehozása** megnyitásához a **nyilvános IP-cím létrehozása** lapot.
     - Adjon meg egy **neve** a nyilvános IP-cím. A Termékváltozatot hagyja **alapszintű**, majd válassza ki **OK** a módosítások mentéséhez.

       > [!Note]
       > VPN Gateway jelenleg csak dinamikus nyilvános IP-cím lefoglalását támogatja. Ez azonban nem jelenti azt, hogy az IP-cím megváltozik, miután hozzá van rendelve a VPN-átjáró. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Átméretezés, alaphelyzetbe vagy más belső karbantartása/frissítése, a VPN-átjáró az IP-cím nem módosítható.

4. Az átjáró beállításainak ellenőrzése.
5. Válassza ki **létrehozás** a VPN-átjáró létrehozásához. A rendszer érvényesíti az átjáró-beállításokat, és a "Üzembe helyezés virtuális hálózati átjáró" csempére az irányítópulton jelenik meg.

   >[!Note]
   >Az átjáró létrehozása akár 45 percet is igénybe vehet. Előfordulhat, hogy a kész állapot megjelenítéséhez frissítenie kell a portáloldalt.

    Az átjáró létrehozása után megjelenik a portálon a virtuális hálózat megtekintésével rendelt IP-címet. Az átjáró csatlakoztatott eszközként fog megjelenni. További információ az átjáró megtekintéséhez válassza ki az eszközt.

6. Ismételje az előző lépést (1-5) az Azure Stack üzemelő példányon.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>A helyi hálózati átjáró létrehozása az Azure és az Azure Stackben

A helyi hálózati átjáró általában a helyszínt jelenti. El kell neveznie a helyet, amely Azure-ban is hivatkozik, és adja meg:

- Egy kapcsolatot hoz létre a helyszíni VPN-eszköz IP-címét.
- Az IP-címelőtagokat, amelyek keresztül a VPN-átjáró a VPN-eszközre lesznek irányítva. Az Ön által meghatározott címelőtagok a helyszíni hálózatán található előtagok.

  >[!Note]
  >Ha a helyszíni hálózat megváltozik, vagy módosítania kell a VPN-eszköz nyilvános IP-címét, könnyen frissítheti ezeket az értékeket később.

1. Válassza a portál **+ erőforrás létrehozása**.
2. A Keresés mezőbe írja be a **helyi hálózati átjáró**, majd **Enter** kereséséhez. Ez visszaad egy listát az eredményekkel.
3. Válassza ki **helyi hálózati átjáró**, majd **létrehozás** megnyitásához a **helyi hálózati átjáró létrehozása** lap.
4. A **helyi hálózati átjáró létrehozása**, adja meg a helyi hálózati átjáró értékeit használja a **oktatóanyag példaértékeket**. Az alábbi értékeket tartalmaznak.

    - **IP-cím**: Ez az Azure-ban való csatlakozáshoz használni kívánt VPN-eszköz nyilvános IP-címét. Adjon meg egy érvényes nyilvános IP-címet, amely nem NAT mögött, így Azure érni a címet. Ha az IP-cím jelenleg nem rendelkezik, használhatja a példa egy értéket egy helyőrző, de visszaléphet, és a helyőrzőket cserélje le a VPN-eszköz nyilvános IP-címét kell. Az Azure nem tud kapcsolódni az eszköz, amíg nem egy érvényes címet biztosít.
    - **Címtér**: Ez az a címtartományt, a hálózat, amely a helyi hálózat képvisel. Több címtartományt is felvehet. Győződjön meg arról, hogy a megadott címtartományok nem fednek át tartományok csatlakozni más hálózatokhoz. Az Azure a helyszíni VPN-eszköz IP-címéhez irányítja át a megadott címtartományt. Ha szeretne csatlakozni a helyszíni hely, a nem érték például a saját értékeit használja.
    - **BGP-beállítások konfigurálása**: Csak BGP konfigurálásakor használja. Más esetben ne jelölje be ezt a jelölőnégyzetet.
    - **Előfizetés**: Győződjön meg arról, hogy a megfelelő előfizetés jelenik-e meg.
    - **Erőforráscsoport**: Válassza ki a használni kívánt erőforráscsoportot. Hozzon létre egy új erőforráscsoportot, vagy kiválaszthat egy korábban már létrehozott.
    - **Hely**: Válassza ki a hellyel, ez az objektum létrejön. Előfordulhat, hogy szeretné kiválasztani a ugyanarra a helyre, amely a virtuális hálózat található, de már nem szükséges ehhez.
5. Ha befejezte a szükséges értékek megadásával, válassza ki a **létrehozás** a helyi hálózati átjáró létrehozásához.
6. Ismételje meg ezeket a lépéseket (1-5) az Azure Stack üzemelő példányához.

## <a name="configure-your-connection"></a>A kapcsolat konfigurálása

A helyszíni hálózaton a helyek közötti kapcsolatok létesítéséhez VPN-eszközre van szükség. Konfigurálja a VPN-eszköz kapcsolat nevezzük. A kapcsolat konfigurálásához lesz szüksége:

- Megosztott kulcs. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor ad meg. A példákban alapvető megosztott kulcsot használunk. Javasoljuk egy ennél összetettebb kulcs létrehozását.
- A virtuális hálózati átjáró nyilvános IP-címe. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti. Keresse meg az Azure portal használatával a VPN-átjáró nyilvános IP-címét, navigáljon a virtuális hálózati átjárók, majd válassza ki az átjáró nevét.

A következő lépések segítségével hozzon létre egy helyek közötti VPN-kapcsolatot a virtuális hálózati átjáró és a helyszíni VPN-eszköz között.

1. Az Azure Portalon válassza ki a **+ erőforrás létrehozása**.
2. Keresse meg **kapcsolatok**.
3. A **eredmények**válassza **kapcsolatok**.
4. A **kapcsolat**válassza **létrehozás**.
5. A **kapcsolat létrehozása**, adja meg a következő beállításokat:

    - **Kapcsolat típusa**: Válassza ki a Site-to-site (IPSec).
    - **Erőforráscsoport**: Válassza ki a teszt erőforráscsoportot.
    - **Virtuális hálózati átjáró**: Válassza ki a létrehozott virtuális hálózati átjárót.
    - **Helyi hálózati átjáró**: Válassza ki a helyi hálózati átjáró hozott létre.
    - **Kapcsolat neve**: Ez a rendszer automatikusan kitölti a két átjáró a értékeinek használatával.
    - **Megosztott kulcs**: Ezt az értéket meg kell egyeznie az érték, amely a helyi helyszíni VPN-eszközhöz használ. Az oktatóanyag példájában használja az "abc123", de lehetséges (és javasolt) használhatja összetettebb. A lényeg az, hogy ennek az értéknek kell lennie, amely a VPN-eszköz konfigurálásakor ad meg ugyanazt az értéket.
    - A tartozó értékeket **előfizetés**, **erőforráscsoport**, és **hely** rögzítettek.

6. Válassza ki **OK** a kapcsolat létrehozásához.

Láthatja, hogy a kapcsolatot a **kapcsolatok** a virtuális hálózati átjáró oldalán. Az állapot *ismeretlen* való *csatlakozás*, majd a *sikeres*.

## <a name="next-steps"></a>További lépések

- Az Azure-minták Felhőkhöz kapcsolatos további információkért lásd: [tervezési minták Felhőkhöz](https://docs.microsoft.com/azure/architecture/patterns).
