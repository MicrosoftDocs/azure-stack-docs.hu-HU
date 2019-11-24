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
ms.openlocfilehash: 25cb846b8e3a2200636dff0f2acfc25244a5e3d4
ms.sourcegitcommit: e8aa26b078a9bab09c8fafd888a96785cc7abb4d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709003"
---
# <a name="deploy-app-service-in-an-offline-environment-in-azure-stack"></a>App Service üzembe helyezése offline környezetben Azure Stack

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!IMPORTANT]
> Alkalmazza a 1907-es frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack Development Kitt (ASDK) a Azure App Service 1,7 üzembe helyezése előtt.

A cikkben található utasításokat követve telepítheti a [app Service erőforrás-szolgáltatót](azure-stack-app-service-overview.md) egy olyan Azure stack-környezetbe, amely a következő:

- Nem kapcsolódik az internethez.
- Active Directory összevonási szolgáltatások (AD FS) (AD FS) védi.

> [!IMPORTANT]
> Az erőforrás-szolgáltató telepítőjének futtatása előtt győződjön meg arról, hogy végrehajtotta a [app Service telepítésének előfeltételei](azure-stack-app-service-before-you-get-started.md)című témakör lépéseit Azure stack. Emellett olvassa el a 1,7-es kiadáshoz mellékelt [kibocsátási megjegyzéseket](azure-stack-app-service-release-notes-update-seven.md) , amelyekkel megismerheti az új funkciókat, javításokat és az üzembe helyezést befolyásoló ismert problémákat.

Ha a App Service erőforrás-szolgáltatót a kapcsolat nélküli Azure Stack üzembe helyezéshez szeretné adni, el kell végeznie ezeket a legfelső szintű feladatokat:

1. Hajtsa végre az [előfeltételként szükséges lépéseket](azure-stack-app-service-before-you-get-started.md) (például a tanúsítványok megvásárlása, amely igénybe vehet néhány napot).
2. [Töltse le és csomagolja ki a telepítési és a segítő fájlokat](azure-stack-app-service-before-you-get-started.md) egy internethez csatlakozó gépre.
3. Hozzon létre egy offline telepítőcsomagot.
4. Futtassa a appservice. exe telepítőfájlt.

## <a name="create-an-offline-installation-package"></a>Offline telepítőcsomag létrehozása

A App Service offline környezetben történő telepítéséhez először hozzon létre egy offline telepítőcsomagot egy internetkapcsolattal rendelkező számítógépen.

1. Futtassa a AppService. exe telepítőjét olyan gépen, amely csatlakozik az internethez.

2. Válassza a **speciális** > **Offline telepítési csomag létrehozása**lehetőséget.

    ![Offline csomag létrehozása App Service telepítőben][1]

3. A App Service-telepítő offline telepítőcsomagot hoz létre, és megjeleníti az elérési utat. A **mappa megnyitása lehetőség kiválasztásával** megnyithatja a mappát a fájlkezelőben.

    ![Az offline telepítési csomag sikeresen létrejött App Service telepítőben](media/azure-stack-app-service-deploy-offline/image02.png)

4. Másolja a telepítőt (AppService. exe) és az offline telepítőcsomagot a Azure Stack gazdagépre.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>App Service offline telepítésének befejezése Azure Stack

1. Futtassa a appservice. exe fájlt rendszergazdaként egy olyan számítógépről, amely elérheti a Azure Stack felügyeleti Azure erőforrás-kezelési végpontját.

1. Válassza a **speciális** > az **Offline telepítés befejezése**lehetőséget.

    ![Offline telepítés befejezése App Service telepítőben][2]

1. Keresse meg a korábban létrehozott offline telepítőcsomag helyét, majd kattintson a **tovább**gombra.

    ![Offline telepítési csomag elérési útjának megadása im App Service telepítőjének](media/azure-stack-app-service-deploy-offline/image04.png)

1. Tekintse át és fogadja el a Microsoft szoftverlicenc-feltételeit, majd kattintson a **tovább**gombra.

1. Tekintse át és fogadja el a harmadik féltől származó licencfeltételeket, majd kattintson a **tovább**gombra.

