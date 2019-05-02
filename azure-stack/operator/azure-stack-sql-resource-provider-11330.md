---
title: Az Azure Stack SQL resource provider 1.1.30.0 kibocsátási megjegyzései |} A Microsoft Docs
description: Ismerje meg, mi az a legújabb Azure Stack SQL resource provider frissítések, beleértve az olyan ismert problémákat és helyét, ahonnan letöltheti azt.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: ae65199ca313dbc8d8410ece02edda97631218e4
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984775"
---
# <a name="sql-resource-provider-11330-release-notes"></a>SQL erőforrás-szolgáltató 1.1.33.0 kibocsátási megjegyzései

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A kibocsátási megjegyzések a javításokat és a SQL-erőforrás-szolgáltató verziója 1.1.33.0 ismert problémákat ismertetik.

## <a name="build-reference"></a>Hivatkozás létrehozása
Töltse le az SQL erőforrás-szolgáltató bináris, és futtassa a mappába, csomagolja ki a tartalmát egy ideiglenes könyvtárba. Az erőforrás-szolgáltató rendelkezik egy minimális megfelelő Azure Stack hozhat létre. Ezen verziója az SQL erőforrás-szolgáltató telepítéséhez szükséges minimális Azure Stack-verzió alább:

> |Azure Stack minimális verziója|Erőforrás-szolgáltató az SQL verziója|
> |-----|-----|
> |Verzió 1808 (1.1808.0.97)|[SQL-RP 1.1.33.0 verzió](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> A minimális támogatott az Azure Stack-frissítés alkalmazása az Azure Stackkel integrált rendszereknél, vagy üzembe helyezése a legújabb Azure Stack Development Kit (ASDK) az erőforrás-szolgáltató az SQL legújabb verziójának telepítése előtt.

## <a name="new-features-and-fixes"></a>Új funkciókkal és javításokkal
Az Azure Stack SQL erőforrás-szolgáltató ezen verziója tartalmazza a következő fejlesztések és javítások:

### <a name="fixes"></a>Javítások
- **SQL resource provider portálbővítmény előfordulhat, hogy válassza ki a megfelelő előfizetést**. Az erőforrás-szolgáltató az SQL Azure Resource Manager hívásait, lehet, hogy nem lesz meghatározni az első szolgáltatás rendszergazdai előfizetést szeretné használni, használja a *szolgáltatói előfizetés alapértelmezett*. Ha ez történik, az erőforrás-szolgáltató az SQL nem működik megfelelően. 

- **SQL Állomáskiszolgálót nem tartalmazza a üzemeltetett adatbázisok.** Felhasználó által létrehozott adatbázisok előfordulhat, hogy nem szerepel a listában, az SQL-kiszolgálókat üzemeltető bérlői erőforrások megtekintésekor.

- **A korábbi SQL resource provider (1.1.30.0) központi telepítés sikertelen lehet, ha nincs engedélyezve a TLS 1.2**. Frissítve az SQL erőforrás-szolgáltató 1.1.33.0 engedélyezéséhez a TLS 1.2-es, az erőforrás-szolgáltató, az erőforrás-szolgáltató frissítése vagy titkos kulcsok Elforgatás üzembe helyezésekor. 

- **SQL erőforrás-szolgáltató titkos elforgatás nem sikerül**. Kijavítva a hiba a következő hibakóddal eredményez, amikor a titkos kulcsok elforgatás: `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Ismert problémák 

- **SQL-termékváltozatok órát is igénybe vehet egy lesznek láthatók a portálon**. Is igénybe vehet egy órát újonnan létrehozott termékváltozatok lesznek láthatók a használatra, amikor új SQL-adatbázisok létrehozásához. 

    **Megkerülő megoldás**: Nincs.

- **Fel újra az SQL-bejelentkezésekben**. Próbál létrehozni egy új SQL jelentkezzen be a felhasználónevet, egy előfizetésen belül egy meglévő bejelentkezést eredményez szakember újból felhasználja a meglévő jelszót, és ugyanazokat a bejelentkezési adatokat. 

    **Megkerülő megoldás**: Azonos előfizetéshez tartozó új bejelentkezéseket létrehozásakor különböző felhasználónevet használ, vagy hozzon létre bejelentkezések a különböző előfizetéseknél felhasználónevet.

- **Megosztott SQL-bejelentkezésekben adatinkonzisztenciát okozhat**. Ha az egy előfizetésen belül több SQL-adatbázis egy SQL-bejelentkezési megosztott, a bejelentkezési jelszó módosítása adatinkonzisztenciát okoz.

    **Megkerülő megoldás**: Mindig használjon különböző bejelentkezések különböző adatbázisokat ugyanahhoz az előfizetéshez.

- **SQL típusú erőforrás-szolgáltató nem sikerül hozzáadni az SQL Server Always On figyelő**. Az SQL Server Always figyelője figyelő IP-címének használata esetén az SQL-erőforrás-szolgáltató virtuális gép nem oldható fel a figyelő gazdagép nevét.

    **Megkerülő megoldás**: Győződjön meg arról, hogy DNS megfelelően működik, és oldja fel a figyelő IP-címet figyelő gazdagép nevét.

### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Azure Stack működő felhő rendszergazdái számára ismert problémák
A dokumentáció a [Azure Stack kibocsátási megjegyzések](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>További lépések
[További információ az SQL erőforrás-szolgáltató](azure-stack-sql-resource-provider.md).

[Az SQL erőforrás-szolgáltató telepítésének előkészítéséhez](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Az erőforrás-szolgáltató SQL frissítés egy korábbi verziójáról](azure-stack-sql-resource-provider-update.md). 