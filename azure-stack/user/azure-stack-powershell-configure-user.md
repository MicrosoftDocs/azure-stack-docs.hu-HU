---
title: Csatlakozhat az Azure Stack felhasználói PowerShell használatával |} A Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat a Azure Stackhoz a PowerShell használatával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 6a75eb788afd84b6619326293ae2399d8ed5b0e1
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824208"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Csatlakozás a PowerShell-lel az Azure Stack felhasználói

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Azure Stack-erőforrások kezeléséhez csatlakozhat a Azure Stackhoz a PowerShell használatával. A PowerShell használatával például előfizethet az ajánlatokra, virtuális gépeket hozhat létre, és Azure Resource Manager sablonokat telepíthet.

A telepítő beszerzése:
  - Győződjön meg arról, hogy a követelmények.
  - Csatlakozás az Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS). 
  - Erőforrás-szolgáltatók regisztrálásával.
  - Tesztelje a kapcsolatot.

## <a name="prerequisites-to-connecting-with-powershell"></a>A PowerShell-lel való csatlakozás előfeltételei

Konfigurálja ezeket az előfeltételeket a [fejlesztői készletből](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)vagy egy Windows-alapú külső ügyfélről, ha [VPN-kapcsolaton keresztül csatlakozik](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn):

* Telepítés [Azure Stack-kompatibilis Azure PowerShell-modulok](../operator/azure-stack-powershell-install.md).
* Töltse le a [az Azure Stack működéséhez szükséges eszközök](../operator/azure-stack-powershell-download.md).

Győződjön meg arról, hogy a következő parancsfájl-változókat cserélje az Azure Stack-konfigurációból értékeket:

- **Az Azure AD-bérlő neve**  
  Az Azure Stack kezeléséhez használt Azure AD-bérlő neve. Például: yourdirectory.onmicrosoft.com.
- **Az Azure Resource Manager-végpont**  
  Az Azure Stack development Kitet, az alapérték https://management.local.azurestack.external. Az Azure Stack integrált rendszerek Ez az érték beszerzéséhez forduljon a szolgáltatójához.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Kapcsolódás Azure Stack az Azure AD-vel

```powershell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-to-azure-stack-with-ad-fs"></a>Kapcsolódás Azure Stackhoz AD FS

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackUser"
  ```

## <a name="register-resource-providers"></a>Erőforrás-szolgáltató regisztrálása

Az erőforrás-szolgáltatók nincsenek automatikusan regisztrálva olyan új felhasználói előfizetésekhez, amelyek nem rendelkeznek a portálon keresztül telepített erőforrásokkal. Erőforrás-szolgáltató explicit módon regisztrálhatja a következő szkript futtatásával:

```powershell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider
    }
```

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Ha minden beállítással rendelkezik, tesztelje a kapcsolatot a PowerShell használatával a Azure Stack erőforrásainak létrehozásához. Tesztként hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. A következő paranccsal hozzon létre egy erőforráscsoportot "MyResourceGroup" nevű:

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>További lépések

- [Sablonok fejlesztése az Azure Stackhez](azure-stack-develop-templates.md)
- [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
- [Azure Stack PowerShell-modul referenciája](https://docs.microsoft.com/powershell/azure/azure-stack/overview)
- Ha a PowerShell beállítása a felhőkörnyezet operátor szeretne, olvassa el a [konfigurálása a PowerShell-környezet az Azure Stack-operátorokról](../operator/azure-stack-powershell-configure-admin.md) cikk.
