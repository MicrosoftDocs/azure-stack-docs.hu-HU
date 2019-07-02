---
title: Az Azure és az Azure Stack egy hibrid felhő üzembe helyezése |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe az Azure és az Azure Stack egy hibrid felhő.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 97869ef7659cb5619ff962fc4b3bc8facbc599ed
ms.sourcegitcommit: eccbd0098ef652919f357ef6dba62b68abde1090
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492447"
---
# <a name="tutorial-deploy-a-hybrid-cloud-solution-with-azure-and-azure-stack"></a>Oktatóanyag: Az Azure és az Azure Stack egy hibrid felhőmegoldás üzembe helyezése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ez az oktatóanyag bemutatja, hogyan helyezhet üzembe egy hibrid felhőmegoldás, amely az Azure nyilvános felhő és az Azure Stack magánfelhő használ.

Felhőalapú hibrid megoldás használatával egyesíthetők a magánfelhő megfelelőségi előnyeit a nyilvános felhő skálázhatóságát. Emellett a fejlesztők is kihasználhatja a Microsoft fejlesztői ökoszisztéma, és képességeik alkalmazni a felhőbeli és helyi környezetekben.

## <a name="overview-and-assumptions"></a>Áttekintés és Előfeltételek

Ez az oktatóanyag egy munkafolyamatot, amely lehetővé teszi a fejlesztők számára a nyilvános felhő és a egy magánfelhő-azonos WebApp üzembe helyezése beállításához. Az alkalmazás hozzáférhet a privát felhőben lévő üzemeltetett nem internetes irányítható hálózathoz. Ezek a webalkalmazások monitorozza, és a forgalom kiugrás tapasztalható, amikor a program módosítja a DNS-rekordok irányítsa át a forgalmat a nyilvános felhőben. Amikor a forgalom a megnövekedett előtt szintre csökken, adatforgalmat vissza a magánfelhő számára.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> - A hibrid kapcsolódó SQL Server adatbázis-kiszolgáló üzembe helyezése.
> - Globális Azure-beli webes alkalmazás csatlakoztatása egy hibrid hálózathoz.
> - A DNS konfigurálása a több felhőre kiterjedő méretezést.
> - A több felhőre kiterjedő skálázás SSL-tanúsítványok konfigurálása.
> - Konfigurálja, és a webes alkalmazás üzembe helyezése.
> - Traffic Manager-profil létrehozása, és konfigurálja úgy a több felhőre kiterjedő méretezését.
> - Figyelési és riasztási megnövekedett forgalmának az Application Insights beállítása.
> - Konfigurálja a globális Azure és az Azure Stack közötti váltáskor automatikus forgalmat.

