---
title: Sablonok a parancssor az Azure Stack üzembe helyezése |} A Microsoft Docs
description: Útmutató a sablonok az Azure Stack üzembe helyezése a többplatformos parancssori felület (CLI) használatával.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: fcf84ee394c917e896bc50d5d6a97f42191451e9
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985596"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Az Azure Stack a parancssor használatával sablonok üzembe helyezése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A parancssor használatával üzembe helyezése Azure Resource Manager-sablonok az Azure Stack Development Kit környezetben. Az Azure Resource Manager-sablonok üzembe helyezése, és az alkalmazás egyetlen, koordinált műveletben szereplő összes erőforrás kiépítéséhez.

## <a name="before-you-begin"></a>Előzetes teendők

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

- Sablonok telepítésével kapcsolatos további tudnivalókért lásd:

[Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
