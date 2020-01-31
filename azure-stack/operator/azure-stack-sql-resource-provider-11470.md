---
title: Azure Stack hub SQL Resource Provider 1.1.47.0 kibocsátási megjegyzései
description: Ismerje meg, hogy mi a legújabb Azure Stack hub SQL erőforrás-szolgáltató frissítése, beleértve az ismert problémákat és a letöltés helyét.
author: justinha
ms.topic: article
ms.date: 11/26/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: 1210b537d36d4be5c31f596bde589afa9b3cadfa
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882849"
---
# <a name="sql-resource-provider-11470-release-notes"></a>SQL Resource Provider 1.1.47.0 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések ismertetik az SQL Resource Provider 1.1.47.0 verziójának frissítéseit és ismert problémáit.

## <a name="build-reference"></a>Build referenciája
Töltse le az SQL erőforrás-szolgáltató bináris fájlját, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához. Az erőforrás-szolgáltató minimálisan megfelelő Azure Stack hub-buildtel rendelkezik. Az SQL-erőforrás-szolgáltató ezen verziójának telepítéséhez szükséges minimális Azure Stack hub-verzió az alábbi listában látható:

> |Azure Stack hub minimális verziója|SQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |1910-es verzió (1.1910.0.58)|[Az SQL RP verziója 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Az SQL-erőforrás-szolgáltató legújabb verziójának telepítése előtt alkalmazza a minimális támogatott Azure Stack hub-frissítést az Azure Stack hub integrált rendszerére.

## <a name="new-features-and-fixes"></a>Új funkciók és hibajavítások

Az Azure Stack hub SQL erőforrás-szolgáltatójának ezen verziója egy gyorsjavítási kiadás, amellyel az erőforrás-szolgáltató kompatibilis lesz az 1910-es frissítés néhány legújabb változásával, új funkció nélkül.

Emellett támogatja a legújabb Azure Stack hub API Version profilt 2019-03-01-Hybrid és Azure Stack hub PowerShell-modul 1.8.0-t. Ezért az üzembe helyezés és a frissítés során nem kell telepíteni a modulok adott korábbi verzióit.

Kövesse az erőforrás-szolgáltató frissítési folyamatát az SQL Resource Provider gyorsjavítás 1.1.47.0 alkalmazásához, miután a Azure Stack hub frissítve lett az 1910-es frissítésre. Ez segít a felügyeleti portálon olyan ismert problémák megoldásában, amelyekben az SQL erőforrás-szolgáltató kapacitás-figyelése megtartja a betöltést.

## <a name="known-issues"></a>Ismert problémák

Nincs.

## <a name="next-steps"></a>Következő lépések
[További információ az SQL erőforrás-szolgáltatóról](azure-stack-sql-resource-provider.md).

[Felkészülés az SQL-erőforrás-szolgáltató üzembe helyezésére](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Frissítse az SQL-erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-sql-resource-provider-update.md). 
