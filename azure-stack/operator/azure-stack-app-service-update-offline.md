---
title: Frissítési Azure App Service offline | Microsoft Docs
description: Részletes útmutató a Azure Stack offline Azure App Service frissítéséhez
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.openlocfilehash: 5482d078e4edbda10806220c989909424d9fcd95
ms.sourcegitcommit: 701685f0b59e5a3d1a8d39fe477b8df701a51cd2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70159593"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>Azure App Service offline frissítése a Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

> [!IMPORTANT]
> Alkalmazza az 1907-es vagy újabb frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack fejlesztői csomagot a Azure App Service 1,7 üzembe helyezése előtt.

A cikkben található utasításokat követve frissítheti az [app Service erőforrás](azure-stack-app-service-overview.md) -szolgáltatót, amelyet a következő Azure stack-környezetben telepített:

* nem kapcsolódik az internethez
* Active Directory összevonási szolgáltatások (AD FS) (AD FS) védi.

> [!IMPORTANT]
> A frissítés futtatása előtt győződjön meg arról, hogy már elvégezte a [Azure app Service üzembe helyezését Azure stack erőforrás](azure-stack-app-service-deploy-offline.md) -szolgáltatón, és hogy elolvasta a [kibocsátási megjegyzéseket](azure-stack-app-service-release-notes-update-seven.md), amelyek a 1,7-es kiadáshoz tartoznak, hogy megismerjék az új funkciók, javítások és minden olyan ismert probléma, amely hatással lehet az üzemelő példányra.

## <a name="run-the-app-service-resource-provider-installer"></a>A App Service erőforrás-szolgáltató telepítőjének futtatása

Ha Azure Stack környezetben szeretné frissíteni a App Service erőforrás-szolgáltatót, el kell végeznie ezeket a feladatokat:

1. Töltse le a [app Service telepítőjét](https://aka.ms/appsvcupdate7installer).
2. Hozzon létre egy offline frissítési csomagot.
3. Futtassa a App Service telepítőt (appservice. exe), és fejezze be a frissítést.

A folyamat során a frissítés a következő lesz:

* App Service korábbi üzembe helyezésének észlelése
* Feltöltés a Storage-ba
* Az összes App Service-szerepkör frissítése (vezérlők, felügyelet, előtér-végpont, közzétevő és feldolgozói szerepkörök)
* App Service méretezési csoport definícióinak frissítése
* App Service erőforrás-szolgáltatói jegyzékfájl frissítése

## <a name="create-an-offline-upgrade-package"></a>Offline frissítési csomag létrehozása

Ha a App Servicet leválasztott környezetben szeretné frissíteni, először létre kell hoznia egy offline frissítési csomagot egy internetkapcsolattal rendelkező számítógépen.

1. A appservice. exe futtatása rendszergazdaként

    ![App Service telepítő][1]

2. Kattintson a **speciális** > **kapcsolat nélküli csomag létrehozása** elemre.

    ![App Service telepítő speciális][2]

3. A App Service telepítő létrehoz egy offline frissítési csomagot, és megjeleníti annak elérési útját.  A mappa **megnyitása** lehetőségre kattintva megnyithatja a mappát a fájlkezelőben.

4. Másolja a telepítőt (AppService. exe) és az offline frissítési csomagot a Azure Stack gazdagépre.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>App Service frissítésének befejezése Azure Stack

> [!IMPORTANT]
> A App Service telepítőjének olyan gépen kell futnia, amely elérheti a Azure Stack rendszergazdai Azure Resource Manager végpontot.
>
>

1. Futtassa a appservice. exe fájlt rendszergazdaként.

    ![App Service telepítő][1]

2. Kattintson a **speciális** > **teljes kapcsolat nélküli telepítés vagy frissítés**lehetőségre.

    ![App Service telepítő speciális][2]

3. Keresse meg a korábban létrehozott offline verziófrissítési csomag helyét, majd kattintson a **tovább**gombra.

4. Tekintse át és fogadja el a Microsoft szoftverlicenc-feltételeit, majd kattintson a **tovább**gombra.

5. Tekintse át és fogadja el a harmadik féltől származó licencfeltételeket, majd kattintson a **tovább**gombra.

6. Győződjön meg arról, hogy a Azure Stack Azure Resource Manager végpont és Active Directory bérlő adatai helyesek. Ha Azure Stack Development Kit központi telepítés során az alapértelmezett beállításokat használta, akkor itt fogadhatja el az alapértelmezett értékeket. Ha azonban a Azure Stack üzembe helyezése során testreszabta a beállításokat, akkor ebben az ablakban kell szerkesztenie az értékeket. Ha például a *mycloud.com*tartományi utótagot használja, akkor a Azure stack Azure Resource Manager végpontnak *Management.region.mycloud.com*értékre kell váltania. Az információk megerősítése után kattintson a **tovább**gombra.

    ![Azure Stack felhő adatai][3]

7. A következő oldalon:

   1. Kattintson a **Azure stack** -előfizetések mező melletti **Kapcsolódás** gombra.
      * Ha Azure Active Directoryt (Azure AD-t) használ, adja meg a Azure Stack telepítésekor megadott Azure AD-rendszergazdai fiókot és jelszót. Kattintson **a bejelentkezés**elemre.
      * Active Directory összevonási szolgáltatások (AD FS) (AD FS) használata esetén adja meg a rendszergazdai fiókját. Például: _cloudadmin@azurestack.local_ . Adja meg a jelszavát, majd kattintson **a bejelentkezés**elemre.
   2. A **Azure stack** -előfizetések mezőben válassza ki az **alapértelmezett szolgáltatói**előfizetést.
   3. A **Azure stack helyek** mezőben válassza ki azt a helyet, amely a központilag üzembe helyezett régiónak felel meg. Válassza például a **helyi** lehetőséget, ha az üzembe helyezést a Azure stack Development Kit.
   4. Ha a rendszer egy meglévő App Service központi telepítést észlel, akkor az erőforráscsoport és a Storage-fiók ki lesz töltve és szürkén jelenik meg.
   5. A frissítés összegzésének áttekintéséhez kattintson a **tovább** gombra.

      ![App Service telepítés észlelhető][4]

8. Az összefoglalás lapon:
   1. Ellenőrizze az elvégzett beállításokat. A módosítások elvégzéséhez használja az előző gombokat az előző lapok megkereséséhez.
   2. Ha a konfigurációk helyesek, jelölje be a jelölőnégyzetet.
   3. A frissítés elindításához kattintson a **tovább**gombra.

       ![App Service frissítés összegzése][5]

9. Frissítési folyamat lapja:
    1. A frissítési folyamat nyomon követése. A App Service on Azure Stack frissítésének időtartama a telepített szerepkör-példányok számától függ.
    2. A frissítés sikeres befejeződése után kattintson a **Kilépés**gombra.

        ![App Service frissítési folyamat][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>További lépések

Felkészülés a további rendszergazdai műveletekre a App Service on Azure Stack

* [A további kapacitás megtervezése](azure-stack-app-service-capacity-planning.md)
* [További kapacitás hozzáadása](azure-stack-app-service-add-worker-roles.md)
