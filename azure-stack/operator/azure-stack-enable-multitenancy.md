---
title: Több bérlő konfigurálása a Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti és tilthatja le több Azure Active Directory bérlőt Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: bryanr
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 168565a47a7c3511111fbae565e80dbfe0e3c606
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019373"
---
# <a name="configure-multi-tenancy-in-azure-stack"></a>Több-bérlő konfigurálása Azure Stack

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A Azure Stack konfigurálható úgy, hogy támogassa a több Azure Active Directory (Azure AD) bérlők felhasználóit, így a Azure Stack szolgáltatásainak használatát is lehetővé teszi. Vegyük például a következő forgatókönyvet:

- Ön a contoso.onmicrosoft.com szolgáltatás rendszergazdája, ahol a Azure Stack telepítve van.
- Mary a fabrikam.onmicrosoft.com címtár-rendszergazdája, ahol a vendég felhasználók találhatók.
- Mary vállalata IaaS-és Péter-szolgáltatásokat kap a vállalattól, és lehetővé teszi a vendégeknek, hogy bejelentkezzenek, és Azure Stack erőforrásokat használjanak a contoso.onmicrosoft.com-ben.

Ez az útmutató ismerteti a szükséges lépéseket ezen forgatókönyv kontextusában a több-bérlő konfigurálásához Azure Stackban. Ebben az esetben Önnek és Mária-nek végre kell hajtania a lépéseket, amelyek lehetővé teszik a fabrikam számára, hogy bejelentkezzenek és felhasználják a szolgáltatásait a contoso Azure Stack üzembe helyezésével.

## <a name="enable-multi-tenancy"></a>A több-bérlős üzemmód engedélyezése

Néhány előfeltételt figyelembe kell vennie, mielőtt több-bérlőt konfigurál a Azure Stackban:
  
 - Önnek és Mary-nek össze kell hangolnia a felügyeleti lépéseket mind a címtár Azure Stack a (contoso) és a vendég Directory (Fabrikam) között.
 - Győződjön meg arról, hogy [telepítette](azure-stack-powershell-install.md) és [konfigurálta](azure-stack-powershell-configure-admin.md) a PowerShellt Azure Stackhoz.
 - [Töltse le a Azure stack eszközöket](azure-stack-powershell-download.md), és importálja a csatlakozási és Identity modulokat:

    ```powershell
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-directory"></a>Azure Stack könyvtár konfigurálása

Ebben a szakaszban úgy konfigurálja Azure Stack, hogy engedélyezze a fabrikam Azure AD címtár-bérlők bejelentkezését.

A vendég Directory-bérlő (Fabrikam Azure Stack) bevezetéséhez konfigurálja a Azure Resource Manager, hogy fogadja a felhasználókat és az egyszerű szolgáltatásokat a vendég címtár bérlője számára.

A contoso.onmicrosoft.com szolgáltatás rendszergazdája a következő parancsokat futtatja:

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
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

Ha a Azure Stack operátor engedélyezte a fabrikam-címtár használatát a Azure Stack, Mary-nek regisztrálnia kell Azure Stack a fabrikam címtár-Bérlővel.

#### <a name="registering-azure-stack-with-the-guest-directory"></a>Azure Stack regisztrálása a vendég címtárban

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
> Ha a Azure Stack rendszergazdája új szolgáltatásokat vagy frissítéseket telepít a jövőben, lehetséges, hogy újra futtatnia kell ezt a parancsfájlt.
>
> Futtassa újra ezt a parancsfájlt a címtárban található Azure Stack-alkalmazások állapotának vizsgálatához.
>
> Ha észrevette a virtuális gépek létrehozásával kapcsolatos problémákat Managed Disks (az 1808-es frissítésben bemutatott), egy új **lemez erőforrás-szolgáltató** lett hozzáadva, amely megköveteli, hogy ez a parancsfájl újra fusson.

### <a name="direct-users-to-sign-in"></a>Közvetlen bejelentkezés a felhasználók számára

Most, hogy Mary elvégezte a Mary címtár bevezetésének lépéseit, Mária a fabrikam-felhasználókat a bejelentkezéshez irányíthatja. A fabrikam felhasználói (fabrikam.onmicrosoft.com utótaggal rendelkező felhasználók) bejelentkezhetnek a https\://Portal.local.azurestack.external.

Mary a fabrikam címtárban (a fabrikam-címtárban lévő felhasználók a fabrikam.onmicrosoft.com utótagja nélkül) minden [idegen rendszerbiztonsági tag](/azure/role-based-access-control/rbac-and-directory-admin-roles) számára a bejelentkezést a https\://Portal.local.azurestack.external/fabrikam.onmicrosoft.com. használatával. Ha nem használják ezt az URL-címet, azokat a rendszer az alapértelmezett könyvtárba (Fabrikam) küldi, és hibaüzenetet kap, amely szerint a rendszergazda nem járult hozzá.

## <a name="disable-multi-tenancy"></a>Több-bérlő letiltása

Ha a továbbiakban nem szeretne több bérlőt használni a Azure Stackban, a következő lépések végrehajtásával letilthatja a többszörös kiszolgálat:

1. A vendég könyvtára rendszergazdájaként (ebben a forgatókönyvben Mária) futtassa a *Regisztráció törlése-AzsWithMyDirectoryTenant*. A parancsmag eltávolítja az összes Azure Stack alkalmazást az új könyvtárból.

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

2. Azure Stack szolgáltatás-rendszergazdájaként (ebben a forgatókönyvben) futtassa a *Regisztráció törlése-AzSGuestDirectoryTenant*.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack directory
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

## <a name="next-steps"></a>További lépések

- [Delegált szolgáltatók kezelése](azure-stack-delegated-provider.md)
- [Azure Stack alapfogalmak](azure-stack-overview.md)
- [A Azure Stack használatának és számlázásának kezelése felhőalapú megoldás-szolgáltatóként](azure-stack-add-manage-billing-as-a-csp.md)
- [Bérlő hozzáadása a használathoz és a számlázáshoz az Azure Stackben](azure-stack-csp-howto-register-tenants.md)
