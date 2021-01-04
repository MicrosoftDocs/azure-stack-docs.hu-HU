---
title: Kapcsolódás Azure Stack hubhoz a PowerShell használatával
description: Megtudhatja, hogyan csatlakozhat Azure Stack hubhoz a PowerShell-lel.
author: mattbriggs
ms.topic: article
ms.date: 11/19/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 11/19/2020
ms.openlocfilehash: 791923d3b2deacdc886dfcc0167736bd7214f9b8
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697626"
---
# <a name="connect-to-azure-stack-hub-with-powershell"></a>Kapcsolódás Azure Stack hubhoz a PowerShell használatával

Az Azure Stack hub a PowerShell használatával is konfigurálható olyan erőforrások kezeléséhez, mint az ajánlatok, a csomagok, a kvóták és a riasztások létrehozása. Ez a témakör segítséget nyújt az operátori környezet konfigurálásához.

## <a name="prerequisites"></a>Előfeltételek

Futtassa a következő előfeltételeket a [Azure stack Development Kit (ASDK)](../asdk/asdk-connect.md#connect-with-rdp) vagy egy Windows-alapú külső ügyfélről, ha VPN- [kapcsolaton keresztül csatlakozik a ASDK](../asdk/asdk-connect.md#connect-with-vpn).

- Telepítse [Azure stack hub-kompatibilis Azure PowerShell modulokat](powershell-install-az-module.md).  
- Töltse le az [Azure stack hub használatához szükséges eszközöket](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Az Azure AD-vel való kapcsolat

Az Azure Stack hub-kezelő környezet PowerShell-lel való konfigurálásához futtassa az alábbi parancsfájlok egyikét. Cserélje le a Azure Active Directory (Azure AD) tenantName, és Azure Resource Manager a végpontok értékeit a saját környezeti konfigurációjával.

### <a name="az-modules"></a>[Az modulok](#tab/az1)

[!include[Remove Account](../includes/remove-account-az.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name.
    $AuthEndpoint = (Get-AzEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```
### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm1)

[!include[Remove Account](../includes/remove-account-azurerm.md)]

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

---


## <a name="connect-with-ad-fs"></a>Kapcsolat AD FS

Kapcsolódjon a Azure Stack hub operátori környezethez a PowerShell-lel Azure Active Directory összevont szolgáltatásokkal (Azure AD FS). A ASDK esetében ez a Azure Resource Manager végpont a következőre van beállítva: `https://adminmanagement.local.azurestack.external` . Azure Stack hub integrált rendszerek Azure Resource Manager végpontjának lekéréséhez forduljon a szolgáltatóhoz.

### <a name="az-modules"></a>[Az modulok](#tab/az2)

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment.
  Login-AzAccount -EnvironmentName "AzureStackAdmin"
  ```

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm2)

```powershell  
# Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
    -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
    -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

# Sign in to your environment.
Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
```

---

[!Include [AD FS only supports interactive authentication with user identities](../includes/note-powershell-adfs.md)]

## <a name="test-the-connectivity"></a>Kapcsolat tesztelése

Most, hogy mindent beállított, a PowerShell használatával hozzon létre erőforrásokat Azure Stack hub-on belül. Létrehozhat például egy erőforráscsoportot az alkalmazáshoz, és hozzáadhat egy virtuális gépet. A következő parancs használatával hozzon létre egy **MyResourceGroup** nevű erőforráscsoportot.

### <a name="az-modules"></a>[Az modulok](#tab/az3)
```powershell  
New-AzResourceGroup -Name "MyResourceGroup" -Location "Local"
```

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm3)

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

---


## <a name="next-steps"></a>További lépések

- [Az előfizetések, csomagok és ajánlatok kezelése a PowerShell használatával Azure Stack hub-ban](azure-stack-powershell-plan-offer.md)
- [Sablonok fejlesztése Azure stack hub számára](../user/azure-stack-develop-templates.md).
- [Sablonok üzembe helyezése a PowerShell](../user/azure-stack-deploy-template-powershell.md)-lel.
- [Azure stack hub-modul referenciája](/powershell/azure/azure-stack/overview).
