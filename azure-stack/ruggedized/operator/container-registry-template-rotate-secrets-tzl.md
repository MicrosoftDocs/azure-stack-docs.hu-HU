---
title: A tároló-beállításjegyzék titkainak elforgatása Azure Stack hub-ban | Microsoft Docs
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan forgathatja el a Container Registry-titkokat Azure Stack központban.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2020
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: ed1809d44f54ec65c60c5a1e17ca472ee401b5ba
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939835"
---
# <a name="rotate-container-registry-secrets-in-azure-stack-hub"></a>A tároló-beállításjegyzék titkainak elforgatása Azure Stack központban

A Azure Stack hub-felhasználók elhelyezhetik a tárolók beállításjegyzék-sablonjának titkait (tanúsítványokat, felhasználóneveket és jelszavakat). Parancsfájl futtatásával feltöltheti Microsoft Azure Key Vault új titkos értékeit, és újból **üzembe helyezheti** a meglévő tároló beállításjegyzék-sablonját. A titkos kódok saját maguk általi elforgatása nem igényel új telepítést.

## <a name="prerequisites-for-the-user"></a>A felhasználó előfeltételei

 - A felhasználónak telepítenie kell a Azure Stack hub PowerShell-modult. További információ: [a PowerShell telepítése Azure Stackhoz](../../operator/azure-stack-powershell-install.md?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure-stack%2Fbreadcrumb%2Ftoc.json).

 - Szerezze be a tároló beállításjegyzék-sablonjának frissítési titkait. A Docker-beállításjegyzék eléréséhez használhat új SSL-tanúsítványt vagy új felhasználónevet és jelszót.

 - A `\registry\scripts` zip-fájlnak a [msazurestackworkloads/azurestack-Gallery GitHub-](https://github.com/msazurestackworkloads/azurestack-gallery/archive/master.zip) tárházból való letöltését követően kapott parancsfájlok beszerzése.

## <a name="import-new-secrets-into-key-vault"></a>Új titkok importálása Key Vaultba

Az alábbi útmutatást követve új titkokat állíthat be Key Vaultban.

### <a name="set-updated-registry-user-password-for-existing-username"></a>A beállításjegyzék frissített felhasználói jelszavának beállítása meglévő felhasználónévhez

1.  Nyisson meg egy rendszergazda jogú PowerShell-parancssort, majd futtassa `Import-Module .\\pre-reqs.ps1` a parancsot a parancsfájlok mappából.

2.  A meglévő beállításjegyzék-felhasználó értékének frissítéséhez futtassa a következő parancsmagot:

    ```powershell  
    Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName <username> `
        -RegistryUserPassword <newpassword> `
        -SkipExistCheck $true
    ```
    A parancsmag például a következő kimenetet adja vissza:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName admin `
        -RegistryUserPassword password1 `
        -SkipExistCheck $true 
    
    Check if key vault secret name (admin) exists.
    Creating key vault secret name (admin) as it does not exist.
    ```

1.  Ha szeretné ellenőrizni, hogy a rekordhoz új érték lett megadva, nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName newregkv -Name admin -IncludeVersions
    ```
    A parancsmag például a következő kimenetet adja vissza:
    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name admin -IncludeVersions
    
    
    Vault Name   : newregkv
    Name         : admin
    Version      : 2a1495372c474cc890c888518f02b19f
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/
                   admin/2a1495372c474cc890c888518f02b19f
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 7:05:56 PM
    Updated      : 12/18/2019 7:05:56 PM
    Content Type : 
    Tags         : 
    
    Vault Name   : newregkv
    Name         : admin
    Version      : 3fd65c1719c74997984648de18a1fa0e
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/
                   admin/3fd65c1719c74997984648de18a1fa0e
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/17/2019 5:05:56 AM
    Updated      : 12/17/2019 5:05:56 AM
    Content Type : user credentials
    Tags         : 
    ```

### <a name="set-new-registry-username-and-password"></a>Új beállításjegyzék-Felhasználónév és-jelszó beállítása

1.  Nyisson meg egy rendszergazda jogú parancssort, és `Import-Module .\pre-reqs.ps1` a szkriptek mappából.

2.  Az új Felhasználónév és jelszó új titkának létrehozásához nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

    ```powershell  
    Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName <newusername> `
        -RegistryUserPassword <newpassword> 
    ```
    A parancsmag például a következő kimenetet adja vissza:
    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName admin1 `
        -RegistryUserPassword password1
    
    Check if key vault secret name (admin1) exists.
    Creating key vault secret name (admin1) as it does not exist. 
    ```

1.  Az új titkos kulcs létrehozásának ellenőrzéséhez nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName \<KeyVaultName> -Name \<username>
    ```

    A parancsmag például a következő kimenetet adja vissza:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name admin1
    
    
    Vault Name   : newregkv
    Name         : admin1
    Version      : 2ae9a7239f4044be82ca9d1e9b80e85a
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/admin1/2ae9a7239f4044be82ca9d1e9b80e85a
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 11:28:18 PM
    Updated      : 12/18/2019 11:28:18 PM
    Content Type : user credentials
    Tags         : 
    ```

> [!Important]  
> Ha új titkot hoz létre (felhasználónév/jelszó kombináció), törölnie kell a régi Key Vault titkot. Ha újból telepíti a meglévő tároló beállításjegyzék-sablonját a régi titok törlése nélkül, akkor a régi és az új Felhasználónév és jelszó kombináció is érvényes lesz a beállításjegyzékbe való bejelentkezéshez.

### <a name="update-the-ssl-certificate-for-existing-key-vault-secret"></a>A meglévő Key Vault titkos kulcs SSL-tanúsítványának frissítése

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

    ```powershell
    Set-CertificateSecret -KeyVaultName \<keyvaultname> `
     -CertificateSecretName \<originalsecretnameforcertificate> `
    Set-CertificateSecret -KeyVaultName <keyvaultname> `
        -CertificateSecretName <originalsecretnameforcertificate> `
        -CertificateFilePath <pathtonewcertificate> `
        -CertificatePassword <certificatepassword> `
        -SkipExistCheck $true
    ```

    A parancsmag például a következő kimenetet adja vissza:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-CertificateSecret -KeyVaultName newregkv `
        -CertificateSecretName containersecret `
        -CertificateFilePath C:\crinstall\shanghairegcertnew.pfx `
        -CertificatePassword <certificatepassword> `
        -SkipExistCheck $true
    Check if key vault secret name (containersecret) exists.
    Creating key vault secret name (containersecret) as it does not exist.
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/997da68a-xxxx-xxxx-ad3d-ffeac81b02dc/resourceGroups/newregreg/providers/Microsoft.KeyVault/vaults/newregkv
    PFX KeyVaultSecretUrl        : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/a07ece6b9914408e8f20c516e15b66c9
    PFX Certificate Thumbprint   : 31810AA7FEF1173188691FB3F47208E5389FBA61
    ---------------------------------------------------------------- 
    ```

1.  A függvény által létrehozott értékeket fogja használni a meglévő tároló beállításjegyzék-sablonjának újbóli telepítésekor.

2.  Annak ellenőrzéséhez, hogy létrejött-e a meglévő titok új verziója, nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName <KeyVaultName> -Name <secretname>
    ```

    A parancsmag például a következő kimenetet adja vissza:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name containersecret -IncludeVersions
    
    
    Vault Name   : newregkv
    Name         : containersecret
    Version      : a07ece6b9914408e8f20c516e15b66c9
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/a07ece6b9914408e8f20c516e15b66c9
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 11:46:28 PM
    Updated      : 12/18/2019 11:46:28 PM
    Content Type : 
    Tags         : 
    
    Vault Name   : newregkv
    Name         : containersecret
    Version      : 0199c7ec1d8d41bb9ddff0f39dca9931
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/0199c7ec1d8d41bb9ddff0f39dca9931
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/17/2019 5:06:03 AM
    Updated      : 12/17/2019 5:06:03 AM
    Content Type : pfx
    Tags         : 
    ```

### <a name="set-a-new-ssl-certificate-for-the-container-registry-template"></a>Új SSL-tanúsítvány beállítása a tároló beállításjegyzék-sablonhoz

1.  Nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagot:

    ```powershell  
    Set-CertificateSecret -KeyVaultName <keyvaultname> `
        -CertificateSecretName <newsecretnameforcertificate> `
        -CertificateFilePath <pathtonewcertificate> `
        -CertificatePassword <certificatepassword>
    ```

    A parancsmag például a következő kimenetet adja vissza:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts>    Set-CertificateSecret -KeyVaultName newregkv `
        -CertificateSecretName containersecret121719 `
        -CertificateFilePath C:\crinstall\shanghairegcertnew.pfx `
        -CertificatePassword <certificatepassword> 
    Check if key vault secret name (containersecret121719) exists.
    Creating key vault secret name (containersecret121719) as it does not exist.
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/997da68a-xxxx-xxxx-ad3d-ffeac81b02dc/resourc
    eGroups/newregreg/providers/Microsoft.KeyVault/vaults/newregkv
    PFX KeyVaultSecretUrl        : https://newregkv.vault.shanghai.azurestack.corp.microsoft.c
    om:443/secrets/containersecret121719/bb2cfe4df7bc4fbe854a00799afa8566
    PFX Certificate Thumbprint   : 31810AA7FEF1173188691FB3F47208E5389FBA61 
    ```

## <a name="redeploy-existing-container-registry-template"></a>Meglévő tároló beállításjegyzék-sablonjának újbóli üzembe helyezése

1. Nyissa meg az Azure Stack hub felhasználói portált.

2.  Navigáljon ahhoz az erőforráscsoporthoz, amelyhez a tároló beállításjegyzék-sablonjának virtuális gépe telepítve van.

    ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-rotating-secrets-tzl/image1.png)

