---
title: App Service üzembe helyezése az Azure Stack Hubban
description: Megtudhatja, hogyan helyezheti üzembe a App Service az Azure Stack hub-ban.
author: bryanla
ms.topic: article
ms.date: 05/05/2020
ms.author: bryanla
ms.reviewer: anwestg
ms.lastreviewed: 04/13/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: d8b5be96bf3e150308faf01c161d20e180beeb69
ms.sourcegitcommit: 53b0dde60a6435936a5e0cb9e931245f262d637a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91106991"
---
# <a name="deploy-app-service-in-azure-stack-hub"></a>App Service üzembe helyezése az Azure Stack Hubban

[!INCLUDE [Azure Stack hub update reminder](../includes/app-service-hub-update-banner.md)]

> [!IMPORTANT]
> Az erőforrás-szolgáltató telepítőjének futtatása előtt végre kell hajtania a lépéseket az [első lépések előtt](azure-stack-app-service-before-you-get-started.md)

::: zone pivot="state-connected"
Ebből a cikkből megtudhatja, hogyan helyezheti üzembe a App Servicet Azure Stack hub-ban, amely lehetővé teszi, hogy a felhasználók webes, API-és Azure Functions alkalmazásokat hozzanak létre. A következőket kell tennie:

- Adja hozzá a [app Service erőforrás-szolgáltatót](azure-stack-app-service-overview.md) a Azure stack hub-telepítéshez a jelen cikkben ismertetett lépések alapján.
- A App Service erőforrás-szolgáltató telepítése után az ajánlatokat és a csomagokat is felveheti. A felhasználók ezután előfizethetnek a szolgáltatás beszerzésére és az alkalmazások létrehozásának megkezdésére.

## <a name="run-the-app-service-resource-provider-installer"></a>A App Service erőforrás-szolgáltató telepítőjének futtatása

A App Service erőforrás-szolgáltató telepítése legalább egy órát vesz igénybe. A szükséges időtartam attól függ, hogy hány szerepkör-példányt telepít. A telepítés során a telepítő a következő feladatokat futtatja:

- Regisztrálja a szükséges erőforrás-szolgáltatókat az alapértelmezett szolgáltatói előfizetésben
- Közreműködői hozzáférést biztosít a App Service Identity alkalmazáshoz
- Erőforráscsoport és virtuális hálózat létrehozása (ha szükséges)
- Storage-fiókok és-tárolók létrehozása App Service telepítési összetevőkhöz, a használati szolgáltatásokhoz és az erőforrás-hidratációhoz
- App Service összetevők letöltése és a App Service Storage-fiókba való feltöltése
- A App Service üzembe helyezése
- A használati szolgáltatás regisztrálása
- DNS-bejegyzések létrehozása App Servicehoz
- Regisztrálja a App Service felügyeleti és bérlői erőforrás-szolgáltatót
- Dokumentumtár-elemek regisztrálása – web, API, függvényalkalmazás, App Service Plan, WordPress, DNN, Orchard és Django alkalmazások

App Service erőforrás-szolgáltató üzembe helyezéséhez kövesse az alábbi lépéseket:

1. appservice.exe rendszergazdaként futtasson egy olyan számítógépről, amely hozzáfér az Azure Stack hub rendszergazdai Azure Resource Management-végponthoz.

2. Válassza **a telepítés app Service vagy a frissítés a legújabb verzióra**lehetőséget.

    ![Képernyőfelvétel: a Azure App Service-telepítő fő képernyője.][1]

3. Tekintse át és fogadja el a Microsoft szoftverlicenc-feltételeit, majd kattintson a **tovább**gombra.

4. Tekintse át és fogadja el a harmadik féltől származó licencfeltételeket, majd kattintson a **tovább**gombra.

5. Győződjön meg arról, hogy helyesek-e a App Service felhő konfigurációs adatai. Ha a ASDK telepítése során az alapértelmezett beállításokat használta, fogadja el az alapértelmezett értékeket. Ha azonban testreszabta a ASDK üzembe helyezésekor, vagy egy Azure Stack hub integrált rendszeren való üzembe helyezéskor, akkor az ebben az ablakban található értékeket kell szerkesztenie, hogy azok tükrözzék a különbségeket.

   Ha például a mycloud.com tartományi utótagot használja, akkor a Azure Stack hub-bérlő Azure Resource Manager végpontjának felügyeletre kell váltania. &lt; region &gt; . mycloud.com. Tekintse át ezeket a beállításokat, majd kattintson a **tovább** gombra a beállítások mentéséhez.

   ![Képernyőkép, amely a App Service ARM-végpontok megadására szolgáló képernyőt jeleníti meg.][2]

