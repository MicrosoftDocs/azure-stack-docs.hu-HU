---
title: Magasan elérhető MongoDB-megoldás üzembe helyezése az Azure-ban és a Azure Stack | Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy magasan elérhető MongoDB-megoldást az Azure-ban és Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: e31a71098cda4f769fbce4e55bec6ec72a5a651b
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277766"
---
# <a name="deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack"></a>Magasan elérhető MongoDB-megoldás üzembe helyezése az Azure-ban és Azure Stack

Ez a cikk végigvezeti egy olyan alapszintű magas rendelkezésre állású (HA) MongoDB-fürt automatikus üzembe helyezésén, amely egy vész-helyreállítási (DR) hellyel rendelkezik két Azure Stack környezeten belül. További információ a MongoDB és a magas rendelkezésre állásról: [replikakészlet tagjai](https://docs.mongodb.com/manual/core/replica-set-members/).

Ebben a megoldásban a következőhöz hozzon létre egy mintavételi környezetet:

> [!div class="checklist"]
> - Üzembe helyezés összehangolása két Azure-veremben
> - Az Azure API-profilok függőségi problémáinak csökkentése a Docker használatával
> - Alapszintű, magasan elérhető MongoDB-fürt üzembe helyezése vész-helyreállítási hellyel


> [!Tip]  
> @no__t -0hybrid-Pillars. png @ no__t-1  
> Microsoft Azure Stack az Azure kiterjesztése. Azure Stack a felhő-számítástechnika rugalmasságát és innovációját a helyszíni környezetbe helyezi, így az egyetlen hibrid felhő, amely lehetővé teszi a hibrid alkalmazások bárhol történő létrehozását és üzembe helyezését.  
> 
> A [hibrid alkalmazásokkal kapcsolatos tervezési szempontok](azure-stack-edge-pattern-overview.md) a szoftverek minőségének (elhelyezés, skálázhatóság, rendelkezésre állás, rugalmasság, kezelhetőség és biztonság) pilléreit tekintik át a hibrid alkalmazások tervezéséhez, üzembe helyezéséhez és üzemeltetéséhez. A kialakítási szempontok segítik a hibrid alkalmazások kialakításának optimalizálását, ami minimalizálja az éles környezetekben felmerülő kihívásokat.



## <a name="architecture-for-mongodb-with-azure-stack"></a>A MongoDB architektúrája Azure Stack

![magasan elérhető MongoDB a Azure Stack](media/azure-stack-solution-mongdb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack"></a>A MongoDB előfeltételei a Azure Stack

  - Két csatlakoztatott Azure Stack integrált rendszer (Azure Stack), ez a telepítés nem működik Azure Stack fejlesztői csomagokon (ASDKs). További információ a Azure Stackről: [Mi az Azure stack?](https://azure.microsoft.com/overview/azure-stack/)
      - Bérlői előfizetés az egyes Azure Stackeken.    
      - **Jegyezze fel mindegyik előfizetés-azonosítót és a Azure Resource Manager végpontot az egyes Azure Stackokhoz.**
  - Egy Azure Active Directory (Azure AD) egyszerű szolgáltatásnév, amely minden egyes Azure Stack a bérlői előfizetéshez rendelkezik engedéllyel. Előfordulhat, hogy két egyszerű szolgáltatást kell létrehoznia, ha az Azure stackek különböző Azure AD-bérlők között vannak telepítve. Ha meg szeretné tudni, hogyan hozhat létre egyszerű szolgáltatásnevet a Azure Stackhoz, tekintse meg az [egyszerű szolgáltatások létrehozása az alkalmazások Azure stack erőforrásokhoz való hozzáférésének biztosítása érdekében](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals)című témakört.    
      - **Jegyezze fel az egyes egyszerű szolgáltatások alkalmazás-AZONOSÍTÓját, az ügyfél titkos kulcsát és a bérlő nevét (xxxxx.onmicrosoft.com).**
  - Ubuntu 16,04 szindikált minden Azure Stack Marketplace-en. További információ a Marketplace Syndication [szolgáltatásról: Marketplace-elemek letöltése az Azure-ból Azure stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
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

3.  Futtassa az üzemelő példányt. Adja meg a hitelesítő adatokat és az erőforrások nevét, ahol szükséges. HA arra a Azure Stackre hivatkozik, ahol a HA-fürtöt telepíti, és DR-t arra a Azure Stackra, ahová a DR-fürtöt telepíteni fogja.

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

4.  Írja be a `Y` értéket a NuGet-szolgáltató telepítésének engedélyezéséhez, amely elindítja a telepíteni kívánt "2018-03-01-Hybrid" modulok API-profilját.

5.  Először a HA-erőforrások lesznek telepítve. Figyelje a központi telepítést, és várjon, amíg befejeződik. Ha az üzenet arról tájékoztatja, hogy a HA a telepítés befejeződött, a Azure Stack portálján megtekintheti a telepített erőforrásokat. 

6.  Folytassa a DR erőforrások üzembe helyezésével, és döntse el, hogy szeretné-e engedélyezni a DR Azure Stack Jump Box használatát a fürttel való kommunikációhoz.

7.  Várjon, amíg a DR erőforrás üzembe helyezése befejeződött.

8.  Miután a DR erőforrás üzembe helyezése befejeződött, lépjen ki a tárolóból.

  ```powershell
  exit
  ```

## <a name="next-steps"></a>Következő lépések

  - Ha a DR Azure Stackban engedélyezte a Jump Box virtuális gépet, az SSH-n keresztül kapcsolódhat a MongoDB-fürthöz, és a Mongo parancssori felületének telepítésével is folytathatja a munkát. Ha többet szeretne megtudni a MongoDB-mel való interakcióról, tekintse meg [a Mongo-rendszerhéjat](https://docs.mongodb.com/manual/mongo/).

  - További információ a hibrid felhőalapú alkalmazásokról: [hibrid felhőalapú megoldások.](https://aka.ms/azsdevtutorials)

  - Módosítsa a minta kódját a [githubon](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
