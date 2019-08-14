---
title: A Azure Stack Policy modul használata | Microsoft Docs
description: Megtudhatja, hogyan kényszerítheti az Azure-előfizetést úgy, hogy az Azure Stack-előfizetésre viselkedjen
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: c0872c598cc621250c3b2c5d39aca0e392f71b29
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991604"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Az Azure-szabályzat kezelése a Azure Stack házirend modul használatával

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Az Azure Stack házirend modul lehetővé teszi egy Azure-előfizetés konfigurálását ugyanazzal a verziószámozással és szolgáltatással, mint Azure Stack. A modul a [New-AzureRmPolicyDefinition PowerShell-](/powershell/module/azurerm.resources/new-azurermpolicydefinition) parancsmaggal létrehoz egy Azure-szabályzatot, amely korlátozza az előfizetésben elérhető erőforrástípusok és szolgáltatások használatát. Ezután létrehoz egy szabályzat-hozzárendelést a megfelelő hatókörön belül a [New-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) parancsmag használatával. A házirend konfigurálása után az Azure-előfizetéssel kifejlesztheti Azure Stack számára szánt alkalmazásokat.

## <a name="install-the-module"></a>A modul telepítése

1. Telepítse a AzureRM PowerShell-modul szükséges verzióját a [PowerShell telepítése a Azure Stackhoz](../operator/azure-stack-powershell-install.md)című témakör 1. lépésében leírtak szerint.
2. [Töltse le a Azure stack eszközöket](../operator/azure-stack-powershell-download.md)a githubról.
3. [Konfigurálja a powershellt Azure Stackhoz való használatra](azure-stack-powershell-configure-user.md).
4. Importálja a *AzureStack. Policy. psm1* modult:

   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Szabályzat alkalmazása az Azure-előfizetésre

Az alábbi parancsokkal alapértelmezett Azure Stack szabályzatot alkalmazhat az Azure-előfizetésére. A parancsok futtatása előtt cserélje le `Azure subscription name` az értékét az Azure-előfizetés nevére:

```powershell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>Házirend alkalmazása erőforráscsoporthoz

Előfordulhat, hogy olyan házirendeket szeretne alkalmazni, amelyek részletesebbek. Előfordulhat például, hogy az adott előfizetésben futó más erőforrásokkal is rendelkezik. A házirend alkalmazást egy adott erőforráscsoporthoz is felhasználhatja, amely lehetővé teszi az alkalmazások tesztelését Azure Stack Azure-erőforrások használatával. A következő parancsok futtatása előtt cserélje le `Azure subscription name` az értékét az Azure-előfizetés nevére:

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

## <a name="next-steps"></a>További lépések

* [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
* [Sablonok üzembe helyezése az Azure CLI-vel](azure-stack-deploy-template-command-line.md)
* [Sablonok üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
