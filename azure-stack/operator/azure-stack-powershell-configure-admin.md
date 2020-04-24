---
title: Kapcsolódás Azure Stack hubhoz a PowerShell használatával
description: Megtudhatja, hogyan csatlakozhat Azure Stack hubhoz a PowerShell-lel.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: e0f10db1c9d983b4802812bd4274429387032c66
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77698402"
---
# <a name="connect-to-azure-stack-hub-with-powershell"></a>Kapcsolódás Azure Stack hubhoz a PowerShell használatával

Az Azure Stack hub a PowerShell használatával is konfigurálható olyan erőforrások kezeléséhez, mint az ajánlatok, a csomagok, a kvóták és a riasztások létrehozása. Ez a témakör segítséget nyújt az operátori környezet konfigurálásához.

## <a name="prerequisites"></a>Előfeltételek

Futtassa a következő előfeltételeket a [Azure stack Development Kit (ASDK)](../asdk/asdk-connect.md#connect-with-rdp) vagy egy Windows-alapú külső ügyfélről, ha VPN- [kapcsolaton keresztül csatlakozik a ASDK](../asdk/asdk-connect.md#connect-with-vpn).

- Telepítse [Azure stack hub-kompatibilis Azure PowerShell modulokat](azure-stack-powershell-install.md).  
- Töltse le az [Azure stack hub használatához szükséges eszközöket](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Az Azure AD-vel való kapcsolat

Az Azure Stack hub-kezelő környezet PowerShell-lel való konfigurálásához futtassa az alábbi parancsfájlok egyikét. Cserélje le a Azure Active Directory (Azure AD) tenantName, és Azure Resource Manager a végpontok értékeit a saját környezeti konfigurációjával.

[!include[Remove Account](../../includes/remove-account.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name.
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Kapcsolat AD FS

Kapcsolódjon a Azure Stack hub operátori környezethez a PowerShell-lel Azure Active Directory összevont szolgáltatásokkal (Azure AD FS). A ASDK esetében ez a Azure Resource Manager végpont a következőre `https://adminmanagement.local.azurestack.external`van beállítva:. Azure Stack hub integrált rendszerek Azure Resource Manager végpontjának lekéréséhez forduljon a szolgáltatóhoz.

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment.
  Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
  ```

> [!Note]  
> AD FS csak a felhasználói identitásokkal való interaktív hitelesítést támogatja. Ha egy hitelesítőadat-objektumra van szükség, akkor egy egyszerű szolgáltatásnevet (SPN) kell használnia. További információ az Azure Stack hub és a AD FS identitás-kezelési szolgáltatásként való beállításáról: [AD FS egyszerű szolgáltatásnév kezelése](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

## <a name="test-the-connectivity"></a>Kapcsolat tesztelése

Most, hogy mindent beállított, a PowerShell használatával hozzon létre erőforrásokat Azure Stack hub-on belül. Létrehozhat például egy erőforráscsoportot az alkalmazáshoz, és hozzáadhat egy virtuális gépet. A következő parancs használatával hozzon létre egy **MyResourceGroup**nevű erőforráscsoportot.

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>További lépések

- [Sablonok fejlesztése Azure stack hub számára](../user/azure-stack-develop-templates.md).
- [Sablonok üzembe helyezése a PowerShell](../user/azure-stack-deploy-template-powershell.md)-lel.
  - [Azure stack hub-modul referenciája](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
