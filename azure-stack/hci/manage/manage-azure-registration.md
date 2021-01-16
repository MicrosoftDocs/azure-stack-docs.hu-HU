---
title: Azure-regisztráció kezelése Azure Stack HCI-hez
description: Az Azure-regisztráció kezelése Azure Stack HCI-hez és a regisztrációs állapot megértése a PowerShell használatával.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 12/10/2020
ms.openlocfilehash: a81a1973d7324371cb42b23ca7905d39492401cf
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254432"
---
# <a name="manage-azure-registration"></a>Azure-regisztráció kezelése

> Azure Stack HCI-v20H2 vonatkozik

Miután létrehozott egy Azure Stack HCI-fürtöt, [regisztrálnia kell a fürtöt az Azure arc](../deploy/register-with-azure.md)-ban. A fürt regisztrálása után rendszeres időközönként szinkronizálja a helyszíni fürt és a felhő közötti információkat. Ez a témakör a regisztrációs állapot megértését, Azure Active Directory engedélyek megadását és a fürt regisztrációjának törlését ismerteti, amikor készen áll a leszerelésre.

## <a name="understanding-registration-status"></a>A regisztrációs állapot ismertetése

A regisztrációs állapot megismeréséhez használja a `Get-AzureStackHCI` PowerShell-parancsmagot, valamint a `ClusterStatus` ,, `RegistrationStatus` és `ConnectionStatus` tulajdonságokat. Például az Azure Stack HCI operációs rendszer telepítése után, a fürt létrehozása vagy csatlakoztatása előtt a tulajdonság a `ClusterStatus` "még nem" állapotot jeleníti meg:

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="Azure regisztrációs állapot a fürt létrehozása előtt":::

A fürt létrehozása után a rendszer csak a `RegistrationStatus` "még nem" állapotot jeleníti meg:

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="Azure-beli regisztráció állapota a fürt létrehozása után":::

Azure Stack HCI-nek az Azure Online Services használati feltételeinek megfelelően regisztrálnia kell a telepítést követő 30 napon belül. Ha nem fürtözött 30 nap után, a a `ClusterStatus` következőt fogja látni `OutOfPolicy` , és ha nem regisztrálja magát 30 nap után, a fog megjelenni `RegistrationStatus` `OutOfPolicy` .

A fürt regisztrálása után megtekintheti a `ConnectionStatus` és az `LastConnected` időt, amely általában az elmúlt napon belül van, kivéve, ha a fürt átmenetileg le van választva az internetről. Az Azure Stack HCI-fürtök akár 30 egymást követő napig is működhetnek teljesen offline állapotban.

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="Az Azure regisztrációs állapota Regisztráció után":::

Ha túllépi a maximális időtartamot, a `ConnectionStatus` fog megjelenni `OutOfPolicy` .

## <a name="azure-active-directory-app-permissions"></a>Azure Active Directory alkalmazás engedélyei

Az előfizetéshez tartozó Azure-erőforrások létrehozása mellett a Azure Stack HCI-regisztrációja egy alkalmazás identitását hozza létre, amely fogalmi módon hasonló egy felhasználóhoz, a Azure Active Directory-bérlőben. Az alkalmazásidentitás örökli a fürt nevét. Az identitás szükség szerint az Azure Stack HCI-felhőszolgáltatás nevében jár el az előfizetésen belül.

Ha a futtatott felhasználó `Register-AzureStackHCI` egy Azure Active Directory-rendszergazda, vagy megfelelő engedélyekkel rendelkezik, ez az összes automatikusan megtörténik, és nincs szükség további műveletre. Ha nem, a regisztráció befejezéséhez jóváhagyásra lehet szükség a Azure Active Directory rendszergazdájától. A rendszergazda vagy explicit módon megadhatja az alkalmazás beleegyezését, vagy delegálhatja az engedélyeket, hogy jóváhagyást adjon az alkalmazásnak:

:::image type="content" source="media/manage-azure-registration/aad-permissions.png" alt-text="Azure Active Directory engedélyek és azonosítók diagramja" border="false":::

