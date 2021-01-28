---
title: Azure-regisztráció kezelése Azure Stack HCI-hez
description: Az Azure-regisztráció kezelése Azure Stack HCI-hez, a regisztrációs állapot megismerése és a fürt regisztrációjának törlése, amikor készen áll a leszerelésre.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 01/27/2021
ms.openlocfilehash: c16216a52b0955277bc6d30725f88d0555908685
ms.sourcegitcommit: dc11aabd3b97c505c5b3cecd3bdb2d5c8e8496aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98981186"
---
# <a name="manage-azure-registration"></a>Azure-regisztráció kezelése

> Azure Stack HCI-v20H2 vonatkozik

Miután létrehozott egy Azure Stack HCI-fürtöt, [regisztrálnia kell a fürtöt az Azure arc](../deploy/register-with-azure.md)-ban. A fürt regisztrálása után rendszeres időközönként szinkronizálja a helyszíni fürt és a felhő közötti információkat. Ez a témakör a regisztrációs állapot megértését, Azure Active Directory engedélyek megadását és a fürt regisztrációjának törlését ismerteti, amikor készen áll a leszerelésre.

## <a name="understanding-registration-status-using-windows-admin-center"></a>A regisztráció állapotának megértése a Windows felügyeleti központban

Amikor Windows felügyeleti központtal csatlakozik egy fürthöz, megjelenik az irányítópult, amely az Azure-kapcsolat állapotát jeleníti meg. A **csatlakoztatott** érték azt jelenti, hogy a fürt már regisztrálva van az Azure-ban, és az elmúlt nap során sikeresen szinkronizálva lett a felhőbe.

   :::image type="content" source="media/manage-azure-registration/registration-status.png" alt-text="A Windows felügyeleti központ irányítópultja mindig megjeleníti a fürt kapcsolatainak állapotát" lightbox="media/manage-azure-registration/registration-status.png":::

További információkat a bal oldali **eszközök** menü alján található **Beállítások** elemre kattintva, majd **Azure stack HCI-regisztráció** lehetőség kiválasztásával érhet el.

   :::image type="content" source="media/manage-azure-registration/azure-stack-hci-registration.png" alt-text="További információért válassza a beállítások > eszközök > Azure Stack HCI-regisztráció lehetőséget." lightbox="media/manage-azure-registration/azure-stack-hci-registration.png":::

## <a name="understanding-registration-status-using-powershell"></a>A regisztrációs állapot megértése a PowerShell használatával

A Windows PowerShell használatával a regisztrációs állapot megértéséhez használja a `Get-AzureStackHCI` PowerShell-parancsmagot, valamint a `ClusterStatus` ,, `RegistrationStatus` és `ConnectionStatus` tulajdonságokat. Például az Azure Stack HCI operációs rendszer telepítése után, a fürt létrehozása vagy csatlakoztatása előtt a tulajdonság a `ClusterStatus` "még nem" állapotot jeleníti meg:

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="Azure regisztrációs állapot a fürt létrehozása előtt":::

A fürt létrehozása után a rendszer csak a `RegistrationStatus` "még nem" állapotot jeleníti meg:

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="Azure-beli regisztráció állapota a fürt létrehozása után":::

Azure Stack HCI-nek az Azure Online Services használati feltételeinek megfelelően regisztrálnia kell a telepítést követő 30 napon belül. Ha nem fürtözött 30 nap után, a a `ClusterStatus` következőt fogja látni `OutOfPolicy` , és ha nem regisztrálja magát 30 nap után, a fog megjelenni `RegistrationStatus` `OutOfPolicy` .

A fürt regisztrálása után megtekintheti a `ConnectionStatus` és az `LastConnected` időt, amely általában az elmúlt napon belül van, kivéve, ha a fürt átmenetileg le van választva az internetről. Az Azure Stack HCI-fürtök akár 30 egymást követő napig is működhetnek teljesen offline állapotban.

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="Az Azure regisztrációs állapota Regisztráció után":::

Ha túllépi a maximális időtartamot, a `ConnectionStatus` fog megjelenni `OutOfPolicy` .

## <a name="azure-active-directory-app-permissions"></a>Azure Active Directory alkalmazás engedélyei

Az előfizetéshez tartozó Azure-erőforrások létrehozása mellett a Azure Stack HCI-regisztrációja egy alkalmazás identitását hozza létre, amely fogalmi módon hasonló egy felhasználóhoz, a Azure Active Directory-bérlőben. Az alkalmazásidentitás örökli a fürt nevét. Az identitás szükség szerint az Azure Stack HCI-felhőszolgáltatás nevében jár el az előfizetésen belül.

Ha a fürtöt regisztráló felhasználó Azure Active Directory rendszergazda, vagy megfelelő engedélyekkel rendelkezik, ez az összes automatikusan megtörténik, és nincs szükség további műveletre. Ha nem, a regisztráció befejezéséhez jóváhagyásra lehet szükség a Azure Active Directory rendszergazdájától. A rendszergazda vagy explicit módon megadhatja az alkalmazás beleegyezését, vagy delegálhatja az engedélyeket, hogy jóváhagyást adjon az alkalmazásnak:

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

