---
title: Azure Stack hub SQL Resource Provider 1.1.30.0 kibocsátási megjegyzései
titleSuffix: Azure Stack Hub
description: Tekintse meg az Azure Stack hub SQL Resource Provider 1.1.30.0 frissítésének kiadási megjegyzéseit.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 23767e4d9134e27e1aaa27e5309cd4408a6789d4
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77697450"
---
# <a name="sql-resource-provider-11300-release-notes"></a>SQL Resource Provider 1.1.30.0 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések ismertetik az SQL Resource Provider 1.1.30.0 verziójának frissítéseit és ismert problémáit.

## <a name="build-reference"></a>Build referenciája
Töltse le az SQL erőforrás-szolgáltató bináris fájlját, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához. Az erőforrás-szolgáltató minimálisan megfelelő Azure Stack hub-buildtel rendelkezik. Az SQL-erőforrás-szolgáltató ezen verziójának telepítéséhez szükséges minimális Azure Stack hub-verzió az alábbi listában látható:

> |Azure Stack hub minimális verziója|SQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |1808-es verzió (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Az SQL-erőforrás-szolgáltató legújabb verziójának telepítése előtt alkalmazza a minimális támogatott Azure Stack hub-frissítést az Azure Stack hub integrált rendszerére.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások
Az Azure Stack hub SQL erőforrás-szolgáltató ezen verziója a következő javításokat és javításokat tartalmazza:

- **Telemetria engedélyezve az SQL erőforrás-szolgáltató üzembe helyezéséhez**. A telemetria-gyűjtemény engedélyezve van az SQL erőforrás-szolgáltató üzembe helyezéséhez. A gyűjtött telemetria magában foglalja az erőforrás-szolgáltató üzembe helyezését, az indítási és leállítási időt, a kilépési állapotot, a kilépési üzeneteket és a hibák részleteit

- **TLS 1,2 titkosítási frissítés**. Enabled TLS 1,2 – csak az erőforrás-szolgáltató kommunikációjának támogatása belső Azure Stack hub-összetevőkkel. 

### <a name="fixes"></a>Javítások

- **SQL erőforrás-szolgáltató Azure stack hub PowerShell-kompatibilitás**. Az SQL erőforrás-szolgáltató frissítve lett az Azure Stack hub 2018-03-01-Hybrid PowerShell-profillal való együttműködéshez, valamint a AzureRM 1.3.0 és újabb verziókkal való kompatibilitás biztosításához.

- **SQL-bejelentkezési jelszó módosítása**panel. Kijavított egy hibát, ha a jelszó módosítása panelen nem lehet módosítani a jelszót. Eltávolított hivatkozások a jelszó-módosítási értesítésekről.

- Az **SQL üzemeltetési kiszolgáló beállításai panel frissítése**. Kijavítva a hiba, hogy a beállítások panel neve helytelenül "password".

## <a name="known-issues"></a>Ismert problémák

- **Az SQL SKU-ban akár egy órát is igénybe vehet, hogy megjelenjenek a portálon**. Akár egy óráig is eltarthat, amíg az újonnan létrehozott SKU-t új SQL-adatbázisok létrehozásakor használni szeretné.

    **Áthidaló megoldás**: nincs.

- Az **SQL-bejelentkezések újra felhasználva**. Ha egy olyan új SQL-bejelentkezést próbál létrehozni ugyanazzal a felhasználónévvel, amely ugyanazzal az előfizetéssel rendelkezik, akkor ugyanazt a bejelentkezési azonosítót és a meglévő jelszót fogja használni.

    **Megkerülő megoldás**: használjon eltérő felhasználóneveket, amikor új bejelentkezéseket hoz létre ugyanazon előfizetés alatt, vagy hozzon létre bejelentkezési adatokat ugyanazzal a felhasználónévvel különböző előfizetésekben.

- A **megosztott SQL-bejelentkezések adatkövetkezetlenséget okozhatnak**. Ha egy SQL-bejelentkezés ugyanahhoz az előfizetéshez tartozó több SQL-adatbázishoz van megosztva, akkor a bejelentkezési jelszó módosítása az adatok inkonzisztenciát okoz.

    **Megkerülő megoldás**: az azonos előfizetéshez tartozó különböző adatbázisokhoz mindig használjon eltérő bejelentkezéseket.

- **TLS 1,2-támogatásra vonatkozó követelmény**. Ha olyan számítógépről próbálja meg telepíteni vagy frissíteni az SQL-erőforrás-szolgáltatót, ahol a TLS 1,2 nincs engedélyezve, a művelet sikertelen lehet. Futtassa a következő PowerShell-parancsot az erőforrás-szolgáltató üzembe helyezéséhez vagy frissítéséhez használt számítógépen annak ellenőrzéséhez, hogy a TLS 1,2 támogatott-e:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Ha a **Tls12** nem szerepel a parancs kimenetében, a TLS 1,2 nincs engedélyezve a számítógépen.

    **Áthidaló megoldás**: futtassa a következő PowerShell-parancsot a TLS 1,2 engedélyezéséhez, majd indítsa el az erőforrás-szolgáltató üzembe helyezését vagy a parancsfájl frissítését ugyanabból a PowerShell-munkamenetből:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```

- **Az SQL erőforrás-szolgáltató nem tud felvenni SQL Server always on figyelőt**. Ha a figyelő IP-címét használja a SQL Server mindig a figyelőn, az SQL erőforrás-szolgáltató virtuális gép nem tudja feloldani a figyelő gazdagépének nevét.

    **Áthidaló megoldás**: Győződjön meg arról, hogy a DNS megfelelően működik a figyelő IP-címének a figyelő állomásneve általi feloldásához.

### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Ismert problémák a Cloud adminok operációs Azure Stack hubhoz
Tekintse meg az [Azure stack hub kibocsátási megjegyzései](azure-stack-servicing-policy.md)dokumentációját.

## <a name="next-steps"></a>További lépések
[További információ az SQL erőforrás-szolgáltatóról](azure-stack-sql-resource-provider.md).

[Felkészülés az SQL-erőforrás-szolgáltató üzembe helyezésére](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Frissítse az SQL-erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-sql-resource-provider-update.md).