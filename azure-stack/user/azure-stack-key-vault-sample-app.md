---
title: Azure Stack Key Vault titkok elérésének engedélyezése az alkalmazások számára | Microsoft Docs
description: Megtudhatja, hogyan futtathat egy olyan minta alkalmazást, amely a kulcsokat és titkos kulcsokat egy Azure Stack található kulcstartóból kéri le.
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
ms.date: 07/17/2019
ms.author: sethm
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: fa5a602fbdca32aed635f587fef248bcff0dae06
ms.sourcegitcommit: 2063332b4d7f98ee944dd1f443847eea70eb5614
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68303164"
---
# <a name="allow-apps-to-access-azure-stack-key-vault-secrets"></a>Azure Stack Key Vault titkok elérésének engedélyezése az alkalmazások számára

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A cikk lépéseit követve futtassa a minta alkalmazás **HelloKeyVault** , amely lekérdezi a kulcsokat és a titkos kulcsokat egy Azure stack található kulcstartóból.

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételeket telepítheti a [Azure stack Development Kit](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)vagy egy Windows-alapú külső ügyfélről, ha [VPN-kapcsolaton keresztül csatlakozik](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn):

* Telepítés [Azure Stack-kompatibilis Azure PowerShell-modulok](../operator/azure-stack-powershell-install.md).
* Töltse le a [az Azure Stack működéséhez szükséges eszközök](../operator/azure-stack-powershell-download.md).

## <a name="create-a-key-vault-and-register-an-app"></a>Key Vault létrehozása és alkalmazás regisztrálása

Felkészülés a minta alkalmazásra:

* Hozzon létre egy Key vaultot a Azure Stackban.
* Alkalmazás regisztrálása a Azure Active Directoryban (Azure AD).

Készítse elő a minta alkalmazást a Azure Portal vagy a PowerShell használatával.

> [!NOTE]
> Alapértelmezés szerint a PowerShell-parancsfájl új alkalmazást hoz létre Active Directoryban. Azonban regisztrálhat egy meglévő alkalmazást is.

A következő parancsfájl futtatása előtt győződjön meg arról, hogy megadja a és `aadTenantName` `applicationPassword` a változók értékeit. Ha nem ad meg értéket a `applicationPassword`értékhez, ez a szkript véletlenszerűen generált jelszót hoz létre.

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

Az alábbi képen a kulcstároló létrehozásához használt parancsfájl kimenete látható:

![Key Vault hozzáférési kulcsokkal](media/azure-stack-key-vault-sample-app/settingsoutput.png)

Jegyezze fel az előző szkript által visszaadott **VaultUrl**, **AuthClientId**és **AuthClientSecret** értékeket. Ezeket az értékeket használhatja a **HelloKeyVault** alkalmazás futtatásához.

## <a name="download-and-configure-the-sample-application"></a>A minta alkalmazás letöltése és konfigurálása

Töltse le a Key Vault-mintát az Azure [Key Vault Client Samples](https://www.microsoft.com/download/details.aspx?id=45343) oldaláról. Bontsa ki a. zip-fájl tartalmát a fejlesztői munkaállomáson. A Samples mappában két alkalmazás található. Ez a cikk a **HelloKeyVault**-t használja.

A **HelloKeyVault** minta betöltése:

1. Tallózással keresse meg a **Microsoft. Azure.**  > kulcstartót. a Samples > **HelloKeyVault** mappája.
2. Nyissa meg a **HelloKeyVault** alkalmazást a Visual Studióban.

### <a name="configure-the-sample-application"></a>A minta alkalmazás konfigurálása

A Visual Studióban:

1. Nyissa meg a HelloKeyVault\App.config fájlt, `<appSettings>` és keresse meg az elemet.
2. Frissítse a **VaultUrl**, a **AuthClientId**és a **AuthClientSecret** kulcsokat a kulcstartó létrehozásakor visszaadott értékekkel. Alapértelmezés szerint az app. config fájl helyőrzőt tartalmaz a következőhöz `AuthCertThumbprint`:. A helyőrzőt cserélje `AuthClientSecret`le a következőre:.

   ![Alkalmazásbeállítások](media/azure-stack-key-vault-sample-app/appconfig.png)

3. Hozza létre újra a megoldást.

## <a name="run-the-app"></a>Az alkalmazás futtatása

A **HelloKeyVault**futtatásakor az alkalmazás bejelentkezik az Azure ad-be, majd a `AuthClientSecret` token használatával hitelesíti a Azure Stackban található kulcstartót.

A **HelloKeyVault** minta a következőre használható:

* Olyan alapszintű műveleteket hajthat végre, mint például a kulcsok és a titkos kódok létrehozása, titkosítása, becsomagolása és törlése.
* Adja át a paramétereket `encrypt` , `decrypt` például a és a **HelloKeyVault**, és alkalmazza a megadott módosításokat egy kulcstartóra.

## <a name="next-steps"></a>További lépések

* [Virtuális gép üzembe helyezése Key Vault-jelszóval](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Key Vault tanúsítvánnyal rendelkező virtuális gép üzembe helyezése](azure-stack-key-vault-push-secret-into-vm.md)
