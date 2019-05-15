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
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: e5126f55a60b98ec7c410b7f8f2256ad08fa9753
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618563"
---
# <a name="mysql-resource-provider-11300--release-notes"></a>MySQL erőforrás-szolgáltató 1.1.30.0 kibocsátási megjegyzései

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A kibocsátási megjegyzések a fejlesztéseket és a MySQL erőforrás-szolgáltató verziója 1.1.30.0 az ismert problémákat ismertetik.

## <a name="build-reference"></a>Hivatkozás létrehozása
Töltse le a MySQL erőforrás-szolgáltató bináris, és futtassa a mappába, csomagolja ki a tartalmát egy ideiglenes könyvtárba. Az erőforrás-szolgáltató rendelkezik egy minimális megfelelő Azure Stack hozhat létre. Ez a verzió, a MySQL erőforrás-szolgáltató telepítéséhez szükséges minimális Azure Stack-verzió alább:

> |Azure Stack minimális verziója|MySQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |Azure Stack 1808 frissítése (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> A minimális támogatott az Azure Stack-frissítés alkalmazása az Azure Stackkel integrált rendszereknél, vagy a legújabb Azure Stack Development Kit (ASDK) telepíteni a MySQL erőforrás-szolgáltató legújabb verziójának telepítése előtt.

## <a name="new-features-and-fixes"></a>Új funkciókkal és javításokkal
Az Azure Stack MySQL erőforrás-szolgáltató ezen verziója tartalmazza a következő fejlesztések és javítások:

- **MySQL erőforrás-szolgáltató telepítések engedélyezve telemetriai**. Eszköztelemetria-gyűjtést MySQL erőforrás-szolgáltató telepítések engedélyezve van. Gyűjtött telemetriai tartalmazza az erőforrás-szolgáltató üzembe helyezési, indítsa el záró időpontját, lépjen ki a állapot, a kilépési üzenetet és a hiba részletei (ha van).

- **A TLS 1.2-es titkosítási frissítés**. Engedélyezve a TLS 1.2-es csak támogatja a belső Azure Stack-összetevők erőforrás-szolgáltató kommunikál. 

### <a name="fixes"></a>Javítások

- **MySQL erőforrás-szolgáltató az Azure Stack PowerShell kompatibilitási**. A MySQL erőforrás-szolgáltató frissítve lett az Azure Stack 2018-03-01-hibrid PowerShell-profillal, és kompatibilitást biztosítanak AzureRM 1.3.0 a munkahelyi és a későbbi.

- **MySQL bejelentkezési Módosítsa jelszavát panel**. Javítva lett egy probléma, ahol a jelszó nem lehet módosítani a jelszó módosítása panelen. Jelszó eltávolított hivatkozások fájlmódosítási értesítések.

## <a name="known-issues"></a>Ismert problémák 

- **MySQL termékváltozatot órát is igénybe vehet egy lesznek láthatók a portálon**. Is igénybe vehet egy órát újonnan létrehozott termékváltozatok lesznek láthatók a használatra, amikor új MySQL-adatbázisok létrehozásához. 

    **Megkerülő megoldás**: Nincs.

- **Újra felhasználhatók a MySQL-bejelentkezések**. Hozzon létre egy új MySQL próbál jelentkezzen be a felhasználónevet, egy meglévő bejelentkezést egy előfizetésen belül eredményez újbóli felhasználása ugyanazokat a bejelentkezési adatokat és a meglévő jelszót. 

    **Megkerülő megoldás**: Azonos előfizetéshez tartozó új bejelentkezéseket létrehozásakor különböző felhasználónevet használ, vagy hozzon létre bejelentkezések a különböző előfizetéseknél felhasználónevet.

- **A TLS 1.2 támogatási követelmények**. Ha megpróbálja telepíteni vagy frissíteni a MySQL erőforrás-szolgáltató egy olyan számítógépről, amelyen nincs engedélyezve a TLS 1.2-es, a művelet sikertelen lehet. Futtassa a következő PowerShell-parancsot a számítógépen használt telepíteni vagy frissíteni az erőforrás-szolgáltatót, győződjön meg arról, hogy a TLS 1.2 támogatott adja vissza:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Ha **Tls12** van a parancs kimenete nem szerepel, a TLS 1.2 nincs engedélyezve a számítógépen.

    **Megkerülő megoldás**: Futtassa a következő PowerShell-parancsot a TLS 1.2-es és indítsa el az erőforrás-szolgáltató üzembe helyezés, vagy frissítse a parancsfájlt a ugyanebben a PowerShell-munkamenetben:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
 
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Azure Stack működő felhő rendszergazdái számára ismert problémák
A dokumentáció a [Azure Stack kibocsátási megjegyzések](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>További lépések
[További információ a MySQL erőforrás-szolgáltató](azure-stack-mysql-resource-provider.md).

[A MySQL erőforrás-szolgáltató telepítésének előkészítéséhez](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[A MySQL erőforrás-szolgáltató frissítés egy korábbi verziójáról](azure-stack-mysql-resource-provider-update.md). 