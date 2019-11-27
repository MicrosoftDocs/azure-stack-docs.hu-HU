---
title: Kapcsolódás a Azure Stackhoz a PowerShell használatával | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat a Azure Stackhoz a PowerShell használatával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 988d2e75dfecd499293576bff3d9722e7ff2c96f
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74298871"
---
# <a name="connect-to-azure-stack-with-powershell"></a>Kapcsolódás Azure Stack a PowerShell-lel

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Azure Stack konfigurálható úgy, hogy a PowerShell használatával olyan erőforrásokat kezeljen, mint az ajánlatok, a csomagok, a kvóták és a riasztások létrehozása. Ez a témakör segít az üzemeltető környezet konfigurálásához.

## <a name="prerequisites"></a>Előfeltételek

Futtassa a következő előfeltételeket a [Azure stack Development Kit (ASDK)](../asdk/asdk-connect.md#connect-with-rdp) vagy egy Windows-alapú külső ügyfélről, ha VPN- [kapcsolaton keresztül csatlakozik a ASDK](../asdk/asdk-connect.md#connect-with-vpn).

- Telepítse [Azure stack-kompatibilis Azure PowerShell modulokat](azure-stack-powershell-install.md).  
- Töltse le a [Azure stack használatához szükséges eszközöket](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Az Azure AD Connect

A Azure Stack operátori környezet PowerShell-lel való konfigurálásához futtassa az alábbi parancsfájlok egyikét. Cserélje le a Azure Active Directory (Azure AD) tenantName, és Azure Resource Manager a végpontok értékeit a saját környezeti konfigurációjával.

[!include[Remove Account](../../includes/remove-account.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name.
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Csatlakozás az AD FS-sel

Csatlakozás az Azure Stack-üzemeltető környezet, a PowerShell-lel az Azure Active Directory összevont szolgáltatások (Azure AD FS). A ASDK esetében ez az Azure Resource Manager-végpont `https://adminmanagement.local.azurestack.external`ra van beállítva. Az Azure Stack integrált rendszerek az Azure Resource Manager-végpont beszerzéséhez forduljon a szolgáltatójához.

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment.
  Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
  ```

> [!Note]  
> AD FS csak a felhasználói identitásokkal való interaktív hitelesítést támogatja. Ha egy hitelesítőadat-objektumra van szükség, akkor egy egyszerű szolgáltatásnevet (SPN) kell használnia. Az Azure Stack és a AD FS identitás-kezelési szolgáltatásként való létrehozásával kapcsolatos további információkért lásd: [AD FS egyszerű szolgáltatás kezelése](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Most, hogy mindent beállítottunk beállításról, belül az Azure Stack-erőforrások létrehozása a PowerShell használatával. Létrehozhat például egy erőforráscsoportot az alkalmazáshoz, és hozzáadhat egy virtuális gépet. A következő parancs használatával hozzon létre egy **MyResourceGroup**nevű erőforráscsoportot.

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>További lépések

- [Sablonok fejlesztése a Azure Stackhoz](../user/azure-stack-develop-templates.md).
- [Sablonok üzembe helyezése a PowerShell](../user/azure-stack-deploy-template-powershell.md)-lel.
  - [Azure stack modul-hivatkozás](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