1. Győződjön meg arról, hogy a App Service felhő konfigurációs adatai helyesek. Ha a ASDK telepítése során az alapértelmezett beállításokat használta, akkor itt fogadhatja el az alapértelmezett értékeket. Ha azonban a Azure Stack telepítésekor vagy egy integrált rendszeren való üzembe helyezéskor testreszabta a beállításokat, akkor az ebben az ablakban található értékeket kell szerkesztenie, hogy azok tükrözzék ezeket a módosításokat. Ha például a mycloud.com tartományi utótagot használja, akkor a Azure Stack bérlő Azure Resource Manager végpontjának `management.<region>.mycloud.com`re kell váltania. Az adatok megerősítése után válassza a **tovább**lehetőséget.

    ![Azure App Service-felhő konfigurálása App Service telepítőben][3]

1. A következő oldalon:

   a. Kattintson a **Azure stack-előfizetések** mező melletti **Kapcsolódás** gombra. 

   b. Adja meg a rendszergazdai fiókját. Például: cloudadmin@azurestack.local. Adja meg a jelszavát, majd válassza a **Bejelentkezés**lehetőséget.

   c. Az **Azure stack-előfizetések** mezőben válassza az **alapértelmezett szolgáltatói előfizetés**lehetőséget.
   > [!NOTE]
   > App Service csak az **alapértelmezett szolgáltatói előfizetésbe**helyezhető üzembe.

   d. A **Azure stack helyek** mezőben válassza ki azt a helyet, amely a központilag üzembe helyezett régiónak felel meg. Ha például üzembe helyezi a ASDK, válassza a **helyi**lehetőséget.

   e. Kattintson a **Tovább** gombra.

      ![Azure Stack-előfizetések és-helyszínek a App Service telepítőben][4]

