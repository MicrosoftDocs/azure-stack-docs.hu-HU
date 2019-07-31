---
title: Alkalmazások fejlesztése a Azure Stackhoz | Microsoft Docs
description: Fejlesztési szempontok a Azure Stack az Azure-szolgáltatások használatával történő prototípusok készítéséhez.
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
ms.date: 05/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 059e7961cae5c6d6faa8d79d05dbc08a05a13893
ms.sourcegitcommit: b3dac698f2e1834491c2f9af56a80e95654f11f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68658568"
---
# <a name="develop-for-azure-stack"></a>Fejlesztés az Azure Stackhez

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Megkezdheti az alkalmazások fejlesztését még akkor is, ha nincs hozzáférése Azure Stack-környezethez. A Azure Stack az adatközpontban futó Microsoft Azure-szolgáltatásokat biztosítja, ami azt jelenti, hogy ugyanazokat az Azure-eszközöket és-folyamatokat használhatja a Azure Stack fejlesztéséhez.

## <a name="development-considerations"></a>Fejlesztési szempontok

Néhány előkészítéssel és az alábbi témakörökben található útmutatás használatával az Azure-ban Emulálhatja Azure Stack környezetét.

* Az Azure-ban létrehozhat Azure Resource Manager sablonokat, amelyek Azure Stack telepíthetők. A hordozhatóság biztosításához [tekintse](azure-stack-develop-templates.md) meg a sablonok kialakításával kapcsolatos útmutatást.
* A szolgáltatás rendelkezésre állása és az Azure és Azure Stack közötti szolgáltatások verziószámozása eltérő lehet. Az [Azure stack házirend modul](azure-stack-policy-module.md) segítségével korlátozhatja az Azure-szolgáltatások rendelkezésre állását és az erőforrás típusát a Azure Stackban elérhetővé tételekhez. A korlátozási szolgáltatások biztosítják, hogy alkalmazásai a Azure Stack számára elérhető szolgáltatásokat használják.
* A [Azure stack](https://github.com/Azure/AzureStack-QuickStart-Templates) rövid útmutatók gyakori példái példákat mutatnak be, amelyek bemutatják, hogyan fejleszthet az Azure-hoz és a Azure stack-hoz egyaránt telepíthető sablonokat.

## <a name="next-steps"></a>További lépések

A Azure Stack fejlesztésével kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Ajánlott eljárások az Azure Resource Manager-sablonokhoz](azure-stack-develop-templates.md)
* [A GitHubon Azure Stack a gyors üzembe helyezési sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates)