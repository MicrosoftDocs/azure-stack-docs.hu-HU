---
title: Egy magas rendelkezésre állású MongoDB-megoldás üzembe helyezése Azure és az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy magas rendelkezésre állású MongoDB megoldás az Azure és az Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: solution
ms.date: 06/20/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: aa48e3b40afc841a26f15ce06870d002261c5932
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286837"
---
# <a name="deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack"></a>Az Azure és az Azure Stack egy magas rendelkezésre állású MongoDB-megoldás üzembe helyezése

Ez a cikk fog végigvezeti egy vész-helyreállítási webhelyként alapszintű magas rendelkezésre állású (HA) MongoDB fürtök automatikus telepítése két Azure Stack-környezet között. Mongodb-hez és a magas rendelkezésre állás kapcsolatos további információkért lásd: [beállítása Replikatagok](https://docs.mongodb.com/manual/core/replica-set-members/).

Ez a megoldás egy minta környezetet hozunk létre:

> [!div class="checklist"]
> - Központi telepítés előkészítését két Azure-t
> - Az Azure API-profillal rendelkező függőségi problémák minimalizálása érdekében a Docker használatával
> - A vész-helyreállítási webhelyként alapszintű magas rendelkezésre állású MongoDB-fürt üzembe helyezése


> [!Tip]  
> ![hibrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> A Microsoft Azure Stack az Azure bővítménye. Az Azure Stack számos lehetőséget kínál a hatékonyságával és innovációjával emeli a felhő-számítástechnika a helyszíni környezetben, az egyetlen olyan hibrid felhős, amely lehetővé teszi, hogy létrehozása és üzembe helyezése hibrid alkalmazások bárhol engedélyezése.  
> 
> A cikk [hibrid alkalmazások kapcsolatos kialakítási szempontok](azure-stack-edge-pattern-overview.md) kialakítása, üzembe helyezése és működtetése hibrid a szoftverminőség alappillérei (elhelyezési, méretezhetőség, rendelkezésre állás, rugalmasság, kezelhetőségi és biztonsági) felülvizsgálatai az alkalmazások. A kialakítási szempontokat segít az alkalmazás kialakítása, minimálisra csökkentik az éles környezetben kihívások optimalizálása.



## <a name="architecture-for-mongodb-with-azure-stack"></a>A mongodb-hez az Azure Stack-architektúra

![magas rendelkezésre állású mongodb-hez az Azure Stackben](media/azure-stack-solution-mongdb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack"></a>Előfeltételek az Azure Stack használatával a MongoDB-hez

  - Két csatlakoztatott Azure Stack integrált rendszerek (az Azure Stack), a központi telepítés nem működik az Azure Stack Development Kit (ASDKs). Azure Stack kapcsolatos további információkért lásd: [az Azure Stack?](https://azure.microsoft.com/overview/azure-stack/)
      - Az egyes Azure Stack bérlő előfizetés.    
      - **Jegyezze fel a minden egyes előfizetés-azonosító és az Azure Resource Manager-végpont esetében minden egyes Azure Stack.**
  - Egy Azure Active Directory (Azure AD) egyszerű szolgáltatást, amely minden egyes Azure Stack a bérlői előfizetéshez engedélyekkel rendelkezik. Szükség lehet két szolgáltatásnevek létrehozása, ha a Azure-t is települ a másik Azure AD-n bérlők számára. Ismerje meg, hogyan hozhat létre egy egyszerű szolgáltatást az Azure Stack, lásd: [alkalmazások hozzáférés biztosítása az Azure Stack-erőforrások egyszerű szolgáltatások létrehozása](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).    
      - **Jegyezze fel minden szolgáltatásnév Alkalmazásazonosítót, a titkos Ügyfélkód és a bérlő neve (xxxxx.onmicrosoft.com).**
  - Ubuntu 16.04 hírcsatorna-minden egyes Azure Stack piactéren. Marketplace-en tartalomtípus-gyűjtési kapcsolatos további információkért lásd: [Piactéri termékek letöltése az Azure-ból az Azure Stackhez](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
  - [A Windows docker](https://docs.docker.com/docker-for-windows/) telepítve a helyi gépen.

## <a name="get-the-docker-image"></a>A Docker-lemezkép beolvasása

Az egyes központi telepítések docker-rendszerképek Azure PowerShell-lel különböző verziói között függőségi hibák kiküszöbölése.
1.  Ügyeljen arra, hogy a Docker a Windows a Windows-tárolók használja-e.
2.  A Docker-tároló üzembe helyezési parancsfájlok beolvasásához rendszergazda jogú parancssorból futtassa a következő.
```powershell  
docker pull intelligentedge/mongodb-hadr:1.0.0
```

## <a name="deploy-the-clusters"></a>A fürtök üzembe helyezése

1.  Miután a tároló rendszerképét sikeresen kéri le, indítsa el a lemezképet. \

    ```powershell  
    docker run -it intelligentedge/mongodb-hadr:1.0.0 powershell
    ```

2.  Miután a tároló elindult, egyúttal egy emelt szintű PowerShell-terminált a tárolóban. Módosítsa a könyvtárakat az üzembe helyezési parancsfájl beolvasásához.

    ```powershell  
    cd .\MongoHADRDemo\
    ```

3.  A központi telepítéshez. Adja meg a hitelesítő adatok és erőforrások neveit, ahol szükséges. Magas rendelkezésre ÁLLÁS az Azure Stack, ahol a magas rendelkezésre ÁLLÁSÚ fürtben üzembe helyezett és Vészhelyreállítás az Azure stack, ahol a DR-fürtben üzembe helyezett hivatkozik.

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

4.  Típus `Y` , hogy a NuGet-szolgáltató telepíthető, amely a telepítendő API profil "2018-03-01-hibrid" modulok elindít.

5.  A magas rendelkezésre ÁLLÁSÚ erőforrások először lesz üzembe helyezve. A központi telepítésének figyelése, és várjon, amíg annak végrehajtása befejeződik. Ha már van az üzenetet, amely megállapítja, hogy a magas rendelkezésre ÁLLÁSÚ telepítés befejeződött, ellenőrizheti a magas rendelkezésre ÁLLÁS Azure Stack a portálon az üzembe helyezett erőforrások megtekintéséhez. 

6.  DR-erőforrások a telepítés folytatásához, és döntse el, ha szeretné-e a jump boxon a Vészhelyreállítás Azure Stack használatával kommunikáljanak a fürttel a engedélyezése.

7.  Várjon, amíg a DR erőforrás üzembe helyezés befejeződik.

8.  DR-erőforrások üzembe helyezésének befejezése után lépjen ki a tárolót.

  ```powershell
  exit
  ```

## <a name="next-steps"></a>További lépések

  - Ha engedélyezte a virtuális gép a Vészhelyreállítás Azure Stack a jump boxon, SSH-n keresztül csatlakozik, és kezelheti a MongoDB-fürttel a mongo CLI telepítésével. Használatához a MongoDB-vel kapcsolatos további információkért lásd: [a mongo Shell](https://docs.mongodb.com/manual/mongo/).

  - További tudnivalók a hibrid felhőbeli alkalmazások, lásd: [hibrid felhőalapú megoldásokkal.](https://aka.ms/azsdevtutorials)

  - Módosítsa a kódot, és ez a minta [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).