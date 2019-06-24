---
title: Az Azure Stacken SQL erőforrás-szolgáltató eltávolítása |} A Microsoft Docs
description: Ismerje meg, hogyan, hogy az SQL erőforrás-szolgáltató eltávolítása az Azure Stack üzemelő példányához.
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
ms.date: 06/13/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: 1f943daaebd368230fdc0257bbf719bef959dfb5
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308393"
---
# <a name="remove-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató eltávolítása

Az SQL erőforrás-szolgáltató eltávolítása, előtt el kell távolítania a szolgáltató függőségeket. Emellett szüksége lesz egy példányát a központi telepítési csomag, amely az erőforrás-szolgáltató telepítéséhez használt.

> [!NOTE]
> A letöltési hivatkozásokat talál az erőforrás a szolgáltató telepítők [üzembe helyezése az erőforrás-szolgáltatóra vonatkozó Előfeltételek](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Az SQL erőforrás-szolgáltató eltávolítása nem törli bérlői adatbázisokat üzemeltető kiszolgálók.

## <a name="dependency-cleanup"></a>Függőségi karbantartása

Nincsenek ehhez az erőforrás-szolgáltató eltávolítása a DeploySqlProvider.ps1 parancsfájl futtatása előtt több karbantartási feladatot.

Az Azure Stack – operátor felelős az alábbi karbantartási feladatokat:

* Törölje azokat a csomagokat, amelyek az SQL-Adapter hivatkoznak.
* Törölje az SQL-Adapter társított kvóták.

## <a name="to-remove-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató eltávolítása

1. Győződjön meg arról, hogy eltávolította a meglévő SQL resource provider függőségeket.

   > [!NOTE]
   > Az SQL erőforrás-szolgáltató eltávolítása akkor is, ha a tőle függő erőforrások jelenleg használja az erőforrás-szolgáltató folytatódik.
  
2. Az SQL erőforrás-szolgáltató telepítési csomag le, és futtassa a mappába, csomagolja ki a tartalmát egy ideiglenes könyvtárba.

3. Nyisson meg egy új emelt szintű PowerShell-konzolablakot, és váltson arra a könyvtárra, amelyben kibontotta az SQL resource provider telepítőfájljainak.

4. Futtassa a DeploySqlProvider.ps1 parancsfájlt a következő paraméterekkel:

    * **Távolítsa el**. Eltávolítja az erőforrás-szolgáltató és az összes társított erőforrást.
    * **PrivilegedEndpoint**. Az IP-cím vagy a kiemelt végponthoz DNS-nevét.
    * **AzureEnvironment**. Az Azure-környezethez az Azure Stack üzembe helyezéséhez használt. Kizárólag az Azure AD központi telepítések esetén szükséges.
    * **CloudAdminCredential**. A felhő rendszergazdájához, a kiemelt végponthoz eléréséhez szükséges hitelesítő adatait.
    * **AzCredential**. Az Azure Stack szolgáltatás rendszergazdai fiók hitelesítő adatait. Használja az Azure Stack üzembe helyezéséhez használt hitelesítő adatokkal.

## <a name="next-steps"></a>További lépések

[Alkalmazás-szolgáltatások ajánlása PaaS-ként](azure-stack-app-service-overview.md)
