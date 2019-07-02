---
title: Virtuális gép létrehozása az Azure Stacken biztonságosan tárolt tanúsítvány |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy virtuális gépet, és küldje le azt a tanúsítványt a key vault használatával az Azure Stackben
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
ms.date: 06/11/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 9403931d91756e744dcdb6c34adb26e8281f6d28
ms.sourcegitcommit: eccbd0098ef652919f357ef6dba62b68abde1090
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492383"
---
# <a name="deploy-a-vm-with-a-securely-stored-certificate-on-azure-stack"></a>Virtuális gép létrehozása az Azure Stacken biztonságosan tárolt tanúsítvány 

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ez a cikk bemutatja, hogyan helyezhet üzembe egy Azure Stack virtuális gépet (VM) telepítve van a key vault-tanúsítvánnyal.

## <a name="overview"></a>Áttekintés

A tanúsítványokat helyzetekben, például az Active Directory hitelesítő vagy a webes forgalom titkosításához használja. Tanúsítványok biztonságosan tárolhatja az Azure Stack a key vault titkos kódként. Az Azure Stack Key Vault használatának előnyei a következők:

* Egy parancsfájl, a parancssori előzményekben vagy a sablon a tanúsítványok nem érhetőek el.
* A tanúsítványkezelési folyamatot zökkenőmentesen történik.
* A tanúsítványok hozzáférő kulcsok hozzáférése van.

## <a name="process-description"></a>Folyamat – leírás

A folyamat leküldéses tanúsítványt a virtuális géphez szükséges a következő lépésekből áll:

1. A key vault titkos kulcs létrehozása.
2. Frissítés a **azuredeploy.parameters.json** fájlt.
3. A sablon üzembe helyezéséhez.

> [!NOTE]
> Ezeket a lépéseket, vagy az Azure Stack Development Kit (ASDK) a külső ügyfélről is használhatja, ha a VPN-kapcsolaton keresztül kapcsolódik.

## <a name="prerequisites"></a>Előfeltételek

* Az ajánlat, amely tartalmazza a Key Vault szolgáltatás elő kell fizetnie.
* [Az Azure Stack PowerShell telepítése](../operator/azure-stack-powershell-install.md).
* [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md).

## <a name="create-a-key-vault-secret"></a>A key vault titkos kulcs létrehozása

A következő szkriptet a .pfx formátumú tanúsítványt hoz létre egy kulcstartót hoz létre és tárolja a tanúsítványt a key vault titkos kulcs.

> [!IMPORTANT]
> Kell használnia a `-EnabledForDeployment` paraméter a key vault létrehozása során. Ez a paraméter gondoskodik arról, hogy a key vault az Azure Resource Manager-sablonok lehet hivatkozni.

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

Ez a szkript futtatásakor a kimenet tartalmazza a titkos URI. Jegyezze fel ezt az URI, hivatkoznia kell a a [Push-tanúsítvány a Windows Resource Manager-sablon](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Töltse le a [vm-push-tanúsítvány-windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) Szolgáltatássablon mappáját, a fejlesztői számítógépen. Ebben a mappában találhatók a **azuredeploy.json** és **azuredeploy.parameters.json** fájlokat, amelyek a következő lépésekben van szüksége.

Módosítsa a **azuredeploy.parameters.json** fájl a környezet értékeknek megfelelően. Fontos paraméterei a következők: a tároló nevére, a tár erőforráscsoportja és a titkos kulcs URI-t (mivel az előző parancsfájl által létrehozott). A következő szakasz egy alkalmazásparaméter-fájlt egy példát mutat be.

## <a name="update-the-azuredeployparametersjson-file"></a>Frissítés a azuredeploy.parameters.json fájlhoz

Frissítés a **azuredeploy.parameters.json** -fájlt a `vaultName`, titkos URI `VmName`, és más paramétereket, a környezet alapján. A következő JSON-fájlt a sablon paraméterfájljának egy példát mutat be:

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

A sablon üzembe helyezése a következő PowerShell-parancsfájl használatával:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Ha a sablon üzembe helyezése sikeresen befejeződött, a következő kimenetet eredményezi:

![Sablon üzembe helyezés eredményei](media/azure-stack-key-vault-push-secret-into-vm/deployment-output.png)

Az Azure Stack leküldéses tanúsítvány a virtuális gép üzembe helyezése során. A tanúsítvány helye a virtuális gép operációs rendszerének függ:

* A Windows, hogy hozzáadta a tanúsítványt a **LocalMachine** tanúsítványt a tanúsítványtárolóban, amely a felhasználó által megadott helyen.
* A Linux, a tanúsítvány alá kerül a **/var/lib/waagent** könyvtárban, a Fájlnév **UppercaseThumbprint.crt** a X509 a tanúsítványfájl és **UppercaseThumbprint.prv**  a titkos kulcs.

## <a name="retire-certificates"></a>Tanúsítványok visszavonása

Tanúsítványok eltávolítása a felügyeleti folyamat részét képezi. Nem lehet törölni a tanúsítványt a régebbi verzióját, de a segítségével letilthatja a `Set-AzureKeyVaultSecretAttribute` parancsmagot.

Az alábbi példa bemutatja, hogyan tilthatja le a tanúsítványt. A saját értékeit használja a `VaultName`, `Name`, és `Version` paramétereket.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>További lépések

* [Virtuális gép üzembe helyezése Key Vault-jelszóval](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Key Vault elérése érdekében alkalmazás engedélyezése](azure-stack-key-vault-sample-app.md)
