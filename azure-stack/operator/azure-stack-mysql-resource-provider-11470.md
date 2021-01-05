---
title: Azure Stack hub MySQL erőforrás-szolgáltató 1.1.47.0 kibocsátási megjegyzései
description: Tekintse meg a kibocsátási megjegyzéseket, és tekintse meg az Azure Stack hub MySQL erőforrás-szolgáltató 1.1.47.0 frissítésének újdonságait.
author: PatAltimore
ms.topic: article
ms.date: 11/26/2019
ms.author: patricka
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: d98a4d4c7de76d5a73cab3005893dd279a6e1025
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869848"
---
# <a name="mysql-resource-provider-11470-release-notes"></a>MySQL erőforrás-szolgáltató 1.1.47.0 kibocsátási megjegyzései

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

Az Azure Stack hub MySQL erőforrás-szolgáltató ezen verziója egy gyorsjavítási kiadás, amellyel az erőforrás-szolgáltató kompatibilis lesz az 1910-es frissítésben szereplő legújabb portálok egyes módosításaival. Nincsenek új funkciók.

Emellett támogatja a legújabb Azure Stack hub API Version profilt 2019-03-01-Hybrid és Azure Stack hub PowerShell-modul 1.8.0. Ezért az üzembe helyezés és a frissítés során nem kell telepíteni a modulok adott korábbi verzióit.

Javasoljuk, hogy alkalmazza a MySQL erőforrás-szolgáltató gyorsjavítási 1.1.47.0 azt követően, hogy Azure Stack hub frissítve lett az 1910-es kiadásra.

## <a name="known-issues"></a>Ismert problémák

Azure Stack hub integrált rendszerek [tanúsítványának elforgatásakor](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation) a KeyVaultPfxPassword argumentum akkor is mendatory, ha nincs szándéka a Key Vault tanúsítvány jelszavának frissítésére.

## <a name="next-steps"></a>További lépések

- [További információ a MySQL erőforrás-szolgáltatóról](azure-stack-mysql-resource-provider.md).
- [Felkészülés a MySQL erőforrás-szolgáltató üzembe helyezésére](azure-stack-mysql-resource-provider-deploy.md#prerequisites).
- [Frissítse a MySQL erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-mysql-resource-provider-update.md).