### <a name="assumptions"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy a globális Azure és az Azure Stack alapszintű ismerete. Ha azt szeretné, további az oktatóanyag elindítása előtt tekintse át ezeket a cikkeket:

 - [Bevezetés az Azure-bA](https://azure.microsoft.com/overview/what-is-azure/)
 - [Az Azure Stack főbb fogalmak](../operator/azure-stack-overview.md)

Ez az oktatóanyag azt is feltételezi, hogy egy Azure-előfizetést. Ha nem rendelkezik előfizetéssel, akkor az [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a Kezdés előtt győződjön meg arról, hogy az alábbi követelményeknek:

- Az Azure Stack Development Kit (ASDK) vagy az Azure Stack integrált rendszerek az előfizetést. Az Azure Stack fejlesztői készletének üzembe helyezéséhez kövesse a [üzembe helyezése a telepítő a ASDK](../asdk/asdk-install.md).
- Az Azure Stack telepítése a következőkkel kell rendelkeznie:
  - Az Azure App Service. Az Azure Stack-operátorokról telepítheti és konfigurálhatja az Azure App Service a környezetben dolgozhat. Ehhez az oktatóanyaghoz legalább egy (1) elérhető dedikált feldolgozói szerepkörrel kell rendelkeznie az App Service.
  - A Windows Server 2016 rendszerképet.
  - A Windows Server 2016 és a egy Microsoft SQL Server-lemezképet.
  - A szükséges csomagokat és ajánlatokat.
  - A domain name for your web app. Ha nem rendelkezik egy tartománynevet, vásároljon egyet egy tartományszolgáltatótól, például a GoDaddy, Bluehost és InMotion.
- A tartomány egy megbízható hitelesítésszolgáltatótól LetsEncrypt például SSL-tanúsítvány.
- SQL Server-adatbázis kommunikál, amely támogatja az Application Insights webalkalmazás. Letöltheti a [dotnetcore-sqldb-tutorial](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial) mintaalkalmazást a Githubról.
- Hibrid hálózat egy Azure virtuális hálózat és az Azure Stack virtuális hálózat között. Részletes útmutatásért lásd: [hibrid felhő-kapcsolat konfigurálása az Azure és az Azure Stack](azure-stack-solution-hybrid-connectivity.md).

- Hibrid folyamatos integráció/folyamatos készregyártás (CI/CD) rendelkező folyamatot egy privát fordító-ügynökhöz az Azure Stacken. Részletes útmutatásért lásd: [hibrid felhőbeli identitás konfigurálása az Azure és az Azure Stack apps](azure-stack-solution-hybrid-identity.md).

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>A hibrid kapcsolódó SQL Server adatbázis-kiszolgáló üzembe helyezése

1. Jelentkezzen az Azure Stack felhasználói portálon.

2. Az a **irányítópult**válassza **Marketplace**.

    ![Azure Stack Marketplace](media/azure-stack-solution-hybrid-cloud/image1.png)

3. A **Marketplace**válassza **számítási**, és válassza a **további**. A **további**, jelölje be a **ingyenes SQL Server-licenc: Az SQL Server 2017 Developer, a Windows Server** kép.

    ![Válassza ki a virtuálisgép-lemezkép](media/azure-stack-solution-hybrid-cloud/image2.png)

4. A **ingyenes SQL Server-licenc: Az SQL Server 2017 Developer, a Windows Server**válassza **létrehozás**.

5. A **alapjai > konfigurálja az alapbeállításokat**, adjon meg egy **neve** a virtuális gép (VM), egy **felhasználónév** számára az SQL Server-rendszergazdai és a egy **jelszó** az SA.  Az a **előfizetés** legördülő listára, válassza ki az előfizetést, amelyhez a helyezi üzembe. A **erőforráscsoport**, használjon **válasszon egy meglévő** és helyezze a virtuális gép ugyanazt az erőforráscsoportot az Azure Stack-webalkalmazásként.

    ![Virtuális gép alapszintű beállításainak konfigurálása](media/azure-stack-solution-hybrid-cloud/image3.png)

6. A **mérete**, válasszon méretet a virtuális géphez. A jelen oktatóanyag esetében azt javasoljuk A2_Standard egy DS2_V2_Standard.

7. A **beállítások > választható funkciók konfigurálása**, adja meg a következő beállításokat:

   - **Storage-fiók**: Hozzon létre egy új fiókot, ha szükség van egy.
   - **Virtuális hálózat**:

     > [!Important]  
     > Ellenőrizze, hogy az SQL Server virtuális gép üzemel, a VPN-átjárók ugyanazon a virtuális hálózaton.

   - **Nyilvános IP-cím**: Az alapértelmezett beállításokat használja.
   - **Hálózati biztonsági csoport**: (NSG). Hozzon létre egy új NSG-t.
   - **Bővítmények és a figyelés**: Tartsa meg az alapértelmezett beállításokat.
   - **Diagnosztikai tárfiók**: Hozzon létre egy új fiókot, ha szükség van egy.
   - Válassza ki **OK** kattintva mentse a konfigurációt.

     ![Választható funkciók konfigurálása](media/azure-stack-solution-hybrid-cloud/image4.png)

8. A **SQL Server-beállítások**, adja meg a következő beállításokat:
   - A **SQL-kapcsolat**válassza ki a **nyilvános (Internet)** .
   - A **Port**, tartsa meg az alapértelmezett **1433-as**.
   - A **SQL-hitelesítés**válassza **engedélyezése**.

     > [!Note]  
     > Ha az SQL-hitelesítés engedélyezéséhez azt kell automatikus – adja meg "SQLAdmin" adatokat konfigurált **alapjai**.

   - A többi beállítást hagyja meg az alapértelmezett értékeket. Kattintson az **OK** gombra.

     ![SQL Server-beállítások konfigurálása](media/azure-stack-solution-hybrid-cloud/image5.png)

9. A **összefoglalás**, tekintse át a virtuális gép konfigurációját, és válassza **OK** a üzembe helyezésének megkezdéséhez.

    ![A konfiguráció összegzése](media/azure-stack-solution-hybrid-cloud/image6.png)

10. Az új virtuális gép létrehozása hosszabb időt vesz igénybe. A virtuális gépek ÁLLAPOTÁT is megtekintheti **virtuális gépek**.

    ![Virtual machines (Virtuális gépek)](media/azure-stack-solution-hybrid-cloud/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack"></a>Webalkalmazások létrehozása az Azure és az Azure Stackben

Az Azure App Service leegyszerűsíti a futtató és a webes alkalmazások kezelése. Mivel az Azure Stack egységes Azure-ral, az App Service-ben mindkét környezetben is futtatható. Az App Service-ben való az alkalmazás üzemeltetésére használni kívánt.

### <a name="create-web-apps"></a>Webalkalmazások létrehozása

1. Webalkalmazás létrehozása az Azure-ban utasításait követve [kezelése az Azure App Service-csomag](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan). Ellenőrizze, hogy helyezi a webalkalmazás ugyanazt az előfizetést és erőforráscsoportot, a hibrid hálózat.

2. Ismételje meg az előző lépést (1) az Azure Stackben.

### <a name="add-route-for-azure-stack"></a>Útvonal hozzáadása az Azure Stackhez

Az App Service az Azure Stacken irányítható, hogy a felhasználók az alkalmazás elérése a nyilvános internetről kell lennie. Ha az Azure Stackben elérhető az internetről, jegyezze fel a nyilvános IP-címe vagy URL-cím az Azure Stack web app for.

Ha egy ASDK használja, akkor [egy statikus NAT-leképezés konfigurálása](../operator/azure-stack-create-vpn-connection-one-node.md#configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal) App Service-ben elérhetővé a virtuális környezeten kívül.

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>Az Azure-ban webes alkalmazás csatlakoztatása egy hibrid hálózat

Ahhoz, hogy a webes előtér, az Azure-ban és az SQL Server-adatbázist az Azure Stack közötti kapcsolatot, a web app kapcsolódnia kell az Azure és az Azure Stack közötti hibrid hálózat. A csatlakozás engedélyezéséhez kell:

- Pont – hely kapcsolat konfigurálása.
- A WebApp konfigurálását.
- Módosítsa a helyi hálózati átjárót, az Azure Stackben.

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>Az Azure virtuális hálózat pont – hely kapcsolat konfigurálása

A virtuális hálózati átjáró a hibrid hálózat Azure oldalán engedélyeznie kell a pont – hely kapcsolatok az Azure App Service integrálható.

1. Az Azure-ban keresse meg a virtuális hálózati átjáró oldalán. A **beállítások**válassza **pont – hely konfiguráció**.

    ![Pont – hely lehetőség](media/azure-stack-solution-hybrid-cloud/image8.png)

2. Válassza ki **konfigurálás most** pont-hely konfigurálása.

    ![Indítsa el a pont – hely konfiguráció](media/azure-stack-solution-hybrid-cloud/image9.png)

3. Az a **pont – hely** konfigurációs lap, adja meg a használni kívánt magánhálózati IP-címtartományt **címkészlet**.

   > [!Note]  
   > Győződjön meg arról, hogy a megadott tartomány nincs átfedésben a globális Azure-ban összetevők, a hibrid hálózat alhálózatai által már használt címtartományok.

   A **Alagúttípus**, törölje a jelet a **IKEv2 VPN**. Válassza ki **mentése** pont – hely konfigurálásának befejezéséhez.

   ![Pont – hely beállításai](media/azure-stack-solution-hybrid-cloud/image10.png)

### <a name="integrate-the-azure-app-service-app-with-the-hybrid-network"></a>Az Azure App Service-alkalmazás és a hibrid hálózat integrációja

1. Az alkalmazás csatlakoztatása az Azure virtuális hálózat, kövesse a [átjáró szükséges a VNet-integráció](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#gateway-required-vnet-integration).

2. Navigáljon a **beállítások** az App Service-csomag a webalkalmazás üzemeltetéséhez. A **beállítások**válassza **hálózatkezelés**.

    ![Hálózatkezelés konfigurálása](media/azure-stack-solution-hybrid-cloud/image11.png)

3. A **VNET-integráció**válassza **Ide kattintva kezelheti**.

    ![VNET-integráció kezelése](media/azure-stack-solution-hybrid-cloud/image12.png)

4. Válassza ki a konfigurálni kívánt virtuális Hálózatot. A **IP CÍMEK IRÁNYÍTJA a virtuális hálózat**, adja meg az IP-címtartományt az Azure virtuális hálózat, az Azure Stack virtuális hálózat és a pont – hely címtereket. Válassza ki **mentése** érvényesítéséhez, és ezek a beállítások mentéséhez.

    ![IP-címtartományok irányíthatja](media/azure-stack-solution-hybrid-cloud/image13.png)

App Service-ben együttműködéséről az Azure virtuális hálózatok kapcsolatos további információkért lásd: [az alkalmazás integrálása az Azure Virtual Network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet).

### <a name="configure-the-azure-stack-virtual-network"></a>Az Azure Stack virtuális hálózat konfigurálása

A helyi hálózati átjárót, az Azure Stack virtuális hálózatban kell konfigurálni kell a forgalom az App Service-végpont és telephely-címtartományból.

1. Az Azure Stack, lépjen **helyi hálózati átjáró**. A **Beállítások** területen válassza a **Konfiguráció** elemet.

    ![Átjáró konfigurációs beállítás](media/azure-stack-solution-hybrid-cloud/image14.png)

2. A **címtér**, adja meg a pont – hely címtartományt a virtuális hálózati átjárót, az Azure-ban.

    ![Pont-hely típusú címtér](media/azure-stack-solution-hybrid-cloud/image15.png)

3. Válassza ki **mentése** érvényesítéséhez és a konfiguráció mentéséhez.

## <a name="configure-dns-for-cross-cloud-scaling"></a>A DNS konfigurálása a több felhőre kiterjedő méretezése

A több felhőre kiterjedő alkalmazások megfelelően konfigurálja a DNS, a felhasználók hozzáférhessenek a webalkalmazás globális Azure-ban és az Azure Stack példányait. Ebben az oktatóanyagban a DNS-konfiguráció is lehetővé teszi az Azure Traffic Manager irányíthatja a forgalmat, ha a terhelés növekszik vagy csökken.

Ez az oktatóanyag az Azure DNS használatával kezelheti a DNS-ben, mert App Service-tartományok nem fog működni.

### <a name="create-subdomains"></a>Create subdomains

A Traffic Manager DNS CNAME-rekordokat támaszkodik, mert egy altartomány megfelelően irányíthatja a forgalmat végpontok van szükség. További információ a DNS-rekordok és a tartomány-hozzárendelés: [tartományok leképezése a Traffic Managerrel](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name).

Az Azure-végpont létre fog hozni egy altartományt, hogy a felhasználók használhatják a webes alkalmazás eléréséhez. A jelen oktatóanyag esetében használható **app.northwind.com**, de ezt az értéket a saját tartomány alapján testreszabhatja.

Is kell egy A rekordot az Azure Stack-végpont létrehozása egy altartományt. Használhat **azurestack.northwind.com**.

### <a name="configure-a-custom-domain-in-azure"></a>Egyéni tartomány konfigurálása az Azure-ban

1. Adja hozzá a **app.northwind.com** állomásnév szerint az Azure webalkalmazás való [egy CNAME rekord leképezése az Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

### <a name="configure-custom-domains-in-azure-stack"></a>Egyéni tartományok konfigurálása az Azure Stackben

1. Adja hozzá a **azurestack.northwind.com** állomásnév szerint az Azure Stack web App [egy A rekordot az Azure App Service-leképezés](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record). Az App Service-alkalmazás internetről elérhető IP-címet használja.

2. Adja hozzá a **app.northwind.com** állomásnév szerint az Azure Stack web App [egy CNAME rekord leképezése az Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record). Használja az állomásnevet, konfigurálta az előző lépésben (1) a cél számára a CNAME REKORDOT.

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>A több felhőre kiterjedő skálázás SSL-tanúsítványok konfigurálása

Fontos annak biztosítása érdekében, a webalkalmazás által gyűjtött bizalmas adatok biztonságos, az átvitel során, és ha az SQL-adatbázisban tárolt.

Konfigurálja az Azure és az Azure Stack web apps SSL-tanúsítványokat használ az összes bejövő forgalmat.

### <a name="add-ssl-to-azure-and-azure-stack"></a>SSL hozzáadása az Azure és az Azure Stackben

Az SSL hozzáadása az Azure-bA:

1. Make sure that the SSL certificate you obtain is valid for the subdomain you created. (Nem probléma, a helyettesítő tanúsítvány használatára.)

2. Az Azure-ban, kövesse az utasításokat a **a webalkalmazás előkészítése** és **az SSL-tanúsítvány kötése** szakaszai a [meglévő egyéni SSL-tanúsítvány kötése az Azure Web Appshez](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) a cikk. Válassza ki **SNI-alapú SSL** , a **SSL-típus**.

3. Az összes forgalom átirányítása HTTPS-portjához. Kövesse az utasításokat a **HTTPS kényszerítése** szakaszában a [meglévő egyéni SSL-tanúsítvány kötése az Azure Web Appshez](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) cikk.

Azure Stack SSL hozzáadása:

- Ismételje meg az 1-3, amelyet az Azure-hoz használt.

## <a name="configure-and-deploy-the-web-app"></a>Konfigurálása és üzembe helyezése a webalkalmazásban

A jelentés a telemetriai a megfelelő Application Insights-példányhoz az alkalmazáskód konfigurálása fog, és a webalkalmazások konfigurálása a megfelelő kapcsolati karakterláncok. Az Application Insights kapcsolatos további információkért lásd: [Mi az Application Insights?](https://docs.microsoft.com/azure/application-insights/app-insights-overview)

### <a name="add-application-insights"></a>Az Application Insights hozzáadása

1. Nyissa meg a webes alkalmazás a Microsoft Visual Studio.

2. [Az Application Insights hozzáadása](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-client-side-telemetry-for-web-applications) továbbítására a telemetriát az Application Insights használó hozhat létre riasztásokat, amikor a webes forgalom növeli vagy csökkenti a projekthez.

### <a name="configure-dynamic-connection-strings"></a>Dinamikus csatlakozási karakterláncok konfigurálása

Minden példánya a webalkalmazás egy másik módszer használatával csatlakozhat az SQL database. Az Azure-ban az alkalmazás az SQL Server virtuális gép (VM) magánhálózati IP-címet használja, és az alkalmazás az Azure Stackben használja az SQL Server rendszerű virtuális gép nyilvános IP-címét.

> [!Note]  
> Az Azure Stack integrált rendszer a nyilvános IP-cím nem lehet internetről elérhető. A az Azure Stack Development Kit (ASDK), a nyilvános IP-cím nem a ASDK kívülről irányítható.

App Service-ben a környezeti változók használatával adja át egy másik kapcsolati karakterláncot az alkalmazás összes példánya.

1. Nyissa meg az alkalmazást a Visual Studióban.

2. Nyissa meg a Startup.cs, és keresse meg a következő kódrészletet:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. Cserélje le az előző kódblokkot az alábbi kód, amely a megadott kapcsolati karakterláncot használja a *appsettings.json* fájlt:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-app-settings"></a>Az App Service beállításainak konfigurálása

1. Kapcsolati karakterláncok létrehozása az Azure és az Azure Stackben. A karakterláncok azonosnak kell lennie, kivéve az IP-címek vannak már használatban.

2. Az Azure és az Azure Stack, adja hozzá a megfelelő kapcsolati karakterláncot [alkalmazásbeállítást,](https://docs.microsoft.com/azure/app-service/web-sites-configure) a web app alkalmazásban használatával `SQLCONNSTR\_` előtagjaként a nevében.

3. **Mentés** a webalkalmazás-beállítások, és indítsa újra az alkalmazást.

## <a name="enable-automatic-scaling-in-global-azure"></a>Globális Azure-ban az automatikus skálázás engedélyezése

App Service environment-környezetben a webes alkalmazás létrehozásakor elindul egy példánnyal rendelkező. Meg lehet automatikusan horizontálisan adjon hozzá további példányokat biztosít a nagyobb számítási erőforrás biztosítható az alkalmazások. Hasonlóképpen, automatikusan skálázhatja a, és csökkentse a példányok számát az alkalmazás igényeinek megfelelően.

> [!Note]  
> Szüksége lesz az App Service-csomag horizontális felskálázást és a méretezési csoport konfigurálása. Ha nem rendelkezik egy csomagot, hozzon létre egyet a következő lépések megkezdése előtt.

### <a name="enable-automatic-scale-out"></a>Engedélyezze az automatikus horizontális felskálázás

1. Az Azure-ban keresse meg az App Service-csomag a horizontális felskálázás, és válassza ki a kívánt helyek **horizontális felskálázás (App Service-csomag)** .

    ![Horizontális felskálázás](media/azure-stack-solution-hybrid-cloud/image16.png)

2. Válassza ki **automatikus skálázás engedélyezése**.

    ![Automatikus skálázás engedélyezése](media/azure-stack-solution-hybrid-cloud/image17.png)

3. Adjon meg egy nevet **automatikus skálázási beállítás neve**. Az a **alapértelmezett** automatikus skálázási szabály, jelölje be **skálázás metrika alapján**. Állítsa be a **Példányszámkorlátoknál** való **Minimum: 1**, **maximális: 10**, és **alapértelmezett: 1**.

    ![Automatikus skálázás konfigurálása](media/azure-stack-solution-hybrid-cloud/image18.png)

4. Válassza ki **és a egy szabály hozzáadásához**.

5. A **metrika forrás**válassza **aktuális erőforrás**. Használja a következő feltételeket és műveleteket a szabályhoz.

**Feltételek**

1. A **idő összesítése,** kiválasztása **átlagos**.

2. A **metrika neve**válassza **processzorhasználat**.

3. A **operátor**válassza **nagyobb, mint**.

   - Állítsa be a **küszöbérték** való **50**.
   - Állítsa be a **időtartama** való **10**.

**Művelet**

1. A **művelet**válassza **mennyiség növelése a következővel**.

2. Állítsa be a **példányszám** való **2**.

3. Állítsa be a **várakozási** való **5**.

4. Válassza a **Hozzáadás** lehetőséget.

5. Válassza ki a **és a egy szabály hozzáadásához**.

6. A **metrika forrás**válassza **aktuális erőforrás.**

   > [!Note]  
   > Az aktuális erőforrás tartalmazni fogja az App Service-csomag neve vagy GUID-Azonosítóját, és a **erőforrástípus** és **erőforrás** legördülő listák nem lesz elérhető.

### <a name="enable-automatic-scale-in"></a>Az automatikus méretezés engedélyezése

Amikor a forgalom csökken, az Azure web app automatikusan csökkentheti a költségek csökkentése érdekében aktív példányok száma. Ez a művelet kevésbé agresszív, mint a horizontális felskálázás, és minimalizálja az alkalmazás felhasználóinak a.

1. Keresse meg a **alapértelmezett** skálázási feltétel, válassza ki **és a egy szabály hozzáadásához**. Használja a következő feltételeket és műveleteket a szabályhoz.

**Feltételek**

1. A **idő összesítése,** kiválasztása **átlagos**.

2. A **metrika neve**válassza **processzorhasználat**.

3. A **operátor**válassza **kevesebb mint**.

   - Állítsa be a **küszöbérték** való **30**.
   - Állítsa be a **időtartama** való **10**.

**Művelet**

1. A **művelet**válassza **mennyiség csökkentése a következővel**.

   - Állítsa be a **példányszám** való **1**.
   - Állítsa be a **várakozási** való **5**.

2. Válassza a **Hozzáadás** lehetőséget.

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>Traffic Manager-profil létrehozása és konfigurálása a több felhőre kiterjedő méretezése

Traffic Manager-profil létrehozása az Azure-ban, és ezután a több felhőre kiterjedő skálázás engedélyezése végpontok konfigurálása.

### <a name="create-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

1. Válassza ki **erőforrás létrehozása**.
2. Válassza ki **hálózatkezelés**.
3. Válassza ki **Traffic Manager-profil** és a következő beállításokat:

   - A **neve**, adja meg a profil nevét. Ez a név **kell** egyedinek lennie a trafficmanager.net zónában és a egy új DNS-nevet (például northwindstore.trafficmanager.net) létrehozására használatos.
   - A **útválasztási módszer**, jelölje be a **súlyozott**.
   - A **előfizetés**, ezt a profilt a létrehozni kívánt előfizetés kiválasztásához.
   - A **erőforráscsoport**, hozzon létre egy új erőforráscsoportot, ehhez a profilhoz.
   - Az **Erőforráscsoport helye** területen válassza ki az erőforráscsoport helyét. Ez a beállítás az erőforráscsoport helyére vonatkozik, és nem befolyásolja a Traffic Manager-profilt, amely globálisan üzembe van helyezve.

4. Kattintson a **Létrehozás** gombra.

    ![Traffic Manager-profil létrehozása](media/azure-stack-solution-hybrid-cloud/image19.png)

   Ha a Traffic Manager-profil globális üzembe helyezése befejeződött, az erőforráscsoport, a létrehozott erőforrások listájában jelenik meg.

### <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

1. Keressen rá a létrehozott Traffic Manager-profil. Ha a profil az erőforrás-csoportba nyit meg, válassza ki a profilt.

2. A **Traffic Manager-profil**alatt **beállítások**válassza **végpontok**.

3. Válassza a **Hozzáadás** lehetőséget.

4. A **végpont hozzáadása**, használja a következő beállításokat az Azure Stack:

   - A **típus**válassza **külső végpont**.
   - Adjon meg egy **neve** a végponthoz.
   - A **teljes tartománynév (FQDN) vagy IP-** , adja meg a külső URL-cím az Azure Stack-webalkalmazás.
   - A **súly**, tartsa meg az alapértelmezett **1**. A weight teljes forgalmat a végpont ha kifogástalan eredményez.
   - Hagyja **letiltottként** nincs bejelölve.

5. Válassza ki **OK** mentése az Azure Stack-végpont.

Ezután konfigurálja az Azure-végpont.

1. A **Traffic Manager-profil**válassza **végpontok**.
2. Válassza ki **+ Hozzáadás**.
3. A **végpont hozzáadása**, használja a következő beállításokat az Azure-hoz:

   - A **típus**válassza **Azure-végpont**.
   - Adjon meg egy **neve** a végponthoz.
   - A **célerőforrás típusánál**válassza **App Service-ben**.
   - A **Célerőforrásnál**válassza **alkalmazásszolgáltatás kiválasztása** ugyanabban az előfizetésben a Web Apps alkalmazások listájának megtekintéséhez.
   - Az **Erőforrás** panelen válassza ki az első végpontként hozzáadni kívánt alkalmazásszolgáltatást.
   - A **súly**válassza **2**. Ez a beállítás a teljes forgalmat ezt a végpontot, ha az elsődleges végpont állapota nem megfelelő, vagy ha egy szabály, illetve riasztást, amely átirányítja a forgalmat, adatvezérelt eredményez.
   - Hagyja **letiltottként** nincs bejelölve.

4. Válassza ki **OK** mentése az Azure-végpont.

Mindkét végpontok konfigurálása után a felsorolás **Traffic Manager-profil** kiválasztásakor **végpontok**. Az alábbi képernyőfelvétel-készítés a példában két végpontot, minden egyes állapotára és az adatokkal.

![Végpontok](media/azure-stack-solution-hybrid-cloud/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>Application Insights figyelési és riasztási beállítása

Az Azure Application Insights lehetővé teszi az alkalmazás figyelésére, és küldje a riasztásokat konfigurálja feltételek alapján. Néhány példa: az alkalmazás nem érhető el, hibákat tapasztal vagy teljesítményproblémák láthatók.

Riasztások létrehozása az Application Insights-metrikák fogja használni. Ezek a riasztások indít, amikor a webalkalmazás-példányokat lesz automatikusan átvált az Azure Stack Azure-ba történő horizontális felskálázás, érdekében pedig később újból méretezése az Azure Stack.

### <a name="create-an-alert-from-metrics"></a>Hozzon létre egy riasztást a metrikák

Ebben az oktatóanyagban keresse meg az erőforráscsoportot, és válassza az Application Insights-példány megnyitásához **Application Insights**.

![Application Insights](media/azure-stack-solution-hybrid-cloud/image21.png)

Ez a nézet egy horizontális felskálázási riasztás és a méretezési csoport létrehozása a riasztás fogja használni.

### <a name="create-the-scale-out-alert"></a>A horizontális felskálázási értesítés létrehozása

1. A **KONFIGURÁLÁSA**válassza **riasztások (klasszikus)** .
2. Válassza ki **metrikariasztás hozzáadása (klasszikus)** .
3. A **szabály felvétele**, adja meg a következő beállításokat:

   - A **neve**, adja meg **Azure-felhőbe Burst**.
   - A **leírás** nem kötelező.
   - A **forrás** > **riasztás**válassza **metrikák**.
   - A **feltételek**, válassza ki az előfizetését, az erőforráscsoport, a Traffic Manager-profil és a Traffic Manager-profil az erőforrás nevét.

4. A **metrika**válassza **kérelmek gyakorisága**.
5. A **feltétel**válassza **nagyobb, mint**.
6. A **küszöbérték**, adja meg **2**.
7. A **időszak**válassza **az utolsó 5 percben**.
8. A **értesítés ezen keresztül**:
   - Jelölje be a jelölőnégyzetet a **E-mail-tulajdonosoknak, közreműködőknek és olvasóknak**.
   - Adja meg az e-mail-címe **további rendszergazdai email(s)** .

9. A menüsávban válassza **mentése**.

### <a name="create-the-scale-in-alert"></a>A riasztás a méretezési csoport létrehozása

1. A **KONFIGURÁLÁSA**válassza **riasztások (klasszikus)** .
2. Válassza ki **metrikariasztás hozzáadása (klasszikus)** .
3. A **szabály felvétele**, adja meg a következő beállításokat:

   - A **neve**, adja meg **vissza az Azure Stack méretezési**.
   - A **leírás** nem kötelező.
   - A **forrás** > **riasztás**válassza **metrikák**.
   - A **feltételek**, válassza ki az előfizetését, az erőforráscsoport, a Traffic Manager-profil és a Traffic Manager-profil az erőforrás nevét.

4. A **metrika**válassza **kérelmek gyakorisága**.
5. A **feltétel**válassza **kevesebb mint**.
6. A **küszöbérték**, adja meg **2**.
7. A **időszak**válassza **az utolsó 5 percben**.
8. A **értesítés ezen keresztül**:
   - Jelölje be a jelölőnégyzetet a **E-mail-tulajdonosoknak, közreműködőknek és olvasóknak**.
   - Adja meg az e-mail-címe **további rendszergazdai email(s)** .

9. A menüsávban válassza **mentése**.

Az alábbi képernyőfelvétel-készítés horizontális felskálázást és horizontális leskálázás riasztásokat tartalmazza.

   ![Riasztások (klasszikus)](media/azure-stack-solution-hybrid-cloud/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack"></a>Az Azure és az Azure Stack közötti forgalom átirányítása

Konfigurálhatja a manuális vagy automatikus váltás, a web app-forgalom az Azure és az Azure Stack közötti váltáskor.

### <a name="configure-manual-switching-between-azure-and-azure-stack"></a>Konfigurálja az Azure és az Azure Stack közötti manuális váltása

A küszöbértékek határozzák meg a webhely elérésekor riasztást kap. Az alábbi lépések segítségével manuálisan forgalom átirányítása az Azure-bA.

1. Az Azure Portalon válassza ki a Traffic Manager-profil.

    ![Traffic Manager-végpontok](media/azure-stack-solution-hybrid-cloud/image20.png)

2. Válassza ki **végpontok**.
3. Válassza ki a **Azure-végpont**.
4. Alatt **állapot**válassza **engedélyezve**, majd válassza ki **mentése**.

    ![Azure-végpont engedélyezése](media/azure-stack-solution-hybrid-cloud/image23.png)

5. A **végpontok** a Traffic Manager-profil kiválasztása **külső végpont**.
6. Alatt **állapot**válassza **letiltott**, majd válassza ki **mentése**.

    ![Tiltsa le az Azure Stack-végpont](media/azure-stack-solution-hybrid-cloud/image24.png)

A végpontok konfigurálása után a app-forgalom a horizontális felskálázás az Azure web app helyett az Azure Stack web app kerül.

 ![A végpontok megváltozott](media/azure-stack-solution-hybrid-cloud/image25.png)

A folyamat fordított vissza az Azure Stackhez, használja az előző lépéseket:

- Az Azure Stack-végpont engedélyezéséhez.
- Tiltsa le az Azure-végpont.

### <a name="configure-automatic-switching-between-azure-and-azure-stack"></a>Az Azure és az Azure Stack közötti automatikus váltás konfigurálása

Használhatja azt is, ha az alkalmazás fut, Application Insights-figyelést egy [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) Azure Functions által nyújtott környezetben.

Ebben a forgatókönyvben az Application Insights egy webhookot, amely meghívja a függvényalkalmazás használatára konfigurálhatja. Ez az alkalmazás automatikusan letiltja vagy engedélyezi egy végpontot egy riasztásra adott válaszként.

Alapján a következő lépések segítségével konfigurálhatja a automatikus forgalom vált.

1. Hozzon létre egy Azure-függvényalkalmazást.
2. Hozzon létre egy HTTP-eseményindítóval aktivált függvényt.
3. Az Azure SDK-k importálása a Resource Manager, a Web Apps és a Traffic Manager.
4. Fejlesztés a kódot:

   - Hitelesítés az Azure-előfizetéshez.
   - Egy paraméter, amely ki-és Azure-ban a forgalom a Traffic Manager-végpontok használata.

5. Mentse a kódot, és adja hozzá a megfelelő paramétereket a függvényalkalmazás URL-CÍMÉT a **Webhook** az Application Insights-riasztási szabály beállításai.
6. Forgalmat a rendszer automatikusan átirányítja az Application Insights-riasztások aktiválódásakor.

## <a name="next-steps"></a>További lépések

- Az Azure-minták Felhőkhöz kapcsolatos további információkért lásd: [tervezési minták Felhőkhöz](https://docs.microsoft.com/azure/architecture/patterns).
