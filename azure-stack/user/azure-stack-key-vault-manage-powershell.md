---
title: Azure Stack hub Key Vault kezelése a PowerShell használatával
description: Megtudhatja, hogyan kezelheti Key Vault a Azure Stack hub-ban a PowerShell használatával.
author: sethmanheim
ms.topic: article
ms.date: 11/20/2020
ms.author: sethm
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 0d7ce96b1619c21c5f442ab4d5a240a9eed16397
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518347"
---
# <a name="manage-key-vault-in-azure-stack-hub-using-powershell"></a>Azure Stack hub Key Vault kezelése a PowerShell használatával

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet egy kulcstartót Azure Stack hub-ban a PowerShell használatával. Megtudhatja, hogyan használhatja Key Vault PowerShell-parancsmagokat a következőkre:

* Kulcstartó létrehozása.
* Titkosítási kulcsok és titkos kódok tárolása és kezelése.
* Engedélyezi, hogy a felhasználók vagy alkalmazások meghívjanak műveleteket a tárolóban.

>[!NOTE]
>A cikkben ismertetett Key Vault PowerShell-parancsmagok a Azure PowerShell SDK-ban találhatók.

## <a name="prerequisites"></a>Előfeltételek

* Elő kell fizetnie egy olyan ajánlatra, amely tartalmazza a Azure Key Vault szolgáltatást.
* [Telepítse a powershellt Azure stack hubhoz](../operator/powershell-install-az-module.md).
* [Konfigurálja az Azure stack hub PowerShell-környezetét](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Bérlői előfizetés engedélyezése Key Vault műveletekhez

Mielőtt bármilyen műveletet kiállít egy kulcstartón, gondoskodnia kell arról, hogy a bérlői előfizetés engedélyezve legyen a tár műveleteihez. Annak ellenőrzéséhez, hogy a Key Vault-műveletek engedélyezve vannak-e, futtassa a következő parancsot:

### <a name="az-modules"></a>[Az modulok](#tab/az1)

```powershell  
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```
### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm1)
 

 ```powershell  
Get-AzureRMResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```
---


Ha az előfizetése engedélyezve van a tár műveleteihez, a kimenet a kulcstartó összes **RegistrationState** **regisztrálva** jelenik meg.

![Key Vault regisztrációs állapota a PowerShellben](media/azure-stack-key-vault-manage-powershell/image1.png)

Ha a tár műveletei nincsenek engedélyezve, adja ki a következő parancsot a Key Vault szolgáltatás regisztrálásához az előfizetésében:

### <a name="az-modules"></a>[Az modulok](#tab/az2)

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm2)
 
 ```powershell
Register-AzureRMResourceProvider -ProviderNamespace Microsoft.KeyVault
```

---


Ha a regisztráció sikeres, a rendszer a következő kimenetet adja vissza:

![A Key Vault-regisztráció sikeresen megtörtént a PowerShellben](media/azure-stack-key-vault-manage-powershell/image2.png)

A Key Vault-parancsok meghívásakor előfordulhat, hogy hibaüzenetet kap, például "az előfizetés nincs regisztrálva a Microsoft. kulcstartó" névtér használatára. " Ha hibaüzenetet kap, erősítse meg, hogy engedélyezte a Key Vault erőforrás-szolgáltatót az előző utasítások alapján.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

A kulcstartó létrehozása előtt hozzon létre egy erőforráscsoportot, hogy a kulcstartóhoz kapcsolódó összes erőforrás létezik egy erőforráscsoporthoz. Új erőforráscsoport létrehozásához használja a következő parancsot:

### <a name="az-modules"></a>[Az modulok](#tab/az3)

```powershell
New-AzResourceGroup -Name "VaultRG" -Location local -verbose -Force
```
### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm3)
 
 ```powershell
New-AzureRMResourceGroup -Name "VaultRG" -Location local -verbose -Force
```

---


![Új erőforráscsoport létrehozva a PowerShellben](media/azure-stack-key-vault-manage-powershell/image3.png)

Most a következő parancsmaggal hozzon létre egy Key vaultot a korábban létrehozott erőforráscsoporthoz. Ez a parancs három kötelező paramétert olvas: erőforráscsoport neve, kulcstároló neve és földrajzi hely.

A Key Vault létrehozásához futtassa a következő parancsot:

### <a name="az-modules"></a>[Az modulok](#tab/az4)

```powershell
New-AzKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```
   
### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm4)
 
```powershell
New-AzureRMKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

---


![Új kulcstartó létrehozva a PowerShellben](media/azure-stack-key-vault-manage-powershell/image4.png)

A parancs kimenete a létrehozott kulcstartó tulajdonságait jeleníti meg. Amikor egy alkalmazás hozzáfér a tárolóhoz, a tároló **URI** tulajdonságát kell használnia, amely `https://vault01.vault.local.azurestack.external` ebben a példában szerepel.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Active Directory összevonási szolgáltatások (AD FS) (AD FS) üzembe helyezése

