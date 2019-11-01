---
title: Erőforrások biztonsági mentése az Azure Stack előfizetés-replikációs szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan készíthet biztonsági mentést az erőforrásokról az Azure Stack előfizetés-replikációs szolgáltatással.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 10/29/2019
ms.openlocfilehash: 5ef02dbe7683b4c7364811452af59013476687fd
ms.sourcegitcommit: cc5c965b13bc3dae9a4f46a899e602f41dc66f78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73236245"
---
# <a name="how-to-back-up-resources-using-the-azure-stack-subscription-replicator"></a>Erőforrások biztonsági mentése az Azure Stack előfizetés-replikációs szolgáltatással

A Azure Stack előfizetés-replikációs PowerShell-parancsfájl segítségével Azure Stack-előfizetések közötti erőforrásokat másolhat. A replikátor parancsfájl beolvassa és újraépíti a Azure Resource Manager erőforrásokat a különböző Azure-és Azure Stack-előfizetésekkel. Ebből a cikkből megtudhatja, hogyan működik a parancsfájl, hogyan használhatja a parancsfájlt, és hogyan nyújt útmutatást a műveletekhez a parancsfájlban.

## <a name="subscription-replicator-overview"></a>Az előfizetés-replikátor áttekintése

Az Azure-előfizetési replikátor (v3) modulárisnak lett tervezve. Ez az eszköz egy olyan alapszintű processzort használ, amely összehangolja az erőforrás-replikációt. Emellett az eszköz támogatja a különböző típusú erőforrások másolásához sablonként szolgáló testreszabható processzorokat is. 

Az alapvető processzor a következő három parancsfájlból áll:

- **resource_retriever. ps1**

    - Mappákat hoz létre a kimeneti fájlok tárolásához.

    - Környezet beállítása a forrás-előfizetésre.

    - Lekéri az erőforrásokat, és továbbítja őket a **resource_processor. ps1**-nek.

- **resource_processor. ps1**

    - Feldolgozza a **resource_retriever. ps1**által átadott erőforrást.

    - Meghatározza, hogy melyik testreszabott processzort kell használni, és átadja az erőforrásokat.

- **post_process. ps1**

    - A post feldolgozza a testreszabott processzor által létrehozott kimenetet, hogy előkészítse a cél előfizetésben való üzembe helyezést.

    - Üzembe helyezési kódot hoz létre a cél előfizetésben lévő erőforrások telepítéséhez.

A három szkript egy szabványos módon vezérli az adatok áramlását, így nagyobb rugalmasságot biztosít. További erőforrások támogatásának hozzáadásával például nem kell megváltoztatnia az alapprocesszorban található kódokat.

A fent említett testreszabott processzorok olyan `ps1` fájlok, amelyek egy bizonyos típusú erőforrás feldolgozását írják le. A testreszabott processzorok neve mindig az erőforrás típusú adattípussal van elnevezve. Tegyük fel például, hogy `$vm` rendelkezik egy `$vm`futtató virtuálisgép-objektummal. A típus `Microsoft.Compute/virtualMachines`t eredményez. Ez azt jelenti, hogy egy virtuális géphez tartozó processzor neve `virtualMachines_processor.ps1`, a névnek pontosan úgy kell lennie, ahogy az erőforrás-metaadatokban jelenik meg, ahogyan az az alapvető processzor határozza meg, hogy melyik testreszabott processzort használja.

Egy testreszabott processzor azt diktálja, hogy az erőforrást hogyan kell replikálni annak meghatározásával, hogy milyen információk fontosak, és hogy az információk hogyan legyenek kihúzva az erőforrás-metaadatokból. A testreszabott processzor Ezután elvégzi az összes kibontott adatmennyiséget, és felhasználja egy Azure Resource Manager sablonnal együtt használt Parameters-fájl létrehozásához, hogy az erőforrást a cél előfizetésben telepítse. Ezt a paramétert a **Parameter_Files** tárolja a rendszer, miután a post_process. ps1 feldolgozta a bejegyzést.

