---
title: Sablonok a parancssor az Azure Stack üzembe helyezése |} A Microsoft Docs
description: Útmutató a sablonok az Azure Stack üzembe helyezése a többplatformos parancssori felület (CLI) használatával.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 05/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: d225e113a19dc62ce66df49d89273d00e2e35683
ms.sourcegitcommit: 2b6a0b3b4dc63c26df3d0535d630d640ff232fb0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521116"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Az Azure Stack a parancssor használatával sablonok üzembe helyezése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure parancssori felület (CLI) használatával az Azure Resource Manager-sablonok az Azure Stack üzembe helyezése. Az Azure Resource Manager-sablonok üzembe helyezése, és egyetlen, koordinált műveletben lévő alkalmazás-erőforrások kiépítése.

## <a name="before-you-begin"></a>Előkészületek

- [Telepítése és csatlakozás](azure-stack-version-profiles-azurecli2.md) az Azure Stackhez az Azure CLI használatával.
- Töltse le a fájlokat *azuredeploy.json* és *azuredeploy.parameters.json* származó a [hozzon létre a fiók példa tárolósablonját](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Sablon üzembe helyezése

Lépjen abba a mappába, amelybe a rendszer letölti a fájlokat, és futtassa a következő parancsot a sablon üzembe helyezéséhez:

```azurecli
az group create "cliRG" "local" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json
```

Ez a parancs üzembe helyezi a sablont az erőforráscsoport **cliRG** az Azure Stack POC alapértelmezett helyen.

## <a name="validate-template-deployment"></a>A sablon telepítésének ellenőrzése

Az erőforrás és a tárfiókja fiók megtekintéséhez használja a következő CLI-parancsokat:

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>További lépések

Tudjon meg többet [sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md).
