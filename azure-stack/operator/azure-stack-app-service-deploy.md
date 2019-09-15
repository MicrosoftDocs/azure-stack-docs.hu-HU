---
title: App Service üzembe helyezése Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe App Service a Azure Stackban.
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
ms.date: 08/29/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 219d8bcf884945353b08186324edc23feb028964
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974962"
---
# <a name="deploy-app-service-in-azure-stack"></a>App Service üzembe helyezése Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ez a cikk azt ismerteti, hogyan helyezhetők üzembe App Service a Azure Stackban.

> [!IMPORTANT]
> Alkalmazza a 1907-es frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack Development Kitt (ASDK) a Azure App Service 1,7 üzembe helyezése előtt.

Lehetővé teheti, hogy a felhasználók webes és API-alkalmazásokat hozzanak létre. Ahhoz, hogy a felhasználók létre lehessen hozni ezeket az alkalmazásokat, a következőket kell tennie:

- Adja hozzá a [app Service erőforrás-szolgáltatót](azure-stack-app-service-overview.md) a Azure stack üzembe helyezéséhez a jelen cikkben ismertetett lépések alapján.
- A App Service erőforrás-szolgáltató telepítése után az ajánlatokat és a csomagokat is felveheti. A felhasználók ezután előfizethetnek a szolgáltatás beszerzésére és az alkalmazások létrehozásának megkezdésére.

> [!IMPORTANT]
> Az erőforrás-szolgáltató telepítőjének futtatása előtt győződjön meg arról, hogy az [első lépések előtt](azure-stack-app-service-before-you-get-started.md) követte a útmutatást, és olvassa el a 1,7-es kiadáshoz tartozó [kibocsátási megjegyzéseket](azure-stack-app-service-release-notes-update-seven.md) . A tartalom olvasásával megismerheti az új funkciókat, javításokat és az üzembe helyezést befolyásoló ismert problémákat.

## <a name="run-the-app-service-resource-provider-installer"></a>A App Service erőforrás-szolgáltató telepítőjének futtatása

A App Service erőforrás-szolgáltató telepítése legalább egy órát vesz igénybe. A szükséges időtartam attól függ, hogy hány szerepkör-példányt telepít. A telepítés során a telepítő a következő feladatokat futtatja:

- Hozzon létre egy BLOB-tárolót a megadott Azure Stack Storage-fiókban.
- Hozzon létre egy DNS-zónát és-bejegyzéseket a App Servicehoz.
- Regisztrálja a App Service erőforrás-szolgáltatót.
- Regisztrálja a App Service gyűjtemény elemeit.

App Service erőforrás-szolgáltató üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Futtassa a appservice. exe fájlt rendszergazdaként egy olyan számítógépről, amely hozzáfér a Azure Stack felügyeleti Azure erőforrás-kezelési végponthoz.

2. Válassza **a telepítés app Service vagy a frissítés a legújabb verzióra**lehetőséget.

    ![App Service telepítő][1]

3. Tekintse át és fogadja el a Microsoft szoftverlicenc-feltételeit, majd kattintson a **tovább**gombra.

4. Tekintse át és fogadja el a harmadik féltől származó licencfeltételeket, majd kattintson a **tovább**gombra.

5. Győződjön meg arról, hogy helyesek-e a App Service felhő konfigurációs adatai. Ha a ASDK telepítése során az alapértelmezett beállításokat használta, fogadja el az alapértelmezett értékeket. Ha azonban a ASDK telepítésekor vagy egy Azure Stack integrált rendszeren való üzembe helyezéskor testreszabta a beállításokat, akkor az ebben az ablakban található értékeket kell szerkesztenie, hogy azok tükrözzék a különbségeket.

   Ha például a mycloud.com tartományi utótagot használja, akkor a Azure Stack bérlő Azure Resource Manager végpontjának felügyeletre kell váltania. &lt;region&gt;. mycloud.com. Tekintse át ezeket a beállításokat, majd kattintson a **tovább** gombra a beállítások mentéséhez.

   ![App Service telepítő][2]

