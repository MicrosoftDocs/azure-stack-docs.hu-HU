---
title: Átmeneti adatok analytics megoldás üzembe helyezése az Azure Stackhez |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy előkészített adatelemzési megoldással az Azure Stackhez
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/20/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 7148e93977f50a7c64d79c422c43c6825b22b4a3
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308870"
---
# <a name="tutorial-deploy-a-staged-data-analytics-solution-to-azure-stack"></a>Oktatóanyag: Átmeneti adatok analytics megoldás üzembe helyezése az Azure Stackhez

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy megoldást, hogy gyors döntéseket lehet tenni a gyűjtemény folyamatos elemzés igénylő adatokat gyűjt. Általában adatgyűjteményben nincs Internet-hozzáféréssel rendelkező történik. Ha a kapcsolat létrejött, szükség lehet egy erőforrás-igényes elemzéseket végezhet az adatok további betekintést.

Ebben az oktatóanyagban egy mintául szolgáló környezetet hozunk létre:

> [!div class="checklist"]
> - A nyers adatok tárolási blob létrehozásához.
> - Hozzon létre egy új Azure Stack-függvény tiszta adatok áthelyezése az Azure Stack az Azure-bA.
> - Blobtároló által aktivált függvény létrehozása.
> - Hozzon létre egy Azure Stack tárfiókok a blob és üzenetsor tartalmazó.
> - Üzenetsor által aktivált függvény létrehozása.
> - Teszt az üzenetsor által aktivált függvény.

> [!Tip]  
> ![hibrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> A Microsoft Azure Stack az Azure bővítménye. Az Azure Stack számos lehetőséget kínál a hatékonyságával és innovációjával emeli a felhő-számítástechnika a helyszíni környezetben, és lehetővé teszi a egyetlen olyan hibrid felhő, amely lehetővé teszi, hogy létrehozása és üzembe helyezése bárhol hibrid alkalmazásokat.  
> 
> A tanulmány [hibrid alkalmazások kapcsolatos kialakítási szempontok](https://aka.ms/hybrid-cloud-applications-pillars) áttekinti a szoftverminőség alappillérei (elhelyezési, méretezhetőség, rendelkezésre állás, rugalmasság, kezelhetőségi és biztonsági) a kialakítása, üzembe helyezése és működtetése hibrid alkalmazások. A kialakítási szempontokat segítséget nyújt a hibrid alkalmazások kialakítását, minimálisra csökkentik az éles környezetben kihívások optimalizálása.

## <a name="architecture-for-staged-data-analytics"></a>A kétlépcsős data Analytics architektúrája

![a kétlépcsős adatelemzés](media/azure-stack-solution-staged-data/image1.png)

## <a name="prerequisites-for-staged-data-analytics"></a>A kétlépcsős data analytics előfeltételei

  - Azure-előfizetés.
  - Az Azure Active Directory (AAD) szolgáltatásnév, amely jogosult az Azure és az Azure Stack-bérlői előfizetéshez. Szükség lehet két szolgáltatásnevek létrehozása, ha az Azure Stack, mint az Azure-előfizetés egy másik AAD-bérlőt használja. További információk az Azure stack-beli szolgáltatásnév létrehozása [alkalmazások hozzáférés biztosítása az Azure Stack-erőforrások egyszerű szolgáltatások létrehozása](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).
      - **Jegyezze fel a minden szolgáltatásnév Alkalmazásazonosítót, titkos Ügyfélkód, az Azure AD-bérlő azonosítója és bérlő neve (xxxxx.onmicrosoft.com).**
  - Adatok gyűjteménye, adja meg a data-elemzéshez kell. Mintaadatok van megadva.
  - [A Windows docker](https://docs.docker.com/docker-for-windows/) telepítve a helyi gépen.

## <a name="get-the-docker-image"></a>A Docker-lemezkép beolvasása

Az egyes központi telepítések docker-rendszerképek Azure PowerShell-lel különböző verziói között függőségi hibák kiküszöbölése.
1.  Ügyeljen arra, hogy a Docker a Windows a Windows-tárolók használja-e.
2.  A Docker-tároló üzembe helyezési parancsfájlok beolvasásához rendszergazda jogú parancssorból futtassa a következő.

```
 docker pull intelligentedge/stageddatasolution:1.0.0
```

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

1.  Miután a tároló rendszerképét sikeresen kéri le, indítsa el a lemezképet.

      ```powershell  
      docker run -it intelligentedge/stageddatasolution:1.0.0 powershell
      ```

2.  Miután a tároló elindult, egyúttal egy emelt szintű PowerShell-terminált a tárolóban. Módosítsa a könyvtárakat az üzembe helyezési parancsfájl beolvasásához.

      ```powershell  
      cd .\SDDemo\
      ```

3.  A központi telepítéshez. Adja meg a hitelesítő adatok és erőforrások neveit, ahol szükséges. Magas rendelkezésre ÁLLÁS az Azure Stack, ahol a magas rendelkezésre ÁLLÁSÚ fürtben üzembe helyezett és Vészhelyreállítás az Azure stack, ahol a DR-fürtben üzembe helyezett hivatkozik.

      ```powershell
      .\DeploySolution-Azure-AzureStack.ps1 `
      -AzureApplicationId "applicationIDforAzureServicePrincipal" `
      -AzureApplicationSercet "clientSecretforServicePrincipal" `
      -AzureTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -AzureStackAADTenantName "azurestacktenant.onmicrosoft.com" `
      -AzureStackTenantARMEndpoint "https://management.haazurestack.com" `
      -AzureStackApplicationId "applicationIDforStackServicePrincipal" `
      -AzureStackApplicationSercet "ClientSecretforStackServicePrincipal" `
      -AzureStackTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -ResourcePrefix "aPrefixForResources"
      ```

1.  Ha a rendszer kéri; Adja meg az Azure-beli és az Application Insights egy régiót.

2.  Írja be az "Y", hogy a NuGet-szolgáltató telepíthető, amely elindít az API-profil "2018-03-01-hibrid" modulokat telepíteni kell az Azure és az Azure Stack való központi telepítésének engedélyezése.

3.  Az erőforrások telepítése után tesztelje, hogy az adatok Azure Stackben és az Azure jön.

    ```powershell  
      .\TDAGenerator.exe
    ```

4.  Tekintse meg az Azure-ban üzembe helyezett webalkalmazások a feldolgozott adatok.

### <a name="azure-web-app"></a>Azure Web App
 
![a kétlépcsős adatelemzési megoldással](media/azure-stack-solution-staged-data/image2.png)
 
### <a name="azure-stack-web-app"></a>Azure Stack Web App
 
![az Azure Stack előkészített adatelemzési megoldással](media/azure-stack-solution-staged-data/image3.png)

## <a name="next-steps"></a>További lépések

  - További tudnivalók a hibrid felhőbeli alkalmazások, lásd: [hibrid felhőalapú megoldásokkal.](https://aka.ms/azsdevtutorials)

  - A saját adatok használatával, vagy módosítsa a kódot, és ez a példa a [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
