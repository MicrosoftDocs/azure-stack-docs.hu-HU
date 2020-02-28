---
title: Magasan elérhető MongoDB-megoldás üzembe helyezése az Azure-ban és Azure Stack hub
description: Ismerje meg, hogyan helyezhet üzembe egy magasan elérhető MongoDB-megoldást az Azure-ban és Azure Stack hub-ban
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: d9a0447938c44eec90d1d5cb2898e950cddbd7bf
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701377"
---
# <a name="deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack-hub"></a>Magasan elérhető MongoDB-megoldás üzembe helyezése az Azure-ban és Azure Stack hub

Ez a cikk végigvezeti egy olyan alapszintű magas rendelkezésre állású (HA) MongoDB-fürt automatikus üzembe helyezésén, amely egy vész-helyreállítási (DR) hellyel rendelkezik két Azure Stack hub-környezetben. További információ a MongoDB és a magas rendelkezésre állásról: [replikakészlet tagjai](https://docs.mongodb.com/manual/core/replica-set-members/).

Ebben a megoldásban a következőhöz hozzon létre egy mintavételi környezetet:

> [!div class="checklist"]
> - Központi telepítés összehangolása két Azure Stack hubok között
> - Az Azure API-profilok függőségi problémáinak csökkentése a Docker használatával
> - Alapszintű, magasan elérhető MongoDB-fürt üzembe helyezése vész-helyreállítási hellyel


> [!Tip]  
> ![Hybrid-Pillars. png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack hub az Azure kiterjesztése. Azure Stack hub a felhő-számítástechnika rugalmasságát és innovációját a helyszíni környezetbe helyezi, így az egyetlen hibrid felhő, amely lehetővé teszi a hibrid alkalmazások bárhol történő létrehozását és üzembe helyezését.  
> 
> A [hibrid alkalmazásokkal kapcsolatos tervezési szempontok](overview-app-design-considerations.md) a szoftverek minőségének (elhelyezés, skálázhatóság, rendelkezésre állás, rugalmasság, kezelhetőség és biztonság) pilléreit tekintik át a hibrid alkalmazások tervezéséhez, üzembe helyezéséhez és üzemeltetéséhez. A kialakítási szempontok segítik a hibrid alkalmazások kialakításának optimalizálását, ami minimalizálja az éles környezetekben felmerülő kihívásokat.



## <a name="architecture-for-mongodb-with-azure-stack-hub"></a>A MongoDB architektúrája Azure Stack hub-vel

![magasan elérhető MongoDB Azure Stack hub-ban](media/solution-deployment-guide-mongodb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack-hub"></a>A MongoDB előfeltételei Azure Stack hubhoz

  - Két csatlakoztatott Azure Stack hub integrált rendszer (Azure Stack hub), ez a telepítés nem működik Azure Stack fejlesztői csomagokon (ASDKs). További információ az Azure Stack hub-ról: [Mi az Azure stack hub?](https://azure.microsoft.com/overview/azure-stack/)
      - Bérlői előfizetés az egyes Azure Stack hubokon.    
      - **Jegyezze fel minden egyes Azure Stack hub előfizetési AZONOSÍTÓját és Azure Resource Manager végpontját.**
  - Egy Azure Active Directory (Azure AD) egyszerű szolgáltatásnév, amely jogosult a bérlői előfizetésre az egyes Azure Stack hubokon. Előfordulhat, hogy két egyszerű szolgáltatást kell létrehoznia, ha az Azure Stack hubok különböző Azure AD-bérlők között vannak telepítve. Ha meg szeretné tudni, hogyan hozhat létre egyszerű szolgáltatásnevet Azure Stack hubhoz, tekintse meg az [egyszerű szolgáltatások létrehozása az Azure stack hub-erőforrásokhoz való hozzáférést biztosító alkalmazások létrehozásával](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals)foglalkozó témakört.    
      - **Jegyezze fel az egyes egyszerű szolgáltatások alkalmazás-AZONOSÍTÓját, az ügyfél titkos kulcsát és a bérlő nevét (xxxxx.onmicrosoft.com).**
  - Ubuntu 16,04 szindikált minden Azure Stack hub piactéren. A Marketplace Syndication szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Marketplace-elemek letöltése az Azure-ból Azure stack hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item)-ba című témakört.
  - A helyi gépre telepített [Windows Docker](https://docs.docker.com/docker-for-windows/) .

## <a name="get-the-docker-image"></a>A Docker-rendszerkép beszerzése

Az egyes központi telepítésekhez tartozó Docker-rendszerképek megszüntetik a Azure PowerShell különböző verziói közötti függőségi problémákat.
1.  Győződjön meg arról, hogy a Windows rendszerhez készült Docker Windows-tárolókat használ.
2.  Futtassa az alábbi parancsot egy rendszergazda jogú parancssorban a Docker-tároló üzembe helyezési parancsfájlokkal való lekéréséhez.
```powershell  
docker pull intelligentedge/mongodb-hadr:1.0.0
```

## <a name="deploy-the-clusters"></a>A fürtök üzembe helyezése

1.  A tároló rendszerképének leállítása után indítsa el a rendszerképet. \

    ```powershell  
    docker run -it intelligentedge/mongodb-hadr:1.0.0 powershell
    ```

2.  A tároló elindítása után a tárolóban egy emelt szintű PowerShell-terminál lesz megadva. Módosítsa a címtárakat az üzembe helyezési parancsfájl eléréséhez.

    ```powershell  
    cd .\MongoHADRDemo\
    ```

3.  Futtassa az üzemelő példányt. Adja meg a hitelesítő adatokat és az erőforrások nevét, ahol szükséges. HA arra a Azure Stack központra hivatkozik, ahol a HA-fürtöt telepíti, és DR arra a Azure Stack hubhoz, ahol a DR-fürtöt telepíteni fogja.

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

4.  Írja be `Y` a NuGet-szolgáltató telepítésének engedélyezéséhez, amely elindítja az "2018-03-01-Hybrid" API-profilt, amelyet telepíteni kell.

5.  Először a HA-erőforrások lesznek telepítve. Figyelje a központi telepítést, és várjon, amíg befejeződik. Ha az üzenet arról tájékoztatja, hogy a HA a telepítés befejeződött, akkor az üzembe helyezett erőforrások megtekintéséhez tekintse meg a HA Azure Stack hub portálját. 

6.  Folytassa a DR-erőforrások üzembe helyezésével, és döntse el, hogy szeretné-e engedélyezni a DR Azure Stack hub Jump Box használatát a fürttel való kommunikációhoz.

7.  Várjon, amíg a DR erőforrás üzembe helyezése befejeződött.

8.  Miután a DR erőforrás üzembe helyezése befejeződött, lépjen ki a tárolóból.

  ```powershell
  exit
  ```

## <a name="next-steps"></a>Következő lépések

  - Ha a DR Azure Stack hubhoz engedélyezte a Jump Box virtuális gépet, akkor SSH-n keresztül kapcsolódhat, és a Mongo parancssori felületének telepítésével használhatja a MongoDB-fürtöt. Ha többet szeretne megtudni a MongoDB-mel való interakcióról, tekintse meg [a Mongo-rendszerhéjat](https://docs.mongodb.com/manual/mongo/).

  - További információ a hibrid felhőalapú alkalmazásokról: [hibrid felhőalapú megoldások.](https://aka.ms/azsdevtutorials)

  - Módosítsa a minta kódját a [githubon](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
