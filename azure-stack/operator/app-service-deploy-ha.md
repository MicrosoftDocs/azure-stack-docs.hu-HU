---
title: Azure Stack hub App Service üzembe helyezése egy magasan elérhető konfigurációban
description: Megtudhatja, hogyan helyezheti üzembe a App Servicet a Azure Stack hub-ban egy magasan elérhető konfiguráció használatával.
author: BryanLa
ms.topic: article
ms.date: 01/02/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/02/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: e6d8708b15d5402d16b17a722640c9bf0c2251c4
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82848030"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>App Service üzembe helyezése egy magasan elérhető konfigurációban

Ebből a cikkből megtudhatja, hogyan használhatók a Azure Stack hub Marketplace-elemek az Azure Stack hub-hoz való App Service központi telepítésére egy magasan elérhető konfigurációban. A piactéren elérhető elemek mellett ez a megoldás a [appservice-fájlmegosztás-SQLServer-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure stack hub gyors üzembe helyezési sablont is használja. Ez a sablon automatizálja a App Service erőforrás-szolgáltató üzemeltetéséhez szükséges, magasan elérhető infrastruktúra létrehozását. Ezt követően a rendszer telepíti a App Service a rendelkezésre álló, magasan elérhető virtuálisgép-infrastruktúrára. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>A magasan elérhető App Service infrastruktúra-alapú virtuális gépek üzembe helyezése
A [appservice-fájlmegosztás-SQLServer-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure stack hub gyors üzembe helyezési sablonja leegyszerűsíti app Service központi telepítését magas rendelkezésre állású konfigurációban. Az alapértelmezett szolgáltatói előfizetésben kell központilag telepíteni. 

Ha Azure Stack központban hoz létre egyéni erőforrást, a sablon a következőket hozza létre:
- Egy virtuális hálózat és szükséges alhálózatok.
- Fájlkiszolgáló, SQL Server és Active Directory tartományi szolgáltatások (AD DS) alhálózatok hálózati biztonsági csoportjai.
- Storage-fiókok virtuálisgép-lemezekhez és a fürt Felhőbeli tanúja.
- Egy belső terheléselosztó a magánhálózati IP-címmel rendelkező SQL virtuális gépekhez, amelyek az SQL AlwaysOn-figyelőhöz vannak kötve.
- Három rendelkezésre állási csoport a AD DShoz, a fájlkiszolgáló fürthöz és az SQL-fürthöz.
- Két csomópontos SQL-fürt.
- Két csomópontos fájlkiszolgáló-fürt.
- Két tartományvezérlő.

### <a name="required-azure-stack-hub-marketplace-items"></a>Szükséges Azure Stack hub Marketplace-elemek
A sablon használata előtt győződjön meg arról, hogy az alábbi [Azure stack hub Marketplace-elemek](azure-stack-marketplace-azure-items.md) elérhetők a Azure stack hub-példányban:

- Windows Server 2016 Datacenter Core rendszerkép (AD DS és fájlkiszolgáló virtuális gépekhez)
- SQL Server 2016 SP2 Windows Server 2016 rendszeren (Enterprise)
- Legújabb SQL IaaS-bővítmény 
- A PowerShell kívánt állapotának legújabb konfigurációs bővítménye 

> [!TIP]
> A sablonra vonatkozó követelményekkel és az alapértelmezett értékekkel kapcsolatos további részletekért tekintse meg [a sablonhoz tartozó Readme fájlt](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) a githubon. 

### <a name="deploy-the-app-service-infrastructure"></a>A App Service infrastruktúra üzembe helyezése
Az ebben a szakaszban ismertetett lépéseket követve hozzon létre egy egyéni központi telepítést a **appservice-fájlmegosztás-SQLServer-ha** Azure stack hub gyorsindító sablonnal.

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Válassza **\+** **az erőforrás** > létrehozása**Egyéni**lehetőséget, majd **template Deployment**.

   ![Egyéni sablon központi telepítése](media/app-service-deploy-ha/1.png)


3. A **Custom Deployment (egyéni üzembe helyezés** ) panelen válassza a **sablon** > -**Gyorsindítás sablon** szerkesztése lehetőséget, majd az elérhető egyéni sablonok legördülő listájában válassza ki a **appservice-fájlmegosztás-SQLServer-ha** sablont. Kattintson **az OK**, majd a **Mentés**gombra.

   ![Válassza ki a appservice-fájlmegosztás-SQLServer-ha Gyorsindítás sablont](media/app-service-deploy-ha/2.png)

4. Az **Egyéni telepítés** panelen válassza a **Paraméterek szerkesztése** lehetőséget, és görgessen lefelé az alapértelmezett sablon értékeinek áttekintéséhez. Szükség szerint módosítsa ezeket az értékeket az összes kötelező paraméter információjának megadásához, majd kattintson **az OK**gombra.<br><br> A (z),,, `ADMINPASSWORD`és `FILESHAREOWNERPASSWORD` `FILESHAREUSERPASSWORD` `SQLSERVERSERVICEACCOUNTPASSWORD` `SQLLOGINPASSWORD` paraméterekhez legalább összetett jelszavakat adjon meg.
    
   ![Egyéni telepítési paraméterek szerkesztése](media/app-service-deploy-ha/3.png)

5. Az **Egyéni telepítés** panelen ellenőrizze, hogy az **alapértelmezett szolgáltatói előfizetés** van-e kiválasztva a használni kívánt előfizetésként, majd hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot az egyéni telepítéshez.<br><br> Ezután válassza ki az erőforráscsoport helyét (ASDK-telepítések esetén**helyi** ), majd kattintson a **Létrehozás**gombra. Az egyéni központi telepítési beállítások érvényesítése a sablon üzembe helyezésének megkezdése előtt történik.

    ![Egyéni központi telepítés létrehozása](media/app-service-deploy-ha/4.png)

6. A felügyeleti portálon válassza az **erőforráscsoportok** elemet, majd az egyéni telepítéshez létrehozott erőforráscsoport nevét (ebben a példában az**app-Service-ha** lehetőséget). Tekintse meg a központi telepítés állapotát, és győződjön meg arról, hogy az összes központi telepítés sikeresen befejeződött.

   > [!NOTE]
   > A sablon üzembe helyezése körülbelül egy órát vesz igénybe.

   [![](media/app-service-deploy-ha/5-sm.png "Review template deployment status")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>Sablon kimenetének rögzítése
Miután a sablon üzembe helyezése sikeresen befejeződött, jegyezze fel a sablon központi telepítési kimeneteit. Ezt az információt a App Service telepítőjének futtatásakor kell megadnia.

Ügyeljen arra, hogy a következő kimeneti értékeket jegyezze fel:
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

A sablon kimeneti értékeinek felderítéséhez kövesse az alábbi lépéseket:

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. A felügyeleti portálon válassza az **erőforráscsoportok** elemet, majd az egyéni telepítéshez létrehozott erőforráscsoport nevét (ebben a példában az**app-Service-ha** lehetőséget). 

3. Kattintson a **központi telepítések** elemre, és válassza a **Microsoft. template**lehetőséget.

    ![Microsoft. Sablonalapú telepítés](media/app-service-deploy-ha/6.png)

4. A **Microsoft. template** telepítésének kiválasztása után válassza a **kimenetek** lehetőséget, és jegyezze fel a sablon paraméter kimenetét. Ez az információ a App Service telepítésekor szükséges.

    ![Paraméter kimenete](media/app-service-deploy-ha/7.png)

## <a name="deploy-app-service-in-a-highly-available-configuration"></a>App Service üzembe helyezése egy magasan elérhető konfigurációban
Kövesse az ebben a szakaszban ismertetett lépéseket a Azure Stack hub App Servicejának központi telepítéséhez magas rendelkezésre állású konfigurációban a [appservice-fájlmegosztás-SQLServer-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure stack hub rövid útmutatójának sablonja alapján. 

A App Service erőforrás-szolgáltató telepítése után az ajánlatokat és a csomagokat is felveheti. A felhasználók ezután előfizethetnek a szolgáltatás beszerzésére és az alkalmazások létrehozásának megkezdésére.

> [!IMPORTANT]
> Az erőforrás-szolgáltatói telepítő futtatása előtt ellenőrizze, hogy elolvasta-e az egyes App Service kiadásokhoz tartozó kibocsátási megjegyzéseket, hogy megismerje az új funkciókat, javításokat, valamint az üzembe helyezést befolyásoló ismert problémákat.

### <a name="prerequisites"></a>Előfeltételek
A App Service telepítőjének futtatása előtt több lépésre van szükség a következő témakörben leírtak szerint: az [első lépések a app Service a Azure stack hub](azure-stack-app-service-before-you-get-started.md)-ben című cikkben.

> [!TIP]
> A [app Service cikk](azure-stack-app-service-before-you-get-started.md) első lépéseinek megkezdése előtt leírt lépések nem szükségesek, mert a sablon központi telepítése konfigurálja az infrastruktúra-alapú virtuális gépeket.

- [Töltse le a app Service telepítőjét és a segítő parancsfájlokat](azure-stack-app-service-before-you-get-started.md#installer-and-helper-scripts).
- [Elemek letöltése az Azure stack hub piactérről](azure-stack-app-service-before-you-get-started.md#download-items-from-the-azure-marketplace).
- [A központi telepítési típushoz szükséges tanúsítványok előállítása](azure-stack-app-service-before-you-get-started.md).
- Hozza létre az ID-alkalmazást az Azure Stack hub számára kiválasztott azonosítási szolgáltató alapján. Egy azonosító alkalmazás az [Azure ad](azure-stack-app-service-before-you-get-started.md#create-an-azure-ad-app) -hez vagy a [Active Directory összevonási szolgáltatások (AD FS)hoz](azure-stack-app-service-before-you-get-started.md#create-an-adfs-app) , és rögzíti az alkalmazás azonosítóját.
<!-- Connected --->
::: zone pivot="state-connected"
- Győződjön meg arról, hogy felvette a Windows Server 2016 Datacenter rendszerképet a Azure Stack hub piactéren. Ez a rendszerkép szükséges a App Service telepítéséhez.
::: zone-end

::: zone pivot="state-disconnected"
<!-- Disconnected --->
- **Windows Server 2016 Datacenter teljes virtuálisgép-rendszerkép a Microsoft.net 3.5.1 SP1-mel aktivált**.  A Azure App Service on Azure Stack hub megköveteli, hogy a Microsoft .NET 3.5.1 SP1 legyen aktiválva az üzembe helyezéshez használt rendszerképben. Piactér – a Windows Server 2016-lemezképek nem rendelkeznek ezzel a szolgáltatással, és a leválasztott környezetekben nem érhetők el Microsoft Update a DISM használatával telepítendő csomagok letöltéséhez. Ezért létre kell hoznia és használnia kell egy Windows Server 2016 rendszerképet ezzel a szolgáltatással, és ezzel előre engedélyezve kell lennie a leválasztott központi telepítéseknek.

   Az egyéni rendszerkép létrehozásával és a piactérhez való hozzáadásával kapcsolatos további tudnivalókért lásd: [Egyéni virtuálisgép-rendszerkép hozzáadása Azure stack hubhoz](azure-stack-add-vm-image.md) . A rendszerkép a piactérhez való hozzáadásakor ügyeljen a következő tulajdonságok megadására:

   - Közzétevő = MicrosoftWindowsServer
   - Ajánlat = WindowsServer
   - SKU = 2016 – Datacenter
   - Version = a "legújabb" verzió meghatározása

::: zone-end

<!-- For All --> 

### <a name="steps-for-app-service-deployment"></a>App Service központi telepítésének lépései
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

<!-- Connected --->
::: zone pivot="state-connected"
A App Service erőforrás-szolgáltató üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Futtassa a korábban letöltött App Service telepítőt (**appservice. exe**) rendszergazdaként egy olyan számítógépről, amely hozzáfér az Azure stack hub admin Azure Resource Management végponthoz.

2. Válassza **a telepítés app Service vagy a frissítés a legújabb verzióra**lehetőséget.

    ![App Service üzembe helyezése vagy frissítése](media/app-service-deploy-ha/01.png)

3. Fogadja el a Microsoft licencfeltételeket, és kattintson a **tovább**gombra.

    ![A Microsoft licencelési feltételei a App Service](media/app-service-deploy-ha/02.png)

4. Fogadjon el nem Microsoft licencelési feltételeket, és kattintson a **tovább**gombra.

    ![Nem a Microsofttól származó licencelési feltételek App Service](media/app-service-deploy-ha/03.png)

5. Adja meg az Azure Stack hub-környezet App Service Felhőbeli végpontjának konfigurációját.

    ![App Service a Felhőbeli végpontok konfigurálása App Service](media/app-service-deploy-ha/04.png)

6. **Kapcsolódjon** a telepítéshez használni kívánt Azure stack hub-előfizetéshez, és válassza ki a helyet. 

    ![Kapcsolódjon az Azure Stack hub-előfizetéshez App Service](media/app-service-deploy-ha/05.png)

7. Válassza a **meglévő VNet és alhálózatok** és az **erőforráscsoport nevének** használata a magasan elérhető sablon üzembe helyezéséhez használt erőforráscsoport számára lehetőséget.<br><br>Ezután válassza ki a sablon központi telepítésének részeként létrehozott virtuális hálózatot, majd válassza ki a megfelelő szerepkör-alhálózatokat a legördülő lista beállításai közül. 

    ![Vnet kiválasztása App Service](media/app-service-deploy-ha/06.png)

8. Adja meg a korábban rögzített sablon kimeneteit a fájlmegosztás elérési útjának és a fájlmegosztás tulajdonosának paramétereinek. Amikor végzett, kattintson a **Tovább**gombra.

    ![Fájlmegosztás kimeneti adatai App Service](media/app-service-deploy-ha/07.png)

9. Mivel az App Service telepítéséhez használt gép nem ugyanazon a VNet található, mint a App Service fájlmegosztás üzemeltetéséhez használt fájlkiszolgáló, a név nem oldható fel. **Ez a hiba a várt viselkedés**.<br><br>Ellenőrizze, hogy helyesek-e a fájlmegosztás UNC elérési útjának és a fiókok adatainak megadott adatok. Ezután nyomja meg az **Igen** gombot a riasztási párbeszédpanelen a app Service telepítés folytatásához.

    ![Várt hiba párbeszédpanel App Service](media/app-service-deploy-ha/08.png)

10. Adja meg az azonosító alkalmazás AZONOSÍTÓját, valamint az identitási tanúsítványok elérési útját és jelszavát, majd kattintson a **tovább**gombra:
    - Identity Application-tanúsítvány ( **SSO. appservice. local. azurestack. external. pfx**formátumban)
    - Azure Resource Manager főtanúsítvány (**AzureStackCertificationAuthority. cer**)

    ![AZONOSÍTÓ alkalmazás tanúsítványa és főtanúsítványa App Service](media/app-service-deploy-ha/008.png)

11. Ezután adja meg a fennmaradó szükséges információkat a következő tanúsítványokhoz, és kattintson a **tovább**gombra:
    - Alapértelmezett Azure Stack hub SSL-tanúsítvány (a következő formátumban: **_. appservice. local. azurestack. external. pfx**)
    - API SSL-tanúsítvány ( **API. appservice. local. azurestack. external. pfx**formátumban)
    - Közzétevői tanúsítvány ( **FTP. appservice. local. azurestack. external. pfx**formátumban) 

    ![További konfigurációs tanúsítványok App Service](media/app-service-deploy-ha/09.png)

12. Adja meg a SQL Server kapcsolódási adatait a magas rendelkezésre állású sablon központi telepítési kimenetének SQL Server kapcsolódási adataival:

    ![SQL Server a kapcsolatok adatai a App Service](media/app-service-deploy-ha/10.png)

13. Mivel az App Service telepítéséhez használt gép nem ugyanazon a VNet található, mint a App Service-adatbázisok üzemeltetéséhez használt SQL Server, a név nem oldható fel.  **Ez a várt viselkedés**.<br><br>Győződjön meg arról, hogy a SQL Server neve és a fiókadatok adatai helyesek, és nyomja meg az **Igen** gombot a app Service telepítés folytatásához. Kattintson a **Tovább** gombra.

    ![SQL Server a kapcsolatok adatai a App Service](media/app-service-deploy-ha/11.png)

14. Fogadja el az alapértelmezett szerepkör-konfigurációs értékeket, vagy váltson az ajánlott értékekre, és kattintson a **tovább**gombra.<br><br>Azt javasoljuk, hogy az App Service infrastruktúra szerepkör-példányok alapértelmezett értékeit a következőképpen módosítsák a magasan elérhető konfigurációk esetén:

    |Szerepkör|Alapértelmezett|Magasan elérhető javaslat|
    |-----|-----|-----|
    |Vezérlő szerepkör|2|2|
    |Felügyeleti szerepkör|1|3|
    |Közzétevői szerepkör|1|3|
    |Előtér-szerepkör|1|3|
    |Megosztott feldolgozói szerepkör|1|2|
    |     |     |     |

    ![Infrastruktúra-szerepkör példányának értékei App Service](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Az ebben az oktatóanyagban ajánlott értékekre való váltás a App Service telepítéséhez szükséges hardverkövetelmények megnövekedésével növekszik. Összesen 18 mag és 32 256 MB RAM szükséges ahhoz, hogy támogassa az ajánlott 13 virtuális gépet az alapértelmezett 9 maggal és a 16 128 MB RAM-mal a 6 virtuális gépen.

15. Válassza ki az App Service-infrastruktúra virtuális gépei telepítéséhez használni kívánt platformot, és kattintson a **tovább**gombra:

    ![Platform képének kiválasztása App Service](media/app-service-deploy-ha/13.png)

16. Adja meg App Service infrastruktúra szerepkörének hitelesítő adatait, és kattintson a **tovább**gombra:

    ![Infrastruktúra-szerepkör hitelesítő adatai App Service](media/app-service-deploy-ha/14.png)

17. Tekintse át a App Service telepítéséhez használni kívánt adatokat, majd kattintson a **tovább** gombra az üzembe helyezés megkezdéséhez.

    ![A telepítés összegzésének áttekintése App Service](media/app-service-deploy-ha/15.png)

18. Tekintse át a App Service telepítési folyamatát. Ez az üzembe helyezés az adott telepítési konfigurációtól és hardvertől függően egy órát is igénybe vehet. A telepítő sikeres befejeződése után válassza a **Kilépés**lehetőséget.

    ![A telepítés befejeződött App Service](media/app-service-deploy-ha/16.png)

::: zone-end

::: zone pivot="state-disconnected"
<!-- Disconnected --->

## <a name="create-an-offline-installation-package"></a>Offline telepítőcsomag létrehozása

A Azure App Service offline környezetben történő telepítéséhez először hozzon létre egy offline telepítőcsomagot egy internetkapcsolattal rendelkező számítógépen.

1. Futtassa a AppService. exe telepítőjét olyan gépen, amely csatlakozik az internethez. 

1. Válassza a **speciális** > **Létrehozás offline telepítési csomag**lehetőséget. Ennek a lépésnek a végrehajtása több percet is igénybe vehet.

    ! [Offline csomag létrehozása Azure App Service telepítőben] 31

1. A Azure App Service-telepítő offline telepítőcsomagot hoz létre, és megjeleníti az elérési utat. A **mappa megnyitása lehetőség kiválasztásával** megnyithatja a mappát a fájlkezelőben.

    ![Az offline telepítési csomag sikeresen létrejött Azure App Service telepítőben](media/azure-stack-app-service-deploy-offline/image02.png)

1. Másolja a telepítőt (AppService. exe) és az offline telepítőcsomagot egy olyan gépre, amely az Azure Stack hubhoz van csatlakoztatva.

## <a name="complete-the-offline-installation-of-azure-app-service-on-azure-stack-hub"></a>Azure App Service offline telepítésének befejezése Azure Stack hub-on

1. Futtassa a appservice. exe fájlt rendszergazdaként egy olyan számítógépről, amely elérheti az Azure Stack hub rendszergazdai Azure Resource Management végpontját.

1. Válassza a **speciális** > **teljes kapcsolat nélküli telepítés**lehetőséget.

    ! [Offline telepítés befejezése Azure App Service telepítőben] [32]

1. Keresse meg a korábban létrehozott offline telepítőcsomag helyét, majd kattintson a **tovább**gombra.

    ![Offline telepítési csomag elérési útjának megadása im Azure App Service telepítőjének](media/azure-stack-app-service-deploy-offline/image04.png)

1. Fogadja el a Microsoft licencfeltételeket, és kattintson a **tovább**gombra.

    ![A Microsoft licencelési feltételei a App Service](media/app-service-deploy-ha/02.png)

1. Fogadjon el nem Microsoft licencelési feltételeket, és kattintson a **tovább**gombra.

    ![Nem a Microsofttól származó licencelési feltételek App Service](media/app-service-deploy-ha/03.png)

1. Adja meg az Azure Stack hub-környezet App Service Felhőbeli végpontjának konfigurációját.

    ![App Service a Felhőbeli végpontok konfigurálása App Service](media/app-service-deploy-ha/04.png)

1. **Kapcsolódjon** a telepítéshez használni kívánt Azure stack hub-előfizetéshez, és válassza ki a helyet. 

    ![Kapcsolódjon az Azure Stack hub-előfizetéshez App Service](media/app-service-deploy-ha/05.png)

1. Válassza a **meglévő VNet és alhálózatok** és az **erőforráscsoport nevének** használata a magasan elérhető sablon üzembe helyezéséhez használt erőforráscsoport számára lehetőséget.  Ezután válassza ki a sablon központi telepítésének részeként létrehozott virtuális hálózatot, majd válassza ki a megfelelő szerepkör-alhálózatokat a legördülő lista beállításai közül. 

    ![Vnet kiválasztása App Service](media/app-service-deploy-ha/06.png)

1. Adja meg a korábban rögzített sablon kimeneteit a fájlmegosztás elérési útjának és a fájlmegosztás tulajdonosának paramétereinek. Amikor végzett, kattintson a **Tovább**gombra.

    ![Fájlmegosztás kimeneti adatai App Service](media/app-service-deploy-ha/07.png)

1. Mivel az App Service telepítéséhez használt gép nem ugyanazon a VNet található, mint a App Service fájlmegosztás üzemeltetéséhez használt fájlkiszolgáló, a név nem oldható fel. **Ez a hiba a várt viselkedés**.  Ellenőrizze, hogy helyesek-e a fájlmegosztás UNC elérési útjának és a fiókok adatainak megadott adatok. Ezután nyomja meg az **Igen** gombot a riasztási párbeszédpanelen a app Service telepítés folytatásához.

    ![Várt hiba párbeszédpanel App Service](media/app-service-deploy-ha/08.png)

1. Adja meg az azonosító alkalmazás AZONOSÍTÓját, valamint az identitási tanúsítványok elérési útját és jelszavát, majd kattintson a **tovább**gombra:
    - Identity Application-tanúsítvány ( **SSO. appservice. local. azurestack. external. pfx**formátumban)
    - Azure Resource Manager főtanúsítvány (**AzureStackCertificationAuthority. cer**)

    ![AZONOSÍTÓ alkalmazás tanúsítványa és főtanúsítványa App Service](media/app-service-deploy-ha/008.png)

1. Ezután adja meg a fennmaradó szükséges információkat a következő tanúsítványokhoz, és kattintson a **tovább**gombra:
    - Alapértelmezett Azure Stack hub SSL-tanúsítvány (a következő formátumban: **_. appservice. local. azurestack. external. pfx**)
    - API SSL-tanúsítvány ( **API. appservice. local. azurestack. external. pfx**formátumban)
    - Közzétevői tanúsítvány ( **FTP. appservice. local. azurestack. external. pfx**formátumban) 

    ![További konfigurációs tanúsítványok App Service](media/app-service-deploy-ha/09.png)

1. Adja meg a SQL Server kapcsolódási adatait a magas rendelkezésre állású sablon központi telepítési kimenetének SQL Server kapcsolódási adataival:

    ![SQL Server a kapcsolatok adatai a App Service](media/app-service-deploy-ha/10.png)

1. Mivel az App Service telepítéséhez használt gép nem ugyanazon a VNet található, mint a App Service-adatbázisok üzemeltetéséhez használt SQL Server, a név nem oldható fel.  **Ez a várt viselkedés**.  Győződjön meg arról, hogy a SQL Server neve és a fiókadatok adatai helyesek, és nyomja meg az **Igen** gombot a app Service telepítés folytatásához. Kattintson a **Tovább** gombra.

    ![SQL Server a kapcsolatok adatai a App Service](media/app-service-deploy-ha/11.png)

1. Fogadja el az alapértelmezett szerepkör-konfigurációs értékeket, vagy váltson az ajánlott értékekre, és kattintson a **tovább**gombra.  Azt javasoljuk, hogy az App Service infrastruktúra szerepkör-példányok alapértelmezett értékeit a következőképpen módosítsák a magasan elérhető konfigurációk esetén:

    |Szerepkör|Alapértelmezett|Magasan elérhető javaslat|
    |-----|-----|-----|
    |Vezérlő szerepkör|2|2|
    |Felügyeleti szerepkör|1|3|
    |Közzétevői szerepkör|1|3|
    |Előtér-szerepkör|1|3|
    |Megosztott feldolgozói szerepkör|1|2|
    |     |     |     |

    ![Infrastruktúra-szerepkör példányának értékei App Service](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Az ebben az oktatóanyagban ajánlott értékekre való váltás a App Service telepítéséhez szükséges hardverkövetelmények megnövekedésével növekszik. Összesen 18 mag és 32 256 MB RAM szükséges ahhoz, hogy támogassa az ajánlott 13 virtuális gépet az alapértelmezett 9 maggal és a 16 128 MB RAM-mal a 6 virtuális gépen.

1. Válassza ki az App Service-infrastruktúra virtuális gépei telepítéséhez használni kívánt platformot, és kattintson a **tovább**gombra:

    ![Platform képének kiválasztása App Service](media/app-service-deploy-ha/13.png)

1. Adja meg App Service infrastruktúra szerepkörének hitelesítő adatait, és kattintson a **tovább**gombra:

    ![Infrastruktúra-szerepkör hitelesítő adatai App Service](media/app-service-deploy-ha/14.png)

1. Tekintse át a App Service telepítéséhez használni kívánt adatokat, majd kattintson a **tovább** gombra az üzembe helyezés megkezdéséhez.

    ![A telepítés összegzésének áttekintése App Service](media/app-service-deploy-ha/15.png)

1. Tekintse át a App Service telepítési folyamatát. Ez az üzembe helyezés az adott telepítési konfigurációtól és hardvertől függően egy órát is igénybe vehet. A telepítő sikeres befejeződése után válassza a **Kilépés**lehetőséget.

    ![A telepítés befejeződött App Service](media/app-service-deploy-ha/16.png)

::: zone-end

## <a name="post-deployment-steps"></a>Üzembe helyezés utáni lépések

1. Fel **kell** [vennie a appservice_hosting és appservice_metering adatbázist egy rendelkezésre állási csoportba](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database). Szinkronizálnia kell az adatbázisokat is, hogy megakadályozza a szolgáltatás elvesztését egy adatbázis-feladatátvétel esetén.

2. App Servicet telepített egy meglévő virtuális hálózatba, és belső IP-címet használ a fájlkiszolgálón való kapcsolódáshoz, hozzá **kell** adnia egy kimenő biztonsági szabályt, amely engedélyezi az SMB-forgalmat a feldolgozó alhálózat és a fájlkiszolgáló között. A felügyeleti portálon nyissa meg a WorkersNsg hálózati biztonsági csoportot, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:

- Forrás: bármely
- Forrásoldali porttartomány: *
- Cél: IP-címek
- Célként megadott IP-címtartomány: a fájlkiszolgáló IP-címeinek tartománya
- Célport tartománya: 445
- Protokoll: TCP
- Művelet: Engedélyezés
- Prioritás: 700
- Név: Outbound_Allow_SMB445

## <a name="next-steps"></a>További lépések

Ha a App Service erőforrás-szolgáltatót SQL always on-példánnyal adta meg, [adja hozzá a appservice_hosting és appservice_metering adatbázisokat egy rendelkezésre állási csoporthoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) . Szinkronizálja az adatbázisokat, hogy megakadályozza a szolgáltatás elvesztését egy adatbázis-feladatátvétel esetén. [Parancsfájlt](https://blog.sqlauthority.com/2017/11/30/sql-server-alwayson-availability-groups-script-sync-logins-replicas/) is futtathat a AppServices-bejelentkezések az eredeti elsődleges kiszolgálóról a feladatátvételi kiszolgálóra való importálásához.

[App Service skálázása](azure-stack-app-service-add-worker-roles.md). Előfordulhat, hogy további App Service infrastruktúra-szerepkörrel rendelkező munkatársakat kell hozzáadnia, hogy megfeleljenek a környezetében várható alkalmazási igényeknek. Alapértelmezés szerint a App Service on Azure Stack hub támogatja az ingyenes és a közös feldolgozói szintet. Más munkavégző rétegek hozzáadásához további feldolgozói szerepköröket kell hozzáadnia.

[Konfigurálja a központi telepítési forrásokat](azure-stack-app-service-configure-deployment-sources.md). További konfigurálásra van szükség a több forrásból származó, például a GitHub, a BitBucket, a OneDrive és a DropBox eszközök igény szerinti telepítésének támogatásához.

[App Service biztonsági mentése](app-service-back-up.md). App Service sikeres telepítése és konfigurálása után győződjön meg arról, hogy a vész-helyreállításhoz szükséges összes összetevőről biztonsági mentés készül. Az alapvető összetevőinek biztonsági mentése segít megakadályozni az adatvesztést és a szükségtelen szolgáltatási állásidőt a helyreállítási műveletek során.
