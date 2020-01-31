---
title: Több-bérlő konfigurálása Azure Stack központban
description: Megtudhatja, hogyan engedélyezheti és tilthatja le több Azure Active Directory bérlőt Azure Stack hub-ban.
author: ihenkel
ms.topic: article
ms.date: 06/10/2019
ms.author: inhenkel
ms.reviewer: bryanr
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: dd91cfed0dd4f2444371de43e7da8a48b6339069
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890272"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>Több-bérlő konfigurálása Azure Stack központban

Azure Stack hub-t úgy konfigurálhatja, hogy támogassa a több Azure Active Directory (Azure AD) bérlő felhasználóit, így az Azure Stack hub szolgáltatásainak használatát is lehetővé teszi. Vegyük például a következő forgatókönyvet:

- Ön a contoso.onmicrosoft.com szolgáltatás rendszergazdája, ahol a Azure Stack hub telepítve van.
- Mary a fabrikam.onmicrosoft.com címtár-rendszergazdája, ahol a vendég felhasználók találhatók.
- Mary vállalata IaaS-és Péter-szolgáltatásokat kap a vállalattól, és lehetővé kell tennie a vendégeknek, hogy bejelentkezzenek és Azure Stack hub-erőforrásokat használjanak a contoso.onmicrosoft.com-ben.

Ez az útmutató ismerteti a szükséges lépéseket a forgatókönyv kontextusában a Azure Stack hub több-bérlős szolgáltatásának konfigurálásához. Ebben az esetben Önnek és Mária-nek végre kell hajtania a lépéseket, amelyek lehetővé teszik a fabrikam számára, hogy bejelentkezzenek és felhasználják a szolgáltatásait a contoso Azure Stack hub üzembe helyezésével.

## <a name="enable-multi-tenancy"></a>A több-bérlős üzemmód engedélyezése

A Azure Stack hub-beli több bérlő konfigurálása előtt néhány előfeltételt kell figyelembe vennie:
  
 - Önnek és Mary-nek koordinálnia kell a felügyeleti lépéseket mind a Directory Azure Stack hub (contoso), mind a Guest Directory (Fabrikam) között.
 - Győződjön meg arról, hogy [telepítette](azure-stack-powershell-install.md) és [konfigurálta](azure-stack-powershell-configure-admin.md) a PowerShellt Azure stack hubhoz.
 - [Töltse le az Azure stack hub-eszközöket](azure-stack-powershell-download.md), és importálja a csatlakozási és Identity modulokat:

    ```powershell
    Import-Module .\Connect\AzureStack.Connect.psm1
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

#### <a name="registering-azure-stack-hub-with-the-guest-directory"></a>Azure Stack hub regisztrálása a vendég címtárral

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

Most, hogy Mary elvégezte a Mary címtár bevezetésének lépéseit, Mária a fabrikam-felhasználókat a bejelentkezéshez irányíthatja. A fabrikam felhasználói (fabrikam.onmicrosoft.com utótaggal rendelkező felhasználók) bejelentkezhetnek a https\://Portal.local.azurestack.external.

Mary a fabrikam címtárban (a fabrikam-címtárban lévő felhasználók a fabrikam.onmicrosoft.com utótagja nélkül) minden [idegen rendszerbiztonsági tag](/azure/role-based-access-control/rbac-and-directory-admin-roles) számára a bejelentkezést a https\://Portal.local.azurestack.external/fabrikam.onmicrosoft.com. használatával. Ha nem használják ezt az URL-címet, azokat a rendszer az alapértelmezett könyvtárba (Fabrikam) küldi, és hibaüzenetet kap, amely szerint a rendszergazda nem járult hozzá.

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

## <a name="next-steps"></a>Következő lépések

- [Delegált szolgáltatók kezelése](azure-stack-delegated-provider.md)
- [Azure Stack hub főbb fogalmak](azure-stack-overview.md)
- [Azure Stack hub használatának és számlázásának kezelése felhőalapú megoldás-szolgáltatóként](azure-stack-add-manage-billing-as-a-csp.md)
- [Bérlő hozzáadása a Azure Stack hubhoz való használathoz és számlázáshoz](azure-stack-csp-howto-register-tenants.md)
