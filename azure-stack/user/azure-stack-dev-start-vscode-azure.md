---
title: Csatlakozás Azure-fiók bővítmény használata a Visual Studio Code az Azure Stack |} A Microsoft Docs
description: A fejlesztők az Azure Stack Azure-fiók bővítmény használata a Visual Studio Code csatlakozni
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: Howto
ms.date: 05/31/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 8e64a570ab45e57e3cf58639bc2ec23d9b9bd81b
ms.sourcegitcommit: 07cc716d97bf484c7260eb165ae205ae25e09589
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66453493"
---
# <a name="connect-to-azure-stack-using-azure-account-extension-in-visual-studio-code"></a>Csatlakozhat az Azure Stack Azure-fiók bővítmény használata a Visual Studio Code

Ebben a cikkben végigvezetjük, hogyan csatlakozhat az Azure Stack használatával az Azure-fiókkiterjesztés. A Visual Studio Code-ot (a VS Code) beállításait frissíteni kell.

A VS Code-e egy egyszerűsített szerkesztője épület és hibakeresési webes és felhőalapú alkalmazásokat. ASP.NET Core, Python, a NodeJS, Go és más fejlesztők által szolgál. Az Azure-fiók bővítményt is használhat egy egyetlen Azure bejelentkezési szűrést további Azure-bővítményeket az előfizetéshez. A bővítmény elérhetővé teszi az Azure Cloud Shell a VS Code integrált terminálon. A bővítmény használata csatlakozhat az Azure Stack-előfizetés használata az Azure AD (Azure AD) és az Active Directory összevont szolgáltatások (AD FS) az identity Manager. Ez lehetővé teszi, hogy jelentkezzen be az Azure Stack, válassza ki az előfizetését és a egy új parancssor megnyitása a cloud shellben. 

> [!Note]  
> Ebben a cikkben egy olyan környezetben, az Active Directory összevonási szolgáltatásokban (AD FS) használhatja a lépéseket. Az AD FS hitelesítő adatok és a végpontok használata.

## <a name="pre-requisites-for-the-azure-account-extension"></a>Az Azure-fiók bővítmény előfeltételei

1. Az Azure Stack-környezet 1904 build vagy újabb
2. [Visual Studio Code](https://code.visualstudio.com/)
3. [Azure Account Extension](https://github.com/Microsoft/vscode-azure-account)
4. [Azure Stack-előfizetéshez](https://azure.microsoft.com/overview/azure-stack/)

## <a name="steps-to-connect-to-azure-stack"></a>Csatlakozás az Azure Stack lépései

1. Nyissa meg a VS Code.

2. Válassza ki **bővítmények** a bal oldali sarokban.

3. A keresőmezőbe írja be a `Azure Account` kifejezést.

4. Válassza ki **Azure-fiók** válassza **telepítése**.

      ![Az Azure Stack a Visual Studio Code](media/azure-stack-dev-start-vscode-azure/image1.png)

5. Indítsa újra a VS Code betölteni a bővítményt.

6. Csatlakozás az Azure Resource Manager az Azure stack a metaadatok lekérése. 
    
    A Microsoft Azure Resource Manager-keretrendszert, amely lehetővé teszi, hogy üzembe helyezése, kezelése és monitorozása az Azure-erőforrások.
    - A Resource Manager URL-cím az Azure Stack Development Kit (ASDK) a következő: `https://management.local.azurestack.external/` 
    - Integrált rendszer Resource Manager URL-je: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`
    - Adja hozzá a következő szöveget a metaadatok elérésére az URL-címre: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

    Ha például az URL-cím az Azure Resource Manager-végpont a metaadatok lekérése céljából, előfordulhat, hogy következőhöz hasonló: `https://management.local.azurestack.external/metadata/endpoints?api-version=1.0`

    Jegyezze fel a visszaküldött JSON. Szüksége lesz a tartozó értékeket a `loginEndpoint` és `loginEndgraphEndpointpoint` tulajdonság.

7. Nyomja meg **Ctrl + Shift + P** válassza **beállítások: Nyissa meg a felhasználói beállítások (JSON)** .

8. A kódszerkesztőben frissítse a következő JSON-kódrészletben értékekkel a környezetnek, és majd kódrészlet illessze be a beállítások letiltása.

    - Értékek:

        | Paraméter | Leírás |
        | --- | --- |
        | `tenant-ID` | Az Azure Stack értékét [bérlőazonosító](../operator/azure-stack-identity-overview.md). |
        | `activeDirectoryEndpointUrl` | Ez az az URL-cím loginEndpoint tulajdonságból. |
        | `activeDirectoryResourceId` | Ez az az URL-cím loginEndgraphEndpointpoint tulajdonságból.
        | `resourceManagerEndpointUrl` | Ez az Azure stack az Azure Resource Manager gyökér URL-címe. | 

    - JSON-kódrészletre:

      ```JSON  
      "azure.tenant": "tenant-ID",
      "azure.ppe": {
          "activeDirectoryEndpointUrl": "Login endpoint",
          "activeDirectoryResourceId": "graph audience",
          "resourceManagerEndpointUrl": "Management Endpoint",
      },
      "azure.cloud": "AzurePPE"
      ```

8. A felhasználói beállítások mentése és használata **Ctrl + Shift + P** még egyszer. Válassza ki **Azure: Jelentkezzen be Azure-felhő**. Az új lehetőség **AzurePPE**, tárolók listája megjelenik.

9. Válassza ki **AzurePPE**. A hitelesítési oldalt a böngészőben tölti be. Jelentkezzen be a végponthoz.

11. Ha tesztelni szeretné, hogy sikeresen bejelentkezett az Azure Stack-előfizetéssel, használja a **Ctrl + Shift + P** válassza **Azure: Válassza ki előfizetését** és az előfizetés van-e rendelkezésre.

## <a name="commands"></a>Parancsok

| Azure: Bejelentkezés | Jelentkezzen be az Azure-előfizetésébe |
| --- | --- |
| Azure: Jelentkezzen be az eszköz kód | Jelentkezzen be az Azure-előfizetéshez egy eszköz kóddal. Ezzel a beállításokat, a bejelentkezés parancs nem működik. |
| Azure: Jelentkezzen be Azure-felhőben | Jelentkezzen be az Azure-előfizetéshez a független felhőkben egyikében. |
| Azure: Kijelentkezés | Jelentkezzen ki az Azure-előfizetésében. |
| Azure: Előfizetések kiválasztása | Válassza ki a használni kívánt előfizetések készletét. A bővítmény csak a szűrt előfizetések belüli erőforrások jeleníti meg. |
| Azure: Fiók létrehozása | Ha nem rendelkezik Azure-fiók, akkor [regisztráció](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-azure-account&mktingSource=vscode-azure-account) egy mai részesül \$200 ingyenes kredittel jutalmazzuk. |
| Azure: Nyissa meg a Bash Cloud shellben | Nyisson meg egy új terminálablakban a Cloud Shell Bash futtatása. |
| Azure: Nyissa meg a Cloud Shellbeli PowerShell | Nyisson meg egy új terminálablakban a Cloud Shellben futtatja a Powershellt. |
| Azure: Cloud Shell feltöltendő | Töltse fel egy fájlt a Cloud Shell tárfiókját. |

## <a name="next-steps"></a>További lépések

[Az Azure Stack fejlesztői környezet beállítása ](azure-stack-dev-start.md)