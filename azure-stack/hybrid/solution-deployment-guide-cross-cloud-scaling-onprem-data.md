---
title: Helyszíni adatkezelést használó alkalmazás üzembe helyezése, és az Azure-t és Azure Stack hub-t használó alkalmazások közötti méretezés
description: Megtudhatja, hogyan helyezhet üzembe egy helyszíni információkat használó alkalmazást, és hogyan méretezhető a felhőben az Azure és a Azure Stack hub használatával.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: b376be7855300dab0177bbbe735d6a5bf34d6bb9
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77701071"
---
# <a name="deploy-an-app-that-uses-on-premises-data-and-scales-cross-cloud-using-azure-and-azure-stack-hub"></a>Helyszíni adatkezelést használó alkalmazás üzembe helyezése, és az Azure-t és Azure Stack hub-t használó alkalmazások közötti méretezés

Ebből a megoldási útmutatóból megtudhatja, hogyan helyezhet üzembe egy hibrid alkalmazást, amely az Azure-t és Azure Stack hub-t is felöleli, és egyetlen helyszíni adatforrást használ.

A hibrid felhőalapú megoldásokkal kombinálhatja a privát felhő megfelelőségi előnyeit a nyilvános felhő méretezhetőségével. Emellett a fejlesztők kihasználhatják a Microsoft fejlesztői ökoszisztémáját, és alkalmazhatják tudásukat a felhőben és a helyszíni környezetekben.

## <a name="overview-and-assumptions"></a>Áttekintés és feltételezések

Ezt az oktatóanyagot követve beállíthat egy olyan munkafolyamatot, amely lehetővé teszi, hogy a fejlesztők egy azonos webalkalmazást telepítsenek egy nyilvános felhőbe és egy privát felhőbe. Ez az alkalmazás elérheti a privát felhőben üzemeltetett, nem internetes útválasztású hálózatot. Ezeket a webalkalmazásokat a rendszer figyeli, és ha van egy tüske a forgalomban, a program módosítja a DNS-rekordokat, hogy átirányítsa a forgalmat a nyilvános felhőbe. Ha a forgalom a csúcs előtti szintre esik, a forgalmat a rendszer visszairányítja a privát felhőbe.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> - Hibrid csatlakozású SQL Server adatbázis-kiszolgáló üzembe helyezése.
> - Egy webalkalmazás összekötése a globális Azure-ban egy hibrid hálózattal.
> - Konfigurálja a DNS-t a Felhőbeli skálázáshoz.
> - Konfigurálja az SSL-tanúsítványokat a Felhőbeli skálázáshoz.
> - Konfigurálja és telepítse a webalkalmazást.
> - Hozzon létre egy Traffic Manager profilt, és konfigurálja a Felhőbeli skálázáshoz.
> - Application Insights monitorozás és riasztások beállítása a megnövekedett forgalomhoz.
> - Konfigurálja az automatikus forgalmat a globális Azure és az Azure Stack hub között.

