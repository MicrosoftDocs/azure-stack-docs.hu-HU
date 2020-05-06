---
title: Magasan elérhető MongoDB-megoldás üzembe helyezése az Azure-ban és Azure Stack hub
description: Ismerje meg, hogyan helyezhet üzembe egy magasan elérhető MongoDB-megoldást az Azure-ban és Azure Stack hub-ban
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: b34ba7c10ff5f658d645923ae8b6de2fb2607ccb
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847180"
---
# <a name="deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack-hub"></a>Magasan elérhető MongoDB-megoldás üzembe helyezése az Azure-ban és Azure Stack hub

Ez a cikk végigvezeti egy olyan alapszintű magas rendelkezésre állású (HA) MongoDB-fürt automatikus üzembe helyezésén, amely egy vész-helyreállítási (DR) hellyel rendelkezik két Azure Stack hub-környezetben. További információ a MongoDB és a magas rendelkezésre állásról: [replikakészlet tagjai](https://docs.mongodb.com/manual/core/replica-set-members/).

Ebben a megoldásban a következőhöz hozzon létre egy mintavételi környezetet:

> [!div class="checklist"]
> - Üzembe helyezés koordinálása két Azure Stack hub között.
> - A Docker használatával csökkentheti a függőségi problémákat az Azure API-profilokkal.
> - Helyezzen üzembe egy alapszintű, magasan elérhető MongoDB-fürtöt vész-helyreállítási hellyel.

> [!Tip]  
> ![Hybrid-Pillars. png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack hub az Azure kiterjesztése. Azure Stack hub a felhő-számítástechnika rugalmasságát és innovációját a helyszíni környezetbe helyezi, így az egyetlen hibrid felhő, amely lehetővé teszi a hibrid alkalmazások bárhol történő létrehozását és üzembe helyezését.  
> 
> A [hibrid alkalmazások kialakításával kapcsolatos megfontolások](overview-app-design-considerations.md) a szoftverek minőségének (elhelyezés, skálázhatóság, rendelkezésre állás, rugalmasság, kezelhetőség és biztonság) pilléreit tekintik át hibrid alkalmazások tervezéséhez, üzembe helyezéséhez és üzemeltetéséhez. A kialakítási szempontok segítik a hibrid alkalmazások kialakításának optimalizálását, ami minimalizálja az éles környezetekben felmerülő kihívásokat.

## <a name="architecture-for-mongodb-with-azure-stack-hub"></a>A MongoDB architektúrája Azure Stack hub-vel

![magasan elérhető MongoDB architektúra Azure Stack hub-ban](media/solution-deployment-guide-mongodb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack-hub"></a>A MongoDB előfeltételei Azure Stack hubhoz

- Két csatlakoztatott Azure Stack hub integrált rendszer (Azure Stack hub). Ez a központi telepítés nem működik a Azure Stack Development Kiton (ASDK). További információ az Azure Stack hub-ról: [Mi az Azure stack hub?](https://azure.microsoft.com/products/azure-stack/hub/)
  - Bérlői előfizetés az egyes Azure Stack hubokon. 
  - **Jegyezze fel minden egyes Azure Stack hub előfizetési AZONOSÍTÓját és Azure Resource Manager végpontját.**
- Egy Azure Active Directory (Azure AD) egyszerű szolgáltatásnév, amely jogosult a bérlői előfizetésre az egyes Azure Stack hubokon. Előfordulhat, hogy két egyszerű szolgáltatást kell létrehoznia, ha az Azure Stack hubok különböző Azure AD-bérlők között vannak telepítve. Ha meg szeretné tudni, hogyan hozhat létre egyszerű szolgáltatásnevet Azure Stack hubhoz, tekintse meg [az alkalmazás-identitás használata Azure stack hub-erőforrások eléréséhez](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals)című témakört.
  - **Jegyezze fel az egyes egyszerű szolgáltatások alkalmazás-AZONOSÍTÓját, az ügyfél titkos kulcsát és a bérlő nevét (xxxxx.onmicrosoft.com).**
- Ubuntu 16,04 szindikált minden Azure Stack hub piactéren. További információ a Marketplace Syndication szolgáltatásról: [Marketplace-elemek letöltése Azure stack hubhoz](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
- A helyi gépre telepített [Windows Docker](https://docs.docker.com/docker-for-windows/) .

## <a name="get-the-docker-image"></a>A Docker-rendszerkép beszerzése

Az egyes központi telepítésekhez tartozó Docker-rendszerképek megszüntetik a Azure PowerShell különböző verziói közötti függőségi problémákat.

1. Győződjön meg arról, hogy a Windows rendszerhez készült Docker Windows-tárolókat használ.
2. Futtassa a következő parancsot egy rendszergazda jogú parancssorban a Docker-tároló üzembe helyezési parancsfájlokkal való lekéréséhez.

    ```powershell  
    docker pull intelligentedge/mongodb-hadr:1.0.0
    ```

## <a name="deploy-the-clusters"></a>A fürtök üzembe helyezése

1. A tároló rendszerképének leállítása után indítsa el a rendszerképet.

    ```powershell  
    docker run -it intelligentedge/mongodb-hadr:1.0.0 powershell
    ```

2. A tároló elindítása után egy emelt szintű PowerShell-terminált kap a tárolóban. Módosítsa a címtárakat az üzembe helyezési parancsfájl eléréséhez.

    ```powershell  
    cd .\MongoHADRDemo\
    ```

3. Futtassa az üzemelő példányt. Adja meg a hitelesítő adatokat és az erőforrások nevét, ahol szükséges. HA az a Azure Stack központot jelöli, ahol a HA-fürtöt telepíteni fogja. DR arra a Azure Stack hubhoz utal, ahol a DR-fürtöt telepíti.

    ```powershell
    .\Deploy-AzureResourceGroup.ps1 `
    -AzureStackApplicationId_HA "applicationIDforHAServicePrincipal" `
    -AzureStackApplicationSercet_HA "clientSecretforHAServicePrincipal" `
    -AADTenantName_HA "hatenantname.onmicrosoft.com" `
    -AzureStackResourceGroup_HA "haresourcegroupname" `
    -AzureStackArmEndpoint_HA "https://management.haazurestack.com" `
    -AzureStackSubscriptionId_HA "haSubscriptionId" `
    -AzureStackApplicationId_DR "applicationIDforDRServicePrincipal" `
    -AzureStackApplicationSercet_DR "ClientSecretforDRServicePrincipal" `
    -AADTenantName_DR "drtenantname.onmicrosoft.com" `
    -AzureStackResourceGroup_DR "drresourcegroupname" `
    -AzureStackArmEndpoint_DR "https://management.drazurestack.com" `
    -AzureStackSubscriptionId_DR "drSubscriptionId"
    ```

4. Írja `Y` be a NuGet-szolgáltató telepítésének engedélyezését, amely a telepítendő "2018-03-01-Hybrid" modulok indítását fogja elindítani.

5. Először a HA-erőforrások lesznek telepítve. Figyelje a központi telepítést, és várjon, amíg befejeződik. Ha az üzenet arról tájékoztatja, hogy a HA a telepítés befejeződött, akkor az üzembe helyezett erőforrások megtekintéséhez tekintse meg a HA Azure Stack hub portálját.

6. Folytassa a DR-erőforrások üzembe helyezésével, és döntse el, hogy szeretné-e engedélyezni a DR Azure Stack hub Jump Box használatát a fürttel való kommunikációhoz.

7. Várjon, amíg befejeződik a DR erőforrás üzembe helyezése.

8. Miután a DR erőforrás üzembe helyezése befejeződött, lépjen ki a tárolóból.

  ```powershell
  exit
  ```

## <a name="next-steps"></a>További lépések

- Ha a DR Azure Stack hubhoz engedélyezte a Jump Box virtuális gépet, akkor SSH-n keresztül kapcsolódhat, és a Mongo parancssori felületének telepítésével használhatja a MongoDB-fürtöt. Ha többet szeretne megtudni a MongoDB-mel való interakcióról, tekintse meg [a Mongo-rendszerhéjat](https://docs.mongodb.com/manual/mongo/).
- A hibrid felhőalapú alkalmazásokkal kapcsolatos további információkért lásd: [hibrid felhőalapú megoldások.](https://aka.ms/azsdevtutorials)
- Módosítsa a minta kódját a [githubon](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
