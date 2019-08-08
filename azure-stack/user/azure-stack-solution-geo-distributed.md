---
title: Közvetlen forgalom egy földrajzilag elosztott alkalmazás-megoldással az Azure-ban és a Azure Stack | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre földrajzilag elosztott alkalmazás-megoldást az Azure-ban, és Azure Stack a forgalmat egy adott végpontra irányítja.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: bac13382c1fe579a04b96274d8f310d5a1737be2
ms.sourcegitcommit: a0dcb61890ad0f7b8e1f738f7186198681adcc2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68820773"
---
# <a name="create-a-geo-distributed-app-solution-to-direct-traffic-with-azure-and-azure-stack"></a>Földrajzilag elosztott alkalmazás-megoldás létrehozása az Azure-ban és a Azure Stack való közvetlen forgalomhoz

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Megtudhatja, hogyan irányíthatja át a forgalmat adott végpontokra különböző mérőszámok alapján a földrajzilag elosztott alkalmazások mintájának használatával. A Traffic Manager-profilok földrajzi alapú útválasztási és végponti konfigurációval való létrehozása biztosítja az információk átirányítását a végpontok számára a regionális követelmények, a vállalati és a nemzetközi szabályozás, valamint az adatok igényei alapján.

Ebben a megoldásban egy példaként szolgáló környezetet fog kiépíteni a következőkre:

> [!div class="checklist"]
> - Hozzon létre egy földrajzilag elosztott alkalmazást.
> - Az alkalmazás célzásához használja a Traffic Manager.

## <a name="use-the-geo-distributed-apps-pattern"></a>A földrajzilag elosztott alkalmazások mintájának használata

A földrajzilag elosztott mintázattal az alkalmazás a régiókat öleli fel. Alapértelmezés szerint a nyilvános felhőbe kerül, de előfordulhat, hogy egyes felhasználók az adataikat a saját régiójába kell megtartaniuk. A felhasználókat a legmegfelelőbb felhőre irányíthatja a követelmények alapján.

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

#### <a name="scalability-considerations"></a>Méretezési szempontok

Az ezzel a cikkel felépített megoldás nem alkalmas a méretezhetőségre. Ha azonban más Azure-és helyszíni megoldásokkal együtt használja, a méretezhetőségre vonatkozó követelményeket is igénybe vehet. További információ a Traffic Manager használatával történő automatikus skálázással rendelkező hibrid megoldások létrehozásáról: [felhőalapú méretezési megoldások létrehozása az Azure-](azure-stack-solution-cloud-burst.md)ban.

#### <a name="availability-considerations"></a>Rendelkezésre állási szempontok

A méretezhetőséggel kapcsolatos szempontoknak megfelelően ez a megoldás nem foglalkozik közvetlenül a rendelkezésre állással. Az Azure és a helyszíni megoldások azonban a megoldáson belül valósíthatók meg, így biztosítva az összes érintett összetevő magas rendelkezésre állását.

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?

- A szervezet olyan nemzetközi ágakat tartalmaz, amelyek egyéni regionális biztonsági és terjesztési házirendeket igényelnek.

- A szervezet minden irodája lekéri az alkalmazottak, az üzleti és a létesítmény adatait, amelyeken helyi szabályozások és időzónák szerint jelentéskészítési tevékenységre van szükség.

- A nagy léptékű követelmények teljesítése az alkalmazások horizontális felskálázásával történik, amely egyetlen régión belül több alkalmazás-telepítéssel rendelkezik, és a szélsőséges terhelési követelmények kezelése érdekében a régiók között.

### <a name="planning-the-topology"></a>A topológia megtervezése

Az elosztott alkalmazás-lábnyom kiépítése előtt a következő dolgokat ismerheti meg:

-   **Egyéni tartomány az alkalmazáshoz:** Mi az az Egyéni tartománynév, amelyet az ügyfelek az alkalmazás eléréséhez használni fognak? A minta alkalmazás esetében az Egyéni tartománynév a *\.www scalableasedemo.com.*

-   **Traffic Manager tartomány:** Az [Azure Traffic Manager-profil](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles)létrehozásakor egy tartománynév van kiválasztva. Ezt a nevet a *trafficmanager.net* utótaggal kombinálva regisztrálja Traffic Manager által felügyelt tartományi bejegyzést. A minta alkalmazás esetében a választott név a *skálázható – a bemutató*. Ennek eredményeképpen a Traffic Manager által felügyelt teljes tartománynév *Scalable-ASE-demo.trafficmanager.net*.

-   **Az alkalmazás helyigényének méretezésére szolgáló stratégia:** Döntse el, hogy az alkalmazás adatlábnyoma több App Service-környezetben legyen elosztva egyetlen régióban, több régióban vagy mindkét megközelítés kombinációjában. Ennek a döntésnek az alapján kell megjelennie, hogy az ügyfelek forgalmának hol kell származnia, és milyen mértékben méretezhető az alkalmazás további támogatása. Például egy 100%-os állapot nélküli alkalmazás esetében az alkalmazások nagy mértékben méretezhetők az Azure-régiók több App Service környezetének kombinációjával, és a több Azure-régióban üzembe helyezett App Service környezetek szorzatával. A 15 és a globális Azure-régiók közül választhatnak, így az ügyfelek az egész világra kiterjedő, Hyper-Scale szintű alkalmazás-lábnyomot hozhatnak létre. Az itt használt minta alkalmazáshoz három App Service környezet lett létrehozva egyetlen Azure-régióban (USA déli középső régiója).