AD FS központi telepítés esetén a következő figyelmeztetés jelenik meg: "a hozzáférési szabályzat nincs beállítva. Egyetlen felhasználónak vagy alkalmazásnak nincs hozzáférési engedélye a tár használatára. " A probléma megoldásához állítson be egy hozzáférési házirendet a tárolóhoz a [**set-AzKeyVaultAccessPolicy**](#authorize-an-app-to-use-a-key-or-secret) parancs használatával:

### <a name="az-modules"></a>[Az modulok](#tab/az5)

```powershell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm5)

```powershell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRMKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

---


## <a name="manage-keys-and-secrets"></a>Kulcsok és titkos kódok kezelése

A tároló létrehozása után a következő lépésekkel hozhat létre és kezelhet kulcsokat és titkos kódokat a tárolóban.

### <a name="create-a-key"></a>Kulcs létrehozása

Szoftveres védelemmel ellátott kulcs létrehozása vagy importálása a Key vaultban az **Add-AzureKeyVaultKey** parancsmag használatával:

```powershell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```


A `-Destination` paraméter használatával adható meg, hogy a kulcs szoftveres védelemmel van ellátva. A kulcs sikeres létrehozása után a parancs kiírja a létrehozott kulcs részleteit.

![Új Key Vault-kulcs létrehozva a PowerShellben](media/azure-stack-key-vault-manage-powershell/image5.png)

Ezután hivatkozhat a létrehozott kulcsra az URI használatával. Ha olyan kulcsot hoz létre vagy importál, amelynek a neve megegyezik egy meglévő kulccsal, az eredeti kulcsot az új kulcsban megadott értékekkel frissíti a rendszer. A korábbi verziót a kulcs Version-specifikus URI-ja segítségével érheti el. Például:

* `https://vault10.vault.local.azurestack.external:443/keys/key01`A használatával mindig megkapja az aktuális verziót.
* `https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a`Ezzel a verzióval szerezheti be a megadott verziót.

### <a name="get-a-key"></a>Kulcs lekérése

A **Get-AzureKeyVaultKey** parancsmag használatával olvassa el a kulcsot és annak részleteit:

```powershell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>Titkos kulcs létrehozása

A **set-AzureKeyVaultSecret** parancsmag használatával hozzon létre vagy frissítsen egy titkos kulcsot egy tárolóban. A rendszer titkos kulcsot hoz létre, ha még nem létezik ilyen. Ha már létezik, a rendszer létrehozza a titkos kulcs új verzióját:

```powershell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

![Titkos kód létrehozása a PowerShellben](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Titkos kód beszerzése

A **Get-AzureKeyVaultSecret** parancsmaggal olvashatja el a titkos kulcsot a kulcstartóban. Ez a parancs a titkos kulcs összes vagy meghatározott verzióját visszaadhatja:

```powershell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

A kulcsok és a titkos kódok létrehozása után engedélyezheti a külső alkalmazások használatát.

## <a name="authorize-an-app-to-use-a-key-or-secret"></a>Kulcs vagy titkos kód használatának engedélyezése az alkalmazás számára

A következő parancsmaggal engedélyezheti, hogy egy alkalmazás hozzáférjen egy kulcshoz vagy titkos kulcshoz a kulcstartóban.

A következő példában a tár neve **ContosoKeyVault**, és az engedélyezni kívánt alkalmazás a **8F8C4BBD-485B-45FD-98F7-EC6300B7B4ED** ügyfél-azonosítója. Az alkalmazás engedélyezéséhez futtassa a következő parancsot. A **PermissionsToKeys** paramétert is megadhatja egy felhasználó, egy alkalmazás vagy egy biztonsági csoport engedélyeinek megadásához.

Ha a parancsmagot egy AD FS konfigurált Azure Stack hub-környezethez használja, meg kell adni a BypassObjectIdValidation paramétert.

### <a name="az-modules"></a>[Az modulok](#tab/az6)

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign -BypassObjectIdValidation
```
### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm6)

```powershell
Set-AzureRMKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign -BypassObjectIdValidation
```

---


Ha engedélyezni szeretné, hogy ugyanaz az alkalmazás beolvassa a titkos kulcsokat a tárolóban, futtassa a következő parancsmagot:

### <a name="az-modules"></a>[Az modulok](#tab/az7)

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get -BypassObjectIdValidation
```

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm7)

```powershell
Set-AzureRMKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get -BypassObjectIdValidation
```

---


## <a name="next-steps"></a>Következő lépések

* [Virtuális gép üzembe helyezése Key Vault tárolt jelszóval](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Virtuális gép üzembe helyezése Key Vault tárolt tanúsítvánnyal](azure-stack-key-vault-push-secret-into-vm.md)
