---
title: App Service üzembe helyezése az Azure Stack kapcsolat nélküli környezetben |} A Microsoft Docs
description: Részletes útmutatást AD FS által védett leválasztott környezet az Azure Stack App Service üzembe helyezése.
services: azure-stack
documentationcenter: ''
author: BryanLa
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
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: c97598145b0d03f3b25876296cb070b0301a3742
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269236"
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Az App Service erőforrás-szolgáltató hozzáadása az AD FS által védett kapcsolat nélküli Azure Stack-környezet

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!IMPORTANT]
> Az Azure Stackkel integrált rendszereknél 1904 frissítés alkalmazása, vagy a legújabb Azure Stack fejlesztői készletének telepítése az Azure App Service 1.6-os üzembe helyezése előtt.

Ez a cikk utasításait követve telepítheti a [App Service erőforrás-szolgáltató](azure-stack-app-service-overview.md) , amely az Azure Stack-környezetben:

- nem csatlakozik az internethez
- az Active Directory összevonási szolgáltatások (AD FS) védi.

> [!IMPORTANT]
> A resource provider telepítőjének futtatása, előtt győződjön meg arról, hogy követte az útmutató [használatának megkezdése előtt](azure-stack-app-service-before-you-get-started.md) elolvasta és a [kibocsátási megjegyzések](azure-stack-app-service-release-notes-update-six.md) amely kísérő 6 kiadás további információ az új Funkciók, javításokat és olyan ismert problémákat, amelyek hatással lehetnek a központi telepítés.

Az App Service erőforrás-szolgáltató hozzáadása az Azure Stack kapcsolat nélküli üzembe helyezés, a legfelső szintű feladatot kell elvégeznie:

1. Végezze el a [előfeltételként felsorolt lépéseket](azure-stack-app-service-before-you-get-started.md) (például a tanúsítványok vásárlása, amelyhez napokat is igénybe vehet néhány fogadásához).
2. [Töltse le és csomagolja ki a telepítési és segítő fájlokat](azure-stack-app-service-before-you-get-started.md) egy géphez csatlakozik az internethez.
3. Offline telepítőcsomag létrehozása.
4. Futtassa a appservice.exe installer fájlt.

## <a name="create-an-offline-installation-package"></a>Offline telepítőcsomag létrehozása

App Service-ben leválasztott környezet üzembe helyezéséhez, először létre kell hoznia egy offline telepítőcsomagot olyan számítógépen, amelyen az internethez csatlakozik.

1. Futtassa a AppService.exe telepítő olyan számítógépen, amelyen az internethez csatlakozik.

2. Kattintson a **speciális** > **offline telepítőcsomag létrehozása**.

    ![Az App Service-telepítő][1]

3. Az App Service-ben telepítő létrehoz egy offline telepítőcsomagot, és elérési útja. Kattinthat **mappa megnyitása** a mappa megnyitása a Fájlkezelőben.

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy-offline/image02.png)

4. A telepítő (AppService.exe) és az offline telepítőcsomag másolja az Azure Stack gazdagépen.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>App Service-ben az Azure Stack kapcsolat nélküli telepítésének befejezése

1. Egy számítógép, amely elérheti az Azure Stack rendszergazdai Azure Resource Management-végpont appservice.exe rendszergazdaként futtatni.

2. Kattintson a **speciális** > **offline telepítés befejezéséhez**.

    ![Az App Service-telepítő][2]

3. Keresse meg a helyet, a korábban létrehozott offline telepítőcsomagot, és kattintson **tovább**.

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy-offline/image04.png)

4. Tekintse át és fogadja el a Microsoft szoftverlicenc-feltételeket, majd **tovább**.

5. Tekintse át és fogadja el a külső licencfeltételeket, és kattintson **tovább**.

6. Győződjön meg arról, hogy helyesen szerepel-e az App Service-ben felhőbeli konfigurációs adatait. Az alapértelmezett beállításokat használta az Azure Stack fejlesztői készletének üzembe helyezés során, ha elfogadhatja az alapértelmezett értékeket itt. Azonban ha testre szabta a beállításokat, amikor Azure Stack üzembe vagy telepíti az integrált rendszereken, szerkesztenie kell az értékeket az ebben a változás tükrözése érdekében. Például, ha a tartományi utótag mycloud.com használja, az Azure Stack bérlő Azure Resource Manager-végpontot kell módosítania `management.<region>.mycloud.com`. Miután meggyőződött a adatait, kattintson a **tovább**.

    ![Az App Service-telepítő][3]

