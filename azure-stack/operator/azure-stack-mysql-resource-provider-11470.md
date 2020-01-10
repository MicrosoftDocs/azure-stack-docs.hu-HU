---
title: Azure Stack hub MySQL erőforrás-szolgáltató 1.1.47.0 kibocsátási megjegyzései | Microsoft Docs
description: Tekintse meg a kibocsátási megjegyzéseket, és tekintse meg az Azure Stack hub MySQL erőforrás-szolgáltató 1.1.47.0 frissítésének újdonságait.
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
ms.openlocfilehash: 32ac5cb56df9fc860bd2873c818cabb93723a170
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75804900"
---
# <a name="mysql-resource-provider-11470-release-notes"></a>MySQL erőforrás-szolgáltató 1.1.47.0 kibocsátási megjegyzései

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek és Azure Stack Development Kit*

Ezek a kibocsátási megjegyzések ismertetik a MySQL erőforrás-szolgáltató 1.1.47.0-verziójának újdonságait és ismert problémáit.

## <a name="build-reference"></a>Build referenciája
Töltse le a MySQL erőforrás-szolgáltató bináris fájlját, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához. Az erőforrás-szolgáltató minimálisan megfelelő Azure Stack hub-buildtel rendelkezik. A MySQL erőforrás-szolgáltató ezen verziójának telepítéséhez szükséges minimális Azure Stack hub kiadási verzió az alábbi listában látható:

> |Azure Stack hub minimális verziója|MySQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |1910-es verzió (1.1910.0.58)|[MySQL RP-verzió 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Alkalmazza a minimális támogatott Azure Stack hub-frissítést a Azure Stack hub integrált rendszerére, vagy telepítse a legújabb Azure Stack Development Kitt (ASDK) a MySQL erőforrás-szolgáltató legújabb verziójának üzembe helyezése előtt.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások

Az Azure Stack hub MySQL erőforrás-szolgáltató ezen verziója egy gyorsjavítási kiadás, amellyel az erőforrás-szolgáltató kompatibilis lesz az 1910-es frissítés néhány legújabb változásával, új funkció nélkül.

Emellett támogatja a legújabb Azure Stack hub API Version profilt 2019-03-01-Hybrid és Azure Stack hub PowerShell-modul 1.8.0-t. Ezért az üzembe helyezés és a frissítés során nem kell telepíteni a modulok adott korábbi verzióit.

Javasoljuk, hogy alkalmazza a MySQL erőforrás-szolgáltató gyorsjavítási 1.1.47.0 azt követően, hogy Azure Stack hub frissítve lett az 1910-es kiadásra.

## <a name="known-issues"></a>Ismert problémák

Nincs.

## <a name="next-steps"></a>Következő lépések
[További információ a MySQL erőforrás-szolgáltatóról](azure-stack-mysql-resource-provider.md).

[Felkészülés a MySQL erőforrás-szolgáltató üzembe helyezésére](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Frissítse a MySQL erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-mysql-resource-provider-update.md). 
