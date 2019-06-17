---
title: Engedélyezi, hogy az alkalmazások eléréséhez az Azure Stack Key Vault titkos kódok |} A Microsoft Docs
description: Ismerje meg, hogyan futtathat egy mintaalkalmazást, amely lekéri a kulcsok és titkos kulcsok a key vault az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sethm
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: 6cc9475ee04bcdcb7b4c35f4ca5a39efc7c36aa8
ms.sourcegitcommit: ad2f2cb4dc8d5cf0c2c37517d5125921cff44cdd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67138896"
---
# <a name="allow-apps-to-access-azure-stack-key-vault-secrets"></a>Azure Stack Key Vault titkos kulcsok el az alkalmazások engedélyezése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Kövesse a mintaalkalmazás futtatása ebből a cikkből **HelloKeyVault** , amely lekéri a kulcsok és titkos kulcs-tároló az Azure Stackben.

## <a name="prerequisites"></a>Előfeltételek

Telepítheti a következő előfeltételek a [Azure Stack Development Kit](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp), vagy egy Windows-alapú külső ügyfél Ha [VPN-kapcsolaton keresztül csatlakozó](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn):

* Telepítés [Azure Stack-kompatibilis Azure PowerShell-modulok](../operator/azure-stack-powershell-install.md).
* Töltse le a [az Azure Stack működéséhez szükséges eszközök](../operator/azure-stack-powershell-download.md).

## <a name="create-a-key-vault-and-register-an-app"></a>Hozzon létre egy kulcstartót, és a egy alkalmazás regisztrálása

A mintaalkalmazás előkészítése:

* Kulcstartó létrehozása az Azure Stackben.
* Alkalmazás regisztrálása az Azure Active Directory (Azure AD).

Az Azure portal vagy a PowerShell használatával a mintaalkalmazás előkészítése.

> [!NOTE]
> Alapértelmezés szerint a PowerShell-parancsfájlt az Active Directoryban egy új alkalmazást hoz létre. Azonban regisztrálhat egyet a meglévő alkalmazásokat.

A következő szkript futtatása előtt győződjön meg arról, az értékeket ad meg a `aadTenantName` és `applicationPassword` változókat. Ha nem ad meg értéket `applicationPassword`, ez a szkript létrehoz egy véletlenszerű jelszó.

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script generates a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator's PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "https://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host
```

Az alábbi képen látható a kulcstároló létrehozásához használt a szkript kimenetében:

![Kulcstartó-hozzáférési kulcsok](media/azure-stack-key-vault-sample-app/settingsoutput.png)

Jegyezze fel a **VaultUrl**, **AuthClientId**, és **AuthClientSecret** az előző parancsfájl által visszaadott értékek. Ezeket az értékeket használja a futtatásához a **HelloKeyVault** alkalmazás.

## <a name="download-and-configure-the-sample-application"></a>Töltse le és a mintaalkalmazás konfigurálása

A key vault-minta letöltése az Azure [Key Vault-ügyfélnek minták](https://www.microsoft.com/download/details.aspx?id=45343) lapot. Bontsa ki a .zip fájlt a fejlesztő munkaállomás tartalmát. Nincsenek két alkalmazás a mintákat tartalmazó mappára. Ez a cikk **HelloKeyVault**.

Betölteni a **HelloKeyVault** minta:

1. Keresse meg a **Microsoft.Azure.KeyVault.Samples** > **minták** > **HelloKeyVault** mappát.
2. Nyissa meg a **HelloKeyVault** alkalmazáshoz a Visual Studióban.

### <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

A Visual Studióban:

1. Nyissa meg a HelloKeyVault\App.config fájlt, és keresse meg a `<appSettings>` elemet.
2. Frissítés a **VaultUrl**, **AuthClientId**, és **AuthClientSecret** kulcsok az értékekkel adja vissza, ha a kulcstároló létrehozásához. Alapértelmezés szerint az App.config fájl rendelkezik egy helyőrző `AuthCertThumbprint`. Cserélje le a helyőrző a `AuthClientSecret`.

   ![Alkalmazásbeállítások](media/azure-stack-key-vault-sample-app/appconfig.png)

3. Építse újra a megoldást.

## <a name="run-the-app"></a>Az alkalmazás futtatása

Futtatásakor **HelloKeyVault**, az alkalmazás az Azure ad Szolgáltatásba bejelentkezik, majd a `AuthClientSecret` jogkivonat a key vault az Azure Stackben történő hitelesítéséhez.

Használhatja a **HelloKeyVault** a mintát:

* Hajtsa végre alapszintű műveleteket, például létrehozhat, titkosítása, wrap, és törölje a kulcsok és titkos kulcsok.
* Például adja át a paramétereket `encrypt` és `decrypt` való **HelloKeyVault**, és alkalmazza a megadott módosításokat, egy kulcstárolóba.

## <a name="next-steps"></a>További lépések

* [Virtuális gép üzembe helyezése Key Vault-jelszóval](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Virtuális gép létrehozása Key Vault-tanúsítvánnyal](azure-stack-key-vault-push-secret-into-vm.md)
