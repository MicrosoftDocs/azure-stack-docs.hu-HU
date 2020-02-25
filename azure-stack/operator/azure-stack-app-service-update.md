---
title: Azure Stack hub Azure App Service frissítése
description: Megtudhatja, hogyan frissítheti Azure App Service Azure Stack hub-on.
author: BryanLa
ms.topic: article
ms.date: 01/13/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
ms.openlocfilehash: 07cf0762a87fbf0cea490d246e6e31e003fd7275
ms.sourcegitcommit: a7db4594de43c31fe0c51e60e84fdaf4d41ef1bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2020
ms.locfileid: "77568605"
---
# <a name="update-azure-app-service-on-azure-stack-hub"></a>Azure Stack hub Azure App Service frissítése

> [!IMPORTANT]
> Alkalmazza az 1910-es frissítést az Azure Stack hub integrált rendszerére, vagy telepítse a legújabb Azure Stack Development Kit (ASDK) a Azure App Service 1,8 telepítése előtt.

Ebben a cikkben bemutatjuk, hogyan frissítheti a [Azure app Service erőforrás-szolgáltatót](azure-stack-app-service-overview.md) az internethez csatlakoztatott Azure stack hub-környezetben.

> [!IMPORTANT]
> A frissítés futtatása előtt győződjön meg arról, hogy már elvégezte a [Azure app Service üzembe helyezését Azure stack hub-on](azure-stack-app-service-deploy.md). Az 1,8-es kiadáshoz tartozó [kibocsátási megjegyzéseket](azure-stack-app-service-release-notes-update-eight.md) is el kell olvasnia, így megismerheti az új funkciókat, javításokat és az üzembe helyezést befolyásoló ismert problémákat.

## <a name="run-the-azure-app-service-resource-provider-installer"></a>A Azure App Service erőforrás-szolgáltató telepítőjének futtatása

A folyamat során a frissítés a következő lesz:

* Azure App Service korábbi üzembe helyezésének észlelése.
* Készítse elő az összes frissítési csomagot és az összes telepítendő OSS-könyvtár új verzióját.
* Feltöltés a Storage-ba.
* Az összes Azure App Service-szerepkör frissítése (vezérlők, felügyelet, előtér-, közzétevői és feldolgozói szerepkörök).
* Azure App Service méretezési csoport definícióinak frissítése.
* Frissítse Azure App Service erőforrás-szolgáltatói jegyzékfájlt.

> [!IMPORTANT]
> A Azure App Service telepítőjének olyan gépen kell futnia, amely elérheti a Azure Stack hub rendszergazdai Azure Resource Manager végpontját.

Az Azure App Service Azure Stack hub-beli telepítésének frissítéséhez kövesse az alábbi lépéseket:

1. Töltse le a [Azure app Service telepítőjét](https://aka.ms/appsvcupdate8installer).

2. Futtassa a appservice. exe fájlt rendszergazdaként.

    ![Azure App Service telepítő][1]

3. Kattintson **a Azure app Service telepítése vagy a frissítés a legújabb verzióra** lehetőségre.

4. Tekintse át és fogadja el a Microsoft szoftverlicenc-feltételeit, majd kattintson a **tovább**gombra.

5. Tekintse át és fogadja el a harmadik féltől származó licencfeltételeket, majd kattintson a **tovább**gombra.

6. Győződjön meg arról, hogy a Azure Stack hub Azure Resource Manager végpont és a Active Directory bérlő adatai helyesek. Ha a ASDK telepítése során az alapértelmezett beállításokat használta, akkor itt fogadhatja el az alapértelmezett értékeket. Ha azonban a Azure Stack hub telepítésekor testreszabta a beállításokat, akkor ebben az ablakban kell szerkesztenie az értékeket. Ha például a *mycloud.com*tartományi utótagot használja, akkor a Azure Stack hub Azure Resource Manager végpontnak *Management.region.mycloud.com*értékre kell váltania. Az adatok megerősítése után kattintson a **tovább**gombra.

    ![Azure Stack hub felhő információi][2]

7. A következő oldalon:

    1. Válassza ki azt a kapcsolódási módszert, amelyet használni szeretne – **hitelesítő adat** vagy **szolgáltatásnév**
        - **Hitelesítőadat**
            - Ha Azure Active Directoryt (Azure AD) használ, adja meg az Azure AD-rendszergazda fiókját és jelszavát, amelyet az Azure Stack hub telepítésekor adott meg. Kattintson a **Csatlakozás** gombra.
            - Active Directory összevonási szolgáltatások (AD FS) (AD FS) használata esetén adja meg a rendszergazdai fiókját. Például: cloudadmin@azurestack.local. Adja meg a jelszót, majd válassza a **kapcsolat**lehetőséget.
        - **Egyszerű szolgáltatásnév**
            - A **használt szolgáltatásnév** **tulajdonosi** jogosultságokkal kell rendelkeznie az **alapértelmezett szolgáltatói előfizetéshez** .
            - Adja meg az **egyszerű szolgáltatásnév azonosítóját**, a **tanúsítványfájl** és a **jelszót** , majd válassza a **kapcsolat**lehetőséget.

    1. **Azure stack hub-előfizetések**területen válassza ki az **alapértelmezett szolgáltatói előfizetést**.    A **Azure stack Hub Azure app Service** az **alapértelmezett szolgáltatói előfizetésben**kell központilag telepíteni.

    1. A **Azure stack hub helyein**válassza ki azt a helyet, amely a központilag üzembe helyezett régiónak felel meg. Válassza például a **Local (helyi** ) lehetőséget, ha üzembe helyezi a ASDK.

    1. Ha a rendszer meglévő Azure App Service központi telepítést észlel, akkor az erőforráscsoport és a Storage-fiók fel van töltve, és nem érhető el.

      ![Azure App Service telepítés észlelhető][3]

8. Az összefoglalás lapon:
   1. Ellenőrizze az elvégzett beállításokat. A módosítások elvégzéséhez használja az **előző gombokat** az előző lapok megkereséséhez.
   2. Ha a konfigurációk helyesek, jelölje be a jelölőnégyzetet.
   3. A frissítés elindításához kattintson a **tovább**gombra.

       ![Azure App Service frissítés összegzése][4]

9. Frissítési folyamat lapja:
    1. A frissítési folyamat nyomon követése. Azure Stack hub Azure App Service frissítésének időtartama a telepített szerepkör-példányok számától függően változhat.
    2. A frissítés sikeres befejeződése után kattintson a **Kilépés**gombra.

        ![Azure App Service frissítési folyamat][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Következő lépések

Felkészülés további rendszergazdai műveletekre Azure Stack hub Azure App Service:

* [A további kapacitás megtervezése](azure-stack-app-service-capacity-planning.md)
* [További kapacitás hozzáadása](azure-stack-app-service-add-worker-roles.md)
