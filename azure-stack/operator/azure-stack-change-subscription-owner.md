---
title: Azure Stack felhasználói előfizetés számlázási tulajdonosának módosítása | Microsoft Docs
description: Megtudhatja, hogyan módosíthatja a Azure Stack felhasználói előfizetés számlázási tulajdonosát.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: justinha
ms.reviewer: shnatara
ms.lastreviewed: 10/19/2018
ms.openlocfilehash: 3c4453974092fd1873e5f77b1074c82851fc1be1
ms.sourcegitcommit: c196463492732218d2474d3a964f88e995272c80
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71094370"
---
# <a name="change-the-billing-owner-for-an-azure-stack-user-subscription"></a>Azure Stack felhasználói előfizetés számlázási tulajdonosának módosítása

Azure Stack operátorok a PowerShell használatával módosíthatják a felhasználói előfizetések számlázási tulajdonosát. A tulajdonos módosításának egyik oka például egy olyan felhasználó cseréje, amely elhagyja a szervezetet.

Az előfizetéshez két típusú *tulajdonos* van rendelve:

- **Számlázási tulajdonos**: Alapértelmezés szerint a számlázási tulajdonos az a felhasználói fiók, amely lekéri az előfizetést egy ajánlatból, majd az előfizetéshez tartozó számlázási kapcsolatot birtokolja. Ez a fiók az előfizetés rendszergazdája is. Egy előfizetéshez csak egy felhasználói fiók tartozhat. A számlázási tulajdonos gyakran szervezet vagy csapat érdeklődő.

  Használhatja a [set-AzsUserSubscription PowerShell-](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) parancsmagot a számlázási tulajdonos módosításához.  

- **RBAC szerepkörökkel hozzáadott tulajdonosok** – a további felhasználók a **tulajdonos** szerepkört a [szerepköralapú hozzáférés-vezérlés](azure-stack-manage-permissions.md) (RBAC) használatával biztosíthatják. Tetszőleges számú további felhasználói fiókot adhat hozzá tulajdonosként a számlázási tulajdonoshoz. A további tulajdonosok az előfizetés rendszergazdái is, és minden jogosultsággal rendelkeznek az előfizetéshez, kivéve a számlázási tulajdonos törlésére vonatkozó engedélyt.

  A PowerShell használatával további tulajdonosokat is kezelhet. További információkért tekintse meg [ezt a cikket](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>A számlázási tulajdonos módosítása

Futtassa a következő szkriptet a felhasználói előfizetés számlázási tulajdonosának módosításához. A parancsfájl futtatásához használt számítógépnek csatlakoznia kell Azure Stackhoz, és futtatnia kell a Azure Stack PowerShell-modul 1.3.0 vagy újabb verzióját. További információ: [Install Azure stack PowerShell](azure-stack-powershell-install.md).

>[!NOTE]
>Több-bérlős Azure Stack esetében az új tulajdonosnak ugyanabban a címtárban kell lennie, mint a meglévő tulajdonosnak. Ahhoz, hogy az előfizetés tulajdonjogát egy másik címtárbeli felhasználó számára is meg tudja adni, először meg kell [hívnia a felhasználót vendégként a címtárba](/azure/active-directory/b2b/add-users-administrator).

A futtatása előtt cserélje le a következő értékeket a parancsfájlba:

- **$ArmEndpoint**: A környezet Resource Manager-végpontja.
- **$TenantId**: A bérlő azonosítója.
- **$SubscriptionId**: Az előfizetés azonosítója.
- **$OwnerUpn**: Egy fiók, például a **felhasználó\@example.com**, hogy új számlázási tulajdonosként adja hozzá.

```powershell
# Set up Azure Stack admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

[!include[Remove Account](../../includes/remove-account.md)]

## <a name="next-steps"></a>További lépések

- [Szerepköralapú Access Control kezelése](azure-stack-manage-permissions.md)
