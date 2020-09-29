---
title: Azure Stack hub Azure App Service frissítése
description: Megtudhatja, hogyan frissítheti Azure App Service Azure Stack hub-on.
author: BryanLa
ms.topic: article
ms.date: 05/05/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 6a63ae93488a13b2b3c8f872aadf882a9ccd1a13
ms.sourcegitcommit: 53b0dde60a6435936a5e0cb9e931245f262d637a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91106869"
---
# <a name="update-azure-app-service-on-azure-stack-hub"></a>Azure Stack hub Azure App Service frissítése

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

::: zone pivot="state-connected"
Ebből a cikkből megtudhatja, hogyan frissítheti a [Azure app Service erőforrás-szolgáltatót](azure-stack-app-service-overview.md) az internethez csatlakoztatott Azure stack hub-környezetben.

> [!IMPORTANT]
> A frissítés futtatása előtt el kell végeznie a [Azure app Service telepítését Azure stack hub-on](azure-stack-app-service-deploy.md). 

## <a name="run-the-azure-app-service-resource-provider-installer"></a>A Azure App Service erőforrás-szolgáltató telepítőjének futtatása

A folyamat során a frissítés a következő lesz:

* Azure App Service korábbi üzembe helyezésének észlelése.
* Készítse elő az összes frissítési csomagot és az összes telepítendő OSS-könyvtár új verzióját.
* Feltöltés tárolóba.
* Az összes Azure App Service-szerepkör frissítése (vezérlők, felügyelet, előtér-, közzétevői és feldolgozói szerepkörök).
* Azure App Service méretezési csoport definícióinak frissítése.
* Frissítse Azure App Service erőforrás-szolgáltatói jegyzékfájlt.

> [!IMPORTANT]
> A Azure App Service telepítőjének olyan gépen kell futnia, amely elérheti a Azure Stack hub rendszergazdai Azure Resource Manager végpontját.

Az Azure App Service Azure Stack hub-beli telepítésének frissítéséhez kövesse az alábbi lépéseket:

