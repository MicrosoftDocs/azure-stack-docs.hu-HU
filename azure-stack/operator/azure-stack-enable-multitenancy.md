---
title: Több-bérlő konfigurálása Azure Stack központban
description: Megtudhatja, hogyan engedélyezheti és tilthatja le több Azure Active Directory bérlőt Azure Stack hub-ban.
author: BryanLa
ms.topic: how-to
ms.date: 10/16/2020
ms.author: bryanla
ms.reviewer: bryanr
ms.lastreviewed: 10/16/2020
ms.openlocfilehash: 923c430291c742069a29806449b45d4fc9cdef07
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94544224"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>Több-bérlő konfigurálása Azure Stack központban

Azure Stack hub-t úgy konfigurálhatja, hogy támogassa a több Azure Active Directory (Azure AD) bérlő felhasználóit, így az Azure Stack hub szolgáltatásainak használatát is lehetővé teszi. Vegyük példaként a következő esetet:

- Ön a contoso.onmicrosoft.com szolgáltatás rendszergazdája, ahol a Azure Stack hub telepítve van.
- Mary a fabrikam.onmicrosoft.com címtár-rendszergazdája, ahol a vendég felhasználók találhatók.
- Mary vállalata IaaS-és Péter-szolgáltatásokat kap a vállalattól, és lehetővé kell tennie a vendégeknek, hogy bejelentkezzenek és Azure Stack hub-erőforrásokat használjanak a contoso.onmicrosoft.com-ben.

Ez az útmutató ismerteti a szükséges lépéseket a forgatókönyv kontextusában a Azure Stack hub több-bérlős szolgáltatásának konfigurálásához. Ebben az esetben Önnek és Mária-nek végre kell hajtania a lépéseket, amelyek lehetővé teszik a fabrikam számára, hogy bejelentkezzenek és felhasználják a szolgáltatásait a contoso Azure Stack hub üzembe helyezésével.

Ha Ön egy felhőalapú megoldás-szolgáltató (CSP), akkor további módon [konfigurálhatja és kezelheti a több-bérlős Azure stack hubot](azure-stack-add-manage-billing-as-a-csp.md). 

## <a name="enable-multi-tenancy"></a>A több-bérlős üzemmód engedélyezése

A Azure Stack hub-beli több bérlő konfigurálása előtt néhány előfeltételt kell figyelembe vennie:
  
 - Önnek és Mary-nek koordinálnia kell a felügyeleti lépéseket mind a Directory Azure Stack hub (contoso), mind a Guest Directory (Fabrikam) között.
 - Győződjön meg arról, hogy [telepítette](powershell-install-az-module.md) és [konfigurálta](azure-stack-powershell-configure-admin.md) a PowerShellt Azure stack hubhoz.
 - [Töltse le az Azure stack hub-eszközöket](azure-stack-powershell-download.md), és importálja a csatlakozási és Identity modulokat:

    ```powershell
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-hub-directory"></a>Azure Stack hub könyvtárának konfigurálása

Ebben a szakaszban úgy konfigurálja Azure Stack hubot, hogy engedélyezze a fabrikam Azure AD címtár-bérlők bejelentkezését.

A vendég Directory-bérlő (Fabrikam) beléptetése Azure Stack hubhoz úgy, hogy Azure Resource Manager segítségével fogadja el a felhasználókat és az egyszerű szolgáltatásokat a vendég címtár bérlője számára.

A contoso.onmicrosoft.com szolgáltatás rendszergazdája a következő parancsokat futtatja:

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack Hub directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group.
$location = "local"

# Subscription Name
$SubscriptionName = "Default Provider Subscription"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName `
 -SubscriptionName $SubscriptionName
```

### <a name="configure-guest-directory"></a>A vendég könyvtárának konfigurálása

Miután a Azure Stack hub-kezelő engedélyezte a fabrikam címtárat a Azure Stack hub-ban való használathoz, Mary-nek regisztrálnia kell Azure Stack hubot a fabrikam címtár-Bérlővel.

#### <a name="register-azure-stack-hub-with-the-guest-directory"></a>Azure Stack hub regisztrálása a vendég címtárral

Mária (a fabrikam címtár-rendszergazdája) a következő parancsokat futtatja a vendég címtár fabrikam.onmicrosoft.com:

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory.
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose
```