1. Lehetővé teheti, hogy a App Service telepítőjének virtuális hálózatot és társított alhálózatokat hozzon létre. Vagy egy meglévő virtuális hálózatba is telepítheti [ezeket a lépéseket](azure-stack-app-service-before-you-get-started.md#virtual-network).
   - A App Service telepítő módszer használatához válassza a **VNet létrehozása alapértelmezett beállításokkal**lehetőséget, fogadja el az alapértelmezett beállításokat, majd kattintson a **tovább**gombra.
   - Meglévő hálózatra történő üzembe helyezéshez válassza a **meglévő VNet és alhálózatok használata**lehetőséget, majd a következőket:
       1. Válassza ki a virtuális hálózatot tartalmazó **erőforráscsoport** -beállítást.
       2. Válassza ki azt a **Virtual Network** nevet, amelyet központilag telepíteni szeretne.
       3. Válassza ki a megfelelő **alhálózati** értékeket minden szükséges szerepkör-alhálózathoz.
       4. Kattintson a **Tovább** gombra.

      ![Virtuális hálózat és alhálózat adatai App Service telepítőben][5]

1. Adja meg a fájlmegosztás adatait, majd kattintson a **tovább**gombra. A fájlmegosztás címének a fájlkiszolgáló teljes tartománynevét (FQDN) vagy IP-címét kell használnia. Például: \\\appservicefileserver.local.cloudapp.azurestack.external\websites, vagy \\\10.0.0.1\websites.  Ha tartományhoz csatlakoztatott fájlkiszolgálón használ, meg kell adnia a teljes felhasználónevet, beleértve a tartományt is. Például: `<myfileserverdomain>\<FileShareOwner>`.

    > [!NOTE]
    > A telepítő megkísérli a fájlmegosztás kapcsolatának tesztelését a folytatás előtt. Ha azonban egy meglévő virtuális hálózatba helyezi üzembe a telepítést, előfordulhat, hogy a telepítő nem tud kapcsolódni a fájlmegosztás számára, és figyelmeztetést jelenít meg, hogy szeretné-e folytatni. Ellenőrizze a fájlmegosztás adatait, és folytassa, ha helyes.

   ![Fájlmegosztási információk a App Service-telepítőben][8]

1. A következő oldalon:
    1. Az **Identity Application ID (identitás-alkalmazás azonosítója** ) mezőben adja meg az identitáshoz használt alkalmazás GUID azonosítóját (az Azure ad-ból).
    1. Az **Identity Application tanúsítványfájl** mezőben adja meg a tanúsítványfájl helyét (vagy tallózással keresse meg a fájlt).
    1. Adja meg a tanúsítvány jelszavát a **személyazonossági alkalmazás tanúsítványának jelszava** mezőben. Ez a jelszó azt jelzi, hogy mikor használta a parancsfájlt a tanúsítványok létrehozásához.
    1. A **Azure Resource Manager legfelső szintű tanúsítvány fájl** mezőjébe írja be (vagy tallózással keresse meg) a tanúsítványfájl helyét.
    1. Kattintson a **Tovább** gombra.

    ![Adja meg az alkalmazás AZONOSÍTÓját és a tanúsítvány adatait App Service telepítőben][10]

1. A három tanúsítványfájl-mező esetében válassza a **Tallózás**lehetőséget, majd lépjen a megfelelő tanúsítványfájl. Minden tanúsítványhoz meg kell adnia a jelszót. Ezek a tanúsítványok a [szükséges tanúsítványok létrehozása lépésben](azure-stack-app-service-before-you-get-started.md#get-certificates)létrehozott tanúsítványokat is magukban foglalják. Az összes információ beírása után válassza a **tovább** lehetőséget.

    | Box | Példa tanúsítványfájl-fájlnévre |
    | --- | --- |
    | **Alapértelmezett SSL-tanúsítványfájl App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **App Service API SSL-tanúsítványfájl** | api.appservice.local.AzureStack.external.pfx |
    | **App Service közzétevő SSL-tanúsítványfájl** | ftp.appservice.local.AzureStack.external.pfx |

    Ha a tanúsítványok létrehozásakor eltérő tartományi utótagot használt, a tanúsítványfájl neve nem a helyi nevet használja *. AzureStack. external*. Ehelyett használja az egyéni tartomány adatait.

    ![Adja meg az SSL-tanúsítvány adatait App Service telepítőben][11]

1. Adja meg a App Service erőforrás-szolgáltatói adatbázisok üzemeltetéséhez használt kiszolgálópéldány SQL Server adatait, majd kattintson a **tovább**gombra. A telepítő ellenőrzi, hogy az SQL-kapcsolatok tulajdonságai megtörténtek-e. A SQL Server neveként a belső IP-címet vagy a teljes tartománynevet **kell** megadnia.

    > [!NOTE]
    > A telepítő megkísérli a SQL Server rendszert futtató számítógép kapcsolatának tesztelését a továbblépés előtt. Ha azonban egy meglévő virtuális hálózatba helyezi üzembe a telepítést, előfordulhat, hogy a telepítő nem tud csatlakozni a SQL Server rendszerű számítógéphez, és figyelmeztetést jelenít meg arról, hogy folytatni kívánja-e a telepítést. Ellenőrizze a SQL Server információt, és ha helyes, folytassa a művelettel.
    >
    > A Azure App Service Azure Stack 1,3-től kezdve a telepítő ellenőrzi, hogy a SQL Server futtató számítógép SQL Server szinten engedélyezve van-e az adatbázis-tárolás. Ha nem, a rendszer a következő kivételt kéri:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;
    >    GO
    >    RECONFIGURE;
    >    GO
    >    ***********************************************************
    > ```
    > További részletekért tekintse [meg a Azure App Service Azure Stack 1,3 kibocsátási megjegyzéseit](azure-stack-app-service-release-notes-update-three.md).

    ![Adja meg SQL Server info App Service telepítőben][12]

1. Tekintse át a szerepkör-példány és az SKU beállításait. Az alapértelmezett érték a példányok minimális száma, valamint az egyes szerepkörök minimális SKU-jának ASDK-telepítésben való feltöltése. A vCPU és a memória követelményeinek összefoglalása az üzemelő példány megtervezéséhez nyújt segítséget. A kijelölések után válassza a **tovább**lehetőséget.

     > [!NOTE]
     > Éles üzembe helyezés esetén kövesse a [Azure Stack Azure app Service kiszolgálói szerepkörök kapacitásának megtervezése](azure-stack-app-service-capacity-planning.md)című témakör útmutatását.
     >
     >

    | Szerepkör | Minimális példányszám | Minimális SKU | Megjegyzések |
    | --- | --- | --- | --- |
    | Tartományvezérlő | 1 | Standard_A2 – (2 vCPU, 3584 MB) | Felügyeli és karbantartja a App Service felhő állapotát. |
    | Kezelés | 1 | Standard_A2 – (2 vCPU, 3584 MB) | A App Service Azure Resource Manager és az API-végpontokat, a portál-bővítményeket (rendszergazda, bérlő, functions portál) és az adatszolgáltatást kezeli. A feladatátvétel támogatásához növelje az ajánlott példányokat 2-ra. |
    | Közzétevő | 1 | Standard_A1 – (1 vCPU, 1792 MB) | Tartalmat tesz közzé FTP-n keresztül és webes telepítéssel. |
    | Előtér | 1 | Standard_A1 – (1 vCPU, 1792 MB) | Átirányítja a kérelmeket App Service alkalmazásokba. |
    | Megosztott feldolgozók | 1 | Standard_A1 – (1 vCPU, 1792 MB) | Webes vagy API-alkalmazásokat, valamint Azure Functions alkalmazásokat üzemeltet. Előfordulhat, hogy további példányokat szeretne hozzáadni. Operátorként megadhatja az ajánlatát, és kiválaszthatja az SKU-szintet. A rétegeknek legalább egy vCPU kell rendelkezniük. |

    ![Szerepkörök szintjeinek és SKU-beállításainak megadása App Service telepítőben][14]

    > [!NOTE]
    > A Windows Server 2016 Core *nem* támogatott platform-rendszerkép, amellyel a Azure app Service Azure stack.  Éles környezetben ne használjon próbaverziókat. A Azure App Service on Azure Stack megköveteli, hogy a Microsoft .NET 3.5.1 SP1 legyen aktiválva az üzembe helyezéshez használt rendszerképben. Piactér – a Windows Server 2016-lemezképek nem rendelkeznek ezzel a szolgáltatással. Ezért egy Windows Server 2016 rendszerképet kell létrehoznia és használnia ezzel a szolgáltatással előre engedélyezve.

1. A **platform kiválasztása rendszerkép** mezőben válassza ki a Windows Server 2016 virtuális gép (VM) lemezképét a app Service felhő számítási erőforrás-szolgáltatóján elérhető rendszerképekből. Kattintson a **Tovább** gombra.

1. A következő oldalon:
     1. Adja meg a feldolgozói szerepkör virtuális gép rendszergazdai felhasználónevét és jelszavát.
     2. Adja meg a többi szerepkört a virtuális gép rendszergazdájának felhasználónevét és jelszavát.
     3. Kattintson a **Tovább** gombra.

    ![Adja meg a szerepkörök virtuálisgép-rendszergazdáit App Service telepítőben][16]

1. Az összefoglalás lapon:
    1. Ellenőrizze az elvégzett beállításokat. A módosítások elvégzéséhez használja az **előző gombokat** az előző lapok megkereséséhez.
    2. Ha a konfigurációk helyesek, jelölje be a jelölőnégyzetet.
    3. A telepítés elindításához kattintson a **tovább**gombra.

    ![A App Service-telepítőben végrehajtott Kijelölések összegzése][17]

1. A következő oldalon:
    1. A telepítési folyamat nyomon követése. A App Service on Azure Stack az alapértelmezett beállítások alapján körülbelül 60 percet vesz igénybe.
    2. Miután a telepítő befejezte a futtatást, válassza a **Kilépés**lehetőséget.

    ![App Service telepítőjének telepítési folyamatának követése][18]

## <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

> [!IMPORTANT]
> Ha a App Service RP-t egy SQL always on-példánnyal adta meg, akkor hozzá *kell* [adnia a appservice_hosting és appservice_metering adatbázist egy rendelkezésre állási csoporthoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database). Szinkronizálnia kell az adatbázisokat is, hogy megakadályozza a szolgáltatás elvesztését egy adatbázis-feladatátvétel esetén.

Ha úgy döntött, hogy egy meglévő virtuális hálózatra és egy belső IP-címet helyez üzembe a fájlkiszolgálón való kapcsolódáshoz, hozzá kell adnia egy kimenő biztonsági szabályt, amely engedélyezi az SMB-forgalmat a munkavégző alhálózat és a fájlkiszolgáló között. A felügyeleti portálon nyissa meg a WorkersNsg hálózati biztonsági csoportot, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:

- Forrás: bármely
- Forrásoldali porttartomány: *
- Cél: IP-címek
- Célként megadott IP-címtartomány: a fájlkiszolgáló IP-címeinek tartománya
- Célport tartománya: 445
- Protokoll: TCP
- Művelet: Engedélyezés
- Prioritás: 700
- Név: Outbound_Allow_SMB445

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Azure Stack telepítés App Service ellenőrzése

1. A Azure Stack felügyeleti portálon lépjen a **felügyelet-app Service**elemre.

1. Az Áttekintés területen az állapot alatt ellenőrizze, hogy az **állapot** megjeleníti-e az **összes szerepkört**.

    ![Az App Service felügyeletének áttekintése](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack"></a>App Service tesztelése Azure Stack

A App Service erőforrás-szolgáltató üzembe helyezése és regisztrálása után ellenőrizze, hogy a felhasználók telepíthetnek-e webes és API-alkalmazásokat.

> [!NOTE]
> Létre kell hoznia egy ajánlatot, amely a csomagon belül a Microsoft. Web névtérrel rendelkezik. Ezután egy bérlői előfizetéssel kell rendelkeznie, amely Előfizet erre az ajánlatra. További információért lásd az [ajánlat létrehozása](azure-stack-create-offer.md) és a [terv létrehozása](azure-stack-create-plan.md)című témakört.
>
> A App Servicet Azure Stack használó alkalmazások létrehozásához bérlői előfizetéssel *kell* rendelkeznie. A szolgáltatás-rendszergazda által a felügyeleti portálon elvégezhető képességek a App Service erőforrás-szolgáltatói felügyeletéhez kapcsolódnak. Ezek a képességek többek között a kapacitás hozzáadását, a telepítési források konfigurálását, valamint a feldolgozói rétegek és az SKU-ket adja
>
> A harmadik technikai előzetes verziótól kezdve a webes, API-és Azure Functions alkalmazások létrehozásához a bérlői portált kell használnia, és bérlői előfizetéssel kell rendelkeznie.

1. A Azure Stack bérlői portálon válassza az **+ erőforrás létrehozása** > **web és mobil** > **webalkalmazás**lehetőséget.

1. A **Web App (webalkalmazás** ) panelen írja be a kívánt nevet a **Web App (webalkalmazás** ) mezőbe.

1. Az **erőforráscsoport**területen válassza az **új**lehetőséget. Adjon meg egy nevet az **erőforráscsoport** mezőben.

1. Válassza ki **app Service terv/hely** > **hozzon létre új**elemet.

1. A **app Service terv** panelen adjon meg egy nevet a **app Service terv** mezőben.

1. Válassza ki az **árképzési szintet** > **Free-Shared** vagy **shared-shared** > válassza a > **OK** > **Létrehozás** **lehetőséget** .

1. Kevesebb mint egy percen belül az új webalkalmazás csempéje megjelenik az irányítópulton. Válassza ki a csempét.

1. A **Web App (webalkalmazás** ) panelen kattintson a **Tallózás** gombra az alkalmazás alapértelmezett webhelyének megtekintéséhez.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>WordPress-, DNN-vagy Django-webhely üzembe helyezése (opcionális)

1. A Azure Stack bérlői portálon válassza a **+** lehetőséget, lépjen az Azure Marketplace webhelyre, helyezzen üzembe egy Django-webhelyet, és várja meg a sikeres befejezést. A Django webes platform fájlrendszer alapú adatbázist használ. Nem igényel további erőforrás-szolgáltatót, például az SQL-t vagy a MySQL-t.

1. Ha egy MySQL erőforrás-szolgáltatót is üzembe helyezett, a WordPress-webhelyet üzembe helyezheti az Azure piactéren. Amikor a rendszer az adatbázis paramétereinek megadását kéri, írja be a felhasználónevet *felhasználó1\@Kiszolgáló1*néven, a felhasználónévvel és a kiszolgáló nevével.

1. Ha SQL Server erőforrás-szolgáltatót is üzembe helyezett, üzembe helyezhet egy DNN-webhelyet az Azure piactéren. Ha a rendszer az adatbázis paramétereinek megadását kéri, válasszon egy adatbázist az erőforrás-szolgáltatóhoz csatlakoztatott SQL Servert futtató számítógépen.

## <a name="next-steps"></a>További lépések

További rendszergazdai műveletek előkészítése a Azure Stack App Serviceához:

- [Kapacitástervezés](azure-stack-app-service-capacity-planning.md)
- [Központi telepítés forrásának konfigurálása](azure-stack-app-service-configure-deployment-sources.md)

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
