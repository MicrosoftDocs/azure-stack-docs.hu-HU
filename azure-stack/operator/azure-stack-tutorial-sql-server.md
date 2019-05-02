---
title: SQL-adatbázisok az Azure Stack-felhasználók számára elérhetővé |} A Microsoft Docs
description: Az oktatóanyag az SQL Server erőforrás-szolgáltató telepítéséhez, és hozzon létre kínál, amelyek lehetővé teszik az SQL-adatbázisok létrehozása az Azure Stack felhasználóinak.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
origin.date: 02/12/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: quying
ms.lastreviewed: 11/05/2018
ms.custom: mvc
ms.openlocfilehash: 26ab5b0528f54a351c7168269cb1cfb4b1e13f9e
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64292100"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>Oktatóanyag: SQL-adatbázisok számára elérhetővé az Azure Stack-felhasználók

Azure Stack a felhőalapú rendszergazdák ajánlatok, amelyek lehetővé teszik a felhasználók hozhat létre (bérlők) is használható a natív felhőalkalmazásokat, a websites és a számítási feladatokat az SQL-adatbázisok létrehozása. Az egyéni, igény szerinti, felhőalapú adatbázisok biztosításával a felhasználók számára, mentheti őket időt és erőforrásokat. Állítsa, a következőket fogja végrehajtani:

> [!div class="checklist"]
> * Az SQL Server erőforrás-szolgáltató üzembe helyezése
> * Ajánlat létrehozása
> * Az ajánlat tesztelése

## <a name="deploy-the-sql-server-resource-provider"></a>Az SQL Server erőforrás-szolgáltató üzembe helyezése

A telepítési folyamat részletes leírása a [SQL Database használata az Azure Stack-cikk](azure-stack-sql-resource-provider-deploy.md), és a következő fő lépésből áll:

1. [Az SQL erőforrás-szolgáltató üzembe helyezése](azure-stack-sql-resource-provider-deploy.md).
2. [A telepítés ellenőrzése](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Csatlakozva üzemeltető SQL server-kapacitást biztosítanak. További információkért lásd: [üzemeltető kiszolgálók hozzáadása](azure-stack-sql-resource-provider-hosting-servers.md)

## <a name="create-an-offer"></a>Ajánlat létrehozása

1.  [Kvóták beállítása](azure-stack-plan-offer-quota-overview.md ) , és nevezze el *SQLServerQuota*. Válassza ki **Microsoft.SQLAdapter** számára a **Namespace** mező.
2.  [Hozzon létre egy csomagot](azure-stack-create-plan.md). Nevezze el *TestSQLServerPlan*, jelölje be a **Microsoft.SQLAdapter** szolgáltatást, és **SQLServerQuota** kvótát.

    > [!NOTE]
    > Ahhoz, hogy a felhasználók más alkalmazásokat hozhat létre, a terv más szolgáltatások lehet szükség. Ha például az Azure Functions igényel a **Microsoft.Storage** közben Wordpress igényel a csomag szolgáltatási **Microsoft.MySQLAdapter**.

3.  [Hozzon létre egy ajánlatot](azure-stack-create-offer.md), adja neki **TestSQLServerOffer** , és válassza ki a **TestSQLServerPlan** tervet.

## <a name="test-the-offer"></a>Az ajánlat tesztelése

Most, hogy üzembe helyezte az SQL Server erőforrás-szolgáltató, és létrehozott egy ajánlatot, bejelentkezhet felhasználóként, előfizetnek az ajánlatra, és hozzon létre egy adatbázist.

### <a name="subscribe-to-the-offer"></a>Fizessen elő az ajánlatra

1. Jelentkezzen be az Azure Stack portálon (https://portal.local.azurestack.external) bérlőként.
2. Válassza ki **előfizetés beszerzése** és írja be **TestSQLServerSubscription** alatt **megjelenítendő név**.
3. Válassza ki **ajánlat kiválasztása** > **TestSQLServerOffer** > **létrehozás**.
4. Válassza ki **minden szolgáltatás** > **előfizetések** > **TestSQLServerSubscription** > **erőforrás szolgáltatók**.
5. Válassza ki **regisztrálása** mellett a **Microsoft.SQLAdapter** szolgáltató.

### <a name="create-a-sql-database"></a>SQL-adatbázis létrehozása

1. Válassza ki **+**  >  **adatok + tárolás** > **SQL-adatbázis**.
2. Használja az alapértelmezett értékeket, vagy használjon ezekben a példákban a következő mezőket:
    - **Adatbázis neve**: SQLdb
    - **Maximális méret (MB)**: 100
    - **Előfizetés**: TestSQLOffer
    - **Erőforráscsoport**: SQL-RG
3. Válassza ki **bejelentkezési beállítások**, adja meg az adatbázis hitelesítő adatait, és válassza **OK**.
4. Válassza ki **Termékváltozat** > Válassza ki az SQL-Termékváltozatát üzemeltető SQL Server létrehozott > majd **OK**.
5. Kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az SQL Server erőforrás-szolgáltató üzembe helyezése
> * Ajánlat létrehozása
> * Az ajánlat tesztelése

A következő oktatóanyaggal, amelyben tudatjuk a felhasználókkal hogyan:

> [!div class="nextstepaction"]
> [Webes, mobil és API-alkalmazások elérhetővé tétele a felhasználók számára]( azure-stack-tutorial-app-service.md)

<!-- Update_Description: wording update -->