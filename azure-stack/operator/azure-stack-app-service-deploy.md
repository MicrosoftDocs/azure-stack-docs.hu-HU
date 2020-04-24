---
title: App Service üzembe helyezése Azure Stack központban
description: Megtudhatja, hogyan helyezheti üzembe a App Service az Azure Stack hub-ban.
author: bryanla
ms.topic: article
ms.date: 01/13/2020
ms.author: bryanla
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
ms.openlocfilehash: a1939049c3eb8c4440e37e58b6acfafa91881406
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77688967"
---
# <a name="deploy-app-service-in-azure-stack-hub"></a>App Service üzembe helyezése Azure Stack központban

Ez a cikk azt ismerteti, hogyan telepítheti App Service a Azure Stack hub-ban.

> [!IMPORTANT]
> Alkalmazza az 1910-es frissítést az Azure Stack hub integrált rendszerére, vagy telepítse a legújabb Azure Stack Development Kit (ASDK) a Azure App Service 1,8 telepítése előtt.

Lehetővé teheti, hogy a felhasználók webes és API-alkalmazásokat hozzanak létre. Ahhoz, hogy a felhasználók létre lehessen hozni ezeket az alkalmazásokat, a következőket kell tennie:

- Adja hozzá a [app Service erőforrás-szolgáltatót](azure-stack-app-service-overview.md) a Azure stack hub-telepítéshez a jelen cikkben ismertetett lépések alapján.
- A App Service erőforrás-szolgáltató telepítése után az ajánlatokat és a csomagokat is felveheti. A felhasználók ezután előfizethetnek a szolgáltatás beszerzésére és az alkalmazások létrehozásának megkezdésére.

> [!IMPORTANT]
> Az erőforrás-szolgáltató telepítőjének futtatása előtt győződjön meg arról, hogy az [első lépések előtt](azure-stack-app-service-before-you-get-started.md) követte a útmutatást, és olvassa el a 1,8-es kiadáshoz tartozó [kibocsátási megjegyzéseket](azure-stack-app-service-release-notes-update-eight.md) . A tartalom olvasásával megismerheti az új funkciókat, javításokat és az üzembe helyezést befolyásoló ismert problémákat.

## <a name="run-the-app-service-resource-provider-installer"></a>A App Service erőforrás-szolgáltató telepítőjének futtatása

A App Service erőforrás-szolgáltató telepítése legalább egy órát vesz igénybe. A szükséges időtartam attól függ, hogy hány szerepkör-példányt telepít. A telepítés során a telepítő a következő feladatokat futtatja:

- Hozzon létre egy BLOB-tárolót a megadott Azure Stack hub Storage-fiókban.
- Hozzon létre egy DNS-zónát és-bejegyzéseket a App Servicehoz.
- Regisztrálja a App Service erőforrás-szolgáltatót.
- Regisztrálja a App Service gyűjtemény elemeit.

App Service erőforrás-szolgáltató üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Futtassa a appservice. exe fájlt rendszergazdaként egy olyan számítógépről, amely hozzáfér az Azure Stack hub rendszergazdai Azure Resource Management végponthoz.

2. Válassza **a telepítés app Service vagy a frissítés a legújabb verzióra**lehetőséget.

    ![App Service telepítő][1]

3. Tekintse át és fogadja el a Microsoft szoftverlicenc-feltételeit, majd kattintson a **tovább**gombra.

4. Tekintse át és fogadja el a harmadik féltől származó licencfeltételeket, majd kattintson a **tovább**gombra.

5. Győződjön meg arról, hogy helyesek-e a App Service felhő konfigurációs adatai. Ha a ASDK telepítése során az alapértelmezett beállításokat használta, fogadja el az alapértelmezett értékeket. Ha azonban testreszabta a ASDK üzembe helyezésekor, vagy egy Azure Stack hub integrált rendszeren való üzembe helyezéskor, akkor az ebben az ablakban található értékeket kell szerkesztenie, hogy azok tükrözzék a különbségeket.

   Ha például a mycloud.com tartományi utótagot használja, akkor a Azure Stack hub-bérlő Azure Resource Manager végpontjának felügyeletre kell váltania. &lt;region&gt;. mycloud.com. Tekintse át ezeket a beállításokat, majd kattintson a **tovább** gombra a beállítások mentéséhez.

   ![App Service telepítő][2]

