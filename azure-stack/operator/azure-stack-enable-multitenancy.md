---
title: Több-bérlős az Azure Stackben
description: Ismerje meg, hogyan támogatja a több Azure Active Directory-címtár az Azure Stackben
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
ms.date: 03/04/2019
ms.author: patricka
ms.reviewer: bryanr
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: a35b16d2c862547ffed340f3e06dc769e373eb85
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64296676"
---
# <a name="multi-tenancy-in-azure-stack"></a>Több-bérlős az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Konfigurálhatja az Azure Stack-szolgáltatások használatára az Azure Stackben több Azure Active Directory (Azure AD) bérlő felhasználók támogatására. Vegyük példaként az alábbi forgatókönyvet:

- Most már a szolgáltatás-rendszergazda contoso.onmicrosoft.com, amelyen telepítve van-e az Azure Stack.
- Mary a címtár rendszergazdájának fabrikam.onmicrosoft.com, hol találhatók vendégfelhasználók.
- Mária vállalati IaaS és PaaS-szolgáltatások fogadja a cégtől, és van szüksége, hogy a felhasználók a Vendég könyvtárból (fabrikam.onmicrosoft.com) jelentkezik be, és a contoso.onmicrosoft.com Azure Stack-erőforrások használata.

Ez az Útmutató lépéseit, ebben az esetben a környezetében történő konfigurálásához szükséges több-bérlős az Azure Stackben. Ebben a forgatókönyvben, és Mary kell befejezéséhez jelentkezzen be, és az Azure Stack üzembe helyezés a Contoso szolgáltatásait igénybe a Fabrikam felhasználók engedélyezésének lépései.  

## <a name="enable-multi-tenancy"></a>A több-bérlős üzemmód engedélyezése

Van néhány előfeltételeket, hogy figyelembe vegye az Azure Stackben több-bérlős konfigurálása előtt:
  
 - Ön és Mary kell rendszergazdai lépések erőforrások közötti koordinációhoz is az Azure Stack (Contoso) van telepítve, és a Vendég könyvtárat (a Fabrikam).  
 - Győződjön meg arról, hogy [telepített](azure-stack-powershell-install.md) és [konfigurált](azure-stack-powershell-configure-admin.md) PowerShell az Azure Stackhez.
 - [Töltse le az Azure Stack eszközöket](azure-stack-powershell-download.md), és a Connect és az identitás-modulok importálása:

    ```powershell  
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-directory"></a>Azure Stack-címtár konfigurálása

Ebben a szakaszban az Azure Stack bejelentkezések engedélyezéséhez a Fabrikam az Azure Active directory-bérlők fog konfigurálni.

Előkészítése az Azure Stackhez az Azure Resource Manager egyszerű szolgáltatások a Vendég directory-bérlőhöz, és fogadja el a felhasználók konfigurálásával vendég Címtárbérlőben (Fabrikam).

A contoso.onmicrosoft.com szolgáltatás-rendszergazda futtatja a következő parancsokat.

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group. 
$location = "local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName
```

### <a name="configure-guest-directory"></a>Vendég címtár konfigurálása

Egyszer az Azure Stack rendszergazdai / operátor engedélyezte a Fabrikam könyvtár használható az Azure Stack, Mary regisztrálnia kell az Azure Stack, a Fabrikam repülőmotorjai directory bérlővel.

#### <a name="registering-azure-stack-with-the-guest-directory"></a>Azure Stack Regisztrálás a Vendég-könyvtár

Mary Fabrikam Directory rendszergazdájának az alábbi parancsokat a Vendég directory fabrikam.onmicrosoft.com futtatható.

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
```

> [!IMPORTANT]
> Ha az Azure Stack rendszergazdai a jövőben telepít új szolgáltatások vagy frissítések, szükség lehet futtassa ismét ezt a szkriptet.
>
> Ez a szkript futtatása újra bármikor ellenőrizheti az állapotot az Azure Stack-alkalmazások a címtárban.
>
> Ha új virtuális gépek létrehozása a Managed Disks (a 1808 frissítés jelent meg), a problémák megfigyelte **lemez erőforrás-szolgáltató** lett hozzáadva, igénylő futtassa újra ezt a szkriptet.

### <a name="direct-users-to-sign-in"></a>Jelentkezzen be a közvetlen felhasználók

Most, hogy Ön és Mary végrehajtotta a előkészítése Mary könyvtárba, Mary irányíthatók a Fabrikam felhasználók jelentkezhetnek be.  Fabrikam felhasználók (vagyis a fabrikam.onmicrosoft.com utótagjával rendelkező felhasználók) jelentkezzen be funkcionáló https://portal.local.azurestack.external.  

Mary átirányítja bármely [külső principals]((/azure/role-based-access-control/rbac-and-directory-admin-roles) a Fabrikam könyvtárban (azt jelenti, anélkül, hogy a fabrikam.onmicrosoft.com utótagja a Fabrikam címtárban felhasználó), jelentkezzen be a https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Ha az URL-cím nem használnak, a rendszer az alapértelmezett könyvtára (Fabrikam), és egy hibaüzenetet, amely szerint a rendszergazda nem egyezett bele.

## <a name="disable-multi-tenancy"></a>Több-bérlős letiltása

Ha már nem szeretne több bérlő számára az Azure Stackben, letilthatja a több-bérlős végrehajtásával sorrendben az alábbi lépéseket:

1. Futtassa a vendégcímtár (ebben a forgatókönyvben Mary), rendszergazdaként *Unregister-AzsWithMyDirectoryTenant*. A parancsmag eltávolítja az Azure Stack-alkalmazásokat az új könyvtárból.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. A szolgáltatás-rendszergazda az Azure Stack (meg ebben a forgatókönyvben), futtassa, *Unregister-AzSGuestDirectoryTenant*. 

    ``` PowerShell  
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
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
    > A letiltás több-bérlős lépések sorrendben kell végrehajtani. #1. lépés sikertelen lesz, ha először #2. lépés befejeződött.

## <a name="next-steps"></a>További lépések

- [Delegált szolgáltatók kezelése](azure-stack-delegated-provider.md)
- [Az Azure Stack főbb fogalmak](azure-stack-overview.md)
- [Az Azure Stack használatának és számlázásának felügyelete felhőszolgáltatóként](azure-stack-add-manage-billing-as-a-csp.md)
- [Bérlő hozzáadása a használathoz és a számlázáshoz az Azure Stackben](azure-stack-csp-howto-register-tenants.md)
