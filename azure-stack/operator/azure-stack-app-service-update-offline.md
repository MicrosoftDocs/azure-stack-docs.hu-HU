---
title: 'Azure App Service offline frissítése '
description: Részletes útmutató a Azure Stack hub Azure App Service frissítéséhez offline
author: bryanla
ms.topic: article
ms.date: 01/13/2020
ms.author: anwestg
ms.reviewer: anwe
ms.openlocfilehash: 65fb8a0adebb73fa8740f41d50eb83f869ac534a
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700799"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack-hub"></a>Azure App Service offline frissítése Azure Stack hub-on

> [!IMPORTANT]
> Alkalmazza az 1910-es vagy újabb frissítést az Azure Stack hub integrált rendszerére, vagy telepítse a legújabb Azure Stack Development Kitt a Azure App Service 1,8 telepítése előtt.

A cikkben található utasításokat követve a Azure Stack hub-környezetben telepített [Azure app Service erőforrás-szolgáltatót](azure-stack-app-service-overview.md) a következő módon frissítheti:

* nem kapcsolódik az internethez
* Active Directory összevonási szolgáltatások (AD FS) (AD FS) védi.

> [!IMPORTANT]
> A frissítés futtatása előtt győződjön meg arról, hogy már elvégezte a [Azure app Service telepítését Azure stack hub erőforrás-szolgáltatón](azure-stack-app-service-deploy-offline.md) , és hogy elolvasta a [kiadási megjegyzéseket](azure-stack-app-service-release-notes-update-eight.md), amelyek a 1,8-es kiadáshoz tartoznak, hogy megismerje az új funkciókat, javításokat és az üzembe helyezést befolyásoló ismert problémákat.

## <a name="run-the-app-service-resource-provider-installer"></a>A App Service erőforrás-szolgáltató telepítőjének futtatása

Ha Azure Stack hub-környezetben szeretné frissíteni a App Service erőforrás-szolgáltatót, el kell végeznie ezeket a feladatokat:

1. Töltse le a [Azure app Service telepítőjét](https://aka.ms/appsvcupdate8installer).
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

    ![Azure App Service telepítő][1]

2. Kattintson a **speciális** > **Offline csomag létrehozása** elemre.

    ![Azure App Service telepítő speciális][2]

3. A Azure App Service telepítő létrehoz egy offline frissítési csomagot, és megjeleníti annak elérési útját.  A mappa **megnyitása** lehetőségre kattintva megnyithatja a mappát a fájlkezelőben.

4. Másolja a telepítőt (AppService. exe) és az offline frissítési csomagot az Azure Stack hub-gazdagépre.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack-hub"></a>App Service verziófrissítésének befejezése Azure Stack hub-on

> [!IMPORTANT]
> A Azure App Service-telepítőt olyan gépen kell futtatni, amely elérheti a Azure Stack hub rendszergazdai Azure Resource Manager végpontját.

1. Futtassa a appservice. exe fájlt rendszergazdaként.

    ![Azure App Service telepítő][1]

2. Kattintson a **speciális** > **teljes kapcsolat nélküli telepítés vagy frissítés**lehetőségre.

    ![Azure App Service telepítő speciális][2]

3. Keresse meg a korábban létrehozott offline verziófrissítési csomag helyét, majd kattintson a **tovább**gombra.

4. Tekintse át és fogadja el a Microsoft szoftverlicenc-feltételeit, majd kattintson a **tovább**gombra.

5. Tekintse át és fogadja el a harmadik féltől származó licencfeltételeket, majd kattintson a **tovább**gombra.

6. Győződjön meg arról, hogy a Azure Stack hub Azure Resource Manager végpont és Active Directory bérlő adatai helyesek. Ha Azure Stack Development Kit központi telepítés során az alapértelmezett beállításokat használta, akkor itt fogadhatja el az alapértelmezett értékeket. Ha azonban a Azure Stack hub telepítésekor testreszabta a beállításokat, akkor ebben az ablakban kell szerkesztenie az értékeket. Ha például a *mycloud.com*tartományi utótagot használja, akkor a Azure Stack hub Azure Resource Manager végpontnak *Management.region.mycloud.com*értékre kell váltania. Az információk megerősítése után kattintson a **tovább**gombra.

    ![Azure Stack hub felhő információi][3]

7. A következő oldalon:

   1. Válassza ki azt a kapcsolódási módszert, amelyet használni szeretne – **hitelesítő adat** vagy **szolgáltatásnév**
        - **Hitelesítőadat**
            - Ha Azure Active Directoryt (Azure AD) használ, adja meg az Azure AD-rendszergazda fiókját és jelszavát, amelyet az Azure Stack hub telepítésekor adott meg. Kattintson a **Csatlakozás** gombra.
            - Active Directory összevonási szolgáltatások (AD FS) (AD FS) használata esetén adja meg a rendszergazdai fiókját. Például: cloudadmin@azurestack.local. Adja meg a jelszót, majd válassza a **kapcsolat**lehetőséget.
        - **Egyszerű szolgáltatásnév**
            - A **használt szolgáltatásnév** **tulajdonosi** jogosultságokkal kell rendelkeznie az **alapértelmezett szolgáltatói előfizetéshez** .
            - Adja meg az **egyszerű szolgáltatásnév azonosítóját**, a **tanúsítványfájl** és a **jelszót** , majd válassza a **kapcsolat**lehetőséget.

   1. **Azure stack hub-előfizetések**területen válassza ki az **alapértelmezett szolgáltatói előfizetést**.  A **Azure stack Hub Azure app Service** az **alapértelmezett szolgáltatói előfizetésben**kell központilag telepíteni.

   1. A **Azure stack hub helyein**válassza ki azt a helyet, amely a központilag üzembe helyezett régiónak felel meg. Válassza például a **Local (helyi** ) lehetőséget, ha üzembe helyezi a ASDK.
   
   1. Ha a rendszer egy meglévő App Service központi telepítést észlel, akkor az erőforráscsoport és a Storage-fiók ki lesz töltve és szürkén jelenik meg.

      ![Azure App Service telepítés észlelhető][4]
8. Az összefoglalás lapon:
   1. Ellenőrizze az elvégzett beállításokat. A módosítások elvégzéséhez használja az **előző gombokat** az előző lapok megkereséséhez.
   2. Ha a konfigurációk helyesek, jelölje be a jelölőnégyzetet.
   3. A frissítés elindításához kattintson a **tovább**gombra.

       ![Azure App Service frissítés összegzése][5]

9. Frissítési folyamat lapja:
    1. A frissítési folyamat nyomon követése. Az App Service Azure Stack hub-on való frissítésének időtartama a telepített szerepkör-példányok számától függ.
    2. A frissítés sikeres befejeződése után kattintson a **Kilépés**gombra.

        ![Azure App Service frissítési folyamat][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Következő lépések

További rendszergazdai műveletek előkészítése Azure Stack hub Azure App Service

* [A további kapacitás megtervezése](azure-stack-app-service-capacity-planning.md)
* [További kapacitás hozzáadása](azure-stack-app-service-add-worker-roles.md)