A **Standardized_ARM_Templates**nevű replikátor-fájl struktúrájában egy mappa található. A forrás-környezettől függően az üzemelő példányok ezen szabványosított Azure Resource Manager-sablonok valamelyikét fogják használni, vagy egy testreszabott Azure Resource Manager sablont kell létrehozni. Ebben az esetben a testreszabott processzornak meg kell hívnia egy Azure Resource Manager sablon-generátort. A korábban elindított példában a virtuális gépekhez tartozó Azure Resource Manager sablon-generátor neve **virtualMachines_ARM_Template_Generator. ps1**lesz. A Azure Resource Manager sablon-generátor felelős egy testreszabott Azure Resource Manager sablon létrehozásához, amely alapján az adott erőforrás metaadataiban található információk alapján kell létrehoznia. Ha például a virtuális gép erőforrásához metaadatok vannak megadva, hogy az egy rendelkezésre állási csoport tagja, akkor a Azure Resource Manager template Generator létrehoz egy kódot tartalmazó Azure Resource Manager sablont, amely megadja a rendelkezésre állási csoport AZONOSÍTÓját, amely a a virtuális gép a részét képezi. Így amikor a virtuális gépet üzembe helyezi az új előfizetésre, automatikusan bekerül a rendelkezésre állási csoportba az üzembe helyezés után. Ezeket a testreszabott Azure Resource Manager sablonokat a **Standardized_ARM_Templates** mappában található **Custom_ARM_Templates** mappában tárolja a rendszer. A post_processor. ps1 feladata annak megállapítása, hogy egy üzemelő példány egy szabványosított Azure Resource Manager sablont vagy egy testreszabott alkalmazást használ-e, és létrehozza-e a megfelelő központi telepítési kódot.

A **post-Process. ps1** parancsfájl feladata a paraméterek fájljainak törlése és a felhasználó által az új erőforrások üzembe helyezéséhez használt parancsfájlok létrehozása. A tisztítási fázisban a parancsfájl a forrás-előfizetési AZONOSÍTÓra, a bérlői AZONOSÍTÓra és a helyre mutató összes hivatkozást lecseréli a megfelelő célértékek alapján. Ezután kiírja a paramétereket tartalmazó fájlt a **Parameter_Files** mappába. Ezután meghatározza, hogy a feldolgozás alatt álló erőforrás testreszabott Azure Resource Manager sablont használ-e, vagy sem, és létrehozza a megfelelő központi telepítési kódot, amely a **New-AzureRmResourceGroupDeployment** parancsmagot használja. A rendszer Ezután hozzáadja a telepítési kódot a **DeployResources. ps1** nevű fájlhoz, amelyet a **Deployment_Files** mappában tárolt. Végül a parancsfájl meghatározza azt az erőforráscsoportot, amelyhez az erőforrás tartozik, és a **DeployResourceGroups. ps1** parancsfájlban ellenőrzi, hogy az erőforráscsoport üzembe helyezési kódja már létezik-e. Ha nem, akkor a parancsfájlhoz hozzá kell adnia egy kódot az erőforráscsoport üzembe helyezéséhez, ha ez nem történik meg.

### <a name="dynamic-api-retrieval"></a>Dinamikus API-lekérés

Az eszköz beépített dinamikus API-beolvasási szolgáltatásának része, hogy a forrás-előfizetésben elérhető legújabb erőforrás-szolgáltató API-verzió a cél előfizetésben lévő erőforrások üzembe helyezéséhez használható:

![API-lekérések ábrája](./media/azure-stack-network-howto-backup-replicator/image1.png)

A **resource_processor. ps1**API-lekérésének ábrája.

Azonban előfordulhat, hogy a cél-előfizetés erőforrás-szolgáltatójának API-verziója régebbi, mint a forrás-előfizetés, és nem támogatja a forrás-előfizetéshez megadott verziót. Ebben az esetben a rendszer a központi telepítés futtatásakor hibát jelez. Ennek megoldásához frissítse a cél előfizetésben lévő erőforrás-szolgáltatókat, hogy azok megfeleljenek a forrás-előfizetésben szereplőknek.

### <a name="parallel-deployments"></a>Párhuzamos üzembe helyezések

Az eszközhöz a **Parallel**nevű paraméter szükséges. Ez a paraméter egy logikai értéket vesz fel, amely meghatározza, hogy a beolvasott erőforrásokat párhuzamosan kell-e telepíteni. Ha az érték **true (igaz),** akkor a **New-AzureRmResourceGroupDeployment** minden hívása a **-asJob** jelzővel és a kód azon blokkokkal fog bővülni, amelyek a párhuzamos feladatok befejezésére várnak, az erőforrás alapján típusú. Gondoskodik arról, hogy az egyik típus összes erőforrása telepítve legyen a következő típusú erőforrás üzembe helyezése előtt. Ha a **Parallel** paraméter értéke **false (hamis**), akkor a rendszer az erőforrásokat a soros módon telepíti.

