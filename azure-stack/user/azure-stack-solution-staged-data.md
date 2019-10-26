---
title: Szakaszos adatelemzési megoldás üzembe helyezése Azure Stack | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe egy előkészített adatelemzési megoldást Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.topic: conceptual
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/24/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 4d93e16a46efad33c4392e40ca3a79e89a14795b
ms.sourcegitcommit: e6a738f674634e1d5dd4eb23b6c44b660ea2fe84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72891272"
---
# <a name="deploy-a-staged-data-analytics-solution-to-azure-stack"></a>Szakaszos adatelemzési megoldás üzembe helyezése Azure Stack

Ez a cikk bemutatja, hogyan helyezhet üzembe olyan megoldást, amely az elemzést igénylő adatok gyűjtéséhez szükséges a gyors döntések elvégzéséhez. Az adatgyűjtés gyakran az Internet-hozzáférés nélkül történik. Ha a kapcsolat létrejött, előfordulhat, hogy az adatelemzést erőforrás-igényesen kell elemezni, hogy további információkhoz jussanak.

Ebben a megoldásban a következőhöz hozzon létre egy mintavételi környezetet:

> [!div class="checklist"]
> - Hozza létre a nyers adattárolási blobot.
> - Hozzon létre egy új Azure Stack függvényt, amellyel a tiszta adatok áthelyezhetők a Azure Stack az Azure-ba.
> - Hozzon létre egy blob Storage által aktivált függvényt.
> - Hozzon létre egy Azure Stack Storage-fiókot, amely egy blobot és egy várólistát tartalmaz.
> - Üzenetsor által aktivált függvény létrehozása.
> - Tesztelje az üzenetsor által aktivált függvényt.

> [!Tip]  
> ![Hybrid-Pillars. png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack az Azure kiterjesztése. Azure Stack a felhő-számítástechnika rugalmasságát és innovációját a helyszíni környezetbe helyezi, így az egyetlen hibrid felhő, amely lehetővé teszi a hibrid alkalmazások bárhol történő létrehozását és üzembe helyezését.  
> 
> A [hibrid alkalmazásokkal kapcsolatos tervezési szempontok](azure-stack-edge-pattern-overview.md) a szoftverek minőségének (elhelyezés, skálázhatóság, rendelkezésre állás, rugalmasság, kezelhetőség és biztonság) pilléreit tekintik át a hibrid alkalmazások tervezéséhez, üzembe helyezéséhez és üzemeltetéséhez. A kialakítási szempontok segítik a hibrid alkalmazások kialakításának optimalizálását, ami minimalizálja az éles környezetekben felmerülő kihívásokat.

## <a name="architecture-for-staged-data-analytics"></a>Az előkészített adatelemzés architektúrája

![előkészített adatelemzés](media/azure-stack-solution-staged-data/image1.png)

## <a name="prerequisites-for-staged-data-analytics"></a>Az előkészített adatelemzés előfeltételei

  - Azure-előfizetés.
  - Egy Azure Active Directory (Azure AD) egyszerű szolgáltatásnév, amely jogosult a bérlői előfizetésre az Azure-ban és Azure Stack. Előfordulhat, hogy két egyszerű szolgáltatást kell létrehoznia, ha a Azure Stack eltérő HRE-bérlőt használ, mint az Azure-előfizetése. Ha meg szeretné tudni, hogyan hozhat létre egyszerű szolgáltatásnevet a Azure Stackhoz, nyissa meg az [egyszerű szolgáltatás létrehozása lehetőséget, hogy az alkalmazások hozzáférjenek Azure stack erőforrásokhoz](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).
      - **Jegyezze fel az egyes szolgáltatásnév alkalmazás-AZONOSÍTÓját, az ügyfél titkos kulcsát, az Azure AD-bérlő AZONOSÍTÓját és a bérlő nevét (xxxxx.onmicrosoft.com).**
  - Az adatelemzéshez meg kell adnia egy adatgyűjtési adatkészletet. A mintaadatok megadására szolgálnak.
  - A helyi gépre telepített [Windows Docker](https://docs.docker.com/docker-for-windows/) .

## <a name="get-the-docker-image"></a>A Docker-rendszerkép beszerzése

Az egyes központi telepítésekhez tartozó Docker-rendszerképek megszüntetik a Azure PowerShell különböző verziói közötti függőségi problémákat.
1.  Győződjön meg arról, hogy a Windows rendszerhez készült Docker Windows-tárolókat használ.
2.  Futtassa az alábbi parancsot egy rendszergazda jogú parancssorban a Docker-tároló üzembe helyezési parancsfájlokkal való lekéréséhez.

```
 docker pull intelligentedge/stageddatasolution:1.0.0
```

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

1.  A tároló rendszerképének leállítása után indítsa el a rendszerképet.

      ```powershell  
      docker run -it intelligentedge/stageddatasolution:1.0.0 powershell
      ```

2.  A tároló elindítása után a tárolóban egy emelt szintű PowerShell-terminál lesz megadva. Módosítsa a címtárakat az üzembe helyezési parancsfájl eléréséhez.

      ```powershell  
      cd .\SDDemo\
      ```

3.  Futtassa az üzemelő példányt. Adja meg a hitelesítő adatokat és az erőforrások nevét, ahol szükséges. HA arra a Azure Stackre hivatkozik, ahol a HA-fürtöt telepíti, és DR-t arra a Azure Stackra, ahová a DR-fürtöt telepíteni fogja.

      ```powershell
      .\DeploySolution-Azure-AzureStack.ps1 `
      -AzureApplicationId "applicationIDforAzureServicePrincipal" `
      -AzureApplicationSecret "clientSecretforServicePrincipal" `
      -AzureTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -AzureStackAADTenantName "azurestacktenant.onmicrosoft.com" `
      -AzureStackTenantARMEndpoint "https://management.haazurestack.com" `
      -AzureStackApplicationId "applicationIDforStackServicePrincipal" `
      -AzureStackApplicationSecret "ClientSecretforStackServicePrincipal" `
      -AzureStackTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -ResourcePrefix "aPrefixForResources"
      ```

1.  Ha a rendszer kéri, Adjon meg egy régiót az Azure üzembe helyezéséhez, és Application Insights.

2.  Írja be az "Y" értéket a NuGet-szolgáltató telepítésének engedélyezéséhez, amely elindítja az "2018-03-01-Hybrid" modulok telepítését, amelyek lehetővé teszik az Azure-ba és a Azure Stack-re való üzembe helyezést.

3.  Miután telepítette az erőforrásokat, tesztelje, hogy a rendszer létrehozza-e az Azure Stack és az Azure-hoz tartozó adatforrásokat.

    ```powershell  
      .\TDAGenerator.exe
    ```

4.  Az Azure-ba vagy Azure Stack-ra telepített webalkalmazásokra kattintva tekintheti meg a feldolgozott adatfeldolgozást.

### <a name="azure-web-app"></a>Azure Web App
 
![előkészített adatelemzési megoldás](media/azure-stack-solution-staged-data/image2.png)
 
### <a name="azure-stack-web-app"></a>Azure Stack webalkalmazás
 
![előkészített adatelemzési megoldás Azure Stack](media/azure-stack-solution-staged-data/image3.png)

## <a name="next-steps"></a>Következő lépések

  - További információ a hibrid felhőalapú alkalmazásokról: [hibrid felhőalapú megoldások.](https://aka.ms/azsdevtutorials)

  - Használja saját adatait, vagy módosítsa a kódot erre a mintára a [githubon](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
