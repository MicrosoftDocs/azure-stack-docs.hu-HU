---
title: Azure Stack hub felhasználói előfizetés számlázási tulajdonosának módosítása
description: Megtudhatja, hogyan módosíthatja egy Azure Stack hub felhasználói előfizetés számlázási tulajdonosát.
author: PatAltimore
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: patricka
ms.reviewer: shnatara
ms.lastreviewed: 02/11/2021
ms.openlocfilehash: c612ace63515a4df8c8195cfdd1e58797dcd9ba2
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100647844"
---
# <a name="change-the-billing-owner-for-an-azure-stack-hub-user-subscription"></a>Azure Stack hub felhasználói előfizetés számlázási tulajdonosának módosítása

Azure Stack hub-kezelők a PowerShell használatával módosíthatják a felhasználói előfizetések számlázási tulajdonosát. A tulajdonos módosításának egyik oka például egy olyan felhasználó cseréje, amely elhagyja a szervezetet.

Az előfizetéshez két típusú *tulajdonos* van rendelve:

- **Számlázási tulajdonos**: alapértelmezés szerint a számlázási tulajdonos az a felhasználói fiók, amely lekéri az előfizetést egy ajánlatból, majd az előfizetéshez tartozó számlázási kapcsolatot birtokolja. Ez a fiók az előfizetés rendszergazdája is. Egy előfizetéshez csak egy felhasználói fiók tartozhat. A számlázási tulajdonos gyakran szervezet vagy csapat érdeklődő.

  Használhatja a [set-AzsUserSubscription PowerShell-](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) parancsmagot a számlázási tulajdonos módosításához.  

- **RBAC szerepkörökkel hozzáadott tulajdonosok** – a további felhasználók a **tulajdonos** szerepkört a [szerepköralapú hozzáférés-vezérlés](azure-stack-manage-permissions.md) (RBAC) használatával biztosíthatják. Tetszőleges számú további felhasználói fiókot adhat hozzá tulajdonosként a számlázási tulajdonoshoz. A további tulajdonosok az előfizetés rendszergazdái is, és minden jogosultsággal rendelkeznek az előfizetéshez, kivéve a számlázási tulajdonos törlésére vonatkozó engedélyt.

  A PowerShell használatával további tulajdonosokat is kezelhet. További információkért tekintse meg [ezt a cikket](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>A számlázási tulajdonos módosítása

Futtassa a következő szkriptet a felhasználói előfizetés számlázási tulajdonosának módosításához. A parancsfájl futtatásához használt számítógépnek csatlakoznia kell Azure Stack hubhoz, és futtatnia kell az Azure Stack hub PowerShell-modul 1.3.0 vagy újabb verzióját. További információ: [Install Azure stack hub PowerShell](powershell-install-az-module.md).

>[!NOTE]
>Több-bérlős Azure Stack hub esetében az új tulajdonosnak a meglévő tulajdonossal megegyező könyvtárban kell lennie. Ahhoz, hogy az előfizetés tulajdonjogát egy másik címtárbeli felhasználó számára is meg tudja adni, először meg kell [hívnia a felhasználót vendégként a címtárba](/azure/active-directory/b2b/add-users-administrator).

A futtatása előtt cserélje le a következő értékeket a parancsfájlba:

- **$ArmEndpoint**: a környezet Resource Manager-végpontja.
- **$TenantId**: a bérlő azonosítója.
- **$SubscriptionId**: az előfizetés azonosítója.
- **$OwnerUpn**: egy olyan fiók, például **felhasználói \@ example.com**, amelyet új számlázási tulajdonosként szeretne hozzáadni.

### <a name="az-modules"></a>[Az modulok](#tab/az)

```powershell
# Set up Azure Stack Hub admin environment
Add-AzEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Connect-AzAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
$Subscription | Set-AzsUserSubscription | fl *
```

[!include[Remove Account](../includes/remove-account-az.md)]

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm)

```powershell
# Set up Azure Stack Hub admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount  -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRMSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRMContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
$Subscription | Set-AzsUserSubscription | fl *
```
[!include[Remove Account](../includes/remove-account-azurerm.md)]
---

## <a name="next-steps"></a>Következő lépések

- [Role-Based kezelése Access Control](azure-stack-manage-permissions.md)
