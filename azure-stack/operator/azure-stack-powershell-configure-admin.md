---
title: Csatlakozhat az Azure Stack a PowerShell-lel kezelőként |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az Azure Stack-kezelőként PowerShell használatával
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
ms.date: 09/18/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/18/2019
ms.openlocfilehash: 84799fa50411b753ae5983efbc743f07282a512b
ms.sourcegitcommit: c46d913ebfa4cb6c775c5117ac5c9e87d032a271
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101149"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Csatlakozás Azure Stack PowerShell-kezelőként

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Az Azure Stack, például az ajánlatok, tervek, kvóták és riasztások létrehozása-erőforrások kezelése a PowerShell segítségével konfigurálhatja. Ez a témakör segít az üzemeltető környezet konfigurálásához.

## <a name="prerequisites"></a>Előfeltételek

Futtassa a következő előfeltételeket a [fejlesztői készletből](../asdk/asdk-connect.md#connect-with-rdp) vagy egy Windows-alapú külső ügyfélről, ha [VPN-kapcsolaton keresztül csatlakozik a ASDK](../asdk/asdk-connect.md#connect-with-vpn). 

 - Telepítés [Azure Stack-kompatibilis Azure PowerShell-modulok](azure-stack-powershell-install.md).  
 - Töltse le a [az Azure Stack működéséhez szükséges eszközök](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Az Azure AD Connect

Az Azure Stack-üzemeltető környezet konfigurálása a PowerShell használatával. Futtassa a következő parancsfájlok egyikét: Cserélje le a Azure Active Directory (Azure AD) tenantName, és Azure Resource Manager a végpontok értékeit a saját környezeti konfigurációjával. 

[!include[Remove Account](../../includes/remove-account.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
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
> AD FS csak a felhasználói identitásokkal való interaktív hitelesítést támogatja. Ha egy hitelesítőadat-objektumra van szükség, egyszerű szolgáltatásnevet (SPN) kell használnia. Az Azure Stack és a AD FS identitás-kezelési szolgáltatásként való létrehozásával kapcsolatos további információkért lásd: [AD FS egyszerű szolgáltatás kezelése](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Most, hogy mindent beállítottunk beállításról, belül az Azure Stack-erőforrások létrehozása a PowerShell használatával. Például hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. Az alábbi parancs segítségével hozzon létre egy erőforráscsoportot **MyResourceGroup**.

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>További lépések

- [Sablonok fejlesztése az Azure Stackhez](../user/azure-stack-develop-templates.md)
- [Sablonok üzembe helyezése a PowerShell-lel](../user/azure-stack-deploy-template-powershell.md)
  - [Azure Stack modul leírása](https://docs.microsoft.com/powershell/azure/azure-stack/overview)  
