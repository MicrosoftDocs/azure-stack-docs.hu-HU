---
title: Kapcsolódás Azure Stack Azure-fiók bővítmény használatával a Visual Studio Code-ban | Microsoft Docs
description: Fejlesztőként kapcsolódjon Azure Stack Azure-fiók bővítmény használatával a Visual Studio Code-ban
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 08/05/2019
ms.openlocfilehash: 44d35d59b2b50682dd6911f6d2b08fea8e005938
ms.sourcegitcommit: a0dcb61890ad0f7b8e1f738f7186198681adcc2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68820812"
---
# <a name="connect-to-azure-stack-using-azure-account-extension-in-visual-studio-code"></a>Kapcsolódás Azure Stack Azure-fiók bővítmény használatával a Visual Studio Code-ban

Ebből a cikkből megtudhatja, hogyan csatlakozhat a Azure Stackhoz az Azure-fiók bővítmény használatával. Frissítenie kell a Visual Studio Code (VS Code) beállításait.

A VS Code egy könnyű szerkesztő a webes és felhőalapú alkalmazások létrehozásához és hibakereséséhez. A ASP.NET Core, a Python, a NodeJS, a Go és más fejlesztők a VS Code-ot használják. Az Azure-fiók bővítmény használatával egyetlen Azure-bejelentkezést is használhat előfizetés-szűréssel további Azure-bővítményekhez. A bővítmény a VS Code-integrált terminálon elérhetővé teszi a Azure Cloud Shell. A bővítmény használatával az Azure AD (Azure AD) és az Identity Manager Active Directory összevont szolgáltatások (AD FS) használatával csatlakozhat a Azure Stack-előfizetéshez. Bejelentkezhet Azure Stackba, kiválaszthatja az előfizetését, és megnyithat egy új parancssort a Cloud shellben. 

> [!Note]  
> A cikkben szereplő lépéseket egy Active Directory összevont szolgáltatások (AD FS) környezetéhez is használhatja. Használja a AD FS hitelesítő adatait és a végpontokat.

## <a name="pre-requisites-for-the-azure-account-extension"></a>Az Azure-fiók bővítményre vonatkozó előfeltételek