1. Töltse le a [Azure app Service telepítőjét](https://aka.ms/appsvcupdateQ2installer).

2. appservice.exe futtatása rendszergazdaként.

    ![Képernyőfelvétel: a telepítési vagy frissítési folyamat elindítása a App Service telepítőben.][1]

3. Kattintson **a Azure app Service telepítése vagy a frissítés a legújabb verzióra** lehetőségre.

4. Tekintse át és fogadja el a Microsoft szoftverlicenc-feltételeit, majd kattintson a **tovább**gombra.

5. Tekintse át és fogadja el a harmadik féltől származó licencfeltételeket, majd kattintson a **tovább**gombra.

6. Győződjön meg arról, hogy a Azure Stack hub Azure Resource Manager végpont és a Active Directory bérlő adatai helyesek. Ha a ASDK telepítése során az alapértelmezett beállításokat használta, akkor itt fogadhatja el az alapértelmezett értékeket. Ha azonban a Azure Stack hub telepítésekor testreszabta a beállításokat, akkor ebben az ablakban kell szerkesztenie az értékeket. Ha például a *mycloud.com*tartományi utótagot használja, akkor a Azure Stack hub Azure Resource Manager végpontnak *Management.region.mycloud.com*értékre kell váltania. Az adatok megerősítése után kattintson a **tovább**gombra.

    ![Képernyőfelvétel: az ARM-végpontok konfigurálása a App Service-telepítőben.][2]

7. A következő oldalon:

    1. Válassza ki azt a kapcsolódási módszert, amelyet használni szeretne – **hitelesítő adat** vagy **szolgáltatásnév**
        - **Hitelesítőadat**
            - Ha Azure Active Directoryt (Azure AD) használ, adja meg az Azure AD-rendszergazda fiókját és jelszavát, amelyet az Azure Stack hub telepítésekor adott meg. Kattintson a **Csatlakozás** gombra.
            - Active Directory összevonási szolgáltatások (AD FS) (AD FS) használata esetén adja meg a rendszergazdai fiókját. Például: cloudadmin@azurestack.local. Adja meg a jelszót, majd válassza a **kapcsolat**lehetőséget.
        - **Szolgáltatásnév**
            - A **használt szolgáltatásnév** **tulajdonosi** jogosultságokkal kell rendelkeznie az **alapértelmezett szolgáltatói előfizetéshez** .
            - Adja meg az **egyszerű szolgáltatásnév azonosítóját**, a **tanúsítványfájl**és a **jelszót** , majd válassza a **kapcsolat**lehetőséget.

    1. **Azure stack hub-előfizetések**területen válassza ki az **alapértelmezett szolgáltatói előfizetést**.    A **Azure stack Hub Azure app Service** az **alapértelmezett szolgáltatói előfizetésben**kell központilag telepíteni.

    1. A **Azure stack hub helyein**válassza ki azt a helyet, amely a központilag üzembe helyezett régiónak felel meg. Válassza például a **Local (helyi** ) lehetőséget, ha üzembe helyezi a ASDK.

    1. Ha a rendszer meglévő Azure App Service központi telepítést észlel, akkor az erőforráscsoport és a Storage-fiók fel van töltve, és nem érhető el.

      ![Képernyőfelvétel: Itt adhatja meg a Azure Stack hub előfizetési adatait a App Service telepítőben.][3]

8. Az összefoglalás lapon:
   1. Ellenőrizze az elvégzett beállításokat. A módosítások elvégzéséhez használja az **előző gombokat** az előző lapok megkereséséhez.
   2. Ha a konfigurációk helyesek, jelölje be a jelölőnégyzetet.
   3. A frissítés elindításához kattintson a **tovább**gombra.

       ![A telepítő App Service frissítési összegzését bemutató képernyőkép.][4]

9. Frissítési folyamat lapja:
    1. A frissítési folyamat nyomon követése. Azure Stack hub Azure App Service frissítésének időtartama a telepített szerepkör-példányok számától függően változhat.
    2. A frissítés sikeres befejeződése után kattintson a **Kilépés**gombra.

        ![A App Service telepítőjének telepítési folyamatát bemutató képernyőkép.][5]
::: zone-end

::: zone pivot="state-disconnected"
Ebből a cikkből megtudhatja, hogyan frissítheti a [Azure app Service erőforrás-szolgáltatót](azure-stack-app-service-overview.md) egy olyan Azure stack hub-környezetben, amely a következő:

* nem kapcsolódik az internethez
* Active Directory összevonási szolgáltatások (AD FS) (AD FS) védi.

> [!IMPORTANT]
> A frissítés futtatása előtt el kell végeznie [Azure app Service telepítését a Azure stack hub-ban egy leválasztott környezetben](./azure-stack-app-service-deploy.md?pivots=state-disconnected&view=azs-2002). 

## <a name="run-the-app-service-resource-provider-installer"></a>A App Service erőforrás-szolgáltató telepítőjének futtatása

Ha Azure Stack hub-környezetben szeretné frissíteni a App Service erőforrás-szolgáltatót, el kell végeznie ezeket a feladatokat:

1. Töltse le a [Azure app Service telepítőjét](https://aka.ms/appsvcupdate8installer).
2. Hozzon létre egy offline frissítési csomagot.
3. Futtassa a App Service telepítőt (appservice.exe), és fejezze be a frissítést.

A folyamat során a frissítés a következő lesz:

* App Service korábbi üzembe helyezésének észlelése
* Feltöltés a Storage-ba
* Az összes App Service-szerepkör frissítése (vezérlők, felügyelet, előtér-végpont, közzétevő és feldolgozói szerepkörök)
* App Service méretezési csoport definícióinak frissítése
* App Service erőforrás-szolgáltatói jegyzékfájl frissítése

## <a name="create-an-offline-upgrade-package"></a>Offline frissítési csomag létrehozása

Ha a App Servicet leválasztott környezetben szeretné frissíteni, először létre kell hoznia egy offline frissítési csomagot egy internetkapcsolattal rendelkező számítógépen.

1. appservice.exe futtatása rendszergazdaként

    ![Képernyőkép, amely bemutatja, hogyan indíthatja el a frissítést egy leválasztási környezetben.][11]

2. Kattintson a **speciális**  >  **kapcsolat nélküli csomag létrehozása** elemre.

    ![Képernyőfelvétel: offline csomag létrehozása a App Service-telepítőben.][12]

3. A Azure App Service telepítő létrehoz egy offline frissítési csomagot, és megjeleníti annak elérési útját.  A mappa **megnyitása** lehetőségre kattintva megnyithatja a mappát a fájlkezelőben.

4. Másolja a telepítőt (AppService.exe) és az offline telepítőcsomagot egy olyan gépre, amely kapcsolódik az Azure Stack hubhoz.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack-hub"></a>App Service verziófrissítésének befejezése Azure Stack hub-on

> [!IMPORTANT]
> A Azure App Service-telepítőt olyan gépen kell futtatni, amely elérheti a Azure Stack hub rendszergazdai Azure Resource Manager végpontját.

1. appservice.exe futtatása rendszergazdaként.

    ![A frissítés elindítását bemutató képernyőkép.][11]

2. Kattintson a **speciális**  >  **teljes kapcsolat nélküli telepítés vagy frissítés**lehetőségre.

    ![Képernyőkép a App Service-telepítő offline telepítésének vagy frissítésének módjáról.][12]

3. Keresse meg a korábban létrehozott offline verziófrissítési csomag helyét, majd kattintson a **tovább**gombra.

4. Tekintse át és fogadja el a Microsoft szoftverlicenc-feltételeit, majd kattintson a **tovább**gombra.

5. Tekintse át és fogadja el a harmadik féltől származó licencfeltételeket, majd kattintson a **tovább**gombra.

6. Győződjön meg arról, hogy a Azure Stack hub Azure Resource Manager végpont és Active Directory bérlő adatai helyesek. Ha Azure Stack Development Kit központi telepítés során az alapértelmezett beállításokat használta, akkor itt fogadhatja el az alapértelmezett értékeket. Ha azonban a Azure Stack hub telepítésekor testreszabta a beállításokat, akkor ebben az ablakban kell szerkesztenie az értékeket. Ha például a *mycloud.com*tartományi utótagot használja, akkor a Azure Stack hub Azure Resource Manager végpontnak *Management.region.mycloud.com*értékre kell váltania. Az információk megerősítése után kattintson a **tovább**gombra.

    ![Képernyőfelvétel: az ARM-végpontok konfigurálása a telepítőben.][13]

7. A következő oldalon:

   1. Válassza ki azt a kapcsolódási módszert, amelyet használni szeretne – **hitelesítő adat** vagy **szolgáltatásnév**
        - **Hitelesítőadat**
            - Ha Azure Active Directoryt (Azure AD) használ, adja meg az Azure AD-rendszergazda fiókját és jelszavát, amelyet az Azure Stack hub telepítésekor adott meg. Kattintson a **Csatlakozás** gombra.
            - Active Directory összevonási szolgáltatások (AD FS) (AD FS) használata esetén adja meg a rendszergazdai fiókját. Például: cloudadmin@azurestack.local. Adja meg a jelszót, majd válassza a **kapcsolat**lehetőséget.
        - **Szolgáltatásnév**
            - A **használt szolgáltatásnév** **tulajdonosi** jogosultságokkal kell rendelkeznie az **alapértelmezett szolgáltatói előfizetéshez** .
            - Adja meg az **egyszerű szolgáltatásnév azonosítóját**, a **tanúsítványfájl**és a **jelszót** , majd válassza a **kapcsolat**lehetőséget.

   1. **Azure stack hub-előfizetések**területen válassza ki az **alapértelmezett szolgáltatói előfizetést**.  A **Azure stack Hub Azure app Service** az **alapértelmezett szolgáltatói előfizetésben**kell központilag telepíteni.

   1. A **Azure stack hub helyein**válassza ki azt a helyet, amely a központilag üzembe helyezett régiónak felel meg. Válassza például a **Local (helyi** ) lehetőséget, ha üzembe helyezi a ASDK.
   
   1. Ha a rendszer egy meglévő App Service központi telepítést észlel, akkor az erőforráscsoport és a Storage-fiók ki lesz töltve és szürkén jelenik meg.

      ![Képernyőkép, amely bemutatja, hol kell konfigurálni a Azure Stack hub-előfizetéseket a telepítőben.][14]
8. Az összefoglalás lapon:
   1. Ellenőrizze az elvégzett beállításokat. A módosítások elvégzéséhez használja az **előző gombokat** az előző lapok megkereséséhez.
   2. Ha a konfigurációk helyesek, jelölje be a jelölőnégyzetet.
   3. A frissítés elindításához kattintson a **tovább**gombra.

       ![Képernyőkép, amely a telepítőben gyűjtött információk összegzését jeleníti meg.][15]

9. Frissítési folyamat lapja:
    1. A frissítési folyamat nyomon követése. Az App Service Azure Stack hub-on való frissítésének időtartama a telepített szerepkör-példányok számától függ.
    2. A frissítés sikeres befejeződése után kattintson a **Kilépés**gombra.

        ![A frissítést bemutató képernyőfelvétel sikeresen befejeződött.][16]
::: zone-end

## <a name="next-steps"></a>Következő lépések

Felkészülés további rendszergazdai műveletekre Azure Stack hub Azure App Service:

* [A további kapacitás megtervezése](azure-stack-app-service-capacity-planning.md)
* [További kapacitás hozzáadása](azure-stack-app-service-add-worker-roles.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

[11]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[12]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[13]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[14]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[15]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[16]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png