-   **A App Service környezetek elnevezési konvenciója:** Minden App Service Environment egyedi nevet igényel. Egy vagy két App Service környezeten kívül hasznos lehet elnevezési konvenciója az egyes App Service Environment azonosításához. Az itt használt minta alkalmazáshoz egyszerű elnevezési konvenciót használunk. A három App Service környezet neve *fe1ase*, *fe2ase*és *fe3ase*.

-   **Az alkalmazások elnevezési konvenciója:** Mivel az alkalmazás több példánya is telepítve lesz, a központilag telepített alkalmazás minden példányához nevet kell megadni. App Service környezetek esetében ugyanaz az alkalmazásnév több környezet között is használható. Mivel minden App Service Environment egyedi tartományi utótaggal rendelkezik, a fejlesztők úgy dönthetnek, hogy ugyanazt az alkalmazást használják az egyes környezetekben. Előfordulhat például, hogy egy fejlesztőnek a következőképpen kell megneveznie az alkalmazásokat: *MyApp.Foo1.p.azurewebsites.net*, *MyApp.Foo2.p.azurewebsites.net*, *MyApp.Foo3.p.azurewebsites.net*stb. Az itt használt alkalmazás esetében minden alkalmazás-példány egyedi névvel rendelkezik. Az *webfrontend1*, a *webfrontend2*és a *webfrontend3*használt alkalmazás-példányok nevei.

