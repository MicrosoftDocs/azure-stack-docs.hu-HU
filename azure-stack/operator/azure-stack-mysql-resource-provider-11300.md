---
title: Azure Stack hub MySQL erőforrás-szolgáltató 1.1.30.0 kibocsátási megjegyzései | Microsoft Docs
description: Tekintse meg a kibocsátási megjegyzéseket, és tekintse meg az Azure Stack hub MySQL erőforrás-szolgáltató 1.1.30.0 frissítésének újdonságait.
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
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 21fdc832203e276960168e2ac15b4e552439090a
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76535008"
---
# <a name="mysql-resource-provider-11300-release-notes"></a>MySQL erőforrás-szolgáltató 1.1.30.0 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések ismertetik a MySQL erőforrás-szolgáltató 1.1.30.0-verziójának újdonságait és ismert problémáit.

## <a name="build-reference"></a>Build referenciája
Töltse le a MySQL erőforrás-szolgáltató bináris fájlját, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához. Az erőforrás-szolgáltató minimálisan megfelelő Azure Stack hub-buildtel rendelkezik. A MySQL erőforrás-szolgáltató ezen verziójának telepítéséhez szükséges minimális Azure Stack hub kiadási verzió az alábbi listában látható:

> |Azure Stack hub minimális verziója|MySQL erőforrás-szolgáltató verziója|
> |-----|-----|
> |Azure Stack hub 1808 frissítés (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Alkalmazza a minimális támogatott Azure Stack hub-frissítést a Azure Stack hub integrált rendszerére, vagy telepítse a legújabb Azure Stack Development Kitt (ASDK) a MySQL erőforrás-szolgáltató legújabb verziójának üzembe helyezése előtt.

## <a name="new-features-and-fixes"></a>Új funkciók és javítások
Az Azure Stack hub MySQL erőforrás-szolgáltató ezen verziója a következő javításokat és javításokat tartalmazza:

- **A telemetria engedélyezve van a MySQL erőforrás-szolgáltató üzembe helyezéséhez**. A telemetria-gyűjtemény engedélyezve lett a MySQL erőforrás-szolgáltató üzembe helyezéséhez. A gyűjtött telemetria magában foglalja az erőforrás-szolgáltató üzembe helyezését, az indítási és leállítási időt, a kilépési állapotot, a kilépési üzeneteket és a hibák részleteit

- **TLS 1,2 titkosítási frissítés**. Enabled TLS 1,2 – csak az erőforrás-szolgáltató kommunikációjának támogatása belső Azure Stack hub-összetevőkkel. 

### <a name="fixes"></a>Javítások

- **MySQL erőforrás-szolgáltató Azure stack hub PowerShell-kompatibilitás**. A MySQL erőforrás-szolgáltató frissítve lett az Azure Stack hub 2018-03-01-Hybrid PowerShell-profillal való együttműködéshez, valamint a AzureRM 1.3.0 és újabb verziókkal való kompatibilitás biztosításához.

- **MySQL bejelentkezési jelszó módosítása**panel. Kijavított egy hibát, ha a jelszó módosítása panelen nem lehet módosítani a jelszót. Eltávolított hivatkozások a jelszó-módosítási értesítésekről.

## <a name="known-issues"></a>Ismert problémák

- **A MySQL SKU-ban akár egy órát is igénybe vehet, hogy megjelenjenek a portálon**. Akár egy órát is igénybe vehet, hogy az újonnan létrehozott SKU-i megjelenjenek az új MySQL-adatbázisok létrehozásakor.

    **Megkerülő megoldás**: Nincs.

- A **MySQL-bejelentkezések újrafelhasználása**. Az azonos előfizetéshez tartozó meglévő bejelentkezéssel azonos felhasználónévvel rendelkező új MySQL-bejelentkezés létrehozásakor a rendszer ugyanazt a bejelentkezési azonosítót és a meglévő jelszót fogja használni.

    **Megkerülő megoldás**: használjon eltérő felhasználóneveket, amikor új bejelentkezéseket hoz létre ugyanazon előfizetés alatt, vagy hozzon létre bejelentkezési adatokat ugyanazzal a felhasználónévvel különböző előfizetésekben.

- **TLS 1,2-támogatásra vonatkozó követelmény**. Ha olyan számítógépről próbálja meg telepíteni vagy frissíteni a MySQL erőforrás-szolgáltatót, ahol a TLS 1,2 nincs engedélyezve, a művelet sikertelen lehet. Futtassa a következő PowerShell-parancsot az erőforrás-szolgáltató üzembe helyezéséhez vagy frissítéséhez használt számítógépen annak ellenőrzéséhez, hogy a TLS 1,2 támogatott-e:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Ha a **Tls12** nem szerepel a parancs kimenetében, a TLS 1,2 nincs engedélyezve a számítógépen.

    **Áthidaló megoldás**: futtassa a következő PowerShell-parancsot a TLS 1,2 engedélyezéséhez, majd indítsa el az erőforrás-szolgáltató üzembe helyezését vagy a parancsfájl frissítését ugyanabból a PowerShell-munkamenetből:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
 
### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Ismert problémák a Cloud adminok operációs Azure Stack hubhoz
Tekintse meg az [Azure stack hub kibocsátási megjegyzései](azure-stack-servicing-policy.md)dokumentációját.

## <a name="next-steps"></a>Következő lépések
[További információ a MySQL erőforrás-szolgáltatóról](azure-stack-mysql-resource-provider.md).

[Felkészülés a MySQL erőforrás-szolgáltató üzembe helyezésére](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Frissítse a MySQL erőforrás-szolgáltatót egy korábbi verzióról](azure-stack-mysql-resource-provider-update.md). 