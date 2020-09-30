---
title: Azure Stack hub SQL Resource Provider 1.1.93.0 kibocsátási megjegyzései
description: Tekintse meg a kibocsátási megjegyzéseket, és tekintse meg az Azure Stack hub SQL Resource Provider 1.1.93.0 frissítésének újdonságait.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: 035e1cc2e78aaebcc0ea653354d00f617d7e95c5
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91585924"
---
# <a name="sql-resource-provider-11930-release-notes"></a>SQL Resource Provider 1.1.93.0 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések ismertetik az SQL Resource Provider 1.1.93.0 verziójának frissítéseit és ismert problémáit.

## <a name="build-reference"></a>Build referenciája
Töltse le az SQL erőforrás-szolgáltató bináris fájlját, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához. Az erőforrás-szolgáltató minimálisan megfelelő Azure Stack hub-buildtel rendelkezik. Az SQL-erőforrás-szolgáltató ezen verziójának telepítéséhez szükséges minimális Azure Stack hub-verzió az alábbi listában látható:

> |Támogatott Azure Stack hub-verzió|SQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |2005-es verzió|[Az SQL RP verziója 1.1.93.0](https://aka.ms/azshsqlrp11930)|  
> |     |     |

> [!IMPORTANT]
> A MySQL erőforrás-szolgáltató legújabb verziójának telepítése előtt alkalmazza a minimális támogatott Azure Stack hub-frissítést a Azure Stack hub integrált rendszerére.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások

Az Azure Stack hub SQL erőforrás-szolgáltató ezen verziója a következő javításokat és javításokat tartalmazza:

- **Frissítse az alapszintű virtuális gépet egy speciális Windows-kiszolgálóra.** Ez a Windows Server-verzió a Azure Stack hub-bővítményhez tartozó RP-infrastruktúrára specializálódott, és nem látható a bérlői piactéren. Győződjön meg arról, hogy az SQL-erőforrás-szolgáltató ezen verziójára való üzembe helyezése vagy frissítése előtt letölti a **Microsoft AzureStack kiegészítő Windows Server-kiszolgáló csak belső** lemezképét.
- **Az árva adatbázis metaadatainak és az üzemeltetési kiszolgáló metaadatainak eltávolításának támogatása.** Ha egy üzemeltetési kiszolgáló már nem csatlakoztatható, a bérlő el fogja távolítani az árva adatbázis-metaadatokat a portálról. Ha az üzemeltetési kiszolgálóhoz nem kapcsolódik árva adatbázis-metaadatok, az operátor el tudja távolítani az árva üzemeltetési kiszolgáló metaadatait a felügyeleti portálról.
- **A titkok rotációjának végrehajtásakor nem kötelező argumentumot KeyVaultPfxPassword.** További részletekért olvassa el [ezt a dokumentumot](azure-stack-sql-resource-provider-maintain.md#secrets-rotation) .
- **További hibajavítások.**

Azt javasoljuk, hogy az SQL Resource Provider 1.1.93.0 az Azure Stack hub 2005-es kiadásra való frissítése után alkalmazza.

## <a name="known-issues"></a>Ismert problémák
Nincsenek.

## <a name="next-steps"></a>További lépések

- [További információ az SQL erőforrás-szolgáltatóról](azure-stack-sql-resource-provider.md).
- [Felkészülés az SQL-erőforrás-szolgáltató üzembe helyezésére](azure-stack-sql-resource-provider-deploy.md#prerequisites).
- [Frissítse az SQL-erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-sql-resource-provider-update.md).

