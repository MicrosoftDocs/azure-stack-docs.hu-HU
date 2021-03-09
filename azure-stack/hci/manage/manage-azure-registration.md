---
title: Azure Stack HCI-fürt regisztrációjának kezelése az Azure-ban
description: Az Azure-regisztráció kezelése Azure Stack HCI-fürtökhöz, a regisztrációs állapot megismerése és a fürt regisztrációjának törlése, amikor készen áll a leszerelésre.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 101d1cbcab097803e9c8a9fd9cd651dcf6fc80d1
ms.sourcegitcommit: e432e7f0a790bd6419987cbb5c5f3811e2e7a4a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102515634"
---
# <a name="manage-cluster-registration-with-azure"></a>Fürt regisztrációjának kezelése az Azure-ban

> A Azure Stack HCI verzió 20H2 vonatkozik

Miután létrehozott egy Azure Stack HCI-fürtöt, [regisztrálnia kell a Windows felügyeleti központot az Azure](register-windows-admin-center.md) -ban, majd [regisztrálnia kell a fürtöt az Azure](../deploy/register-with-azure.md)-ban. A fürt regisztrálása után rendszeres időközönként szinkronizálja a helyszíni fürt és a felhő közötti információkat. 

Ez a cikk azt ismerteti, hogyan lehet megtekinteni a regisztrációs állapotát, megadhatja Azure Active Directory (Azure AD) engedélyeit, és hogyan törölheti a fürt regisztrációját, ha készen áll a leszerelésre.

## <a name="view-registration-status-in-windows-admin-center"></a>A regisztrációs állapot megtekintése a Windows felügyeleti központban

Amikor a Windows felügyeleti központban csatlakozik a fürthöz, megjelenik az irányítópult, amely az Azure-kapcsolat állapotát jeleníti meg. A **csatlakoztatott** érték azt jelenti, hogy a fürt már regisztrálva van az Azure-ban, és az elmúlt nap során sikeresen szinkronizálva lett a felhőbe.

:::image type="content" source="media/manage-azure-registration/registration-status.png" alt-text="A Windows felügyeleti központ irányítópultján a fürthöz tartozó kapcsolatok állapotát bemutató képernyőkép." lightbox="media/manage-azure-registration/registration-status.png":::

További információkat a bal oldali **eszközök** menü alján található **Beállítások** elemre kattintva, majd **Azure stack HCI-regisztráció** lehetőség kiválasztásával érhet el.

:::image type="content" source="media/manage-azure-registration/azure-stack-hci-registration.png" alt-text="Képernyőkép, amely megjeleníti Azure Stack H C I regisztrációs adatok beszerzésére vonatkozó beállításokat." lightbox="media/manage-azure-registration/azure-stack-hci-registration.png":::

## <a name="view-registration-status-in-powershell"></a>Regisztrációs állapot megtekintése a PowerShellben

Ha a Windows PowerShell használatával szeretné megtekinteni a regisztrációs állapotot, használja a `Get-AzureStackHCI` PowerShell-parancsmagot, valamint a `ClusterStatus` ,, `RegistrationStatus` és `ConnectionStatus` tulajdonságokat. 

Például az Azure Stack HCI operációs rendszer telepítése után, de a fürt létrehozása vagy csatlakoztatása előtt a tulajdonság a következő `ClusterStatus` állapotot jeleníti meg `NotYet` :

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="Képernyőkép, amely az Azure regisztrációs állapotát mutatja a fürt létrehozása előtt.":::

A fürt létrehozása után a rendszer csak az `RegistrationStatus` állapotot jeleníti meg `NotYet` :

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="Képernyőkép, amely az Azure regisztrációs állapotát mutatja a fürt létrehozása után.":::

A telepítéstől számított 30 napon belül regisztrálnia kell egy Azure Stack HCI-fürtöt az Azure Online Services használati feltételeiben meghatározottak szerint. Ha a 30 nap elteltével nem hozott létre vagy csatlakoztatott egy fürtöt, a `ClusterStatus` következő jelenik meg: `OutOfPolicy` . Ha még 30 nap elteltével nem regisztrálta a fürtöt, a `RegistrationStatus` következő jelenik meg: `OutOfPolicy` .

A fürt regisztrálása után megtekintheti és elvégezheti `ConnectionStatus` az `LastConnected` időpontot. Az `LastConnected` idő általában az elmúlt napon belül van, kivéve, ha a fürt átmenetileg le van választva az internetről. Az Azure Stack HCI-fürtök akár 30 egymást követő napig is működhetnek teljesen offline állapotban.

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="Képernyőkép, amely az Azure regisztrációs állapotát mutatja a regisztráció után.":::

