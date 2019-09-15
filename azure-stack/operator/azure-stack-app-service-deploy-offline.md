---
title: App Service üzembe helyezése offline környezetben Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe App Service a AD FS által védett offline Azure Stack környezetben.
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
ms.date: 08/29/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 0147108a2e4fb45fce98460fcde141b5f2e28df5
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70975040"
---
# <a name="deploy-app-service-in-an-offline-environment-in-azure-stack"></a>App Service üzembe helyezése offline környezetben Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

> [!IMPORTANT]
> Alkalmazza a 1907-es frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack Development Kitt (ASDK) a Azure App Service 1,7 üzembe helyezése előtt.

A cikkben található utasításokat követve telepítheti a [app Service erőforrás-szolgáltatót](azure-stack-app-service-overview.md) egy olyan Azure stack-környezetbe, amely a következő:

- nem kapcsolódik az internethez
- Active Directory összevonási szolgáltatások (AD FS) (AD FS) védi.

> [!IMPORTANT]
> Az erőforrás-szolgáltató telepítőjének futtatása előtt győződjön meg arról, hogy végrehajtotta a [app Service telepítésének előfeltételei](azure-stack-app-service-before-you-get-started.md)című témakör lépéseit Azure stack. Az 1,7-es kiadáshoz tartozó [kibocsátási megjegyzéseket](azure-stack-app-service-release-notes-update-seven.md) is el kell olvasnia, így megismerheti az új funkciókat, javításokat és az üzembe helyezést befolyásoló ismert problémákat.

Ha a App Service erőforrás-szolgáltatót a kapcsolat nélküli Azure Stack üzembe helyezéshez szeretné adni, el kell végeznie ezeket a legfelső szintű feladatokat:

1. Hajtsa végre az [előfeltételként szükséges lépéseket](azure-stack-app-service-before-you-get-started.md) (például a tanúsítványok megvásárlása, amely igénybe vehet néhány napot).
2. [Töltse le és csomagolja ki a telepítési és a segítő fájlokat](azure-stack-app-service-before-you-get-started.md) az internethez csatlakozó gépre.
3. Hozzon létre egy offline telepítőcsomagot.
4. Futtassa a appservice. exe telepítőfájlt.

## <a name="create-an-offline-installation-package"></a>Offline telepítőcsomag létrehozása

App Service offline környezetben történő telepítéséhez először létre kell hoznia egy offline telepítőcsomagot az internethez csatlakozó számítógépen.

1. Futtassa a AppService. exe telepítőjét olyan gépen, amely csatlakozik az internethez.

2. Kattintson a **speciális** > **kapcsolat nélküli telepítési csomag létrehozása**elemre.

    ![Offline csomag létrehozása App Service telepítőben][1]

3. A App Service-telepítő offline telepítőcsomagot hoz létre, és megjeleníti az elérési utat. A mappa **megnyitása** lehetőségre kattintva megnyithatja a mappát a fájlkezelőben.

    ![Az offline telepítési csomag sikeresen létrejött App Service telepítőben](media/azure-stack-app-service-deploy-offline/image02.png)

4. Másolja a telepítőt (AppService. exe) és az offline telepítőcsomagot a Azure Stack gazdagépre.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>App Service offline telepítésének befejezése Azure Stack

1. Futtassa a appservice. exe fájlt rendszergazdaként egy olyan számítógépről, amely elérheti a Azure Stack felügyeleti Azure erőforrás-kezelési végpontját.

2. Kattintson a **speciális** > **teljes offline telepítés**lehetőségre.

    ![Offline telepítés befejezése App Service telepítőben][2]

3. Keresse meg a korábban létrehozott offline telepítőcsomag helyét, majd kattintson a **tovább**gombra.

    ![Offline telepítési csomag elérési útjának megadása im App Service telepítőjének](media/azure-stack-app-service-deploy-offline/image04.png)

4. Tekintse át és fogadja el a Microsoft szoftverlicenc-feltételeit, majd kattintson a **tovább**gombra.

5. Tekintse át és fogadja el a harmadik féltől származó licencfeltételeket, majd kattintson a **tovább**gombra.

