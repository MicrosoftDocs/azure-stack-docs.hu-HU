---
title: Azure Stack Azure App Service frissítése | Microsoft Docs
description: Útmutató a Azure Stack Azure App Service frissítéséhez.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/28/2019
ms.openlocfilehash: 768a6270021d6a87be3d2d28508288836ffadd29
ms.sourcegitcommit: 7d7a4c8c46613b6104caf23763bfd2275f6a826b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70808261"
---
# <a name="update-azure-app-service-on-azure-stack"></a>Azure App Service frissítése Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

> [!IMPORTANT]
> Alkalmazza a 1904-es frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack Development Kitt (ASDK) a Azure App Service 1,7 üzembe helyezése előtt.

Ebben a cikkben bemutatjuk, hogyan frissítheti a [app Service erőforrás-szolgáltatót](azure-stack-app-service-overview.md) , amelyet egy internetkapcsolattal rendelkező Azure stack környezetben telepített.

> [!IMPORTANT]
> A frissítés futtatása előtt győződjön meg arról, hogy már elvégezte a [Azure app Service telepítését Azure stackon](azure-stack-app-service-deploy.md). Az 1,7-es kiadáshoz tartozó [kibocsátási megjegyzéseket](azure-stack-app-service-release-notes-update-seven.md) is el kell olvasnia, így megismerheti az új funkciókat, javításokat és az üzembe helyezést befolyásoló ismert problémákat.

## <a name="run-the-app-service-resource-provider-installer"></a>A App Service erőforrás-szolgáltató telepítőjének futtatása

A folyamat során a frissítés a következő lesz:

* App Service korábbi üzembe helyezésének észlelése.
* Készítse elő az összes frissítési csomagot és az összes telepítendő OSS-könyvtár új verzióját.
* Feltöltés a Storage-ba.
* Az összes App Service-szerepkör frissítése (vezérlők, felügyelet, előtér-, közzétevői és feldolgozói szerepkörök).
* App Service méretezési csoport definícióinak frissítése.
* Frissítse App Service erőforrás-szolgáltatói jegyzékfájlt.

> [!IMPORTANT]
> A App Service telepítőjének olyan gépen kell futnia, amely elérheti a Azure Stack rendszergazdai Azure Resource Manager végpontját.

Az App Service Azure Stackra történő frissítéséhez kövesse az alábbi lépéseket:

1. Töltse le a [app Service telepítőjét](https://aka.ms/appsvcupdate7installer).

2. Futtassa a appservice. exe fájlt rendszergazdaként.

    ![App Service telepítő][1]

3. Kattintson **a app Service telepítése vagy a frissítés a legújabb verzióra** lehetőségre.

4. Tekintse át és fogadja el a Microsoft szoftverlicenc-feltételeit, majd kattintson a **tovább**gombra.

5. Tekintse át és fogadja el a harmadik féltől származó licencfeltételeket, majd kattintson a **tovább**gombra.

6. Győződjön meg arról, hogy a Azure Stack Azure Resource Manager végpont és a Active Directory bérlő adatai helyesek. Ha a ASDK telepítése során az alapértelmezett beállításokat használta, akkor itt fogadhatja el az alapértelmezett értékeket. Ha azonban a Azure Stack üzembe helyezése során testreszabta a beállításokat, akkor ebben az ablakban kell szerkesztenie az értékeket. Ha például a *mycloud.com*tartományi utótagot használja, akkor a Azure stack Azure Resource Manager végpontnak *Management.region.mycloud.com*értékre kell váltania. Az adatok megerősítése után kattintson a **tovább**gombra.

    ![Azure Stack felhő adatai][2]

7. A következő oldalon:

   1. Kattintson a **Azure stack-előfizetések** mező melletti **Kapcsolódás** gombra.
        * Ha Azure Active Directoryt (Azure AD-t) használ, adja meg a Azure Stack telepítésekor megadott Azure AD-rendszergazdai fiókot és jelszót. Kattintson **a bejelentkezés**elemre.
        * Active Directory összevonási szolgáltatások (AD FS) (AD FS) használata esetén adja meg a rendszergazdai fiókját. Például: *cloudadmin\@azurestack. local*. Adja meg a jelszavát, majd kattintson **a bejelentkezés**elemre.
   2. A **Azure stack-előfizetések** mezőben válassza ki az **alapértelmezett szolgáltatói előfizetést**.
   3. A **Azure stack helyek** mezőben válassza ki azt a helyet, amely a központilag üzembe helyezett régiónak felel meg. Válassza például a **Local (helyi** ) lehetőséget, ha üzembe helyezi a ASDK.
   4. Ha a rendszer meglévő App Service központi telepítést észlel, akkor az erőforráscsoport és a Storage-fiók fel van töltve, és nem érhető el.
   5. A frissítés összegzésének áttekintéséhez kattintson a **tovább** gombra.

      ![App Service telepítés észlelhető][3]

8. Az összefoglalás lapon:
   1. Ellenőrizze az elvégzett beállításokat. A módosítások elvégzéséhez használja az **előző gombokat** az előző lapok megkereséséhez.
   2. Ha a konfigurációk helyesek, jelölje be a jelölőnégyzetet.
   3. A frissítés elindításához kattintson a **tovább**gombra.

       ![App Service frissítés összegzése][4]

9. Frissítési folyamat lapja:
    1. A frissítési folyamat nyomon követése. A App Service Azure Stack frissítésének időtartama a telepített szerepkör-példányok számától függ.
    2. A frissítés sikeres befejeződése után kattintson a **Kilépés**gombra.

        ![App Service frissítési folyamat][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>További lépések

További rendszergazdai műveletek előkészítése a Azure Stack App Serviceához:

* [A további kapacitás megtervezése](azure-stack-app-service-capacity-planning.md)
* [További kapacitás hozzáadása](azure-stack-app-service-add-worker-roles.md)
