---
title: Az Azure Stack SQL resource provider 1.1.30.0 kibocsátási megjegyzései |} A Microsoft Docs
description: Ismerje meg, mi az a legújabb Azure Stack SQL resource provider frissítések, beleértve az olyan ismert problémákat és helyét, ahonnan letöltheti azt.
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
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 22525fc9109bd2c03e28c4ca34b60c7a1f2585fe
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618053"
---
# <a name="sql-resource-provider-11300-release-notes"></a>SQL erőforrás-szolgáltató 1.1.30.0 kibocsátási megjegyzései

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A kibocsátási megjegyzések a javításokat és a SQL-erőforrás-szolgáltató verziója 1.1.30.0 ismert problémákat ismertetik.

## <a name="build-reference"></a>Hivatkozás létrehozása
Töltse le az SQL erőforrás-szolgáltató bináris, és futtassa a mappába, csomagolja ki a tartalmát egy ideiglenes könyvtárba. Az erőforrás-szolgáltató rendelkezik egy minimális megfelelő Azure Stack hozhat létre. Ezen verziója az SQL erőforrás-szolgáltató telepítéséhez szükséges minimális Azure Stack-verzió alább:

> |Azure Stack minimális verziója|Erőforrás-szolgáltató az SQL verziója|
> |-----|-----|
> |Verzió 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> A minimális támogatott az Azure Stack-frissítés alkalmazása az Azure Stackkel integrált rendszereknél, vagy üzembe helyezése a legújabb Azure Stack Development Kit (ASDK) az erőforrás-szolgáltató az SQL legújabb verziójának telepítése előtt.

## <a name="new-features-and-fixes"></a>Új funkciókkal és javításokkal
Az Azure Stack SQL erőforrás-szolgáltató ezen verziója tartalmazza a következő fejlesztések és javítások:

- **Engedélyezve van az erőforrás-szolgáltató telepítések SQL telemetriai**. Eszköztelemetria-gyűjtést SQL erőforrás-szolgáltató telepítések engedélyezve van. Gyűjtött telemetriai tartalmazza az erőforrás-szolgáltató üzembe helyezési, indítsa el záró időpontját, lépjen ki a állapot, a kilépési üzenetet és a hiba részletei (ha van).

- **A TLS 1.2-es titkosítási frissítés**. Engedélyezve a TLS 1.2-es csak támogatja a belső Azure Stack-összetevők erőforrás-szolgáltató kommunikál. 

### <a name="fixes"></a>Javítások

- **SQL-erőforrás-szolgáltató az Azure Stack PowerShell kompatibilitási**. Az SQL erőforrás-szolgáltató frissítve lett az Azure Stack 2018-03-01-hibrid PowerShell-profillal, és kompatibilitást biztosítanak AzureRM 1.3.0 a munkahelyi és a későbbi.

- **Az SQL bejelentkezési Módosítsa jelszavát panel**. Javítva lett egy probléma, ahol a jelszó nem lehet módosítani a jelszó módosítása panelen. Jelszó eltávolított hivatkozások fájlmódosítási értesítések.

- **Az SQL futtató kiszolgálót a beállítások panelen frissítése**. Javítva lett egy probléma, ahol a beállítások panelen helytelenül címe, a "Password".

## <a name="known-issues"></a>Ismert problémák 

- **SQL-termékváltozatok órát is igénybe vehet egy lesznek láthatók a portálon**. Is igénybe vehet egy órát újonnan létrehozott termékváltozatok lesznek láthatók a használatra, amikor új SQL-adatbázisok létrehozásához. 

    **Megkerülő megoldás**: Nincs.

- **Fel újra az SQL-bejelentkezésekben**. Próbál létrehozni egy új SQL jelentkezzen be a felhasználónevet, egy előfizetésen belül egy meglévő bejelentkezést eredményez szakember újból felhasználja a meglévő jelszót, és ugyanazokat a bejelentkezési adatokat. 

    **Megkerülő megoldás**: Azonos előfizetéshez tartozó új bejelentkezéseket létrehozásakor különböző felhasználónevet használ, vagy hozzon létre bejelentkezések a különböző előfizetéseknél felhasználónevet.

- **Megosztott SQL-bejelentkezésekben adatinkonzisztenciát okozhat**. Ha az egy előfizetésen belül több SQL-adatbázis egy SQL-bejelentkezési megosztott, a bejelentkezési jelszó módosítása adatinkonzisztenciát okoz.

    **Megkerülő megoldás**: Mindig használjon különböző bejelentkezések különböző adatbázisokat ugyanahhoz az előfizetéshez.

- **A TLS 1.2 támogatási követelmények**. Ha megpróbálja telepíthet vagy frissíthet az SQL erőforrás-szolgáltató egy olyan számítógépről, amelyen nincs engedélyezve a TLS 1.2-es, a művelet sikertelen lehet. Futtassa a következő PowerShell-parancsot a számítógépen használt telepíteni vagy frissíteni az erőforrás-szolgáltatót, győződjön meg arról, hogy a TLS 1.2 támogatott adja vissza:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Ha **Tls12** van a parancs kimenete nem szerepel, a TLS 1.2 nincs engedélyezve a számítógépen.

    **Megkerülő megoldás**: Futtassa a következő PowerShell-parancsot a TLS 1.2-es és indítsa el az erőforrás-szolgáltató üzembe helyezés, vagy frissítse a parancsfájlt a ugyanebben a PowerShell-munkamenetben:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
- **SQL típusú erőforrás-szolgáltató nem sikerül hozzáadni az SQL Server Always On figyelő**. Az SQL Server Always figyelője figyelő IP-címének használata esetén az SQL-erőforrás-szolgáltató virtuális gép nem oldható fel a figyelő gazdagép nevét.

    **Megkerülő megoldás**: Győződjön meg arról, hogy DNS megfelelően működik, és oldja fel a figyelő IP-címet figyelő gazdagép nevét.
    
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Azure Stack működő felhő rendszergazdái számára ismert problémák
A dokumentáció a [Azure Stack kibocsátási megjegyzések](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>További lépések
[További információ az SQL erőforrás-szolgáltató](azure-stack-sql-resource-provider.md).

[Az SQL erőforrás-szolgáltató telepítésének előkészítéséhez](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Az erőforrás-szolgáltató SQL frissítés egy korábbi verziójáról](azure-stack-sql-resource-provider-update.md). 