6. A következő App Service telepítő lapon kövesse az alábbi lépéseket:

    a. Válassza a **Kapcsolódás** lehetőséget a **Azure stack előfizetések**mellett.

   - Ha Azure Active Directoryt (Azure AD-t) használ, adja meg a Azure Stack telepítésekor megadott Azure AD-rendszergazdai fiókot és jelszót. Válassza **a bejelentkezés**lehetőséget.
   - Active Directory összevonási szolgáltatások (AD FS) (AD FS) használata esetén adja meg a rendszergazdai fiókját. Például: cloudadmin@azurestack.local. Adja meg a jelszót, majd válassza a **Bejelentkezés**lehetőséget.

   b. **Azure stack előfizetések**területen válassza ki az **alapértelmezett szolgáltatói előfizetést**.

     > [!IMPORTANT]
     > App Service **az** **alapértelmezett szolgáltatói előfizetéshez**kell központilag telepíteni.

   c. A **Azure stack helyein**válassza ki azt a helyet, amely a központilag üzembe helyezett régiónak felel meg. Válassza például a **Local (helyi** ) lehetőséget, ha üzembe helyezi a ASDK.

    ![App Service telepítő][3]

7. Most már üzembe helyezhet egy meglévő virtuális hálózatot, amelyet [ezekkel a lépésekkel](azure-stack-app-service-before-you-get-started.md#virtual-network)konfigurált, vagy engedélyezheti, hogy a app Service telepítőjének hozzon létre egy új virtuális hálózatot és alhálózatokat. VNet létrehozásához kövesse az alábbi lépéseket:

   a. Válassza a **VNet létrehozása alapértelmezett beállításokkal**lehetőséget, fogadja el az alapértelmezett beállításokat, majd kattintson a **tovább**gombra.

   b. Másik lehetőségként válassza a **meglévő VNet és alhálózatok használata**lehetőséget. Hajtsa végre a következő műveleteket:

     - Válassza ki a virtuális hálózatot tartalmazó **erőforráscsoportot** .
     - Válassza ki azt a **Virtual Network** nevet, amelyet központilag telepíteni szeretne.
     - Válassza ki a megfelelő **alhálózati** értékeket minden szükséges szerepkör-alhálózathoz.
     - Kattintson a **Tovább** gombra.

   ![App Service telepítő][4]

8. Adja meg a fájlmegosztás adatait, majd kattintson a **tovább**gombra. A fájlmegosztás címének a teljes tartománynevet (FQDN) vagy a fájlkiszolgáló IP-címét kell használnia. Például \\: \appservicefileserver.local.cloudapp.azurestack.external\websites vagy \\\10.0.0.1\websites.  Ha olyan fájlkiszolgálón használ, amely tartományhoz van csatlakoztatva, meg kell adnia a teljes felhasználónevet, beleértve a tartományt is. Például: myfileserverdomain\FileShareOwner.

   >[!NOTE]
   >A telepítő megkísérli a fájlmegosztás kapcsolatának tesztelését a folytatás előtt. Ha azonban egy meglévő virtuális hálózatra telepíti, a kapcsolat tesztelése sikertelen lehet. Figyelmeztetést kap, és a folytatáshoz a rendszer kéri. Ha a fájlmegosztás adatai helyesek, folytassa a telepítést.

   ![App Service telepítő][7]

9. A következő App Service telepítő lapon kövesse az alábbi lépéseket:

   a. Az **Identity Application ID (identitás-alkalmazás azonosítója** ) mezőben adja meg az identitáshoz használt alkalmazás GUID azonosítóját (az Azure ad-ból).

   b. Az **Identity Application tanúsítványfájl** mezőben adja meg a tanúsítványfájl helyét (vagy tallózással keresse meg a fájlt).

   c. Adja meg a tanúsítvány jelszavát a **személyazonossági alkalmazás tanúsítványának jelszava** mezőben. Ez a jelszó azt jelzi, hogy mikor használta a parancsfájlt a tanúsítványok létrehozásához.

   d. A **Azure Resource Manager legfelső szintű tanúsítvány fájl** mezőjébe írja be (vagy tallózással keresse meg) a tanúsítványfájl helyét.

   e. Kattintson a **Tovább** gombra.

   ![App Service telepítő][9]

10. A három tanúsítványfájl-mező esetében válassza a **Tallózás** lehetőséget, és navigáljon a megfelelő tanúsítványfájl-fájlhoz. Minden tanúsítványhoz meg kell adnia a jelszót. Ezek a tanúsítványok a [szükséges tanúsítványok létrehozása lépésben](azure-stack-app-service-before-you-get-started.md#get-certificates)létrehozott tanúsítványokat is magukban foglalják. Az összes információ beírása után válassza a **tovább** lehetőséget.

    | Box | Példa tanúsítványfájl-fájlnévre |
    | --- | --- |
    | **Alapértelmezett SSL-tanúsítványfájl App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **App Service API SSL-tanúsítványfájl** | api.appservice.local.AzureStack.external.pfx |
    | **App Service közzétevő SSL-tanúsítványfájl** | ftp.appservice.local.AzureStack.external.pfx |

    Ha a tanúsítványok létrehozásakor eltérő tartományi utótagot használt, a tanúsítványfájl neve nem a helyi nevet használja *. AzureStack. external*. Ehelyett használja az egyéni tartomány adatait.

    ![App Service telepítő][10]

11. Adja meg a App Service erőforrás-szolgáltató adatbázisának üzemeltetéséhez használt kiszolgálópéldány SQL Server adatait, majd kattintson a **tovább**gombra. A telepítő ellenőrzi, hogy az SQL-kapcsolatok tulajdonságai megtörténtek-e.<br><br>A folytatás előtt a App Service telepítő megkísérli a SQL Server kapcsolat tesztelését. Ha meglévő virtuális hálózatra telepíti, a kapcsolat tesztelése sikertelen lehet. Figyelmeztetést kap, és a folytatáshoz a rendszer kéri. Ha a SQL Server adatok helyesek, folytassa a telepítést.

    ![App Service telepítő][11]

12. Tekintse át a szerepkör-példány és az SKU beállításait. Az alapértelmezett érték a példányok minimális száma, valamint az egyes szerepkörök minimális SKU-jának ASDK-telepítésben való feltöltése. A vCPU és a memória követelményeinek összefoglalása az üzemelő példány megtervezéséhez nyújt segítséget. A kijelölések után válassza a **tovább**lehetőséget.

    >[!NOTE]
    >Éles környezetekben a [Azure Stack Azure app Service kiszolgálói szerepkörök kapacitásának megtervezése](azure-stack-app-service-capacity-planning.md)című témakör útmutatását követve.

    | Role | Minimális példányszám | Minimális SKU | Megjegyzések |
    | --- | --- | --- | --- |
    | Vezérlő | 1 | Standard_A2-(2 vCPU, 3584 MB) | Felügyeli és karbantartja a App Service felhő állapotát. |
    | Kezelés | 1 | Standard_A2-(2 vCPU, 3584 MB) | A App Service Azure Resource Manager és az API-végpontokat, a portál-bővítményeket (rendszergazda, bérlő, functions portál) és az adatszolgáltatást kezeli. A feladatátvétel támogatásához növelte a javasolt példányokat 2-ra. |
    | Kiadó | 1 | Standard_A1-(1 vCPU, 1792 MB) | Tartalmat tesz közzé FTP-n keresztül és webes telepítéssel. |
    | Előtér | 1 | Standard_A1-(1 vCPU, 1792 MB) | Átirányítja a kérelmeket App Service alkalmazásokba. |
    | Megosztott feldolgozók | 1 | Standard_A1-(1 vCPU, 1792 MB) | Webes vagy API-alkalmazásokat, valamint Azure Functions alkalmazásokat üzemeltet. Előfordulhat, hogy további példányokat szeretne hozzáadni. Operátorként megadhatja az ajánlatát, és kiválaszthatja az SKU-szintet. A rétegeknek legalább egy vCPU kell rendelkezniük. |

    ![App Service telepítő][13]

    >[!NOTE]
    >**A Windows Server 2016 Core nem támogatott platform-rendszerkép, amellyel a Azure App Service Azure Stack.  Éles környezetben ne használjon próbaverziókat.**

13. A **platform kiválasztása rendszerkép** mezőben válassza ki a Windows Server 2016 virtuális gép (VM) lemezképét a app Service felhő számítási erőforrás-szolgáltatójában elérhető rendszerképekből. Kattintson a **Tovább** gombra.

14. A következő App Service telepítő lapon kövesse az alábbi lépéseket:

     a. Adja meg a feldolgozói szerepkör virtuális gép rendszergazdai felhasználónevét és jelszavát.

     b. Adja meg a többi szerepkört a virtuális gép rendszergazdájának felhasználónevét és jelszavát.

     c. Kattintson a **Tovább** gombra.

    ![App Service telepítő][15]

15. A App Service-telepítő összegzése lapon kövesse az alábbi lépéseket:

    a. Ellenőrizze az elvégzett beállításokat. A módosítások elvégzéséhez használja az előző gombokat az előző lapok megkereséséhez.

    b. Ha a konfigurációk helyesek, jelölje be a jelölőnégyzetet.

    c. A telepítés elindításához kattintson a **tovább**gombra.

    ![App Service telepítő][16]

16. A következő App Service telepítő lapon kövesse az alábbi lépéseket:

    a. A telepítési folyamat nyomon követése. A App Service on Azure Stack az alapértelmezett beállítások alapján 60 percet vesz igénybe.

    b. A telepítő sikeres befejeződése után válassza a **Kilépés**lehetőséget.

    ![App Service telepítő][17]

## <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

> [!IMPORTANT]
> Ha a App Service RP-t egy SQL always on-példánnyal adta meg, akkor a [appservice_hosting és a appservice_metering-adatbázist hozzá kell adnia egy rendelkezésre állási csoporthoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) , és szinkronizálnia kell az adatbázisokat, hogy elkerülje a szolgáltatás elvesztését a következő esetekben: adatbázis-feladatátvétel.

Ha meglévő virtuális hálózatra telepít üzembe, és belső IP-cím használatával csatlakozik a fájlkiszolgáló kiszolgálóhoz, hozzá kell adnia egy kimenő biztonsági szabályt. Ez a szabály engedélyezi az SMB-forgalmat a munkavégző alhálózat és a fájlkiszolgáló között. A felügyeleti portálon nyissa meg a WorkersNsg hálózati biztonsági csoportot, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:

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

    ![App Service felügyelet](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack"></a>App Service tesztelése Azure Stack

A App Service erőforrás-szolgáltató üzembe helyezése és regisztrálása után ellenőrizze, hogy a felhasználók telepíthetnek-e webes és API-alkalmazásokat.

>[!NOTE]
>Létre kell hoznia egy ajánlatot, amely a tervben a Microsoft. Web névtérrel rendelkezik. Szüksége lesz egy bérlői előfizetésre is, amely előfizet az ajánlatra. További információért lásd az [ajánlat létrehozása](azure-stack-create-offer.md) és a [terv létrehozása](azure-stack-create-plan.md)című témakört.
>
>A App Servicet Azure Stack használó alkalmazások létrehozásához bérlői előfizetéssel *kell* rendelkeznie. A szolgáltatás-rendszergazda által a felügyeleti portálon elvégezhető feladatok a App Service erőforrás-szolgáltatói felügyeletéhez kapcsolódnak. Ez magában foglalja a kapacitás hozzáadását, a telepítési források konfigurálását, valamint a feldolgozói szintek és SKU-ket hozzáadását.
>
>Webes, API-és Azure Functions-alkalmazások létrehozásához a bérlői portált kell használnia, és bérlői előfizetéssel kell rendelkeznie.
>

A teszt webalkalmazás létrehozásához kövesse az alábbi lépéseket:

1. A Azure stack felhasználói portálon válassza az **+ erőforrás** > létrehozása**web és mobil** > **webalkalmazás**lehetőséget.

2. A **Web App (webalkalmazás**) területen adjon meg egy nevet a **webalkalmazásban**.

3. Az **erőforráscsoport**területen válassza az **új**lehetőséget. Adja meg az **erőforráscsoport**nevét.

4. Válassza a **app Service terv/hely** > **létrehozása új**lehetőséget.

5. A **app Servicei csomag**területen adja meg a **app Service terv**nevét.

6. **Válassza a** >  > **Free-Shared** vagy **a Shared-** Shared Select ok > létrehozásalehetőséget > .

7. Megjelenik egy csempe az új webalkalmazáshoz az irányítópulton. Válassza ki a csempét.

8. A **webalkalmazás**lapon válassza a **Tallózás** lehetőséget az alkalmazás alapértelmezett webhelyének megtekintéséhez.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>WordPress-, DNN-vagy Django-webhely üzembe helyezése (opcionális)

1. A Azure stack bérlői portálon válassza **+** ki az Azure Marketplace-t, telepítsen egy Django-webhelyet, és várjon, amíg a telepítés befejeződik. A Django webes platform fájlrendszer alapú adatbázist használ. Nem igényel további erőforrás-szolgáltatót, például az SQL-t vagy a MySQL-t.

2. Ha egy MySQL erőforrás-szolgáltatót is üzembe helyezett, a piactéren telepítheti a WordPress-webhelyeket is. Amikor a rendszer az adatbázis paramétereinek megadását kéri, írja be a felhasználónevet *Felhasználó1\@Kiszolgáló1*néven, a felhasználónévvel és a kiszolgáló nevével.

3. Ha SQL Server erőforrás-szolgáltatót is üzembe helyezett, üzembe helyezhet egy DNN-webhelyet a piactéren. Ha a rendszer az adatbázis paramétereinek megadását kéri, válasszon egy adatbázist az erőforrás-szolgáltatóhoz csatlakoztatott SQL Servert futtató számítógépen.

## <a name="next-steps"></a>További lépések

További rendszergazdai műveletek előkészítése a Azure Stack App Serviceához:

- [Kapacitás megtervezése](azure-stack-app-service-capacity-planning.md)
- [Központi telepítési források konfigurálása](azure-stack-app-service-configure-deployment-sources.md)

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
