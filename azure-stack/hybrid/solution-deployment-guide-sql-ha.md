---
title: SQL Server 2016 rendelkezésre állási csoport üzembe helyezése az Azure-ban és Azure Stack hub-ban
description: Megtudhatja, hogyan helyezhet üzembe egy SQL Server 2016 rendelkezésre állási csoportot az Azure-ban és Azure Stack hub-ban.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 37904a1b47f412e8f03d9f890d9a8c047851c2f3
ms.sourcegitcommit: b185ab34c4c799892948536dd6d1d1b2fc31174e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82150336"
---
# <a name="deploy-a-sql-server-2016-availability-group-to-azure-and-azure-stack-hub"></a>SQL Server 2016 rendelkezésre állási csoport üzembe helyezése az Azure-ban és Azure Stack hub-ban

Ez a cikk végigvezeti egy olyan alapszintű magas rendelkezésre állású (HA) SQL Server 2016 Enterprise-fürt automatikus üzembe helyezésén, amely egy aszinkron vész-helyreállítási (DR) hellyel rendelkezik két Azure Stack hub-környezetben. Ha többet szeretne megtudni a SQL Server 2016 és a magas rendelkezésre állásról, tekintse meg az [Always On rendelkezésre állási csoportok: magas rendelkezésre állású és vész-helyreállítási megoldást](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2016).

Ebben a megoldásban egy példaként szolgáló környezetet fog kiépíteni a következőkre:

> [!div class="checklist"]
> - Üzembe helyezés koordinálása két Azure Stack hub között.
> - A Docker használatával csökkentheti a függőségi problémákat az Azure API-profilokkal.
> - Helyezzen üzembe egy alapszintű, magasan elérhető SQL Server 2016 Enterprise-fürtöt vész-helyreállítási hellyel.

