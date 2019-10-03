---
title: Azure Stack SQL Resource Provider 1.1.30.0 kibocsátási megjegyzései | Microsoft Docs
description: Ismerkedjen meg a legújabb Azure Stack SQL Resource Provider frissítésével, beleértve az ismert problémákat és a letöltés helyét.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 69ea42a9efbf57cfdeb589cc221eae8a9f21913c
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829316"
---
# <a name="sql-resource-provider-11330-release-notes"></a>SQL Resource Provider 1.1.33.0 kibocsátási megjegyzései

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ezek a kibocsátási megjegyzések ismertetik az SQL Resource Provider 1.1.33.0 verziójának frissítéseit és ismert problémáit.

## <a name="build-reference"></a>Hivatkozás létrehozása
Töltse le az SQL erőforrás-szolgáltató bináris fájlját, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához. Az erőforrás-szolgáltató minimálisan megfelelő Azure Stack buildtel rendelkezik. Az SQL-erőforrás-szolgáltató ezen verziójának telepítéséhez szükséges minimális Azure Stack kiadási verzió az alábbi listában látható:

> |Minimális Azure Stack-verzió|SQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |1808-es verzió (1.1808.0.97)|[Az SQL RP verziója 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Alkalmazza a minimális támogatott Azure Stack frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack Development Kitt (ASDK) az SQL Resource Provider legújabb verziójának telepítése előtt.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások
A Azure Stack SQL erőforrás-szolgáltató ezen verziója a következő javításokat és javításokat tartalmazza:

### <a name="fixes"></a>Javítások
- **Előfordulhat, hogy az SQL erőforrás-szolgáltató portál bővítmény nem megfelelő előfizetést választ**. Az SQL erőforrás-szolgáltató Azure Resource Manager hívásokat használ a használni kívánt szolgáltatás-felügyeleti előfizetés meghatározásához, ami esetleg nem az *alapértelmezett szolgáltatói előfizetés*. Ha ez történik, az SQL-erőforrás szolgáltatója nem működik megfelelően. 

- **Az SQL üzemeltetési kiszolgáló nem sorolja fel az üzemeltetett adatbázisokat.** Előfordulhat, hogy a felhasználó által létrehozott adatbázisok nem jelennek meg az SQL üzemeltetési kiszolgálók bérlői erőforrásainak megtekintésekor.

- **A korábbi SQL-erőforrás-szolgáltató (1.1.30.0) telepítése meghiúsulhat, ha a TLS 1,2 nincs engedélyezve**. Frissítette az SQL Resource Provider 1.1.33.0, hogy engedélyezze a TLS 1,2-et az erőforrás-szolgáltató telepítésekor, az erőforrás-szolgáltató frissítésekor vagy a titkok elforgatásakor. 

- **Sikertelen volt az SQL-erőforrás-szolgáltató titkos elforgatása**. A következő hibakód miatt kijavított hiba történt a titkok elforgatásakor: `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Ismert problémák 

- **Az SQL SKU-ban akár egy órát is igénybe vehet, hogy megjelenjenek a portálon**. Akár egy óráig is eltarthat, amíg az újonnan létrehozott SKU-t új SQL-adatbázisok létrehozásakor használni szeretné. 

    **Áthidaló megoldás**: Nincs.

- Az **SQL-bejelentkezések újra felhasználva**. Ha egy olyan új SQL-bejelentkezést próbál létrehozni ugyanazzal a felhasználónévvel, amely ugyanazzal az előfizetéssel rendelkezik, akkor ugyanazt a bejelentkezési azonosítót és a meglévő jelszót fogja használni. 

    **Áthidaló megoldás**: Használjon más felhasználóneveket, amikor új bejelentkezéseket hoz létre ugyanazon előfizetés alatt, vagy hozzon létre bejelentkezési adatokat ugyanazzal a felhasználónévvel különböző előfizetésekben.

- A **megosztott SQL-bejelentkezések adatkövetkezetlenséget okozhatnak**. Ha egy SQL-bejelentkezés ugyanahhoz az előfizetéshez tartozó több SQL-adatbázishoz van megosztva, akkor a bejelentkezési jelszó módosítása az adatok inkonzisztenciát okoz.

    **Áthidaló megoldás**: Mindig használjon eltérő bejelentkezési adatokat a különböző adatbázisokhoz ugyanazon előfizetés alatt.

- **Az SQL erőforrás-szolgáltató nem tud felvenni SQL Server always on figyelőt**. Ha a figyelő IP-címét használja a SQL Server mindig a figyelőn, az SQL-erőforrás szolgáltatójának virtuális gépe nem tudja feloldani a figyelő gazdagépének nevét.

    **Áthidaló megoldás**: Győződjön meg arról, hogy a DNS megfelelően működik-e a figyelő IP-címének a figyelő állomásneve általi feloldásához.

### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>A Cloud adminok operációs Azure Stack ismert problémái
Tekintse át a [Azure stack kibocsátási megjegyzések](azure-stack-servicing-policy.md)dokumentációját.

## <a name="next-steps"></a>További lépések
[További információ az SQL erőforrás-szolgáltatóról](azure-stack-sql-resource-provider.md).

[Felkészülés az SQL-erőforrás-szolgáltató üzembe helyezésére](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Frissítse az SQL-erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-sql-resource-provider-update.md). 