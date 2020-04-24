---
title: Alkalmazások fejlesztése Azure Stack hub-hoz
description: Fejlesztési szempontok a Azure Stack hub-beli alkalmazások Azure-szolgáltatásokkal történő prototípusának fejlesztéséhez.
author: sethmanheim
ms.topic: article
ms.date: 01/24/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 3404e0d0e5bbf2c8293d70a7cf816b9e4923261f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77704080"
---
# <a name="develop-for-azure-stack-hub"></a>Fejlesztés Azure Stack hub-hoz

Megkezdheti az alkalmazások fejlesztését még akkor is, ha nem fér hozzá egy Azure Stack hub-környezethez. Az Azure Stack hub biztosítja az adatközpontban futó Microsoft Azure-szolgáltatásokat, ami azt jelenti, hogy ugyanazokat az Azure-eszközöket és-folyamatokat használja az Azure Stack hub fejlesztéséhez.

## <a name="development-considerations"></a>Fejlesztési szempontok

Néhány előkészítéssel és az alábbi témakörökben található útmutatás használatával az Azure egy Azure Stack hub-környezet emulálása céljából használható.

* Az Azure-ban létrehozhat olyan Azure Resource Manager sablonokat, amelyek üzembe helyezhetők Azure Stack hubhoz. A hordozhatóság biztosításához [tekintse meg a sablonok](azure-stack-develop-templates.md) kialakításával kapcsolatos útmutatást.
* A szolgáltatás rendelkezésre állása és az Azure és a Azure Stack hub között a szolgáltatások verziószámozása eltérő lehet. Az [Azure stack hub házirend moduljának](azure-stack-policy-module.md) használatával korlátozhatja az Azure-szolgáltatások rendelkezésre állását és az erőforrás típusát az Azure stack hub-ban elérhetővé tételekhez. A korlátozási szolgáltatások biztosítják, hogy az alkalmazások a Azure Stack hub számára elérhető szolgáltatásokra támaszkodnak.
* Az [Azure stack hub](https://github.com/Azure/AzureStack-QuickStart-Templates) gyors üzembe helyezési sablonjai olyan gyakori forgatókönyvek, amelyek bemutatják, hogyan lehet az Azure-hoz és az Azure stack hub-hoz egyaránt telepíthető sablonokat fejleszteni.

## <a name="next-steps"></a>További lépések

A Azure Stack fejlesztésével kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Ajánlott eljárások az Azure Resource Manager-sablonokhoz](azure-stack-develop-templates.md)
* [Azure Stack hub rövid útmutató sablonok a GitHubon](https://github.com/Azure/AzureStack-QuickStart-Templates)
