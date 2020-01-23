---
title: Azure Stack hub SQL Resource Provider 1.1.33.0 kibocsátási megjegyzései
titleSuffix: Azure Stack Hub
description: Tekintse meg az Azure Stack hub SQL Resource Provider 1.1.33.0 frissítésének kiadási megjegyzéseit.
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
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 27ead61bb1fbb5bdebab439a1deecf3d6f70d861
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76535773"
---
# <a name="sql-resource-provider-11330-release-notes"></a>SQL Resource Provider 1.1.33.0 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések ismertetik az SQL Resource Provider 1.1.33.0 verziójának frissítéseit és ismert problémáit.

## <a name="build-reference"></a>Build referenciája
Töltse le az SQL erőforrás-szolgáltató bináris fájlját, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához. Az erőforrás-szolgáltató minimálisan megfelelő Azure Stack hub-buildtel rendelkezik. Az SQL-erőforrás-szolgáltató ezen verziójának telepítéséhez szükséges minimális Azure Stack hub-verzió az alábbi listában látható:

> |Azure Stack hub minimális verziója|SQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |1808-es verzió (1.1808.0.97)|[Az SQL RP verziója 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Az SQL-erőforrás-szolgáltató legújabb verziójának telepítése előtt alkalmazza a minimális támogatott Azure Stack hub-frissítést az Azure Stack hub integrált rendszerére.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások
Az Azure Stack hub SQL erőforrás-szolgáltató ezen verziója a következő javításokat és javításokat tartalmazza:

### <a name="fixes"></a>Javítások

- **Előfordulhat, hogy az SQL erőforrás-szolgáltató portál bővítmény nem megfelelő előfizetést választ**. Az SQL erőforrás-szolgáltató Azure Resource Manager hívásokat használ a használni kívánt szolgáltatás-felügyeleti előfizetés meghatározásához, ami esetleg nem az *alapértelmezett szolgáltatói előfizetés*. Ha ez történik, az SQL-erőforrás-szolgáltató nem működik megfelelően.

- **Az SQL üzemeltetési kiszolgáló nem sorolja fel az üzemeltetett adatbázisokat.** Előfordulhat, hogy a felhasználó által létrehozott adatbázisok nem jelennek meg az SQL üzemeltetési kiszolgálók bérlői erőforrásainak megtekintésekor.

- A **korábbi SQL-erőforrás-szolgáltató (1.1.30.0) telepítése meghiúsulhat, ha a TLS 1,2 nincs engedélyezve**. Frissítette az SQL Resource Provider 1.1.33.0, hogy engedélyezze a TLS 1,2-et az erőforrás-szolgáltató telepítésekor, az erőforrás-szolgáltató frissítésekor vagy a titkok elforgatásakor.

- **Sikertelen volt az SQL-erőforrás-szolgáltató titkos elforgatása**. A következő hibakód miatt kijavított hiba történt a titkok elforgatásakor: `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Ismert problémák

- **Az SQL SKU-ban akár egy órát is igénybe vehet, hogy megjelenjenek a portálon**. Akár egy óráig is eltarthat, amíg az újonnan létrehozott SKU-t új SQL-adatbázisok létrehozásakor használni szeretné.

    **Megkerülő megoldás**: Nincs.

- Az **SQL-bejelentkezések újra felhasználva**. Ha egy olyan új SQL-bejelentkezést próbál létrehozni ugyanazzal a felhasználónévvel, amely ugyanazzal az előfizetéssel rendelkezik, akkor ugyanazt a bejelentkezési azonosítót és a meglévő jelszót fogja használni.

    **Megkerülő megoldás**: használjon eltérő felhasználóneveket, amikor új bejelentkezéseket hoz létre ugyanazon előfizetés alatt, vagy hozzon létre bejelentkezési adatokat ugyanazzal a felhasználónévvel különböző előfizetésekben.

- A **megosztott SQL-bejelentkezések adatkövetkezetlenséget okozhatnak**. Ha egy SQL-bejelentkezés ugyanahhoz az előfizetéshez tartozó több SQL-adatbázishoz van megosztva, akkor a bejelentkezési jelszó módosítása az adatok inkonzisztenciát okoz.

    **Megkerülő megoldás**: az azonos előfizetéshez tartozó különböző adatbázisokhoz mindig használjon eltérő bejelentkezéseket.

- **Az SQL erőforrás-szolgáltató nem tud felvenni SQL Server always on figyelőt**. Ha a figyelő IP-címét használja a SQL Server mindig a figyelőn, az SQL erőforrás-szolgáltató virtuális gép nem tudja feloldani a figyelő gazdagépének nevét.

    **Áthidaló megoldás**: Győződjön meg arról, hogy a DNS megfelelően működik a figyelő IP-címének a figyelő állomásneve általi feloldásához.

### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Ismert problémák a Cloud adminok operációs Azure Stack hubhoz
Tekintse meg az [Azure stack hub kibocsátási megjegyzései](azure-stack-servicing-policy.md)dokumentációját.

## <a name="next-steps"></a>Következő lépések
[További információ az SQL erőforrás-szolgáltatóról](azure-stack-sql-resource-provider.md).

[Felkészülés az SQL-erőforrás-szolgáltató üzembe helyezésére](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Frissítse az SQL-erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-sql-resource-provider-update.md).