Ha túllépi az offline művelet maximális időtartamát, a `ConnectionStatus` következő jelenik meg: `OutOfPolicy` .

## <a name="assign-azure-ad-app-permissions"></a>Azure AD-alkalmazás engedélyeinek kiosztása

Az előfizetéshez tartozó Azure-erőforrások létrehozása mellett a Azure Stack HCI-regisztrációja létrehoz egy alkalmazás-identitást az Azure AD-bérlőben. Ez az identitás fogalmilag hasonló a felhasználóhoz. Az alkalmazásidentitás örökli a fürt nevét. Az identitás szükség szerint az Azure Stack HCI-felhőszolgáltatás nevében jár el az előfizetésen belül.

Ha a fürtöt regisztráló felhasználó egy Azure AD-rendszergazda vagy rendelkezik megfelelő engedélyekkel, ez az összes automatikusan megtörténik. Nincs szükség további műveletre. Ellenkező esetben előfordulhat, hogy jóvá kell hagynia az Azure AD-rendszergazdától a regisztráció befejezéséhez. A rendszergazda vagy explicit módon megadhatja az alkalmazás beleegyezését, vagy delegálhatja az engedélyeket, hogy jóváhagyást adjon az alkalmazásnak:

:::image type="content" source="media/manage-azure-registration/aad-permissions.png" alt-text="Azure Active Directory engedélyeket és identitást megjelenítő diagram." border="false":::

