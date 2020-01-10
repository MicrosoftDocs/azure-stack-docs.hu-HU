---
title: A MySQL erőforrás-szolgáltató eltávolítása Azure Stack hub-ban | Microsoft Docs
description: Ismerje meg, hogyan távolíthatja el a MySQL erőforrás-szolgáltatót az Azure Stack hub üzembe helyezéséről.
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
ms.openlocfilehash: c1551badd87ba55432ff416aae1230abf317e1b3
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75811039"
---
# <a name="remove-the-mysql-resource-provider-in-azure-stack-hub"></a>A MySQL erőforrás-szolgáltató eltávolítása Azure Stack központban

A MySQL erőforrás-szolgáltató eltávolítása előtt el kell távolítania az összes szolgáltatói függőséget. Szüksége lesz az erőforrás-szolgáltató telepítéséhez használt központi telepítési csomag másolatára is.

> [!NOTE]
> Az erőforrás-szolgáltatói telepítők letöltési hivatkozásait az erőforrás- [szolgáltató előfeltételeinek telepítése című](./azure-stack-mysql-resource-provider-deploy.md#prerequisites)cikkben találja.

A MySQL erőforrás-szolgáltató eltávolítása nem törli a bérlői adatbázisokat az üzemeltetési kiszolgálókról.

## <a name="dependency-cleanup"></a>Függőségek törlése

A DeployMySqlProvider. ps1 parancsfájl futtatása előtt több karbantartási feladatot is el kell végezni az erőforrás-szolgáltató eltávolításához.

A Azure Stack hub operátor felelős a következő karbantartási feladatokért:

* Törölje a MySQL-adapterre hivatkozó terveket.
* Törölje a MySQL-adapterhez társított kvótákat.

## <a name="to-remove-the-mysql-resource-provider"></a>A MySQL erőforrás-szolgáltató eltávolítása

1. Győződjön meg arról, hogy eltávolította az összes meglévő MySQL erőforrás-szolgáltató függőségét.

   > [!NOTE]
   > A MySQL erőforrás-szolgáltató eltávolítása akkor is folytatódni fog, ha a függő erőforrások jelenleg az erőforrás-szolgáltatót használják.
  
2. Szerezzen be egy másolatot a MySQL erőforrás-szolgáltató telepítési csomagjáról, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához.
3. Nyisson meg egy új emelt szintű PowerShell-konzolt, és váltson arra a könyvtárra, ahová kicsomagolta a MySQL erőforrás-szolgáltató telepítési fájljait.
4. Futtassa a DeployMySqlProvider. ps1 parancsfájlt a következő paraméterek használatával:
    - **Eltávolítás**: eltávolítja az erőforrás-szolgáltatót és az összes kapcsolódó erőforrást.
    - **PrivilegedEndpoint**: a privilegizált végpont IP-címe vagy DNS-neve.
    - **AzureEnvironment**: Azure stack hub üzembe helyezéséhez használt Azure-környezet. Csak az Azure AD-telepítésekhez szükséges.
    - **CloudAdminCredential**: a rendszerjogosultságú végpont eléréséhez szükséges hitelesítő adatok a felhő rendszergazdájához.
    - **DirectoryTenantID**
    - **AzCredential**: az Azure stack hub szolgáltatás rendszergazdai fiókjának hitelesítő adatai. Használja ugyanazokat a hitelesítő adatokat, amelyeket az Azure Stack hub üzembe helyezéséhez használt.

## <a name="next-steps"></a>Következő lépések

[Ajánlati App Services Péterként](azure-stack-app-service-overview.md)
