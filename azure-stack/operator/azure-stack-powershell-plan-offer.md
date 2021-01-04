---
title: Az előfizetések, csomagok és ajánlatok kezelése a PowerShell használatával Azure Stack hub-ban
description: Előfizetések, csomagok és ajánlatok kezelése Azure Stack hub PowerShell-lel.
author: PatAltimore
ms.topic: how-to
ms.date: 12/18/2020
ms.author: patricka
ms.lastreviewed: 12/18/2020
ms.reviewer: bganapa
ms.openlocfilehash: 3dcc3eae565f2e2a41078c2696a09f60ffa618b9
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697846"
---
# <a name="use-powershell-to-manage-subscriptions-plans-and-offers-in-azure-stack-hub"></a>Az előfizetések, csomagok és ajánlatok kezelése a PowerShell használatával Azure Stack hub-ban

A PowerShell használatával szolgáltatásokat állíthat be és biztosíthat szolgáltatások számára az ajánlatok, csomagok és előfizetések használatával. a PowerShell használatával. A PowerShell Azure Stack hub-on való beállításával kapcsolatos utasításokért lásd: a [PowerShell telepítése az Azure stack hub](powershell-install-az-module.md)-hoz készült modul. További információ a Azure Stack hub PowerShell-lel való csatlakoztatásáról: [csatlakozás Azure stack hubhoz a PowerShell](azure-stack-powershell-configure-admin.md)használatával.

Mielőtt elkezdené, ellenőrizze, hogy be van-e töltve az Azure Stack hub PowerShell-modulja. A PowerShell-konzolon írja be a következőt: `Import-Module AzureStack` .

## <a name="create-a-plan"></a>Csomag létrehozása

A csomag létrehozásakor kvóták szükségesek. Használhat meglévő kvótákat, vagy létrehozhat új kvótákat. Például tárolási, számítási és hálózati kvóta létrehozásához használhatja a [New-AzsStorageQuota](/powershell/module/azs.storage.admin/new-azsstoragequota), a [New-AzsComputeQuota](/powershell/module/azs.compute.admin/new-azscomputequota)és a [New-AzsNetworkQuota](/powershell/module/azs.network.admin/new-azsnetworkquota) parancsmagokat:

```powershell
$serviceQuotas  = @()
$serviceQuotas += (New-AzsStorageQuota -Name "Example storage quota with defaults").Id
$serviceQuotas += (New-AzsComputeQuota -Name "Example compute quota with defaults").Id
$serviceQuotas += (New-AzsNetworkQuota -Name "Example network quota with defaults").Id
```

Egy alap-vagy kiegészítő csomag létrehozásához vagy frissítéséhez használja a [New-AzsPlan](/powershell/module/azs.subscriptions.admin/new-azsplan).

```powershell
$testPlan = New-AzsPlan -Name "testplan" -ResourceGroupName "testrg" -QuotaIds $serviceQuotas -Description "Test plan"
```

## <a name="create-an-offer"></a>Ajánlat létrehozása

Ajánlat létrehozásához használja a [New-AzsOffer](/powershell/module/azs.subscriptions.admin/new-azsoffer).

```powershell
New-AzsOffer -Name "testoffer" -ResourceGroupName "testrg" -BasePlanIds @($testPlan.Id)
```

Ha már van ajánlata, hozzáadhat terveket az ajánlathoz. Használja az [Add-AzsPlanToOffer](/powershell/module/azs.subscriptions.admin/add-azsplantooffer). A **-PlanLinkType** paraméter megkülönbözteti a csomag típusát.

```powershell
Add-AzsPlanToOffer -PlanName "addonplan" -PlanLinkType Addon -OfferName "testoffer" -ResourceGroupName "testrg" -MaxAcquisitionCount 18
```

Ha módosítani szeretné egy ajánlat állapotát, használja a [set-AzsOffer](/powershell/module/azs.subscriptions.admin/set-azsoffer) parancsmagot.

```powershell
$offer = Get-AzsAdminManagedOffer -Name "testoffer" -ResourceGroupName "testrg"
$offer.state = "Public"
$offer | Set-AzsOffer -Confirm:$false
```

