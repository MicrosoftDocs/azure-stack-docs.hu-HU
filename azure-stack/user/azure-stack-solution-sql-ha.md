---
title: Egy SQL Server 2016-os rendelkezésre állási csoport üzembe helyezése az Azure és az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy SQL Server 2016-os rendelkezésre állási csoport Azure és az Azure Stackben
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
ms.date: 06/20/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 39b0078345c16b7931f41cd2394476f8258d92dd
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308848"
---
# <a name="tutorial-deploy-a-sql-server-2016-availability-group-to-azure-and-azure-stack"></a>Oktatóanyag: Egy SQL Server 2016-os rendelkezésre állási csoportot az Azure és az Azure Stack üzembe helyezése

Ez a cikk fog végigvezeti egy automatikus központi telepítési a alapszintű magas rendelkezésre állású (HA) SQL Server 2016 Enterprise fürtök egy aszinkron vész-helyreállítási webhelyként két Azure Stack-környezet között. Az SQL Server 2016 és a magas rendelkezésre állás kapcsolatos további információkért lásd: [Always On rendelkezésre állási csoportok: a magas rendelkezésre állású és vész-helyreállítási megoldás](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2016).

Ebben az oktatóanyagban egy mintául szolgáló környezet fog létrehozni:

> [!div class="checklist"]
> - Központi telepítés előkészítését két Azure-t
> - Az Azure API-profillal rendelkező függőségi problémák minimalizálása érdekében a Docker használatával
> - A vész-helyreállítási webhelyként alapszintű magas rendelkezésre állású SQL Server 2016 Enterprise-fürt üzembe helyezése

> [!Tip]  
> ![hibrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> A Microsoft Azure Stack az Azure bővítménye. Az Azure Stack számos lehetőséget kínál a hatékonyságával és innovációjával emeli a felhő-számítástechnika a helyszíni környezetben, és lehetővé teszi a egyetlen olyan hibrid felhő, amely lehetővé teszi, hogy létrehozása és üzembe helyezése bárhol hibrid alkalmazásokat.  
> 
> A tanulmány [hibrid alkalmazások kapcsolatos kialakítási szempontok](https://aka.ms/hybrid-cloud-applications-pillars) áttekinti a szoftverminőség alappillérei (elhelyezési, méretezhetőség, rendelkezésre állás, rugalmasság, kezelhetőségi és biztonsági) a kialakítása, üzembe helyezése és működtetése hibrid alkalmazások. A kialakítási szempontokat segítséget nyújt a hibrid alkalmazások kialakítását, minimálisra csökkentik az éles környezetben kihívások optimalizálása.

## <a name="architecture-for-sql-server-2016"></a>Az SQL Server 2016 architektúra

![Az SQL Server 2016 SQL magas rendelkezésre ÁLLÁS az Azure Stack](media/azure-stack-solution-sql-ha/image1.png)

## <a name="prerequisites-for-sql-server-2016"></a>Az SQL Server 2016 előfeltételei

  - Két csatlakoztatott Azure Stack integrált rendszerek (az Azure Stack), a központi telepítés nem működik az Azure Stack Development Kit (ASDKs). Azure Stack kapcsolatos további információkért lásd: [az Azure Stack?](https://azure.microsoft.com/overview/azure-stack/).
  - Az egyes Azure Stack bérlő előfizetés.    
      - **Jegyezze fel a minden egyes előfizetés-azonosító és az Azure Resource Manager-végpont esetében minden egyes Azure Stack.**
  - Egy Azure Active Directory (Azure AD) egyszerű szolgáltatást, amely minden egyes Azure Stack a bérlői előfizetéshez engedélyekkel rendelkezik. Szükség lehet két szolgáltatásnevek létrehozása, ha a Azure-t is települ a másik Azure AD-n bérlők számára. Ismerje meg, hogyan hozhat létre egy egyszerű szolgáltatást az Azure Stack, lásd: [alkalmazások hozzáférés biztosítása az Azure Stack-erőforrások egyszerű szolgáltatások létrehozása](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).
      - **Jegyezze fel minden szolgáltatásnév Alkalmazásazonosítót, a titkos Ügyfélkód és a bérlő neve (xxxxx.onmicrosoft.com).**
  - SQL Server 2016 Enterprise hírcsatorna-minden egyes Azure Stack piactéren. Marketplace-en tartalomtípus-gyűjtési kapcsolatos további információkért lásd: [Piactéri termékek letöltése az Azure-ból az Azure Stackhez](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
    **Győződjön meg arról, hogy a szervezet rendelkezik-e a megfelelő SQL-licenceket.**
  - [A Windows docker](https://docs.docker.com/docker-for-windows/) telepítve a helyi gépen.

## <a name="get-the-docker-image"></a>A Docker-lemezkép beolvasása

Az egyes központi telepítések docker-rendszerképek Azure PowerShell-lel különböző verziói között függőségi hibák kiküszöbölése.

1.  Ügyeljen arra, hogy a Docker a Windows a Windows-tárolók használja-e.
2.  A Docker-tároló üzembe helyezési parancsfájlok beolvasásához rendszergazda jogú parancssorból futtassa a következő.

```powershell  
 docker pull intelligentedge/sqlserver2016-hadr:1.0.0
```

## <a name="deploy-the-availability-group"></a>A rendelkezésre állási csoport központi telepítése

1.  Miután a tároló rendszerképét sikeresen kéri le, indítsa el a lemezképet.

      ```powershell  
      docker run -it intelligentedge/sqlserver2016-hadr:1.0.0 powershell
      ```

2.  Miután a tároló elindult, egyúttal egy emelt szintű PowerShell-terminált a tárolóban. Módosítsa a könyvtárakat az üzembe helyezési parancsfájl beolvasásához.

      ```powershell  
      cd .\SQLHADRDemo\
      ```

3.  A központi telepítéshez. Adja meg a hitelesítő adatok és erőforrások neveit, ahol szükséges. Magas rendelkezésre ÁLLÁS az Azure Stack, ahol a magas rendelkezésre ÁLLÁSÚ fürtben üzembe helyezett és Vészhelyreállítás az Azure stack, ahol a DR-fürtben üzembe helyezett hivatkozik.

      ```powershell
      > .\Deploy-AzureResourceGroup.ps1 `
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

5.  Várjon, amíg az erőforrás üzembe helyezés befejeződik.

6.  DR-erőforrások üzembe helyezésének befejezése után lépjen ki a tárolót.

      ```powershell
      exit
      ```

7.  Vizsgálja meg a központi telepítés minden egyes Azure Stack portálon az erőforrások megtekintésével. Csatlakozhat egy SQL-példánya a magas rendelkezésre ÁLLÁSÚ környezetben, és a rendelkezésre állási csoportot az SQL Server Management Studio (SSMS) keresztül vizsgálatával.

![SQL Server 2016 SQL HA](media/azure-stack-solution-sql-ha/image2.png)

## <a name="next-steps"></a>További lépések

  - SQL Server Management Studio segítségével manuálisan átadja a feladatokat a fürt, lásd: [egy egy Always On rendelkezésre állási csoport (SQL Server) kényszerített manuális feladatátvétel elvégzése](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-2017)
  - További tudnivalók a hibrid felhőbeli alkalmazások, lásd: [hibrid felhőalapú megoldásokkal.](https://aka.ms/azsdevtutorials)
  - A saját adatok használatával, vagy módosítsa a kódot, és ez a példa a [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
