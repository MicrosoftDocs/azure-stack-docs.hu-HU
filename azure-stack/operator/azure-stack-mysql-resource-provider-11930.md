---
title: Azure Stack hub MySQL erőforrás-szolgáltató 1.1.93.0 kibocsátási megjegyzései
description: Tekintse meg a kibocsátási megjegyzéseket, és tekintse meg az Azure Stack hub MySQL erőforrás-szolgáltató 1.1.93.0 frissítésének újdonságait.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: 2312219d67741b9485a6070c00418762e50fac73
ms.sourcegitcommit: a745662c7a5a18f135accf3f70d8508b57e83e2b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97737835"
---
# <a name="mysql-resource-provider-1193x-release-notes"></a>MySQL erőforrás-szolgáltató 1.1.93. x kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések a MySQL erőforrás-szolgáltató 1.1.93. x verziójának újdonságait és ismert problémáit ismertetik.

## <a name="build-reference"></a>Build referenciája
Töltse le a MySQL erőforrás-szolgáltató bináris fájlját, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához. Az erőforrás-szolgáltató minimálisan megfelelő Azure Stack hub-buildtel rendelkezik. A MySQL erőforrás-szolgáltató ezen verziójának telepítéséhez szükséges minimális Azure Stack hub kiadási verzió az alábbi listában látható:

> |Támogatott Azure Stack hub-verzió|MySQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |2008-es verzió, 2005|[MySQL RP-verzió 1.1.93.1](https://aka.ms/azshmysqlrp11931)|  
> |     |     |

> [!IMPORTANT]
> A MySQL erőforrás-szolgáltató legújabb verziójának telepítése előtt alkalmazza a minimális támogatott Azure Stack hub-frissítést a Azure Stack hub integrált rendszerére.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások

Az Azure Stack hub MySQL erőforrás-szolgáltató ezen verziója a következő javításokat és javításokat tartalmazza:

- **Frissítse az alapszintű virtuális gépet egy speciális Windows-kiszolgálóra.** Ez a Windows Server-verzió a Azure Stack hub Add-On RP-infrastruktúrára specializálódott, és nem látható a bérlői piactéren. Győződjön meg arról, hogy a MySQL erőforrás-szolgáltató ezen verziójának telepítése vagy frissítése előtt letölti a **Microsoft AzureStack Add-On RP Windows Server** -lemezképet.
- **Az árva adatbázis metaadatainak és az üzemeltetési kiszolgáló metaadatainak eltávolításának támogatása.** Ha egy üzemeltetési kiszolgáló már nem csatlakoztatható, a bérlő el fogja távolítani az árva adatbázis-metaadatokat a portálról. Ha az üzemeltetési kiszolgálóhoz nem kapcsolódik árva adatbázis-metaadatok, az operátor el tudja távolítani az árva üzemeltetési kiszolgáló metaadatait a felügyeleti portálról.
- **A titkok rotációjának végrehajtásakor nem kötelező argumentumot KeyVaultPfxPassword.** További részletekért olvassa el [ezt a dokumentumot](azure-stack-sql-resource-provider-maintain.md#secrets-rotation) .
- **További hibajavítások.**

Javasoljuk, hogy a MySQL erőforrás-szolgáltató 1.1.93.1 alkalmazza Azure Stack hub frissítése után az 2005-es kiadásra.

## <a name="known-issues"></a>Ismert problémák
A 1.1.93.0 verziójának telepítése sikertelen lehet, ha nem megfelelő AzureRmContext van használatban. Javasoljuk, hogy közvetlenül a 1.1.93.1 verzióra frissítsen. Ha már sikeresen frissített a 1.1.93.0-ra, nyugodtan kihagyhatja a 1.1.93.1 verzióját.

## <a name="next-steps"></a>További lépések

- [További információ a MySQL erőforrás-szolgáltatóról](azure-stack-mysql-resource-provider.md).
- [Felkészülés a MySQL erőforrás-szolgáltató üzembe helyezésére](azure-stack-mysql-resource-provider-deploy.md#prerequisites).
- [Frissítse a MySQL erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-mysql-resource-provider-update.md).
