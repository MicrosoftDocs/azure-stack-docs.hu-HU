---
title: SQL Server 2016 rendelkezésre állási csoport üzembe helyezése az Azure-ban és Azure Stack hub-ban
description: Megtudhatja, hogyan helyezhet üzembe egy SQL Server 2016 rendelkezésre állási csoportot az Azure-ban és Azure Stack hub-ban
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: e0fe07495017d6bd56beab78a37f5e0708197087
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73639013"
---
# <a name="deploy-a-sql-server-2016-availability-group-to-azure-and-azure-stack-hub"></a>SQL Server 2016 rendelkezésre állási csoport üzembe helyezése az Azure-ban és Azure Stack hub-ban

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek*

Ez a cikk végigvezeti egy olyan alapszintű magas rendelkezésre állású (HA) SQL Server 2016 Enterprise-fürt automatikus üzembe helyezésén, amely egy aszinkron vész-helyreállítási (DR) hellyel rendelkezik két Azure Stack hub-környezetben. Ha többet szeretne megtudni a SQL Server 2016 és a magas rendelkezésre állásról, tekintse meg az [Always On rendelkezésre állási csoportok: magas rendelkezésre állású és vész-helyreállítási megoldást](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2016).

Ebben a megoldásban egy példaként szolgáló környezetet fog létrehozni a következőhöz:

> [!div class="checklist"]
> - Központi telepítés összehangolása két Azure Stack hubok között
> - Az Azure API-profilok függőségi problémáinak csökkentése a Docker használatával
> - Alapszintű, magasan elérhető SQL Server 2016 Enterprise-fürt üzembe helyezése vész-helyreállítási hellyel

> [!Tip]  
> ![Hybrid-Pillars. png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack hub az Azure kiterjesztése. Azure Stack hub a felhő-számítástechnika rugalmasságát és innovációját a helyszíni környezetbe helyezi, így az egyetlen hibrid felhő, amely lehetővé teszi a hibrid alkalmazások bárhol történő létrehozását és üzembe helyezését.  
> 
> A [hibrid alkalmazásokkal kapcsolatos tervezési szempontok](overview-app-design-considerations.md) a szoftverek minőségének (elhelyezés, skálázhatóság, rendelkezésre állás, rugalmasság, kezelhetőség és biztonság) pilléreit tekintik át a hibrid alkalmazások tervezéséhez, üzembe helyezéséhez és üzemeltetéséhez. A kialakítási szempontok segítik a hibrid alkalmazások kialakításának optimalizálását, ami minimalizálja az éles környezetekben felmerülő kihívásokat.

## <a name="architecture-for-sql-server-2016"></a>SQL Server 2016 architektúrája

![SQL Server 2016 SQL HA Azure Stack hub](media/solution-deployment-guide-sql-ha/image1.png)

## <a name="prerequisites-for-sql-server-2016"></a>Az SQL Server 2016 előfeltételei

  - Két csatlakoztatott Azure Stack hub integrált rendszer (Azure Stack hub), ez a központi telepítés nem működik Azure Stack hub fejlesztői készleteken (ASDKs). További információ az Azure Stack hub-ról: [Mi az Azure stack hub?](https://azure.microsoft.com/overview/azure-stack/).
  - Bérlői előfizetés az egyes Azure Stack hubokon.    
      - **Jegyezze fel minden egyes Azure Stack hub előfizetési AZONOSÍTÓját és Azure Resource Manager végpontját.**
  - Egy Azure Active Directory (Azure AD) egyszerű szolgáltatásnév, amely jogosult a bérlői előfizetésre az egyes Azure Stack hubokon. Előfordulhat, hogy két egyszerű szolgáltatást kell létrehoznia, ha az Azure Stack hubok különböző Azure AD-bérlők között vannak telepítve. Ha meg szeretné tudni, hogyan hozhat létre egyszerű szolgáltatásnevet Azure Stack hubhoz, tekintse meg az [egyszerű szolgáltatások létrehozása az Azure stack hub-erőforrásokhoz való hozzáférést biztosító alkalmazások létrehozásával](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals)foglalkozó témakört.
      - **Jegyezze fel az egyes egyszerű szolgáltatások alkalmazás-AZONOSÍTÓját, az ügyfél titkos kulcsát és a bérlő nevét (xxxxx.onmicrosoft.com).**
  - SQL Server 2016 Enterprise konzorciális az egyes Azure Stack hub-piactéren. A Marketplace Syndication szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Marketplace-elemek letöltése az Azure-ból Azure stack hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item)-ba című témakört.
    **Győződjön meg arról, hogy a szervezet rendelkezik a megfelelő SQL-licenccel.**
  - A helyi gépre telepített [Windows Docker](https://docs.docker.com/docker-for-windows/) .

## <a name="get-the-docker-image"></a>A Docker-rendszerkép beszerzése

Az egyes központi telepítésekhez tartozó Docker-rendszerképek megszüntetik a Azure PowerShell különböző verziói közötti függőségi problémákat.

1.  Győződjön meg arról, hogy a Windows rendszerhez készült Docker Windows-tárolókat használ.
2.  Futtassa a következő parancsfájlt egy rendszergazda jogú parancssorban a Docker-tároló üzembe helyezési parancsfájlokkal való lekéréséhez.

```powershell  
 docker pull intelligentedge/sqlserver2016-hadr:1.0.0
```

## <a name="deploy-the-availability-group"></a>A rendelkezésre állási csoport üzembe helyezése

1.  A tároló rendszerképének leállítása után indítsa el a rendszerképet.

      ```powershell  
      docker run -it intelligentedge/sqlserver2016-hadr:1.0.0 powershell
      ```

2.  A tároló elindítása után egy emelt szintű PowerShell-terminált kap a tárolóban. Módosítsa a címtárakat az üzembe helyezési parancsfájl eléréséhez.

      ```powershell  
      cd .\SQLHADRDemo\
      ```

3.  Futtassa az üzemelő példányt. Adja meg a hitelesítő adatokat és az erőforrások nevét, ahol szükséges. HA arra a Azure Stack központra hivatkozik, ahol a HA-fürtöt telepíti, és DR arra a Azure Stack hubhoz, ahol a DR-fürtöt telepíteni fogja.

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

4.  Írja be a `Y` értéket a NuGet-szolgáltató telepítésének engedélyezéséhez, amely elindítja a telepíteni kívánt "2018-03-01-Hybrid" modulok API-profilját.

5.  Várjon, amíg az erőforrás üzembe helyezése befejeződik.

6.  Miután a DR erőforrás üzembe helyezése befejeződött, lépjen ki a tárolóból.

      ```powershell
      exit
      ```

7.  Vizsgálja meg az üzemelő példányt az egyes Azure Stack hub-portálon található erőforrások megtekintésével. Kapcsolódjon az egyik SQL-példányhoz a HA-környezetben, és vizsgálja meg a rendelkezésre állási csoportot SQL Server Management Studio (SSMS) használatával.

![SQL Server 2016 SQL HA](media/solution-deployment-guide-sql-ha/image2.png)

## <a name="next-steps"></a>Következő lépések

  - A SQL Server Management Studio használatával manuálisan hajthatja végre a feladatátvételt a fürtön, lásd: az [AlwaysOn rendelkezésre állási csoport kényszerített manuális feladatátvételének végrehajtása (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-2017)
  - További információ a hibrid felhőalapú alkalmazásokról: [hibrid felhőalapú megoldások.](https://aka.ms/azsdevtutorials)
  - Használja saját adatait, vagy módosítsa a kódot erre a mintára a [githubon](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
