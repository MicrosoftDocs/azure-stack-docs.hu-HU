---
title: Web- és API-alkalmazások felhasználók számára elérhetővé az Azure Stack |} A Microsoft Docs
description: Az oktatóanyag az App Service erőforrás-szolgáltató telepítéséhez, és hozzon létre kínál, amelyek lehetővé teszik az Azure Stack felhasználói web- és API-alkalmazások létrehozásához.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: justinha
ms.reviewer: anwestg
ms.custom: mvc
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: fbf6993447f22a354c61102b9e670c8f1188f48d
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268091"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>Oktatóanyag: web- és API-alkalmazások elérhetővé tétele az Azure Stack-felhasználók számára

Azure Stack a felhőalapú rendszergazdák ajánlatok, amelyek lehetővé teszik a felhasználók hozhat létre (bérlők) Azure Functions és a webes és API-alkalmazások létrehozása. Így a felhasználók hozzáférést az igény szerinti, a felhőalapú alkalmazások, mentheti őket időt és erőforrásokat.

Állítsa, a következőket fogja végrehajtani:

> [!div class="checklist"]
> * Az App Service erőforrás-szolgáltató üzembe helyezése
> * Ajánlat létrehozása
> * Az ajánlat tesztelése

## <a name="deploy-the-app-service-resource-provider"></a>Az App Service erőforrás-szolgáltató üzembe helyezése

1. [Készítse elő az Azure Stack Development Kit állomás](azure-stack-app-service-before-you-get-started.md). Ez magában foglalja az SQL Server erőforrás-szolgáltató, amely szükséges az egyes alkalmazások létrehozásával központi telepítése.
2. [Töltse le a telepítő és a segítő szkripteket](azure-stack-app-service-deploy.md).
3. [A segítő parancsprogrammal hozzon létre a szükséges tanúsítványok](azure-stack-app-service-deploy.md).
4. [Az App Service erőforrás-szolgáltató telepítése](azure-stack-app-service-deploy.md) (telepítése néhány órát fog igénybe venni, és minden a feldolgozói szerepköröket jelennek meg.)
5. [A telepítés ellenőrzése](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Ajánlat létrehozása

Tegyük fel ajánlat, amellyel a felhasználók létrehozása DNN webes tartalomkezelő rendszerek is létrehozhat. Az SQL Server szolgáltatás, amely telepíti az SQL Server erőforrás-szolgáltató már engedélyezve van szükség.

1.  [Kvóták beállítása](azure-stack-plan-offer-quota-overview.md ) , és nevezze el *AppServiceQuota*. Válassza ki **Microsoft.Web** számára a **Namespace** mező.
2.  [Hozzon létre egy csomagot](azure-stack-create-plan.md). Nevezze el *TestAppServicePlan*, jelölje be a **Microsoft.SQL** szolgáltatás és a **az App Service-kvóta** kvótát.

    > [!NOTE]
    > Ahhoz, hogy a felhasználók más alkalmazásokat hozhat létre, a terv más szolgáltatások lehet szükség. Ha például az Azure Functions igényel a **Microsoft.Storage** közben Wordpress igényel a csomag szolgáltatási **Microsoft.MySQL**.

3.  [Hozzon létre egy ajánlatot](azure-stack-create-offer.md), adja neki **TestAppServiceOffer** , és válassza ki a **TestAppServicePlan** tervet.

## <a name="test-the-offer"></a>Az ajánlat tesztelése

Most, hogy üzembe helyezte az App Service erőforrás-szolgáltató, és létrehozott egy ajánlatot, bejelentkezhet felhasználóként, előfizetnek az ajánlatra, és hozzon létre egy alkalmazást.

Ebben a példában létrehozunk egy DNN Platform tartalomkezelő rendszer. Először hozzon létre egy SQL-adatbázist, majd a DNN-webalkalmazást.

### <a name="subscribe-to-the-offer"></a>Fizessen elő az ajánlatra

1. Jelentkezzen be az Azure Stack portálon (https://portal.local.azurestack.external) bérlőként.
2. Válassza ki **előfizetés beszerzése** >, adja meg **TestAppServiceSubscription** alatt **megjelenítendő név** > **ajánlat kiválasztása**  >  **TestAppServiceOffer** > **létrehozása**.

### <a name="create-a-sql-database"></a>SQL-adatbázis létrehozása

1. Válassza ki **+**  >  **adatok + tárolás** > **SQL-adatbázis**.
2. Használja az alapértelmezett értékeket, kivéve a következő mezőket:

    - **Adatbázis neve**: DNNdb
    - **Maximális méret (MB)** : 100
    - **Előfizetés**: TestAppServiceOffer
    - **Erőforráscsoport**: DNN-RG

3. Válassza ki **bejelentkezési beállítások**, adja meg az adatbázis hitelesítő adatait, és válassza **OK**. Ezeket a hitelesítő adatokat az oktatóanyag későbbi részében fogja használni.
4. A **Termékváltozat** > Válassza ki az SQL-Termékváltozatát üzemeltető SQL Server létrehozott > majd **OK**.
5. Kattintson a **Létrehozás** gombra.

### <a name="create-a-dnn-app"></a>A DNN-alkalmazás létrehozása

1. Válassza ki **+**  >  **összes** > **DNN Platform előzetes** > **létrehozás** .
2. Adja meg *DNNapp* alatt **alkalmazásnév** válassza **TestAppServiceOffer** alatt **előfizetés**.
3. Válassza ki **kötelező beállítások konfigurálása** > **hozzon létre új** > meg egy **App Service-csomag** nevét.
4. Válassza ki **tarifacsomag** > **F1 – ingyenes** > **kiválasztása** > **OK**.
5. Válassza ki **adatbázis** adja meg a hitelesítő adatait az SQL database a korábban létrehozott.
6. Kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az App Service erőforrás-szolgáltató üzembe helyezése
> * Ajánlat létrehozása
> * Az ajánlat tesztelése

A következő oktatóanyaggal, amelyben tudatjuk a felhasználókkal hogyan:

> [!div class="nextstepaction"]
> [Alkalmazások üzembe helyezése az Azure és az Azure Stackben](../user/azure-stack-solution-pipeline.md)
