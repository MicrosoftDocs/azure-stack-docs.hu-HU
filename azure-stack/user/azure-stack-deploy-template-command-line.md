---
title: Sablon üzembe helyezése a parancssorból a Azure Stackban | Microsoft Docs
description: Az Azure platformfüggetlen parancssori felületének (CLI) használata a sablonok Azure Stack való üzembe helyezéséhez.
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
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: da17e80c802e210d53effbad8f264b1a4019e6e0
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991850"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack"></a>Sablon központi telepítése a parancssorban Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Az Azure parancssori felületének (CLI) használatával Azure Resource Manager sablonokat helyezhet üzembe Azure Stackokban. Azure Resource Manager-sablonok egyetlen, koordinált művelettel telepíthetik és kioszthatják az alkalmazáshoz tartozó erőforrásokat.

## <a name="before-you-begin"></a>Előkészületek

- [Telepítse és kapcsolódjon](azure-stack-version-profiles-azurecli2.md) Azure stack az Azure CLI-vel.
- Töltse le a *azuredeploy. JSON* és a *azuredeploy. Parameters. JSON* fájlt a [Storage-fiók létrehozása példa sablonból](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Sablon üzembe helyezése

Navigáljon ahhoz a mappához, amelybe ezeket a fájlokat letöltötte, és futtassa a következő parancsot a sablon üzembe helyezéséhez:

```azurecli
az group create "cliRG" "local" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json
```

Ez a parancs központilag telepíti a sablont a Azure Stack POC alapértelmezett helyén található erőforráscsoport- **cliRG** .

## <a name="validate-template-deployment"></a>Sablon központi telepítésének ellenőrzése

Az erőforráscsoport és a Storage-fiók megtekintéséhez használja az alábbi CLI-parancsokat:

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [helyezhet üzembe sablonokat a PowerShell használatával](azure-stack-deploy-template-powershell.md).