> [!Tip]  
> ![Hybrid-Pillars. png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack hub az Azure kiterjesztése. Azure Stack hub a felhő-számítástechnika rugalmasságát és innovációját a helyszíni környezetbe helyezi, így az egyetlen hibrid felhő, amely lehetővé teszi a hibrid alkalmazások bárhol történő létrehozását és üzembe helyezését.  
> 
> A [hibrid alkalmazások kialakításával kapcsolatos megfontolások](overview-app-design-considerations.md) a szoftverek minőségének (elhelyezés, skálázhatóság, rendelkezésre állás, rugalmasság, kezelhetőség és biztonság) pilléreit tekintik át hibrid alkalmazások tervezéséhez, üzembe helyezéséhez és üzemeltetéséhez. A kialakítási szempontok segítik a hibrid alkalmazások kialakításának optimalizálását, ami minimalizálja az éles környezetekben felmerülő kihívásokat.

## <a name="architecture-for-sql-server-2016"></a>SQL Server 2016 architektúrája

![SQL Server 2016 SQL HA Azure Stack hub](media/solution-deployment-guide-sql-ha/image1.png)

## <a name="prerequisites-for-sql-server-2016"></a>Az SQL Server 2016 előfeltételei

- Két csatlakoztatott Azure Stack hub integrált rendszer (Azure Stack hub). Ez a központi telepítés nem működik a Azure Stack Development Kiton (ASDK). Ha többet szeretne megtudni az Azure Stack hub-ról, tekintse meg a [Azure stack áttekintését](https://azure.microsoft.com/overview/azure-stack/).
- Bérlői előfizetés az egyes Azure Stack hubokon.
  - **Jegyezze fel minden egyes Azure Stack hub előfizetési AZONOSÍTÓját és Azure Resource Manager végpontját.**
- Egy Azure Active Directory (Azure AD) egyszerű szolgáltatásnév, amely jogosult a bérlői előfizetésre az egyes Azure Stack hubokon. Előfordulhat, hogy két egyszerű szolgáltatást kell létrehoznia, ha az Azure Stack hubok különböző Azure AD-bérlők között vannak telepítve. Ha meg szeretné tudni, hogyan hozhat létre egyszerű szolgáltatásnevet Azure Stack hubhoz, tekintse meg az [egyszerű szolgáltatás létrehozása az alkalmazások Azure stack hub-erőforrásokhoz való hozzáférésének biztosítása érdekében](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals)című témakört.
  - **Jegyezze fel az egyes egyszerű szolgáltatások alkalmazás-AZONOSÍTÓját, az ügyfél titkos kulcsát és a bérlő nevét (xxxxx.onmicrosoft.com).**
- SQL Server 2016 Enterprise konzorciális az egyes Azure Stack hub-piactéren. További információ a Marketplace Syndication szolgáltatásról: [Marketplace-elemek letöltése Azure stack hubhoz](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
    **Győződjön meg arról, hogy a szervezet rendelkezik a megfelelő SQL-licenccel.**
- A helyi gépre telepített [Windows Docker](https://docs.docker.com/docker-for-windows/) .

## <a name="get-the-docker-image"></a>A Docker-rendszerkép beszerzése

Az egyes központi telepítésekhez tartozó Docker-rendszerképek megszüntetik a Azure PowerShell különböző verziói közötti függőségi problémákat.

1. Győződjön meg arról, hogy a Windows rendszerhez készült Docker Windows-tárolókat használ.
2. Futtassa a következő parancsfájlt egy rendszergazda jogú parancssorban a Docker-tároló üzembe helyezési parancsfájlokkal való lekéréséhez.

    ```powershell  
    docker pull intelligentedge/sqlserver2016-hadr:1.0.0
    ```

## <a name="deploy-the-availability-group"></a>A rendelkezésre állási csoport üzembe helyezése

1. A tároló rendszerképének leállítása után indítsa el a rendszerképet.

      ```powershell  
      docker run -it intelligentedge/sqlserver2016-hadr:1.0.0 powershell
      ```

2. A tároló elindítása után egy emelt szintű PowerShell-terminált kap a tárolóban. Módosítsa a címtárakat az üzembe helyezési parancsfájl eléréséhez.

      ```powershell  
      cd .\SQLHADRDemo\
      ```

3. Futtassa az üzemelő példányt. Adja meg a hitelesítő adatokat és az erőforrások nevét, ahol szükséges. HA az a Azure Stack központot jelöli, ahol a HA-fürtöt telepíteni fogja. DR arra a Azure Stack hubhoz utal, ahol a DR-fürtöt telepíti.

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

4. Írja `Y` be a NuGet-szolgáltató telepítésének engedélyezését, amely a telepítendő "2018-03-01-Hybrid" modulok indítását fogja elindítani.

5. Várjon, amíg az erőforrás üzembe helyezése befejeződik.

6. Miután a DR erőforrás üzembe helyezése befejeződött, lépjen ki a tárolóból.

      ```powershell
      exit
      ```

7. Vizsgálja meg az üzemelő példányt az egyes Azure Stack hub-portálon található erőforrások megtekintésével. Kapcsolódjon az egyik SQL-példányhoz a HA-környezetben, és vizsgálja meg a rendelkezésre állási csoportot SQL Server Management Studio (SSMS) használatával.

    ![SQL Server 2016 SQL HA](media/solution-deployment-guide-sql-ha/image2.png)

## <a name="next-steps"></a>További lépések

- SQL Server Management Studio használatával manuálisan hajthatja végre a feladatátvételt a fürtön. Lásd: az [Always On rendelkezésre állási csoport kényszerített manuális feladatátvételének végrehajtása (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-2017)
- További információ a hibrid felhőalapú alkalmazásokról. Tekintse meg a [hibrid felhőalapú megoldásokat.](https://aka.ms/azsdevtutorials)
- Használja saját adatait, vagy módosítsa a kódot erre a mintára a [githubon](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
