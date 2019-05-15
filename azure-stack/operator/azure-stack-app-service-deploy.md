---
title: 'Helyezze üzembe az App Services: Azure Stack | Microsoft Docs'
description: Részletes útmutatást az Azure Stack App Service üzembe helyezése
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 6db643e1123a27fe1716aeeb5ec97d6497764632
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618959"
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Az App Service erőforrás-szolgáltató hozzáadása az Azure Stackhez

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ez a cikk az útmutató segítségével üzembe helyezése az Azure Stack App Service-ben.

> [!IMPORTANT]  
> Az Azure Stackkel integrált rendszereknél 1901 frissítés alkalmazása, vagy a legújabb Azure Stack Development Kit (ASDK) üzembe helyezése, Azure App Service 1.5 telepítése előtt.

A felhasználók számára biztosíthat web- és API-alkalmazások létrehozása. Ahhoz, hogy a felhasználók ezeket az alkalmazásokat hozzanak létre, meg kell:

 - Adja hozzá a [App Service erőforrás-szolgáltató](azure-stack-app-service-overview.md) az Azure Stack üzemelő példány ebben a cikkben ismertetett lépések.
 - Miután telepítette az App Service erőforrás-szolgáltató, megadhatja az ajánlatok és csomagok. Felhasználók majd előfizethetnek az szolgáltatásba és az alkalmazások létrehozásának első lépései.

> [!IMPORTANT]  
> A resource provider telepítőjének futtatása, előtt győződjön meg arról, hogy követte az útmutató [használatának megkezdése előtt](azure-stack-app-service-before-you-get-started.md) rendelkezik-e olvasási és a [kibocsátási megjegyzések](azure-stack-app-service-release-notes-update-five.md), amely az 1.5-ös kiadás kapcsolatos új kísérő Funkciók, javításokat, és olyan ismert problémákat, amelyek hatással lehetnek a központi telepítés.

## <a name="run-the-app-service-resource-provider-installer"></a>Az App Service-ben resource provider telepítőjének futtatása

Az App Service erőforrás-szolgáltató telepítése szükséges legalább egy órát. Szükséges idő hossza attól függ, hogy hány szerepkör példányai, üzembe helyezése. A telepítő a telepítés során fut, a következő feladatokat:

- Hozzon létre egy blobtárolót a megadott Azure Stack-tárfiókban.
- DNS-zóna és -bejegyzések létrehozása App Service-hez.
- Az App Service erőforrás-szolgáltató regisztrálásához.
- Az App Service-katalóguselemek regisztrálása.

  > [!IMPORTANT]
  > Az erőforrás-szolgáltató üzembe helyezése előtt tekintse át a kibocsátási megjegyzéseket, új funkciókat, javításokat és olyan ismert problémákat, amelyek hatással lehetnek a központi telepítés megismeréséhez.

Az App Service erőforrás-szolgáltató üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Appservice.exe futtassa rendszergazdaként egy olyan számítógépről, amely hozzáférhet az Azure Stack rendszergazdai Azure erőforrás-kezelési végpontot.

2. Válassza ki **App Service üzembe helyezése és frissítése a legújabb verzióra**.

    ![Az App Service-telepítő][1]

3. Tekintse át és fogadja el a Microsoft szoftverlicenc-feltételeket, majd **tovább**.

4. Tekintse át és fogadja el a külső licencfeltételeket, majd **tovább**.

5. Győződjön meg arról, hogy helyesen szerepel-e az App Service-ben felhőbeli konfigurációs adatait. Ha az alapértelmezett beállításokat használta az Azure Stack Development Kit (ASDK) üzembe helyezése során, elfogadhatja az alapértelmezett értékeket. De ha testre szabta a beállításokat a ASDK telepítve, vagy az Azure Stackkel integrált rendszereknél végzi, szerkesztenie kell a ebben az ablakban, hogy a különbségeket az értékeket.

   Például ha a tartományi utótag mycloud.com használja, az Azure Stack bérlő Azure Resource Manager-végpont módosítania kell a felügyeleti. &lt;régió&gt;. mycloud.com. Tekintse át ezeket a beállításokat, és válassza ki **tovább** gombra a beállítások mentéséhez.

   ![Az App Service-telepítő][2]

