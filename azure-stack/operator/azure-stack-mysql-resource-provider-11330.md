---
title: Azure Stack MySQL erőforrás-szolgáltató 1.1.30.0 kibocsátási megjegyzései | Microsoft Docs
description: Ismerkedjen meg a legújabb Azure Stack MySQL erőforrás-szolgáltató frissítésével, beleértve az ismert problémákat és a letöltés helyét.
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
ms.openlocfilehash: fdac6c099980a1c2cedb3271123908539d18169d
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909149"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>MySQL erőforrás-szolgáltató 1.1.33.0 kibocsátási megjegyzései

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ezek a kibocsátási megjegyzések ismertetik a MySQL erőforrás-szolgáltató 1.1.33.0-verziójának újdonságait és ismert problémáit.

## <a name="build-reference"></a>Hivatkozás létrehozása
Töltse le a MySQL erőforrás-szolgáltató bináris fájlját, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához. Az erőforrás-szolgáltató minimálisan megfelelő Azure Stack buildtel rendelkezik. A MySQL erőforrás-szolgáltató ezen verziójának telepítéséhez szükséges minimális Azure Stack kiadási verzió az alábbiakban látható:

> |Minimális Azure Stack-verzió|MySQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |1808-es verzió (1.1808.0.97)|[MySQL RP-verzió 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Alkalmazza a minimális támogatott Azure Stack frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack Development Kitt (ASDK) a MySQL erőforrás-szolgáltató legújabb verziójának telepítése előtt.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások
A Azure Stack MySQL erőforrás-szolgáltató ezen verziója a következő javításokat és javításokat tartalmazza:

### <a name="fixes"></a>Javítások
- **Előfordulhat, hogy a MySQL erőforrás-szolgáltató portál bővítmény nem megfelelő előfizetést választ**. A MySQL erőforrás-szolgáltató Azure Resource Manager hívásokat használ az első olyan szolgáltatás-rendszergazdai előfizetés meghatározásához, amely nem az *alapértelmezett szolgáltatói előfizetés*. Ha ez történik, a MySQL erőforrás-szolgáltató nem működik megfelelően. 

- **A MySQL üzemeltetési kiszolgáló nem sorolja fel az üzemeltetett adatbázisokat.** Előfordulhat, hogy a felhasználó által létrehozott adatbázisok nem jelennek meg a MySQL üzemeltetési kiszolgálók bérlői erőforrásainak megtekintésekor.

- **Az előző MySQL erőforrás-szolgáltató (1.1.30.0) telepítése meghiúsulhat, ha a TLS 1,2 nincs engedélyezve**. Frissítettük a MySQL erőforrás-szolgáltató 1.1.33.0, hogy engedélyezzék a TLS 1,2-et az erőforrás-szolgáltató üzembe helyezésekor, az erőforrás-szolgáltató frissítésekor vagy a titkok elforgatásakor. 

- **Nem sikerül a MySQL erőforrás-szolgáltató titkos elforgatása**. A következő hibakód miatt kijavított hiba történt a titkok elforgatásakor: `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Ismert problémák 

- **A MySQL SKU-ban akár egy órát is igénybe vehet, hogy megjelenjenek a portálon**. Akár egy órát is igénybe vehet, hogy az újonnan létrehozott SKU-i megjelenjenek az új MySQL-adatbázisok létrehozásakor. 

    **Áthidaló megoldás**: Nincs.

- A **MySQL-bejelentkezések újrafelhasználása**. Az azonos előfizetéshez tartozó meglévő bejelentkezéssel azonos felhasználónévvel rendelkező új MySQL-bejelentkezés létrehozásakor a rendszer ugyanazt a bejelentkezési azonosítót és a meglévő jelszót fogja használni. 

    **Áthidaló megoldás**: Használjon más felhasználóneveket, amikor új bejelentkezéseket hoz létre ugyanazon előfizetés alatt, vagy hozzon létre bejelentkezési adatokat ugyanazzal a felhasználónévvel különböző előfizetésekben.

- A **megosztott MySQL-bejelentkezések adatkövetkezetlenséget okozhatnak**. Ha egy MySQL-bejelentkezés több MySQL-adatbázishoz van megosztva ugyanazzal az előfizetéssel, akkor a bejelentkezési jelszó módosítása az adatok inkonzisztenciát okoz.

    **Áthidaló megoldás**: Mindig használjon eltérő bejelentkezési adatokat a különböző adatbázisokhoz ugyanazon előfizetés alatt.


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>A Cloud adminok operációs Azure Stack ismert problémái
Tekintse át a [Azure stack kibocsátási megjegyzések](azure-stack-servicing-policy.md)dokumentációját.

## <a name="next-steps"></a>További lépések
[További információ a MySQL erőforrás-szolgáltatóról](azure-stack-mysql-resource-provider.md).

[Felkészülés a MySQL erőforrás-szolgáltató üzembe helyezésére](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Frissítse a MySQL erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-mysql-resource-provider-update.md). 