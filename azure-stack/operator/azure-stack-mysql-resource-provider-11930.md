---
title: Azure Stack hub MySQL erőforrás-szolgáltató 1.1.93.0 kibocsátási megjegyzései
description: Tekintse meg a kibocsátási megjegyzéseket, és tekintse meg az Azure Stack hub MySQL erőforrás-szolgáltató 1.1.93.0 frissítésének újdonságait.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: 225c765642dc48b299c899d71eb0d0ce13d4852d
ms.sourcegitcommit: 0983c1f90734b7ea5e23ae614eeaed38f9cb3c9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571365"
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

A MySQL erőforrás-szolgáltató újbóli üzembe helyezésekor, miközben ugyanazon verzió már telepítve lett (például ha a MySQL erőforrás-szolgáltató 1.1.93.1 már telepítve van, és ugyanaz a verzió van telepítve), a MySQL erőforrás-szolgáltatót üzemeltető virtuális gép leáll. A probléma megoldásához nyissa meg a felügyeleti portált, keresse meg, majd indítsa újra a mysqlvm nevű virtuális gépet \<version\> a System... nevű erőforráscsoport-csoportban. \<region\> mysqladapter.

## <a name="next-steps"></a>További lépések

- [További információ a MySQL erőforrás-szolgáltatóról](azure-stack-mysql-resource-provider.md).
- [Felkészülés a MySQL erőforrás-szolgáltató üzembe helyezésére](azure-stack-mysql-resource-provider-deploy.md#prerequisites).
- [Frissítse a MySQL erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-mysql-resource-provider-update.md).