1. Azure Stack környezet 1904 Build vagy újabb
2. [Visual Studio Code](https://code.visualstudio.com/)
3. [Azure-fiók bővítmény](https://github.com/Microsoft/vscode-azure-account)
4. [Azure Stack előfizetés](https://azure.microsoft.com/overview/azure-stack/)

## <a name="steps-to-connect-to-azure-stack"></a>A Azure Stackhoz való kapcsolódás lépései

1. Futtassa az **azonosító** parancsfájlt Azure stack eszközökről a githubon.

    - A szkript futtatása előtt telepítenie kell a PowerShellt, és konfigurálnia kell a környezetét. Útmutatásért lásd: a [PowerShell telepítése Azure Stackhoz](../operator/azure-stack-powershell-install.md).

    - Az **Identity** script utasításait és parancsfájlját lásd: [AzureStack-Tools/Identity](https://github.com/Azure/AzureStack-Tools/tree/master/Identity).

    - Ugyanebben a munkamenetben futtassa a következőket:

    ```powershell  
    Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
    -DirectoryTenantName $homeDirectoryTenantName -Verbose
    Register-AzsWithMyDirectoryTenant -TenantResourceManagerEndpoint $tenantARMEndpoint `
    -DirectoryTenantName $guestDirectoryTenantName
    ```

2. Nyissa meg a VS Code-ot.

3. A bal oldali sarokban válassza a bővítmények lehetőséget.

4. A keresőmezőbe írja be a `Azure Account` kifejezést.

5. Válassza az **Azure-fiók** lehetőséget, és válassza a **telepítés**lehetőséget.

      ![A Visual Studio Code Azure Stack](media/azure-stack-dev-start-vscode-azure/image1.png)

6. A bővítmény betöltéséhez indítsa újra a VS Code-ot.

7. A metaadatok beolvasása a Azure Stack Azure Resource Manager való kapcsolódáshoz. 
    
    A Microsoft Azure Resource Manager egy felügyeleti keretrendszer, amely lehetővé teszi az Azure-erőforrások üzembe helyezését, kezelését és figyelését.
    - A Azure Stack Development Kit (ASDK) Resource Manager URL-címe a következő:`https://management.local.azurestack.external/` 
    - Az integrált rendszer Resource Manager URL-címe a következő:`https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`
    - Adja hozzá a következő szöveget az URL-címhez a metaadatok eléréséhez:`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

    Például az Azure Resource Manager-végpont metaadatainak beolvasására szolgáló URL-cím az alábbihoz hasonló lehet:`https://management.local.azurestack.external/metadata/endpoints?api-version=1.0`

    Jegyezze fel a Return JSON-t. Szüksége lesz a `loginEndpoint` és `audiences` tulajdonság értékeire.

8. Nyomja le **a CTRL + SHIFT + P** billentyűkombinációt, és válassza a beállítások lehetőséget **: Nyissa meg a felhasználói beállításokat**(JSON).

9. A Kódszerkesztő alkalmazásban frissítse a következő JSON-kódrészletet a környezetéhez tartozó értékekkel, majd illessze be a kódrészletet a Settings (beállítások) blokkba.

    - Értékek

        | Paraméter | Leírás |
        | --- | --- |
        | `tenant-ID` | A Azure Stack [bérlő azonosítójának](../operator/azure-stack-identity-overview.md)értéke. |
        | `activeDirectoryEndpointUrl` | Ez a loginEndpoint tulajdonság URL-címe. |
        | `activeDirectoryResourceId` | Ez a célközönségek tulajdonság URL-címe.
        | `resourceManagerEndpointUrl` | Ez a Azure Stack Azure Resource Manager gyökerének URL-címe. | 

    - JSON-kódrészlet:

      ```JSON  
      "azure.tenant": "tenant-ID",
      "azure.ppe": {
          "activeDirectoryEndpointUrl": "Login endpoint",
          "activeDirectoryResourceId": "This is the URL from the audiences property.",
          "resourceManagerEndpointUrl": "Aure Resource Management Endpoint",
      },
      "azure.cloud": "AzurePPE"
      ```

10. Mentse a felhasználói beállításokat, és használja a **CTRL + SHIFT + P billentyűkombinációt** . Válassza **ki az Azure-t: Jelentkezzen be az Azure**-felhőbe. Az új lehetőség, a **AzurePPE**, megjelenik a célok listájában.

11. Válassza a **AzurePPE**lehetőséget. A hitelesítés lap betöltődik a böngészőben. Jelentkezzen be a végpontba.

12. Annak ellenőrzéséhez, hogy sikeresen bejelentkezett-e a Azure stack-előfizetésbe, használja a **CTRL + SHIFT + P billentyűkombinációt** , és válassza az Azure lehetőséget **: Válassza az** előfizetés lehetőséget, és ellenőrizze, hogy elérhető-e az előfizetés.

## <a name="commands"></a>Parancsok

| Azure: Bejelentkezés | Bejelentkezés az Azure-előfizetésbe |
| --- | --- |
| Azure: Bejelentkezés az eszköz kódjával | Jelentkezzen be az Azure-előfizetésbe egy eszköz kódjával. Használjon olyan programkódot a telepítőben, ahol a bejelentkezési parancs nem működik. |
| Azure: Bejelentkezés az Azure-felhőbe | Jelentkezzen be az Azure-előfizetésbe az egyik szuverén felhőben. |
| Azure: Kijelentkezés | Jelentkezzen ki az Azure-előfizetésből. |
| Azure: Előfizetések kiválasztása | Válassza ki a használni kívánt előfizetések készletét. A bővítmény csak a szűrt előfizetésekben lévő erőforrásokat jeleníti meg. |
| Azure: Fiók létrehozása | Ha még nem rendelkezik Azure-fiókkal, [regisztrálhat](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-azure-account&mktingSource=vscode-azure-account) egy mai napra, és 200 ingyenes \$kreditet kaphat. |
| Azure: Bash megnyitása Cloud Shell | Nyisson meg egy új, bash-t futtató terminált Cloud Shell. |
| Azure: A PowerShell megnyitása Cloud Shell | Nyisson meg egy új, a PowerShellt futtató terminált Cloud Shellban. |
| Azure: Feltöltés Cloud Shell | Töltsön fel egy fájlt a Cloud Shell Storage-fiókjába. |

## <a name="next-steps"></a>További lépések

[Fejlesztési környezet beállítása Azure Stack](azure-stack-dev-start.md)
