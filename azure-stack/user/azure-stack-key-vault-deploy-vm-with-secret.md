---
title: Azure Stack hub virtuális gép üzembe helyezése Key Vault tárolt jelszó használatával
description: Megtudhatja, hogyan helyezhet üzembe egy virtuális gépet egy Azure Stack hub Key vaultban tárolt jelszó használatával.
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: e94ef7ef30b95319ef8974cbce41692e53cf641d
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883652"
---
# <a name="deploy-an-azure-stack-hub-vm-using-a-password-stored-in-key-vault"></a>Azure Stack hub virtuális gép üzembe helyezése Key Vault tárolt jelszó használatával

Ez a cikk a Windows Server rendszerű virtuális gépek (VM) központi telepítésének lépéseit ismerteti Azure Stack hub Key Vaultban tárolt jelszó használatával. A Key Vault-jelszó használata biztonságosabb, mint az egyszerű szöveges jelszó átadása.

## <a name="overview"></a>Áttekintés

A Azure Stack hub Key vaultban az értékeket, például a jelszót titkos kulcsként is tárolhatja. Miután létrehozta a titkos kulcsot, hivatkozhat rá Azure Resource Manager-sablonokban. A Titkok a Resource Managerrel való használata a következő előnyöket biztosítja:

* Nem kell manuálisan megadnia a titkot minden egyes erőforrás telepítésekor.
* Megadhatja, hogy mely felhasználók vagy egyszerű szolgáltatások férhetnek hozzá a titkos kulcshoz.

## <a name="prerequisites"></a>Előfeltételek

* Elő kell fizetnie egy olyan ajánlatra, amely tartalmazza a Key Vault szolgáltatást.
* [Telepítse a PowerShellt Azure Stack hubhoz.](../operator/azure-stack-powershell-install.md)
* [Konfigurálja a PowerShell-környezetet.](azure-stack-powershell-configure-user.md)

A következő lépések ismertetik a virtuális gép létrehozásához szükséges folyamatot a Key Vault tárolt jelszó beolvasásával:

1. Hozzon létre egy Key Vault titkot.
2. Frissítse a `azuredeploy.parameters.json` fájlt.
3. A sablon üzembe helyezése.

> [!NOTE]  
> Ezeket a lépéseket a Azure Stack Development Kit (ASDK) vagy egy külső ügyfélről is használhatja, ha VPN-kapcsolaton keresztül csatlakozik.

## <a name="create-a-key-vault-secret"></a>Key Vault titkos kód létrehozása

A következő szkript egy kulcstartót hoz létre, és a Key vaultban tárolja a jelszót. A Key Vault létrehozásakor használja a `-EnabledForDeployment` paramétert. Ez a paraméter gondoskodik arról, hogy a kulcstároló Azure Resource Manager-sablonokból is hivatkozhat.

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

Az előző parancsfájl futtatásakor a kimenet tartalmazza a titkos URI-t (Uniform Resource Identifier). Jegyezze fel ezt az URI-t. Hivatkoznia kell rá a [Windows rendszerű virtuális gép üzembe helyezése jelszóval a Key Vault-](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) sablonban. Töltse le a [101-VM-Secure-Password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) mappát a fejlesztői számítógépére. Ez a mappa tartalmazza a `azuredeploy.json` és `azuredeploy.parameters.json` fájlokat, amelyekre a következő lépésekben szüksége lesz.

Módosítsa a `azuredeploy.parameters.json` fájlt a környezeti értékek alapján. A különleges érdekű paraméterek a tár neve, a tár erőforráscsoport és a titkos URI (az előző szkript által generált). Az alábbi fájl egy példa egy paraméter fájlra.

## <a name="update-the-azuredeployparametersjson-file"></a>A azuredeploy. Parameters. JSON fájl frissítése

Frissítse a `azuredeploy.parameters.json` fájlt a kulcstároló URI-ja, a secretName, a virtuálisgép-értékek adminUsername alapján a környezetében. A következő JSON-fájl egy példát mutat be a sablon paramétereinek fájljára:

```json
{
    "$schema":  "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
       "adminPassword":  {
         "reference":  {
            "keyVault":  {
              "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
              },
            "secretName":  "MySecret"
         }
       },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Sablonalapú telepítés

Most telepítse a sablont a következő PowerShell-parancsfájl használatával:

```powershell  
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

A sablon sikeres központi telepítésekor a következő kimenetet eredményezi:

![Központi telepítés kimenete](media/azure-stack-key-vault-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>Következő lépések

* [Minta alkalmazás üzembe helyezése Key Vault](azure-stack-key-vault-sample-app.md)
* [Key Vault tanúsítvánnyal rendelkező virtuális gép üzembe helyezése](azure-stack-key-vault-push-secret-into-vm.md)
