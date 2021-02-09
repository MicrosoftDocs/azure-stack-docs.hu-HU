---
title: Több-bérlő konfigurálása Azure Stack központban
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directory bérlők több-bérletét Azure Stack hub-ban.
author: BryanLa
ms.topic: how-to
ms.date: 01/26/2021
ms.author: bryanla
ms.reviewer: bryanr
ms.lastreviewed: 01/26/2021
ms.openlocfilehash: 3de6c5db42285f90e1d4ce6c1ebf6736d7ce4863
ms.sourcegitcommit: d542b68b299b73e045f30916afb6018e365e9db6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975945"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>Több-bérlő konfigurálása Azure Stack központban

Azure Stack hub-t úgy konfigurálhatja, hogy támogassa a más Azure Active Directory (Azure AD-) címtárakban található felhasználóktól érkező bejelentkezéseket, lehetővé téve számukra az Azure Stack hub szolgáltatásainak használatát. Ezek a címtárak "vendég" kapcsolattal rendelkeznek az Azure Stack hubhoz, és mint ilyen, a vendég Azure AD-bérlőnek számítanak. Vegyük példaként a következő esetet:

- Ön a contoso.onmicrosoft.com szolgáltatás rendszergazdája, a Home Azure AD-bérlő identitás-és hozzáférés-kezelési szolgáltatásokat biztosít a Azure Stack hub számára.
- Mary a fabrikam.onmicrosoft.com címtár-rendszergazdája, a vendég Azure AD-bérlő, ahol a vendég felhasználók találhatók.
- Mary vállalata (Fabrikam) IaaS-és Pásti-szolgáltatásokat használ a vállalattól. A fabrikam lehetővé szeretné tenni a vendégeknek, hogy bejelentkezzenek, és Azure Stack hub-erőforrásokat használjanak a contoso.onmicrosoft.com által védett fabrikam.onmicrosoft.com.

Ez az útmutató ismerteti a szükséges lépéseket ebben a forgatókönyvben, hogy engedélyezze vagy tiltsa le a több-bérlős szolgáltatásokat Azure Stack hub-ban egy vendég címtár-bérlő számára. Ezt a folyamatot úgy érheti el, ha regisztrálja/törli a vendég címtár bérlőjét, amely engedélyezi vagy letiltja Azure Stack hub-bejelentkezések és-szolgáltatások felhasználását a fabrikam-felhasználók számára. 

Ha Ön egy felhőalapú megoldás-szolgáltató (CSP), akkor további módon [konfigurálhatja és kezelheti a több-bérlős Azure stack hubot](azure-stack-add-manage-billing-as-a-csp.md). 

## <a name="prerequisites"></a>Előfeltételek

A vendég címtár regisztrációja vagy törlése előtt Önnek és Mary-nek el kell végeznie a megfelelő Azure AD-bérlők felügyeleti lépéseit: az Azure Stack hub kezdőkönyvtár (contoso) és a vendég könyvtár (Fabrikam):

 - [Telepítse](powershell-install-az-module.md) és [konfigurálja](azure-stack-powershell-configure-admin.md) a PowerShellt Azure stack hubhoz.
 - [Töltse le az Azure stack hub-eszközöket](azure-stack-powershell-download.md), és importálja a csatlakozási és Identity modulokat:

    ```powershell
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

## <a name="register-a-guest-directory"></a>Vendég címtár regisztrálása

A több-bérlős vendég címtárának regisztrálásához a Home Azure Stack hub címtárát és a vendég könyvtárat is konfigurálni kell.

#### <a name="configure-azure-stack-hub-directory"></a>Azure Stack hub könyvtárának konfigurálása

A contoso.onmicrosoft.com szolgáltatás-rendszergazdája először a fabrikam vendég Directory-bérlőjét Azure Stack hubhoz kell bevezetni. A következő szkript konfigurálja Azure Resource Managert, hogy fogadja a felhasználók és az egyszerű szolgáltatások fabrikam.onmicrosoft.com-bérlőben való bejelentkezését:

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as adminmanagement.<region>.<FQDN>.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack Hub directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest directory tenant. 
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

#### <a name="configure-guest-directory"></a>A vendég könyvtárának konfigurálása

Ezután Mária (a fabrikam címtár-rendszergazdája) regisztrálnia kell Azure Stack hubot a fabrikam.onmicrosoft.com Guest Directoryval a következő parancsfájl futtatásával:

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as management.<region>.<FQDN>.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest directory tenant.
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

Végül Mary @fabrikam.onmicrosoft.com a [Azure stack hub felhasználói portálon](../user/azure-stack-use-portal.md)meglátogatva irányíthatja a fabrikam-felhasználókat fiókokkal a bejelentkezéshez. A többcsomópontos rendszerek esetében a felhasználói portál URL-címe a következőként van formázva: `https://management.<region>.<FQDN>` . ASDK-telepítés esetén az URL-cím a következő: `https://portal.local.azurestack.external` .

Mary-nek Emellett minden idegen rendszerbiztonsági tag (a fabrikam-címtárban lévő felhasználók, a fabrikam.onmicrosoft.com utótag nélkül) is el kell irányítania a bejelentkezést a használatával `https://<user-portal-url>/fabrikam.onmicrosoft.com` . Ha nem adják meg a `/fabrikam.onmicrosoft.com` címtár-bérlőt az URL-címben, azok az alapértelmezett könyvtárba lesznek küldve, és olyan hibaüzenetet kapnak, amely szerint a rendszergazda nem járult hozzá.

## <a name="unregister-a-guest-directory"></a>Vendég címtár regisztrációjának törlése

Ha már nem szeretné engedélyezni a bejelentkezést Azure Stack hub-szolgáltatások számára egy vendég címtár-bérlőről, törölheti a címtár regisztrációját. A Kezdőlap Azure Stack hub könyvtárat és a vendég könyvtárat is konfigurálni kell:

1. A vendég könyvtára rendszergazdájaként (ebben a forgatókönyvben Mária) futtassa a parancsot `Unregister-AzsWithMyDirectoryTenant` . A parancsmag eltávolítja az összes Azure Stack hub alkalmazást az új könyvtárból.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as management.<region>.<FQDN>.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest directory tenant.
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Azure Stack hub szolgáltatás-rendszergazdájaként (ebben a forgatókönyvben) futtassa a következő `Unregister-AzSGuestDirectoryTenant` parancsmagot:

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as adminmanagement.<region>.<FQDN>.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack Hub directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest directory tenant. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant resource was created (resource group must already exist).
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

## <a name="update-azure-ad-tenant-permissions"></a>Azure AD-bérlői engedélyek frissítése

Ez a művelet törli a riasztást Azure Stack központban, ami azt jelzi, hogy egy könyvtár frissítésre szorul. Futtassa a következő parancsot a **Azurestack-Tools-Master/Identity** mappában:

```powershell
Import-Module ..\Identity\AzureStack.Identity.psm1

$adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"

# This is the primary tenant Azure Stack Hub is registered to:
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