Ha a Azure Active Directory rendszergazdája jóváhagyást kér, eltarthat egy ideig, így a `Register-AzStackHCI` parancsmag kilép, és a "függőben lévő rendszergazdai jóváhagyás" állapotba kerül, azaz részben kész. A jóváhagyás engedélyezése után egyszerűen futtassa újra `Register-AzStackHCI` a regisztrációt.

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

Rendelje hozzá a beépített "Cloud Application Administration" Azure AD-szerepkört a felhasználóhoz. Ez lehetővé teszi a felhasználó számára a fürtök regisztrálását és regisztrációjának megszüntetését anélkül, hogy további AD-rendszergazdai beleegyező jogosultságra van szüksége.

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

## <a name="unregister-azure-stack-hci-using-windows-admin-center"></a>Azure Stack HCI regisztrációjának törlése a Windows felügyeleti központban

Ha készen áll a Azure Stack HCI-fürt leszerelésére, egyszerűen kapcsolódjon a fürthöz a Windows felügyeleti központban, és válassza a **Beállítások** lehetőséget a bal oldali **eszközök** menü alján. Ezután válassza ki **Azure stack HCI-regisztrációt**, és kattintson a **Regisztráció törlése** gombra. A regisztráció megszüntetése folyamat automatikusan törli a fürtöt jelképező Azure-erőforrást, az Azure-erőforráscsoportot (ha a csoportot a regisztráció során hozta létre, és nem tartalmaz más erőforrásokat) és az Azure AD-alkalmazás identitását. Ezzel leállítja az összes figyelési, támogatási és számlázási funkciót az Azure arc használatával.

   > [!NOTE]
   > Azure Stack HCI-fürt regisztrációjának visszavonásához szükség van egy Azure Active Directory rendszergazdára vagy egy olyan felhasználóra, aki rendelkezik a megfelelő engedélyekkel. Lásd: [Azure Active Directory felhasználói engedélyek](#azure-active-directory-user-permissions).

## <a name="unregister-azure-stack-hci-using-powershell"></a>Azure Stack HCI regisztrációjának törlése a PowerShell használatával

A `Unregister-AzStackHCI` parancsmaggal Azure stack HCI-fürtöt is törölheti. A parancsmagot fürtözött csomóponton vagy egy felügyeleti SZÁMÍTÓGÉPRŐL is futtathatja.

Előfordulhat, hogy telepítenie kell a modul legújabb verzióját `Az.StackHCI` . Előfordulhat, hogy a rendszer arra kéri, hogy telepítse a modulokat a "PSGallery"? "értékről, amelyhez az igen (Y) értéket kell megadnia.

```PowerShell
Install-Module -Name Az.StackHCI
```

### <a name="unregister-from-a-cluster-node"></a>Fürt csomópontból való regisztrációjának törlése

Ha a `Unregister-AzStackHCI` parancsmagot a fürt egyik kiszolgálóján futtatja, használja ezt a szintaxist, és adja meg az Azure-előfizetés azonosítóját, valamint a regisztrálni kívánt Azure stack HCI-fürt erőforrásának nevét:

```PowerShell
Unregister-AzStackHCI -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

A rendszer kérni fogja, hogy látogasson el a microsoft.com/devicelogin egy másik eszközre (például a SZÁMÍTÓGÉPére vagy a telefonra), írja be a kódot, és jelentkezzen be az Azure-beli hitelesítéshez.

### <a name="unregister-from-a-management-pc"></a>Regisztráció törlése egy felügyeleti SZÁMÍTÓGÉPRŐL

Ha a parancsmagot egy felügyeleti SZÁMÍTÓGÉPRŐL futtatja, akkor a fürtben is meg kell adnia a kiszolgáló nevét:

```PowerShell
Unregister-AzStackHCI -ComputerName ClusterNode1 -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Megjelenik egy interaktív Azure bejelentkezési ablak. A pontos Rákérdezés a biztonsági beállításoktól (például kétfaktoros hitelesítés) függően változhat. Az utasításokat követve jelentkezzen be.

## <a name="cleaning-up-after-a-cluster-that-was-not-properly-unregistered"></a>Egy olyan fürt után történő tisztítás, amely nem lett megfelelően törölve

Ha egy felhasználó megsemmisít egy Azure Stack HCI-fürtöt a regisztráció törlése nélkül, például a gazdagép-kiszolgálók újbóli lemezképének vagy a virtuális fürtcsomópontok törlésének megszüntetésével, akkor az összetevők az Azure-ban maradnak. Ezek ártalmatlanok, és nem járnak számlázással vagy erőforrás-használattal, de a Azure Portal rendetlenséget okozhatnak. A törléshez manuálisan is törölheti őket.

Az Azure Stack HCI-erőforrás törléséhez navigáljon a Azure Portal oldalára, majd válassza a **Törlés** lehetőséget a felül található műveleti sávon. Írja be az erőforrás nevét a törlés megerősítéséhez, majd válassza a **Törlés** lehetőséget. Az Azure AD-alkalmazás identitásának törléséhez navigáljon az **Azure ad**-be, majd az alkalmazások **regisztrációja** lehetőségre, és megtalálja az **összes alkalmazás** területen. Válassza a **Törlés** lehetőséget, és erősítse meg.

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Az Azure Stack HCI csatlakoztatása az Azure-hoz](../deploy/register-with-azure.md)