3. Válassza ki a **központi telepítések alatt** üzemelő példányokat.

    ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-rotating-secrets-tzl/image2.png)

4.  Ha az első alkalommal forog a titkos kulcsok, válassza ki az eredeti telepítést. Ha nem ez az első alkalom a titkok megforgatására, válassza ki a legutóbbi telepítést, majd válassza az **újratelepítés** lehetőséget.

    ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-rotating-secrets-tzl/image3.png)

5.  A **megoldás sablonjának üzembe helyezése** területen válassza a **meglévő erőforráscsoport használata** lehetőséget, majd válassza ki azt az erőforráscsoportot, amelyet eredetileg a Container Registry-sablon üzembe helyezéséhez használt. Ahhoz, hogy az újratelepítése sikeres legyen, ugyanazt az erőforráscsoportot kell használnia.

    ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-rotating-secrets-tzl/image4.png)

6.  A **paramétereknél** győződjön meg arról, hogy a paraméterek egyeznek az eredeti telepítéssel. Az egyszerű szolgáltatás ügyfél-AZONOSÍTÓját és az egyszerű szolgáltatás titkos kulcsát hozzá kell adni.

    - Ha csak a beállításjegyzék-szolgáltatás felhasználónevét és jelszavát adja meg, csak az egyszerű szolgáltatásnév paramétereit kell felvennie.

    - Ha elforgatja a tanúsítványt, a PFXKeyVaultSecretURL és a PFXThumbprint új értékeit kell megadnia, amelyek az új titkok beállításától származnak.

    ![Tároló beállításjegyzék-sablonja](./media/container-registry-template-rotating-secrets-tzl/image5.png)

7.  Válassza **az OK** , majd a **Létrehozás** lehetőséget. A rendszer folytatja az újratelepítést. Az újratelepítése során a beállításjegyzék funkciói továbbra is működni fognak.

    - Ha elforgatja a felhasználónevet és a jelszót, akkor az ismételt üzembe helyezés befejeződése után ismét hitelesítenie kell magát a beállításjegyzékben.

    - Ha elforgatja a tanúsítványt, a beállításjegyzékhez való hozzáférés elvesztését nem tapasztalhatja. Ez azt feltételezi, hogy megbízható tanúsítvány-szolgáltatótól származó tanúsítványt használ. Privát tanúsítvány használata esetén a tanúsítványt telepíteni kell az ügyfeleken a hozzáférés elvesztésének megakadályozása érdekében.

## <a name="next-steps"></a>Következő lépések

[Azure Stack Marketplace – áttekintés](../../operator/azure-stack-marketplace.md)
