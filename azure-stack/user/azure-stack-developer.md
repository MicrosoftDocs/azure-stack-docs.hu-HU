---
title: Alkalmazások fejlesztése az Azure Stackhez |} A Microsoft Docs
description: Fejlesztési szempontok prototípus-készítés alkalmazásokhoz az Azure Stackben
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
ms.openlocfilehash: 4273326001dc79abb7ea522c3bd4c01e46bceb2b
ms.sourcegitcommit: 6fcd5df8b77e782ef72f0e1419f1f75ec8c16c04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991274"
---
# <a name="develop-for-azure-stack"></a>Fejlesztés az Azure Stackhez

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Kezdheti alkalmazások fejlesztésével, még akkor is, ha nem rendelkezik az Azure Stack környezettel való hozzáférést. Azure Stack biztosít a Microsoft Azure-szolgáltatások, amelyek a helyi adatközpontban futnak, mert hasonló eszközökkel és folyamatokkal használhatja az Azure Stacken fejleszthet, mint az Azure-ral.

## <a name="development-considerations"></a>Fejlesztési szempontok

Az egyes előkészítése, és a következő témakörök útmutatása használhatja az Azure az Azure Stack-környezet emulációjához.

* Az Azure-ban létrehozhat az Azure Resource Manager-sablonok, amelyek az Azure Stack üzembe helyezhető. Lásd: [sablon szempontok](azure-stack-develop-templates.md) útmutatást annak biztosítása érdekében a hordozhatóság sablonok fejlesztéséhez.
* Az Azure és az Azure Stack közötti a szolgáltatás rendelkezésre állása és service versioning különbségek vannak. Használhatja a [Azure Stack-házirendmodul](azure-stack-policy-module.md) , hogy korlátozza azokat az Azure service rendelkezésre állását és az erőforrás elérhető az Azure Stackben. Szolgáltatások korlátozásra biztosítja, hogy az alkalmazások támaszkodjon az Azure Stackben elérhető szolgáltatások.
* A [Azure Stack gyorsindítási sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates) gyakori forgatókönyv példák azt mutatják be, hogyan hozhat létre a sablonok, az Azure és az Azure Stackben is telepíthető.

## <a name="next-steps"></a>További lépések

Azure Stack fejlesztői kapcsolatos további információkért lásd a következő cikkeket:

* [Ajánlott eljárások az Azure Resource Manager sablon](azure-stack-develop-templates.md)
* [Az Azure Stack gyorsindítási sablonok github](https://github.com/Azure/AzureStack-QuickStart-Templates)