> [!Tip]  
> ![hibrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> A Microsoft Azure Stack az Azure bővítménye. Azure Stack a felhő-számítástechnika rugalmasságát és innovációját a helyszíni környezetbe helyezi, így az egyetlen hibrid felhő, amely lehetővé teszi a hibrid alkalmazások bárhol történő létrehozását és üzembe helyezését.  
> 
> A [hibrid alkalmazásokkal kapcsolatos tervezési szempontok](azure-stack-edge-pattern-overview.md) a szoftverek minőségének (elhelyezés, skálázhatóság, rendelkezésre állás, rugalmasság, kezelhetőség és biztonság) pilléreit tekintik át a hibrid alkalmazások tervezéséhez, üzembe helyezéséhez és üzemeltetéséhez. A kialakítási szempontok segítik a hibrid alkalmazások kialakításának optimalizálását, ami minimalizálja az éles környezetekben felmerülő kihívásokat.

## <a name="part-1-create-a-geo-distributed-app"></a>1\. rész: Földrajzilag elosztott alkalmazás létrehozása

Ebben a részben egy webalkalmazást fog létrehozni.

> [!div class="checklist"]
> - Webes alkalmazások létrehozása és közzététele.
> - Kód hozzáadása az Azure Reposhez.
> - Az alkalmazás kiépítése több Felhőbeli célpontra.
> - A CD-folyamat kezelése és konfigurálása.

### <a name="prerequisites"></a>Előfeltételek

Azure-előfizetésre és Azure Stack telepítésre van szükség.

### <a name="geo-distributed-app-steps"></a>Földrajzilag elosztott alkalmazás lépései

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Egyéni tartomány beszerzése és a DNS konfigurálása

Frissítse a tartományhoz tartozó DNS-zónafájl fájlját. Az Azure AD ekkor ellenőrizheti az Egyéni tartománynév tulajdonjogát. Az Azure-ban az Azure/Office 365/External DNS-rekordok [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) használhatók, vagy a DNS-bejegyzést [egy másik DNS-regisztrálónál](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)adja hozzá.

1. Egyéni tartomány regisztrálása nyilvános regisztrálóval.

2. Jelentkezzen be a tartomány tartománynév-regisztrálójába. A DNS-frissítések elvégzéséhez szükség lehet egy jóváhagyott rendszergazdára.

3. Frissítse a tartományhoz tartozó DNS-zónát az Azure AD által biztosított DNS-bejegyzés hozzáadásával. A DNS-bejegyzés nem változtatja meg a viselkedést, például a posta vagy a webes üzemeltetést.

### <a name="create-web-apps-and-publish"></a>Webalkalmazások létrehozása és közzététel

Hibrid folyamatos integráció/folyamatos teljesítés (CI/CD) beállítása a webalkalmazások Azure-ba való üzembe helyezéséhez és a Azure Stackához, valamint az automatikus leküldéses módosítások mindkét felhőben.

> [!Note]  
> Azure Stack a megfelelő rendszerképek futtatásával (Windows Server és SQL), és App Service üzembe helyezés szükséges. További információ: mielőtt megkezdi a [Azure Stack app Serviceának első lépéseit](../operator/azure-stack-app-service-before-you-get-started.md) a Azure stack operátor dokumentációjában.

#### <a name="add-code-to-azure-repos"></a>Kód hozzáadása az Azure Reposhez

1. Jelentkezzen be a Visual studióba egy olyan **fiókkal, amely projekt-létrehozási jogokkal rendelkezik** az Azure reposban.

    A CI/CD az alkalmazás kódjára és az infrastruktúra kódjára is alkalmazható. A saját és a szolgáltatott felhő fejlesztéséhez [Azure Resource Manager sablonokat](https://azure.microsoft.com/resources/templates/) is használhat.

    ![Kapcsolódás egy projekthez a Visual Studióban](media/azure-stack-solution-geo-distributed/image1.JPG)

2. **A tárház klónozása** az alapértelmezett webalkalmazás létrehozásával és megnyitásával.

    ![A klónozott adattár a Visual Studióban](media/azure-stack-solution-geo-distributed/image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>Webalkalmazás-telepítés létrehozása mindkét felhőben

1.  A **webalkalmazás. csproj** fájljának szerkesztése: Válassza `Runtimeidentifier` ki és `win10-x64`adja hozzá a elemet. (Lásd az [önálló központi telepítési](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentációt.)

    ![Webalkalmazás-projekt fájljának szerkesztése a Visual Studióban](media/azure-stack-solution-geo-distributed/image3.png)

1.  **Az Team Explorer használatával keresse meg a kódot az Azure reposban** .

2.  Győződjön meg róla, hogy az **alkalmazás kódja** be lett jelölve az Azure reposban.

### <a name="create-the-build-definition"></a>A Build definíciójának létrehozása

1. Jelentkezzen be az **Azure-folyamatokba** , és erősítse meg a létrehozási definíciók létrehozásának képességét.

2. Kód `-r win10-x64` hozzáadása. Ez a Hozzáadás szükséges a .NET Core-hoz készült önálló telepítés elindításához.

    ![Kód hozzáadása a Build definícióhoz](media/azure-stack-solution-geo-distributed/image4.png)

3. **Futtassa a buildet**. A [saját üzemeltetésű üzembe helyezési](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) folyamat olyan összetevőket tesz közzé, amelyek az Azure-on és a Azure stackon is futtathatók.

**Azure-beli üzemeltetett ügynök használata**

Az üzemeltetett ügynök használata az Azure-folyamatokban kényelmes megoldás webalkalmazások létrehozására és üzembe helyezésére. A karbantartást és a frissítéseket a Microsoft Azure automatikusan hajtja végre, ami lehetővé teszi a folyamatos fejlesztést, tesztelést és üzembe helyezést.

### <a name="manage-and-configure-the-cd-process"></a>A CD-folyamat kezelése és konfigurálása

Az Azure DevOps és Azure DevOps Server kiválóan konfigurálható és kezelhető folyamatokat biztosít több környezet, például fejlesztési, átmeneti, MINŐSÉGBIZTOSÍTÁSi és éles környezetek kiadásához; többek között a jóváhagyások megkövetelése adott fázisokban.

## <a name="create-release-definition"></a>Kiadás definíciójának létrehozása

1.  Kattintson a **plusz** gombra, és adjon hozzá egy új kiadást az VSO **Build és Release** szakaszának **kiadások** lapján.

    ![Kiadási definíció létrehozása](media/azure-stack-solution-geo-distributed/image5.png)

2. Alkalmazza a Azure App Service központi telepítési sablont.

   ![Azure App Service központi telepítési sablon alkalmazása](meDia/azure-stack-solution-geo-distributed/image6.png)

3. Az összetevő **hozzáadása**területen adja hozzá az Azure Cloud Build alkalmazáshoz tartozó összetevőt.

   ![Összetevő hozzáadása az Azure Cloud buildhez](media/azure-stack-solution-geo-distributed/image7.png)

4. A folyamat lapon válassza ki a **fázist, a feladat** hivatkozását, és állítsa be az Azure Cloud Environment értékeit.

   ![Az Azure Cloud Environment értékeinek beállítása](media/azure-stack-solution-geo-distributed/image8.png)

5. Adja meg a **környezet nevét** , és válassza ki az Azure-beli Felhőbeli végponthoz tartozó **Azure** -előfizetést.

      ![Azure-előfizetés kiválasztása Azure Felhőbeli végponthoz](media/azure-stack-solution-geo-distributed/image9.png)

6. Az **app Service neve**alatt állítsa be a szükséges Azure app Service-nevet.

      ![Az Azure app Service nevének beállítása](media/azure-stack-solution-geo-distributed/image10.png)

7. Adja meg a "üzemeltetett VS2017" kifejezést az Azure Cloud üzemeltetett környezet **ügynök** -várólistájában.

      ![Az Azure Cloud üzemeltetett környezet ügynök-várólistájának beállítása](media/azure-stack-solution-geo-distributed/image11.png)

8. A telepítés Azure App Service menüben válassza ki a környezet érvényes **csomagját vagy mappáját** . Kattintson **az OK** gombra a **mappa helyének**megadásához.
  
      ![Azure App Service-környezethez tartozó csomag vagy mappa kiválasztása](media/azure-stack-solution-geo-distributed/image12.png)

      ![Azure App Service-környezethez tartozó csomag vagy mappa kiválasztása](media/azure-stack-solution-geo-distributed/image13.png)

9. Mentse az összes módosítást, éstérjen vissza a kiadási folyamathoz.

    ![A kiadási folyamat módosításainak mentése](media/azure-stack-solution-geo-distributed/image14.png)

10. Vegyen fel egy új összetevőt, és válassza ki a Azure Stack alkalmazás buildjét.
    
    ![Új összetevő hozzáadása Azure Stack alkalmazáshoz](media/azure-stack-solution-geo-distributed/image15.png)


11. Vegyen fel még egy környezetet a Azure App Service központi telepítés alkalmazásával.
    
    ![Környezet hozzáadása Azure App Service központi telepítéshez](media/azure-stack-solution-geo-distributed/image16.png)

12. Nevezze el az új környezetet Azure Stack.
    
    ![Név környezet Azure App Service üzemelő példányban](media/azure-stack-solution-geo-distributed/image17.png)

13. Keresse meg a Azure Stack környezetet a **feladat** lapon.
    
    ![Azure Stack környezet](media/azure-stack-solution-geo-distributed/image18.png)

14. Válassza ki az Azure Stack-végpont előfizetését.
    
    ![Válassza ki az Azure Stack-végpont előfizetését](media/azure-stack-solution-geo-distributed/image19.png)

15. Adja meg a Azure Stack webalkalmazás nevét az App Service neveként.

    ![Azure Stack webalkalmazás nevének beállítása](media/azure-stack-solution-geo-distributed/image20.png)

16. Válassza ki a Azure Stack ügynököt.
    
    ![Azure Stack ügynök kiválasztása](media/azure-stack-solution-geo-distributed/image21.png)

17. A Azure App Service telepítése szakaszban válassza ki a környezet érvényes **csomagját vagy mappáját** . Kattintson **az OK** gombra a mappa helyének megadásához.

    ![Mappa kiválasztása Azure App Service központi telepítéshez](media/azure-stack-solution-geo-distributed/image22.png)

    ![Mappa kiválasztása Azure App Service központi telepítéshez](media/azure-stack-solution-geo-distributed/image23.png)

18. A változó lapon adjon hozzá egy nevű `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`változót, állítsa az értékét **igaz**értékre, a hatókört pedig Azure Stackra.
    
    ![Változó hozzáadása az Azure-alkalmazások üzembe helyezéséhez](media/azure-stack-solution-geo-distributed/image24.png)

19. Válassza ki a **folyamatos** üzembe helyezési trigger ikont mindkét összetevőben , és engedélyezze a folytatás üzembe helyezési triggert.
    
    ![Folyamatos üzembe helyezési trigger kiválasztása](media/azure-stack-solution-geo-distributed/image25.png)

20. Válassza ki az **üzembe helyezés előtti** feltételek ikont a Azure stack környezetben, és állítsa be a triggert a **kiadás után.**
    
    ![Központi telepítés előtti feltételek kiválasztása](media/azure-stack-solution-geo-distributed/image26.png)

21. Mentse az összes módosítást.

> [!Note]  
> Előfordulhat, hogy a feladatok egyes beállításai automatikusan [környezeti változókként](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) vannak definiálva, amikor kiadási definíciót hoz létre egy sablonból. Ezek a beállítások nem módosíthatók a feladat beállításaiban. Ehelyett a szülő környezeti elemet kell kiválasztani a beállítások szerkesztéséhez.

## <a name="part-2-update-web-app-options"></a>2\. rész: Webalkalmazás-beállítások frissítése

Az [Azure App Service](https://docs.microsoft.com/azure/app-service/overview) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. 

![Azure App Service](media/azure-stack-solution-geo-distributed/image27.png)

> [!div class="checklist"]
> - Meglévő egyéni DNS-név leképezése az Azure Web Appsra.
> - CNAME- **rekord** és egy **rekord** használatával rendelje hozzá az egyéni DNS-nevet a app Servicehoz.

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Meglévő egyéni DNS-név hozzárendelése az Azure Web Appshez

> [!Note]  
>  Használjon CNAME-t az összes egyéni DNS-névhez, kivéve a legfelső szintű tartományt (például northwind.com).

Élő webhely és hozzá tartozó DNS-tartománynév migrálása az App Service-be: [Aktív DNS-név migrálása az Azure App Service-be](https://docs.microsoft.com/azure/app-service/manage-custom-dns-migrate-domain).

### <a name="prerequisites"></a>Előfeltételek

A megoldás elvégzéséhez:

-   [Hozzon létre egy app Service alkalmazást](https://docs.microsoft.com/azure/app-service/), vagy használjon egy másik megoldáshoz létrehozott alkalmazást.

-   Adjon meg egy tartománynevet, és győződjön meg arról, hogy a tartományi szolgáltató DNS-beállításjegyzéke elérhető.

Frissítse a tartományhoz tartozó DNS-zónafájl fájlját. Az Azure AD ellenőrzi az Egyéni tartománynév tulajdonjogát. Az Azure-ban az Azure/Office 365/External DNS-rekordok [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) használhatók, vagy a DNS-bejegyzést [egy másik DNS-regisztrálónál](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)adja hozzá.

-   Egyéni tartomány regisztrálása nyilvános regisztrálóval.

-   Jelentkezzen be a tartomány tartománynév-regisztrálójába. (A DNS-frissítések elvégzéséhez egy jóváhagyott rendszergazdára lehet szükség.)

-   Frissítse a tartományhoz tartozó DNS-zónát az Azure AD által biztosított DNS-bejegyzés hozzáadásával.

Például a northwindcloud.com és a www\.northwindcloud.com DNS-bejegyzéseinek hozzáadásához konfigurálja a northwindcloud.com DNS-beállításait.

> [!Note]  
>  A [Azure Portal](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain)használatával megvásárolható egy tartománynév. Egy egyéni DNS-név webalkalmazásra való leképezéséhez a webalkalmazás [App Service-csomagjának](https://azure.microsoft.com/pricing/details/app-service/) fizetős rétegben kell lennie (**megosztott**, **alapvető**, **szabványos** vagy **prémium szintű**).



### <a name="create-and-map-cname-and-a-records"></a>CNAME és rekordok létrehozása és leképezése

#### <a name="access-dns-records-with-domain-provider"></a>DNS-rekordok elérése tartományszolgáltató esetén

> [!Note]  
>  A Azure DNS használatával konfigurálhatja az Azure Web Apps egyéni DNS-nevét. További információt az [egyéni tartománybeállítások egy Azure-szolgáltatáshoz az Azure DNS használatával történő megadását](https://docs.microsoft.com/azure/dns/dns-custom-domain) ismertető cikkben talál.

1.  Jelentkezzen be a fő szolgáltató webhelyére.

2.  Keresse meg a DNS-rekordok kezelésére szolgáló oldalt. Minden tartományi szolgáltató saját DNS-rekordok felülettel rendelkezik. A webhely **Tartománynév**, **DNS** vagy **Névkiszolgáló kezelése** címkével ellátott területeit keresse.

A DNS-rekordok oldala megtekinthető a **saját tartományokban**. Keresse meg a **zónafájl**, **DNS-rekordok**vagy **Speciális konfiguráció**nevű hivatkozást.

A következő képernyőkép egy DNS-rekordokat tartalmazó oldalra mutat példát:

![DNS-rekordokat tartalmazó oldal példája](media/azure-stack-solution-geo-distributed/image28.png)

1. A tartománynév-Regisztrálóban válassza a **Hozzáadás vagy a létrehozás** elemet a rekord létrehozásához. Egyes szolgáltatók eltérő hivatkozásokat használnak a különböző rekordtípusok hozzáadásához. A szolgáltató dokumentációjában tájékozódhat.

2. Adjon hozzá egy CNAME rekordot, amely altartományt rendel az alkalmazás alapértelmezett állomásnevét.

   A www\.northwindcloud.com-tartományhoz példaként adjon hozzá egy CNAME-rekordot, amely leképezi a nevet < alkalmazás\_neve >. azurewebsites. net.

A CNAME hozzáadása után a DNS-rekordok oldal a következő példához hasonlóan néz ki:

![Navigálás a portálon egy Azure-alkalmazáshoz](media/azure-stack-solution-geo-distributed/image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>A CNAME rekord hozzárendelésének engedélyezése az Azure-ban

1. Egy új lapon jelentkezzen be a Azure Portalba.

2. Navigáljon App Services.

3. Válassza a webalkalmazás lehetőséget.

4. Az Azure Portal bal oldali navigációs sávján válassza ki az **Egyéni tartományok** elemet.

5. Válassza ki a **Gazdagépnév hozzáadása** elem melletti **+** ikont.

6. Írja be a teljes tartománynevet, például `www.northwindcloud.com`:.

7. Válassza az **Érvényesítés** lehetőséget.

8. Ha meg van jelölve, további rekordokat adhat hozzá`A` más `TXT`típusokhoz (vagy) a tartománynév-regisztráló DNS-rekordjaihoz. Az Azure megadja a rekordok értékeit és típusát:

   a.  egy **A** rekordra, amelyet leképezhet az alkalmazás IP-címére.

   b.  Egy **txt** -rekord, amely az alkalmazás alapértelmezett állomásneve < APP_NAME >. azurewebsites. net. App Service ezt a rekordot csak a konfiguráció idejére használja az egyéni tartomány tulajdonjogának ellenőrzéséhez. Az ellenőrzés után törölje a TXT-rekordot.

9. Hajtsa végre ezt a feladatot a tartományregisztráló lapon, majd az **állomásnév hozzáadása** gomb aktiválása után ellenőrizze újra a műveletet.

10. Győződjön meg arról, hogy az **állomásnév** bejegyzéstípusa **CNAME** (www.example.com vagy bármely altartomány) értékre van beállítva.

11. Válassza a **Gazdagépnév hozzáadása** lehetőséget.

12. Írja be a teljes tartománynevet, például `northwindcloud.com`:.

13. Válassza az **Érvényesítés** lehetőséget. A **Hozzáadás** aktiválva van.

14. Győződjön meg arról, hogy az **állomásnév bejegyzéstípus** **egy rekordra** (example.com) van beállítva.

15. **Adja hozzá**a gazdagépet.

    Eltarthat egy ideig, amíg az új állomásnevek megjelennek az alkalmazás **Egyéni tartományok** lapján. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.
  
    ![Egyéni tartományok](media/azure-stack-solution-geo-distributed/image31.png) 
  
    Ha hiba történik, a lap alján egy ellenőrző hibaüzenet jelenik meg. ![Ellenőrzési hiba](media/azure-stack-solution-geo-distributed/image32.png)

> [!Note]  
>  A fenti lépések megismétlődnek a helyettesítő karakteres tartomány (\*. northwindcloud.com) leképezéséhez. Ez lehetővé teszi további altartományok hozzáadását az App Service-hez anélkül, hogy mindegyikhez külön CNAME rekordot kellene létrehoznia. A beállítás konfigurálásához kövesse a regisztrátor utasításait.

#### <a name="test-in-a-browser"></a>Tesztelés böngészőben

Tallózással keresse meg a korábban konfigurált DNS-név (oka) t `northwindcloud.com` (például vagy www.northwindcloud.com).

## <a name="part-3-bind-a-custom-ssl-cert"></a>3\. rész: Egyéni SSL-tanúsítvány kötése

Ebben a részben a következőket tesszük:

> [!div class="checklist"]
> - Az egyéni SSL-tanúsítvány kötése App Servicehoz.
> - HTTPS kényszerített alkalmazása az alkalmazáshoz.
> - SSL-tanúsítvány kötésének automatizálása parancsfájlok használatával.

> [!Note]  
> Ha szükséges, szerezze be a Azure Portal ügyfél SSL-tanúsítványát, és kösse azt a webalkalmazáshoz. További információkért tekintse meg a [app Service Certificates oktatóanyagot](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site).

### <a name="prerequisites"></a>Előfeltételek

A megoldás elvégzéséhez:

-   [Hozzon létre egy App Service alkalmazást.](https://docs.microsoft.com/azure/app-service/)
-   [Rendelje hozzá az egyéni DNS-nevet a webalkalmazáshoz.](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
-   Szerezzen be egy SSL-tanúsítványt egy megbízható hitelesítésszolgáltatótól, és a kulcs használatával írja alá a kérelmet.

### <a name="requirements-for-your-ssl-certificate"></a>Az SSL-tanúsítvány követelményei

A tanúsítvány App Service-ben történő használatához a tanúsítványnak meg kell felelnie az alábbi követelmények mindegyikének:

-   Egy megbízható hitelesítésszolgáltató írta alá.

-   Jelszóval védett PFX-fájlként lett exportálva.

-   Legalább 2048 bit hosszúságú titkos kulcsot tartalmaz.

-   A tanúsítványlánc összes köztes tanúsítványát tartalmazza.

> [!Note]  
>  Az **elliptikus görbe titkosítási (ECC-) tanúsítványok** app Service, de nem szerepelnek ebben az útmutatóban. Az ECC-tanúsítványok létrehozásával kapcsolatos segítségért forduljon a hitelesítésszolgáltatóhoz. 

#### <a name="prepare-the-web-app"></a>A webalkalmazás előkészítése

Ha egyéni SSL-tanúsítványt szeretne kötni a webalkalmazáshoz, a [app Service tervnek](https://azure.microsoft.com/pricing/details/app-service/) alapszintű, **standard**vagy **prémium** szintűnek kell lennie.

#### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1.  Nyissa [](https://portal.azure.com/) meg a Azure Portalt, és navigáljon a webalkalmazáshoz.

2.  A bal oldali menüben válassza a **app Services**lehetőséget, majd válassza ki a webalkalmazás nevét.

![Webalkalmazás kiválasztása](media/azure-stack-solution-geo-distributed/image33.png)

#### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

1.  A webalkalmazás bal oldali navigációs sávján görgessen a **Beállítások** szakaszra, és válassza a vertikális **felskálázás (App Service terv)** lehetőséget.

    ![Vertikális felskálázás menü](media/azure-stack-solution-geo-distributed/image34.png)

1.  Győződjön meg arról, hogy a webalkalmazás nem az **ingyenes** vagy a **közös** szinten van. A webalkalmazás jelenlegi szintje sötét kék mezőben van kiemelve.

    ![A tarifacsomag ellenőrzése](media/azure-stack-solution-geo-distributed/image35.png)

Az egyéni SSL nem támogatott az **ingyenes** vagy a **közös** szinten. A felskálázáshoz kövesse a következő szakaszban leírt lépéseket, vagy a **válassza ki a díjszabási szintet** lapot, és ugorjon az [SSL-tanúsítvány feltöltése és kötése](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)lehetőségre.

#### <a name="scale-up-your-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

1.  Válassza az **Alapszintű**, a **Standard** vagy a **Prémium** szintet.

2.  Válassza a **kiválasztás**lehetőséget.

![Tarifacsomag kiválasztása](media/azure-stack-solution-geo-distributed/image36.png)

A skálázási művelet akkor fejeződik be, ha az értesítés megjelenik.

![Vertikális felskálázási értesítés](media/azure-stack-solution-geo-distributed/image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>Az SSL-tanúsítvány kötése és a köztes tanúsítványok egyesítése

Több tanúsítvány egyesítése a láncban.

1. **Nyisson meg minden olyan tanúsítványt** , amelyet egy szövegszerkesztőben kapott.

2. Hozzon létre egy *mergedcertificate.crt* nevű fájlt az egyesített tanúsítvány számára. Egy szövegszerkesztőben másolja ebbe a fájlba az egyes tanúsítványok tartalmát. A tanúsítványok sorrendjének egyeznie kell a tanúsítványláncban lévő sorrenddel, a saját tanúsítvánnyal kezdve és a főtanúsítvánnyal végződve. Az alábbi példához hasonlóan néz ki:

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a>Tanúsítvány exportálása PFX-fájlba

Exportálja az egyesített SSL-tanúsítványt a tanúsítvány által generált titkos kulccsal.

A titkos kulcsfájl az OpenSSL-n keresztül jön létre. A tanúsítvány PFX-fájlba való exportálásához futtassa a következő parancsot, és cserélje le a  *\<titkos kulcs-fájl >* és  *\<az egyesített-Certificate-file >* a titkos kulcs elérési útjára és az egyesített tanúsítványfájl fájlra:

```powershell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Ha a rendszer kéri, adjon meg egy exportálási jelszót az SSL-tanúsítvány feltöltéséhez App Service később.

Ha az IIS vagy a **CertReq. exe** a tanúsítványkérelem előállítására szolgál, telepítse a tanúsítványt egy helyi gépre, majd [exportálja a tanúsítványt a pfx-be](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

#### <a name="upload-the-ssl-certificate"></a>Az SSL-tanúsítvány feltöltése

1. Válassza az **SSL-beállítások** elemet a webalkalmazás bal oldali navigációs sávján.

2. Válassza a **tanúsítvány feltöltése**lehetőséget.

3. A **pfx-tanúsítványfájl**területen válassza a pfx-fájl lehetőséget.

4. A **tanúsítvány jelszava**mezőben adja meg a pfx-fájl exportálásakor létrehozott jelszót.

5. Válassza a **Feltöltés** lehetőséget.

![Tanúsítvány feltöltése](media/azure-stack-solution-geo-distributed/image38.png)

Amikor App Service befejezi a tanúsítvány feltöltését, az SSL- **Beállítások** lapon jelenik meg.

![SSL-beállítások](media/azure-stack-solution-geo-distributed/image39.png)

#### <a name="bind-your-ssl-certificate"></a>Az SSL-tanúsítvány kötése

1.  Az **SSL** -kötések szakaszban válassza a **kötés hozzáadása**elemet.

    > [!Note]  
    >  Ha a tanúsítvány fel lett töltve, de nem jelenik meg az **állomásnév** legördülő listájában, akkor próbálja meg frissíteni a böngésző oldalát.

1.  Az **SSL-kötés hozzáadása** lapon a legördülő listából válassza ki a védeni kívánt tartománynevet, és a használni kívánt tanúsítványt.

1.  Az **SSL Type** (SSL típusa) területen válassza ki, hogy a [**kiszolgálónév jelzésén (SNI)** ](https://en.wikipedia.org/wiki/Server_Name_Indication) alapuló vagy IP-alapú SSL-t kíván-e használni.

    - **SNI-alapú SSL**: Több SNI-alapú SSL-kötés is hozzáadható. Ez a beállítás lehetővé teszi, hogy több SSL-tanúsítvány biztosítson védelmet több tartomány számára ugyanazon az IP-címen. A legtöbb modern böngésző (beleértve az Internet Explorert, a Chrome-ot, a Firefox-ot és az Operát) támogatja az SNI-t (átfogóbb böngészőtámogatási információkat a [Kiszolgálónév jelzése](https://wikipedia.org/wiki/Server_Name_Indication) című szakaszban talál).

    - **IP-alapú SSL**: Csak egy IP-alapú SSL-kötés adható hozzá. Ez a beállítás csak egy SSL-tanúsítványnak engedélyezi egy dedikált nyilvános IP-cím védelmét. Több tartomány biztonságossá tételéhez gondoskodjon arról, hogy mindegyik ugyanazt az SSL-tanúsítványt használja. Az IP-alapú SSL az SSL-kötés hagyományos beállítása.

1. Válassza a **kötés hozzáadása**elemet.

    ![SSL-kötés hozzáadása](media/azure-stack-solution-geo-distributed/image40.png)

Amikor App Service befejezi a tanúsítvány feltöltését, az az **SSL** -kötések szakaszában jelenik meg.

![SSL-kötések](media/azure-stack-solution-geo-distributed/image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>Az A rekord újratársítása IP SSL

Ha az IP-alapú SSL nincs használatban a webalkalmazásban, ugorjon a [https tesztelése az egyéni tartományhoz](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)elemre.

Alapértelmezés szerint a webalkalmazás megosztott nyilvános IP-címet használ. Ha a tanúsítvány IP-alapú SSL-sel van kötve, App Service létrehoz egy új és dedikált IP-címet a webalkalmazáshoz.

Ha egy rekord a webalkalmazásra van leképezve, a tartomány beállításjegyzékét frissíteni kell a dedikált IP-címmel.

Az **egyéni tartomány** lapot az új, dedikált IP-címmel frissíti a rendszer. Másolja ezt az [IP-címet](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain), majd a [rekordot](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain) az új IP-címhez.

#### <a name="test-https"></a>HTTPS tesztelése

Különböző böngészőkben keresse meg a következőt: https://< a. Custom. domain > a webalkalmazás kiszolgálásának biztosítása érdekében.

![webalkalmazás tallózása](media/azure-stack-solution-geo-distributed/image42.png)

> [!Note]  
> Ha a tanúsítvány-érvényesítési hibák merülnek fel, az önaláírt tanúsítvány lehet az OK, vagy előfordulhat, hogy a köztes tanúsítványok a PFX-fájlba való exportáláskor megmaradtak.

#### <a name="enforce-https"></a>HTTPS kényszerítése

Alapértelmezés szerint bárki elérheti a webalkalmazást HTTP-n keresztül. Lehet, hogy a HTTPS-portra irányuló összes HTTP-kérelem át lesz irányítva.

A Web App (webalkalmazás) lapon válassza az **SL-beállítások**elemet. Ezután a **HTTPS Only** (Csak HTTPS) területen válassza az **On** (Be) elemet.

![HTTPS kényszerítése](media/azure-stack-solution-geo-distributed/image43.png)

Ha a művelet befejeződött, navigáljon az alkalmazásra mutató HTTP URL-címek bármelyikéhez. Példa:

-   https://<app_name>.azurewebsites.net
-   https://northwindcloud.com
-   <https://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>A TLS 1.1/1.2 kényszerítése

Az alkalmazás alapértelmezés szerint engedélyezi a [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1,0-et, amely már nem tekinthető biztonságosnak az iparági szabványok, például a [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)számára. A TLS újabb verziójának kényszerítéséhez kövesse az alábbi lépéseket:

1.  A webalkalmazás lap bal oldali navigációs sávján válassza az **SSL-beállítások**elemet.

2.  A **TLS verziónál**válassza ki a TLS minimális verzióját.

![A TLS 1.1 vagy 1.2 kényszerítése](media/azure-stack-solution-geo-distributed/image44.png)

### <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

1.  Válassza **az erőforrás** > létrehozása**hálózatkezelés** > **Traffic Manager profil** > **létrehozása**lehetőséget.

2.  A **Traffic Manager-profil létrehozása** területen adja meg a következőket:

    1.  A **név mezőben**adja meg a profil nevét. Ennek a névnek egyedinek kell lennie a forgalmi manager.net zónán belül, és a Traffic Manager-profil eléréséhez használt DNS-nevet, forgalmi manager.net kell használnia.

    2.  Az **útválasztási módszer**területen válassza ki a **Geographicrouting metódust**.

    3.  Az **előfizetés**területen válassza ki azt az előfizetést, amelyben létre szeretné hozni a profilt.

    4.  Az **Erőforráscsoport** mezőben hozzon létre egy új erőforráscsoportot, amely alá ezt a profilt helyezi.

    5.  Az **Erőforráscsoport helye** területen válassza ki az erőforráscsoport helyét. Ez a beállítás az erőforráscsoport helyére vonatkozik, és nincs hatással a globálisan telepített Traffic Manager-profilra.

    6.  Kattintson a **Létrehozás** gombra.

    7.  Ha a Traffic Manager-profil globális telepítése befejeződött, az a megfelelő erőforráscsoporthoz kerül, mint az egyik erőforrás.

    ![Erőforráscsoportok a Create Traffic Manager Profile](media/azure-stack-solution-geo-distributed/image45.png)

### <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

1. A portálok keresési sávjában keresse meg az előző szakaszban létrehozott **Traffic Manager profil** nevét, és válassza ki a Traffic Manager-profilt a megjelenített eredmények között.

2. **Traffic Manager profilban**a **Beállítások** szakaszban válassza a végpontoklehetőséget.

3. Válassza a **Hozzáadás** lehetőséget.

4. Az Azure Stack végpont hozzáadása.

5. A **Típus mezőben**válassza a **külső végpont**lehetőséget.

6. Adja meg a végpont **nevét** , ideális esetben a Azure stack nevét.

7. Teljes tartománynév (**FQDN**) esetén használja a Azure stack webalkalmazás külső URL-címét.

8. A földrajzi leképezés területen válassza ki azt a régiót/kontinenst, ahol az erőforrás található. Például: **Európa.**

9. A megjelenő ország/régió legördülő listából válassza ki azt az országot, amely erre a végpontra vonatkozik. Például: **Németország**.

10. A **Beállítás letiltottként** jelölőnégyzetet ne jelölje ki.

11. Kattintson az **OK** gombra.

12. Az Azure-végpont hozzáadása:

    1.  A **Típus mezőben**válassza az **Azure-végpont**lehetőséget.

    2.  Adja meg a végpont **nevét** .

    3.  A **cél erőforrástípus mezőben**válassza a **app Service**lehetőséget.

    4.  A **cél erőforrásnál**válassza az **app Service kiválasztása** lehetőséget az azonos előfizetéshez tartozó Web Apps listájának megjelenítéséhez. Az **erőforrás**területen válassza ki az első végpontként használt app Service-t.

13. A földrajzi leképezés területen válassza ki azt a régiót/kontinenst, ahol az erőforrás található. Például: **Észak-Amerika/Közép-Amerika/Karib-térség.**

14. A megjelenő ország/régió legördülő menüben hagyja üresen ezt a helyet, hogy kiválassza az összes fenti regionális csoportosítást.

15. A **Beállítás letiltottként** jelölőnégyzetet ne jelölje ki.

16. Kattintson az **OK** gombra.

    > [!Note]  
    >  Hozzon létre legalább egy olyan végpontot, amelynek földrajzi hatóköre az összes (világ), hogy az erőforrás alapértelmezett végpontja legyen.

1. Mindkét végpont hozzáadásakor a rendszer a **Traffic Manager profilban** jeleníti meg a figyelési állapotukat **online**állapottal együtt.

    ![Traffic Manager profil végpontjának állapota](media/azure-stack-solution-geo-distributed/image46.png)

**A globális vállalat az Azure geo-eloszlási képességeire támaszkodik**

Az adatforgalom Azure-Traffic Manager és földrajzilag specifikus végpontokon keresztüli átirányítása lehetővé teszi a globális vállalatok számára a regionális szabályozások betartását, valamint a helyi és a távoli üzleti telephelyek sikeréhez elengedhetetlen adatok megfelelő és biztonságos megőrzését.

## <a name="next-steps"></a>További lépések

- Az Azure-minták Felhőkhöz kapcsolatos további információkért lásd: [tervezési minták Felhőkhöz](https://docs.microsoft.com/azure/architecture/patterns).