6. A következő App Service telepítő oldalon csatlakozni fog az Azure Stack hub-hoz:

    1. Válassza ki azt a kapcsolódási módszert, amelyet használni szeretne – **hitelesítő adat** vagy **szolgáltatásnév**
 
        - **Hitelesítőadat**
            - Ha Azure Active Directoryt (Azure AD) használ, adja meg az Azure AD-rendszergazda fiókját és jelszavát, amelyet az Azure Stack hub telepítésekor adott meg. Kattintson a **Csatlakozás** gombra.
            - Active Directory összevonási szolgáltatások (AD FS) (AD FS) használata esetén adja meg a rendszergazdai fiókját. Például: cloudadmin@azurestack.local. Adja meg a jelszót, majd válassza a **kapcsolat**lehetőséget.

        - **Szolgáltatásnév**
            - A **használt szolgáltatásnév** **tulajdonosi** jogosultságokkal kell rendelkeznie az **alapértelmezett szolgáltatói előfizetéshez** .
            - Adja meg az **egyszerű szolgáltatásnév azonosítóját**, a **tanúsítványfájl** és a **jelszót** , majd válassza a **kapcsolat**lehetőséget.

    1. **Azure stack hub-előfizetések**területen válassza ki az **alapértelmezett szolgáltatói előfizetést**.  A **Azure stack Hub Azure app Service** az **alapértelmezett szolgáltatói előfizetésben**kell központilag telepíteni.

    1. A **Azure stack hub helyein**válassza ki azt a helyet, amely a központilag üzembe helyezett régiónak felel meg. Válassza például a **Local (helyi** ) lehetőséget, ha üzembe helyezi a ASDK.

    ![App Service telepítő][3]