A jóváhagyás megadásához nyissa meg a [Portal.Azure.com](https://portal.azure.com) , és jelentkezzen be egy olyan Azure-fiókkal, amely rendelkezik megfelelő engedélyekkel a Azure Active Directory. Navigáljon **Azure Active Directory**, majd **Alkalmazásregisztrációk**. Válassza ki a fürt után nevű alkalmazás-identitást, és navigáljon az **API-engedélyekhez**.

Azure Stack HCI általánosan elérhető (GA) kiadásához az alkalmazásnak a következő engedélyekkel kell rendelkeznie, amelyek eltérnek a nyilvános előzetes verzióban szükséges alkalmazási engedélyekkel:

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.ReadWrite

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.ReadWrite
```

A nyilvános előzetes verzióban az alkalmazás engedélyei voltak (ezek már elavultak):

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Census.Sync

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Billing.Sync
```

Ha a Azure Active Directory rendszergazdája jóváhagyást kér, eltarthat egy ideig, így a `Register-AzureStackHCI` parancsmag kilép, és a "függőben lévő rendszergazdai jóváhagyás" állapotba kerül, azaz részben kész. A jóváhagyás engedélyezése után egyszerűen futtassa újra `Register-AzureStackHCI` a regisztrációt.

## <a name="azure-active-directory-user-permissions"></a>Felhasználói engedélyek Azure Active Directory

Az Register-AzStackHCIt futtató felhasználónak Azure AD-engedélyekre van szüksége a következőhöz:

- Azure AD-alkalmazások létrehozása/beolvasása/beállítása/eltávolítása (új/beolvasás/beállítás/eltávolítás – AzureADApplication)
- Azure AD egyszerű szolgáltatás létrehozása/beolvasása (új/Get-New-Azureadserviceprincipal parancsmagot)
- AD-alkalmazási titkok kezelése (új/Get/Remove-AzureADApplicationKeyCredential)
- Jóváhagyás engedélyezése adott alkalmazás-engedélyek használatára (új/AzureADServiceAppRoleAssignments beolvasása/eltávolítása)

Ez háromféle módon valósítható meg.

### <a name="option-1-allow-any-user-to-register-applications"></a>1. lehetőség: az alkalmazások regisztrálásának engedélyezése bármely felhasználó számára

A Azure Active Directoryban navigáljon a **felhasználói beállítások > Alkalmazásregisztrációk**. A **felhasználók regisztrálhatnak alkalmazásokat**, majd válassza az **Igen** lehetőséget.

Ez lehetővé teszi a felhasználók számára az alkalmazások regisztrálását. A felhasználónak azonban továbbra is meg kell adnia az Azure AD-rendszergazdának a jóváhagyást a fürt regisztrálása során. Vegye figyelembe, hogy ez a bérlői szintű beállítás, ezért előfordulhat, hogy a nagyvállalati ügyfelek számára nem megfelelő.

### <a name="option-2-assign-cloud-application-administration-role"></a>2. lehetőség: a felhőalapú alkalmazás-felügyeleti szerepkör kiosztása

Rendelje hozzá a beépített "Cloud Application Administration" Azure AD-szerepkört a felhasználóhoz. Ez lehetővé teszi a felhasználó számára a fürtök regisztrálását anélkül, hogy további AD-rendszergazdai beleegyező jogosultságra van szüksége.

### <a name="option-3-create-a-custom-ad-role-and-consent-policy"></a>3. lehetőség: egyéni AD-szerepkör és beleegyező szabályzat létrehozása

A legszigorúbb lehetőség az egyéni AD-szerepkör létrehozása egyéni engedélyezési házirenddel, amely a bérlői szintű rendszergazdai jóváhagyást delegálja a Azure Stack HCI szolgáltatáshoz szükséges engedélyekhez. Ha ez az egyéni szerepkör hozzá van rendelve, a felhasználók regisztrálhatnak és megadhatnak jóváhagyást anélkül, hogy további AD-rendszergazdai jóváhagyásra van szükségük.

   > [!NOTE]
   > Ehhez a lehetőséghez prémium szintű Azure AD licencre van szükség, és egyéni AD-szerepköröket és egyéni engedélyezési házirend-funkciókat használ, amelyek jelenleg nyilvános előzetes verzióban érhetők el.

   1. Összekapcsolás az Azure Active Directoryval:
   
      ```powershell
      Connect-AzureAD
      ```

   2. Hozzon létre egy egyéni engedélyezési szabályzatot:

      ```powershell
      New-AzureADMSPermissionGrantPolicy -Id "AzSHCI-registration-consent-policy" -DisplayName "Azure Stack HCI registration admin app consent policy" -Description "Azure Stack HCI registration admin app consent policy"
      ```

   3. Vegyen fel egy olyan feltételt, amely tartalmazza a Azure Stack HCI szolgáltatáshoz szükséges alkalmazás-engedélyeket, amely az alkalmazás AZONOSÍTÓjának 1322e676-dee7-41ee-a874-ac923822781c. Vegye figyelembe, hogy a következő engedélyek a Azure Stack HCI kiadásához szükségesek, és a nyilvános előzetes verzióban nem működnek, hacsak nem alkalmazta a [november 23., 2020 előzetes frissítést (KB4586852)](https://support.microsoft.com/help/4595086/azure-stack-hci-release-notes-overview) a fürt összes kiszolgálójára, és az az. StackHCI modul 0.4.1 vagy újabb verzióját töltötte le.
   
      ```powershell
      New-AzureADMSPermissionGrantConditionSet -PolicyId "AzSHCI-registration-consent-policy" -ConditionSetType "includes" -PermissionType "application" -ResourceApplication "1322e676-dee7-41ee-a874-ac923822781c" -Permissions "bbe8afc9-f3ba-4955-bb5f-1cfb6960b242","8fa5445e-80fb-4c71-a3b1-9a16a81a1966","493bd689-9082-40db-a506-11f40b68128f","2344a320-6a09-4530-bed7-c90485b5e5e2"
      ```

   4. Adja meg az engedélyeket a Azure Stack HCI regisztrálásának engedélyezéséhez, amely a 2. lépésben létrehozott egyéni engedélyezési szabályzatot is megadja
   
      ```powershell
      $displayName = "Azure Stack HCI Registration Administrator "
      $description = "Custom AD role to allow registering Azure Stack HCI "
      $templateId = (New-Guid).Guid
      $allowedResourceAction =
      @(
             "microsoft.directory/applications/createAsOwner",
             "microsoft.directory/applications/delete",
             "microsoft.directory/applications/standard/read",
             "microsoft.directory/applications/credentials/update",
             "microsoft.directory/applications/permissions/update",
             "microsoft.directory/servicePrincipals/appRoleAssignedTo/update",
             "microsoft.directory/servicePrincipals/appRoleAssignedTo/read",
             "microsoft.directory/servicePrincipals/appRoleAssignments/read",
             "microsoft.directory/servicePrincipals/createAsOwner",
             "microsoft.directory/servicePrincipals/credentials/update",
             "microsoft.directory/servicePrincipals/permissions/update",
             "microsoft.directory/servicePrincipals/standard/read",
             "microsoft.directory/servicePrincipals/managePermissionGrantsForAll.AzSHCI-registration-consent-policy"
      )
      $rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
      ```

   5. Hozza létre az új egyéni AD-szerepkört:

      ```powershell
      $customADRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
      ```

   6. Rendelje hozzá az új egyéni AD-szerepkört ahhoz a felhasználóhoz, aki regisztrálni fogja az Azure Stack HCI-fürtöt az Azure-ban az alábbi [utasítások](/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal?context=/azure/active-directory/roles/context/ugr-context)követésével.

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

## <a name="next-steps"></a>További lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Az Azure Stack HCI csatlakoztatása az Azure-hoz](../deploy/register-with-azure.md)
- [Az Azure Stack HCI figyelése az Azure Monitorral](azure-monitor.md)