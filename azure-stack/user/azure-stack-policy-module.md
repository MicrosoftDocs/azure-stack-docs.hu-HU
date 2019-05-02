---
title: Az Azure Stack-házirendmodul használata |} A Microsoft Docs
description: Ismerje meg, hogyan korlátozhatja a viselkednek, mint az Azure Stack-előfizetéshez az Azure-előfizetés
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
ms.date: 03/26/2019
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: 1ae0c24fe5f3a130c7878c8623facada222a0e49
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64299668"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Az Azure policy az Azure Stack irányelvmodul használata kezelheti

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack irányelvmodul lehetővé teszi az ugyanazon verziókezelés és szolgáltatás rendelkezésre állása, mint az Azure Stack Azure-előfizetés konfigurálása. A modul használja a [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) PowerShell-parancsmag segítségével hozzon létre egy Azure szabályzatot, amely korlátozza az erőforrástípusok és a egy előfizetésben elérhető szolgáltatások. Ezután használatával hoz létre szabályzat-hozzárendelés, a megfelelő hatókörén belül a [New-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) parancsmagot. Az-házirend konfigurálása után az Azure Stack-alkalmazások fejlesztéséhez használhatja az Azure-előfizetés.

## <a name="install-the-module"></a>A modul telepítése

1. Az AzureRM PowerShell-modult a szükséges verzió telepítéséhez, az 1. lépésben leírtak szerint [Azure Stack PowerShell telepítése](../operator/azure-stack-powershell-install.md).
2. [Az Azure Stack-eszközök letöltése a githubról](../operator/azure-stack-powershell-download.md).
3. [A PowerShell konfigurálása az Azure Stackkel való használathoz](azure-stack-powershell-configure-user.md).
4. A AzureStack.Policy.psm1 modul importálása:


   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Alkalmazza a szabályzatot az Azure-előfizetés

A következő parancsot használhatja az Azure Stack alapértelmezett szabályzat alkalmazásához ellen az Azure-előfizetés. A parancs futtatása előtt cserélje le a `Azure subscription name` az Azure-előfizetése nevére:

```powershell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>Alkalmazza a szabályzatot egy erőforráscsoportot

Érdemes a alkalmazni a házirendeket, amelyek részletesebben. Tegyük fel lehetséges, hogy ugyanahhoz az előfizetéshez futtatott egyéb erőforrásokat. A házirend-alkalmazást egy adott erőforráscsoportban, amely lehetővé teszi, hogy tesztelje alkalmazásait az Azure Stack használatával az Azure-erőforrások is körét. A következő parancs futtatása előtt cserélje le a `Azure subscription name` az Azure-előfizetése nevére:

```powershell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>A művelet házirend

Miután üzembe helyezte az Azure policy, hibaüzenetet kap olyan erőforrások, amelyek tiltják a szabályzat üzembe helyezése közben:

![Erőforrás-központitelepítési hiba miatt házirendkorlátozás eredménye](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>További lépések

* [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
* [Az Azure CLI-vel sablonok üzembe helyezése](azure-stack-deploy-template-command-line.md)
* [Sablonok üzembe helyezése Visual studióval](azure-stack-deploy-template-visual-studio.md)
