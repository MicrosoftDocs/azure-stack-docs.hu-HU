---
title: Azure Stack SQL Resource Provider 1.1.47.0 kibocsátási megjegyzései | Microsoft Docs
description: Ismerkedjen meg a legújabb Azure Stack SQL Resource Provider frissítésével, beleértve az ismert problémákat és a letöltés helyét.
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
ms.openlocfilehash: 703edc0cc2c5229c3301db0b68a400507fe4c872
ms.sourcegitcommit: 11e0c2d9abbc0a2506f992976b3c9f8ca4e746b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823491"
---
# <a name="sql-resource-provider-11470-release-notes"></a>SQL Resource Provider 1.1.47.0 kibocsátási megjegyzései

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ezek a kibocsátási megjegyzések ismertetik az SQL Resource Provider 1.1.47.0 verziójának frissítéseit és ismert problémáit.

## <a name="build-reference"></a>Build referenciája
Töltse le az SQL erőforrás-szolgáltató bináris fájlját, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához. Az erőforrás-szolgáltató minimálisan megfelelő Azure Stack buildtel rendelkezik. Az SQL-erőforrás-szolgáltató ezen verziójának telepítéséhez szükséges minimális Azure Stack kiadási verzió az alábbi listában látható:

> |Minimális Azure Stack-verzió|SQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |1910-es verzió (1.1910.0.58)|[Az SQL RP verziója 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Alkalmazza a minimális támogatott Azure Stack frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack Development Kitt (ASDK) az SQL Resource Provider legújabb verziójának telepítése előtt.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások

A Azure Stack SQL erőforrás-szolgáltató ezen verziója egy gyorsjavítási kiadás, amellyel az erőforrás-szolgáltató kompatibilis lesz az 1910-es frissítés néhány legújabb változásával, új funkció nélkül.

Emellett támogatja az aktuális legújabb Azure Stack API-s verziójú 2019-03-01-Hybrid és Azure Stack PowerShell-modul 1.8.0-t. Ezért az üzembe helyezés és a frissítés során nem kell telepíteni a modulok adott korábbi verzióit.

Kövesse az erőforrás-szolgáltató frissítési folyamatát az SQL Resource Provider gyorsjavítási 1.1.47.0 alkalmazásához, miután Azure Stack frissült az 1910-es frissítésre. Ez segít a felügyeleti portálon olyan ismert problémák megoldásában, amelyekben az SQL erőforrás-szolgáltató kapacitás-figyelése megtartja a betöltést.

## <a name="known-issues"></a>Ismert problémák

Nincs.

## <a name="next-steps"></a>Következő lépések
[További információ az SQL erőforrás-szolgáltatóról](azure-stack-sql-resource-provider.md).

[Felkészülés az SQL-erőforrás-szolgáltató üzembe helyezésére](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Frissítse az SQL-erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-sql-resource-provider-update.md). 
