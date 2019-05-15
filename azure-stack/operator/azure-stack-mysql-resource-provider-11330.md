---
title: Az Azure Stack MySQL erőforrás szolgáltató 1.1.30.0 kibocsátási megjegyzései |} A Microsoft Docs
description: További tudnivalók a a legújabb Azure Stack MySQL erőforrás szolgáltató frissítése, beleértve az olyan ismert problémákat, és a helyét, ahonnan letöltheti azt.
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
ms.date: 01/09/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: b5e649768f075f4e448520c648a77aa64351842a
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618088"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>MySQL erőforrás-szolgáltató 1.1.33.0 kibocsátási megjegyzései

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A kibocsátási megjegyzések a fejlesztéseket és a MySQL erőforrás-szolgáltató verziója 1.1.33.0 az ismert problémákat ismertetik.

## <a name="build-reference"></a>Hivatkozás létrehozása
Töltse le a MySQL erőforrás-szolgáltató bináris, és futtassa a mappába, csomagolja ki a tartalmát egy ideiglenes könyvtárba. Az erőforrás-szolgáltató rendelkezik egy minimális megfelelő Azure Stack hozhat létre. Ez a verzió, a MySQL erőforrás-szolgáltató telepítéséhez szükséges minimális Azure Stack-verzió alább:

> |Azure Stack minimális verziója|MySQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |Verzió 1808 (1.1808.0.97)|[MySQL-RP 1.1.33.0 verzió](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> A minimális támogatott az Azure Stack-frissítés alkalmazása az Azure Stackkel integrált rendszereknél, vagy a legújabb Azure Stack Development Kit (ASDK) telepíteni a MySQL erőforrás-szolgáltató legújabb verziójának telepítése előtt.

## <a name="new-features-and-fixes"></a>Új funkciókkal és javításokkal
Az Azure Stack MySQL erőforrás-szolgáltató ezen verziója tartalmazza a következő fejlesztések és javítások:

### <a name="fixes"></a>Javítások
- **MySQL erőforrás-szolgáltató portál bővítmény előfordulhat, hogy válassza ki a megfelelő előfizetést**. A MySQL erőforrás-szolgáltatót használja az Azure Resource Manager hívások megállapításához az első szolgáltatás rendszergazdai előfizetést szeretné használni, amely nem lehet a *szolgáltatói előfizetés alapértelmezett*. Ha ez történik, a MySQL erőforrás-szolgáltató nem működik megfelelően. 

- **A MySQL üzemeltető kiszolgálót nem tartalmazza a tárolt adatbázisok.** Felhasználó által létrehozott adatbázisok előfordulhat, hogy nem szerepel a listán, bérlői erőforrások üzemeltető kiszolgálók MySQL-hez készült megtekintésekor.

- **A korábbi MySQL erőforrás szolgáltató (1.1.30.0) központi telepítés sikertelen lehet, ha nincs engedélyezve a TLS 1.2**. Frissítve a MySQL erőforrás-szolgáltató 1.1.33.0 engedélyezéséhez a TLS 1.2-es, az erőforrás-szolgáltató, az erőforrás-szolgáltató frissítése vagy titkos kulcsok Elforgatás üzembe helyezésekor. 

- **MySQL erőforrás-szolgáltató titkos elforgatás nem sikerül**. Kijavítva a hiba a következő hibakóddal eredményez, amikor a titkos kulcsok elforgatás: `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Ismert problémák 

- **MySQL termékváltozatot órát is igénybe vehet egy lesznek láthatók a portálon**. Is igénybe vehet egy órát újonnan létrehozott termékváltozatok lesznek láthatók a használatra, amikor új MySQL-adatbázisok létrehozásához. 

    **Megkerülő megoldás**: Nincs.

- **Újra felhasználhatók a MySQL-bejelentkezések**. Hozzon létre egy új MySQL próbál jelentkezzen be a felhasználónevet, egy meglévő bejelentkezést egy előfizetésen belül eredményez újbóli felhasználása ugyanazokat a bejelentkezési adatokat és a meglévő jelszót. 

    **Megkerülő megoldás**: Azonos előfizetéshez tartozó új bejelentkezéseket létrehozásakor különböző felhasználónevet használ, vagy hozzon létre bejelentkezések a különböző előfizetéseknél felhasználónevet.

- **Megosztott MySQL bejelentkezések adatinkonzisztenciát okozhat**. Ha egy MySQL-bejelentkezés több, azonos előfizetéshez tartozó MySQL-adatbázisok számára meg van osztva, a bejelentkezési jelszó módosítása adatinkonzisztenciát okoz.

    **Megkerülő megoldás**: Mindig használjon különböző bejelentkezések különböző adatbázisokat ugyanahhoz az előfizetéshez.


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Azure Stack működő felhő rendszergazdái számára ismert problémák
A dokumentáció a [Azure Stack kibocsátási megjegyzések](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>További lépések
[További információ a MySQL erőforrás-szolgáltató](azure-stack-mysql-resource-provider.md).

[A MySQL erőforrás-szolgáltató telepítésének előkészítéséhez](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[A MySQL erőforrás-szolgáltató frissítés egy korábbi verziójáról](azure-stack-mysql-resource-provider-update.md). 