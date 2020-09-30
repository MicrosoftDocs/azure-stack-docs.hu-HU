---
title: Azure Stack hub MySQL erőforrás-szolgáltató 1.1.93.0 kibocsátási megjegyzései
description: Tekintse meg a kibocsátási megjegyzéseket, és tekintse meg az Azure Stack hub MySQL erőforrás-szolgáltató 1.1.93.0 frissítésének újdonságait.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: f298e2e9e55d8057a63264fb347069ef07204a16
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91585934"
---
# <a name="mysql-resource-provider-11930-release-notes"></a>MySQL erőforrás-szolgáltató 1.1.93.0 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések ismertetik a MySQL erőforrás-szolgáltató 1.1.93.0-verziójának újdonságait és ismert problémáit.

## <a name="build-reference"></a>Build referenciája
Töltse le a MySQL erőforrás-szolgáltató bináris fájlját, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához. Az erőforrás-szolgáltató minimálisan megfelelő Azure Stack hub-buildtel rendelkezik. A MySQL erőforrás-szolgáltató ezen verziójának telepítéséhez szükséges minimális Azure Stack hub kiadási verzió az alábbi listában látható:

> |Támogatott Azure Stack hub-verzió|MySQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |2005-es verzió|[MySQL RP-verzió 1.1.93.0](https://aka.ms/azshmysqlrp11930)|  
> |     |     |

> [!IMPORTANT]
> A MySQL erőforrás-szolgáltató legújabb verziójának telepítése előtt alkalmazza a minimális támogatott Azure Stack hub-frissítést a Azure Stack hub integrált rendszerére.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások

Az Azure Stack hub MySQL erőforrás-szolgáltató ezen verziója a következő javításokat és javításokat tartalmazza:

- **Frissítse az alapszintű virtuális gépet egy speciális Windows-kiszolgálóra.** Ez a Windows Server-verzió a Azure Stack hub-bővítményhez tartozó RP-infrastruktúrára specializálódott, és nem látható a bérlői piactéren. Győződjön meg arról, hogy a MySQL erőforrás-szolgáltató ezen verziójára való telepítése vagy frissítése előtt letölti a **Microsoft AZURESTACK csak belső Windows Server-kiszolgáló** lemezképét.
- **Az árva adatbázis metaadatainak és az üzemeltetési kiszolgáló metaadatainak eltávolításának támogatása.** Ha egy üzemeltetési kiszolgáló már nem csatlakoztatható, a bérlő el fogja távolítani az árva adatbázis-metaadatokat a portálról. Ha az üzemeltetési kiszolgálóhoz nem kapcsolódik árva adatbázis-metaadatok, az operátor el tudja távolítani az árva üzemeltetési kiszolgáló metaadatait a felügyeleti portálról.
- **A titkok rotációjának végrehajtásakor nem kötelező argumentumot KeyVaultPfxPassword.** További részletekért olvassa el [ezt a dokumentumot](azure-stack-sql-resource-provider-maintain.md#secrets-rotation) .
- **További hibajavítások.**

Javasoljuk, hogy a MySQL erőforrás-szolgáltató 1.1.93.0 alkalmazza Azure Stack hub frissítése után az 2005-es kiadásra.

## <a name="known-issues"></a>Ismert problémák
Nincsenek.

## <a name="next-steps"></a>További lépések

- [További információ a MySQL erőforrás-szolgáltatóról](azure-stack-mysql-resource-provider.md).
- [Felkészülés a MySQL erőforrás-szolgáltató üzembe helyezésére](azure-stack-mysql-resource-provider-deploy.md#prerequisites).
- [Frissítse a MySQL erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-mysql-resource-provider-update.md).
