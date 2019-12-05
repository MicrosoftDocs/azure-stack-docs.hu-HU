---
title: Azure Stack MySQL erőforrás-szolgáltató 1.1.47.0 kibocsátási megjegyzései | Microsoft Docs
description: Tekintse meg a kibocsátási megjegyzéseket, és tekintse meg a Azure Stack MySQL erőforrás-szolgáltató 1.1.47.0 frissítésének újdonságait.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: 979aa822c981b4d34cb430e69dc15f80661818fe
ms.sourcegitcommit: 11e0c2d9abbc0a2506f992976b3c9f8ca4e746b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823480"
---
# <a name="mysql-resource-provider-11470-release-notes"></a>MySQL erőforrás-szolgáltató 1.1.47.0 kibocsátási megjegyzései

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ezek a kibocsátási megjegyzések ismertetik a MySQL erőforrás-szolgáltató 1.1.47.0-verziójának újdonságait és ismert problémáit.

## <a name="build-reference"></a>Build referenciája
Töltse le a MySQL erőforrás-szolgáltató bináris fájlját, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához. Az erőforrás-szolgáltató minimálisan megfelelő Azure Stack buildtel rendelkezik. A MySQL erőforrás-szolgáltató ezen verziójának telepítéséhez szükséges minimális Azure Stack kiadási verzió az alábbiakban látható:

> |Minimális Azure Stack-verzió|MySQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |1910-es verzió (1.1910.0.58)|[MySQL RP-verzió 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Alkalmazza a minimális támogatott Azure Stack frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack Development Kitt (ASDK) a MySQL erőforrás-szolgáltató legújabb verziójának telepítése előtt.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások

A Azure Stack MySQL erőforrás-szolgáltató ezen verziója egy gyorsjavítási kiadás, amellyel az erőforrás-szolgáltató kompatibilis lesz a 1910-es frissítés néhány legújabb változásával, új funkció nélkül.

Emellett támogatja az aktuális legújabb Azure Stack API-s verziójú 2019-03-01-Hybrid és Azure Stack PowerShell-modul 1.8.0-t. Ezért az üzembe helyezés és a frissítés során nem kell telepíteni a modulok adott korábbi verzióit.

Azt javasoljuk, hogy az 1910-es kiadásra való frissítés Azure Stack után alkalmazza a MySQL erőforrás-szolgáltató gyorsjavítási 1.1.47.0.

## <a name="known-issues"></a>Ismert problémák

Nincs.

## <a name="next-steps"></a>Következő lépések
[További információ a MySQL erőforrás-szolgáltatóról](azure-stack-mysql-resource-provider.md).

[Felkészülés a MySQL erőforrás-szolgáltató üzembe helyezésére](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Frissítse a MySQL erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-mysql-resource-provider-update.md). 