6. A következő App Service telepítő oldalon csatlakozni fog az Azure Stack hub-hoz:

    1. Válassza ki azt a kapcsolódási módszert, amelyet használni szeretne – **hitelesítő adat** vagy **szolgáltatásnév**
 
        - **Hitelesítőadat**
            - Ha Azure Active Directoryt (Azure AD) használ, adja meg az Azure AD-rendszergazda fiókját és jelszavát, amelyet az Azure Stack hub telepítésekor adott meg. Kattintson a **Csatlakozás** gombra.
            - Active Directory összevonási szolgáltatások (AD FS) (AD FS) használata esetén adja meg a rendszergazdai fiókját. Például: cloudadmin@azurestack.local. Adja meg a jelszót, majd válassza a **kapcsolat**lehetőséget.

        - **Szolgáltatásnév**
            - A **használt szolgáltatásnév** **tulajdonosi** jogosultságokkal kell rendelkeznie az **alapértelmezett szolgáltatói előfizetéshez** .
            - Adja meg az **egyszerű szolgáltatásnév azonosítóját**, a **tanúsítványfájl**és a **jelszót** , majd válassza a **kapcsolat**lehetőséget.

    1. **Azure stack hub-előfizetések**területen válassza ki az **alapértelmezett szolgáltatói előfizetést**.  A **Azure stack Hub Azure app Service** az **alapértelmezett szolgáltatói előfizetésben**kell központilag telepíteni.

    1. A **Azure stack hub helyein**válassza ki azt a helyet, amely a központilag üzembe helyezett régiónak felel meg. Válassza például a **Local (helyi** ) lehetőséget, ha üzembe helyezi a ASDK.

    ![Képernyőfelvétel: Itt adhatja meg a Azure Stack hub előfizetési adatait a App Service telepítőben.][3]

