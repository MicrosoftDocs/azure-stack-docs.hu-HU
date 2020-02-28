---
title: Sablon központi telepítése Azure Stack hub parancssorával
description: Megtudhatja, hogyan helyezhet üzembe sablonokat Azure Stack hubhoz az Azure platformfüggetlen parancssori felületének (CLI) használatával.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: fc01d7ebab8855580470a7d4e29ab86851b88280
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704284"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack-hub"></a>Sablon központi telepítése Azure Stack hub parancssorával

Az Azure parancssori felületének (CLI) használatával Azure Resource Manager sablonokat telepíthet Azure Stack hub-ban. Azure Resource Manager sablonokat egyetlen, koordinált műveletben telepítheti és állíthatja be az alkalmazás erőforrásait.

## <a name="deploy-template"></a>Sablon üzembe helyezése

1. Keresse meg a [AzureStack-Gyorsindítás-sablonok](https://aka.ms/AzureStackGitHub) tárházat, és keresse meg a **101-Create-Storage-Account** sablont. Mentse a sablon (`azuredeploy.json`) és a paraméter fájljait `(azuredeploy.parameters.json`) a helyi meghajtón található helyre, például `C:\templates\`
2. Navigáljon ahhoz a mappához, amelybe letöltötte a fájlokat. 
3. [Telepítse és kapcsolódjon](azure-stack-version-profiles-azurecli2.md) Azure stack hubhoz az Azure CLI-vel.
4. Frissítse a régiót és a helyet a következő parancsban. Ha a ASDK használja, használja a Location paraméterhez `local`. A sablon üzembe helyezése:
    ```azurecli
    az group create --name testDeploy --location local
    az group deployment create --resource-group testDeploy --template-file ./azuredeploy.json --parameters ./azuredeploy.parameters.json
    ```

Ez a parancs központilag telepíti a sablont az Azure Stack hub-példány **testDeploy** .

## <a name="validate-template-deployment"></a>Sablon központi telepítésének ellenőrzése

Az erőforráscsoport és a Storage-fiók áttekintéséhez futtassa az alábbi CLI-parancsokat:

```azurecli
az group list
az storage account list
```

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [helyezhet üzembe sablonokat a PowerShell használatával](azure-stack-deploy-template-powershell.md).
