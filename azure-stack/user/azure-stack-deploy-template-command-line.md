---
title: A parancssorból az Azure Stack-sablon üzembe helyezése |} A Microsoft Docs
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
ms.openlocfilehash: 92c9189f8144804f36e551ab89d8b4fc4c1f8598
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691367"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack"></a>A parancssorból az Azure Stack-sablon üzembe helyezése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure parancssori felület (CLI) használatával az Azure Resource Manager-sablonok az Azure Stack üzembe helyezése. Az Azure Resource Manager-sablonok üzembe helyezése, és egyetlen, koordinált műveletben lévő alkalmazás-erőforrások kiépítése.

## <a name="before-you-begin"></a>Előkészületek

- [Telepítése és csatlakozás](azure-stack-version-profiles-azurecli2.md) az Azure Stackhez az Azure CLI használatával.
- Töltse le a fájlokat *azuredeploy.json* és *azuredeploy.parameters.json* származó a [hozzon létre a fiók példa tárolósablonját](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Sablon üzembe helyezése

Keresse meg a mappát, amelybe ezeket a fájlokat le és futtassa a következő parancsot a sablon üzembe helyezéséhez:

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