## <a name="create-subscription-to-an-offer"></a>Előfizetés létrehozása az ajánlathoz

Miután létrehozott egy ajánlatot, a felhasználóknak elő kell fizetniük rá, hogy használhassák. A felhasználók kétféleképpen fizethetnek elő az ajánlatra:

* Felhőbeli kezelőként létrehozhat egy előfizetést a felhasználó számára. A létrehozott előfizetések nyilvános és személyes ajánlatok részét is képezhetik.
* Felhasználóként előfizethet nyilvános ajánlatra.

Ha felhőalapú operátorként szeretne előfizetést létrehozni egy felhasználóhoz, használja a [New-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/new-azsusersubscription).

```powershell
New-AzsUserSubscription -Owner "user@contoso.com" -DisplayName "User subscription" -OfferId "/subscriptions/<Subscription ID>/resourceGroups/testrg/providers/Microsoft.Subscriptions.Admin/offers/testoffer"
```

Ha egy nyilvános ajánlatra szeretne előfizetni felhasználóként, használja a [New-AzsSubscription](/powershell/module/azs.subscriptions/new-azssubscription). A *New-AzsSubscription* kapcsolódnia kell a felhasználói Azure Resource Manager környezethez. Használja a [kapcsolódás Azure stack hubhoz a PowerShell használatával,](azure-stack-powershell-configure-admin.md) de használja a felhasználói Azure Resource Manager végpontot. Például: `Add-AzEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"`.

```powershell
$testOffer = Get-AzsOffer | Where-Object Name -eq "testoffer"
New-AzsSubscription -OfferId "User subscription" -OfferId $testOffer.Id -DisplayName "My subscription"
```

## <a name="delete-quotas-plans-offers-and-subscriptions"></a>Kvóták, csomagok, ajánlatok és előfizetések törlése

A Azure Stack hub-kvóták,-csomagok,-ajánlatok és-előfizetések törléséhez kiegészítő PowerShell-parancsmagok tartoznak. Az alábbi példák mindegyikre mutatnak példát.

A [Remove-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/remove-azsusersubscription) használatával távolítsa el az előfizetést egy ajánlatból.

```powershell
Remove-AzsUserSubscription -TargetSubscriptionId "c90173b1-de7a-4b1d-8600-b8325ca1eab1e"
```

Ha el szeretne távolítani egy csomagot az ajánlatból, használja a [Remove-AzsPlanFromOffer](/powershell/module/azs.subscriptions.admin/remove-azsplanfromoffer).

```powershell
Remove-AzsPlanFromOffer -PlanName "addonplan" -PlanLinkType Addon -OfferName "testoffer" -ResourceGroupName "testrg"
Remove-AzsPlanFromOffer -PlanName "testplan" -PlanLinkType Base -OfferName "testoffer" -ResourceGroupName "testrg"
```

A [Remove-AzsPlan](/powershell/module/azs.subscriptions.admin/remove-azsplan) használatával távolítsa el a csomagot.

```powershell
Remove-AzsPlan -Name "testplan" -ResourceGroupName "testrg"
```

Egy ajánlat eltávolításához használja a [Remove-AzsOffer](/powershell/module/azs.subscriptions.admin/remove-azsoffer) .

```powershell
Remove-AzsOffer -Name "testoffer" -ResourceGroupName "testrg"
```

A kvóták eltávolításához használja a Remove- [AzsStorageQuota](/powershell/module/azs.storage.admin/remove-azsstoragequota), Remove- [AzsComputeQuota](/powershell/module/azs.compute.admin/remove-azscomputequota), [Remove-AzsNetworkQuota](/powershell/module/azs.network.admin/remove-azsnetworkquota) parancsot.

```powershell
Remove-AzsStorageQuota -Name "Example storage quota with defaults"
Remove-AzsComputeQuota -Name "Example compute quota with defaults"
Remove-AzsNetworkQuota -Name "Example network quota with defaults"
```

## <a name="next-steps"></a>További lépések

- [Frissítések kezelése Azure Stack központban](./azure-stack-updates.md)