6. A következő App Service-telepítő oldalon tegye a következőket:

    a. Válassza ki **Connect** mellett a **Azure Stack-előfizetést**.

   - Ha az Azure Active Directory (Azure AD) használja, adja meg az Azure AD-Rendszergazdafiók és az Azure Stack üzembe helyezésekor megadott jelszót. Válassza ki **jelentkezzen be a**.
   - Ha az Active Directory összevonási szolgáltatások (AD FS) használ, adja meg a rendszergazdai fiókjával. Például: cloudadmin@azurestack.local. Adja meg a jelszót, és válassza ki **bejelentkezés**.

   b. A **Azure Stack-előfizetést**, jelölje be a **szolgáltatói előfizetés alapértelmezett**.

     > [!IMPORTANT]  
     > App Service-ben **kell** üzembe helyezhető a **szolgáltatói előfizetés alapértelmezett**.

   c. Az a **Azure Stack-helyek**, válassza ki a helyet, amely megfelel a régió, helyezi üzembe. Válassza ki például **helyi** Ha az az Azure Stack fejlesztői készletének telepítése.

    ![Az App Service-telepítő][3]

7. Most üzembe helyezhető egy meglévő virtuális hálózatot konfigurált [az alábbi lépések végrehajtásával](azure-stack-app-service-before-you-get-started.md#virtual-network), vagy lehetővé teszik a új virtuális hálózat és alhálózatok létrehozása App Service-telepítő. Hozzon létre egy Vnetet, kövesse az alábbi lépéseket:

   a. Válassza ki **virtuális hálózat létrehozása az alapértelmezett beállításokkal**, fogadja el az alapértelmezett beállításokat, és válassza **tovább**.

   b. Jelölje ki **használja a meglévő virtuális hálózat és alhálózatok**. Hajtsa végre a következő műveleteket:

     - Válassza ki a **erőforráscsoport** , amely tartalmazza a virtuális hálózat.
     - Válassza ki a **virtuális hálózat** telepíteni kívánt nevet.
     - Válassza ki a megfelelő **alhálózati** értékek mind a szükséges szerepkör-alhálózatokat.
     - Kattintson a **Tovább** gombra.

   ![Az App Service-telepítő][4]

8. Adja meg a fájlmegosztás az adatokat, majd **tovább**. A cím a fájlmegosztás a teljesen minősített tartomány nevét (FQDN), vagy a fájlkiszolgáló IP-címét kell használnia. Ha például \\\appservicefileserver.local.cloudapp.azurestack.external\websites, vagy \\\10.0.0.1\websites.  Ha használ egy fájlkiszolgálón, amely tartományhoz van csatlakoztatva, meg kell adnia a teljes felhasználónevet, többek között a tartományhoz, például myfileserverdomain\FileShareOwner.

   >[!NOTE]
   >A telepítő próbál csatlakozni a fájlmegosztáshoz, a folytatás előtt. De ha egy meglévő virtuális hálózatra telepíti, a kapcsolódási teszt sikertelen lehet. Felhőszolgáltatására, figyelmeztetés és a egy parancssort a folytatáshoz. Ha a fájlmegosztási adatok helyesek, továbbra is az üzembe helyezés.

   ![Az App Service-telepítő][7]

9. A következő App Service-telepítő oldalon tegye a következőket:

   a. Az a **identitás Alkalmazásazonosítója** adja meg a globálisan egyedi Azonosítót (az Azure AD) identitás használja az alkalmazást.

   b. Az a **identitás alkalmazástanúsítvány-fájlja** mezőben adja meg (vagy keresse meg a) a tanúsítvány-fájl helyét.

   c. Az a **identitás alkalmazástanúsítványának jelszava** mezőbe írja be a jelszót a tanúsítványhoz. Ez a jelszó pedig végzett jegyezze fel, ha a parancsfájl a tanúsítványok létrehozásához használt.

   d. Az a **Azure Resource Manager-főtanúsítványfájl** mezőben adja meg (vagy keresse meg a) a tanúsítvány-fájl helyét.

   e. Kattintson a **Tovább** gombra.

   ![Az App Service-telepítő][9]

10. Válassza a három tanúsítványsablon a fájl mezők mindegyike esetében **Tallózás** , és keresse meg a megfelelő tanúsítványfájlt. Meg kell adnia a jelszavát minden egyes tanúsítvány. Ezek a tanúsítványok lépnek, amelyet a [létrehozása szükséges tanúsítványok lépésben](azure-stack-app-service-before-you-get-started.md#get-certificates). Válassza ki **tovább** összes információ megadása után.

    | Box | Tanúsítvány-fájl neve például |
    | --- | --- |
    | **Az App Service alapértelmezett SSL-tanúsítványfájlja** | \_.appservice.local.AzureStack.external.pfx |
    | **Az App Service API SSL-tanúsítványfájlja** | api.appservice.local.AzureStack.external.pfx |
    | **App Service-közzétevő SSL-tanúsítványfájlja** | ftp.appservice.local.AzureStack.external.pfx |

    Ha a különböző tartománynak az utótagját, a tanúsítványok létrehozásakor, a tanúsítvány fájlnevek használja, nem *helyi. AzureStack.external*. Ehelyett használja az egyéni tartomány adatait.

    ![Az App Service-telepítő][10]

11. Adja meg, hogy az SQL Server, az App Service erőforrás-szolgáltató adatbázisainak üzemeltetésére, és válassza ki a kiszolgálópéldánynak **tovább**. A telepítő ellenőrzi az SQL-kapcsolat tulajdonságai.<br><br>Az App Service-ben telepítő megpróbálja az SQL Server, a folytatás előtt a kapcsolat teszteléséhez. Ha egy meglévő virtuális hálózatra telepíti, a kapcsolódási teszt sikertelen lehet. Felhőszolgáltatására, figyelmeztetés és a egy parancssort a folytatáshoz. Ha az SQL Server-adatok helyesek, továbbra is az üzembe helyezés.

    ![Az App Service-telepítő][11]

12. Tekintse át a szerepkörpéldányhoz, és a Termékváltozat-beállításokat. Az alapértelmezett értéket adja meg a minimális példányok számát és a minimális Termékváltozat-ASDK telepítés minden egyes szerepkörhöz. VCPU-és memória összegzést érdekében az üzembe helyezésének megtervezése. A kiválasztás után válassza ki a **tovább**.

    >[!NOTE]
    >Éles környezetekben üzemelő példányok található útmutatást [kapacitás tervezése az Azure App Service-ben kiszolgálói szerepkörök az Azure Stackben](azure-stack-app-service-capacity-planning.md).

    | Szerepkör | Minimális példányok | Minimális Termékváltozat | Megjegyzések |
    | --- | --- | --- | --- |
    | Vezérlő | 1. | A Standard_A2 - (2 vCPU, 3584 MB) | Kezeli, és fenntartja az App Service-felhő állapotát. |
    | Kezelés | 1. | A Standard_A2 - (2 vcpu-k, 3584 MB) | Az App Service az Azure Resource Manager és az API-végpontok, portál extensions (felügyeleti, a bérlő, a Functions portálon.) és az adatok szolgáltatás kezeli. A feladatátvétel támogatásához a javasolt példányok 2-re nőtt. |
    | Gyártó | 1. | A Standard_A1 - (1 vCPU, 1792 MB) | Tesz közzé az FTP és a webes üzembe a tartalomhoz. |
    | Előtér | 1. | A Standard_A1 - (1 vCPU, 1792 MB) | Az App Service-alkalmazások felé irányítja a kérelmeket. |
    | Megosztott feldolgozói | 1. | A Standard_A1 - (1 vCPU, 1792 MB) | Gazdagépek webalkalmazás vagy API-alkalmazások és az Azure Functions-alkalmazások. Előfordulhat, hogy szeretne további példányok hozzáadása. Kezelőként ajánlata meghatározása, és bármely Termékváltozat-csomag kiválasztását. A rétegek rendelkeznie kell legalább egy vCPU. |

    ![Az App Service-telepítő][13]

    >[!NOTE]
    >**A Windows Server 2016 Core nem támogatott platform rendszerképe az Azure App Service az Azure Stacken használható.  Ne használja az értékelés rendszerképek éles környezetekben üzemelő példányok.**

13. Az a **Platformlemezkép kiválasztása** válassza ki a Windows Server 2016 telepítési virtuálisgép-lemezkép érhető el a számítási erőforrás-szolgáltató az App Service-felhő a lemezképek alapján. Kattintson a **Tovább** gombra.

14. A következő App Service-telepítő oldalon tegye a következőket:

     a. Adja meg a feldolgozói szerepkör virtuális gép rendszergazdai felhasználónevet és jelszót.

     b. Adja meg az egyéb szerepköröket virtuális gép rendszergazdai felhasználónevet és jelszót.

     c. Kattintson a **Tovább** gombra.

    ![Az App Service-telepítő][15]

15. Az App Service-telepítő összegzési oldalon tegye a következőket:

    a. Ellenőrizze a kiválasztott beállítások. Módosításához használja a **előző** gombok használatával keresse fel az előző lapokra.

    b. Ha a konfiguráció helyes, jelölje be a jelölőnégyzetet.

    c. Válassza ki az üzembe helyezés indításához **tovább**.

    ![Az App Service-telepítő][16]

16. A következő App Service-telepítő oldalon tegye a következőket:

    a. A telepítési folyamat előrehaladását. Az Azure Stack App Service-ben nagyjából percet vesz igénybe 60 üzembe helyezése az alapértelmezett beállításokat alapján.

    b. A telepítő sikeres befejezése után jelölje ki a **kilépési**.

    ![Az App Service-telepítő][17]

## <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

> [!IMPORTANT]  
> Ha az App Service RP példánnyal SQL mindig az adott kell [adja hozzá a appservice_hosting és appservice_metering adatbázisokat egy rendelkezésre állási csoport](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) , így elkerülhető, hogy a szolgáltatás az adatbázis szinkronizálásához, és a egy adatbázis feladatátvételi esemény.

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Az App Service az Azure Stack-telepítés ellenőrzése

1. Az Azure Stack felügyeleti portálon, lépjen a **felügyelet – az App Service**.

2. Az áttekintésben, az állapot, ellenőrizze, hogy, amely a **állapot** megjeleníti **minden szerepkör készen áll**.

    ![App Service Management](media/azure-stack-app-service-deploy/image12.png)

    Ha Ön üzembe helyezése meglévő virtuális hálózattal és belső IP-cím használatával szeretne csatlakozni a fájlkiszolgáló, hozzá kell adnia egy kimenő biztonsági szabályt. Ez a szabály lehetővé teszi a feldolgozó és a fájlkiszolgáló között SMB-forgalom.  Ehhez nyissa meg a WorkersNsg a felügyeleti portálon, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:

    - Adatforrás: Bármely
    - Forrás porttartomány: *
    - Cél: IP-címek
    - Cél IP-címtartomány: IP-címtartományt a fájlkiszolgálóhoz
    - Cél porttartomány: 445
    - Protokoll: TCP
    - Művelet: Engedélyezés
    - Prioritás: 700
    - Név: Outbound_Allow_SMB445

## <a name="test-drive-app-service-on-azure-stack"></a>Az Azure Stack App Service kipróbálása

Miután telepíti és regisztrálja az App Service erőforrás-szolgáltatót, tesztelje, győződjön meg arról, hogy felhasználók üzembe helyezhetnek web- és API-alkalmazások.

>[!NOTE]
>Hozzon létre egy ajánlatot, amely a csomag Microsoft.Web névtérrel kell. Emellett egy, az ajánlat feliratkozik bérlői előfizetéssel. További információkért lásd: [létrehozás ajánlat](azure-stack-create-offer.md) és [terv létrehozása](azure-stack-create-plan.md).
>
>Ön *kell* alkalmazásokat, amelyek az Azure Stacken használni az App Service-bérlői előfizetéssel rendelkezik. Csak a következő feladatok, amelyek a szolgáltatás rendszergazdája befejezheti a felügyeleti portálon a az App Service erőforrás-szolgáltató felügyelethez kapcsolódó. Ez magában foglalja a kapacitást, a központi telepítés forrásának konfigurálása és a feldolgozói rétegek és az SKU-k hozzáadása.
>
>Hozhat létre a webes API-t és az Azure Functions az alkalmazások, kell használni a bérlői portálra, és bérlői előfizetéssel rendelkezik.
>

Hozzon létre egy webes tesztalkalmazás, kövesse az alábbi lépéseket:

1. Az Azure Stack felhasználói portálon, válassza ki a **+ erőforrás létrehozása** > **Web + mobil** > **webalkalmazás**.

2. A **webalkalmazás**, adjon meg egy nevet a **webalkalmazás**.

3. A **erőforráscsoport**válassza **új**. Adjon meg egy nevet a **erőforráscsoport**.

4. Válassza ki **App Service-csomag/hely** > **hozzon létre új**.

5. A **App Service-csomag**, adjon meg egy nevet a **App Service-csomag**.

6. Válassza ki **tarifacsomag** > **ingyenes – megosztott** vagy **megosztott – megosztott** > **kiválasztása**  >  **OK** > **létrehozása**.

7. Az új webalkalmazást egy csempét az irányítópulton jelenik meg. Kattintson a csempére.

8. A **webalkalmazás**válassza **Tallózás** megtekintéséhez az alkalmazás az alapértelmezett webhely.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Webhely üzembe helyezése a WordPress, a DNN vagy a Django (nem kötelező)

1. Válassza ki az Azure Stack-bérlői portálon **+** nyissa meg az Azure Marketplace-en, Django-webhely üzembe helyezése, és ezután Várjon, amíg az üzembe helyezés befejeződik. A Django webes platform file system-alapú adatbázist használ. Nincs szükség semmilyen további erőforrás-szolgáltatók, például az SQL vagy MySQL.

2. Ha is üzembe helyezett MySQL erőforrás-szolgáltató, telepíthet egy WordPress-webhely létrehozása a piactérről. Amikor adatbázis paraméterek kéri, adja meg a felhasználónevet, *User1\@kiszolgáló1*, a felhasználónév és a kívánt kiszolgáló nevét.

3. Ha telepítette az SQL Server erőforrás-szolgáltató is, telepíthet egy DNN-webhely a piactérről. Adatbázis-paraméterek kér, amikor az adatbázis kiválasztása, amely csatlakozik az erőforrás-szolgáltató SQL Servert futtató számítógépen.

## <a name="next-steps"></a>További lépések

Is kipróbálhatja más [platform platformszolgáltatási (PaaS) szolgáltatásokra](azure-stack-offer-services-overview.md).

 - [Az SQL Server erőforrás-szolgáltató](azure-stack-sql-resource-provider-deploy.md)
 - [MySQL típusú erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: https://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: https://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: https://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png