> [!IMPORTANT]
> Ha a Azure Stack hub rendszergazdája új szolgáltatásokat vagy frissítéseket telepít a jövőben, lehetséges, hogy újra futtatnia kell ezt a parancsfájlt.
>
> Futtassa újra ezt a parancsfájlt a címtárban található Azure Stack hub-alkalmazások állapotának vizsgálatához.
>
> Ha észrevette a virtuális gépek létrehozásával kapcsolatos problémákat Managed Disks (az 1808-es frissítésben bemutatott), egy új **lemez erőforrás-szolgáltató** lett hozzáadva, amely megköveteli, hogy ez a parancsfájl újra fusson.

### <a name="direct-users-to-sign-in"></a>Közvetlen bejelentkezés a felhasználók számára

Most, hogy Mary elvégezte a Mary címtár bevezetésének lépéseit, Mária a fabrikam-felhasználókat a bejelentkezéshez irányíthatja. Fabrikam-felhasználók (fabrikam.onmicrosoft.com utótaggal rendelkező felhasználók) bejelentkezés a https- \: //Portal.local.azurestack.external.

Mary a fabrikam címtárban (a fabrikam-könyvtárban lévő felhasználók a fabrikam.onmicrosoft.com utótag nélkül) irányítja át az összes [idegen résztvevőt](/azure/role-based-access-control/rbac-and-directory-admin-roles) a https-//Portal.local.azurestack.external/fabrikam.onmicrosoft.com. használatával történő bejelentkezéshez. \: Ha nem használják ezt az URL-címet, azokat a rendszer az alapértelmezett könyvtárba (Fabrikam) küldi, és hibaüzenetet kap, amely szerint a rendszergazda nem járult hozzá.

## <a name="disable-multi-tenancy"></a>Több-bérlő letiltása

Ha már nem szeretne több bérlőt használni Azure Stack központban, a következő lépések végrehajtásával letilthatja a többszörös kiszolgálat:

1. A vendég könyvtára rendszergazdájaként (ebben a forgatókönyvben Mária) futtassa a *Regisztráció törlése-AzsWithMyDirectoryTenant*. A parancsmag eltávolítja az összes Azure Stack hub alkalmazást az új könyvtárból.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory.
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Azure Stack hub szolgáltatás-rendszergazdájaként (ebben a forgatókönyvben) futtassa a *Regisztráció törlése-AzSGuestDirectoryTenant*.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack Hub directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > A több-bérlős lépések letiltása sorrendben kell végrehajtani. A lépés #1 sikertelen, ha a #2 első lépése befejeződött.

## <a name="retrieve-azure-stack-hub-identity-health-report"></a>Azure Stack hub Identity Health-jelentés beolvasása 

Cserélje le a `<region>` , `<domain>` , és `<homeDirectoryTenant>` helyőrzőket, majd hajtsa végre a következő parancsmagot Azure stack hub-rendszergazdaként.

```powershell

$AdminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
$DirectoryName = "<homeDirectoryTenant>.onmicrosoft.com"
$healthReport = Get-AzsHealthReport -AdminResourceManagerEndpoint $AdminResourceManagerEndpoint -DirectoryTenantName $DirectoryName
Write-Host "Healthy directories: "
$healthReport.directoryTenants | Where status -EQ 'Healthy' | Select -Property tenantName,tenantId,status | ft


Write-Host "Unhealthy directories: "
$healthReport.directoryTenants | Where status -NE 'Healthy' | Select -Property tenantName,tenantId,status | ft
```

### <a name="update-azure-ad-tenant-permissions"></a>Azure AD-bérlői engedélyek frissítése

Ez a művelet törli a riasztást Azure Stack központban, ami azt jelzi, hogy egy könyvtár frissítésre szorul. Futtassa a következő parancsot a **Azurestack-Tools-Master/Identity** mappában:

```powershell
Import-Module ..\Identity\AzureStack.Identity.psm1

$adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"

# This is the primary tenant Azure Stack is registered to:
$homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com"

Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
   -DirectoryTenantName $homeDirectoryTenantName -Verbose
```

A parancsfájl rendszergazdai hitelesítő adatokat kér az Azure AD-bérlőn, és több percet is igénybe vehet. A riasztást a parancsmag futtatása után törölni kell.

## <a name="next-steps"></a>Következő lépések

- [Delegált szolgáltatók kezelése](azure-stack-delegated-provider.md)
- [Azure Stack hub főbb fogalmak](azure-stack-overview.md)
- [Azure Stack hub használatának és számlázásának kezelése felhőalapú megoldás-szolgáltatóként](azure-stack-add-manage-billing-as-a-csp.md)
- [Bérlő hozzáadása a Azure Stack hubhoz való használathoz és számlázáshoz](azure-stack-csp-howto-register-tenants.md)