> [!Tip]  
> ![Hybrid-Pillars. png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack hub az Azure kiterjesztése. Azure Stack hub a felhő-számítástechnika rugalmasságát és innovációját a helyszíni környezetbe helyezi, így az egyetlen hibrid felhő, amely lehetővé teszi a hibrid alkalmazások bárhol történő létrehozását és üzembe helyezését.  
> 
> A [hibrid alkalmazásokkal kapcsolatos tervezési szempontok](overview-app-design-considerations.md) a szoftverek minőségének (elhelyezés, skálázhatóság, rendelkezésre állás, rugalmasság, kezelhetőség és biztonság) pilléreit tekintik át a hibrid alkalmazások tervezéséhez, üzembe helyezéséhez és üzemeltetéséhez. A kialakítási szempontok segítik a hibrid alkalmazások kialakításának optimalizálását, ami minimalizálja az éles környezetekben felmerülő kihívásokat.

### <a name="assumptions"></a>Feltételezések

Ez az oktatóanyag feltételezi, hogy rendelkezik a globális Azure és Azure Stack hub alapszintű ismeretével. Ha többet szeretne megtudni az oktatóanyag megkezdése előtt, tekintse át a következő cikkeket:

 - [Bevezetés az Azure-ba](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack hub főbb fogalmak](../operator/azure-stack-overview.md)

Ez az oktatóanyag azt is feltételezi, hogy rendelkezik Azure-előfizetéssel. Ha nem rendelkezik előfizetéssel, a Kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

A megoldás elindítása előtt győződjön meg arról, hogy megfelel a következő követelményeknek:

- Egy Azure Stack Development Kit (ASDK) vagy előfizetés egy Azure Stack hub integrált rendszeren. Azure Stack Development Kit telepítéséhez kövesse a [ASDK telepítése a telepítő használatával](../asdk/asdk-install.md)című témakör útmutatását.
- A Azure Stack hub telepítése a következőkkel telepíthető:
  - A Azure App Service. Az Azure Stack hub operátorral együttműködve telepítheti és konfigurálhatja a Azure App Servicet a környezetében. Ez az oktatóanyag megköveteli, hogy a App Service legalább egy (1) elérhető dedikált feldolgozói szerepkörrel rendelkezzen.
  - Egy Windows Server 2016-rendszerkép.
  - Egy Microsoft SQL Server rendszerképpel rendelkező Windows Server 2016.
  - A megfelelő csomagok és ajánlatok.
  - A webalkalmazáshoz tartozó tartománynév. Ha nem rendelkezik tartománynévvel, vásárolhat egyet egy olyan tartományi szolgáltatótól, mint a GoDaddy, a Bluehost és a InMotion.
- A tartományhoz tartozó SSL-tanúsítvány megbízható hitelesítésszolgáltatótól, például LetsEncrypt.
- Egy SQL Server-adatbázissal kommunikáló webalkalmazás, amely támogatja a Application Insights. A [dotnetcore-sqldb-tutorial](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial) minta alkalmazást letöltheti a githubról.
- Hibrid hálózat egy Azure-beli virtuális hálózat és Azure Stack hub virtuális hálózat között. Részletes útmutatás: [hibrid felhőalapú kapcsolat konfigurálása az Azure-ban és Azure stack hub](solution-deployment-guide-connectivity.md).

- Hibrid folyamatos integrációs/folyamatos üzembe helyezési (CI/CD) folyamat egy Azure Stack hub-beli privát Build-ügynökkel. Részletes útmutatás: [hibrid Felhőbeli identitás konfigurálása az Azure-ban és Azure stack hub-alkalmazások](solution-deployment-guide-identity.md).

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>Hibrid kapcsolattal rendelkező SQL Server adatbázis-kiszolgáló üzembe helyezése

1. Jelentkezzen be az Azure Stack hub felhasználói portálra.

2. Az **irányítópulton**válassza a **piactér**lehetőséget.

    ![Azure Stack hub piactér](media/solution-deployment-guide-hybrid/image1.png)

3. A **piactéren**válassza a **számítás**lehetőséget, majd válassza a **továbbiak**lehetőséget. A **további**területen válassza ki az **ingyenes SQL Server licencet: SQL Server 2017 Developer Windows Server** rendszerképre.

    ![Virtuális gép rendszerképének kiválasztása](media/solution-deployment-guide-hybrid/image2.png)

4. **Ingyenes SQL Server licenc: SQL Server 2017 Developer on Windows Server**, válassza a **Létrehozás**lehetőséget.

5. Az **alapvető beállítások konfigurálása > alapszintű beállításnál**adja meg a virtuális gép (VM) **nevét** , a SQL Server SA **felhasználónevét** és a biztonsági társításhoz tartozó **jelszót** .  Az **előfizetés** legördülő listából válassza ki azt az előfizetést, amelyre telepíteni kívánja. Az **erőforráscsoport**esetében használja a **meglévő kiválasztása lehetőséget** , és helyezze a virtuális gépet ugyanabba az erőforráscsoporthoz, mint a Azure stack hub webalkalmazás.

    ![A virtuális gép alapszintű beállításainak konfigurálása](media/solution-deployment-guide-hybrid/image3.png)

6. A **méret**területen válasszon egy méretet a virtuális géphez. Ebben az oktatóanyagban A2_Standard vagy DS2_V2_Standard használatát javasoljuk.

7. A **beállítások > a választható funkciók konfigurálása**területen adja meg a következő beállításokat:

   - **Storage-fiók**: hozzon létre egy új fiókot, ha szüksége van rá.
   - **Virtuális hálózat**:

     > [!Important]  
     > Győződjön meg arról, hogy a SQL Server VM ugyanazon a virtuális hálózaton van telepítve, mint a VPN-átjárók.

   - **Nyilvános IP-cím**: használja az alapértelmezett beállításokat.
   - **Hálózati biztonsági csoport**: (NSG). Hozzon létre egy új NSG.
   - **Bővítmények és figyelés**: tartsa meg az alapértelmezett beállításokat.
   - **Diagnosztikai Storage-fiók**: hozzon létre egy új fiókot, ha szüksége van rá.
   - A konfiguráció mentéséhez kattintson **az OK gombra** .

     ![Választható funkciók konfigurálása](media/solution-deployment-guide-hybrid/image4.png)

8. A **SQL Server beállítások**területen adja meg a következő beállításokat:
   - **SQL-kapcsolat**esetén válassza a **nyilvános (Internet)** lehetőséget.
   - A **port**esetében tartsa meg az alapértelmezett **1433**-as értéket.
   - **SQL-hitelesítés**esetén válassza az **Engedélyezés**lehetőséget.

     > [!Note]  
     > Az SQL-hitelesítés engedélyezésekor a rendszer automatikusan feltölti az **alapokban**konfigurált "SQLAdmin" információkkal.

   - A többi beállításnál tartsa meg az alapértelmezett értékeket. Kattintson az **OK** gombra.

     ![SQL Server beállítások konfigurálása](media/solution-deployment-guide-hybrid/image5.png)

9. Az **Összefoglalás**lapon tekintse át a virtuális gép konfigurációját, majd a telepítés elindításához kattintson **az OK gombra** .

    ![Konfiguráció összegzése](media/solution-deployment-guide-hybrid/image6.png)

10. Némi időbe telik az új virtuális gép létrehozása. A **virtuális gépek állapota a Virtual Machines**szolgáltatásban tekinthető meg.

    ![Virtual machines (Virtuális gépek)](media/solution-deployment-guide-hybrid/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack-hub"></a>Webalkalmazások létrehozása az Azure-ban és Azure Stack hub

A Azure App Service leegyszerűsíti a webalkalmazások futtatását és felügyeletét. Mivel Azure Stack hub konzisztens az Azure-ban, a App Service mindkét környezetben futhat. Az alkalmazás futtatásához a App Service fogja használni.

### <a name="create-web-apps"></a>Webalkalmazások létrehozása

1. Hozzon létre egy webalkalmazást az Azure-ban a [app Service-csomag kezelése az Azure-ban](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan)című témakör útmutatásait követve. Győződjön meg arról, hogy a webalkalmazást a hibrid hálózattal megegyező előfizetésben és erőforráscsoporthoz helyezi el.

2. Ismételje meg az előző lépést (1) az Azure Stack központban.

### <a name="add-route-for-azure-stack-hub"></a>Útvonal hozzáadása Azure Stack hubhoz

Az Azure Stack hub App Service a nyilvános internetről irányíthatónak kell lennie ahhoz, hogy a felhasználók hozzáférhessenek az alkalmazáshoz. Ha az Azure Stack hub elérhető az internetről, jegyezze fel az Azure Stack hub-webalkalmazás nyilvános IP-címét vagy URL-címét.

Ha ASDK használ, beállíthatja [a statikus NAT-leképezést](../operator/azure-stack-create-vpn-connection-one-node.md#configure-the-nat-vm-on-each-asdk-for-gateway-traversal) úgy, hogy a virtuális környezeten kívül is elérhetővé tegye a app Service.

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>Webalkalmazás összekötése az Azure-ban hibrid hálózattal

Az Azure webes kezelőfelülete és a Azure Stack hub SQL Server adatbázisa közötti kapcsolat biztosításához a webalkalmazásnak csatlakoznia kell a hibrid hálózathoz az Azure és a Azure Stack hub között. A kapcsolat engedélyezéséhez a következőkre lesz szüksége:

- Pont – hely kapcsolat konfigurálása.
- Konfigurálja a webalkalmazást.
- Módosítsa Azure Stack hub helyi hálózati átjáróját.

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>Az Azure-beli virtuális hálózat konfigurálása pont – hely kapcsolathoz

A hibrid hálózat Azure oldalán lévő virtuális hálózati átjárónak lehetővé kell tennie a pont – hely kapcsolatoknak a Azure App Service való integrálását.

1. Az Azure-ban navigáljon a virtuális hálózati átjáró lapra. A **Beállítások**területen válassza a **pont – hely konfiguráció**lehetőséget.

    ![Pont – hely beállítás](media/solution-deployment-guide-hybrid/image8.png)

2. Válassza a **Konfigurálás most** lehetőséget a pont – hely konfigurálásához.

    ![Pont – hely konfiguráció indítása](media/solution-deployment-guide-hybrid/image9.png)

3. A **pont – hely** konfiguráció lapon adja meg **a címkészlet számára**használni kívánt magánhálózati IP-címtartományt.

   > [!Note]  
   > Ügyeljen arra, hogy a megadott tartomány ne legyen átfedésben a hibrid hálózat globális Azure-ban vagy Azure Stack hub-összetevőjében már használt alhálózatok egyikével sem.

   Az **alagút típusa**területen törölje a **IKEv2 VPN-** t. Válassza a **Mentés** lehetőséget a pont – hely konfigurálásának befejezéséhez.

   ![Pont – hely beállítások](media/solution-deployment-guide-hybrid/image10.png)

### <a name="integrate-the-azure-app-service-app-with-the-hybrid-network"></a>A Azure App Service alkalmazás integrálása a hibrid hálózattal

1. Az alkalmazás Azure VNet való összekapcsolásához kövesse az [átjáró szükséges VNet-integrációjának](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#gateway-required-vnet-integration)utasításait.

2. Navigáljon a webalkalmazást üzemeltető App Service terv **beállításaihoz** . A **Beállítások**területen válassza a **hálózatkezelés**lehetőséget.

    ![Hálózatkezelés konfigurálása](media/solution-deployment-guide-hybrid/image11.png)

3. A **VNET-integráció**területen válassza a **kattintson ide a kezeléséhez**.

    ![VNET-integráció kezelése](media/solution-deployment-guide-hybrid/image12.png)

4. Válassza ki a konfigurálni kívánt VNET. A **VNET felé irányított IP-címek**területen adja meg az Azure VNET, az Azure stack hub VNET és a pont – hely címtartomány IP-címtartományt. A beállítások ellenőrzéséhez és mentéséhez kattintson a **Mentés** gombra.

    ![Útvonalhoz tartozó IP-címtartományok](media/solution-deployment-guide-hybrid/image13.png)

Ha többet szeretne megtudni arról, hogy a App Service hogyan integrálódik az Azure virtuális hálózatok, tekintse meg az [alkalmazás integrálása Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)-beli Virtual Network című témakört.

### <a name="configure-the-azure-stack-hub-virtual-network"></a>Az Azure Stack hub virtuális hálózat konfigurálása

Az Azure Stack hub virtuális hálózatában lévő helyi hálózati átjárót úgy kell konfigurálni, hogy a forgalmat a App Service pont – hely címtartomány alapján irányítsa.

1. Az Azure Stack központban navigáljon a **helyi hálózati átjáró**elemre. A **Beállítások** területen válassza a **Konfiguráció** elemet.

    ![Átjáró konfigurációs beállítása](media/solution-deployment-guide-hybrid/image14.png)

2. A **címterület**mezőben adja meg az Azure-beli virtuális hálózati átjáró pont – hely típusú címtartományt.

    ![Pont – hely címtartomány](media/solution-deployment-guide-hybrid/image15.png)

3. A konfiguráció ellenőrzéséhez és mentéséhez kattintson a **Mentés** gombra.

## <a name="configure-dns-for-cross-cloud-scaling"></a>A DNS konfigurálása a Felhőbeli skálázáshoz

A DNS Felhőbeli alkalmazások számára történő megfelelő konfigurálásával a felhasználók hozzáférhetnek a webalkalmazás globális Azure-és Azure Stack hub-példányaihoz. Az oktatóanyag DNS-konfigurációja azt is lehetővé teszi, hogy az Azure Traffic Manager irányítsa a forgalmat, amikor a terhelés növekszik vagy csökken.

Ez az oktatóanyag a Azure DNS használatával kezeli a DNS-t, mert App Service tartományok nem működnek.

### <a name="create-subdomains"></a>Altartományok létrehozása

Mivel Traffic Manager DNS-CNAME-re támaszkodik, egy altartományra van szükség ahhoz, hogy megfelelően irányítsa a forgalmat a végpontokra. A DNS-rekordokkal és a tartomány-hozzárendeléssel kapcsolatos további információkért lásd: [tartományok leképezése Traffic Managerokkal](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name).

Az Azure-végponthoz létre kell hoznia egy altartományt, amelyet a felhasználók használhatnak a webalkalmazás eléréséhez. Ebben az oktatóanyagban használhatja a **app.Northwind.com**-t, de a saját tartománya alapján kell testreszabnia ezt az értéket.

Létre kell hoznia egy altartományt is az Azure Stack hub-végponthoz tartozó rekorddal. Használhatja a **azurestack.Northwind.com**.

### <a name="configure-a-custom-domain-in-azure"></a>Egyéni tartomány konfigurálása az Azure-ban

1. Adja hozzá a **app.Northwind.com** hostname-t az Azure-webalkalmazáshoz [egy CNAME hozzárendelésével Azure app Servicehoz](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

### <a name="configure-custom-domains-in-azure-stack-hub"></a>Egyéni tartományok konfigurálása Azure Stack központban

1. Adja hozzá a **azurestack.Northwind.com** hostname-t az Azure stack hub webalkalmazáshoz [egy olyan rekord hozzárendelésével, Azure app Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record). Használja az App Service alkalmazás internetre irányítható IP-címét.

2. Adja hozzá a **app.Northwind.com** hostname-t az Azure stack hub webalkalmazáshoz [egy CNAME-fájl Azure app Servicehoz való leképezésével](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record). Használja az előző lépésben (1) konfigurált állomásnevet a CNAME célhelyként.

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>Az SSL-tanúsítványok konfigurálása a Felhőbeli skálázáshoz

Fontos, hogy a webalkalmazás által gyűjtött bizalmas adatok biztonságban legyenek az SQL Database-ben és azokon való átvitel során.

Az Azure-és Azure Stack hub-webalkalmazásokat az összes bejövő forgalom SSL-tanúsítványainak használatára konfigurálja.

### <a name="add-ssl-to-azure-and-azure-stack-hub"></a>SSL hozzáadása az Azure-hoz és Azure Stack hub

SSL hozzáadása az Azure-hoz:

1. Győződjön meg arról, hogy a beszerzett SSL-tanúsítvány érvényes a létrehozott altartományhoz. (A helyettesítő tanúsítványok használata rendben van.)

2. Az Azure-ban kövesse a **webalkalmazás előkészítése** és az SSL- **tanúsítvány** kötése szakaszát a [meglévő egyéni SSL-tanúsítvány kötése az Azure Web Apps](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) cikkhez című cikkben található utasításokat. Az **SSL-típusként**válassza a **SNI-alapú SSL** lehetőséget.

3. A HTTPS-portra irányuló összes forgalom átirányítása. Kövesse a [meglévő egyéni SSL-tanúsítvány kötése az Azure-Web Apps cikk HTTPS-alapú hitelesítésének](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) **megkövetelése** című szakaszának utasításait.

SSL hozzáadása Azure Stack hubhoz:

- Ismételje meg az Azure-ban használt 1-3-es lépést.

## <a name="configure-and-deploy-the-web-app"></a>A webalkalmazás konfigurálása és üzembe helyezése

Konfigurálja az telemetria a helyes Application Insights példányra, majd konfigurálja a webalkalmazásokat a megfelelő kapcsolati karakterláncokkal. További információ a Application Insightsről: [Mi az Application Insights?](https://docs.microsoft.com/azure/application-insights/app-insights-overview)

### <a name="add-application-insights"></a>Application Insights hozzáadása

1. Nyissa meg a webalkalmazást a Microsoft Visual Studióban.

2. [Adja hozzá Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-client-side-telemetry-for-web-applications) a projekthez, hogy továbbítsa a telemetria, amelyet a Application Insights használ a riasztások létrehozásához, amikor a webes forgalom növekszik vagy csökken.

### <a name="configure-dynamic-connection-strings"></a>Dinamikus kapcsolatok karakterláncának konfigurálása

A webalkalmazás minden példánya egy másik módszert fog használni az SQL-adatbázishoz való kapcsolódáshoz. Az Azure-beli alkalmazás a SQL Server virtuális gép (VM) magánhálózati IP-címét használja, az alkalmazás pedig Azure Stack hub-ban a SQL Server VM nyilvános IP-címét használja.

> [!Note]  
> Egy Azure Stack hub-beli integrált rendszeren a nyilvános IP-cím nem lehet internetre irányítható. Egy Azure Stack Development Kiton (ASDK) a nyilvános IP-cím nem irányítható át a ASDK kívül.

App Service környezeti változók használatával más kapcsolódási karakterláncot adhat át az alkalmazás minden egyes példányához.

1. Nyissa meg az alkalmazást a Visual Studióban.

2. Nyissa meg a Startup.cs, és keresse meg a következő kódrészletet:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. Cserélje le az előző kódú blokkot a következő kódra, amely a *appSettings. JSON* fájlban meghatározott kapcsolatok karakterláncot használja:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-app-settings"></a>App Service alkalmazás beállításainak konfigurálása

1. Hozzon létre a kapcsolatok karakterláncait az Azure-hoz és Azure Stack hub-hoz. A karakterláncoknak azonosnak kell lenniük, kivéve a használt IP-címeket.

2. Az Azure-ban és Azure Stack hub-ban adja hozzá a megfelelő kapcsolati karakterláncot [alkalmazás-beállításként](https://docs.microsoft.com/azure/app-service/web-sites-configure) a webalkalmazásban, a név előtagként használva `SQLCONNSTR\_` .

3. **Mentse** a webalkalmazás beállításait, és indítsa újra az alkalmazást.

## <a name="enable-automatic-scaling-in-global-azure"></a>Automatikus skálázás engedélyezése a globális Azure-ban

Ha App Service környezetben hozza létre a webalkalmazást, az egy példánnyal kezdődik. A példányok hozzáadásával automatikusan kibővítheti az alkalmazást, így több számítási erőforrást is biztosíthat az alkalmazáshoz. Hasonlóképpen, automatikusan méretezheti és csökkentheti az alkalmazás által igényelt példányok számát.

> [!Note]  
> Meg kell adnia egy App Service tervet a méretezési és méretezési beállítások konfigurálásához. Ha nem rendelkezik csomaggal, hozzon létre egyet a következő lépések megkezdése előtt.

### <a name="enable-automatic-scale-out"></a>Automatikus kibővítés engedélyezése

1. Az Azure-ban keresse meg a kibővíteni kívánt webhelyek App Service tervét, majd válassza a **kibővítés (App Service terv)** lehetőséget.

    ![Horizontális felskálázás](media/solution-deployment-guide-hybrid/image16.png)

2. Válassza az **autoskálázás engedélyezése**lehetőséget.

    ![Az autoskálázás engedélyezése](media/solution-deployment-guide-hybrid/image17.png)

3. Adja meg az **autoskálázási beállítás nevének**nevét. Az **alapértelmezett** automatikus skálázási szabálynál válassza a **skála mérőszám alapján**lehetőséget. A **példányokra vonatkozó korlátokat** állítsa **minimumra: 1**, **maximum: 10**, **alapértelmezett érték: 1**.

    ![Automatikus skálázás konfigurálása](media/solution-deployment-guide-hybrid/image18.png)

4. Válassza **a + szabály hozzáadása**lehetőséget.

5. A **metrika forrása**mezőben válassza ki az **aktuális erőforrás**elemet. A szabályhoz a következő feltételeket és műveleteket használhatja.

**Feltételek**

1. Az **idő összesítése** területen válassza az **átlag**lehetőséget.

2. A **metrika neve**területen válassza a **CPU százalék**elemet.

3. Az **operátor**területen válassza a **nagyobb, mint**lehetőséget.

   - Állítsa a **küszöbértéket** **50**-re.
   - Állítsa az **időtartamot** **10**értékre.

**Művelet**

1. A **művelet**területen válassza **a számának növekedése a**következővel:.

2. Állítsa a **példányszámot** a **2**értékre.

3. Állítsa a **lehűlni** a **5-öt**.

4. Válassza a **Hozzáadás** lehetőséget.

5. Válassza a **+ szabály hozzáadása**elemet.

6. A **metrika forrása**mezőben válassza ki az **aktuális erőforrás elemet.**

   > [!Note]  
   > Az aktuális erőforrás tartalmazni fogja a App Service terv nevét/GUID azonosítóját, és az **erőforrástípus** és az **erőforrás** legördülő listája nem lesz elérhető.

### <a name="enable-automatic-scale-in"></a>Automatikus skálázás engedélyezése

A forgalom csökkenése esetén az Azure-webalkalmazás automatikusan csökkentheti az aktív példányok számát a költségek csökkentése érdekében. Ez a művelet kevésbé agresszív, mint a felskálázás, és csökkenti az alkalmazás felhasználóira gyakorolt hatást.

1. Navigáljon az **alapértelmezett** Felskálázási feltételhez, majd válassza a **+ szabály hozzáadása**elemet. A szabályhoz a következő feltételeket és műveleteket használhatja.

**Feltételek**

1. Az **idő összesítése** területen válassza az **átlag**lehetőséget.

2. A **metrika neve**területen válassza a **CPU százalék**elemet.

3. Az **operátor**területen válassza a **kisebb, mint**lehetőséget.

   - Állítsa a **küszöbértéket** **30**-ra.
   - Állítsa az **időtartamot** **10**értékre.

**Művelet**

1. A **művelet**területen válassza **a szám csökkentése a**következővel:.

   - Állítsa a **példányszámot** **1-re**.
   - Állítsa a **lehűlni** a **5-öt**.

2. Válassza a **Hozzáadás** lehetőséget.

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>Traffic Manager profil létrehozása és a Felhőbeli skálázás konfigurálása

Hozzon létre egy Traffic Manager-profilt az Azure-ban, majd konfigurálja a végpontokat a Felhőbeli skálázás engedélyezéséhez.

### <a name="create-traffic-manager-profile"></a>Traffic Manager profil létrehozása

1. Válassza az **Erőforrás létrehozása** lehetőséget.
2. Válassza a **Hálózat** lehetőséget.
3. Válassza ki **Traffic Manager profilt** , és konfigurálja a következő beállításokat:

   - A **név**mezőben adja meg a profil nevét. Ennek a **névnek egyedinek kell** lennie az trafficmanager.net zónában, és egy új DNS-név (például northwindstore.trafficmanager.net) létrehozására szolgál.
   - Az **útválasztási módszer**beállításnál válassza ki a **súlyozott**értéket.
   - Az **előfizetés**mezőben válassza ki azt az előfizetést, amelyben a profilt létre szeretné hozni.
   - Az **erőforráscsoport**területen hozzon létre egy új erőforráscsoportot ehhez a profilhoz.
   - Az **Erőforráscsoport helye** területen válassza ki az erőforráscsoport helyét. Ez a beállítás az erőforráscsoport helyére vonatkozik, és nincs hatással a globálisan telepített Traffic Manager-profilra.

4. Kattintson a **Létrehozás** gombra.

    ![Traffic Manager profil létrehozása](media/solution-deployment-guide-hybrid/image19.png)

   Ha a Traffic Manager-profil globális telepítése befejeződött, az a (z) alatt létrehozott erőforráscsoport erőforrásainak listájában jelenik meg.

### <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

1. Keresse meg a létrehozott Traffic Manager profilt. Ha a profilhoz tartozó erőforráscsoporthoz navigál, válassza ki a profilt.

2. **Traffic Manager profilban**a **Beállítások**területen válassza a **végpontok**lehetőséget.

3. Válassza a **Hozzáadás** lehetőséget.

4. A **végpont hozzáadása**területen használja az Azure stack hub következő beállításait:

   - A **Típus mezőben**válassza a **külső végpont**lehetőséget.
   - Adja meg a végpont **nevét** .
   - A **teljes tartománynév (FQDN) vagy az IP-** cím mezőben adja meg az Azure stack hub-webalkalmazás külső URL-címét.
   - A **súlyozáshoz**tartsa meg az alapértelmezett értéket, **1**. Ez a súlyozás azt eredményezi, hogy az adott végpontra irányuló forgalom kifogástalan állapotú lesz.
   - Hagyja **Letiltva a Hozzáadás másként** jelölést.

5. Az Azure Stack hub-végpont mentéséhez kattintson **az OK gombra** .

Ezután konfigurálja az Azure-végpontot.

1. **Traffic Manager profil**lapon válassza a **végpontok**lehetőséget.
2. Válassza a **+ Hozzáadás**lehetőséget.
3. A **végpont hozzáadása**lehetőségnél használja a következő beállításokat az Azure-hoz:

   - A **Típus mezőben**válassza az **Azure-végpont**lehetőséget.
   - Adja meg a végpont **nevét** .
   - A **cél erőforrástípus mezőben**válassza a **app Service**lehetőséget.
   - A **cél erőforrásnál**válassza az **app Service kiválasztása** lehetőséget az azonos előfizetésben lévő Web Apps listájának megtekintéséhez.
   - Az **Erőforrás** panelen válassza ki az első végpontként hozzáadni kívánt alkalmazásszolgáltatást.
   - A **súlyozáshoz**válassza a **2**lehetőséget. Ez a beállítás azt eredményezi, hogy a végponthoz tartozó összes forgalom nem kifogástalan állapotú, vagy ha van olyan szabály/riasztás, amely az indításkor átirányítja a forgalmat.
   - Hagyja **Letiltva a Hozzáadás másként** jelölést.

4. Az Azure-végpont mentéséhez kattintson **az OK gombra** .

Mindkét végpont konfigurálása után **Traffic Manager profilban** szerepelnek a **végpontok**kiválasztása után. A következő képernyőfelvételen szereplő példa két végpontot mutat be, amelyek mindegyike állapota és konfigurációs adatai szerepelnek.

![Végpontok](media/solution-deployment-guide-hybrid/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>Application Insights figyelésének és riasztásának beállítása

Az Azure Application Insights segítségével figyelheti az alkalmazást, és riasztásokat küldhet a konfigurált feltételek alapján. Néhány példa: az alkalmazás nem érhető el, hibákba ütközik, vagy teljesítménnyel kapcsolatos problémákat mutat.

A riasztások létrehozásához Application Insights metrikákat kell használnia. Amikor ezek a riasztások aktiválódnak, a webalkalmazás példánya automatikusan átvált Azure Stack hub-ról az Azure-ra a vertikális felskálázáshoz, majd visszahelyezi a Azure Stack hub-ra a méretezéshez.

### <a name="create-an-alert-from-metrics"></a>Riasztás létrehozása mérőszámokból

Keresse meg az oktatóanyaghoz tartozó erőforráscsoportot, majd válassza ki a Application Insights példányt a **Application Insights**megnyitásához.

![Application Insights](media/solution-deployment-guide-hybrid/image21.png)

Ennek a nézetnek a használatával kibővíthető riasztást és a riasztások méretezését is létrehozhatja.

### <a name="create-the-scale-out-alert"></a>A kibővíthető riasztás létrehozása

1. A **Konfigurálás**területen válassza a **riasztások (klasszikus)** lehetőséget.
2. Válassza a **metrikus riasztás hozzáadása (klasszikus)** lehetőséget.
3. A **szabály hozzáadása**területen adja meg a következő beállításokat:

   - A **név**mezőben adja meg az **Azure-felhőbe való betörést**.
   - A **Leírás** megadása nem kötelező.
   - A **forrás** > **riasztás**területen válassza a **metrikák**lehetőséget.
   - A **feltételek**területen válassza ki az előfizetését, a Traffic Manager profiljához tartozó erőforráscsoportot, valamint az erőforrás Traffic Manager profiljának nevét.

4. A **metrika**mezőben válassza a **kérelmek aránya**lehetőséget.
5. A **feltétel**beállításnál válassza a **nagyobb, mint**lehetőséget.
6. A **küszöbérték**mezőbe írja be a **2**értéket.
7. Az **időtartam**mezőben válassza **az utolsó 5 perc**lehetőséget.
8. Az **értesítés**alatt:
   - Jelölje be az **e-mail-tulajdonosok, a közreműködők és az olvasók**jelölőnégyzetét.
   - Adja meg az e-mail-címét **további rendszergazdai e-mailek (ek)** számára.

9. A menüsávon válassza a **Mentés**lehetőséget.

### <a name="create-the-scale-in-alert"></a>A skála létrehozása riasztásban

1. A **Konfigurálás**területen válassza a **riasztások (klasszikus)** lehetőséget.
2. Válassza a **metrikus riasztás hozzáadása (klasszikus)** lehetőséget.
3. A **szabály hozzáadása**területen adja meg a következő beállításokat:

   - A **név**mezőbe írja be a **Méretezés vissza Azure stack hubhoz**.
   - A **Leírás** megadása nem kötelező.
   - A **forrás** > **riasztás**területen válassza a **metrikák**lehetőséget.
   - A **feltételek**területen válassza ki az előfizetését, a Traffic Manager profiljához tartozó erőforráscsoportot, valamint az erőforrás Traffic Manager profiljának nevét.

4. A **metrika**mezőben válassza a **kérelmek aránya**lehetőséget.
5. A **feltétel**beállításnál válassza a **kisebb, mint**lehetőséget.
6. A **küszöbérték**mezőbe írja be a **2**értéket.
7. Az **időtartam**mezőben válassza **az utolsó 5 perc**lehetőséget.
8. Az **értesítés**alatt:
   - Jelölje be az **e-mail-tulajdonosok, a közreműködők és az olvasók**jelölőnégyzetét.
   - Adja meg az e-mail-címét **további rendszergazdai e-mailek (ek)** számára.

9. A menüsávon válassza a **Mentés**lehetőséget.

Az alábbi képernyőfelvételen a kibővíthető és a méretezési riasztások láthatók.

   ![Riasztások (klasszikus)](media/solution-deployment-guide-hybrid/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack-hub"></a>Az Azure és a Azure Stack hub közötti forgalom átirányítása

Konfigurálhatja a webalkalmazás-forgalom manuális vagy automatikus átváltását az Azure és a Azure Stack hub között.

### <a name="configure-manual-switching-between-azure-and-azure-stack-hub"></a>Manuális váltás konfigurálása az Azure és a Azure Stack hub között

Ha a webhely eléri a konfigurált küszöbértékeket, riasztást kap. A következő lépésekkel manuálisan irányíthatja át a forgalmat az Azure-ba.

1. A Azure Portal válassza ki a Traffic Manager profilt.

    ![Traffic Manager-végpontok](media/solution-deployment-guide-hybrid/image20.png)

2. Válassza a **végpontok**lehetőséget.
3. Válassza ki az **Azure-végpontot**.
4. Az **állapot**területen válassza az **engedélyezve**lehetőséget, majd kattintson a **Mentés**gombra.

    ![Azure-végpont engedélyezése](media/solution-deployment-guide-hybrid/image23.png)

5. A Traffic Manager profilhoz tartozó **végpontokon** válassza a **külső végpont**lehetőséget.
6. Az **állapot**területen válassza a **Letiltva**, majd a **Mentés**lehetőséget.

    ![Azure Stack hub-végpont letiltása](media/solution-deployment-guide-hybrid/image24.png)

A végpontok konfigurálása után az alkalmazások forgalma az Azure Stack hub-webalkalmazás helyett az Azure kibővíthető webalkalmazásra kerül.

 ![Megváltoztatott végpontok](media/solution-deployment-guide-hybrid/image25.png)

Ha vissza szeretné fordítani a folyamatot Azure Stack hubhoz, a következő lépésekkel hajthatja végre az előző lépéseket:

- Engedélyezze az Azure Stack hub-végpontot.
- Tiltsa le az Azure-végpontot.

### <a name="configure-automatic-switching-between-azure-and-azure-stack-hub"></a>Az Azure és a Azure Stack hub közötti automatikus váltás konfigurálása

Application Insights figyelést is használhat, ha az alkalmazás a Azure Functions által biztosított [kiszolgáló](https://azure.microsoft.com/overview/serverless-computing/) nélküli környezetben fut.

Ebben az esetben beállíthatja, hogy a Application Insights egy olyan webhook használatára, amely meghívja a Function alkalmazást. Ez az alkalmazás automatikusan engedélyezi vagy letiltja a végpontot egy riasztásra válaszul.

Az automatikus forgalmi váltás konfigurálásához kövesse az alábbi lépéseket útmutatóként.

1. Hozzon létre egy Azure Function-alkalmazást.
2. Hozzon létre egy HTTP által aktivált függvényt.
3. Importálja a Resource Manager, a Web Apps és a Traffic Manager Azure SDK-kat.
4. Kód fejlesztése a következőre:

   - Hitelesítse magát az Azure-előfizetésében.
   - Olyan paramétert használjon, amely a Traffic Manager végpontokat az Azure-ba vagy Azure Stack hub-ra irányuló közvetlen forgalomra vált.

5. Mentse a kódot, és adja hozzá a Function app URL-címét a megfelelő paraméterekkel a Application Insights riasztási szabály beállításainak **webhook** szakaszához.
6. A rendszer automatikusan átirányítja a forgalmat, amikor egy Application Insights riasztás következik be.

## <a name="next-steps"></a>További lépések

- Az Azure Cloud Patterns szolgáltatással kapcsolatos további információkért lásd: [Felhőbeli tervezési minták](https://docs.microsoft.com/azure/architecture/patterns).