7. A következő oldalon:
   1. Kattintson a **Connect** megjelenítő gombra a **Azure Stack-előfizetést** mezőbe.
      - Adja meg a rendszergazdai fiókjával. Például: cloudadmin@azurestack.local. Adja meg a jelszót, és kattintson a **bejelentkezés**.
   2. Az a **Azure Stack-előfizetést** jelölje ki a **szolgáltatói előfizetés alapértelmezett**.

      > [!NOTE]
      > App Service-ben csak lesz üzembe helyezve a **szolgáltatói előfizetés alapértelmezett**.
      >

   3. Az a **Azure Stack-helyek** válassza ki a helyet, amely megfelel a régió, helyezi üzembe. Válassza ki például **helyi** Ha az az Azure Stack fejlesztői készletének telepítése.
   4. Kattintson a **tovább**.

      ![Az App Service-telepítő][4]

8. Most már rendelkezik konfigurált lépéseit egy meglévő virtuális hálózatban telepíteni [Itt](azure-stack-app-service-before-you-get-started.md#virtual-network), vagy engedélyezheti az App Service-ben telepítő hozhat létre virtuális hálózat és a társított alhálózatokat.
   1. Válassza ki **virtuális hálózat létrehozása az alapértelmezett beállításokkal**, fogadja el az alapértelmezett beállításokat, és kattintson **tovább**, vagy;
   2. Válassza ki **használja a meglévő virtuális hálózat és alhálózatok**.
       1. Válassza ki a **erőforráscsoport** , amely tartalmazza a virtuális hálózat;
       2. Válassza ki a megfelelő **virtuális hálózat** ; üzembe helyezése a kívánt nevét
       3. Válassza ki a megfelelő **alhálózati** értékek mind a szükséges szerepkör-alhálózatok;
       4. Kattintson a **Tovább** gombra

      ![Az App Service-telepítő][5]

9. Adja meg a fájlmegosztás az adatokat, és kattintson a **tovább**. A cím a fájlmegosztás a teljes tartománynév, vagy a fájlkiszolgáló IP-címet kell használnia. Ha például \\\appservicefileserver.local.cloudapp.azurestack.external\websites, vagy \\\10.0.0.1\websites.  Ha használ egy fájlkiszolgálón, amely tartományhoz van csatlakoztatva, meg kell adnia a teljes felhasználónevet, többek között a tartományhoz, például myfileserverdomain\FileShareOwner.

    > [!NOTE]
    > A telepítő próbál csatlakozni a fájlmegosztási, a folytatás előtt.  Azonban ha egy meglévő virtuális hálózaton üzembe helyezéséhez, a telepítő nem tud csatlakozni a fájlmegosztási és figyelmeztető üzenetet, amely rákérdez, hogy szeretné-e továbbra is.  Ellenőrizze a fájlmegosztás adatait, és továbbra is, hogy helyesek.
    >
    >

   ![Az App Service-telepítő][8]

10. A következő oldalon:
    1. Az a **identitás Alkalmazásazonosítója** adja meg a globálisan egyedi Azonosítót (az Azure AD) identitás használja az alkalmazást.
    2. Az a **identitás alkalmazástanúsítvány-fájlja** mezőben adja meg (vagy keresse meg a) a tanúsítvány-fájl helyét.
    3. Az a **identitás alkalmazástanúsítványának jelszava** mezőbe írja be a jelszót a tanúsítványhoz. Ez a jelszó pedig végzett jegyezze fel, ha a parancsfájl a tanúsítványok létrehozásához használt.
    4. Az a **Azure Resource Manager-főtanúsítványfájl** mezőben adja meg (vagy keresse meg a) a tanúsítvány-fájl helyét.
    5. Kattintson a **tovább**.

    ![Az App Service-telepítő][10]

11. A három minden tanúsítvány-fájl mezőbe, kattintson **Tallózás** , majd lépjen a megfelelő tanúsítványfájlt. Meg kell adnia a jelszavát minden egyes tanúsítvány. Ezek a tanúsítványok lépnek, amelyet a [létrehozása szükséges tanúsítványok lépésben](azure-stack-app-service-before-you-get-started.md#get-certificates). Kattintson a **tovább** összes információ megadása után.

    | Box | Tanúsítvány-fájl neve például |
    | --- | --- |
    | **Az App Service alapértelmezett SSL-tanúsítványfájlja** | \_.appservice.local.AzureStack.external.pfx |
    | **Az App Service API SSL-tanúsítványfájlja** | api.appservice.local.AzureStack.external.pfx |
    | **App Service-közzétevő SSL-tanúsítványfájlja** | ftp.appservice.local.AzureStack.external.pfx |

    Ha a különböző tartománynak az utótagját, a tanúsítványok létrehozásakor, a tanúsítvány fájlnevek használja, nem *helyi. AzureStack.external*. Ehelyett használja az egyéni tartomány adatait.

    ![Az App Service-telepítő][11]

12. Adja meg, hogy az SQL Server, az App Service erőforrás-szolgáltató adatbázisainak üzemeltetésére, és kattintson a kiszolgálópéldánynak **tovább**. A telepítő ellenőrzi az SQL-kapcsolat tulajdonságai. Ön **kell** adja meg a belső ip- vagy teljes tartománynevét adja meg az SQL Server nevét.

    > [!NOTE]
    > A telepítő próbál meg csatlakozni az SQl Server, a folytatás előtt.  Azonban ha úgy döntött, hogy egy meglévő virtuális hálózaton üzembe helyezése, a telepítő nem tud csatlakozni az SQL Server és egy figyelmeztetés, amely rákérdez, hogy szeretné-e továbbra is megjeleníti.  Ellenőrizze az SQL Server-adatokat, és továbbra is, hogy helyesek.
    >
    > Az Azure App Service, az Azure Stack 1.3 és újabb verziók esetében a telepítő ellenőrzi, hogy rendelkezik-e az SQL Server adatbázis tartalmazási az SQL Server szintjén engedélyezhető.  Nem érhető el, ha a rendszer kéri, a következő kivétel miatt:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;
    >    GO
    >    RECONFIGURE;
    >    GO
    >    ***********************************************************
    > ```
    > Tekintse meg a [kibocsátási megjegyzések az Azure App Service az Azure Stack 1.3](azure-stack-app-service-release-notes-update-three.md) további részletekért.

    ![Az App Service-telepítő][12]

13. Tekintse át a szerepkörpéldányhoz, és a Termékváltozat-beállításokat. Az alapértelmezett beállításokat a rendszer kitölti a minimális számú példányok és a minimális Termékváltozat-ASDK telepítés minden egyes szerepkörhöz. VCPU-és memória összegzést érdekében az üzembe helyezésének megtervezése. Kattintson a kiválasztás után **tovább**.

     > [!NOTE]
     > Éles környezetekben üzemelő példányok, kövesse az útmutató [kapacitás tervezése az Azure App Service-ben kiszolgálói szerepkörök az Azure Stackben](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Szerepkör | Minimális példányok | Minimális Termékváltozat | Megjegyzések |
    | --- | --- | --- | --- |
    | Vezérlő | 1. | A Standard_A2 - (2 vCPU, 3584 MB) | Kezeli, és fenntartja az App Service-felhő állapotát. |
    | Kezelés | 1. | A Standard_A2 - (2 vcpu-k, 3584 MB) | Az App Service az Azure Resource Manager és az API-végpontok, portál extensions (felügyeleti, a bérlő, a Functions portálon.) és az adatok szolgáltatás kezeli. A feladatátvétel támogatásához a javasolt példányok 2-re nőtt. |
    | Gyártó | 1. | A Standard_A1 - (1 vCPU, 1792 MB) | Tesz közzé az FTP és a webes üzembe a tartalomhoz. |
    | Előtér | 1. | A Standard_A1 - (1 vCPU, 1792 MB) | Az App Service-alkalmazások felé irányítja a kérelmeket. |
    | Megosztott feldolgozói | 1. | A Standard_A1 - (1 vCPU, 1792 MB) | Gazdagépek webalkalmazás vagy API-alkalmazások és az Azure Functions-alkalmazások. Előfordulhat, hogy szeretne további példányok hozzáadása. Kezelőként ajánlata meghatározása, és bármely Termékváltozat-csomag kiválasztását. A rétegek rendelkeznie kell legalább egy vCPU. |

    ![Az App Service-telepítő][14]

    > [!NOTE]
    > **A Windows Server 2016 Core nem támogatott platform rendszerképe az Azure App Service az Azure Stacken használható.  Ne használjon értékelési rendszerképek éles környezetekben üzemelő példányok.  Az Azure App Service az Azure Stacken szükséges, hogy az 3.5.1 SP1 Microsoft.NET aktiválva van-e a központi telepítéshez használt lemezkép.   Hírcsatorna-piactéren a Windows Server 2016 lemezképek nem rendelkezik a szolgáltatás nincs engedélyezve, ezért kell létrehozni, és a Windows Server 2016-rendszerkép használata az előre engedélyezett.**

14. Az a **Platformlemezkép kiválasztása** válassza ki a Windows Server 2016 telepítési virtuálisgép-lemezkép érhető el a számítási erőforrás-szolgáltató az App Service-felhő a lemezképek alapján. Kattintson a **Tovább** gombra.

15. A következő oldalon:
     1. Adja meg a feldolgozói szerepkör virtuális gép rendszergazdai felhasználónevet és jelszót.
     2. Adja meg az egyéb szerepköröket virtuális gép rendszergazdai felhasználónevet és jelszót.
     3. Kattintson a **tovább**.

    ![Az App Service-telepítő][16]

16. Az összefoglalás lapon:
    1. Ellenőrizze a kiválasztott beállítások. Módosításához használja a **előző** gombok használatával keresse fel az előző lapokra.
    2. Ha a konfiguráció helyes, jelölje be a jelölőnégyzetet.
    3. Az üzembe helyezés elindításához kattintson **tovább**.

    ![Az App Service-telepítő][17]

17. A következő oldalon:
    1. A telepítési folyamat előrehaladását. Az Azure Stack App Service-ben nagyjából percet vesz igénybe 60 üzembe helyezése az alapértelmezett beállításokat alapján.
    2. A telepítő sikeres befejezését követően kattintson **kilépési**.

    ![Az App Service-telepítő][18]

## <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

> [!IMPORTANT]
> Ha az App Service RP példánnyal SQL mindig az adott kell [adja hozzá a appservice_hosting és appservice_metering adatbázisokat egy rendelkezésre állási csoport](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) , így elkerülhető, hogy a szolgáltatás az adatbázis szinkronizálásához, és a egy adatbázis feladatátvételi esemény.

Ha úgy döntött, hogy egy meglévő virtuális hálózattal és belső IP-cím szeretne csatlakozni a fájlkiszolgáló üzembe helyezése, hozzá kell adnia egy kimenő biztonsági szabályt a feldolgozó és a fájlkiszolgáló között SMB-forgalom engedélyezése.  Nyissa meg a WorkersNsg, hálózati biztonsági csoportot, a felügyeleti portálon, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:

- Adatforrás: Bármely
- Forrás porttartomány: *
- Cél: IP-címek
- Cél IP-címtartomány: IP-címtartományt a fájlkiszolgálóhoz
- Cél porttartomány: 445
- Protokoll: TCP
- Művelet: Engedélyezés
- Prioritás: 700
- Név: Outbound_Allow_SMB445

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Az App Service az Azure Stack-telepítés ellenőrzése

1. Az Azure Stack felügyeleti portálon, lépjen a **felügyelet – az App Service**.

2. Az áttekintésben, az állapot, ellenőrizze, hogy, amely a **állapot** megjeleníti **minden szerepkör készen áll**.

    ![App Service Management](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack"></a>Az Azure Stack App Service kipróbálása

Miután telepíti és regisztrálja az App Service erőforrás-szolgáltatót, tesztelje, győződjön meg arról, hogy felhasználók üzembe helyezhetnek web- és API-alkalmazások.

> [!NOTE]
> Hozzon létre egy ajánlatot, amely a csomagon belüli Microsoft.Web névtérrel kell. Majd szüksége lesz egy, az ajánlat feliratkozik bérlői előfizetéssel. További információkért lásd: [létrehozás ajánlat](azure-stack-create-offer.md) és [terv létrehozása](azure-stack-create-plan.md).
>
> Ön *kell* alkalmazásokat, amelyek az Azure Stacken használni az App Service-bérlői előfizetéssel rendelkezik. A csak képességeket, amelyek a szolgáltatás rendszergazdája befejezheti a felügyeleti portálon a az App Service erőforrás-szolgáltató felügyelethez kapcsolódó. Ezek a képességek közé tartozik a kapacitást, a központi telepítés forrásának konfigurálása és a feldolgozói rétegek és az SKU-k hozzáadása.
>
> A harmadik technical preview kezdődően létrehozása webes API-t és az Azure Functions az alkalmazások, kell használni a bérlői portálra, és bérlői előfizetéssel rendelkezik.

1. Az Azure Stack-bérlői portálon kattintson a **+ erőforrás létrehozása** > **Web + mobil** > **webalkalmazás**.

2. Az a **webalkalmazás** panelen írja be a nevet a **webalkalmazás** mezőbe.

3. A **erőforráscsoport**, kattintson a **új**. Adjon meg egy nevet a a **erőforráscsoport** mezőbe.

4. Kattintson az **App Service-csomag/Hely** > **Új létrehozása** lehetőségre.

5. Az a **App Service-csomag** panelen írja be a nevet a **App Service-csomag** mezőbe.

6. Kattintson a **tarifacsomag** > **ingyenes – megosztott** vagy **megosztott – megosztott** > **kiválasztása**  >   **OK** > **létrehozása**.

7. Egy perc alatt az új webalkalmazás számára egy csempe jelenik meg az irányítópulton. Kattintson a csempére.

8. Az a **webalkalmazás** panelen kattintson a **Tallózás** megtekintéséhez az alkalmazás az alapértelmezett webhely.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Webhely üzembe helyezése a WordPress, a DNN vagy a Django (nem kötelező)

1. Az Azure Stack-bérlői portálon kattintson a **+** nyissa meg az Azure Marketplace-en, Django-webhely üzembe helyezése, és várjon, amíg a művelet sikeresen befejeződött. A Django webes platform file system-alapú adatbázist használ. Nincs szükség semmilyen további erőforrás-szolgáltatók, például az SQL vagy MySQL.

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
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
