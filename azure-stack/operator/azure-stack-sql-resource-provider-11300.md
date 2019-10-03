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
ms.openlocfilehash: f17c2ba41097d5b9bda903ae5d95c62e0ac9f53a
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829361"
---
# <a name="sql-resource-provider-11300-release-notes"></a>SQL erőforrás-szolgáltató 1.1.30.0 kibocsátási megjegyzései

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ezek a kibocsátási megjegyzések ismertetik az SQL Resource Provider 1.1.30.0 verziójának frissítéseit és ismert problémáit.

## <a name="build-reference"></a>Hivatkozás létrehozása
Töltse le az SQL erőforrás-szolgáltató bináris fájlját, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához. Az erőforrás-szolgáltató minimálisan megfelelő Azure Stack buildtel rendelkezik. Az SQL-erőforrás-szolgáltató ezen verziójának telepítéséhez szükséges minimális Azure Stack kiadási verzió az alábbi listában látható:

> |Minimális Azure Stack-verzió|SQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |1808-es verzió (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Alkalmazza a minimális támogatott Azure Stack frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack Development Kitt (ASDK) az SQL Resource Provider legújabb verziójának telepítése előtt.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások
A Azure Stack SQL erőforrás-szolgáltató ezen verziója a következő javításokat és javításokat tartalmazza:

- **Telemetria engedélyezve az SQL erőforrás-szolgáltató üzembe helyezéséhez**. A telemetria-gyűjtemény engedélyezve van az SQL erőforrás-szolgáltató üzembe helyezéséhez. A gyűjtött telemetria magában foglalja az erőforrás-szolgáltató üzembe helyezését, az indítási és leállítási időt, a kilépési állapotot, a kilépési üzeneteket és a hibák részleteit

- **TLS 1,2 titkosítási frissítés**. Enabled TLS 1,2 – csak az erőforrás-szolgáltató kommunikációjának támogatása belső Azure Stack-összetevőkkel. 

### <a name="fixes"></a>Javítások

- Az **SQL erőforrás-szolgáltató Azure stack PowerShell-kompatibilitást**. Az SQL erőforrás-szolgáltató frissítve lett a Azure Stack 2018-03-01 hibrid PowerShell-profillal való együttműködéshez, valamint a AzureRM 1.3.0 és újabb verziókkal való kompatibilitás biztosításához.

- **SQL-bejelentkezési jelszó módosítása**panel. Kijavított egy hibát, ha a jelszó módosítása panelen nem lehet módosítani a jelszót. Eltávolított hivatkozások a jelszó-módosítási értesítésekről.

- Az **SQL üzemeltetési kiszolgáló beállításai panel frissítése**. Kijavítva a hiba, hogy a beállítások panel neve helytelenül "password".

## <a name="known-issues"></a>Ismert problémák 

- **Az SQL SKU-ban akár egy órát is igénybe vehet, hogy megjelenjenek a portálon**. Akár egy óráig is eltarthat, amíg az újonnan létrehozott SKU-t új SQL-adatbázisok létrehozásakor használni szeretné. 

    **Áthidaló megoldás**: Nincs.

- Az **SQL-bejelentkezések újra felhasználva**. Ha egy olyan új SQL-bejelentkezést próbál létrehozni ugyanazzal a felhasználónévvel, amely ugyanazzal az előfizetéssel rendelkezik, akkor ugyanazt a bejelentkezési azonosítót és a meglévő jelszót fogja használni. 

    **Áthidaló megoldás**: Használjon más felhasználóneveket, amikor új bejelentkezéseket hoz létre ugyanazon előfizetés alatt, vagy hozzon létre bejelentkezési adatokat ugyanazzal a felhasználónévvel különböző előfizetésekben.

- A **megosztott SQL-bejelentkezések adatkövetkezetlenséget okozhatnak**. Ha egy SQL-bejelentkezés ugyanahhoz az előfizetéshez tartozó több SQL-adatbázishoz van megosztva, akkor a bejelentkezési jelszó módosítása az adatok inkonzisztenciát okoz.

    **Áthidaló megoldás**: Mindig használjon eltérő bejelentkezési adatokat a különböző adatbázisokhoz ugyanazon előfizetés alatt.

- **TLS 1,2-támogatásra vonatkozó követelmény**. Ha olyan számítógépről kísérli meg az SQL-erőforrás-szolgáltató üzembe helyezését vagy frissítését, ahol a TLS 1,2 nincs engedélyezve, a művelet sikertelen lehet. Futtassa a következő PowerShell-parancsot az erőforrás-szolgáltató üzembe helyezéséhez vagy frissítéséhez használt számítógépen annak ellenőrzéséhez, hogy a TLS 1,2 támogatott-e:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Ha a **Tls12** nem szerepel a parancs kimenetében, a TLS 1,2 nincs engedélyezve a számítógépen.

    **Áthidaló megoldás**: Futtassa a következő PowerShell-parancsot a TLS 1,2 engedélyezéséhez, majd indítsa el az erőforrás-szolgáltató üzembe helyezését vagy a parancsfájl frissítését ugyanabból a PowerShell-munkamenetből:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
- **Az SQL erőforrás-szolgáltató nem tud felvenni SQL Server always on figyelőt**. Ha a figyelő IP-címét használja a SQL Server mindig a figyelőn, az SQL-erőforrás szolgáltatójának virtuális gépe nem tudja feloldani a figyelő gazdagépének nevét.

    **Áthidaló megoldás**: Győződjön meg arról, hogy a DNS megfelelően működik-e a figyelő IP-címének a figyelő állomásneve általi feloldásához.
    
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>A Cloud adminok operációs Azure Stack ismert problémái
Tekintse át a [Azure stack kibocsátási megjegyzések](azure-stack-servicing-policy.md)dokumentációját.

## <a name="next-steps"></a>További lépések
[További információ az SQL erőforrás-szolgáltatóról](azure-stack-sql-resource-provider.md).

[Felkészülés az SQL-erőforrás-szolgáltató üzembe helyezésére](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Frissítse az SQL-erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-sql-resource-provider-update.md). 