## <a name="adding-additional-resource-types"></a>További erőforrástípusok hozzáadása

Az új erőforrástípusok hozzáadása egyszerű. A fejlesztőnek létre kell hoznia egy testreszabott processzort, valamint egy Azure Resource Manager sablont vagy egy Azure Resource Manager sablon-generátort. A befejezést követően a fejlesztőnek hozzá kell adnia az erőforrástípust a **$resourceType** paraméterhez és a **$resourceTypes** tömbhöz a resource_retriever. ps1 ValidateSet. Az erőforrástípus * * $resourceTypes * * tömbhöz való hozzáadásakor azt a megfelelő sorrendben kell hozzáadni. A tömb sorrendje határozza meg, hogy az erőforrások milyen sorrendben lesznek telepítve, ezért ne feledje, hogy a függőségek megmaradnak. Végül, ha a testreszabott processzor egy Azure Resource Manager sablon-generátort használ, hozzá kell adnia az erőforrástípus nevét a **post_process. ps1** **$customTypes** tömbhöz.

## <a name="running-azure-subscription-replicator"></a>Azure-előfizetési replikátor futtatása

Az Azure-előfizetési replikátor (v3) eszköz futtatásához el kell indítania a resource_retriever. ps1 eszközt, és meg kell adnia az összes paramétert. A **resourceType** paraméterrel lehetőség van az **összes** adattípus kiválasztására. Ha **minden** be van jelölve, a resource_retriever. ps1 az összes erőforrást feldolgozza egy sorrendben, hogy a központi telepítés Mikor fusson, a rendszer először a függő erőforrásokat telepíti. Ha például a virtuális gépek előtt üzembe helyezi a virtuális hálózatok-ket, a virtuális gépekhez VNet van szükség ahhoz, hogy megfelelően üzembe lehessen helyezni őket.

Ha a parancsfájl végrehajtása befejeződött, három új mappa, **Deployment_Files**, **Parameter_Files**és **Custom_ARM_Templates**jelenik meg.

 > [!Note]  
 > A generált parancsfájlok bármelyikének futtatása előtt be kell állítania a megfelelő környezetet, és be kell jelentkeznie a cél előfizetésre (az új Azure Stack ex esetében), és a munkakönyvtárat a **Deployment_Files** mappába kell beállítania.

A Deployment_Files két, **DeployResourceGroups. ps1** és **DeployResources. ps1**fájlt fog tárolni. A DeployResourceGroups. ps1 futtatása az erőforráscsoportok telepítését végzi. A DeployResources. ps1 futtatása a feldolgozott összes erőforrást üzembe helyezi. Abban az esetben, ha az eszköz az **összes** vagy a **Microsoft. számítás/virtualMachines** erőforrás-típussal lett végrehajtva, a DeployResources. ps1 megkéri a felhasználót, hogy adjon meg egy virtuálisgép-rendszergazdai jelszót, amelyet az összes virtuális gép létrehozásához fog használni. .

### <a name="example"></a>Példa

1.  Futtassa a szkriptet.

    ![](./media/azure-stack-network-howto-backup-replicator/image2.png)

1.  Várjon a szkript futtatására.

    ![](./media/azure-stack-network-howto-backup-replicator/image3.png)

1.  Tekintse át az újonnan létrehozott mappákat:

    ![](./media/azure-stack-network-howto-backup-replicator/image4.png)

    ![](./media/azure-stack-network-howto-backup-replicator/image5.png)

1.  Állítsa a kontextust a cél előfizetésre.

    ![](./media/azure-stack-network-howto-backup-replicator/image6.png)

1.  Írja be a `cd` értéket a **Deployment_Files** mappára való váltáshoz.

    ![](./media/azure-stack-network-howto-backup-replicator/image7.png)

1.  `DeployResourceGroups.ps1` futtatása az erőforráscsoportok telepítéséhez.

    ![](./media/azure-stack-network-howto-backup-replicator/image8.png)

