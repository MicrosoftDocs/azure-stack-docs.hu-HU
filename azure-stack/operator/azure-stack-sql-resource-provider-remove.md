---
title: Az SQL-erőforrás szolgáltatójának eltávolítása Azure Stackon | Microsoft Docs
description: Ismerje meg, hogyan távolíthatja el az SQL-erőforrás-szolgáltatót az Azure Stack-telepítésből.
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
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: e753e4bbbf3dbaac8473667a0422a3ad49329bac
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418039"
---
# <a name="remove-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató eltávolítása

Az SQL-erőforrás-szolgáltató eltávolítása előtt el kell távolítania az összes szolgáltatói függőséget. Szüksége lesz az erőforrás-szolgáltató telepítéséhez használt központi telepítési csomag másolatára is.

> [!NOTE]
> Az erőforrás-szolgáltatói telepítők letöltési hivatkozásait az erőforrás- [szolgáltató előfeltételeinek telepítése című](./azure-stack-sql-resource-provider-deploy.md#prerequisites)cikkben találja.

Az SQL-erőforrás-szolgáltató eltávolítása nem törli a bérlői adatbázisokat az üzemeltetési kiszolgálókról.

## <a name="dependency-cleanup"></a>Függőségek törlése

A DeploySqlProvider. ps1 parancsfájl futtatása előtt több karbantartási feladatot is el kell végezni az erőforrás-szolgáltató eltávolításához.

A Azure Stack operátor felelős a következő karbantartási feladatokért:

* Törölje az SQL-adapterre hivatkozó terveket.
* Törölje az SQL-adapterhez társított kvótákat.

## <a name="to-remove-the-sql-resource-provider"></a>Az SQL-erőforrás szolgáltatójának eltávolítása

1. Ellenőrizze, hogy eltávolította-e az összes meglévő SQL Resource Provider-függőséget.

   > [!NOTE]
   > Az SQL-erőforrás-szolgáltató eltávolítása akkor is folytatódni fog, ha a függő erőforrások jelenleg az erőforrás-szolgáltatót használják.
  
2. Szerezzen be egy másolatot az SQL Resource Provider telepítési csomagjáról, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához.

3. Nyisson meg egy új emelt szintű PowerShell-konzolt, és váltson arra a könyvtárra, ahová kicsomagolta az SQL Resource Provider telepítési fájljait.

4. Futtassa a DeploySqlProvider. ps1 parancsfájlt a következő paraméterek használatával:

    * **Eltávolítás**. Eltávolítja az erőforrás-szolgáltatót és az összes kapcsolódó erőforrást.
    * **PrivilegedEndpoint**. Az emelt szintű végpont IP-címe vagy DNS-neve.
    * **AzureEnvironment**. A Azure Stack üzembe helyezéséhez használt Azure-környezet. Csak az Azure AD-telepítésekhez szükséges.
    * **CloudAdminCredential**. A rendszerjogosultságú végpont eléréséhez szükséges hitelesítő adatok a felhő rendszergazdájához.
    * **AzCredential**. A Azure Stack szolgáltatás rendszergazdai fiókjának hitelesítő adatai. Használja ugyanazokat a hitelesítő adatokat, amelyeket a Azure Stack telepítéséhez használt.

## <a name="next-steps"></a>További lépések

[Ajánlati App Services Péterként](azure-stack-app-service-overview.md)
