---
title: Virtuális gép létrehozása az Azure Stacken biztonságosan tárolt jelszó |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe egy virtuális Gépet az Azure Stack Key Vaultban tárolt jelszó használatával
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: ef82c50a8150f10fdbe2b38acf77b5539c575b51
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782798"
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Az Azure Stack Key Vaultban tárolt biztonságos jelszó használata virtuális gép létrehozása

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ez a cikk végigvezeti az Azure Stack Key Vaultban tárolt jelszó használatával Windows Server virtuális gép üzembe helyezése. A key vault a jelszó használata biztonságosabb, mint egy egyszerű szöveges jelszó átadásával.

## <a name="overview"></a>Áttekintés

Például a jelszó értékeit egy Azure Stack key vault-titokként is tárolhatja. Miután létrehozott egy titkos kulcsot, az Azure Resource Manager-sablonokban hivatkozhasson rá. Titkos kódok és a Resource Manager használatával a következő előnyökkel jár:

* Nem kell manuálisan adja meg titkos kulcsot minden alkalommal, amikor telepít egy erőforrást.
* Megadhatja, hogy mely felhasználók vagy egyszerű szolgáltatások hozzáférhet a titkos kulcs.

## <a name="prerequisites"></a>Előfeltételek

* Az ajánlat, amely tartalmazza a Key Vault szolgáltatás elő kell fizetnie.
* [Az Azure Stack PowerShell telepítése.](../operator/azure-stack-powershell-install.md)
* [A PowerShell-környezet konfigurálása.](azure-stack-powershell-configure-user.md)

A virtuális gép létrehozásához a Key Vaultban tárolt jelszó lekérésével szükséges folyamat a következő lépésekből áll:

1. Hozzon létre egy Key Vault titkos.
2. Frissítse az azuredeploy.parameters.json fájlra.
3. A sablon üzembe helyezéséhez.

> [!NOTE]  
> Ezeket a lépéseket az Azure Stack fejlesztői készletet, vagy egy külső ügyfél is használhatja, ha VPN-kapcsolaton keresztül kapcsolódik.

## <a name="create-a-key-vault-secret"></a>A Key Vault titkos kód létrehozása

A következő parancsfájl egy kulcstartót hoz létre, és tárolja a jelszót, egy titkos kulcsot a key vaultban. Használja a `-EnabledForDeployment` paraméter a kulcstartó létrehozásakor. Ez a paraméter gondoskodik arról, hogy a key vault az Azure Resource Manager-sablonok lehet hivatkozni.

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

Amikor az előző parancsfájlt futtat, akkor a kimenete a titkos URI tartalmazza. Jegyezze fel ezt az URI. Arra mutató hivatkozás a rendelkezik a [felhasználónévvel és jelszóval a key vault-sablon üzembe helyezése Windows virtuális gép](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Töltse le a [101-es virtuális gép – biztonságos – jelszó-](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) mappát a fejlesztői számítógépre. Ebben a mappában találhatók a `azuredeploy.json` és `azuredeploy.parameters.json` fájlokat, amelyek a következő lépésben szüksége lesz.

Módosítsa a `azuredeploy.parameters.json` fájl a környezet értékeknek megfelelően. Érdeklik paraméterei a következők: a tároló nevére, a tár erőforráscsoportja és a titkos kulcs URI-t (mivel az előző parancsfájl által létrehozott). A következő fájl egy példát:

## <a name="update-the-azuredeployparametersjson-file"></a>Frissítés a azuredeploy.parameters.json fájlhoz

A KeyVault URI secretName, a virtuális gép értékek alapján a környezet adminUsername frissítse az azuredeploy.parameters.json fájlra. A következő JSON-fájlt a sablon paraméterfájljának egy példát mutat be:

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

## <a name="template-deployment"></a>Sablon telepítése

Most helyezze üzembe a sablont a következő PowerShell-parancsfájl használatával:

```powershell  
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Ha a sablon üzembe helyezése sikeresen befejeződött, a következő kimenetet eredményezi:

![Üzembe helyezés kimenet](media/azure-stack-key-vault-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>További lépések

* [A Key Vault-mintaalkalmazás üzembe helyezése](azure-stack-key-vault-sample-app.md)
* [Virtuális gép létrehozása Key Vault-tanúsítvánnyal](azure-stack-key-vault-push-secret-into-vm.md)
