---
title: Csatlakozhat az Azure Stack felhasználói PowerShell használatával |} A Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az Azure Stack PowerShell használatával.
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
ms.date: 04/26/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: c9ef9c1e936c71a8b0a2a0eb636da1eac5bf69da
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/24/2019
ms.locfileid: "66197328"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Csatlakozás a PowerShell-lel az Azure Stack felhasználói

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Azure Stack az Azure Stack-erőforrások kezelése PowerShell használatával csatlakozhat. Ha például a PowerShell használatával is előfizethetnek azokra, virtuális gépek (VM) létrehozása és üzembe helyezése Azure Resource Manager-sablonok.

A telepítő lekérése:
  - Győződjön meg arról, hogy a követelmények.
  - Csatlakozás az Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS). 
  - Erőforrás-szolgáltatók regisztrálásával.
  - Tesztelje a kapcsolatot.

## <a name="prerequisites-to-connecting-with-powershell"></a>A PowerShell használatával kapcsolódik előfeltételei

Ezek az Előfeltételek konfigurálása az [development Kitet](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp), vagy egy Windows-alapú külső ügyfél Ha [VPN-kapcsolaton keresztül csatlakozó](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn):

* Telepítés [Azure Stack-kompatibilis Azure PowerShell-modulok](../operator/azure-stack-powershell-install.md).
* Töltse le a [az Azure Stack működéséhez szükséges eszközök](../operator/azure-stack-powershell-download.md).

Győződjön meg arról, hogy a következő parancsfájl-változókat cserélje az Azure Stack-konfigurációból értékeket:

- **Az Azure AD-bérlő neve**  
  Az Azure Stack kezelésére szolgáló Azure AD-bérlő neve. Ha például yourdirectory.onmicrosoft.com.
- **Az Azure Resource Manager-végpont**  
  Az Azure Stack development Kitet, az alapérték https://management.local.azurestack.external. Az Azure Stack integrált rendszerek Ez az érték beszerzéséhez forduljon a szolgáltatójához.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Csatlakozás az Azure Stack az Azure ad-vel

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

## <a name="connect-to-azure-stack-with-ad-fs"></a>Csatlakozás az Azure Stack az AD FS-sel

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackUser"
  ```

## <a name="register-resource-providers"></a>Erőforrás-szolgáltató regisztrálása

Erőforrás-szolgáltatók automatikusan az új felhasználói előfizetések, amelyek nem rendelkeznek olyan erőforrások a portálon keresztül telepített aspektusnevek regisztrálva. Erőforrás-szolgáltató explicit módon regisztrálhatja a következő szkript futtatásával:

```powershell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider
    }
```

## <a name="test-the-connectivity"></a>A kapcsolat tesztelése

Ha van minden, teszt kapcsolat beállítása az Azure Stack-erőforrások létrehozása a PowerShell használatával. Egy tesztet hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális Gépet. A következő paranccsal hozzon létre egy erőforráscsoportot "MyResourceGroup" nevű:

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>További lépések

- [Sablonok fejlesztése az Azure Stackhez](azure-stack-develop-templates.md)
- [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
- [Az Azure Stack PowerShell-Modulreferencia](https://docs.microsoft.com/powershell/azure/azure-stack/overview)
- Ha a PowerShell beállítása a felhőkörnyezet operátor szeretne, olvassa el a [konfigurálása a PowerShell-környezet az Azure Stack-operátorokról](../operator/azure-stack-powershell-configure-admin.md) cikk.
