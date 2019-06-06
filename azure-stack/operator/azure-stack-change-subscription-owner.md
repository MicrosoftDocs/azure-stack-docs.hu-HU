---
title: Az Azure Stack felhasználói előfizetés tulajdonosának frissítése |} A Microsoft Docs
description: Módosítsa a számlázási tulajdonosi az Azure Stack felhasználói előfizetések.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: sethm
ms.reviewer: shnatara
ms.lastreviewed: 10/19/2018
ms.openlocfilehash: 99f995941c4e7b09af70dff9391aeceb9a59844d
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691935"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Módosíthatja a tulajdonost az Azure Stack felhasználói előfizetés

Az Azure Stack-operátorok a PowerShell használatával módosíthatja a felhasználói előfizetés számlázási tulajdonosa. A tulajdonos módosítása egyik oka például, cserélje le a felhasználó elhagyja a szervezetet.

Két típusa van *tulajdonosok* előfizetéshez társított:

- **Számlázási tulajdonosi**: Alapértelmezés szerint a számlázási tulajdonosi a felhasználói fiók, amely beolvassa az előfizetés egy ajánlatra, és majd az adott előfizetéshez tartozó számlázási kapcsolat tulajdonosa. Ez a fiók akkor is az előfizetés rendszergazdája. Csak egy felhasználói fiók a megjelölés rendelkezhet egy adott előfizetés. A számlázási tulajdonosi gyakran egy szervezet vagy csapat vezető.

  A PowerShell-parancsmagot is használhatja [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) számlázási tulajdonosát a következőre módosítani.  

- **RBAC-szerepkörök keresztül hozzáadva tulajdonosok** – további felhasználók is megadható a **tulajdonosa** szerepkör [szerepköralapú hozzáférés-vezérlés](azure-stack-manage-permissions.md) (RBAC). Tetszőleges számú további felhasználói fiókokat a számlázási tulajdonosi segítőosztályok tulajdonosként is hozzáadhatók. További tulajdonosok is az előfizetés rendszergazdája, és minden az előfizetést, kivéve, törölheti a számlázási tulajdonosi jogosultságokkal rendelkeznek.

  További tulajdonosainak kezelése a PowerShell használatával is. További információkért tekintse meg [ezt a cikket](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>A számlázási tulajdonosi módosítása

Futtassa a következő parancsfájlt, módosíthatja a felhasználói előfizetés számlázási tulajdonosa. A számítógépen, amely a parancsfájl futtatásához használt kell csatlakozás az Azure Stack, és futtassa az Azure Stack PowerShell modul 1.3.0 vagy újabb. További információkért lásd: [telepítse az Azure Stack PowerShell](azure-stack-powershell-install.md).

>[!NOTE]
>Egy több-bérlős Azure Stack az új tulajdonos ugyanabban a könyvtárban, a meglévő tulajdonosának kell lennie. Mielőtt az előfizetés tulajdonjogának egy felhasználónak, amely egy másik címtárban, előbb futtatnia kell [meghívni, hogy a felhasználó vendégként a címtárba](/azure/active-directory/b2b/add-users-administrator).

Cserélje le a következő értékeket a szkriptben, futtatásuk előtt:

- **$ArmEndpoint**: A Resource Manager-végpontot a környezethez.
- **$TenantId**: A bérlő azonosítója.
- **$SubscriptionId**: Az előfizetés-azonosítójára.
- **$OwnerUpn**: Egy fiókot, például **felhasználói\@example.com**, az új számlázási tulajdonosként hozzáadni.

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

## <a name="next-steps"></a>További lépések

- [Szerepköralapú hozzáférés-vezérlés kezelése](azure-stack-manage-permissions.md)