A jóváhagyás megadásához nyissa meg a [Portal.Azure.com](https://portal.azure.com) , és jelentkezzen be egy olyan Azure-fiókkal, amely rendelkezik megfelelő engedélyekkel az Azure ad-ben. Nyissa meg **Azure Active Directory**  >  **Alkalmazásregisztrációk**. Válassza ki a fürt után megnevezett alkalmazás-identitást, és nyissa meg az **API-engedélyeket**.

Azure Stack HCI általánosan elérhető (GA) kiadásához az alkalmazásnak a következő engedélyekkel kell rendelkeznie. Eltérnek a nyilvános előzetes verzióban szükséges alkalmazási engedélyekkel.

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.ReadWrite

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.ReadWrite
```

A nyilvános előzetes verzióban az alkalmazás engedélyei (mostantól elavultak) a következőek voltak:

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Census.Sync

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Billing.Sync
```

Az Azure AD-rendszergazda jóváhagyásának kérése hosszabb időt is igénybe vehet, így a `Register-AzStackHCI` parancsmag kilép, és a regisztrációt egy `pending admin consent` (részben befejezett) állapotba hagyja. A jóváhagyás megadása után futtassa újra `Register-AzStackHCI` a regisztrációt.

## <a name="assign-azure-ad-user-permissions"></a>Azure AD-beli felhasználói engedélyek kiosztása

Az Azure AD-engedélyeket futtató felhasználónak az alábbiakra `Register-AzStackHCI` van szüksége:

- Hozzon létre ( `New-Remove-AzureADApplication` ), Get () `Get-Remove-AzureADApplication` , set () `Set-Remove-AzureADApplication` vagy Remove ( `Remove-AzureADApplication` ) Azure ad-alkalmazásokat.
- Hozzon létre ( `New-Get-AzureADServicePrincipal` ) vagy Get ( `Get-AzureADServicePrincipal` ) az Azure ad egyszerű szolgáltatását.
- Active Directory alkalmazás-titkok kezelése ( `New-Remove-AzureADApplicationKeyCredential` , `Get-Remove-AzureADApplicationKeyCredential` , vagy `Remove-AzureADApplicationKeyCredential` ).
- Adja meg a jóváhagyást meghatározott alkalmazási engedélyek ( `New-AzureADApplicationKeyCredential` , `Get-AzureADApplicationKeyCredential` vagy `Remove-AzureADServiceAppRoleAssignments` ) használatára.

Ezeket az engedélyeket három módon rendelheti hozzá.

### <a name="option-1-allow-any-user-to-register-applications"></a>1. lehetőség: az alkalmazások regisztrálásának engedélyezése bármely felhasználó számára

A Azure Active Directory területen válassza a **felhasználói beállítások**  >  **Alkalmazásregisztrációk** lehetőséget. A **felhasználók regisztrálhatnak alkalmazásokat**, majd válassza az **Igen** lehetőséget.

Ez a beállítás lehetővé teszi, hogy bármely felhasználó regisztrálja az alkalmazásokat. A felhasználónak azonban továbbra is szüksége van az Azure AD-rendszergazdára, hogy engedélyezze a jóváhagyást a fürt regisztrálása során. 

> [!NOTE]
> Ez a lehetőség bérlői szintű beállítás, így előfordulhat, hogy nem alkalmas a nagyvállalati ügyfelek számára.

### <a name="option-2-assign-the-cloud-application-administration-role"></a>2. lehetőség: a Cloud Application Administration szerepkör kiosztása

Rendelje hozzá a beépített *Cloud Application Administration* Azure ad-szerepkört a felhasználóhoz. Ez a hozzárendelés lehetővé teszi a felhasználó számára a fürtök regisztrálását és regisztrációjának megszüntetését anélkül, hogy további Active Directory rendszergazdai engedélyre lenne szükség.

### <a name="option-3-create-a-custom-active-directory-role-and-consent-policy"></a>3. lehetőség: egyéni Active Directory szerepkör és beleegyező szabályzat létrehozása

A legszigorúbb lehetőség egy egyéni Active Directory szerepkör létrehozása egyéni beleegyező házirenddel, amely a bérlői szintű rendszergazdai jóváhagyást delegálja a szükséges engedélyekhez az Azure Stack HCI szolgáltatáshoz. Ha ezt az egyéni szerepkört a felhasználókhoz rendeli hozzá, akkor is regisztrálhat és biztosíthat jóváhagyást anélkül, hogy további Active Directory rendszergazdai jóváhagyásra lenne szükség.

> [!NOTE]
> Ehhez a beállításhoz prémium szintű Azure AD licenc szükséges. Egyéni Active Directory-szerepköröket és egyéni engedélyezési házirend-funkciókat használ, amelyek mostantól nyilvános előzetes verzióban érhetők el.

1. Összekapcsolás az Azure Active Directoryval:
   
   ```powershell
   Connect-AzureAD
   ```

2. Hozzon létre egy egyéni engedélyezési szabályzatot:

   ```powershell
   New-AzureADMSPermissionGrantPolicy -Id "AzSHCI-registration-consent-policy" -DisplayName "Azure Stack HCI registration admin app consent policy" -Description "Azure Stack HCI registration admin app consent policy"
   ```

3. Adjon hozzá egy olyan feltételt, amely tartalmazza a Azure Stack HCI szolgáltatáshoz szükséges alkalmazás-engedélyeket, amely az alkalmazás AZONOSÍTÓjának 1322e676-dee7-41ee-a874-ac923822781c. 
   
   > [!NOTE]
   > A következő engedélyek a Azure Stack HCI kiadására vonatkoznak. A nyilvános előzetes verzióban nem működnek, hacsak nem alkalmazta a fürt összes kiszolgálójára a [november 23., 2020-es frissítését (KB4586852)](https://support.microsoft.com/help/4595086/azure-stack-hci-release-notes-overview) , és letöltötte az. StackHCI modul 0.4.1 vagy újabb verziójára.
   
   ```powershell
   New-AzureADMSPermissionGrantConditionSet -PolicyId "AzSHCI-registration-consent-policy" -ConditionSetType "includes" -PermissionType "application" -ResourceApplication "1322e676-dee7-41ee-a874-ac923822781c" -Permissions "bbe8afc9-f3ba-4955-bb5f-1cfb6960b242","8fa5445e-80fb-4c71-a3b1-9a16a81a1966","493bd689-9082-40db-a506-11f40b68128f","2344a320-6a09-4530-bed7-c90485b5e5e2"
   ```

4. Engedélyek megadása a Azure Stack HCI regisztrálásának engedélyezéséhez, a 2. lépés:
   
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

5. Hozza létre az új egyéni Active Directory szerepkört:

   ```powershell
   $customADRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
   ```

6. Az [alábbi utasításokat](/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal?context=/azure/active-directory/roles/context/ugr-context) követve rendelje hozzá az új egyéni Active Directory szerepkört ahhoz a felhasználóhoz, aki regisztrálni fogja az Azure stack HCI-fürtöt az Azure-ban.

## <a name="unregister-azure-stack-hci-by-using-windows-admin-center"></a>Azure Stack HCI regisztrációjának törlése a Windows felügyeleti központ használatával

Ha készen áll a Azure Stack HCI-fürt leszerelésére, kapcsolódjon a fürthöz a Windows felügyeleti központ használatával. Válassza a bal oldali **eszközök** menü alján található **Beállítások** elemet. Ezután válassza ki **Azure stack HCI-regisztrációt**, és kattintson a **Regisztráció törlése** gombra. 

A regisztráció megszüntetése folyamat automatikusan törli a fürtöt képviselő Azure-erőforrást, az Azure-erőforráscsoportot (ha a csoport a regisztráció során jött létre, és nem tartalmaz más erőforrásokat) és az Azure AD-alkalmazás identitását. Ez a karbantartás az Azure arc használatával leállítja az összes figyelési, támogatási és számlázási funkciót.

> [!NOTE]
> Azure Stack HCI-fürt regisztrációjának visszavonásához Azure AD-rendszergazdának vagy egy [megfelelő engedélyekkel](#assign-azure-ad-user-permissions)rendelkező másik felhasználónak kell lennie. 
>
> Ha a Windows felügyeleti központ átjárója egy másik Azure Active Directory (bérlői) AZONOSÍTÓhoz van regisztrálva, mint a fürt első regisztrálásához, akkor problémák léphetnek fel, amikor megpróbálja törölni a fürt regisztrációját a Windows felügyeleti központban. Ha ez történik, használja a következő PowerShell-utasításokat.

## <a name="unregister-azure-stack-hci-by-using-powershell"></a>Azure Stack HCI regisztrációjának törlése a PowerShell használatával

A `Unregister-AzStackHCI` parancsmaggal Azure stack HCI-fürtöt is törölheti. A parancsmagot fürtözött csomóponton vagy egy felügyeleti SZÁMÍTÓGÉPRŐL is futtathatja.

Előfordulhat, hogy telepítenie kell a modul legújabb verzióját `Az.StackHCI` . Ha a rendszer kéri `Are you sure you want to install the modules from 'PSGallery'?` , válaszoljon igen ( `Y` ).

```PowerShell
Install-Module -Name Az.StackHCI
```

### <a name="unregister-from-a-cluster-node"></a>Fürt csomópontból való regisztrációjának törlése

Ha a `Unregister-AzStackHCI` parancsmagot a fürt egyik kiszolgálóján futtatja, használja a következő szintaxist: Adja meg az Azure-előfizetés AZONOSÍTÓját és a regisztrálni kívánt Azure Stack HCI-fürt erőforrásának nevét.

```PowerShell
Unregister-AzStackHCI -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

A rendszer arra kéri, hogy látogasson el a microsoft.com/devicelogin egy másik eszközre (például a SZÁMÍTÓGÉPére vagy a telefonra). Adja meg a kódot, és jelentkezzen be az Azure-ban való hitelesítéshez.

### <a name="unregister-from-a-management-pc"></a>Regisztráció törlése egy felügyeleti SZÁMÍTÓGÉPRŐL

Ha a parancsmagot egy felügyeleti SZÁMÍTÓGÉPRŐL futtatja, akkor a fürtben is meg kell adnia a kiszolgáló nevét:

```PowerShell
Unregister-AzStackHCI -ComputerName ClusterNode1 -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Megjelenik egy interaktív Azure bejelentkezési ablak. A megjelenő pontos kérések a biztonsági beállításoktól (például kétfaktoros hitelesítés) függően változnak. Az utasításokat követve jelentkezzen be.

## <a name="clean-up-after-a-cluster-that-was-not-properly-unregistered"></a>Egy olyan fürt eltávolítása után, amely nem lett megfelelően törölve

Ha egy felhasználó megsemmisít egy Azure Stack HCI-fürtöt a regisztráció törlése nélkül, például a gazdagép-kiszolgálók rendszerképének átállításával vagy a virtuális fürtcsomópontok törlésével, akkor az összetevők az Azure-ban maradnak. Ezek az összetevők ártalmatlanok, és nem számítanak fel számlázási vagy erőforrás-használatot, de a Azure Portal is felmerülhetnek. A törléshez manuálisan is törölheti őket.

A Azure Stack HCI-erőforrás törléséhez nyissa meg a saját lapját a Azure Portal, és válassza a **Törlés** lehetőséget a felül található műveleti sávon. Adja meg az erőforrás nevét a törlés megerősítéséhez, majd válassza a **Törlés** lehetőséget. 

Az Azure ad-alkalmazás identitásának törléséhez nyissa meg az **Azure ad**-  >  **alkalmazás regisztrációit** az  >  **összes alkalmazásban**. Válassza a **Törlés** lehetőséget, és erősítse meg.

Az Azure Stack HCI-erőforrást a PowerShell használatával is törölheti:

```PowerShell
Remove-AzResource -ResourceId "HCI001"
```

Lehetséges, hogy telepítenie kell a `Az.Resources` modult:

```PowerShell
Install-Module -Name Az.Resources
```

Ha az erőforráscsoport a regisztráció során lett létrehozva, és nem tartalmaz más erőforrásokat, akkor azt is törölheti:

```PowerShell
Remove-AzResourceGroup -Name "HCI001-rg"
```

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információk a következő témakörben találhatók:

- [A Windows felügyeleti központ regisztrálása az Azure-ban](register-windows-admin-center.md)
- [Az Azure Stack HCI csatlakoztatása az Azure-hoz](../deploy/register-with-azure.md)