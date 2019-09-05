---
title: Azure Stack App Service üzembe helyezése egy magasan elérhető konfigurációban | Microsoft Docs
description: Megtudhatja, hogyan helyezheti üzembe a App Service a Azure Stackban egy kiválóan elérhető konfiguráció használatával.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: e09cc9e5c77379441d7757fa1395941712ecc5ff
ms.sourcegitcommit: e2f6205e6469b39c2395ee09424bb7632cb94c40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70271831"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>App Service üzembe helyezése egy magasan elérhető konfigurációban

Ebből a cikkből megtudhatja, hogyan használhatók a Azure Stack Marketplace-elemek, ha a Azure Stack egy magasan elérhető konfigurációban helyezi üzembe a App Service. A piactéren elérhető elemek mellett ez a megoldás a [appservice-fájlmegosztás-SQLServer-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure stack gyors üzembe helyezési sablont is használja. Ez a sablon automatizálja a App Service erőforrás-szolgáltató üzemeltetéséhez szükséges, magasan elérhető infrastruktúra létrehozását. Ezt követően a rendszer telepíti a App Service a rendelkezésre álló, magasan elérhető virtuálisgép-infrastruktúrára. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>A magasan elérhető App Service infrastruktúra-alapú virtuális gépek üzembe helyezése
A [appservice-fájlmegosztás-SQLServer-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure stack gyors üzembe helyezési sablonja leegyszerűsíti app Service központi telepítését magas rendelkezésre állású konfigurációban. Az alapértelmezett szolgáltatói előfizetésben kell központilag telepíteni. 

Ha Azure Stackban egyéni erőforrást hoz létre, a sablon a következőt hozza létre:
- Egy virtuális hálózat és szükséges alhálózatok.
- Fájlkiszolgáló, SQL Server és Active Directory tartományi szolgáltatások (AD DS) alhálózatok hálózati biztonsági csoportjai.
- Storage-fiókok virtuálisgép-lemezekhez és a fürt Felhőbeli tanúja.
- Egy belső terheléselosztó a magánhálózati IP-címmel rendelkező SQL virtuális gépekhez, amelyek az SQL AlwaysOn-figyelőhöz vannak kötve.
- Három rendelkezésre állási csoport a AD DShoz, a fájlkiszolgáló fürthöz és az SQL-fürthöz.
- Két csomópontos SQL-fürt.
- Két csomópontos fájlkiszolgáló-fürt.
- Két tartományvezérlő.

### <a name="required-azure-stack-marketplace-items"></a>Szükséges Azure Stack Marketplace-elemek
A sablon használata előtt győződjön meg arról, hogy az alábbi [Azure stack Marketplace-elemek](azure-stack-marketplace-azure-items.md) elérhetők a Azure stack-példányban:

- Windows Server 2016 Datacenter Core rendszerkép (AD DS és fájlkiszolgáló virtuális gépekhez)
- SQL Server 2016 SP2 Windows Server 2016 rendszeren (Enterprise)
- Legújabb SQL IaaS-bővítmény 
- A PowerShell kívánt állapotának legújabb konfigurációs bővítménye 

> [!TIP]
> A sablonra vonatkozó követelményekkel és az alapértelmezett értékekkel kapcsolatos további részletekért tekintse meg [a sablonhoz tartozó Readme fájlt](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) a githubon. 

### <a name="deploy-the-app-service-infrastructure"></a>A App Service infrastruktúra üzembe helyezése
Az ebben a szakaszban ismertetett lépések segítségével hozzon létre egy egyéni központi telepítést a **appservice-fájlmegosztás-SQLServer-ha** Azure stack rövid útmutatóval.

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Válassza **\+** **az erőforrás** > létrehozása**Egyéni**lehetőséget, majd **template Deployment**.

   ![Egyéni sablon központi telepítése](media/app-service-deploy-ha/1.png)


3. A **Custom Deployment (egyéni üzembe helyezés** ) panelen válassza a **sablon** > -**Gyorsindítás sablon** szerkesztése lehetőséget, majd az elérhető egyéni sablonok legördülő listájában válassza ki a **appservice-fájlmegosztás-SQLServer-ha** sablont. Kattintson **az OK**, majd a **Mentés**gombra.

   ![Válassza ki a appservice-fájlmegosztás-SQLServer-ha Gyorsindítás sablont](media/app-service-deploy-ha/2.png)

4. Az **Egyéni telepítés** panelen válassza a **Paraméterek szerkesztése** lehetőséget, és görgessen lefelé az alapértelmezett sablon értékeinek áttekintéséhez. Szükség szerint módosítsa ezeket az értékeket az összes kötelező paraméter információjának megadásához, majd kattintson **az OK**gombra.<br><br> A (z),, `ADMINPASSWORD`, és `FILESHAREOWNERPASSWORD` `FILESHAREUSERPASSWORD` `SQLLOGINPASSWORD` paraméterekhez legalább összetett `SQLSERVERSERVICEACCOUNTPASSWORD`jelszavakat adjon meg.
    
   ![Egyéni telepítési paraméterek szerkesztése](media/app-service-deploy-ha/3.png)

5. Az **Egyéni telepítés** panelen ellenőrizze, hogy az **alapértelmezett szolgáltatói előfizetés** van-e kiválasztva a használni kívánt előfizetésként, majd hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot az egyéni telepítéshez.<br><br> Ezután válassza ki az erőforráscsoport helyét (ASDK-telepítések esetén**helyi** ), majd kattintson a **Létrehozás**gombra. Az egyéni központi telepítési beállítások érvényesítése a sablon üzembe helyezésének megkezdése előtt történik.

    ![Egyéni központi telepítés létrehozása](media/app-service-deploy-ha/4.png)

6. A felügyeleti portálon válassza az **erőforráscsoportok** elemet, majd az egyéni telepítéshez létrehozott erőforráscsoport nevét (ebben a példában az**app-Service-ha** lehetőséget). Tekintse meg a központi telepítés állapotát, és győződjön meg arról, hogy az összes központi telepítés sikeresen befejeződött.

   > [!NOTE]
   > A sablon üzembe helyezése körülbelül egy órát vesz igénybe.

   [![](media/app-service-deploy-ha/5-sm.png "Sablon központi telepítési állapotának áttekintése")](media/app-service-deploy-ha/5-lg.png#lightbox)


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

    ![Microsoft. Template deployment](media/app-service-deploy-ha/6.png)

4. A **Microsoft. template** telepítésének kiválasztása után válassza a **kimenetek** lehetőséget, és jegyezze fel a sablon paraméter kimenetét. Ez az információ a App Service telepítésekor szükséges.

    ![Paraméter kimenete](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>App Service üzembe helyezése egy magasan elérhető konfigurációban
Az ebben a szakaszban ismertetett lépéseket követve telepítse a App Service for Azure Stackt magas rendelkezésre állású konfigurációban a [appservice-fájlmegosztás-SQLServer-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure stack gyors üzembe helyezési sablon alapján. 

A App Service erőforrás-szolgáltató telepítése után az ajánlatokat és a csomagokat is felveheti. A felhasználók ezután előfizethetnek a szolgáltatás beszerzésére és az alkalmazások létrehozásának megkezdésére.

> [!IMPORTANT]
> Az erőforrás-szolgáltatói telepítő futtatása előtt ellenőrizze, hogy elolvasta-e az egyes App Service kiadásokhoz tartozó kibocsátási megjegyzéseket, hogy megismerje az új funkciókat, javításokat, valamint az üzembe helyezést befolyásoló ismert problémákat.

### <a name="prerequisites"></a>Előfeltételek
A App Service telepítőjének futtatása előtt több lépésre van szükség, ahogy az [első lépések a App Service Azure stack cikkben](azure-stack-app-service-before-you-get-started.md)leírt módon:

> [!TIP]
> A [app Service cikk](azure-stack-app-service-before-you-get-started.md) első lépéseinek megkezdése előtt leírt lépések nem szükségesek, mert a sablon központi telepítése konfigurálja az infrastruktúra-alapú virtuális gépeket.

- [Töltse le a app Service telepítőjét és a segítő parancsfájlokat](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts).
- [Töltse le az egyéni szkriptek legújabb bővítményét a Azure stack Marketplace-](azure-stack-app-service-before-you-get-started.md#syndicate-the-custom-script-extension-from-the-marketplace)re.
- A [szükséges tanúsítványok előállítása](azure-stack-app-service-before-you-get-started.md#get-certificates).
- Hozza létre az azonosító alkalmazást a Azure Stack kiválasztott azonosító alapján. Egy azonosító alkalmazás az [Azure ad](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app) -hez vagy a [Active Directory összevonási szolgáltatások (AD FS)hoz](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-app) , és rögzíti az alkalmazás azonosítóját.
- Győződjön meg arról, hogy felvette a Windows Server 2016 Datacenter rendszerképet a Azure Stack piactéren. Ez a rendszerkép szükséges a App Service telepítéséhez.

### <a name="steps-for-app-service-deployment"></a>App Service központi telepítésének lépései
A App Service erőforrás-szolgáltató telepítése legalább egy órát vesz igénybe. A szükséges időtartam attól függ, hogy hány szerepkör-példányt telepít. A telepítés során a telepítő a következő feladatokat futtatja:

- Hozzon létre egy BLOB-tárolót a megadott Azure Stack Storage-fiókban.
- Hozzon létre egy DNS-zónát és-bejegyzéseket a App Servicehoz.
- Regisztrálja a App Service erőforrás-szolgáltatót.
- Regisztrálja a App Service gyűjtemény elemeit.

A App Service erőforrás-szolgáltató üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Futtassa a korábban letöltött App Service telepítőt (**appservice. exe**) rendszergazdaként egy olyan számítógépről, amely hozzáfér a Azure stack rendszergazdai Azure Resource Management-végponthoz.

2. Válassza **a telepítés app Service vagy a frissítés a legújabb verzióra**lehetőséget.

    ![App Service üzembe helyezése vagy frissítése](media/app-service-deploy-ha/01.png)

3. Fogadja el a Microsoft licencfeltételeket, és kattintson a **tovább**gombra.

    ![A Microsoft licencelési feltételei a App Service](media/app-service-deploy-ha/02.png)

4. Fogadjon el nem Microsoft licencelési feltételeket, és kattintson a **tovább**gombra.

    ![Nem a Microsofttól származó licencelési feltételek App Service](media/app-service-deploy-ha/03.png)

5. Adja meg a Azure Stack-környezet App Service Felhőbeli végpontjának konfigurációját.

    ![App Service a Felhőbeli végpontok konfigurálása App Service](media/app-service-deploy-ha/04.png)

6. **Kapcsolódjon** a telepítéshez használni kívánt Azure stack-előfizetéshez, és válassza ki a helyet. 

    ![Kapcsolódás a Azure Stack-előfizetéshez App Service](media/app-service-deploy-ha/05.png)

7. Válassza a **meglévő VNet és alhálózatok** és az **erőforráscsoport nevének** használata a magasan elérhető sablon üzembe helyezéséhez használt erőforráscsoport számára lehetőséget.<br><br>Ezután válassza ki a sablon központi telepítésének részeként létrehozott virtuális hálózatot, majd válassza ki a megfelelő szerepkör-alhálózatokat a legördülő lista beállításai közül. 

    ![Vnet kiválasztása App Service](media/app-service-deploy-ha/06.png)

8. Adja meg a korábban rögzített sablon kimeneteit a fájlmegosztás elérési útjának és a fájlmegosztás tulajdonosának paramétereinek. Ha elkészült, kattintson a **tovább**gombra.

    ![Fájlmegosztás kimeneti adatai App Service](media/app-service-deploy-ha/07.png)

9. Mivel az App Service telepítéséhez használt gép nem ugyanazon a VNet található, mint a App Service fájlmegosztás üzemeltetéséhez használt fájlkiszolgáló, a név nem oldható fel. **Ez a hiba a várt viselkedés**.<br><br>Ellenőrizze, hogy helyesek-e a fájlmegosztás UNC elérési útjának és a fiókok adatainak megadott adatok. Ezután nyomja meg az **Igen** gombot a riasztási párbeszédpanelen a app Service telepítés folytatásához.

    ![Várt hiba párbeszédpanel App Service](media/app-service-deploy-ha/08.png)

    Ha úgy dönt, hogy egy meglévő virtuális hálózatra és egy belső IP-címet helyez üzembe a fájlkiszolgálón való kapcsolódáshoz, hozzá kell adnia egy kimenő biztonsági szabályt. Ez a szabály engedélyezi az SMB-forgalmat a munkavégző alhálózat és a fájlkiszolgáló között. Nyissa meg a WorkersNsg a felügyeleti portálon, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:
    - Forrás: Any
    - Forrásoldali porttartomány: *
    - Cél: IP-címek
    - Cél IP-címtartomány: A fájlkiszolgáló IP-címeinek tartománya
    - Célport tartománya: 445
    - Protokoll: TCP
    - Művelet: Allow
    - Fontosság: 700
    - Név: Outbound_Allow_SMB445

10. Adja meg az azonosító alkalmazás AZONOSÍTÓját, valamint az identitási tanúsítványok elérési útját és jelszavát, majd kattintson a **tovább**gombra:
    - Identity Application-tanúsítvány ( **SSO. appservice. local. azurestack. external. pfx**formátumban)
    - Azure Resource Manager főtanúsítvány (**AzureStackCertificationAuthority. cer**)

    ![AZONOSÍTÓ alkalmazás tanúsítványa és főtanúsítványa App Service](media/app-service-deploy-ha/008.png)

11. Ezután adja meg a fennmaradó szükséges információkat a következő tanúsítványokhoz, és kattintson a **tovább**gombra:
    - Alapértelmezett Azure Stack SSL-tanúsítvány (a következő formátumban: **_. appservice. local. azurestack. external. pfx**)
    - API SSL-tanúsítvány ( **API. appservice. local. azurestack. external. pfx**formátumban)
    - Közzétevői tanúsítvány ( **FTP. appservice. local. azurestack. external. pfx**formátumban) 

    ![További konfigurációs tanúsítványok App Service](media/app-service-deploy-ha/09.png)

12. Adja meg a SQL Server kapcsolódási adatait a magas rendelkezésre állású sablon központi telepítési kimenetének SQL Server kapcsolódási adataival:

    ![SQL Server a kapcsolatok adatai a App Service](media/app-service-deploy-ha/10.png)

13. Mivel az App Service telepítéséhez használt gép nem ugyanazon a VNet található, mint a App Service-adatbázisok üzemeltetéséhez használt SQL Server, a név nem oldható fel.  **Ez a várt viselkedés**.<br><br>Győződjön meg arról, hogy a SQL Server neve és a fiókadatok adatai helyesek, és nyomja meg az **Igen** gombot a app Service telepítés folytatásához. Kattintson a **Tovább** gombra.

    ![SQL Server a kapcsolatok adatai a App Service](media/app-service-deploy-ha/11.png)

14. Fogadja el az alapértelmezett szerepkör-konfigurációs értékeket, vagy váltson az ajánlott értékekre, és kattintson a **tovább**gombra.<br><br>Azt javasoljuk, hogy az App Service infrastruktúra szerepkör-példányok alapértelmezett értékeit a következőképpen módosítsák a magasan elérhető konfigurációk esetén:

    |Role|Alapértelmezett|Magasan elérhető javaslat|
    |-----|-----|-----|
    |Vezérlő szerepkör|2|2|
    |Felügyeleti szerepkör|1|3|
    |Közzétevői szerepkör|1|3|
    |Előtér-kiszolgálói szerepkör|1|3|
    |Megosztott feldolgozói szerepkör|1|10|
    |     |     |     |

    ![Infrastruktúra-szerepkör példányának értékei App Service](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Az ebben az oktatóanyagban ajánlott értékekre való váltás a App Service telepítéséhez szükséges hardverkövetelmények megnövekedésével növekszik. Összesen 26 mag és 46 592 MB RAM szükséges az ajánlott 21 virtuális gépek támogatásához az alapértelmezett 18 maggal és a 32 256 MB RAM-mal a 15 virtuális gépen.

15. Válassza ki az App Service-infrastruktúra virtuális gépei telepítéséhez használni kívánt platformot, és kattintson a **tovább**gombra:

    ![Platform képének kiválasztása App Service](media/app-service-deploy-ha/13.png)

16. Adja meg App Service infrastruktúra szerepkörének hitelesítő adatait, és kattintson a **tovább**gombra:

    ![Infrastruktúra-szerepkör hitelesítő adatai App Service](media/app-service-deploy-ha/14.png)

17. Tekintse át a App Service telepítéséhez használni kívánt adatokat, majd kattintson a **tovább** gombra az üzembe helyezés megkezdéséhez.

    ![A telepítés összegzésének áttekintése App Service](media/app-service-deploy-ha/15.png)

18. Tekintse át a App Service telepítési folyamatát. Ez az üzembe helyezés az adott telepítési konfigurációtól és hardvertől függően egy órát is igénybe vehet. A telepítő sikeres befejeződése után válassza a **Kilépés**lehetőséget.

    ![A telepítés befejeződött App Service](media/app-service-deploy-ha/16.png)

## <a name="next-steps"></a>További lépések

[Adja hozzá a appservice_hosting és a appservice_metering-adatbázist egy rendelkezésre állási csoporthoz](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) , ha a app Service erőforrás-SZOLGÁLTATÓt SQL always on-példányon adta meg. Szinkronizálja az adatbázisokat, hogy megakadályozza a szolgáltatás elvesztését egy adatbázis-feladatátvétel esetén.

[App Service skálázása](azure-stack-app-service-add-worker-roles.md). Előfordulhat, hogy további App Service infrastruktúra-szerepkörrel rendelkező munkatársakat kell hozzáadnia, hogy megfeleljenek a környezetében várható alkalmazási igényeknek. Alapértelmezés szerint a App Service on Azure Stack támogatja az ingyenes és a közös feldolgozói szintet. Más munkavégző rétegek hozzáadásához további feldolgozói szerepköröket kell hozzáadnia.

[Konfigurálja a központi telepítési forrásokat](azure-stack-app-service-configure-deployment-sources.md). További konfigurálásra van szükség a több forrásból származó, például a GitHub, a BitBucket, a OneDrive és a DropBox eszközök igény szerinti telepítésének támogatásához.

[App Service biztonsági mentése](app-service-back-up.md). App Service sikeres telepítése és konfigurálása után győződjön meg arról, hogy a vész-helyreállításhoz szükséges összes összetevőről biztonsági mentés készül. Az alapvető összetevőinek biztonsági mentése segít megakadályozni az adatvesztést és a szükségtelen szolgáltatási állásidőt a helyreállítási műveletek során.