6. Ellenőrizze, hogy helyesek-e a App Service felhő konfigurációs adatai. Ha a ASDK telepítése során az alapértelmezett beállításokat használta, akkor itt fogadhatja el az alapértelmezett értékeket. Ha azonban a Azure Stack telepítésekor vagy egy integrált rendszeren való üzembe helyezéskor testreszabta a beállításokat, akkor az ebben az ablakban található értékeket kell módosítania, hogy tükrözze ezt. Ha például a mycloud.com tartományi utótagot használja, akkor a Azure Stack bérlőnek Azure Resource Manager végpontra kell váltania `management.<region>.mycloud.com`. Az adatok megerősítése után kattintson a **tovább**gombra.

    ![Azure App Service-felhő konfigurálása App Service telepítőben][3]

7. A következő oldalon:

   1. Kattintson a **Azure stack-előfizetések** mező melletti **Kapcsolódás** gombra. 

   2. Adja meg a rendszergazdai fiókját. Például: cloudadmin@azurestack.local. Adja meg a jelszavát, majd kattintson a **Bejelentkezés**elemre.

   3. A **Azure stack-előfizetések** mezőben válassza ki az **alapértelmezett szolgáltatói előfizetést**.

      > [!NOTE]
      > App Service csak az **alapértelmezett szolgáltatói előfizetésben**helyezhető üzembe.

   4. A **Azure stack helyek** mezőben válassza ki azt a helyet, amely a központilag üzembe helyezett régiónak felel meg. Válassza például a **Local (helyi** ) lehetőséget, ha üzembe helyezi a ASDK.

   5. Kattintson a **Tovább** gombra.

      ![Azure Stack-előfizetések és-helyszínek a App Service telepítőben][4]