1.  `DeployResources.ps1` futtatásával telepítse az erőforrásokat.

    ![](./media/azure-stack-network-howto-backup-replicator/image9.png)

1.  A `Get-Job` futtatásával ellenőrizhető az állapot. Get-Job | A Receive-Job az eredményeket fogja visszaadni.

    ![](./media/azure-stack-network-howto-backup-replicator/image10.png)

## <a name="clean-up"></a>Karbantartás

A replicatorV3 mappában található egy **cleanup_generated_items. ps1** nevű fájl, amely eltávolítja a **Deployment_Files**, a **Parameter_Files**és a **Custom_ARM_Templates** mappát és annak összes tartalmát.

## <a name="subscription-replicator-operations"></a>Előfizetés-replikációs műveletek

Az Azure-előfizetési replikátor (v3) jelenleg a következő erőforrástípusok replikálását végzi el:

- Microsoft. számítás/availabilitySets

- Microsoft.Compute/virtualMachines

- Microsoft.Network/loadBalancers

- Microsoft.Network/networkSecurityGroups

- Microsoft.Network/publicIPAddresses

- Microsoft. Network/routeTables

- Microsoft.Network/virtualNetworks

- Microsoft. Network/virtualNetworkGateways

- Microsoft.Storage/storageAccounts

Ha az eszközt az **összes** erőforrás-típussal futtatja, a replikálás és a telepítés során a rendszer a következő sorrendet követi (az alábbi sorrendben az összes erőforrás konfigurációját replikálták, például SKU, ajánlat stb.):

- Microsoft.Network/virtualNetworks

    - Replikálások: – minden címterület – minden alhálózat

- Microsoft. Network/virtualNetworkGateways

    - Replikálások: – nyilvános IP-konfiguráció – alhálózat konfigurációja – VPN-típus – átjáró típusa

- Microsoft. Network/routeTables

- Microsoft.Network/networkSecurityGroups

    - Replikálások: – az összes bejövő és kimenő biztonsági szabály

- Microsoft.Network/publicIPAddresses

- Microsoft.Network/loadBalancers

    - Replikálások:-magánhálózati IP-címek – nyilvános IP-cím konfigurációja – alhálózat konfigurációja
    
- Microsoft. számítás/availabilitySets

    - Replikálások: – tartalék tartományok száma – frissítési tartományok száma

- Microsoft.Storage/storageAccounts

- Microsoft.Compute/virtualMachines
    - Replikálja  
            – Adatlemezek (adatnélkül)  
            – Virtuális gép mérete  
            -Operációs rendszer  
            -Diagnosztikai Storage-fiók konfigurálása  
            – Nyilvános IP-konfiguráció  
            -Hálózati adapter  
            -Hálózati adapter magánhálózati IP-címe  
            -Hálózati biztonsági csoport konfigurálása  
            – Rendelkezésre állási csoport konfigurációja  
 
> [!Note]  
> Csak felügyelt lemezeket hoz létre az operációsrendszer-lemez és az adatlemezek számára. Jelenleg nem támogatott a Storage-fiókok használata. 

### <a name="limitations"></a>Korlátozások

Az eszköz replikálhatja az erőforrásokat az egyik előfizetésből a másikba, ha a cél-előfizetés erőforrás-szolgáltatói a forrás-előfizetésből replikált összes erőforrást és beállítást támogatják.

A sikeres replikálás érdekében győződjön meg arról, hogy a célként megadott előfizetés erőforrás-szolgáltatójának verziója megegyezik a forrás előfizetésével.

Ha Azure Stack a kereskedelmi Azure-ból a kereskedelmi Azure-ba vagy egy előfizetésből Azure Stackon belül egy másik előfizetésbe replikál, akkor a Storage-fiókok replikálásakor problémákba ütközik. Ennek oka, hogy a Storage-fiók elnevezési követelménye, hogy az összes Storage-fióknév egyedi legyen az összes kereskedelmi Azure-ban, vagy egy Azure Stack régióban/példányon lévő összes előfizetésen keresztül. A tárolási fiókok különböző Azure Stack példányokban való replikálása sikeres lesz, mivel a halmok különálló régiók/példányok.

## <a name="next-steps"></a>Következő lépések

[Különbségek és szempontok Azure Stack hálózatkezeléshez](azure-stack-network-differences.md)  