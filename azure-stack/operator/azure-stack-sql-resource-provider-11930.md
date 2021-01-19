---
title: Azure Stack hub SQL erőforrás-szolgáltató 1.1.93. x kibocsátási megjegyzései
description: Tekintse meg a kibocsátási megjegyzéseket, és tekintse meg az Azure Stack hub SQL Resource Provider 1.1.93. x frissítésének újdonságait.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: 0f03d1209587bcf27dec2a309b7cec4abc67bf32
ms.sourcegitcommit: 0983c1f90734b7ea5e23ae614eeaed38f9cb3c9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571314"
---
# <a name="sql-resource-provider-1193x-release-notes"></a>SQL Resource Provider 1.1.93. x kibocsátási megjegyzések

Ezek a kibocsátási megjegyzések az SQL Resource Provider 1.1.93. x verziójának újdonságait és ismert problémáit ismertetik.

## <a name="build-reference"></a>Build referenciája
Töltse le az SQL erőforrás-szolgáltató bináris fájlját, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához. Az erőforrás-szolgáltató minimálisan megfelelő Azure Stack hub-buildtel rendelkezik. Az SQL-erőforrás-szolgáltató ezen verziójának telepítéséhez szükséges minimális Azure Stack hub-verzió az alábbi listában látható:

> |Támogatott Azure Stack hub-verzió|SQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |2008-es verzió, 2005|[Az SQL RP verziója 1.1.93.1](https://aka.ms/azshsqlrp11931)|  
> |     |     |

> [!IMPORTANT]
> A MySQL erőforrás-szolgáltató legújabb verziójának telepítése előtt alkalmazza a minimális támogatott Azure Stack hub-frissítést a Azure Stack hub integrált rendszerére.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások

Az Azure Stack hub SQL erőforrás-szolgáltató ezen verziója a következő javításokat és javításokat tartalmazza:

- **Frissítse az alapszintű virtuális gépet egy speciális Windows-kiszolgálóra.** Ez a Windows Server-verzió a Azure Stack hub Add-On RP-infrastruktúrára specializálódott, és nem látható a bérlői piactéren. Győződjön meg arról, hogy a **Microsoft AzureStack Add-On RP Windows Server** -lemezképet az SQL Resource Provider ezen verziójának telepítése vagy frissítése előtt tölti le.
- **Az árva adatbázis metaadatainak és az üzemeltetési kiszolgáló metaadatainak eltávolításának támogatása.** Ha egy üzemeltetési kiszolgáló már nem csatlakoztatható, a bérlő el fogja távolítani az árva adatbázis-metaadatokat a portálról. Ha az üzemeltetési kiszolgálóhoz nem kapcsolódik árva adatbázis-metaadatok, az operátor el tudja távolítani az árva üzemeltetési kiszolgáló metaadatait a felügyeleti portálról.
- **A titkok rotációjának végrehajtásakor nem kötelező argumentumot KeyVaultPfxPassword.** További részletekért olvassa el [ezt a dokumentumot](azure-stack-sql-resource-provider-maintain.md#secrets-rotation) .
- **További hibajavítások.**

Azt javasoljuk, hogy az SQL Resource Provider 1.1.93.1 Az Azure Stack hub 2005-es kiadásra való frissítése után alkalmazza.

## <a name="known-issue"></a>Ismert probléma
A 1.1.93.0 verziójának telepítése sikertelen lehet, ha nem megfelelő AzureRmContext van használatban. Javasoljuk, hogy közvetlenül a 1.1.93.1 verzióra frissítsen. Ha már sikeresen frissített a 1.1.93.0-ra, nyugodtan kihagyhatja a 1.1.93.1 verzióját.

Ha az SQL-erőforrás-szolgáltató újbóli üzembe helyezése már megtörtént, és a verzió már telepítve lett (például ha az SQL Resource Provider 1.1.93.1 már telepítve van, és a rendszer ismét telepíti ugyanezt a verziót), az SQL-erőforrás-szolgáltatót futtató virtuális gép le lesz állítva. A probléma megoldásához nyissa meg a felügyeleti portált, keresse meg, majd indítsa újra a sqlvm nevű virtuális gépet \<version\> a System... nevű erőforráscsoport-csoportban. \<region\> sqladapter.

## <a name="next-steps"></a>További lépések

- [További információ az SQL erőforrás-szolgáltatóról](azure-stack-sql-resource-provider.md).
- [Felkészülés az SQL-erőforrás-szolgáltató üzembe helyezésére](azure-stack-sql-resource-provider-deploy.md#prerequisites).
- [Frissítse az SQL-erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-sql-resource-provider-update.md).

