---
title: Kapcsolódás Azure Stack hubhoz az Azure-fiók bővítmény használatával a Visual Studio Code-ban
description: Fejlesztőként kapcsolódjon Azure Stack hubhoz az Azure-fiók bővítmény használatával a Visual Studio Code-ban
author: mattbriggs
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/21/2020
ms.openlocfilehash: fc07b78253a19d8a7f459aecacd699338a129ea7
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546701"
---
# <a name="connect-to-azure-stack-hub-using-azure-account-extension-in-visual-studio-code"></a>Kapcsolódás Azure Stack hubhoz az Azure-fiók bővítmény használatával a Visual Studio Code-ban

Ebből a cikkből megtudhatja, hogyan csatlakozhat az Azure Stack hubhoz az Azure-fiók bővítmény használatával. Frissítenie kell a Visual Studio Code (VS Code) beállításait.

A VS Code egy könnyű szerkesztő a webes és felhőalapú alkalmazások létrehozásához és hibakereséséhez. A ASP.NET Core, a Python, a NodeJS, a Go és más fejlesztők a VS Code-ot használják. Az Azure-fiók bővítmény használatával egyetlen Azure-bejelentkezést is használhat előfizetés-szűréssel további Azure-bővítményekhez. A bővítmény a VS Code-integrált terminálon elérhetővé teszi a Azure Cloud Shell. A bővítmény használatával az Azure AD (Azure AD) és az Identity Manager Active Directory összevont szolgáltatások (AD FS) használatával kapcsolódhat Azure Stack hub-előfizetéséhez. Bejelentkezhet Azure Stack hubhoz, kiválaszthatja az előfizetését, és megnyithat egy új parancssort a Cloud shellben. 

> [!NOTE]  
> A cikkben szereplő lépéseket egy Active Directory összevont szolgáltatások (AD FS) környezetéhez is használhatja. Használja a AD FS hitelesítő adatait és a végpontokat.

## <a name="pre-requisites-for-the-azure-account-extension"></a>Az Azure-fiók bővítményre vonatkozó előfeltételek

