---
title: Hibrid felhőalapú kapcsolat konfigurálása az Azure és az Azure Stack hub használatával
description: Megtudhatja, hogyan konfigurálhat hibrid felhőalapú kapcsolatot az Azure és a Azure Stack hub használatával.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 4b65ce6d03ee345b308cd5c5dd594e4dcf56cfec
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875504"
---
# <a name="configure-hybrid-cloud-connectivity-using-azure-and-azure-stack-hub"></a>Hibrid felhőalapú kapcsolat konfigurálása az Azure és az Azure Stack hub használatával

A hibrid kapcsolati mintával hozzáférhet az erőforrásokhoz a globális Azure-ban és Azure Stack hub-ban.

Ebben a megoldásban egy példaként szolgáló környezetet fog kiépíteni a következőkre:

> [!div class="checklist"]
> - Tartsa naprakészen az adatokat a helyszínen az adatvédelmi vagy szabályozási követelmények teljesítése érdekében, de a globális Azure-erőforrások elérését is megtarthatja.
> - Egy örökölt rendszer fenntartása a felhőalapú alkalmazások üzembe helyezésének és erőforrásainak a globális Azure-ban való használata során.

> [!Tip]  
> ![Hybrid-Pillars. png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack hub az Azure kiterjesztése. Azure Stack hub a felhő-számítástechnika rugalmasságát és innovációját a helyszíni környezetbe helyezi, így az egyetlen hibrid felhő, amely lehetővé teszi a hibrid alkalmazások bárhol történő létrehozását és üzembe helyezését.  
> 
> A [hibrid alkalmazásokkal kapcsolatos tervezési szempontok](overview-app-design-considerations.md) a szoftverek minőségének (elhelyezés, skálázhatóság, rendelkezésre állás, rugalmasság, kezelhetőség és biztonság) pilléreit tekintik át a hibrid alkalmazások tervezéséhez, üzembe helyezéséhez és üzemeltetéséhez. A kialakítási szempontok segítik a hibrid alkalmazások kialakításának optimalizálását, ami minimalizálja az éles környezetekben felmerülő kihívásokat.


## <a name="prerequisites"></a>Előfeltételek

A hibrid kapcsolat központi telepítésének létrehozásához néhány összetevő szükséges. Ezen összetevők némelyike időt szán a felkészülésre, ezért tervezze meg ennek megfelelően.

**Azure Stack hub**

Egy Azure OEM/Hardware partner üzembe helyezhet egy éles Azure Stack központot, és minden felhasználó üzembe helyezhet egy Azure Stack hub Development Kit (ASDK) csomagot.

**Azure Stack hub-összetevők**

Az Azure Stack hub-operátornak telepítenie kell a App Service, terveket és ajánlatokat kell létrehoznia, bérlői előfizetést kell létrehoznia, és hozzá kell adnia a Windows Server 2016 lemezképet. Ha már rendelkezik ezekkel az összetevőkkel, a megoldás megkezdése előtt győződjön meg arról, hogy megfelelnek a követelményeknek.

A megoldás példája feltételezi, hogy rendelkezik az Azure és az Azure Stack hub alapszintű ismeretével. Ha többet szeretne megtudni a megoldás megkezdése előtt, olvassa el a következő cikkeket:

 - [Bevezetés az Azure-ba](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack hub főbb fogalmak](../operator/azure-stack-overview.md)

### <a name="azure"></a>Azure

 - Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
 - Hozzon létre egy [webalkalmazást](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts) az Azure-ban. Jegyezze fel a webalkalmazás URL-címét, mert szüksége lesz rá a megoldásban.

### <a name="azure-stack-hub"></a>Azure Stack Hub

 - Használja az éles Azure Stack hub-t, vagy telepítse a Azure Stack hub fejlesztői készletet https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >A ASDK üzembe helyezése akár 7 órát is igénybe vehet, ezért tervezze meg ennek megfelelően.

 - [App Service](../operator/azure-stack-app-service-deploy.md) Péter-szolgáltatások üzembe helyezése Azure stack hubhoz.
 - [Terveket és ajánlatokat hozhat létre](../operator/service-plan-offer-subscription-overview.md) a Azure stack hub-környezetben.
 - [Bérlői előfizetés létrehozása](../operator/azure-stack-subscribe-plan-provision-vm.md) az Azure stack hub-környezeten belül.

### <a name="before-you-begin"></a>Előzetes teendők

A hibrid felhőalapú kapcsolatok konfigurálásának megkezdése előtt győződjön meg arról, hogy megfelel az alábbi feltételeknek:

 - A VPN-eszközhöz külsőleg megtekinthető nyilvános IPv4-címnek kell lennie. Ez az IP-cím nem helyezhető el NAT mögött (hálózati címfordítás).
 - Minden erőforrás üzembe helyezése ugyanabban a régióban/helyen történik.

#### <a name="solution-example-values"></a>Megoldási példa értékei

A megoldás példái a következő értékeket használják. Ezekkel az értékekkel létrehozhat egy tesztkörnyezetben, vagy megtekintheti őket a példák jobb megismeréséhez. A VPN Gateway beállításaival kapcsolatos további információkért lásd: [Tudnivalók a VPN Gateway beállításairól](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

A kapcsolatok specifikációi:

 - **VPN típusa**: Route-based
 - **Kapcsolat típusa**: helyek közötti (IPSec)
 - **Átjáró típusa**: VPN
 - **Azure-beli kapcsolatok neve**: Azure-Gateway-AzureStack-S2SGateway (a portál ezt az értéket fogja kitöltésre)
 - **Azure stack hub-kapcsolatok neve**: AzureStack-Gateway-Azure-S2SGateway (a portál ezt az értéket fogja kitöltésre)
 - **Megosztott kulcs**: bármely kompatibilis VPN-hardverrel, a kapcsolat mindkét oldalán egyező értékekkel
 - **Előfizetés**: bármely előnyben részesített előfizetés
 - **Erőforráscsoport**: test-infra

Hálózati és alhálózat IP-címei:

| Azure/Azure Stack hub-kapcsolatok | Név | Alhálózat | IP-cím |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure-vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure Stack hub vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Azure Virtual Network-átjáró | Azure – átjáró |  |  |
| Azure Stack hub Virtual Network átjáró | AzureStack – átjáró |  |  |
| Azure nyilvános IP-cím | Azure – GatewayPublicIP |  | Létrehozáskor meghatározva |
| Azure Stack hub nyilvános IP-címe | AzureStack – GatewayPublicIP |  | Létrehozáskor meghatározva |
| Azure-beli helyi hálózati átjáró | AzureStack – S2SGateway<br>   10.100.100.0/23 |  | Azure Stack hub nyilvános IP-értéke |
| Azure Stack hub helyi hálózati átjárója | Azure – S2SGateway<br>10.100.102.0/23 |  | Azure nyilvános IP-érték |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack-hub"></a>Virtuális hálózat létrehozása a globális Azure-ban és Azure Stack hub-ban

A következő lépésekkel hozhat létre virtuális hálózatot a portál használatával. Ha ezt a cikket csak megoldásként használja, használhatja ezeket a [példákat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) . Ha ezt a cikket egy éles környezet konfigurálására használja, cserélje le a példában szereplő beállításokat a saját értékeire.

> [!IMPORTANT]
> Győződjön meg arról, hogy nincs átfedésben az Azure-beli IP-címek vagy Azure Stack hub-vNet.

VNet létrehozása az Azure-ban:

1. A böngészőjével csatlakozhat a [Azure Portalhoz](https://portal.azure.com/) , és bejelentkezhet az Azure-fiókjával.
2. Válassza **az erőforrás létrehozása**lehetőséget. A **Keresés a piactéren** mezőbe írja be a "virtuális hálózat" kifejezést. Válassza ki a **virtuális hálózatot** az eredmények közül.
3. A **telepítési modell kiválasztása** listában válassza ki a **Resource Manager**elemet, majd válassza a **Létrehozás**lehetőséget.
4. A **virtuális hálózat létrehozása**területen konfigurálja a VNet beállításait. A kötelező mezők nevei vörös csillaggal vannak ellátva.  Ha érvényes értéket ad meg, a csillag zöld pipa jelre változik.

VNet létrehozása Azure Stack központban:

* Ismételje meg az előző lépéseket (1-4) az Azure Stack hub- **bérlői portál**használatával.

## <a name="add-a-gateway-subnet"></a>Átjáróalhálózat hozzáadása

Mielőtt csatlakoztatja virtuális hálózatát egy átjáróhoz, létre kell hoznia annak a virtuális hálózatnak az átjáró-alhálózatát, amelyhez csatlakozni kíván. Az átjáró-szolgáltatások az átjáró alhálózatában megadott IP-címeket használják.

A [Azure Portal](https://portal.azure.com/)Navigáljon arra a Resource Manager virtuális hálózatra, amelyben létre kíván hozni egy virtuális hálózati átjárót.

1. Válassza ki a vNet a **virtuális hálózat** lap megnyitásához.
2. A **Beállítások**területen válassza az **alhálózatok**lehetőséget.
3. Az **alhálózatok** lapon válassza az **+ átjáró alhálózat** lehetőséget az **alhálózat hozzáadása** lap megnyitásához.

    ![Átjáró-alhálózat hozzáadása](media/solution-deployment-guide-connectivity/image4.png)

4. Az alhálózat **nevét** a rendszer automatikusan kitölti a "GatewaySubnet" értékkel. Ez az érték szükséges ahhoz, hogy az Azure felismerje az alhálózatot átjáró-alhálózatként.
5. Módosítsa a megadott **címtartomány** -értékeket úgy, hogy megfeleljenek a konfigurációs követelményeinek, majd válassza az **OK**gombot.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Virtual Network átjáró létrehozása az Azure-ban és Azure Stack

Az alábbi lépéseket követve létrehozhat egy virtuális hálózati átjárót az Azure-ban.

1. A portál lap bal oldalán válassza a **+** lehetőséget, majd a keresőmezőbe írja be a "virtuális hálózati átjáró" kifejezést.
2. Az **eredmények**területen válassza a **virtuális hálózati átjáró**elemet.
3. A **virtuális hálózati átjáró**lapon válassza a **Létrehozás** lehetőséget a **virtuális hálózati átjáró létrehozása** lap megnyitásához.
4. A **virtuális hálózati átjáró létrehozása**lapon adja meg a hálózati átjáró értékeit az **oktatóanyag példái**alapján, valamint a következő további értékeket:

   - **SKU**: alapszintű
   - **Virtual Network**: válassza ki a korábban létrehozott Virtual Network. A létrehozott átjáró-alhálózat automatikusan ki van választva.
   - **Első IP-konfiguráció**: az átjáró nyilvános IP-címe.
     - Válassza az **átjáró létrehozása IP-konfiguráció**lehetőséget, amely a **nyilvános IP-cím választása** lapra lép.
     - Válassza az **+ új létrehozása** lehetőséget a **nyilvános IP-cím létrehozása** lap megnyitásához.
     - Adja meg a nyilvános IP-cím **nevét** . Hagyja **alapszintű**az SKU-t, majd kattintson **az OK gombra** a módosítások mentéséhez.

       > [!Note]
       > Jelenleg a VPN Gateway csak a dinamikus nyilvános IP-címek kiosztását támogatja. Ez azonban nem jelenti azt, hogy az IP-cím megváltozik a VPN-átjáróhoz való hozzárendelése után. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. A VPN-átjáró átméretezése, alaphelyzetbe állítása vagy egyéb belső karbantartása/frissítése nem változtatja meg az IP-címet.

4. Ellenőrizze az átjáró beállításait.
5. Válassza a **Létrehozás** lehetőséget a VPN-átjáró létrehozásához. Az átjáró beállításainak ellenőrzése megtörténik, és a "virtuális hálózati átjáró üzembe helyezése" csempe megjelenik az irányítópulton.

   >[!Note]
   >Az átjáró létrehozása akár 45 percet is igénybe vehet. Előfordulhat, hogy a kész állapot megjelenítéséhez frissítenie kell a portáloldalt.

    Az átjáró létrehozása után megtekintheti a hozzá rendelt IP-címet, ha megnézi a virtuális hálózatot a portálon. Az átjáró csatlakoztatott eszközként fog megjelenni. Az átjáróval kapcsolatos további információk megtekintéséhez válassza ki az eszközt.

6. Ismételje meg az előző lépéseket (1-5) az Azure Stack hub üzembe helyezése során.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack-hub"></a>Helyi hálózati átjáró létrehozása az Azure-ban és Azure Stack hub

A helyi hálózati átjáró általában a helyszínt jelenti. Adja meg a hely számára az Azure-beli vagy Azure Stack hub által hivatkozott nevet, majd adja meg a következőket:

- Annak a helyszíni VPN-eszköznek az IP-címe, amelyhez a kapcsolat létrejön.
- A VPN-átjárón a VPN-eszközre átirányított IP-címek előtagjai. Az Ön által meghatározott címelőtagok a helyszíni hálózatán található előtagok.

  >[!Note]
  >Ha a helyszíni hálózat megváltozik, vagy módosítania kell a VPN-eszköz nyilvános IP-címét, akkor később egyszerűen frissítheti ezeket az értékeket.

1. A portálon válassza az **+ erőforrás létrehozása**lehetőséget.
2. A keresőmezőbe írja be a **helyi hálózati átjáró**kifejezést, majd válassza az **ENTER billentyűt** a kereséshez. Az eredmények listája jelenik meg.
3. Válassza a **helyi hálózati átjáró**lehetőséget, majd válassza a **Létrehozás** lehetőséget a **helyi hálózati átjáró létrehozása** lap megnyitásához.
4. A **helyi hálózati átjáró létrehozása**lapon adja meg a helyi hálózati átjáró értékeit az **oktatóanyag példáinak**használatával. Adja meg a következő további értékeket.

    - **IP-cím**: annak a VPN-eszköznek a nyilvános IP-címe, amelyhez az Azure-t vagy Azure stack hubot csatlakoztatni kívánja. Olyan érvényes nyilvános IP-címet válasszon, amely nem a NAT mögött van, így az Azure elérheti a címet. Ha jelenleg nem rendelkezik az IP-címmel, a példában szereplő értéket helyőrzőként használhatja, de vissza kell térnie a helyőrző helyére a VPN-eszköz nyilvános IP-címével. Az Azure nem tud csatlakozni az eszközhöz, amíg érvényes címeket nem ad meg.
    - **Címterület**: annak a hálózatnak a címtartomány, amelyet ez a helyi hálózat képvisel. Több címtartományt is felvehet. Ügyeljen arra, hogy a megadott tartományok ne legyenek átfedésben más hálózatok tartományával, amelyhez csatlakozni kíván. Az Azure a helyszíni VPN-eszköz IP-címéhez irányítja át a megadott címtartományt. Saját értékeket használhat, ha a helyszíni helyhez szeretne csatlakozni, nem pedig egy példa értékre.
    - **BGP-beállítások konfigurálása**: csak a BGP konfigurálásakor használható. Ellenkező esetben ne jelölje be ezt a beállítást.
    - **Előfizetés**: Ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg.
    - **Erőforráscsoport**: válassza ki a használni kívánt erőforráscsoportot. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy már létrehozott csoportot is.
    - **Hely**: válassza ki azt a helyet, amelyen az objektumot létre kívánja hozni. Érdemes kijelölni ugyanazt a helyet, amelyet a VNet is tárol, de erre nincs szükség.
5. Amikor befejezte a szükséges értékek megadását, válassza a **Létrehozás** lehetőséget a helyi hálózati átjáró létrehozásához.
6. Ismételje meg ezeket a lépéseket (1-5) az Azure Stack hub üzembe helyezésére.

## <a name="configure-your-connection"></a>A kapcsolatok konfigurálása

A helyszíni hálózathoz való helyek közötti kapcsolatokhoz VPN-eszközre van szükség. A konfigurált VPN-eszközt kapcsolatnak nevezzük. A kapcsolódás konfigurálásához a következőkre lesz szüksége:

- Megosztott kulcs. Ez a kulcs ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor adott meg. A példákban alapvető megosztott kulcsot használunk. Javasoljuk egy ennél összetettebb kulcs létrehozását.
- A virtuális hálózati átjáró nyilvános IP-címe. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti. A VPN-átjáró nyilvános IP-címének a Azure Portal használatával történő megkereséséhez navigáljon a virtuális hálózati átjárók elemre, majd válassza ki az átjáró nevét.

A következő lépésekkel hozhat létre helyek közötti VPN-kapcsolatot a virtuális hálózati átjáró és a helyszíni VPN-eszköz között.

1. A Azure Portal válassza az **+ erőforrás létrehozása**lehetőséget.
2. **Kapcsolatok**keresése.
3. Az **eredmények**területen válassza a **kapcsolatok**lehetőséget.
4. A **kapcsolatok**lapon válassza a **Létrehozás**lehetőséget.
5. A **kapcsolatok létrehozása**területen adja meg a következő beállításokat:

    - **Kapcsolat típusa**: válassza a helyek közötti (IPSec) lehetőséget.
    - **Erőforráscsoport**: válassza ki a tesztelési erőforráscsoportot.
    - **Virtual Network átjáró**: válassza ki a létrehozott virtuális hálózati átjárót.
    - **Helyi hálózati átjáró**: válassza ki a létrehozott helyi hálózati átjárót.
    - **Kapcsolatok neve**: Ez a név automatikusan fel van töltve a két átjáró értékeinek használatával.
    - **Megosztott kulcs**: ennek az értéknek meg kell egyeznie a helyi helyszíni VPN-eszközhöz használt értékkel. Az oktatóanyag példája a "abc123"-t használja, de érdemes valami összetettebbet használni. A lényeg az, hogy ennek az értéknek meg kell egyeznie a VPN-eszköz konfigurálásakor megadott értékkel.
    - Az **előfizetés**, az **erőforráscsoport**és a **hely** értékei rögzítettek.

6. A kapcsolódás létrehozásához kattintson **az OK gombra** .

A kapcsolatot a virtuális hálózati átjáró **kapcsolatok** lapján tekintheti meg. Az állapot az *ismeretlentől* a *csatlakozáshoz*, majd a *sikeres*művelethez fog esni.

## <a name="next-steps"></a>Következő lépések

- Az Azure Cloud Patterns szolgáltatással kapcsolatos további információkért lásd: [Felhőbeli tervezési minták](https://docs.microsoft.com/azure/architecture/patterns).