8. Üzembe helyezhet egy meglévő virtuális hálózatot az [itt](azure-stack-app-service-before-you-get-started.md#virtual-network)ismertetett lépésekkel, vagy engedélyezheti a app Service telepítőnek, hogy virtuális hálózatot és társított alhálózatokat hozzon létre.
   - Válassza a **VNet létrehozása alapértelmezett beállításokkal**lehetőséget, fogadja el az alapértelmezett beállításokat, majd kattintson a **tovább**gombra, vagy a
   - Válassza **a meglévő VNet és alhálózatok használata**lehetőséget.
       1. Válassza ki azt az **erőforráscsoportot** , amely a virtuális hálózatot tartalmazza;
       2. Válassza ki a helyes **Virtual Network** nevet, amelyet üzembe szeretne helyezni;
       3. Válassza ki a megfelelő alhálózati értékeket minden szükséges szerepkör-alhálózathoz;
       4. Kattintson a **Tovább** gombra.

      ![Virtuális hálózat és alhálózat adatai App Service telepítőben][5]

9. Adja meg a fájlmegosztás adatait, majd kattintson a **tovább**gombra. A fájlmegosztás címének a fájlkiszolgáló teljes tartománynevét (FQDN) vagy IP-címét kell használnia. Például \\: \appservicefileserver.local.cloudapp.azurestack.external\websites vagy \\\10.0.0.1\websites.  Ha olyan fájlkiszolgálón használ, amely tartományhoz van csatlakoztatva, meg kell adnia a teljes felhasználónevet, beleértve a tartományt is. Például: myfileserverdomain\FileShareOwner.

    > [!NOTE]
    > A telepítő megkísérli a kapcsolat tesztelését a fájlmegosztás a továbblépés előtt. Ha azonban egy meglévő virtuális hálózatban telepíti a telepítését, előfordulhat, hogy a telepítő nem tud csatlakozni a fájlmegosztás, és egy figyelmeztetést jelenít meg, amely azt kérdezi, hogy folytatja-e a telepítést. Ellenőrizze a fájlmegosztás adatait, és folytassa, ha helyes.

   ![Fájlmegosztási információk a App Service-telepítőben][8]

10. A következő oldalon:
    1. Az **Identity Application ID (identitás-alkalmazás azonosítója** ) mezőben adja meg az identitáshoz használt alkalmazás GUID azonosítóját (az Azure ad-ból).
    2. Az **Identity Application tanúsítványfájl** mezőben adja meg a tanúsítványfájl helyét (vagy tallózással keresse meg a fájlt).
    3. Adja meg a tanúsítvány jelszavát a **személyazonossági alkalmazás tanúsítványának jelszava** mezőben. Ez a jelszó azt jelzi, hogy mikor használta a parancsfájlt a tanúsítványok létrehozásához.
    4. A **Azure Resource Manager legfelső szintű tanúsítvány fájl** mezőjébe írja be (vagy tallózással keresse meg) a tanúsítványfájl helyét.
    5. Kattintson a **Tovább** gombra.

    ![Adja meg az alkalmazás AZONOSÍTÓját és a tanúsítvány adatait App Service telepítőben][10]

11. A három tanúsítványfájl-mező esetében kattintson a **Tallózás** gombra, és keresse meg a megfelelő tanúsítványfájl-fájlt. Minden tanúsítványhoz meg kell adnia a jelszót. Ezek a tanúsítványok a [szükséges tanúsítványok létrehozása lépésben](azure-stack-app-service-before-you-get-started.md#get-certificates)létrehozott tanúsítványokat is magukban foglalják. Az összes információ beírása után kattintson a **tovább** gombra.

    | Box | Példa tanúsítványfájl-fájlnévre |
    | --- | --- |
    | **Alapértelmezett SSL-tanúsítványfájl App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **App Service API SSL-tanúsítványfájl** | api.appservice.local.AzureStack.external.pfx |
    | **App Service közzétevő SSL-tanúsítványfájl** | ftp.appservice.local.AzureStack.external.pfx |

    Ha a tanúsítványok létrehozásakor eltérő tartományi utótagot használt, a tanúsítványfájl neve nem a helyi nevet használja *. AzureStack. external*. Ehelyett használja az egyéni tartomány adatait.

    ![Adja meg az SSL-tanúsítvány adatait App Service telepítőben][11]

12. Adja meg a App Service erőforrás-szolgáltatói adatbázisok üzemeltetéséhez használt kiszolgálópéldány SQL Server adatait, majd kattintson a **tovább**gombra. A telepítő ellenőrzi, hogy az SQL-kapcsolatok tulajdonságai megtörténtek-e. A SQL Server neveként a belső IP-címet vagy a teljes tartománynevet **kell** megadnia.

    > [!NOTE]
    > A telepítő megkísérli a SQL Server kapcsolat tesztelését a továbblépés előtt. Ha azonban egy meglévő virtuális hálózatban helyezi üzembe a telepítést, előfordulhat, hogy a telepítő nem tud csatlakozni a SQL Serverhoz, és figyelmeztetést jelenít meg, hogy szeretne-e továbblépni. Ellenőrizze a SQL Server információt, és ha helyes, folytassa a művelettel.
    >
    > A Azure App Service Azure Stack 1,3-től kezdődően a telepítő ellenőrzi, hogy a SQL Server rendelkezik-e az adatbázis-tárolóval SQL Server szinten. Ha nem, a rendszer a következő kivételt kéri:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;
    >    GO
    >    RECONFIGURE;
    >    GO
    >    ***********************************************************
    > ```
    > További részletekért tekintse [meg a Azure App Service Azure Stack 1,3 kibocsátási megjegyzéseit](azure-stack-app-service-release-notes-update-three.md) .

    ![Adja meg SQL Server info App Service telepítőben][12]

13. Tekintse át a szerepkör-példány és az SKU beállításait. Az alapértelmezett érték a példányok minimális száma, valamint az egyes szerepkörök minimális SKU-jának ASDK-telepítésben való feltöltése. A vCPU és a memória követelményeinek összefoglalása az üzemelő példány megtervezéséhez nyújt segítséget. A kijelölések után kattintson a **tovább**gombra.

     > [!NOTE]
     > Éles üzembe helyezés esetén kövesse a [Azure Stack Azure app Service kiszolgálói szerepkörök kapacitásának megtervezése](azure-stack-app-service-capacity-planning.md)című témakör útmutatását.
     >
     >

    | Role | Minimális példányszám | Minimális SKU | Megjegyzések |
    | --- | --- | --- | --- |
    | Vezérlő | 1 | Standard_A2-(2 vCPU, 3584 MB) | Felügyeli és karbantartja a App Service felhő állapotát. |
    | Kezelés | 1 | Standard_A2-(2 vCPU, 3584 MB) | A App Service Azure Resource Manager és az API-végpontokat, a portál-bővítményeket (rendszergazda, bérlő, functions portál) és az adatszolgáltatást kezeli. A feladatátvétel támogatásához növelje az ajánlott példányokat 2-ra. |
    | Kiadó | 1 | Standard_A1-(1 vCPU, 1792 MB) | Tartalmat tesz közzé FTP-n keresztül és webes telepítéssel. |
    | Előtér | 1 | Standard_A1-(1 vCPU, 1792 MB) | Átirányítja a kérelmeket App Service alkalmazásokba. |
    | Megosztott feldolgozók | 1 | Standard_A1-(1 vCPU, 1792 MB) | Webes vagy API-alkalmazásokat, valamint Azure Functions alkalmazásokat üzemeltet. Előfordulhat, hogy további példányokat szeretne hozzáadni. Operátorként megadhatja az ajánlatát, és kiválaszthatja az SKU-szintet. A rétegeknek legalább egy vCPU kell rendelkezniük. |

    ![Szerepkörök szintjeinek és SKU-beállításainak megadása App Service telepítőben][14]

    > [!NOTE]
    > A Windows Server 2016 Core *nem* támogatott platform-rendszerkép, amellyel a Azure app Service Azure stack.  Éles környezetben ne használjon próbaverziókat. A Azure App Service on Azure Stack megköveteli, hogy a Microsoft.NET 3.5.1 SP1 aktiválva legyen az üzembe helyezéshez használt rendszerképben.  A Marketplace konzorciális Windows Server 2016-lemezképek nem rendelkeznek ezzel a szolgáltatással, ezért előre engedélyezve kell lennie egy Windows Server 2016-lemezkép létrehozásához és használatához.

14. A **platform kiválasztása rendszerkép** mezőben válassza ki a Windows Server 2016 virtuális gép (VM) lemezképét a app Service felhő számítási erőforrás-szolgáltatójában elérhető rendszerképekből. Kattintson a **Tovább** gombra.

15. A következő oldalon:
     1. Adja meg a feldolgozói szerepkör virtuális gép rendszergazdai felhasználónevét és jelszavát.
     2. Adja meg a többi szerepkört a virtuális gép rendszergazdájának felhasználónevét és jelszavát.
     3. Kattintson a **Tovább** gombra.

    ![Adja meg a szerepkörök virtuálisgép-rendszergazdáit App Service telepítőben][16]

16. Az összefoglalás lapon:
    1. Ellenőrizze az elvégzett beállításokat. A módosítások elvégzéséhez használja az előző gombokat az előző lapok megkereséséhez.
    2. Ha a konfigurációk helyesek, jelölje be a jelölőnégyzetet.
    3. A telepítés elindításához kattintson a **tovább**gombra.

    ![A App Service-telepítőben végrehajtott Kijelölések összegzése][17]

17. A következő oldalon:
    1. A telepítési folyamat nyomon követése. A App Service on Azure Stack az alapértelmezett beállítások alapján 60 percet vesz igénybe.
    2. A telepítő sikeres befejeződése után kattintson a **Kilépés**gombra.

    ![App Service telepítőjének telepítési folyamatának követése][18]

## <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

> [!IMPORTANT]
> Ha a App Service RP-t egy SQL always on-példánnyal adta meg, akkor a [appservice_hosting és a appservice_metering-adatbázist hozzá kell adnia egy rendelkezésre állási csoporthoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) , és szinkronizálnia kell az adatbázisokat, hogy elkerülje a szolgáltatás elvesztését a következő esetekben: adatbázis-feladatátvétel.

Ha úgy döntött, hogy egy meglévő virtuális hálózatra és egy belső IP-címet helyez üzembe a fájlkiszolgálón való kapcsolódáshoz, hozzá kell adnia egy kimenő biztonsági szabályt, amely engedélyezi az SMB-forgalmat a munkavégző alhálózat és a fájlkiszolgáló között. A felügyeleti portálon nyissa meg a WorkersNsg hálózati biztonsági csoportot, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:

- Forrás: Any
- Forrásoldali porttartomány: *
- Cél: IP-címek
- Cél IP-címtartomány: A fájlkiszolgáló IP-címeinek tartománya
- Célport tartománya: 445
- Protokoll: TCP
- Művelet: Allow
- Fontosság: 700
- Név: Outbound_Allow_SMB445

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Azure Stack telepítés App Service ellenőrzése

1. A Azure Stack felügyeleti portálon lépjen a **felügyelet-app Service**elemre.

2. Az Áttekintés területen az állapot alatt ellenőrizze, hogy az **állapot** megjeleníti-e az **összes szerepkört**.

    ![Az App Service felügyeletének áttekintése](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack"></a>App Service tesztelése Azure Stack

A App Service erőforrás-szolgáltató üzembe helyezése és regisztrálása után ellenőrizze, hogy a felhasználók telepíthetnek-e webes és API-alkalmazásokat.

> [!NOTE]
> Létre kell hoznia egy ajánlatot, amely a csomagon belül a Microsoft. Web névtérrel rendelkezik. Ezután egy bérlői előfizetéssel kell rendelkeznie, amely Előfizet erre az ajánlatra. További információért lásd az [ajánlat létrehozása](azure-stack-create-offer.md) és a [terv létrehozása](azure-stack-create-plan.md)című témakört.
>
> A App Servicet Azure Stack használó alkalmazások létrehozásához bérlői előfizetéssel *kell* rendelkeznie. A szolgáltatás-rendszergazda által a felügyeleti portálon elvégezhető képességek a App Service erőforrás-szolgáltatói felügyeletéhez kapcsolódnak. Ezek a képességek többek között a kapacitás hozzáadását, a telepítési források konfigurálását, valamint a feldolgozói rétegek és az SKU-ket adja
>
> A harmadik technikai előzetes verziótól kezdve a webes, API-és Azure Functions alkalmazások létrehozásához a bérlői portált kell használnia, és bérlői előfizetéssel kell rendelkeznie.

1. A Azure stack bérlői portálon kattintson az **+ erőforrás** > létrehozása**web és mobil** > **webalkalmazás**elemre.

2. A **Web App (webalkalmazás** ) panelen írja be a kívánt nevet a **Web App (webalkalmazás** ) mezőbe.

3. Az **erőforráscsoport**területen kattintson az **új**elemre. Adjon meg egy nevet az **erőforráscsoport** mezőben.

4. Kattintson az **App Service-csomag/Hely** > **Új létrehozása** lehetőségre.

5. A **app Service terv** panelen adjon meg egy nevet a **app Service terv** mezőben.

6.  >  >  > Kattintson az ingyenes csomag – megosztott vagy megosztott – megosztott elemre, majd az ok létrehozása lehetőségre. > 

7. Egy perc alatt az új webalkalmazáshoz tartozó csempe megjelenik az irányítópulton. Kattintson a csempére.

8. A **Web App (webalkalmazás** ) panelen kattintson a **Tallózás** gombra az alkalmazás alapértelmezett webhelyének megtekintéséhez.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>WordPress-, DNN-vagy Django-webhely üzembe helyezése (opcionális)

1. A Azure stack bérlői portálon kattintson **+** az Azure Marketplace-re, telepítsen egy Django-webhelyet, és várjon a sikeres befejezésre. A Django webes platform fájlrendszer alapú adatbázist használ. Nem igényel további erőforrás-szolgáltatót, például az SQL-t vagy a MySQL-t.

2. Ha egy MySQL erőforrás-szolgáltatót is üzembe helyezett, a piactéren telepítheti a WordPress-webhelyeket is. Amikor a rendszer az adatbázis paramétereinek megadását kéri, írja be a felhasználónevet *Felhasználó1\@Kiszolgáló1*néven, a felhasználónévvel és a kiszolgáló nevével.

3. Ha SQL Server erőforrás-szolgáltatót is üzembe helyezett, üzembe helyezhet egy DNN-webhelyet a piactéren. Ha a rendszer az adatbázis paramétereinek megadását kéri, válasszon egy adatbázist az erőforrás-szolgáltatóhoz csatlakoztatott SQL Servert futtató számítógépen.

## <a name="next-steps"></a>További lépések

További rendszergazdai műveletek előkészítése a Azure Stack App Serviceához:

- [Kapacitás megtervezése](azure-stack-app-service-capacity-planning.md)
- [Központi telepítési források konfigurálása](azure-stack-app-service-configure-deployment-sources.md)

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
