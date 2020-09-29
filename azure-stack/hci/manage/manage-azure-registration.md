---
title: Azure-regisztráció kezelése Azure Stack HCI-hez
description: Az Azure-regisztráció kezelése Azure Stack HCI-hez és a regisztrációs állapot megértése a PowerShell használatával.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: 696ef552dcf49f31fb613a22393617e653f7e10d
ms.sourcegitcommit: eb91a28a19a74f799b093ae2a705f7f6e4c5cd49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87436469"
---
# <a name="manage-azure-registration"></a>Azure-regisztráció kezelése

> Azure Stack HCI-v20H2 vonatkozik

Miután létrehozott egy Azure Stack HCI-fürtöt, [regisztrálnia kell a fürtöt az Azure arc](../deploy/register-with-azure.md)-ban. A fürt regisztrálása után rendszeres időközönként szinkronizálja a helyszíni fürt és a felhő közötti információkat. Ez a témakör ismerteti, hogyan értelmezheti a regisztrációs állapotát, és törölheti a fürt regisztrációját, amikor készen áll a leszerelésre.

## <a name="understanding-registration-status"></a>A regisztrációs állapot ismertetése

A regisztrációs állapot megismeréséhez használja a `Get-AzureStackHCI` PowerShell-parancsmagot, valamint a `ClusterStatus` ,, `RegistrationStatus` és `ConnectionStatus` tulajdonságokat. Például az Azure Stack HCI operációs rendszer telepítése után, a fürt létrehozása vagy csatlakoztatása előtt a tulajdonság a `ClusterStatus` "még nem" állapotot jeleníti meg:

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="Azure regisztrációs állapot a fürt létrehozása előtt":::

A fürt létrehozása után a rendszer csak a `RegistrationStatus` "még nem" állapotot jeleníti meg:

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="Azure-beli regisztráció állapota a fürt létrehozása után":::

Azure Stack HCI-nek az Azure Online Services használati feltételeinek megfelelően regisztrálnia kell a telepítést követő 30 napon belül. Ha nem fürtözött 30 nap után, a a `ClusterStatus` következőt fogja látni `OutOfPolicy` , és ha nem regisztrálja magát 30 nap után, a fog megjelenni `RegistrationStatus` `OutOfPolicy` .

A fürt regisztrálása után megtekintheti a `ConnectionStatus` és az `LastConnected` időt, amely általában az elmúlt napon belül van, kivéve, ha a fürt átmenetileg le van választva az internetről. Az Azure Stack HCI-fürtök akár 30 egymást követő napig is működhetnek teljesen offline állapotban.

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="Az Azure regisztrációs állapota Regisztráció után":::

Ha túllépi a maximális időtartamot, a `ConnectionStatus` fog megjelenni `OutOfPolicy` .

## <a name="azure-active-directory-permissions"></a>Engedélyek Azure Active Directory

Az előfizetéshez tartozó Azure-erőforrások létrehozása mellett a Azure Stack HCI-regisztrációja egy alkalmazás identitását hozza létre, amely fogalmi módon hasonló egy felhasználóhoz, a Azure Active Directory-bérlőben. Az alkalmazás identitása örökli a fürt nevét. Ez az identitás a Azure Stack HCI Cloud Service-ben, az előfizetésen belül, a megfelelő módon működik.

Ha a futtatott felhasználó `Register-AzureStackHCI` egy Azure Active Directory-rendszergazda, vagy megfelelő engedélyekkel rendelkezik, ez az összes automatikusan megtörténik, és nincs szükség további műveletre. Ha nem, a regisztráció befejezéséhez jóváhagyásra lehet szükség a Azure Active Directory rendszergazdájától. A rendszergazda vagy explicit módon megadhatja az alkalmazás beleegyezését, vagy delegálhatja az engedélyeket, hogy jóváhagyást adjon az alkalmazásnak:

:::image type="content" source="media/manage-azure-registration/aad-permissions.png" alt-text="Azure Active Directory engedélyek és azonosítók diagramja" border="false":::

A jóváhagyás megadásához nyissa meg a portal.azure.com, és jelentkezzen be egy olyan Azure-fiókkal, amely rendelkezik megfelelő engedélyekkel a Azure Active Directory. Navigáljon **Azure Active Directory**, majd **Alkalmazásregisztrációk**. Válassza ki a fürt után nevű alkalmazás-identitást, és navigáljon az **API-engedélyekhez**.

Az alkalmazáshoz két engedély szükséges:

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Census.Sync

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Billing.Sync
```

Ha a Azure Active Directory rendszergazdája jóváhagyást kér, eltarthat egy ideig, így a `Register-AzureStackHCI` parancsmag kilép, és a "függőben lévő rendszergazdai jóváhagyás" állapotba kerül, azaz részben kész. A jóváhagyás engedélyezése után egyszerűen futtassa újra `Register-AzureStackHCI` a regisztrációt.

## <a name="unregister-azure-stack-hci-with-azure"></a>Azure Stack HCI regisztrációjának törlése az Azure-ban

Ha készen áll a Azure Stack HCI-fürt leszerelésére, használja a `Unregister-AzStackHCI` parancsmagot a regisztráció megszüntetéséhez. Ezzel leállítja az összes figyelési, támogatási és számlázási funkciót az Azure arc használatával. A fürtöt jelképező Azure-erőforrás és a Azure Active Directory alkalmazás identitása törölve lett, de az erőforráscsoport nem, mert más nem kapcsolódó erőforrásokat is tartalmazhat.

Ha csomóponton futtatja a `Unregister-AzStackHCI` parancsmagot, használja ezt a szintaxist, és adja meg az Azure-előfizetés azonosítóját, valamint a regisztrálni kívánt Azure stack HCI-fürt erőforrás-nevét:

```PowerShell
Unregister-AzStackHCI -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

A rendszer kérni fogja, hogy látogasson el a microsoft.com/devicelogin egy másik eszközre (például a SZÁMÍTÓGÉPére vagy a telefonra), írja be a kódot, és jelentkezzen be az Azure-beli hitelesítéshez.

Ha a parancsmagot egy felügyeleti SZÁMÍTÓGÉPRŐL futtatja, akkor a fürtben is meg kell adnia a kiszolgáló nevét:

```PowerShell
Unregister-AzStackHCI -ComputerName ClusterNode1 -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Megjelenik egy interaktív Azure bejelentkezési ablak. A pontos Rákérdezés a biztonsági beállításoktól (például kétfaktoros hitelesítés) függően változhat. Az utasításokat követve jelentkezzen be.

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Azure Stack HCI összekötése az Azure-ba](../deploy/register-with-azure.md)
- [Azure Stack HCI figyelése Azure Monitor](azure-monitor.md)