1. Azure Stack hub-környezet 1904 Build vagy újabb
2. [Visual Studio Code](https://code.visualstudio.com/)
3. [Azure-fiók bővítmény](https://github.com/Microsoft/vscode-azure-account)
4. [Azure Stack hub-előfizetés](https://azure.microsoft.com/overview/azure-stack/)

## <a name="steps-to-connect-to-azure-stack-hub"></a>Azure Stack hubhoz való kapcsolódás lépései

1. Futtassa az **Identity** scriptet Azure stack Hub-eszközökről a githubon.

    - A szkript futtatása előtt telepítenie kell a PowerShellt, és konfigurálnia kell a környezetét. Útmutatásért lásd: a [PowerShell telepítése Azure stack hubhoz](../operator/powershell-install-az-module.md).

    - Az **Identity** script utasításait és parancsfájlját lásd: [AzureStack-Tools/Identity](https://aka.ms/aa6z611).

    - Ugyanebben a munkamenetben futtassa a következőket:

    ```powershell  
    Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
    -DirectoryTenantName $homeDirectoryTenantName -Verbose
    Register-AzsWithMyDirectoryTenant -TenantResourceManagerEndpoint $tenantARMEndpoint `
    -DirectoryTenantName $guestDirectoryTenantName
    ```

2. Nyissa meg a VS Code-ot.

3. A bal oldali sarokban válassza a **bővítmények** lehetőséget.

4. A keresőmezőbe írja be a `Azure Account` kifejezést.

5. Válassza az **Azure-fiók** lehetőséget, és válassza a **telepítés** lehetőséget.

      ![Azure Stack hub Visual Studio Code](media/azure-stack-dev-start-vscode-azure/image1.png)

6. A bővítmény betöltéséhez indítsa újra a VS Code-ot.

7. Kérje le a metaadatokat, hogy csatlakozzanak a Azure Stack hub Azure Resource Manager. 
    
    A Microsoft Azure Resource Manager egy felügyeleti keretrendszer, amely lehetővé teszi az Azure-erőforrások üzembe helyezését, kezelését és figyelését.
    - A Azure Stack Development Kit (ASDK) Resource Manager URL-címe a következő: `https://management.local.azurestack.external/` 
    - Az integrált rendszer Resource Manager URL-címe a következő: `https://management.region.<fqdn>/` , ahol a a `<fqdn>` teljes tartománynév.
    - Adja hozzá a következő szöveget az URL-címhez a metaadatok eléréséhez: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

    Például az Azure Resource Manager-végpont metaadatainak beolvasására szolgáló URL-cím az alábbihoz hasonló lehet: `https://management.local.azurestack.external/metadata/endpoints?api-version=1.0`

    Jegyezze fel a Return JSON-t. Szüksége lesz a és tulajdonság értékeire `loginEndpoint` `audiences` .

8. Nyomja le **a CTRL + SHIFT + P** billentyűkombinációt, majd válassza a **Beállítások: felhasználói beállítások (JSON) megnyitása** lehetőséget.

9. A Kódszerkesztő alkalmazásban frissítse a következő JSON-kódrészletet a környezetéhez tartozó értékekkel, majd illessze be a kódrészletet a Settings (beállítások) blokkba.

    - Értékek:

        | Paraméter | Leírás |
        | --- | --- |
        | `tenant-ID` | Az Azure Stack hub- [bérlő azonosítójának](../operator/azure-stack-identity-overview.md)értéke. |
        | `activeDirectoryEndpointUrl` | Ez a loginEndpoint tulajdonság URL-címe. |
        | `activeDirectoryResourceId` | Ez a célközönségek tulajdonság URL-címe.
        | `resourceManagerEndpointUrl` | Ez az Azure Stack hub Azure Resource Managerjának legfelső szintű URL-címe. |
        | `validateAuthority` | Ezt a paramétert akkor hagyhatja ki, ha az Azure AD-t használja Identity managerként. Ha AD FS használ, adja hozzá a paramétert az értékhez `false` . |

    - JSON-kódrészlet:

      ```JSON  
      "azure.tenant": "tenant-ID",
      "azure.ppe": {
          "activeDirectoryEndpointUrl": "Login endpoint",
          "activeDirectoryResourceId": "This is the URL from the audiences property.",
          "resourceManagerEndpointUrl": "Aure Resource Management Endpoint",
          "validateAuthority" : false, 
      },
      "azure.cloud": "AzurePPE"
      ```

10. Mentse a felhasználói beállításokat, és használja a **CTRL + SHIFT + P billentyűkombinációt** . Válassza ki az **Azure-t: Jelentkezzen be az Azure-felhőbe**. Az új lehetőség, a **AzurePPE** , megjelenik a célok listájában.

11. Válassza a **AzurePPE** lehetőséget. A hitelesítés lap betöltődik a böngészőben. Jelentkezzen be a végpontba.

12. Annak ellenőrzéséhez, hogy sikeresen bejelentkezett-e a Azure Stack hub-előfizetésbe, használja a **CTRL + SHIFT + P billentyűkombinációt** , és válassza az **Azure lehetőséget: válassza az előfizetés lehetőséget** , és ellenőrizze, hogy elérhető-e az előfizetés.

## <a name="commands"></a>Parancsok

| Azure: bejelentkezés | Jelentkezzen be az Azure-előfizetésbe |
| --- | --- |
| Azure: bejelentkezés az eszköz kódjával | Jelentkezzen be az Azure-előfizetésbe egy eszköz kódjával. Használjon olyan programkódot a telepítőben, ahol a bejelentkezési parancs nem működik. |
| Azure: bejelentkezés az Azure Cloudba | Jelentkezzen be az Azure-előfizetésbe az egyik szuverén felhőben. |
| Azure: kijelentkezés | Jelentkezzen ki az Azure-előfizetésből. |
| Azure: előfizetések kiválasztása | Válassza ki a használni kívánt előfizetések készletét. A bővítmény csak a szűrt előfizetésekben lévő erőforrásokat jeleníti meg. |
| Azure: fiók létrehozása | Ha még nem rendelkezik Azure-fiókkal, [regisztrálhat](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-azure-account&mktingSource=vscode-azure-account) egy mai napra, és \$ 200 ingyenes kreditet kaphat. |
| Azure: bash megnyitása Cloud Shell | Nyisson meg egy új, bash-t futtató terminált Cloud Shell. |
| Azure: a PowerShell megnyitása Cloud Shell | Nyisson meg egy új, a PowerShellt futtató terminált Cloud Shellban. |
| Azure: feltöltés a Cloud Shellba | Töltsön fel egy fájlt a Cloud Shell Storage-fiókjába. |

## <a name="next-steps"></a>Következő lépések

[Fejlesztési környezet beállítása Azure Stack központban ](azure-stack-dev-start.md)
