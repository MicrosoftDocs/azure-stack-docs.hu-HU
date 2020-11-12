---
title: Az Azure Stack hub házirend moduljának használata
description: Ismerje meg, hogyan kényszerítheti az Azure-előfizetést úgy, hogy az Azure Stack hub-előfizetéshez hasonlóan viselkedjen
author: sethmanheim
ms.topic: article
ms.date: 06/09/2020
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: ca96de45f50f48b91dbb2e6e8679df5dedab8d8f
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94547058"
---
# <a name="manage-azure-policy-using-the-azure-stack-hub-policy-module"></a>Az Azure-szabályzat kezelése az Azure Stack hub házirend moduljának használatával

Az Azure Stack hub házirend modul lehetővé teszi egy Azure-előfizetés konfigurálását ugyanazzal a verziószámozással és szolgáltatással, mint Azure Stack hub. A modul a [**New-AzPolicyDefinition PowerShell-**](/powershell/module/Az.resources/new-Azpolicydefinition) parancsmaggal létrehoz egy Azure-szabályzatot, amely korlátozza az előfizetésben elérhető erőforrástípusok és szolgáltatások használatát. Ezután létrehoz egy szabályzat-hozzárendelést a megfelelő hatókörön belül a [**New-AzPolicyAssignment**](/powershell/module/Az.resources/new-Azpolicyassignment) parancsmag használatával. A házirend konfigurálása után az Azure-előfizetéssel fejlesztheti Azure Stack hub-ra irányuló alkalmazásokat.

## <a name="install-the-module"></a>A modul telepítése

1. Telepítse az az PowerShell modul szükséges verzióját az [Azure stack hub PowerShell-telepítésének](../operator/powershell-install-az-module.md)1. lépésében leírtak szerint.
2. [Töltse le az Azure stack hub-eszközöket a githubról](../operator/azure-stack-powershell-download.md).
3. [Konfigurálja a powershellt Azure stack hubhoz való használatra](azure-stack-powershell-configure-user.md).
4. Importálja a **AzureStack. Policy. psm1** modult:

   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Szabályzat alkalmazása az Azure-előfizetésre

Az alábbi parancsokkal telepítheti az alapértelmezett Azure Stack hub-szabályzatot az Azure-előfizetésre. A parancsok futtatása előtt cserélje le az értékét `Azure subscription name` Az Azure-előfizetés nevére:

```powershell
Add-AzAccount
$s = Select-AzSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>Házirend alkalmazása erőforráscsoporthoz

Előfordulhat, hogy olyan házirendeket szeretne alkalmazni, amelyek részletesebbek. Előfordulhat például, hogy az adott előfizetésben futó más erőforrásokkal is rendelkezik. A házirend-alkalmazást egy adott erőforráscsoporthoz is felhasználhatja, amely lehetővé teszi, hogy az Azure-erőforrások használatával tesztelje az alkalmazásait Azure Stack hubhoz. A következő parancsok futtatása előtt cserélje le az értékét `Azure subscription name` Az Azure-előfizetés nevére:

```powershell
Add-AzAccount
$rgName = 'myRG01'
$s = Select-AzSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>Házirend működés közben

Miután telepítette az Azure-szabályzatot, hibaüzenet jelenik meg, amikor olyan erőforrást próbál telepíteni, amelyet a szabályzat tiltott:

![Erőforrás-telepítési hiba eredménye a házirend megkötése miatt](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Következő lépések

* [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
* [Sablonok üzembe helyezése az Azure CLI-vel](azure-stack-deploy-template-command-line.md)
* [Sablonok üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
