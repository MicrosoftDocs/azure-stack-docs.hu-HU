---
title: A MySQL erőforrás-szolgáltató eltávolítása Azure Stack központban
description: Ismerje meg, hogyan távolíthatja el a MySQL erőforrás-szolgáltatót az Azure Stack hub üzembe helyezéséről.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2
ms.openlocfilehash: 03a4f928c5759f72d78ca09816593d70b1dcc76f
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840014"
---
# <a name="remove-the-mysql-resource-provider-in-azure-stack-hub"></a>A MySQL erőforrás-szolgáltató eltávolítása Azure Stack központban

A MySQL erőforrás-szolgáltató eltávolítása előtt el kell távolítania az összes szolgáltatói függőséget. Szüksége lesz az erőforrás-szolgáltató telepítéséhez használt központi telepítési csomag másolatára is.

> [!NOTE]
> Az erőforrás-szolgáltatói telepítők letöltési hivatkozásait az erőforrás- [szolgáltató előfeltételeinek telepítése című](./azure-stack-mysql-resource-provider-deploy.md#prerequisites)cikkben találja.

A MySQL erőforrás-szolgáltató eltávolítása törli az operátor által kezelt társított terveket és kvótákat. De nem törli a bérlői adatbázisokat az üzemeltetési kiszolgálókról.

## <a name="to-remove-the-mysql-resource-provider"></a>A MySQL erőforrás-szolgáltató eltávolítása

1. Győződjön meg arról, hogy eltávolította az összes meglévő MySQL erőforrás-szolgáltató függőségét.

   > [!NOTE]
   > A MySQL erőforrás-szolgáltató eltávolítása akkor is folytatódni fog, ha a függő erőforrások jelenleg az erőforrás-szolgáltatót használják.
  
2. Szerezzen be egy másolatot a MySQL erőforrás-szolgáltató telepítési csomagjáról, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához.
3. Nyisson meg egy új emelt szintű PowerShell-konzolt, és váltson arra a könyvtárra, ahová kicsomagolta a MySQL erőforrás-szolgáltató telepítési fájljait.

> [!IMPORTANT]
> Javasoljuk, hogy a **AzureRmContext-scope CurrentUser** és a **Clear-AzureRmContext-scope folyamat** használatával törölje a gyorsítótárat a parancsfájl futtatása előtt.

4. Futtassa a DeployMySqlProvider.ps1 szkriptet a következő paraméterekkel:
    - **Eltávolítás**: eltávolítja az erőforrás-szolgáltatót és az összes kapcsolódó erőforrást.
    - **PrivilegedEndpoint**: a privilegizált végpont IP-címe vagy DNS-neve.
    - **AzureEnvironment**: Azure stack hub üzembe helyezéséhez használt Azure-környezet. Csak az Azure AD-telepítésekhez szükséges.
    - **CloudAdminCredential**: a rendszerjogosultságú végpont eléréséhez szükséges hitelesítő adatok a felhő rendszergazdájához.
    - **AzCredential**: az Azure stack hub szolgáltatás rendszergazdai fiókjának hitelesítő adatai. Használja ugyanazokat a hitelesítő adatokat, amelyeket az Azure Stack hub üzembe helyezéséhez használt. A szkript sikertelen lesz, ha a AzCredential használt fiók többtényezős hitelesítést (MFA) igényel.

## <a name="next-steps"></a>Következő lépések

[Ajánlati App Services Péterként](azure-stack-app-service-overview.md)
