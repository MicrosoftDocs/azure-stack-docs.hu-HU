---
title: Biztonságos tárolt tanúsítvánnyal rendelkező virtuális gép üzembe helyezése Azure Stackon | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe egy virtuális gépet, és hogyan küldhet el egy tanúsítványt a Azure Stack található kulcstartó használatával
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 8741d63dbbcefde950fc10c0917d87bc4e9718f7
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961525"
---
# <a name="deploy-a-vm-with-a-securely-stored-certificate-on-azure-stack"></a>Biztonságos tárolt tanúsítvánnyal rendelkező virtuális gép üzembe helyezése Azure Stack 

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy Azure Stack virtuális gépet (VM) egy telepített Key Vault-tanúsítvánnyal.

## <a name="overview"></a>Áttekintés

A tanúsítványokat számos esetben használják, például a hitelesítés Active Directory vagy a webes forgalom titkosítása. A tanúsítványokat a Azure Stack Key vaultban titkos kulcsként is tárolhatja. A Azure Stack Key Vault használatának előnyei a következők:

* A tanúsítványok nem jelennek meg parancsfájlokban, parancssori előzményekben vagy sablonban.
* A Tanúsítványkezelő folyamata egyszerűsítve van.
* A tanúsítványokhoz hozzáférő kulcsok ellenőrzése megtörtént.

## <a name="process-description"></a>Folyamat leírása

A következő lépések azt írják le, hogy milyen folyamat szükséges a tanúsítvány virtuális géphez való leküldéséhez:

1. Hozzon létre egy Key Vault-titkot.
2. Frissítse az **azuredeploy. Parameters. JSON** fájlt.
3. A sablon üzembe helyezése.

> [!NOTE]
> Ezeket a lépéseket a Azure Stack Development Kit (ASDK) vagy egy külső ügyfélről is használhatja, ha VPN-kapcsolaton keresztül csatlakozik.

## <a name="prerequisites"></a>Előfeltételek

* Elő kell fizetnie egy olyan ajánlatra, amely tartalmazza a Key Vault szolgáltatást.
* [Telepítse a powershellt Azure Stackhoz](../operator/azure-stack-powershell-install.md).
* [Konfigurálja a Azure stack felhasználó PowerShell-környezetét](azure-stack-powershell-configure-user.md).

## <a name="create-a-key-vault-secret"></a>Key Vault-titok létrehozása

A következő szkript egy. pfx formátumú tanúsítványt hoz létre, létrehoz egy kulcstartót, és a Key vaultban tárolja a tanúsítványt titkos kulcsként.

> [!IMPORTANT]
> A Key Vault létrehozásakor a `-EnabledForDeployment` paramétert kell használnia. Ez a paraméter biztosítja, hogy a kulcstároló Azure Resource Manager-sablonokból is hivatkozhat.

```powershell
# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<certificate thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path to where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path to where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret
```

A parancsfájl futtatásakor a kimenet tartalmazza a titkos URI-t. Jegyezze fel ezt az URI-t, mert a [leküldéses tanúsítványban a Windows Resource Manager-sablonra](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate)kell hivatkoznia. Töltse le a [VM-push-Certificate-Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) template mappát a fejlesztői számítógépére. Ez a mappa tartalmazza a **azuredeploy. JSON** és a **azuredeploy. Parameters. JSON** fájlt, amelyet a következő lépésekben kell megadnia.

Módosítsa a **azuredeploy. Parameters. JSON** fájlt a környezeti értékek alapján. A fontos paraméterek a tár neve, a tár erőforráscsoport és a titkos URI (az előző szkript által generált). A következő szakasz egy példát mutat be egy paraméter fájlra.

## <a name="update-the-azuredeployparametersjson-file"></a>A azuredeploy. Parameters. JSON fájl frissítése

Frissítse az **azuredeploy. Parameters. JSON** fájlt a `vaultName`, a titkos URI, a `VmName`és egyéb paraméterekkel a környezetében. A következő JSON-fájl egy példát mutat be a sablon paramétereinek fájljára:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablon üzembe helyezéséhez használja a következő PowerShell-parancsfájlt:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret to it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

A sablon sikeres központi telepítésekor a következő kimenet jelenik meg:

![Template deployment eredmények](media/azure-stack-key-vault-push-secret-into-vm/deployment-output.png)

Azure Stack leküldi a tanúsítványt a virtuális gépre az üzembe helyezés során. A tanúsítvány helye a virtuális gép operációs rendszertől függ:

* A Windows rendszerben a tanúsítvány a **LocalMachine** -tanúsítvány helyére kerül, a felhasználó által megadott tanúsítványtárolóval együtt.
* A Linux rendszerben a tanúsítvány a **/var/lib/waagent** könyvtár alá kerül, és a **UppercaseThumbprint. CRT** fájlnevet adja meg a X509-tanúsítványfájl és a **UppercaseThumbprint. prv** fájl számára a titkos kulcshoz.

## <a name="retire-certificates"></a>Tanúsítványok kivonása

A tanúsítványok kivonása a Tanúsítványkezelő folyamat része. A tanúsítvány régebbi verzióját nem lehet törölni, de a `Set-AzureKeyVaultSecretAttribute` parancsmag használatával letilthatja.

Az alábbi példa bemutatja, hogyan tilthatja le a tanúsítványokat. Használja a `VaultName`, `Name`és `Version` paraméterek saját értékeit.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>További lépések

* [Virtuális gép üzembe helyezése Key Vault-jelszóval](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Key Vault elérésének engedélyezése az alkalmazás számára](azure-stack-key-vault-sample-app.md)
