---
title: Alkalmazások fejlesztése Azure Stack hub-hoz | Microsoft Docs
description: Fejlesztési szempontok a Azure Stack hub-beli alkalmazások Azure-szolgáltatásokkal történő prototípusának fejlesztéséhez.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: ba5aefa61db489f5f7063ebc4785785ba2f26f4c
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883728"
---
# <a name="develop-for-azure-stack-hub"></a>Fejlesztés Azure Stack hub-hoz

Megkezdheti az alkalmazások fejlesztését még akkor is, ha nem fér hozzá egy Azure Stack hub-környezethez. Az Azure Stack hub biztosítja az adatközpontban futó Microsoft Azure-szolgáltatásokat, ami azt jelenti, hogy ugyanazokat az Azure-eszközöket és-folyamatokat használja az Azure Stack hub fejlesztéséhez.

## <a name="development-considerations"></a>Fejlesztési szempontok

Néhány előkészítéssel és az alábbi témakörökben található útmutatás használatával az Azure egy Azure Stack hub-környezet emulálása céljából használható.

* Az Azure-ban létrehozhat olyan Azure Resource Manager sablonokat, amelyek üzembe helyezhetők Azure Stack hubhoz. A hordozhatóság biztosításához [tekintse meg a sablonok](azure-stack-develop-templates.md) kialakításával kapcsolatos útmutatást.
* A szolgáltatás rendelkezésre állása és az Azure és a Azure Stack hub között a szolgáltatások verziószámozása eltérő lehet. Az [Azure stack hub házirend moduljának](azure-stack-policy-module.md) használatával korlátozhatja az Azure-szolgáltatások rendelkezésre állását és az erőforrás típusát az Azure stack hub-ban elérhetővé tételekhez. A korlátozási szolgáltatások biztosítják, hogy az alkalmazások a Azure Stack hub számára elérhető szolgáltatásokra támaszkodnak.
* Az [Azure stack hub](https://github.com/Azure/AzureStack-QuickStart-Templates) gyors üzembe helyezési sablonjai olyan gyakori forgatókönyvek, amelyek bemutatják, hogyan lehet az Azure-hoz és az Azure stack hub-hoz egyaránt telepíthető sablonokat fejleszteni.

## <a name="next-steps"></a>Következő lépések

A Azure Stack fejlesztésével kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Ajánlott eljárások az Azure Resource Manager-sablonokhoz](azure-stack-develop-templates.md)
* [Azure Stack hub rövid útmutató sablonok a GitHubon](https://github.com/Azure/AzureStack-QuickStart-Templates)
