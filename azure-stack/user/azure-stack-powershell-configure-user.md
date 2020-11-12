---
title: Kapcsolódás Azure Stack hubhoz a PowerShell-lel felhasználóként
description: Megtudhatja, hogyan csatlakozhat Azure Stack hubhoz a PowerShell-lel az interaktív prompt vagy az írási parancsfájlok használatához.
author: mattbriggs
ms.topic: article
ms.date: 8/4/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 8/4/2020
ms.openlocfilehash: 717eb2cf7ea9ee15c528059462a6f7553bba53e2
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94547041"
---
# <a name="connect-to-azure-stack-hub-with-powershell-as-a-user"></a>Kapcsolódás Azure Stack hubhoz a PowerShell-lel felhasználóként

Az Azure Stack hub-erőforrások kezeléséhez a PowerShell használatával csatlakozhat Azure Stack hubhoz. A PowerShell használatával például előfizethet az ajánlatokra, virtuális gépeket hozhat létre, és Azure Resource Manager sablonokat telepíthet.

A telepítő beszerzése:
  - Győződjön meg arról, hogy rendelkezik a követelményekkel.
  - Kapcsolódjon Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) szolgáltatáshoz. 
  - Erőforrás-szolgáltatók regisztrálása.
  - Tesztelje a kapcsolatot.

## <a name="prerequisites-to-connecting-with-powershell"></a>A PowerShell-lel való csatlakozás előfeltételei

Konfigurálja ezeket az előfeltételeket a [fejlesztői készletből](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)vagy egy Windows-alapú külső ügyfélről, ha [VPN-kapcsolaton keresztül csatlakozik](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn):

* Telepítse [Azure stack hub-kompatibilis Azure PowerShell modulokat](../operator/powershell-install-az-module.md).
* Töltse le az [Azure stack hub használatához szükséges eszközöket](../operator/azure-stack-powershell-download.md).

Ügyeljen arra, hogy a következő parancsfájl-változókat a Azure Stack hub-konfiguráció értékeit cserélje le:

- **Azure AD-bérlő neve**  
  Az Azure Stack hub felügyeletéhez használt Azure AD-bérlő neve. Például: yourdirectory.onmicrosoft.com.
- **Azure Resource Manager végpont**  
  A Azure Stack Development Kit esetében ez az érték a következőre van beállítva: `https://management.local.azurestack.external` . Ha ezt az értéket szeretné lekérni Azure Stack hub integrált rendszerek esetében, forduljon a szolgáltatóhoz.

## <a name="connect-to-azure-stack-hub-with-azure-ad"></a>Kapcsolódás Azure Stack hubhoz az Azure AD-vel

```powershell  
    Add-AzEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-to-azure-stack-hub-with-ad-fs"></a>Kapcsolódás Azure Stack hubhoz AD FS

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance
  Add-AzEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzAccount -EnvironmentName "AzureStackUser"
  ```

## <a name="register-resource-providers"></a>Erőforrás-szolgáltatók regisztrálása

Az erőforrás-szolgáltatók nincsenek automatikusan regisztrálva olyan új felhasználói előfizetésekhez, amelyek nem rendelkeznek a portálon keresztül telepített erőforrásokkal. A következő parancsfájl futtatásával explicit módon regisztrálhat egy erőforrás-szolgáltatót:

```powershell  
foreach($s in (Get-AzSubscription)) {
        Select-AzSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzResourceProvider -ListAvailable | Register-AzResourceProvider
    }
```

[!Include [AD FS only supports interactive authentication with user identities](../includes/note-powershell-adfs.md)]

## <a name="test-the-connectivity"></a>Kapcsolat tesztelése

Ha minden beállítással rendelkezik, tesztelje a kapcsolatot a PowerShell használatával a Azure Stack hub erőforrásainak létrehozásához. Tesztként hozzon létre egy erőforráscsoportot egy alkalmazáshoz, és adjon hozzá egy virtuális gépet. Futtassa a következő parancsot egy "MyResourceGroup" nevű erőforráscsoport létrehozásához:

```powershell  
New-AzResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Következő lépések

- [Sablonok fejlesztése Azure Stack hubhoz](azure-stack-develop-templates.md)
- [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
- [Azure Stack hub PowerShell-modul referenciája](/powershell/azure/azure-stack/overview)
- Ha be szeretné állítani a PowerShellt a felhőalapú kezelői környezethez, tekintse meg az [Azure stack hub-kezelő PowerShell-környezetének konfigurálása](../operator/azure-stack-powershell-configure-admin.md) című cikket.
