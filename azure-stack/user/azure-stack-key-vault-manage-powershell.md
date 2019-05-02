---
title: A Key Vault az Azure Stackben kezelése a PowerShell használatával |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a Key Vault az Azure Stack PowerShell-lel
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: sethm
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 5fcf6f4db84a0421c508ac151e3c2f446a963f4c
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986123"
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>Kezelése a Key Vault az Azure Stack PowerShell-lel

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A Key Vault az Azure Stack PowerShell használatával kezelheti. További tudnivalók a Key Vault PowerShell-parancsmagok használatával:

* Kulcstartó létrehozása.
* Store és a titkosítási kulcsokat és titkos kódokat.
* Engedélyezze a felhasználónak vagy alkalmazásnak a tárban lévő műveletek meghívását.

>[!NOTE]
>A Key Vault PowerShell-parancsmagok ebben a cikkben leírt az Azure PowerShell SDK vannak megadva.

## <a name="prerequisites"></a>Előfeltételek

* Meg kell előfizetés egy ajánlatra, amely magában foglalja az Azure Key Vault szolgáltatást.
* [Az Azure Stack PowerShell telepítése](../operator/azure-stack-powershell-install.md).
* [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>A Key Vault-műveletek a bérlő előfizetés engedélyezése

Key vault kapcsolatos bármilyen művelet adhat ki, mielőtt kell győződjön meg arról, hogy a bérlő előfizetés engedélyezve van-e a vault-műveletek. Győződjön meg arról, hogy engedélyezve vannak-e a vault-műveletek, futtassa a következő parancsot:

```powershell  
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

**Kimenet**

Az előfizetés vault-műveletek engedélyezett, ha a kimenet mutatja, "RegistrationState" "regisztrálva" minden erőforrás esetében a key vault.

![A Key vault regisztrációs állapot](media/azure-stack-key-vault-manage-powershell/image1.png)

Ha vault-műveletek nem engedélyezettek, meghívása az előfizetés regisztrálása a Key Vault szolgáltatás a következő parancsot:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Kimenet**

Ha a regisztráció sikeres, a következő eredményt adja vissza:

![Regisztráljon](media/azure-stack-key-vault-manage-powershell/image2.png) a key vault-parancsokat indít el, amikor előfordulhat, hogy hibaüzenetet kap, mint például "az előfizetés nincs regisztrálva a névtér"Microsoft.KeyVault"." Ha hibaüzenetet kap, győződjön meg arról, hogy a Key Vault erőforrás-szolgáltató engedélyezte, hogy a korábban említett útmutatásai alapján.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Mielőtt egy kulcstartót hoz létre, hozzon létre egy erőforráscsoportot, hogy az összes olyan erőforrást a key vaulttal kapcsolatos egy erőforráscsoportban létezik. A következő paranccsal hozzon létre egy új erőforráscsoportot:

```powershell
New-AzureRmResourceGroup -Name "VaultRG" -Location local -verbose -Force

```

**Kimenet**

![Új erőforráscsoport](media/azure-stack-key-vault-manage-powershell/image3.png)

Mostantól használhatja a **New-AzureRMKeyVault** parancs használatával hozzon létre egy kulcstartót a korábban létrehozott erőforráscsoportban. Ez a parancs beolvassa a három kötelező paraméterrel: erőforráscsoport-nevet, a kulcstároló nevét és a földrajzi hely.

Futtassa a key vault létrehozása a következő parancsot:

```powershell
New-AzureRmKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

**Kimenet**

![Új kulcstartó](media/azure-stack-key-vault-manage-powershell/image4.png)

Ez a parancs kimenetét mutatja az Ön által létrehozott kulcstartó tulajdonságait. Ha egy alkalmazás hozzáfér az ebben a tárban, azt kell használnia a **Vault URI** tulajdonság, amely "https:\//vault01.vault.local.azurestack.external" Ebben a példában.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Az Active Directory összevonási szolgáltatások (AD FS) központi telepítés

Az AD FS telepítése az ezt a figyelmeztetést kaphat: "A hozzáférési szabályzat nincs beállítva. Nincs felhasználó vagy alkalmazás nem hozzáférési engedélyt a tár használatára." A probléma megoldásához állítsa be a tároló hozzáférési házirend használatával a [Set-AzureRmKeyVaultAccessPolicy](#authorize-an-application-to-use-a-key-or-secret) parancsot:

```powershell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Kulcsok és titkos kulcsok kezelése

Miután létrehozott egy tárolót, a következő eljárással hozhat létre, és a kulcsokat és titkos kódokat, a tárolóban.

### <a name="create-a-key"></a>Kulcs létrehozása

Használja a **Add-AzureKeyVaultKey** parancs használatával hozzon létre vagy importáljon egy szoftveresen védett kulcsot a kulcstartóban.

```powershell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

A **cél** paraméter adja meg, hogy a kulcs által védett szoftver használható. Ha a kulcs sikeresen létrejött, a parancsot a létrehozott kulcs részleteinek jelenít meg.

**Kimenet**

![Új kulcs](media/azure-stack-key-vault-manage-powershell/image5.png)

A létrehozott kulcsot az URI használatával hivatkozhat. Hozzon létre vagy importálhat egy kulcsot, amelynek neve megegyezik egy meglévő kulcsot, ha az eredeti kulcs frissül az új kulcs megadott értékeket. Elérheti az előző verzió verzióspecifikus URI-ját a kulcs használatával. Példa:

* Használat "https:\//vault10.vault.local.azurestack.external:443/keys/key01", mindig letöltheti a legfrissebb verziót.
* Használat "https:\//vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a" lekérni ezt a verziót.

### <a name="get-a-key"></a>Kulcs lekérése

Használja a **Get-AzureKeyVaultKey** parancs egy kulcsot és az adatok olvasásához.

```powershell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>Titkos kulcs létrehozása

Használja a **Set-AzureKeyVaultSecret** parancsot létrehozni vagy frissíteni egy tároló titkos kulcs. Titkos kulcs jön létre, ha egy már nem létezik. A titkos kulcs új verziója jön létre, ha már létezik.

```powershell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

**Kimenet**

![Titkos kulcs létrehozása](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Titkos kulcs lekérése

Használja a **Get-AzureKeyVaultSecret** parancsot a kulcstartóban található titkos olvasásához. Ezzel a paranccsal lépjen vissza az összes vagy titkos kulcs bizonyos verziójának.

```powershell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

Miután létrehozta a kulcsok és titkos kulcsok, engedélyezheti a külső alkalmazások általi használatát.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Az alkalmazások számára egy kulcs vagy titkos kód használata

Használja a **Set-AzureRmKeyVaultAccessPolicy** parancsot az alkalmazások számára a hozzáférési kulcs vagy titkos kulcsot a kulcstartóban.
A következő példában a tároló nevére van *ContosoKeyVault* , és a regisztrálni kívánt alkalmazás Ügyfélazonosítója *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*. Engedélyezze az alkalmazás, futtassa a következő parancsot. Másik lehetőségként megadhatja a **PermissionsToKeys** paraméter segítségével állítsa be az engedélyeket a felhasználó, alkalmazás vagy egy biztonsági csoportot.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Ha azt szeretné, hogy a tároló titkos kulcsainak olvasásához az alkalmazás engedélyezésére, futtassa a következő parancsmagot:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>További lépések

* [Virtuális gép üzembe helyezése a Key Vault-jelszóval](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Virtuális gép üzembe helyezése a Key Vault-tanúsítvánnyal](azure-stack-key-vault-push-secret-into-vm.md)
