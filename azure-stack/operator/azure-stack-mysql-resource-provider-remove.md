---
title: A MySQL erőforrás-szolgáltató eltávolítása a Azure Stack-ben | Microsoft Docs
description: Ismerje meg, hogyan távolíthatja el a MySQL erőforrás-szolgáltatót a Azure Stack üzemelő példányáról.
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
ms.openlocfilehash: a2b55707bc05aecf8681cb866c58b0ed34fb87cd
ms.sourcegitcommit: a23b80b57668615c341c370b70d0a106a37a02da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72682163"
---
# <a name="remove-the-mysql-resource-provider-in-azure-stack"></a>A MySQL erőforrás-szolgáltató eltávolítása Azure Stack

A MySQL erőforrás-szolgáltató eltávolítása előtt el kell távolítania az összes szolgáltatói függőséget. Szüksége lesz az erőforrás-szolgáltató telepítéséhez használt központi telepítési csomag másolatára is.

> [!NOTE]
> Az erőforrás-szolgáltatói telepítők letöltési hivatkozásait az erőforrás- [szolgáltató előfeltételeinek telepítése című](./azure-stack-mysql-resource-provider-deploy.md#prerequisites)cikkben találja.

A MySQL erőforrás-szolgáltató eltávolítása nem törli a bérlői adatbázisokat az üzemeltetési kiszolgálókról.

## <a name="dependency-cleanup"></a>Függőségek törlése

A DeployMySqlProvider. ps1 parancsfájl futtatása előtt több karbantartási feladatot is el kell végezni az erőforrás-szolgáltató eltávolításához.

A Azure Stack operátor felelős a következő karbantartási feladatokért:

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
    - **AzureEnvironment**: a Azure stack üzembe helyezéséhez használt Azure-környezet. Csak az Azure AD-telepítésekhez szükséges.
    - **CloudAdminCredential**: a rendszerjogosultságú végpont eléréséhez szükséges hitelesítő adatok a felhő rendszergazdájához.
    - **DirectoryTenantID**
    - **AzCredential**: a Azure stack szolgáltatás rendszergazdai fiókjának hitelesítő adatai. Használja ugyanazokat a hitelesítő adatokat, amelyeket a Azure Stack telepítéséhez használt.

## <a name="next-steps"></a>Következő lépések

[Ajánlati App Services Péterként](azure-stack-app-service-overview.md)