7. Most már üzembe helyezhet egy meglévő virtuális hálózatot, amelyet [ezekkel a lépésekkel](azure-stack-app-service-before-you-get-started.md#virtual-network)konfigurált, vagy engedélyezheti, hogy a app Service telepítőjének hozzon létre egy új virtuális hálózatot és alhálózatokat. VNet létrehozásához kövesse az alábbi lépéseket:

   a. Válassza a **VNet létrehozása alapértelmezett beállításokkal**lehetőséget, fogadja el az alapértelmezett beállításokat, majd kattintson a **tovább**gombra.

   b. Másik lehetőségként válassza a **meglévő VNet és alhálózatok használata**lehetőséget. Hajtsa végre a következő műveleteket:

     - Válassza ki a virtuális hálózatot tartalmazó **erőforráscsoportot** .
     - Válassza ki azt a **Virtual Network** nevet, amelyet központilag telepíteni szeretne.
     - Válassza ki a megfelelő **alhálózati** értékeket minden szükséges szerepkör-alhálózathoz.
     - Kattintson a **Tovább** gombra.

   ![App Service telepítő][4]

8. Adja meg a fájlmegosztás adatait, majd kattintson a **tovább**gombra. A fájlmegosztás címének a teljes tartománynevet (FQDN) vagy a fájlkiszolgáló IP-címét kell használnia. Például: \\\appservicefileserver.local.cloudapp.azurestack.external\websites vagy \\\10.0.0.1\websites.  Ha olyan fájlkiszolgálón használ, amely tartományhoz van csatlakoztatva, meg kell adnia a teljes felhasználónevet, beleértve a tartományt is. Például: myfileserverdomain\FileShareOwner.

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
    | **Alapértelmezett SSL-tanúsítványfájl App Service** | \_. appservice. local. AzureStack. external. pfx |
    | **App Service API SSL-tanúsítványfájl** | API. appservice. local. AzureStack. external. pfx |
    | **App Service közzétevő SSL-tanúsítványfájl** | FTP. appservice. local. AzureStack. external. pfx |

    Ha a tanúsítványok létrehozásakor eltérő tartományi utótagot használt, a tanúsítványfájl neve nem a helyi nevet használja *. AzureStack. external*. Ehelyett használja az egyéni tartomány adatait.

    ![App Service telepítő][10]

11. Adja meg a App Service erőforrás-szolgáltató adatbázisának üzemeltetéséhez használt kiszolgálópéldány SQL Server adatait, majd kattintson a **tovább**gombra. A telepítő ellenőrzi, hogy az SQL-kapcsolatok tulajdonságai megtörténtek-e.<br><br>A folytatás előtt a App Service telepítő megkísérli a SQL Server kapcsolat tesztelését. Ha meglévő virtuális hálózatra telepíti, a kapcsolat tesztelése sikertelen lehet. Figyelmeztetést kap, és a folytatáshoz a rendszer kéri. Ha a SQL Server adatok helyesek, folytassa a telepítést.

    ![App Service telepítő][11]

12. Tekintse át a szerepkör-példány és az SKU beállításait. Az alapértelmezett érték a példányok minimális száma, valamint az egyes szerepkörök minimális SKU-jának ASDK-telepítésben való feltöltése. A vCPU és a memória követelményeinek összefoglalása az üzemelő példány megtervezéséhez nyújt segítséget. A kijelölések után válassza a **tovább**lehetőséget.

    >[!NOTE]
    >Éles környezetekben az [Azure stack Hub Azure app Service kiszolgálói szerepköreinek kapacitásának megtervezése](azure-stack-app-service-capacity-planning.md)című témakör útmutatását követve.

    | Szerepkör | Minimális példányszám | Minimális SKU | Megjegyzések |
    | --- | --- | --- | --- |
    | Tartományvezérlő | 1 | Standard_A2 – (2 vCPU, 3584 MB) | Felügyeli és karbantartja a App Service felhő állapotát. |
    | Kezelés | 1 | Standard_A2 – (2 vCPU, 3584 MB) | A App Service Azure Resource Manager és az API-végpontokat, a portál-bővítményeket (rendszergazda, bérlő, functions portál) és az adatszolgáltatást kezeli. A feladatátvétel támogatásához növelte a javasolt példányokat 2-ra. |
    | Közzétevő | 1 | Standard_A1 – (1 vCPU, 1792 MB) | Tartalmat tesz közzé FTP-n keresztül és webes telepítéssel. |
    | Előtér | 1 | Standard_A1 – (1 vCPU, 1792 MB) | Átirányítja a kérelmeket App Service alkalmazásokba. |
    | Megosztott feldolgozók | 1 | Standard_A1 – (1 vCPU, 1792 MB) | Webes vagy API-alkalmazásokat, valamint Azure Functions alkalmazásokat üzemeltet. Előfordulhat, hogy további példányokat szeretne hozzáadni. Operátorként megadhatja az ajánlatát, és kiválaszthatja az SKU-szintet. A rétegeknek legalább egy vCPU kell rendelkezniük. |

    ![App Service telepítő][13]

    >[!NOTE]
    >**A Windows Server 2016 Core nem támogatott platform-rendszerkép a Azure App Service Azure Stack hub-ban való használatához.  Éles környezetben ne használjon próbaverziókat.**

13. A **platform kiválasztása rendszerkép** mezőben válassza ki a Windows Server 2016 virtuális gép (VM) lemezképét a app Service felhő számítási erőforrás-szolgáltatójában elérhető rendszerképekből. Kattintson a **Tovább** gombra.

14. A következő App Service telepítő lapon kövesse az alábbi lépéseket:

     a. Adja meg a feldolgozói szerepkör virtuális gép rendszergazdai felhasználónevét és jelszavát.

     b. Adja meg a többi szerepkört a virtuális gép rendszergazdájának felhasználónevét és jelszavát.

     c. Kattintson a **Tovább** gombra.

    ![App Service telepítő][15]

15. A App Service-telepítő összegzése lapon kövesse az alábbi lépéseket:

    a. Ellenőrizze az elvégzett beállításokat. A módosítások elvégzéséhez használja az **előző gombokat** az előző lapok megkereséséhez.

    b. Ha a konfigurációk helyesek, jelölje be a jelölőnégyzetet.

    c. A telepítés elindításához kattintson a **tovább**gombra.

    ![App Service telepítő][16]

16. A következő App Service telepítő lapon kövesse az alábbi lépéseket:

    a. A telepítési folyamat nyomon követése. A App Service on Azure Stack hub az alapszintű Windows 2016 Datacenter-rendszerkép alapértelmezett kiválasztása és kora alapján akár 240 percet is igénybe vehet.

    b. A telepítő sikeres befejeződése után válassza a **Kilépés**lehetőséget.

    ![App Service telepítő][17]

## <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

> [!IMPORTANT]
> Ha a App Service RP-t egy SQL always on-példánnyal adta **must** meg, akkor [a appservice_hosting és appservice_metering adatbázisokat hozzá kell adnia egy rendelkezésre állási csoporthoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) , és szinkronizálnia kell az adatbázisokat, hogy megakadályozza a szolgáltatás elvesztését egy adatbázis-feladatátvétel esetén.

Ha meglévő virtuális hálózatra telepít üzembe, és belső IP-cím használatával csatlakozik a fájlkiszolgáló kiszolgálóhoz, hozzá kell adnia egy kimenő biztonsági szabályt. Ez a szabály engedélyezi az SMB-forgalmat a munkavégző alhálózat és a fájlkiszolgáló között. A felügyeleti portálon nyissa meg a WorkersNsg hálózati biztonsági csoportot, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:

- Forrás: bármely
- Forrásoldali porttartomány: *
- Cél: IP-címek
- Célként megadott IP-címtartomány: a fájlkiszolgáló IP-címeinek tartománya
- Célport tartománya: 445
- Protokoll: TCP
- Művelet: Engedélyezés
- Prioritás: 700
- Név: Outbound_Allow_SMB445

## <a name="validate-the-app-service-on-azure-stack-hub-installation"></a>Azure Stack hub-telepítés App Service ellenőrzése

1. Az Azure Stack hub felügyeleti portálján lépjen a **felügyelet – app Service**elemre.

2. Az Áttekintés területen az állapot alatt ellenőrizze, hogy az **állapot** megjeleníti-e az **összes szerepkört**.

    ![App Service felügyelet](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack-hub"></a>App Service tesztelése Azure Stack központban

A App Service erőforrás-szolgáltató üzembe helyezése és regisztrálása után ellenőrizze, hogy a felhasználók telepíthetnek-e webes és API-alkalmazásokat.

>[!NOTE]
>Létre kell hoznia egy ajánlatot, amely a tervben a Microsoft. Web névtérrel rendelkezik. Szüksége lesz egy bérlői előfizetésre is, amely előfizet az ajánlatra. További információért lásd az [ajánlat létrehozása](azure-stack-create-offer.md) és a [terv létrehozása](azure-stack-create-plan.md)című témakört.
>
>A App Service Azure Stack hub-on használó alkalmazások létrehozásához bérlői előfizetéssel *kell* rendelkeznie. A szolgáltatás-rendszergazda által a felügyeleti portálon elvégezhető feladatok a App Service erőforrás-szolgáltatói felügyeletéhez kapcsolódnak. Ez magában foglalja a kapacitás hozzáadását, a telepítési források konfigurálását, valamint a feldolgozói szintek és SKU-ket hozzáadását.
>
>Webes, API-és Azure Functions-alkalmazások létrehozásához a felhasználói portált kell használnia, és bérlői előfizetéssel kell rendelkeznie.
>

A teszt webalkalmazás létrehozásához kövesse az alábbi lépéseket:

1. Az Azure stack hub felhasználói portálon válassza az **+ erőforrás** > létrehozása**web és mobil** > **webalkalmazás**lehetőséget.

2. A **Web App (webalkalmazás**) területen adjon meg egy nevet a **webalkalmazásban**.

3. Az **erőforráscsoport**területen válassza az **új**lehetőséget. Adja meg az **erőforráscsoport**nevét.

4. Válassza a **app Service terv/hely** > **létrehozása új**lehetőséget.

5. A **app Servicei csomag**területen adja meg a **app Service terv**nevét.

6.  > Válassza **a****Free-Shared** vagy **a Shared-** > Shared**Select** > **OK** > **létrehozása**lehetőséget.

7. Megjelenik egy csempe az új webalkalmazáshoz az irányítópulton. Válassza ki a csempét.

8. A **webalkalmazás**lapon válassza a **Tallózás** lehetőséget az alkalmazás alapértelmezett webhelyének megtekintéséhez.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>WordPress-, DNN-vagy Django-webhely üzembe helyezése (opcionális)

1. Az Azure Stack hub felhasználói portálon válassza ki **+** az Azure Marketplace-t, telepítsen egy Django-webhelyet, és várjon, amíg a telepítés befejeződik. A Django webes platform fájlrendszer alapú adatbázist használ. Nem igényel további erőforrás-szolgáltatót, például az SQL-t vagy a MySQL-t.

2. Ha egy MySQL erőforrás-szolgáltatót is üzembe helyezett, a piactéren telepítheti a WordPress-webhelyeket is. Amikor a rendszer az adatbázis paramétereinek megadását kéri, írja be a felhasználónevet *Felhasználó1\@Kiszolgáló1*néven, a felhasználónévvel és a kiszolgáló nevével.

3. Ha SQL Server erőforrás-szolgáltatót is üzembe helyezett, üzembe helyezhet egy DNN-webhelyet a piactéren. Ha a rendszer az adatbázis paramétereinek megadását kéri, válasszon egy adatbázist az erőforrás-szolgáltatóhoz csatlakoztatott SQL Servert futtató számítógépen.

## <a name="next-steps"></a>További lépések

Felkészülés további rendszergazdai műveletekre Azure Stack hub App Service:

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
