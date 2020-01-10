---
title: Az SQL erőforrás-szolgáltató eltávolítása
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan távolíthatja el az SQL-erőforrás-szolgáltatót az Azure Stack hub üzembe helyezéséről.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: 15178627cbc58cd466a09f7ef534e7ec115d9c1a
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75814405"
---
# <a name="remove-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató eltávolítása

Az SQL-erőforrás-szolgáltató eltávolítása előtt el kell távolítania az összes szolgáltatói függőséget. Szüksége lesz az erőforrás-szolgáltató telepítéséhez használt központi telepítési csomag másolatára is.

> [!NOTE]
> Az erőforrás-szolgáltatói telepítők letöltési hivatkozásait az erőforrás- [szolgáltató előfeltételeinek telepítése című](./azure-stack-sql-resource-provider-deploy.md#prerequisites)cikkben találja.

Az SQL-erőforrás-szolgáltató eltávolítása nem törli a bérlői adatbázisokat az üzemeltetési kiszolgálókról.

## <a name="dependency-cleanup"></a>Függőségek törlése

A DeploySqlProvider. ps1 parancsfájl futtatása előtt több karbantartási feladatot is el kell végezni az erőforrás-szolgáltató eltávolításához.

A Azure Stack hub operátor felelős a következő karbantartási feladatokért:

* Törölje az SQL-adapterre hivatkozó terveket.
* Törölje az SQL-adapterhez társított kvótákat.

## <a name="to-remove-the-sql-resource-provider"></a>Az SQL-erőforrás szolgáltatójának eltávolítása

1. Ellenőrizze, hogy eltávolította-e az összes meglévő SQL Resource Provider-függőséget.

   > [!NOTE]
   > Az SQL-erőforrás-szolgáltató eltávolítása akkor is folytatódni fog, ha a függő erőforrások jelenleg az erőforrás-szolgáltatót használják.
  
2. Szerezzen be egy másolatot az SQL Resource Provider telepítési csomagjáról, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához.

3. Nyisson meg egy új emelt szintű PowerShell-konzolt, és váltson arra a könyvtárra, ahová kicsomagolta az SQL Resource Provider telepítési fájljait.

4. Futtassa a DeploySqlProvider. ps1 parancsfájlt a következő paraméterek használatával:

    * **Eltávolítás**: eltávolítja az erőforrás-szolgáltatót és az összes kapcsolódó erőforrást.
    * **PrivilegedEndpoint**: a privilegizált végpont IP-címe vagy DNS-neve.
    * **AzureEnvironment**: Azure stack hub üzembe helyezéséhez használt Azure-környezet. Csak az Azure AD-telepítésekhez szükséges.
    * **CloudAdminCredential**: a rendszerjogosultságú végpont eléréséhez szükséges hitelesítő adatok a felhő rendszergazdájának.
    * **AzCredential**: az Azure stack hub szolgáltatás rendszergazdai fiókjának hitelesítő adatai. Használja ugyanazokat a hitelesítő adatokat, amelyeket az Azure Stack hub üzembe helyezéséhez használt.

## <a name="next-steps"></a>Következő lépések

[Ajánlati App Services Péterként](azure-stack-app-service-overview.md)
