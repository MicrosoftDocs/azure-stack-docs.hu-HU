---
title: Az Azure App Service Offline frissítése |} A Microsoft Docs
description: Részletes útmutató az Azure App Service az Azure Stack kapcsolat nélküli frissítése
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
ms.date: 05/28/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.openlocfilehash: 2bbab1870c06e32889187f676022dab410aaf08c
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269074"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>Az Azure App Service az Azure Stacken offline frissítés

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!IMPORTANT]
> 1904 frissítést a vagy később, az Azure Stackkel integrált rendszer, vagy a legújabb Azure Stack fejlesztői készletének telepítése az Azure App Service 1.6-os üzembe helyezése előtt.

Ez a cikk utasításait követve frissíthet a [App Service erőforrás-szolgáltató](azure-stack-app-service-overview.md) üzembe helyezve az Azure Stack-környezet, amely:

* nem csatlakozik az internethez
* az Active Directory összevonási szolgáltatások (AD FS) védi.

> [!IMPORTANT]
> A frissítés futtatása előtt győződjön meg arról, hogy már végrehajtotta a [központi telepítését az Azure App Service az Azure Stack Resource Provider](azure-stack-app-service-deploy-offline.md) és, hogy elolvasta a [kibocsátási megjegyzések](azure-stack-app-service-release-notes-update-six.md), amely kísérő az 1.6-os verzióra kiadás, új funkciókat, javításokat és olyan ismert problémákat, amelyek hatással lehetnek a központi telepítés megismeréséhez.

## <a name="run-the-app-service-resource-provider-installer"></a>Az App Service-ben resource provider telepítőjének futtatása

Az App Service erőforrás-szolgáltató az Azure Stack-környezet frissítése, ezeket a feladatokat kell elvégeznie:

1. Töltse le a [az App Service-telepítő](https://aka.ms/appsvcupdate6installer)
2. Hozzon létre egy offline frissítési csomagot.
3. Futtassa a telepítőt az App Service-ben (appservice.exe), és a frissítés befejezéséhez.

Ez a folyamat során a frissítés tartalma:

* Az App Service előzetes központi telepítés észlelése
* A Storage feltöltése
* Minden App Service-szerepkör frissítése (vezérlők, előtér-kezelés, kiadó és feldolgozó szerepkörök)
* Az App Service méretezésicsoport-definícióinak frissítése
* Az App Service erőforrás-szolgáltatói jegyzékének frissítése

## <a name="create-an-offline-upgrade-package"></a>Frissítési offline csomag létrehozása

Leválasztott környezet frissítése az App Service-ben, akkor először létre kell hoznia offline frissítési csomag olyan számítógépen, amelyen az internethez csatlakozik.

1. Appservice.exe futtassa rendszergazdaként

    ![Az App Service-telepítő][1]

2. Kattintson a **speciális** > **offline csomag létrehozása**

    ![App Service Installer Advanced][2]

3. Az App Service-telepítő létrehoz egy offline frissítési csomagot, és elérési útja.  Kattinthat **mappa megnyitása** a mappa megnyitása a Fájlkezelőben.

4. Másolja a telepítőt (AppService.exe) és az offline csomagot az Azure Stack gazdagépen.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>Végezze el a frissítést az App Service az Azure Stackben

> [!IMPORTANT]
> Az App Service-telepítő a számítógépen, amely elérheti az Azure Stack rendszergazdai Azure Resource Manager-végpontot kell futtatni.
>
>

1. Appservice.exe futtassa rendszergazdaként.

    ![Az App Service-telepítő][1]

2. Kattintson a **speciális** > **hajtsa végre a kapcsolat nélküli telepítési vagy frissítési**.

    ![App Service Installer Advanced][2]

3. Keresse meg a korábban létrehozott offline frissítési csomag a helyet, és kattintson a **tovább**.

4. Tekintse át és fogadja el a Microsoft szoftverlicenc-feltételeket, majd kattintson **tovább**.

5. Tekintse át és fogadja el a külső licencfeltételeket, majd kattintson **tovább**.

6. Győződjön meg arról, hogy az Azure Stack az Azure Resource Manager-végpontot, és az Active Directory-bérlő adatok helyességéről. Az alapértelmezett beállításokat használta az Azure Stack fejlesztői készletének üzembe helyezés során, ha elfogadhatja az alapértelmezett értékeket itt. Azonban ha testre szabta a beállításokat, az Azure Stack üzembe helyezésekor, szerkesztenie kell az értékek, ebben az ablakban. Ha például a tartományi utótag használata *mycloud.com*, az Azure Stack az Azure Resource Manager-végpontot kell módosítania *management.region.mycloud.com*. Miután meggyőződött a adatait, kattintson a **tovább**.

    ![Azure Stack Cloud Information][3]

7. A következő oldalon:

   1. Kattintson a **Connect** megjelenítő gombra a **Azure Stack-előfizetést** mezőbe.
      * Ha az Azure Active Directory (Azure AD) használja, adja meg az Azure AD-Rendszergazdafiók és az Azure Stack üzembe helyezésekor megadott jelszót. Kattintson a **jelentkezzen be a**.
      * Ha az Active Directory összevonási szolgáltatások (AD FS) használ, adja meg a rendszergazdai fiókjával. Például: _cloudadmin@azurestack.local_ . Adja meg a jelszót, és kattintson a **bejelentkezés**.
   2. Az a **Azure Stack-előfizetést** jelölje ki a **szolgáltatói előfizetés alapértelmezett**.
   3. Az a **Azure Stack-helyek** válassza ki a helyet, amely megfelel a régió, helyezi üzembe. Válassza ki például **helyi** Ha az az Azure Stack fejlesztői készletének telepítése.
   4. Ha meglévő App Service-környezet észlel, majd az erőforrás és a tárfiókja fog használni feltöltve, szürkén jelenik meg.
   5. Kattintson a **tovább** , tekintse át a frissítési összefoglalót.

      ![Az App Service-telepítést észlelt][4]

8. Az összefoglalás lapon:
   1. Ellenőrizze a kiválasztott beállítások. Módosításához használja a **előző** gombok használatával keresse fel az előző lapokra.
   2. Ha a konfiguráció helyes, jelölje be a jelölőnégyzetet.
   3. A frissítés indításához kattintson **tovább**.

       ![App Service-ben a frissítési összefoglalót][5]

9. Folyamat lap frissítése:
    1. Nyomon követheti a frissítési folyamat állapotát. Az App Service az Azure Stacken frissítésének időtartamára változik üzembe helyezett szerepkörpéldányt függ.
    2. Miután a frissítés sikeresen befejeződött, kattintson a **kilépési**.

        ![App Service-ben a frissítési folyamat állapota][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>További lépések

Is kipróbálhatja más [platform platformszolgáltatási (PaaS) szolgáltatásokra](azure-stack-offer-services-overview.md).

* [Az SQL Server erőforrás-szolgáltató](azure-stack-sql-resource-provider-deploy.md)
* [MySQL típusú erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md)