7. Most már üzembe helyezhet egy meglévő virtuális hálózatot, amelyet [ezekkel a lépésekkel](azure-stack-app-service-before-you-get-started.md#virtual-network)konfigurált, vagy engedélyezheti, hogy a app Service telepítőjének hozzon létre egy új virtuális hálózatot és alhálózatokat. VNet létrehozásához kövesse az alábbi lépéseket:

   a. Válassza a **VNet létrehozása alapértelmezett beállításokkal**lehetőséget, fogadja el az alapértelmezett beállításokat, majd kattintson a **tovább**gombra.

   b. Másik lehetőségként válassza a **meglévő VNet és alhálózatok használata**lehetőséget. Hajtsa végre a következő műveleteket:

     - Válassza ki a virtuális hálózatot tartalmazó **erőforráscsoportot** .
     - Válassza ki azt a **Virtual Network** nevet, amelyet központilag telepíteni szeretne.
     - Válassza ki a megfelelő **alhálózati** értékeket minden szükséges szerepkör-alhálózathoz.
     - Kattintson a **Tovább** gombra.

   ![Képernyőfelvétel: a virtuális hálózatot a App Service-telepítőben konfiguráló képernyő.][4]

8. Adja meg a fájlmegosztás adatait, majd kattintson a **tovább**gombra. A fájlmegosztás címének a teljes tartománynevet (FQDN) vagy a fájlkiszolgáló IP-címét kell használnia. Például: \\ \appservicefileserver.local.cloudapp.azurestack.external\websites vagy \\ \10.0.0.1\websites.  Ha olyan fájlkiszolgálón használ, amely tartományhoz van csatlakoztatva, meg kell adnia a teljes felhasználónevet, beleértve a tartományt is. Például: myfileserverdomain\FileShareOwner.

   >[!NOTE]
   >A telepítő megkísérli a fájlmegosztás kapcsolatának tesztelését a folytatás előtt. Ha azonban egy meglévő virtuális hálózatra telepíti, a kapcsolat tesztelése sikertelen lehet. Figyelmeztetést kap, és a folytatáshoz a rendszer kéri. Ha a fájlmegosztás adatai helyesek, folytassa a telepítést.

   ![A App Service telepítőjének fájlmegosztás-konfigurációját megjelenítő képernyőkép.][7]

9. A következő App Service telepítő lapon kövesse az alábbi lépéseket:

   a. Az azonosító **alkalmazás azonosítója** mezőben adja meg az [előfeltételként](azure-stack-app-service-before-you-get-started.md)létrehozott Identity alkalmazás GUID azonosítóját.

   b. Az **Identity Application tanúsítványfájl** mezőben adja meg a tanúsítványfájl helyét (vagy tallózással keresse meg a fájlt).

   c. Adja meg a tanúsítvány jelszavát a **személyazonossági alkalmazás tanúsítványának jelszava** mezőben. Ez a jelszó azt jelzi, hogy mikor használta a parancsfájlt a tanúsítványok létrehozásához.

   d. A **Azure Resource Manager legfelső szintű tanúsítvány fájl** mezőjébe írja be (vagy tallózással keresse meg) a tanúsítványfájl helyét.

   e. Kattintson a **Tovább** gombra.

   ![Képernyőfelvétel: az Identity app-adatok beírásának helye a App Service-telepítőben.][9]

10. A három tanúsítványfájl-mező esetében válassza a **Tallózás** lehetőséget, és navigáljon a megfelelő tanúsítványfájl-fájlhoz. Minden tanúsítványhoz meg kell adnia a jelszót. Ezek a tanúsítványok a [App Service Azure stack hub-on való üzembe helyezésének előfeltételei](azure-stack-app-service-before-you-get-started.md). Az összes információ beírása után válassza a **tovább** lehetőséget.

    | Box | Példa tanúsítványfájl-fájlnévre |
    | --- | --- |
    | **Alapértelmezett SSL-tanúsítványfájl App Service** | \_. appservice. local. AzureStack. external. pfx |
    | **App Service API SSL-tanúsítványfájl** | API. appservice. local. AzureStack. external. pfx |
    | **App Service közzétevő SSL-tanúsítványfájl** | FTP. appservice. local. AzureStack. external. pfx |

    Ha a tanúsítványok létrehozásakor eltérő tartományi utótagot használt, a tanúsítványfájl neve nem a helyi nevet használja *. AzureStack. external*. Ehelyett használja az egyéni tartomány adatait.

    ![Képernyőfelvétel: a tanúsítvány helyeinek és jelszavának beírása a App Service-telepítőben.][10]

11. Adja meg a App Service erőforrás-szolgáltató adatbázisának üzemeltetéséhez használt kiszolgálópéldány SQL Server adatait, majd kattintson a **tovább**gombra. A telepítő ellenőrzi, hogy az SQL-kapcsolatok tulajdonságai megtörténtek-e.<br><br>A folytatás előtt a App Service telepítő megkísérli a SQL Server kapcsolat tesztelését. Ha meglévő virtuális hálózatra telepíti, a kapcsolat tesztelése sikertelen lehet. Figyelmeztetést kap, és a folytatáshoz a rendszer kéri. Ha a SQL Server adatok helyesek, folytassa a telepítést.

    ![Képernyőfelvétel: a App Service-telepítőben található SQL-konfigurációs információk beírásának helye.][11]

12. Tekintse át a szerepkör-példány és az SKU beállításait. Az alapértelmezett értékek az éles környezetben üzemelő példányok minimális száma és a minimális SKU érték szerint vannak feltöltve.  A ASDK üzembe helyezéséhez a példányokat lekicsinyítheti alacsonyabb SKU-ra, hogy csökkentse az alapvető és a memória-végrehajtást, de a teljesítmény romlása is megtapasztalható. A vCPU és a memória követelményeinek összefoglalása az üzemelő példány megtervezéséhez nyújt segítséget. A kijelölések után válassza a **tovább**lehetőséget.

    >[!NOTE]
    >Éles környezetekben az [Azure stack Hub Azure app Service kiszolgálói szerepköreinek kapacitásának megtervezése](azure-stack-app-service-capacity-planning.md)című témakör útmutatását követve.

    | Szerepkör | Minimális példányszám | Minimális SKU | Jegyzetek |
    | --- | --- | --- | --- |
    | Tartományvezérlő | 2 | Standard_A4_v2-(4 mag, 8192 MB) | Felügyeli és karbantartja a App Service felhő állapotát. |
    | Kezelés | 1 | Standard_D3_v2-(4 mag, 14336 MB) | A App Service Azure Resource Manager és az API-végpontokat, a portál-bővítményeket (rendszergazda, bérlő, functions portál) és az adatszolgáltatást kezeli. A feladatátvétel támogatásához növelje az ajánlott példányokat 2-ra. |
    | Publisher | 1 | Standard_A2_v2 – (2 mag, 4096 MB) | Tartalmat tesz közzé FTP-n keresztül és webes telepítéssel. |
    | Előtér | 1 | Standard_A4_v2-(4 mag, 8192 MB) | Átirányítja a kérelmeket App Service alkalmazásokba. |
    | Megosztott feldolgozók | 1 | Standard_A4_v2-(4 mag, 8192 MB) | Webes vagy API-alkalmazásokat, valamint Azure Functions alkalmazásokat üzemeltet. Előfordulhat, hogy további példányokat szeretne hozzáadni. Operátorként megadhatja az ajánlatát, és kiválaszthatja az SKU-szintet. A rétegeknek legalább egy vCPU kell rendelkezniük. |

    ![Képernyőkép, amely bemutatja, hol konfigurálhatja a feldolgozói szerepköröket a App Service-telepítőben.][13]

    > [!NOTE]
    > **A Windows Server 2016 Core nem támogatott platform-rendszerkép a Azure App Service Azure Stack hub-ban való használatához.  Éles környezetben ne használjon próbaverziókat.**

13. A **platform kiválasztása rendszerkép** mezőben válassza ki a Windows Server 2016 virtuális gép (VM) lemezképét a app Service felhő számítási erőforrás-szolgáltatójában elérhető rendszerképekből. Kattintson a **Tovább** gombra.

14. A következő App Service telepítő lapon kövesse az alábbi lépéseket:

     a. Adja meg a feldolgozói szerepkör virtuális gép rendszergazdai felhasználónevét és jelszavát.

     b. Adja meg a többi szerepkört a virtuális gép rendszergazdájának felhasználónevét és jelszavát.

     c. Válassza a **Tovább** gombot.

    ![Képernyőkép, amely bemutatja, hol kell konfigurálni a feldolgozói szerepkör hitelesítő adatait a App Service-telepítőben.][15]

15. A App Service-telepítő összegzése lapon kövesse az alábbi lépéseket:

    a. Ellenőrizze az elvégzett beállításokat. A módosítások elvégzéséhez használja az **előző gombokat** az előző lapok megkereséséhez.

    b. Ha a konfigurációk helyesek, jelölje be a jelölőnégyzetet.

    c. A telepítés elindításához kattintson a **tovább**gombra.

    ![A verem központi telepítésének összegző információit megjelenítő képernyőkép a App Service-telepítőben.][16]

16. A következő App Service telepítő lapon kövesse az alábbi lépéseket:

    a. A telepítési folyamat nyomon követése. A App Service on Azure Stack hub az alapszintű Windows 2016 Datacenter-rendszerkép alapértelmezett kiválasztása és kora alapján akár 240 percet is igénybe vehet.

    b. A telepítő sikeres befejeződése után válassza a  **Kilépés**lehetőséget.

    ![A App Service telepítőjének telepítési folyamatát bemutató képernyőkép.][17]

## <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

> [!IMPORTANT]
> Ha a App Service RP-t egy SQL always on-példánnyal adta **must** meg, akkor [a appservice_hosting és appservice_metering adatbázisokat hozzá kell adnia egy rendelkezésre állási csoporthoz](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) , és szinkronizálnia kell az adatbázisokat, hogy megakadályozza a szolgáltatás elvesztését egy adatbázis-feladatátvétel esetén.

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

## <a name="test-drive-app-service-on-azure-stack-hub"></a>App Service-tesztverzió az Azure Stack Hubon

A App Service erőforrás-szolgáltató üzembe helyezése és regisztrálása után ellenőrizze, hogy a felhasználók telepíthetnek-e webes és API-alkalmazásokat.

>[!NOTE]
>Létre kell hoznia egy ajánlatot, amely a tervben a Microsoft. Web névtérrel rendelkezik. Szüksége lesz egy bérlői előfizetésre is, amely előfizet az ajánlatra. További információért lásd az [ajánlat létrehozása](azure-stack-create-offer.md) és a [terv létrehozása](azure-stack-create-plan.md)című témakört.
>
>A App Service Azure Stack hub-on használó alkalmazások létrehozásához bérlői előfizetéssel *kell* rendelkeznie. A szolgáltatás-rendszergazda által a felügyeleti portálon elvégezhető feladatok a App Service erőforrás-szolgáltatói felügyeletéhez kapcsolódnak. Ez magában foglalja a kapacitás hozzáadását, a telepítési források konfigurálását, valamint a feldolgozói szintek és SKU-ket hozzáadását.
>
>Webes, API-és Azure Functions-alkalmazások létrehozásához a felhasználói portált kell használnia, és bérlői előfizetéssel kell rendelkeznie.
>

A teszt webalkalmazás létrehozásához kövesse az alábbi lépéseket:

1. Az Azure stack hub felhasználói portálon válassza az **+ erőforrás létrehozása**  >  **web és mobil**  >  **webalkalmazás**lehetőséget.

2. A **Web App (webalkalmazás**) területen adjon meg egy nevet a **webalkalmazásban**.

3. Az **erőforráscsoport**területen válassza az **új**lehetőséget. Adja meg az **erőforráscsoport**nevét.

4. Válassza a **app Service terv/hely**  >  **létrehozása új**lehetőséget.

5. A **app Servicei csomag**területen adja meg a **app Service terv**nevét.

6. Válassza **a**  >  **Free-Shared** vagy a **Shared-** Shared  >  **Select**  >  **OK**  >  **létrehozása**lehetőséget.

7. Megjelenik egy csempe az új webalkalmazáshoz az irányítópulton. Válassza ki a csempét.

8. A **webalkalmazás**lapon válassza a **Tallózás** lehetőséget az alkalmazás alapértelmezett webhelyének megtekintéséhez.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>WordPress-, DNN-vagy Django-webhely üzembe helyezése (opcionális)

1. Az Azure Stack hub felhasználói portálon válassza ki **+** Az Azure Marketplace-t, telepítsen egy Django-webhelyet, és várjon, amíg a telepítés befejeződik. A Django webes platform fájlrendszer alapú adatbázist használ. Nem igényel további erőforrás-szolgáltatót, például az SQL-t vagy a MySQL-t.

2. Ha egy MySQL erőforrás-szolgáltatót is üzembe helyezett, a piactéren telepítheti a WordPress-webhelyeket is. Amikor a rendszer az adatbázis paramétereinek megadását kéri, írja be a felhasználónevet *Felhasználó1 \@ Kiszolgáló1*néven, a felhasználónévvel és a kiszolgáló nevével.

3. Ha SQL Server erőforrás-szolgáltatót is üzembe helyezett, üzembe helyezhet egy DNN-webhelyet a piactéren. Ha a rendszer az adatbázis paramétereinek megadását kéri, válasszon egy adatbázist az erőforrás-szolgáltatóhoz csatlakoztatott SQL Servert futtató számítógépen.
::: zone-end



<!----------------------------------------- DISCONNECTED PIVOT -------------------------------------------->
::: zone pivot="state-disconnected"
Ebből a cikkből megtudhatja, hogyan helyezheti üzembe a [Azure app Service erőforrás-szolgáltatót](azure-stack-app-service-overview.md) egy Azure stack hub-környezetbe:
- Nem kapcsolódik az internethez.
- Active Directory összevonási szolgáltatások (AD FS) (AD FS) védi.

Ha a Azure App Service erőforrás-szolgáltatót a kapcsolat nélküli Azure Stack hub-telepítéshez szeretné hozzáadni, el kell végeznie ezeket a legfelső szintű feladatokat:

1. Hajtsa végre az [előfeltételként szükséges lépéseket](azure-stack-app-service-before-you-get-started.md) (például a tanúsítványok megvásárlása, amely igénybe vehet néhány napot).
2. [Töltse le és csomagolja ki a telepítési és a segítő fájlokat](azure-stack-app-service-before-you-get-started.md) egy internethez csatlakozó gépre.
3. Hozzon létre egy offline telepítőcsomagot.
4. Futtassa a appservice.exe Installer-fájlt.

## <a name="create-an-offline-installation-package"></a>Offline telepítőcsomag létrehozása

A Azure App Service offline környezetben történő telepítéséhez először hozzon létre egy offline telepítőcsomagot egy internetkapcsolattal rendelkező számítógépen.

1. Futtassa a AppService.exe telepítőjét az internethez csatlakozó gépen. 

2. Válassza a **speciális**  >  **Létrehozás offline telepítési csomag**lehetőséget. Ennek a lépésnek a végrehajtása több percet is igénybe vehet.

    ![Offline csomag létrehozása Azure App Service telepítőben][31]

3. A Azure App Service-telepítő offline telepítőcsomagot hoz létre, és megjeleníti az elérési utat. A **mappa megnyitása lehetőség kiválasztásával** megnyithatja a mappát a fájlkezelőben.

    ![Az offline telepítési csomag sikeresen létrejött Azure App Service telepítőben](media/azure-stack-app-service-deploy-offline/image02.png)

4. Másolja a telepítőt (AppService.exe) és az offline telepítőcsomagot egy olyan gépre, amely kapcsolódik az Azure Stack hubhoz.

## <a name="complete-the-offline-installation-of-azure-app-service-on-azure-stack-hub"></a>Azure App Service offline telepítésének befejezése Azure Stack hub-on

1. appservice.exe rendszergazdaként futtasson egy olyan számítógépről, amely elérheti a Azure Stack hub rendszergazdai Azure Resource Management végpontját.

1. Válassza a **speciális**  >  **teljes kapcsolat nélküli telepítés**lehetőséget.

    ![Offline telepítés befejezése Azure App Service telepítőben][32]

1. Keresse meg a korábban létrehozott offline telepítőcsomag helyét, majd kattintson a **tovább**gombra.

    ![Offline telepítési csomag elérési útjának megadása im Azure App Service telepítőjének](media/azure-stack-app-service-deploy-offline/image04.png)

1. Tekintse át és fogadja el a Microsoft szoftverlicenc-feltételeit, majd kattintson a **tovább**gombra.

1. Tekintse át és fogadja el a harmadik féltől származó licencfeltételeket, majd kattintson a **tovább**gombra.


1. Győződjön meg arról, hogy a Azure App Service felhő konfigurációs adatai helyesek. Ha a ASDK telepítése során az alapértelmezett beállításokat használta, akkor itt fogadhatja el az alapértelmezett értékeket. Ha azonban a Azure Stack hub telepítésekor vagy egy integrált rendszeren való üzembe helyezéskor testreszabta a beállításokat, akkor az ebben az ablakban található értékeket kell szerkesztenie, hogy azok tükrözzék ezeket a módosításokat. Ha például a mycloud.com tartományi utótagot használja, akkor a Azure Stack hub-bérlő Azure Resource Manager végpontjának a értékre kell váltania `management.<region>.mycloud.com` . Az adatok megerősítése után válassza a **tovább**lehetőséget.

    ![Azure App Service-felhő konfigurálása Azure App Service telepítőben][33]

1. A következő App Service telepítő oldalon csatlakozni fog az Azure Stack hub-hoz:

    1. Válassza ki azt a kapcsolódási módszert, amelyet használni szeretne – **hitelesítő adat** vagy **szolgáltatásnév**
        - **Hitelesítőadat**
            - Ha Azure Active Directoryt (Azure AD) használ, adja meg az Azure AD-rendszergazda fiókját és jelszavát, amelyet az Azure Stack hub telepítésekor adott meg. Kattintson a **Csatlakozás** gombra.
            - Active Directory összevonási szolgáltatások (AD FS) (AD FS) használata esetén adja meg a rendszergazdai fiókját. Például: cloudadmin@azurestack.local. Adja meg a jelszót, majd válassza a **kapcsolat**lehetőséget.
        - **Szolgáltatásnév**
            - A **használt szolgáltatásnév** **tulajdonosi** jogosultságokkal kell rendelkeznie az **alapértelmezett szolgáltatói előfizetéshez** .
            - Adja meg az **egyszerű szolgáltatásnév azonosítóját**, a **tanúsítványfájl**és a **jelszót** , majd válassza a **kapcsolat**lehetőséget.

    1. **Azure stack hub-előfizetések**területen válassza ki az **alapértelmezett szolgáltatói előfizetést**.  A **Azure stack Hub Azure app Service** az **alapértelmezett szolgáltatói előfizetésben**kell központilag telepíteni.

    1. A **Azure stack hub helyein**válassza ki azt a helyet, amely a központilag üzembe helyezett régiónak felel meg. Válassza például a **Local (helyi** ) lehetőséget, ha üzembe helyezi a ASDK.

1. Lehetővé teheti, hogy a Azure App Service telepítőjének virtuális hálózatot és társított alhálózatokat hozzon létre. Vagy egy meglévő virtuális hálózatba is telepítheti [ezeket a lépéseket](azure-stack-app-service-before-you-get-started.md#virtual-network).
   - A Azure App Service telepítő módszer használatához válassza a **VNet létrehozása alapértelmezett beállításokkal**lehetőséget, fogadja el az alapértelmezett beállításokat, majd kattintson a **tovább**gombra.
   - Meglévő hálózatra történő üzembe helyezéshez válassza a **meglévő VNet és alhálózatok használata**lehetőséget, majd a következőket:
       1. Válassza ki a virtuális hálózatot tartalmazó **erőforráscsoport** -beállítást.
       2. Válassza ki azt a **Virtual Network** nevet, amelyet központilag telepíteni szeretne.
       3. Válassza ki a megfelelő **alhálózati** értékeket minden szükséges szerepkör-alhálózathoz.
       4. Kattintson a **Tovább** gombra.

      ![Virtuális hálózat és alhálózat adatai Azure App Service telepítőben][35]

1. Adja meg a fájlmegosztás adatait, majd kattintson a **tovább**gombra. A fájlmegosztás címének a fájlkiszolgáló teljes tartománynevét (FQDN) vagy IP-címét kell használnia. Például: \\ \appservicefileserver.local.cloudapp.azurestack.external\websites vagy \\ \10.0.0.1\websites.  Ha tartományhoz csatlakoztatott fájlkiszolgálón használ, meg kell adnia a teljes felhasználónevet, beleértve a tartományt is. Például: `<myfileserverdomain>\<FileShareOwner>`.

    > [!NOTE]
    > A telepítő megkísérli a fájlmegosztás kapcsolatának tesztelését a folytatás előtt. Ha azonban egy meglévő virtuális hálózatba helyezi üzembe a telepítést, előfordulhat, hogy a telepítő nem tud kapcsolódni a fájlmegosztás számára, és figyelmeztetést jelenít meg, hogy szeretné-e folytatni. Ellenőrizze a fájlmegosztás adatait, és folytassa, ha helyes.

   ![Fájlmegosztási információk a Azure App Service-telepítőben][38]

1. A következő oldalon:
    1. Az azonosító **alkalmazás azonosítója** mezőben adja meg az [előfeltételként](azure-stack-app-service-before-you-get-started.md)létrehozott Identity alkalmazás GUID azonosítóját.
    1. Az **Identity Application tanúsítványfájl** mezőben adja meg a tanúsítványfájl helyét (vagy tallózással keresse meg a fájlt).
    1. Adja meg a tanúsítvány jelszavát a **személyazonossági alkalmazás tanúsítványának jelszava** mezőben. Ez a jelszó azt jelzi, hogy mikor használta a parancsfájlt a tanúsítványok létrehozásához.
    1. A **Azure Resource Manager legfelső szintű tanúsítvány fájl** mezőjébe írja be (vagy tallózással keresse meg) a tanúsítványfájl helyét.
    1. Kattintson a **Tovább** gombra.

    ![Adja meg az alkalmazás AZONOSÍTÓját és a tanúsítvány adatait Azure App Service telepítőben][40]

1. A három tanúsítványfájl-mező esetében válassza a **Tallózás** lehetőséget, és navigáljon a megfelelő tanúsítványfájl-fájlhoz. Minden tanúsítványhoz meg kell adnia a jelszót. Ezek a tanúsítványok a [App Service Azure stack hub-on való üzembe helyezésének előfeltételei](azure-stack-app-service-before-you-get-started.md). Az összes információ beírása után válassza a **tovább** lehetőséget.

    | Box | Példa tanúsítványfájl-fájlnévre |
    | --- | --- |
    | **Alapértelmezett SSL-tanúsítványfájl App Service** | \_. appservice. local. AzureStack. external. pfx |
    | **App Service API SSL-tanúsítványfájl** | API. appservice. local. AzureStack. external. pfx |
    | **App Service közzétevő SSL-tanúsítványfájl** | FTP. appservice. local. AzureStack. external. pfx |

    Ha a tanúsítványok létrehozásakor eltérő tartományi utótagot használt, a tanúsítványfájl neve nem a helyi nevet használja *. AzureStack. external*. Ehelyett használja az egyéni tartomány adatait.

    ![Adja meg az SSL-tanúsítvány adatait Azure App Service telepítőben][41]

1. Adja meg a Azure App Service erőforrás-szolgáltatói adatbázisok üzemeltetéséhez használt kiszolgálópéldány SQL Server adatait, majd kattintson a **tovább**gombra. A telepítő ellenőrzi, hogy az SQL-kapcsolatok tulajdonságai megtörténtek-e. A SQL Server neveként a belső IP-címet vagy a teljes tartománynevet **kell** megadnia.

    > [!NOTE]
    > A telepítő megkísérli a SQL Server rendszert futtató számítógép kapcsolatának tesztelését a továbblépés előtt. Ha azonban egy meglévő virtuális hálózatba helyezi üzembe a telepítést, előfordulhat, hogy a telepítő nem tud csatlakozni a SQL Server rendszerű számítógéphez, és figyelmeztetést jelenít meg arról, hogy folytatni kívánja-e a telepítést. Ellenőrizze a SQL Server információt, és ha helyes, folytassa a művelettel.
    >
    > A Azure App Service Azure Stack hub 1,3-as és újabb verziójában a telepítő ellenőrzi, hogy a SQL Server futtató számítógép SQL Server szinten engedélyezve van-e az adatbázis-tárolás. Ha nem, a rendszer a következő kivételt kéri:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;
    >    GO
    >    RECONFIGURE;
    >    GO
    >    ***********************************************************
    > ```
    > További információkért tekintse meg a [Azure stack Hub 1,3-es verziójának Azure app Service kibocsátási megjegyzéseit](azure-stack-app-service-release-notes-update-three.md).

    ![Adja meg SQL Server info Azure App Service telepítőben][42]

1. Tekintse át a szerepkör-példány és az SKU beállításait. Az alapértelmezett értékek az éles környezetben üzemelő példányok minimális száma és a minimális SKU érték szerint vannak feltöltve.  A ASDK üzembe helyezéséhez a példányokat lekicsinyítheti alacsonyabb SKU-ra, hogy csökkentse az alapvető és a memória-végrehajtást, de a teljesítmény romlása is megtapasztalható.  A vCPU és a memória követelményeinek összefoglalása az üzemelő példány megtervezéséhez nyújt segítséget. A kijelölések után válassza a **tovább**lehetőséget.

     > [!NOTE]
     > Éles üzembe helyezések esetén kövesse az [Azure stack Hub Azure app Service kiszolgálói szerepkörök kapacitásának megtervezése](azure-stack-app-service-capacity-planning.md)című témakör útmutatását.
     >
     >

    
    | Szerepkör | Minimális példányszám | Minimális SKU | Jegyzetek |
    | --- | --- | --- | --- |
    | Tartományvezérlő | 2 | Standard_A4_v2-(4 mag, 8192 MB) | Felügyeli és karbantartja a App Service felhő állapotát. |
    | Kezelés | 1 | Standard_D3_v2-(4 mag, 14336 MB) | A App Service Azure Resource Manager és az API-végpontokat, a portál-bővítményeket (rendszergazda, bérlő, functions portál) és az adatszolgáltatást kezeli. A feladatátvétel támogatásához növelje az ajánlott példányokat 2-ra. |
    | Publisher | 1 | Standard_A2_v2 – (2 mag, 4096 MB) | Tartalmat tesz közzé FTP-n keresztül és webes telepítéssel. |
    | Előtér | 1 | Standard_A4_v2-(4 mag, 8192 MB) | Átirányítja a kérelmeket App Service alkalmazásokba. |
    | Megosztott feldolgozók | 1 | Standard_A4_v2-(4 mag, 8192 MB) | Webes vagy API-alkalmazásokat, valamint Azure Functions alkalmazásokat üzemeltet. Előfordulhat, hogy további példányokat szeretne hozzáadni. Operátorként megadhatja az ajánlatát, és kiválaszthatja az SKU-szintet. A rétegeknek legalább egy vCPU kell rendelkezniük. |

    ![Szerepkörök szintjeinek és SKU-beállításainak megadása Azure App Service telepítőben][44]

1. A **platform kiválasztása rendszerkép** mezőben válassza ki a Windows Server 2016 virtuális gép (VM) lemezképét a Azure app Service felhő számítási erőforrás-szolgáltatóján elérhető rendszerképekből. Kattintson a **Tovább** gombra.

    > [!NOTE]
    > A Windows Server 2016 Core *nem* támogatott platform-rendszerkép a Azure app Service Azure stack hub-ban való használatához.  Éles környezetben ne használjon próbaverziókat. A Azure App Service on Azure Stack hub megköveteli, hogy a Microsoft .NET 3.5.1 SP1 legyen aktiválva az üzembe helyezéshez használt rendszerképben. Piactér – a Windows Server 2016-lemezképek nem rendelkeznek ezzel a szolgáltatással. Ezért egy Windows Server 2016 rendszerképet kell létrehoznia és használnia ezzel a szolgáltatással előre engedélyezve.
    >
    > Az egyéni rendszerkép létrehozásával és a piactérhez való hozzáadásával kapcsolatos további tudnivalókért lásd: [Egyéni virtuálisgép-rendszerkép hozzáadása Azure stack hubhoz](azure-stack-add-vm-image.md) . Ügyeljen arra, hogy a következőt adja meg, amikor hozzáadja a rendszerképet a piactérhez:
    >
    >- Közzétevő = MicrosoftWindowsServer
    >- Ajánlat = WindowsServer
    >- SKU = 2016 – Datacenter
    >- Version = a "legújabb" verzió meghatározása

1. A következő oldalon:
     1. Adja meg a feldolgozói szerepkör virtuális gép rendszergazdai felhasználónevét és jelszavát.
     2. Adja meg a többi szerepkört a virtuális gép rendszergazdájának felhasználónevét és jelszavát.
     3. Kattintson a **Tovább** gombra.

    ![Adja meg a szerepkörök virtuálisgép-rendszergazdáit Azure App Service telepítőben][46]

1. Az összefoglalás lapon:
    1. Ellenőrizze az elvégzett beállításokat. A módosítások elvégzéséhez használja az **előző gombokat** az előző lapok megkereséséhez.
    2. Ha a konfigurációk helyesek, jelölje be a jelölőnégyzetet.
    3. A telepítés elindításához kattintson a **tovább**gombra.

    ![A Azure App Service-telepítőben végrehajtott Kijelölések összegzése][47]

1. A következő oldalon:
    1. A telepítési folyamat nyomon követése. A App Service on Azure Stack hub az alapszintű Windows 2016 Datacenter-rendszerkép alapértelmezett kiválasztása és kora alapján akár 240 percet is igénybe vehet.

    2. Miután a telepítő befejezte a futtatást, válassza a **Kilépés**lehetőséget.

    ![Azure App Service telepítőjének telepítési folyamatának követése][48]

## <a name="post-deployment-steps"></a>Üzembe helyezés után lépések

> [!IMPORTANT]
> Ha a Azure App Service RP-t egy SQL always on-példánnyal adta meg, akkor hozzá **kell** [adnia a appservice_hosting és appservice_metering adatbázist egy rendelkezésre állási csoporthoz](/sql/database-engine/availability-groups/windows/availability-group-add-a-database). Szinkronizálnia kell az adatbázisokat is, hogy megakadályozza a szolgáltatás elvesztését egy adatbázis-feladatátvétel esetén.

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

## <a name="validate-the-azure-app-service-on-azure-stack-hub-installation"></a>Azure Stack hub-telepítés Azure App Service ellenőrzése

1. Az Azure Stack hub felügyeleti portálján lépjen a **felügyelet – app Service**elemre.

1. Az Áttekintés területen az állapot alatt ellenőrizze, hogy az **állapot** megjeleníti-e az **összes szerepkört**.

    ![Az Azure App Service felügyeletének áttekintése](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-azure-app-service-on-azure-stack-hub"></a>Azure App Service tesztelése Azure Stack központban

A Azure App Service erőforrás-szolgáltató üzembe helyezése és regisztrálása után ellenőrizze, hogy a felhasználók telepíthetnek-e webes és API-alkalmazásokat.

> [!NOTE]
> Létre kell hoznia egy ajánlatot, amely a csomagon belül a Microsoft. Web névtérrel rendelkezik. Ezután egy bérlői előfizetéssel kell rendelkeznie, amely Előfizet erre az ajánlatra. További információért lásd az [ajánlat létrehozása](azure-stack-create-offer.md) és a [terv létrehozása](azure-stack-create-plan.md)című témakört.
>
> A Azure App Service Azure Stack hub-on használó alkalmazások létrehozásához bérlői előfizetéssel *kell* rendelkeznie. A szolgáltatás-rendszergazda által a felügyeleti portálon elvégezhető képességek a Azure App Service erőforrás-szolgáltatói felügyeletéhez kapcsolódnak. Ezek a képességek többek között a kapacitás hozzáadását, a telepítési források konfigurálását, valamint a feldolgozói rétegek és az SKU-ket adja
>
> A harmadik technikai előzetes verziótól kezdve a webes, API-és Azure Functions alkalmazások létrehozásához a felhasználói portált kell használnia, és bérlői előfizetéssel kell rendelkeznie.

1. Az Azure stack hub felhasználói portálon válassza az **+ erőforrás létrehozása**  >  **web és mobil**  >  **webalkalmazás**lehetőséget.

1. A **Web App (webalkalmazás** ) panelen írja be a kívánt nevet a **Web App (webalkalmazás** ) mezőbe.

1. Az **erőforráscsoport**területen válassza az **új**lehetőséget. Adjon meg egy nevet az **erőforráscsoport** mezőben.

1. Válassza a **app Service terv/hely**  >  **létrehozása új**lehetőséget.

1. A **app Service terv** panelen adjon meg egy nevet a **app Service terv** mezőben.

1. Válassza **a**  >  **Free-Shared** vagy a **Shared-** Shared  >  **Select**  >  **OK**  >  **létrehozása**lehetőséget.

1. Kevesebb mint egy percen belül az új webalkalmazás csempéje megjelenik az irányítópulton. Válassza ki a csempét.

1. A **Web App (webalkalmazás** ) panelen kattintson a **Tallózás** gombra az alkalmazás alapértelmezett webhelyének megtekintéséhez.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>WordPress-, DNN-vagy Django-webhely üzembe helyezése (opcionális)

1. Az Azure Stack hub felhasználói portálon válassza az **+** Azure Marketplace, Django-webhely üzembe helyezése lehetőséget, és várja meg a sikeres befejezést. A Django webes platform fájlrendszer alapú adatbázist használ. Nem igényel további erőforrás-szolgáltatót, például az SQL-t vagy a MySQL-t.

1. Ha egy MySQL erőforrás-szolgáltatót is üzembe helyezett, a WordPress-webhelyet üzembe helyezheti az Azure piactéren. Amikor a rendszer az adatbázis paramétereinek megadását kéri, írja be a felhasználónevet *Felhasználó1 \@ Kiszolgáló1*néven, a felhasználónévvel és a kiszolgáló nevével.

1. Ha SQL Server erőforrás-szolgáltatót is üzembe helyezett, üzembe helyezhet egy DNN-webhelyet az Azure piactéren. Ha a rendszer az adatbázis paramétereinek megadását kéri, válasszon egy adatbázist az erőforrás-szolgáltatóhoz csatlakoztatott SQL Servert futtató számítógépen.

::: zone-end

## <a name="next-steps"></a>Következő lépések

Felkészülés további rendszergazdai műveletekre Azure Stack hub App Service:

- [Kapacitás megtervezése](azure-stack-app-service-capacity-planning.md)
- [Központi telepítési források konfigurálása](azure-stack-app-service-configure-deployment-sources.md)

<!-- Connected image references-->
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

<!-- Disconnected image references-->
[31]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[32]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[33]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[34]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[35]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[36]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[37]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[38]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[39]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[40]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[41]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[42]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[43]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[44]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[45]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[46]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[47]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[48]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
