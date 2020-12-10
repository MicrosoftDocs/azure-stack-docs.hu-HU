---
title: Az SQL erőforrás-szolgáltató eltávolítása
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan távolíthatja el az SQL-erőforrás-szolgáltatót az Azure Stack hub üzembe helyezéséről.
author: bryanla
ms.topic: article
ms.date: 10/02/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2019
ms.openlocfilehash: b232bf2efb690ddc0a351d762e6866a75ced4a81
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97011365"
---
# <a name="remove-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató eltávolítása

Az SQL-erőforrás-szolgáltató eltávolítása előtt el kell távolítania az összes szolgáltatói függőséget. Szüksége lesz az erőforrás-szolgáltató telepítéséhez használt központi telepítési csomag másolatára is.

> [!NOTE]
> Az erőforrás-szolgáltatói telepítők letöltési hivatkozásait az erőforrás- [szolgáltató előfeltételeinek telepítése című](./azure-stack-sql-resource-provider-deploy.md#prerequisites)cikkben találja.

Az SQL-erőforrás-szolgáltató eltávolítása törli az operátor által kezelt társított terveket és kvótákat. De nem törli a bérlői adatbázisokat az üzemeltetési kiszolgálókról.

## <a name="to-remove-the-sql-resource-provider"></a>Az SQL-erőforrás szolgáltatójának eltávolítása

1. Ellenőrizze, hogy eltávolította-e az összes meglévő SQL Resource Provider-függőséget.

   > [!NOTE]
   > Az SQL-erőforrás-szolgáltató eltávolítása akkor is folytatódni fog, ha a függő erőforrások jelenleg az erőforrás-szolgáltatót használják.
  
2. Szerezzen be egy másolatot az SQL Resource Provider telepítési csomagjáról, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához.

3. Nyisson meg egy új emelt szintű PowerShell-konzolt, és váltson arra a könyvtárra, ahová kicsomagolta az SQL Resource Provider telepítési fájljait.

4. Futtassa a DeploySqlProvider.ps1 szkriptet a következő paraméterekkel:

    * **Eltávolítás**: eltávolítja az erőforrás-szolgáltatót és az összes kapcsolódó erőforrást.
    * **PrivilegedEndpoint**: a privilegizált végpont IP-címe vagy DNS-neve.
    * **AzureEnvironment**: Azure stack hub üzembe helyezéséhez használt Azure-környezet. Csak az Azure AD-telepítésekhez szükséges.
    * **CloudAdminCredential**: a rendszerjogosultságú végpont eléréséhez szükséges hitelesítő adatok a felhő rendszergazdájának.
    * **AzCredential**: az Azure stack hub szolgáltatás rendszergazdai fiókjának hitelesítő adatai. Használja ugyanazokat a hitelesítő adatokat, amelyeket az Azure Stack hub üzembe helyezéséhez használt. A szkript sikertelen lesz, ha a AzCredential használt fiók többtényezős hitelesítést (MFA) igényel.

## <a name="next-steps"></a>Következő lépések

[Ajánlati App Services Péterként](azure-stack-app-service-overview.md)
