---
title: Az Azure Stack hub Policy modul használata | Microsoft Docs
description: Ismerje meg, hogyan kényszerítheti az Azure-előfizetést úgy, hogy az Azure Stack hub-előfizetéshez hasonlóan viselkedjen
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: aedc2915f3e2be473b74766c4274197112ecf6fe
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75878747"
---
# <a name="manage-azure-policy-using-the-azure-stack-hub-policy-module"></a>Az Azure-szabályzat kezelése az Azure Stack hub házirend moduljának használatával

Az Azure Stack hub házirend modul lehetővé teszi egy Azure-előfizetés konfigurálását ugyanazzal a verziószámozással és szolgáltatással, mint Azure Stack hub. A modul a [**New-AzureRmPolicyDefinition PowerShell-** ](/powershell/module/azurerm.resources/new-azurermpolicydefinition) parancsmaggal létrehoz egy Azure-szabályzatot, amely korlátozza az előfizetésben elérhető erőforrástípusok és szolgáltatások használatát. Ezután létrehoz egy szabályzat-hozzárendelést a megfelelő hatókörön belül a [**New-AzureRmPolicyAssignment**](/powershell/module/azurerm.resources/new-azurermpolicyassignment) parancsmag használatával. A házirend konfigurálása után az Azure-előfizetéssel fejlesztheti Azure Stack hub-ra irányuló alkalmazásokat.

## <a name="install-the-module"></a>A modul telepítése

1. Telepítse a AzureRM PowerShell-modul szükséges verzióját az [Azure stack hub PowerShell-telepítésének](../operator/azure-stack-powershell-install.md)1. lépésében leírtak szerint.
2. [Töltse le az Azure stack hub-eszközöket a githubról](../operator/azure-stack-powershell-download.md).
3. [Konfigurálja a powershellt Azure stack hubhoz való használatra](azure-stack-powershell-configure-user.md).
4. Importálja a **AzureStack. Policy. psm1** modult:

   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Szabályzat alkalmazása az Azure-előfizetésre

Az alábbi parancsok segítségével alkalmazhatja az alapértelmezett Azure Stack hub-szabályzatot az Azure-előfizetéséhez. A parancsok futtatása előtt cserélje le az `Azure subscription name` az Azure-előfizetés nevére:

```powershell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>Házirend alkalmazása erőforráscsoporthoz

Előfordulhat, hogy olyan házirendeket szeretne alkalmazni, amelyek részletesebbek. Előfordulhat például, hogy az adott előfizetésben futó más erőforrásokkal is rendelkezik. A házirend-alkalmazást egy adott erőforráscsoporthoz is felhasználhatja, amely lehetővé teszi, hogy az Azure-erőforrások használatával tesztelje az alkalmazásait Azure Stack hubhoz. A következő parancsok futtatása előtt cserélje le az `Azure subscription name` az Azure-előfizetés nevére:

```powershell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>Házirend működés közben

Miután telepítette az Azure-szabályzatot, hibaüzenet jelenik meg, amikor olyan erőforrást próbál telepíteni, amelyet a szabályzat tiltott:

![Erőforrás-telepítési hiba eredménye a házirend megkötése miatt](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Következő lépések

* [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
* [Sablonok üzembe helyezése az Azure CLI-vel](azure-stack-deploy-template-command-line.md)
* [Sablonok üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
