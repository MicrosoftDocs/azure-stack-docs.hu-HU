---
title: A Docker használata a PowerShell futtatásához Azure Stack központban
description: A Docker használata a PowerShell futtatásához Azure Stack központban
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/16/2020
ms.openlocfilehash: 798b3d9e94f6263b02c291d2f5725768f1a8522a
ms.sourcegitcommit: f30e5178e0b4be4e3886f4e9f699a2b51286e2a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97620687"
---
# <a name="use-docker-to-run-powershell-for-azure-stack-hub"></a>A Azure Stack hub PowerShellének futtatása a Docker használatával

Ebben a cikkben a Docker használatával létrehozhat egy tárolót, amelyen futtathatja a PowerShell azon verzióját, amely a különböző felületeken való munkához szükséges. A AzureRM-modulok és a legújabb az modulok használatához útmutatást talál. A AzureRM Windows-alapú tárolót igényel. Az az egy Linux-alapú tárolót használ.

## <a name="docker-prerequisites"></a>A Docker előfeltételei

### <a name="install-docker"></a>A Docker telepítése

1. A [Docker](https://docs.docker.com/install/)telepítése.

1. Egy parancssori programban, például a PowerShellben vagy a bash-ben írja be a következőket:

    ```bash
    docker --version
    ```

### <a name="set-up-a-service-principal-for-using-powershell"></a>Egyszerű szolgáltatásnév beállítása a PowerShell használatához

Ahhoz, hogy a PowerShell használatával hozzáférhessen a Azure Stack hub erőforrásaihoz, szüksége lesz egy egyszerű szolgáltatásnév használatára a Azure Active Directory-(Azure AD-) bérlőben. Engedélyeket delegálhat a felhasználói szerepköralapú hozzáférés-vezérléssel (RBAC). Előfordulhat, hogy az egyszerű szolgáltatást a felhő üzemeltetőjétől kell kérnie.

1. Az egyszerű szolgáltatás beállításához kövesse az [alkalmazások Azure stack hub-erőforrásokhoz való hozzáférésének biztosítása az egyszerű szolgáltatások létrehozásával](../operator/azure-stack-create-service-principals.md?view=azs-2002)című témakör utasításait.

2. Jegyezze fel az alkalmazás AZONOSÍTÓját, a titkos kulcsot, a bérlői azonosítót és az objektumazonosítót a későbbi használatra.

## <a name="run-powershell-in-docker"></a>A PowerShell futtatása a Docker-ben

### <a name="az-modules"></a>[Az modulok](#tab/az)

Ebben az útmutatóban egy Linux-alapú tároló-rendszerképet fog futtatni, amely tartalmazza a PowerShellt és az Azure Stack hub szükséges modulját.

1. A Docker-t Linux-tároló használatával kell futtatnia. A Docker futtatásakor váltson a Linux-tárolók elemre.

1. Futtassa a Docker-t egy olyan gépről, amely ugyanahhoz a tartományhoz csatlakozik, mint Azure Stack hub. Ha a Azure Stack Development Kit (ASDK) használja, telepítenie kell [a VPN-t a távoli gépen](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn).


## <a name="install-azure-stack-hub-az-module-on-a-linux-container"></a>Az Azure Stack hub az modul telepítése Linux-tárolón

1. A parancssorból futtassa a következő Docker-parancsot a PowerShell Ubuntu-tárolóban való futtatásához:

    ```bash
    docker run -it mcr.microsoft.com/azurestack/powershell
    ```

    Futtathat Ubuntut, Debiant vagy CentOS-t is. A következő Docker-fájlokat találhatja meg a GitHub-adattárban: [azurestack-PowerShell](https://github.com/Azure/azurestack-powershell). A Docker-fájlok legújabb változásairól a GitHub-tárházban tájékozódhat. Minden operációs rendszer címkével rendelkezik. Cserélje le a címkét, a kettőspont utáni szakaszt a kívánt operációs rendszer címkéjére.

    | Linux | Docker-rendszerkép |
    | --- | --- |
    | Ubuntu | `docker run -it mcr.microsoft.com/azurestack/powershell:ubuntu-18.04` |
    | Debian | `docker run -it mcr.microsoft.com/azurestack/powershell:debian-9` |
    | Centos | `docker run -it mcr.microsoft.com/azurestack/powershell:centos-7` |

2. A rendszerhéj készen áll a parancsmagokra. A rendszerhéj-kapcsolat teszteléséhez jelentkezzen be, majd futtassa a alkalmazást `Test-AzureStack.ps1` .

    Először hozza létre a szolgáltatás egyszerű hitelesítő adatait. Szüksége lesz a **titkos** és az **alkalmazás-azonosítóra**. A tároló ellenőrzésekor is szüksége lesz az **objektum-azonosítóra** `Test-AzureStack.ps1` . Előfordulhat, hogy egy egyszerű szolgáltatást kell kérnie a Felhőbeli operátortól.

    Adja meg a következő parancsmagokat a Service elvi objektum létrehozásához:

    ```powershell  
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    ```

5. Kapcsolódjon a környezethez a következő parancsfájl futtatásával az Azure Stack hub-példány következő értékeivel.

    | Érték | Leírás |
    | --- | --- |
    | A környezet neve. | Az Azure Stack hub-környezet neve. |
    | Resource Manager-végpont | A Resource Manager URL-címe. Ha nem tudja, vegye fel a kapcsolatot a Felhőbeli szolgáltatójával. A következőhöz hasonlóan fog kinézni: `https://management.region.domain.com`. | 
    | Címtár-bérlő azonosítója | Az Azure Stack hub bérlői könyvtárának azonosítója. | 
    | Hitelesítő adat | A szolgáltatásnevet tartalmazó objektum. Ebben az esetben `$pscredential` .  |

    ```powershell
    ./Login-Environment.ps1 -Name <String> -ResourceManagerEndpoint <resource manager endpoint> -DirectoryTenantId <String> -Credential $pscredential
    ```

   A PowerShell visszaadja a fiók objektumát.

7. Tesztelje a környezetet úgy, hogy futtatja a `Test-AzureStack.ps1` szkriptet a tárolóban. Az egyszerű szolgáltatásnév- **objektum azonosítójának** meghatározása. Ha nem jelöli meg az objektumazonosítót, a szkript továbbra is futni fog, de csak a bérlői (felhasználói) modulok tesztelésére és a rendszergazdai jogosultságokat igénylő modulok meghibásodására lesz szükség.

    ```powershell  
    ./Test-AzureStack.ps1 <Object ID>
    ```

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/rm)

Ebben az útmutatóban egy Windows-alapú tároló rendszerképét kell futtatnia, és telepítenie kell a PowerShellt és a szükséges modulokat a Azure Stack hub számára. Ha azt tervezi, hogy a Windows rendszerű Docker-t futtatja, akkor futtatnia kell a Docker-t egy fizikai gépen. A beágyazott virtualizálás nem támogatott a Hyper-V-ben.

1. A Docker futtatásához Windows 10 rendszerű Windows-tárolókat kell használnia. A Docker futtatásakor váltson a Windows-tárolók elemre. Az az modult támogató lemezképek a Docker 17,05-es vagy újabb verziójára lesz szükség.

1. Futtassa a Docker-t egy olyan gépről, amely ugyanahhoz a tartományhoz csatlakozik, mint Azure Stack hub. Ha a Azure Stack Development Kit (ASDK) használja, telepítenie kell [a VPN-t a távoli gépen](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn).

### <a name="install-azure-stack-hub-azurerm-module-on-a-windows-container"></a>Azure Stack hub AzureRM-modul telepítése Windows-tárolón

A Docker megnyitja a Microsoft */Windowsservercore Microsoft-* rendszerképét, amelyen telepítve van a Windows PowerShell 5,1. A fájl ezután betölti a NuGet és a Azure Stack hub PowerShell-modulokat, és letölti az eszközöket Azure Stack Hub-eszközökről.

1. [Töltse le az Azure-stack-PowerShell-tárházat](https://github.com/Azure-Samples/azure-stack-hub-powershell-in-docker.git) zip-fájlként, vagy az adattár klónozásával.

2. Nyissa meg a tárház mappát a terminálon.

3. Nyisson meg egy parancssori felületet a tárházban, majd írja be a következő parancsot:

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. A rendszerkép létrehozása után indítson el egy interaktív tárolót az alábbiak beírásával:

    ```bash  
    docker run -it azure-stack-powershell powershell
    ```

    Jegyezze fel a tároló nevét. Ugyanazt a tárolót használhatja ahelyett, hogy minden alkalommal új tárolót hozna létre a következő Docker-parancs futtatásával:

    ```bash  
        docker exec -it "Container name" powershell
    ```

5. A rendszerhéj készen áll a parancsmagokra.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Kapcsolódjon a Azure Stack hub-példányhoz az egyszerű szolgáltatásnév használatával. Most egy PowerShell-parancssort használ a Docker-ben. 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint <Your Azure Resource Manager endoint>
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId <TenantID> -ServicePrincipal -Credential $pscredential
    ```

   A PowerShell visszaadja a fiók objektumát:

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. A kapcsolat teszteléséhez hozzon létre egy erőforráscsoportot az Azure Stack hub-ban.

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

---

## <a name="next-steps"></a>További lépések

- Tekintse át az [Azure stack hub PowerShell Azure stack hub-](azure-stack-powershell-overview.md)beli áttekintését.
- További információ a [PowerShell API-profiljairól](azure-stack-version-profiles.md) Azure stack hub-ban.
- Telepítse [Azure stack hub PowerShellt](../operator/azure-stack-powershell-install.md).
- További információ a Felhőbeli konzisztencia [Azure Resource Manager-sablonok](azure-stack-develop-templates.md) létrehozásáról.
