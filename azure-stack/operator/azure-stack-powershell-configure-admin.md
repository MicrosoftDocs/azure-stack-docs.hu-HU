---
title: Csatlakozhat az Azure Stack a PowerShell-lel kezelőként |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az Azure Stack-kezelőként PowerShell használatával
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
origin.date: 03/15/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: thoroet
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 9d49727538f89e9429c1ae979057e89c40dc0ce9
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64293926"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Csatlakozás Azure Stack PowerShell-kezelőként

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack, például az ajánlatok, tervek, kvóták és riasztások létrehozása-erőforrások kezelése a PowerShell segítségével konfigurálhatja. Ez a témakör segít az üzemeltető környezet konfigurálásához.

## <a name="prerequisites"></a>Előfeltételek

Futtassa a következő előfeltételek vonatkoznak, az a [development Kitet](../asdk/asdk-connect.md#connect-with-rdp) vagy -ügyfélről Windows-alapú külső Ha Ön [csatlakozik a VPN-kapcsolaton keresztül ASDK](../asdk/asdk-connect.md#connect-with-vpn). 

 - Telepítés [Azure Stack-kompatibilis Azure PowerShell-modulok](azure-stack-powershell-install.md).  
 - Töltse le a [az Azure Stack működéséhez szükséges eszközök](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Az Azure AD Connect

Az Azure Stack-üzemeltető környezet konfigurálása a PowerShell használatával. Futtassa az alábbi parancsfájlok egyikét: Az Azure Active Directory (Azure AD) tenantName és Azure Resource Manager-végpont értékeket cserélje le a saját környezetének konfigurációját. 

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.partner.onmschina.cn"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Csatlakozás az AD FS-sel

Csatlakozás az Azure Stack-üzemeltető környezet, a PowerShell-lel az Azure Active Directory összevont szolgáltatások (Azure AD FS). Az Azure Stack development Kitet, az Azure Resource Manager-végpont beállítása `https://adminmanagement.local.azurestack.external`. Az Azure Stack integrált rendszerek az Azure Resource Manager-végpont beszerzéséhez forduljon a szolgáltatójához.


  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
  ```

> [!Note]  
> Az AD FS csak támogatja az interaktív hitelesítéshez a felhasználói identitásokat. Ha egy hitelesítőadat-objektumnak szükség, egy szolgáltatásnév (SPN) kell használnia. Egy egyszerű szolgáltatást az Azure Stack és az AD FS, az identity management-szolgáltatás beállításának további információkért lásd: [kezelés egyszerű szolgáltatást az AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Most, hogy mindent beállítottunk beállításról, belül az Azure Stack-erőforrások létrehozása a PowerShell használatával. Például hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. Az alábbi parancs segítségével hozzon létre egy erőforráscsoportot **MyResourceGroup**.

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>További lépések

- [Sablonok fejlesztése az Azure Stackhez](../user/azure-stack-develop-templates.md)
- [Sablonok üzembe helyezése a PowerShell-lel](../user/azure-stack-deploy-template-powershell.md)
  - [Az Azure Stack-modulokra vonatkozó referenciák](https://docs.microsoft.com/powershell/azure/azure-stack/overview)  
