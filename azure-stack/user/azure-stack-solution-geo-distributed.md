---
title: A forgalmat egy földrajzilag elosztott alkalmazás megoldással az Azure és az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy földrajzilag elosztott alkalmazás megoldásokat az Azure és az Azure Stacken, amely arra utasítja a megadott végpontokra érkező forgalom.
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
ms.date: 01/14/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: a348e4e7eada9537defa292f667cfd3eb1e27438
ms.sourcegitcommit: eccbd0098ef652919f357ef6dba62b68abde1090
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492462"
---
# <a name="tutorial-create-a-geo-distributed-app-solution-to-direct-traffic-with-azure-and-azure-stack"></a>Oktatóanyag: Földrajzilag elosztott alkalmazás számára a forgalom az Azure és az Azure Stack megoldás létrehozása

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Útmutató a forgalmat a földrajzilag elosztott alkalmazások minta használatával különböző metrikák alapján meghatározott végpontokhoz. Egy Traffic Manager létrehozása a földrajzi alapú útválasztást és a végpont konfigurációs profil biztosítja, információk alapján a regionális követelmények, a vállalati és a nemzetközi szabályozás és az adattárolási igényeinek végpontok van irányítva.

Ebben az oktatóanyagban egy mintául szolgáló környezet építi fel:

> [!div class="checklist"]
> - Földrajzilag elosztott alkalmazás létrehozása.
> - A Traffic Manager segítségével az alkalmazást.

## <a name="use-the-geo-distributed-apps-pattern"></a>A földrajzilag elosztott alkalmazások minta használata

A földrajzilag elosztott mintával az alkalmazás kiterjedő régióban. Is alapértelmezés szerint a nyilvános felhő, de a felhasználói egy része lehet szükség, hogy adataikat az adott régióban maradjanak. Felhasználók számára a leginkább megfelelő felhő igényeik alapján irányíthatók.

### <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

#### <a name="scalability-considerations"></a>Méretezési szempontok

Ön ebben az oktatóanyagban létre fog hozni a megoldás nem méretezhetőség befogadásához. Azonban más Azure-ban és a helyszíni megoldásokkal együtt használni, ha úgy tud megfelelni méretezhetőségi követelményeinek. A hibrid megoldás létrehozása az automatikus skálázást keresztül traffic manager további információkért lásd: [több felhőre kiterjedő méretezési megoldások létrehozása az Azure-ral](azure-stack-solution-cloud-burst.md).

#### <a name="availability-considerations"></a>Rendelkezésre állási szempontok

Ebben az esetben a méretezési szempontok szerint ez a megoldás közvetlenül nem cím rendelkezésre állási. Az Azure és helyszíni megoldások azonban ez a megoldás magas rendelkezésre állásának részt vevő összetevők belül is kell végrehajtani.

### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?

- A szervezete egyéni regionális biztonsági és terjesztési házirendeket igénylő nemzetközi ágak.

- A szervezet irodával mindegyikének lekéri a dolgozók, üzleti és létesítmény, tevékenység / helyi szabályozásokról és az időzónák reporting igénylő adatok.

- Nagy méretű követelmények több alkalmazást üzembe helyezés egy adott régión belül és több régió nagy terhelés követelmények kezelésére az alkalmazások horizontális felskálázásával teljesül.

### <a name="planning-the-topology"></a>A topológia tervezése

Mielőtt egy elosztott alkalmazás üzembe helyezésének előkészítése kialakítására, megismeréséről az alábbiakat:

-   **Az alkalmazás egyéni tartomány:** Mi az az egyéni tartománynév használó ügyfelek számára a hozzáférést az alkalmazáshoz? A mintaalkalmazás az egyéni tartománynév van *www.scalableasedemo.com.*

-   **Traffic Manager-tartományt:** Tartománynév akkor kell kiválasztani, amikor hoz létre egy [Azure Traffic Manager-profil](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles). Ezzel a névvel együtt a *trafficmanager.net* utótag regisztrálni egy Traffic Manager által felügyelt tartomány-bejegyzést. A mintaalkalmazás a név kiválasztott van *méretezhető ase bemutató*. Ennek eredményeképpen a teljes tartománynévnek a Traffic Manager által felügyelt rendszer *méretezhető ase demo.trafficmanager.net*.

-   **Az alkalmazás üzembe helyezésének előkészítése méretezési stratégia:** Döntse el, hogy az alkalmazás üzembe helyezésének előkészítése elosztva több App Service-környezetek egyetlen régióban, több régióban vagy mindkét módszerénél vegyesen. A döntést kell alapulnia elvárásainak, ahol a felhasználói forgalmat fog származnak, és arról, hogy a többi egy alkalmazást támogató háttér-infrastruktúra is méretezhető. Például 100 %-os állapot nélküli alkalmazások, egy alkalmazást rugalmasan méretezhetők több App Service Environment-környezetek kombinációját használó Azure-régiónként, szorozva az App Service-környezetek üzembe helyezett Azure-területekre. A 15 + globális Azure-régiókban érhető el, amelyek közül választhatnak, ügyfelei valóban hozhat létre egy világméretű kapacitású alkalmazás üzembe helyezésének előkészítése. Az itt használt mintaalkalmazás három App Service Environment-környezetek létrehozott egy Azure-régióban (USA déli középső Régiója).

-   **Az App Service Environment-környezetek elnevezési:** Minden App Service Environment-környezet megköveteli egy egyedi nevet. Egy vagy két App Service Environment-környezetek, túl hasznos van egy elnevezési konvenciója segítségével azonosíthatja az egyes App Service Environment-környezet. Az itt használt mintaalkalmazás egy egyszerű elnevezési egyezmény lett megadva. A neve, a három App Service Environment-környezetek *fe1ase*, *fe2ase*, és *fe3ase*.

-   **Az alkalmazások elnevezési:** Mivel az alkalmazás több példányát telepíti, egy nevet az üzembe helyezett alkalmazás minden egyes példányánál van szükség. Az App Service-környezetek az alkalmazás névvel több környezetben is használható. Mivel minden App Service Environment-környezet rendelkezik egy egyedi tartományutótagot, a fejlesztők választhat újra pontos ugyanazon alkalmazás minden környezetben. Egy fejlesztő például rendelkezhet elnevezése a következő alkalmazásokat: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, és így tovább. Az itt használt alkalmazás minden példányt egyedi névvel rendelkezik. A használt alkalmazás-példány neve *webfrontend1*, *webfrontend2*, és *webfrontend3*.

> [!Tip]  
> ![hibrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> A Microsoft Azure Stack az Azure bővítménye. Az Azure Stack számos lehetőséget kínál a hatékonyságával és innovációjával emeli a felhő-számítástechnika a helyszíni környezetben, az egyetlen olyan hibrid felhős, amely lehetővé teszi, hogy létrehozása és üzembe helyezése hibrid alkalmazások bárhol engedélyezése.  
> 
> A tanulmány [hibrid alkalmazások kapcsolatos kialakítási szempontok](https://aka.ms/hybrid-cloud-applications-pillars) áttekinti a szoftverminőség alappillérei (elhelyezési, méretezhetőség, rendelkezésre állás, rugalmasság, kezelhetőségi és biztonsági) a kialakítása, üzembe helyezése és működtetése hibrid alkalmazások. A kialakítási szempontokat segítséget nyújt a hibrid alkalmazások kialakítását, minimálisra csökkentik az éles környezetben kihívások optimalizálása.

## <a name="part-1-create-a-geo-distributed-app"></a>1\. rész: Földrajzilag elosztott alkalmazás létrehozása

Ez a kijelző egy webalkalmazást fog létrehozni.

> [!div class="checklist"]
> - Webalkalmazások létrehozása és közzététele.
> - Adja hozzá az Azure-Adattárakkal kódot.
> - Az alkalmazás buildszáma több felhő cél mutat.
> - Kezelheti, és konfigurálhatja a CD-folyamat.

### <a name="prerequisites"></a>Előfeltételek

Egy Azure-előfizetés és az Azure Stack-telepítés is szükséges.

### <a name="geo-distributed-app-steps"></a>Földrajzilag elosztott alkalmazás lépések

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Szerezze be az egyéni tartomány és a DNS konfigurálása

Frissítse a tartomány DNS-zónafájljában. Az Azure AD majd ellenőrizheti az egyéni tartománynév tulajdonjogát. Használja [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) az Azure vagy Office 365 és a külső DNS-rekordok Azure-ban, vagy adja hozzá a DNS-bejegyzést, [egy másik DNS-regisztráló webhelyén](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Egyéni tartomány regisztrálása egy nyilvános regisztrálójánál.

2. Jelentkezzen be a tartomány tartománynév-regisztrálójába. Jóváhagyott rendszergazda fiókdíjat, hogy a DNS-frissítések.

3. A tartomány DNS-zónafájljában frissítése az Azure AD által biztosított DNS-bejegyzés hozzáadásával. A DNS-bejegyzés nem módosítja a viselkedése például a levélkezelési útválasztást vagy a webes üzemeltetés.

### <a name="create-web-apps-and-publish"></a>Web Apps alkalmazások létrehozása és közzététele

Webes alkalmazás üzembe helyezése az Azure és az Azure Stack, hibrid folyamatos integráció/folyamatos Készregyártás (CI/CD) beállítása és automatikus ügyfélleküldéses mindkét felhőben változtatásait.

> [!Note]  
> Az Azure Stack megfelelő képekkel hírcsatorna-futtassa (Windows Server és SQL) és az App Service-környezet szükség. További információkért lásd: [az App Service-ben az Azure Stack használatának megkezdése előtt](../operator/azure-stack-app-service-before-you-get-started.md) az Azure Stack-operátorokról dokumentációjában.

#### <a name="add-code-to-azure-repos"></a>Adja hozzá a kódot az Azure-Adattárakkal

1. Jelentkezzen be a Visual Studio- **project-létrehozási jogokkal rendelkező fiók** az Azure-Adattárakkal.

    CI/CD kódját és az infrastruktúra kódjának alkalmazhatja. Használat [Azure Resource Manager-sablonok](https://azure.microsoft.com/resources/templates/) mindkét magán- és üzemeltetett felhőalapú fejlesztéshez.

    ![Csatlakozás egy projektet a Visual Studióban](media/azure-stack-solution-geo-distributed/image1.JPG)

2. **A tárház klónozása** létrehozásával és az alapértelmezett webes alkalmazás megnyitásával.

    ![Klónozott tárház a Visual Studióban](media/azure-stack-solution-geo-distributed/image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>Webes alkalmazás üzembe helyezése mindkét felhőben létrehozása

1.  Szerkessze a **WebApplication.csproj** fájlt: Válassza ki `Runtimeidentifier` , és adja hozzá `win10-x64`. (Lásd: [Self-contained telepítési](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentációja.)

    ![Webes alkalmazás projektfájlt a Visual Studióban szerkesztése](media/azure-stack-solution-geo-distributed/image3.png)

1.  **Az Azure-kódtárak a forráskód verziókezelőbe** Team Explorer használatával.

2.  Ellenőrizze, hogy a **alkalmazáskód** be Azure-kódtárak ellenőrzése megtörtént.

### <a name="create-the-build-definition"></a>A build definíció létrehozása

1. **Jelentkezzen be Azure-folyamatok** buildelési definíciókat létrehozására megerősítéséhez.

2. Adjon hozzá `-r win10-x64` kódot. A Hozzáadás szükség egy önálló telepítés a .NET Core használatával aktiválásához.

    ![Adja hozzá a builddefiníció kódot](media/azure-stack-solution-geo-distributed/image4.png)

3. **Futtassa a build**. A [önálló telepítés build](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) folyamat közzéteszi az összetevők, amelyek futhatnak az Azure és az Azure Stackben.

**Az Azure szolgáltatott ügynök használatával**

Az Azure-folyamatokban üzemeltetett ügynök használatával beállítás kényelmes készíthet és helyezhet üzembe webalkalmazásokat. A Microsoft Azure, amely lehetővé teszi a folyamatos fejlesztési, tesztelési és üzembe helyezési automatikusan történik a karbantartása és frissítései.

### <a name="manage-and-configure-the-cd-process"></a>Kezelheti és konfigurálhatja a CD-folyamat

Az Azure DevOps és az Azure DevOps-kiszolgáló adja meg hatékonyan konfigurálható és kezelhető folyamat számára több környezetekben, például fejlesztési, előkészítési, QA és éles környezetek; amely jóváhagyások adott szintjén is.

## <a name="create-release-definition"></a>Kiadási definíció létrehozása

1.  Válassza ki a **plusz** gombra kattintva adhat hozzá egy új kiadása alatt a **kiadásokban** lapján a **készítése és kiadása** VSO szakaszában.

    ![Kiadási definíció létrehozása](media/azure-stack-solution-geo-distributed/image5.png)

2. A alkalmazni az Azure App Service központi telepítési sablont.

   ![Az Azure App Service üzembe helyezési sablon alkalmazása](meDia/azure-stack-solution-geo-distributed/image6.png)

3. A **Hozzáadás összetevő**, adja hozzá az Azure Cloud build alkalmazás összetevő.

   ![Az Azure Cloud buildre összetevő hozzáadása](media/azure-stack-solution-geo-distributed/image7.png)

4. A folyamat fülre, válassza a **fázisba, a feladat** a környezet hivatkozásra, és állítsa be az Azure-felhő környezet értékeket.

   ![Azure-felhő környezeti értékeinek beállítása](media/azure-stack-solution-geo-distributed/image8.png)

5. Állítsa be a **környezet neve** , és válassza ki a **Azure-előfizetés** az Azure Felhőbeli végpont számára.

      ![Válassza ki az Azure-előfizetéshez tartozó Azure-Felhőbeli végpont](media/azure-stack-solution-geo-distributed/image9.png)

6. A **App service neve**, állítsa be a szükséges az Azure app service neve.

      ![Beállítása az Azure app service neve](media/azure-stack-solution-geo-distributed/image10.png)

7. Adja meg a "Hosted VS2017" alatt **fronta Agenta** Azure-felhőben üzemeltetett környezetben.

      ![Fronta Agenta Azure-felhőben üzemeltetett környezet beállítása](media/azure-stack-solution-geo-distributed/image11.png)

8. Az Azure App Service üzembe helyezése menüben válassza a érvényes **csomag vagy a mappa** a környezethez. Válassza ki **OK** való **mappába**.
  
      ![Válassza ki a csomag vagy a mappa az Azure App Service Environment környezetben](media/azure-stack-solution-geo-distributed/image12.png)

      ![Válassza ki a csomag vagy a mappa az Azure App Service Environment környezetben](media/azure-stack-solution-geo-distributed/image13.png)

9. Mentse az összes módosítást, és térjen vissza a **kibocsátási folyamatok**.

    ![Mentse a módosításokat a kibocsátási folyamat](media/azure-stack-solution-geo-distributed/image14.png)

10. Adjon hozzá egy új összetevő, a build, az Azure Stack-alkalmazás kiválasztása.
    
    ![Az Azure Stack app új összetevő hozzáadása](media/azure-stack-solution-geo-distributed/image15.png)


11. Adjon hozzá egy további környezet az Azure App Service üzembe helyezési alkalmazásával.
    
    ![Az Azure App Service üzembe helyezési környezet hozzáadása](media/azure-stack-solution-geo-distributed/image16.png)

12. Az Azure Stack új környezet nevét.
    
    ![Az Azure App Service üzembe helyezési környezet neve](media/azure-stack-solution-geo-distributed/image17.png)

13. Keresse meg az Azure Stack-környezet alapján **feladat** fülre.
    
    ![Az Azure Stack-környezet](media/azure-stack-solution-geo-distributed/image18.png)

14. Válassza ki az előfizetést az Azure Stack-végpont.
    
    ![Válassza ki az előfizetést az Azure Stack-végpont](media/azure-stack-solution-geo-distributed/image19.png)

15. Az Azure Stack webalkalmazás nevét állítja be az App service nevét.

    ![Állítsa be az Azure Stack webalkalmazás neve](media/azure-stack-solution-geo-distributed/image20.png)

16. Válassza ki az Azure Stack-ügynök.
    
    ![Válassza ki az Azure Stack-ügynök](media/azure-stack-solution-geo-distributed/image21.png)

17. Az Azure App Service üzembe helyezése területen válassza ki a érvényes **csomag vagy a mappa** a környezethez. Válassza ki **OK** mappába.

    ![Válassza ki a mappát az Azure App Service üzembe helyezéséhez](media/azure-stack-solution-geo-distributed/image22.png)

    ![Válassza ki a mappát az Azure App Service üzembe helyezéséhez](media/azure-stack-solution-geo-distributed/image23.png)

18. Változó lapján nevű változó hozzáadása `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, mint az értékét állítsa **igaz**, és az Azure Stackhez.
    
    ![A változó hozzáadása az Azure-alkalmazás üzembe helyezése](media/azure-stack-solution-geo-distributed/image24.png)

19. Válassza ki a **folyamatos** üzembe helyezési eseményindító ikonra az összetevők és a engedélyezése a **folytatja** készregyártás eseményindítója.
    
    ![Válassza ki a folyamatos készregyártás eseményindítója](media/azure-stack-solution-geo-distributed/image25.png)

20. Válassza ki a **központi telepítés előtti** feltételek ikonra az Azure Stack-környezetben, és állítsa az eseményindító **kiadás után.**
    
    ![Központi telepítés előtti feltételek kiválasztása](media/azure-stack-solution-geo-distributed/image26.png)

21. Mentse az összes módosítást.

> [!Note]  
> A feladatok egyes beállítások előfordulhat, hogy automatikusan meghatározott [környezeti változók](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) kiadási definíció a sablon létrehozásakor. Ezek a beállítások nem módosíthatók a tevékenység beállításait; Ehelyett a szülő környezet típusú elem ki kell választani a beállítások szerkesztéséhez.

## <a name="part-2-update-web-app-options"></a>2\. rész: Webes alkalmazás beállítások frissítése

Az [Azure App Service](https://docs.microsoft.com/azure/app-service/overview) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. 

![Azure App Service](media/azure-stack-solution-geo-distributed/image27.png)

> [!div class="checklist"]
> - Meglévő egyéni DNS-név leképezése az Azure Web Appshez.
> - Használja a **CNAME-rekordot** és a egy **rekord** egy egyéni DNS-név leképezése az App Service-ben.

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Meglévő egyéni DNS-név leképezése az Azure Web Appsra

> [!Note]  
>  Használjon egy olyan CNAME REKORDOT egy legfelső szintű tartomány (például northwind.com) kivételével az összes egyéni DNS-neveket.

Élő webhely és hozzá tartozó DNS-tartománynév migrálása az App Service-be: [Aktív DNS-név migrálása az Azure App Service-be](https://docs.microsoft.com/azure/app-service/manage-custom-dns-migrate-domain).

### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

-   [Hozzon létre egy App Service-alkalmazás](https://docs.microsoft.com/azure/app-service/), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.

-   Vásároljon egy tartománynevet és a DNS-jegyzékéhez való hozzáférés biztosítása a tartomány-szolgáltatóhoz.

Frissítse a tartomány DNS-zónafájljában. Az Azure AD ellenőrzi, hogy az egyéni tartománynév tulajdonjogát. Használja [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) az Azure vagy Office 365 és a külső DNS-rekordok Azure-ban, vagy adja hozzá a DNS-bejegyzést, [egy másik DNS-regisztráló webhelyén](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

-   Egyéni tartomány regisztrálása egy nyilvános regisztrálójánál.

-   Jelentkezzen be a tartomány tartománynév-regisztrálójába. (A jóváhagyott rendszergazda előfordulhat, hogy megkövetelhető, hogy a DNS-frissítések.)

-   A tartomány DNS-zónafájljában frissítése az Azure AD által biztosított DNS-bejegyzés hozzáadásával.

For example, to add DNS entries for northwindcloud.com and www.northwindcloud.com, configure DNS settings for the northwindcloud.com root domain.

> [!Note]  
>  A tartománynév alapján vásárolhatók a [az Azure portal](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain). Egy egyéni DNS-név webalkalmazásra való leképezéséhez a webalkalmazás [App Service-csomagjának](https://azure.microsoft.com/pricing/details/app-service/) fizetős rétegben kell lennie (**megosztott**, **alapvető**, **szabványos** vagy **prémium szintű**).



### <a name="create-and-map-cname-and-a-records"></a>CNAME és A rekordok létrehozását és

#### <a name="access-dns-records-with-domain-provider"></a>DNS-rekordok elérése tartományszolgáltató esetén

> [!Note]  
>  Az Azure DNS konfigurálása az Azure-webalkalmazások egyéni DNS-nevet. További információt az [egyéni tartománybeállítások egy Azure-szolgáltatáshoz az Azure DNS használatával történő megadását](https://docs.microsoft.com/azure/dns/dns-custom-domain) ismertető cikkben talál.

1.  Jelentkezzen be a webhely a fő szolgáltató.

2.  Keresse meg a DNS-rekordok kezelésére szolgáló oldalt. Minden tartományszolgáltatójának saját DNS-rekordok felülettel rendelkezik. A webhely **Tartománynév**, **DNS** vagy **Névkiszolgáló kezelése** címkével ellátott területeit keresse.

DNS-rekordok oldala tekinthető **saját tartományok**. Keresse meg a hivatkozás nevű **zónafájl**, **DNS-rekordok**, vagy **speciális konfiguráció**.

A következő képernyőkép egy DNS-rekordokat tartalmazó oldalra mutat példát:

![DNS-rekordokat tartalmazó oldal példája](media/azure-stack-solution-geo-distributed/image28.png)

1. A tartománynév Regisztrálójánál, válassza ki **hozzáadása vagy a létrehozás** rekord létrehozásához. Egyes szolgáltatók eltérő hivatkozásokat használnak a különböző rekordtípusok hozzáadásához. A szolgáltató dokumentációjában talál.

2. Adjon hozzá egy CNAME-rekordot le egy altartományt az alkalmazás alapértelmezett gazdagépnevére.

   A www.northwindcloud.com tartomány például vegyen fel egy CNAME-rekordot, amely leképezi a neve < alkalmazás\_neve >. azurewebsites.NET webhelyet.

Miután hozzáadta a CNAME REKORDOT, a DNS-rekordok oldala a következő példához hasonlóan néz ki:

![Navigálás a portálon egy Azure-alkalmazáshoz](media/azure-stack-solution-geo-distributed/image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>A CNAME rekord hozzárendelésének engedélyezése az Azure-ban

1. Egy új lap jelentkezzen be az Azure Portalon

2. Keresse meg az App Servicesbe való.

3. Válassza ki a webalkalmazást.

4. Az Azure Portal bal oldali navigációs sávján válassza ki az **Egyéni tartományok** elemet.

5. Válassza ki a **Gazdagépnév hozzáadása** elem melletti **+** ikont.

6. Írja be például a teljesen minősített tartománynevét `www.northwindcloud.com`.

7. Válassza az **Érvényesítés** lehetőséget.

8. Jelzi, ha más típusú további rekordok hozzáadása (`A` vagy `TXT`) a tartomány nevét regisztráló szervezetek DNS-rekordok. Az Azure biztosít az értékeket, és ezeket a rekordokat típusát:

   a.  egy **A** rekordra, amelyet leképezhet az alkalmazás IP-címére.

   b.  A **TXT** rekordra, amelyet leképezhet az alkalmazás alapértelmezett gazdagépnevére < alkalmazás_neve >. azurewebsites.NET webhelyet. App Service-ben ezt a rekordot csak a konfiguráláskor használja egyéni tartomány tulajdonjogának ellenőrzéséhez. Ellenőrzés után a TXT-rekord törlése.

9. Befejezheti a feladatot a tartomány regisztráló lapon, és kísérelje meg újra amíg érvényesítését a **gazdagépnév hozzáadása** gomb aktívvá válik.

10. Győződjön meg arról, hogy **gazdagépnév rekordtípusa** értékre van állítva **CNAME** (www.example.com vagy bármely altartomány).

11. Válassza a **Gazdagépnév hozzáadása** lehetőséget.

12. Írja be például a teljesen minősített tartománynevét `northwindcloud.com`.

13. Válassza az **Érvényesítés** lehetőséget. A **Hozzáadás** aktiválva van.

14. Győződjön meg arról, hogy **gazdagépnév rekordtípusa** értékre van állítva **rekord** (example.com).

15. **Gazdagépnév hozzáadása**.

    Az új gazdanév megjelenik az alkalmazás egy kis ideig eltarthat **egyéni tartományok** lapot. Próbálja meg frissíteni a böngészőt az adatok frissítéséhez.
  
    ![Egyéni tartományok](media/azure-stack-solution-geo-distributed/image31.png) 
  
    Ha hiba történik, egy ellenőrzési hiba értesítés jelenik meg a lap alján. ![Ellenőrzési hiba](media/azure-stack-solution-geo-distributed/image32.png)

> [!Note]  
>  A fenti lépéseket is meg lehet ismételni a helyettesítő karaktert tartalmazó tartomány hozzárendelése (\*. northwindcloud.com). Ez lehetővé teszi az app Service-ben további altartományok hozzáadását, mindegyikhez külön CNAME rekord létrehozása nélkül. A regisztráló utasítások szerint a beállítás megadásához.

#### <a name="test-in-a-browser"></a>Tesztelés a böngészőben

Tallózással keresse meg a korábban konfigurált DNS-nevét (például `northwindcloud.com` vagy www.northwindcloud.com).

## <a name="part-3-bind-a-custom-ssl-cert"></a>3\. rész: Egyéni SSL-tanúsítvány kötése

Ez a rész a következő történik:

> [!div class="checklist"]
> - Az egyéni SSL-tanúsítvány kötése az App Service-ben.
> - HTTPS kényszerítése az alkalmazáshoz.
> - Parancsfájlok SSL-tanúsítványok kötésének automatizálása.

> [!Note]  
> Ha szükséges, szerezzen be egy ügyfél SSL-tanúsítvány az Azure Portalon, és a webalkalmazáshoz kötheti azt. További információkért lásd: a [App Service-tanúsítványok oktatóanyag](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site).

### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

-   [Hozzon létre egy App Service-alkalmazást.](https://docs.microsoft.com/azure/app-service/)
-   [Képezhet le egyedi DNS-nevet a webalkalmazáshoz.](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
-   Egy SSL-tanúsítványt egy megbízható hitelesítésszolgáltatótól beszerezni, és a kulcs használatával a kérés aláírásához.

### <a name="requirements-for-your-ssl-certificate"></a>Az SSL-tanúsítvány követelményei

A tanúsítvány App Service-ben történő használatához a tanúsítványnak meg kell felelnie az alábbi követelmények mindegyikének:

-   Megbízható hitelesítésszolgáltató által aláírt.

-   Jelszóval védett PFX-fájlba exportált.

-   Mennyi ideig tartalmazza a titkos kulcs legalább 2048 bit.

-   A tanúsítványláncban lévő összes köztes tanúsítványt tartalmaz.

> [!Note]  
>  **Elliptikus görbéjű titkosítási (ECC) tanúsítványok** használata App Service-ben, de ez az útmutató nem tartalmazza. Egy hitelesítésszolgáltató kérjen segítséget az ECC-tanúsítványok létrehozása. 

#### <a name="prepare-the-web-app"></a>A webalkalmazás előkészítése

Az egyéni SSL-tanúsítvány kötése egy webes alkalmazásban a [App Service-csomag](https://azure.microsoft.com/pricing/details/app-service/) kell lennie a **alapszintű**, **Standard**, vagy **prémium szintű** szint.

#### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1.  Nyissa meg a [az Azure portal](https://portal.azure.com/) , és keresse meg a webalkalmazáshoz.

2.  A bal oldali menüben válassza ki a **App Services**, majd válassza ki a webalkalmazás nevét.

![Webalkalmazás kiválasztása](media/azure-stack-solution-geo-distributed/image33.png)

#### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

1.  A webalkalmazás lapjának bal oldali navigációs sávján görgessen a **beállítások** szakaszt, és válassza **vertikális felskálázás (App Service-csomag)** .

    ![Vertikális felskálázás menü](media/azure-stack-solution-geo-distributed/image34.png)

1.  Győződjön meg arról, a webalkalmazás nem szerepel a **ingyenes** vagy **megosztott** szint. A webalkalmazás aktuális szint egy kékkel kiemelt tanúsítványé.

    ![A tarifacsomag ellenőrzése](media/azure-stack-solution-geo-distributed/image35.png)

Egyéni SSL nem támogatott a **ingyenes** vagy **megosztott** szint. Upscale, kövesse a következő szakaszban, vagy a **válassza ki a tarifacsomagot** lapon, és ugorjon [feltölteni és hozzákötni az SSL-tanúsítvány](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

#### <a name="scale-up-your-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

1.  Válassza az **Alapszintű**, a **Standard** vagy a **Prémium** szintet.

2.  Válassza ki **kiválasztása**.

![Tarifacsomag kiválasztása](media/azure-stack-solution-geo-distributed/image36.png)

A skálázási művelet befejeződött, amikor értesítés jelenik meg.

![Vertikális felskálázási értesítés](media/azure-stack-solution-geo-distributed/image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>Az SSL-tanúsítvány kötése és köztes tanúsítványok egyesítése

Egyesítse a lánc több tanúsítványt.

1. **Nyissa meg az egyes tanúsítványok** kapott egy szövegszerkesztőben.

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

Az egyesített SSL-tanúsítvány exportálása a titkos kulccsal, a tanúsítvány által generált.

Egy titkos kulcsfájlt OpenSSL lett létrehozva. A tanúsítvány exportálása PFX-FÁJLBA, futtassa a következő parancsot, és cserélje le a zárójelben  *\<private-key-file >* és  *\<egyesített-certificate-file >* -a titkos kulcs elérési útja, és az egyesített tanúsítványfájl:

```powershell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Amikor a rendszer kéri, adja meg egy exportálási jelszót később az App Service-ben az SSL-tanúsítvány feltöltése.

Amikor az IIS vagy **Certreq.exe** szolgálnak a tanúsítványkérelem létrehozása, telepítse a tanúsítványt a helyi számítógépre, majd [exportálja a tanúsítványt PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

#### <a name="upload-the-ssl-certificate"></a>Az SSL-tanúsítvány feltöltése

1. Válassza ki **SSL-beállítások** a webalkalmazás bal oldali navigációs.

2. Válassza ki **-tanúsítvány feltöltése**.

3. A **PFX tanúsítványfájl**válassza PFX-fájlt.

4. A **tanúsítvány jelszava**, írja be a PFX-fájl exportálásakor létrehozott jelszót.

5. Válassza a **Feltöltés** lehetőséget.

![Tanúsítvány feltöltése](media/azure-stack-solution-geo-distributed/image38.png)

Amikor az App Service befejezi a tanúsítvány feltöltését, megjelenik a **SSL-beállítások** lapot.

![Az SSL-beállítások](media/azure-stack-solution-geo-distributed/image39.png)

#### <a name="bind-your-ssl-certificate"></a>Az SSL-tanúsítvány kötése

1.  Az a **SSL-kötések** szakaszban jelölje be **kötés hozzáadása**.

    > [!Note]  
    >  Ha a tanúsítvány fel lett töltve, de nem jelenik meg a tartományneveket a **állomásnév** legördülő listában, próbálkozzon a böngészőoldal.

1.  Az a **SSL-kötés hozzáadása** lapon, jelölje be a tartomány nevét a legördülő olyan plusz lehetőségeket, valamint a használni kívánt tanúsítványt.

1.  Az **SSL Type** (SSL típusa) területen válassza ki, hogy a [**kiszolgálónév jelzésén (SNI)** ](https://en.wikipedia.org/wiki/Server_Name_Indication) alapuló vagy IP-alapú SSL-t kíván-e használni.

    - **SNI-alapú SSL**: Több SNI-alapú SSL-kötés adható hozzá. Ez a beállítás lehetővé teszi, hogy több SSL-tanúsítvány biztosítson védelmet több tartomány számára ugyanazon az IP-címen. A legtöbb modern böngésző (beleértve az Internet Explorert, a Chrome-ot, a Firefox-ot és az Operát) támogatja az SNI-t (átfogóbb böngészőtámogatási információkat a [Kiszolgálónév jelzése](https://wikipedia.org/wiki/Server_Name_Indication) című szakaszban talál).

    - **IP-alapú SSL**: Csak egy IP-alapú SSL-kötés adható hozzá. Ez a beállítás csak egy SSL-tanúsítványnak engedélyezi egy dedikált nyilvános IP-cím védelmét. Több tartomány védelméhez, védheti meg az összes SSL-tanúsítvány használatával. IP-alapú SSL, a hagyományos lehetőség SSL-kötés létrehozásához.

1. Válassza ki **kötés hozzáadása**.

    ![SSL-kötés hozzáadása](media/azure-stack-solution-geo-distributed/image40.png)

Amikor az App Service befejezi a tanúsítvány feltöltését, megjelenik a **SSL-kötések** szakaszokat.

![SSL-kötések](media/azure-stack-solution-geo-distributed/image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>Az A rekord újramegfeleltetése IP SSL-hez

Ha IP-alapú SSL nincs használatban a web app, ugorjon a [az egyéni tartomány HTTPS teszt](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

Alapértelmezés szerint a a webalkalmazás megosztott nyilvános IP-címet használ. Ha a tanúsítványt az IP-alapú SSL van kötve, App Service-ben létrehoz egy új, dedikált IP-címet a webalkalmazás.

Amikor a webalkalmazás le van képezve egy A rekordot, a tartomány-beállításjegyzékbeli frissíteni kell a dedikált IP-címet.

A **egyéni tartomány** lap frissül az új, dedikált IP-címet. Ezt [IP-cím](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain), majd újból a [rekord](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain) az új IP-címre.

#### <a name="test-https"></a>HTTPS tesztelése

Különböző böngészőkben tallózással keresse meg a https://<your.custom.domain>to győződjön meg arról, a web app biztosítja.

![webalkalmazás kikeresése](media/azure-stack-solution-geo-distributed/image42.png)

> [!Note]  
> Tanúsítvány-ellenőrzési hibák fordulnak elő, ha önaláírt tanúsítványt okok válthatják ki, vagy a köztes tanúsítványok előfordulhat, hogy maradtak a PFX-fájlból történő exportálás során.

#### <a name="enforce-https"></a>HTTPS kényszerítése

Alapértelmezés szerint bárki elérheti a webalkalmazást HTTP-n keresztül. Előfordulhat, hogy a rendszer átirányítja a HTTPS-port a HTTP-kéréseket.

Válassza ki a webalkalmazás lapjának **SL beállítások**. Ezután a **HTTPS Only** (Csak HTTPS) területen válassza az **On** (Be) elemet.

![HTTPS kényszerítése](media/azure-stack-solution-geo-distributed/image43.png)

A művelet befejeződése után nyissa meg a HTTP URL-címek, amelyek az alkalmazás valamelyik. Példa:

-   https://<app_name>.azurewebsites.net
-   https://northwindcloud.com
-   <https://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>A TLS 1.1/1.2 kényszerítése

Az alkalmazás lehetővé teszi, hogy [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) , alapértelmezés szerint a továbbiakban nem tekinthető biztonságos szerint iparági szabványok, például az 1.0-s [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). A TLS újabb verziójának kényszerítéséhez kövesse az alábbi lépéseket:

1.  A webalkalmazás lapjának bal oldali navigációs sávján válassza **SSL-beállítások**.

2.  A **TLS-verzió**, válassza ki a TLS minimális verzióját.

![A TLS 1.1 vagy 1.2 kényszerítése](media/azure-stack-solution-geo-distributed/image44.png)

### <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

1.  Válassza ki **erőforrás létrehozása** > **hálózatkezelés** > **Traffic Manager-profil** > **létrehozás**.

2.  A **Traffic Manager-profil létrehozása** területen adja meg a következőket:

    1.  A **neve**, adja meg a profil nevét. Ez a név egyedinek kell lennie a forgalom manager.net zóna és a forgalom manager.net, amely a Traffic Manager-profil eléréséhez használt DNS-nevet eredményezi.

    2.  A **útválasztási módszer**, jelölje be a **Geographicrouting metódus**.

    3.  A **előfizetés**, válassza ki az előfizetést, amelyben ez a profil létrehozásához.

    4.  Az **Erőforráscsoport** mezőben hozzon létre egy új erőforráscsoportot, amely alá ezt a profilt helyezi.

    5.  Az **Erőforráscsoport helye** területen válassza ki az erőforráscsoport helyét. Ez a beállítás az erőforráscsoport helyére vonatkozik, és nincs hatással a Traffic Manager-profilt telepített globálisan.

    6.  Kattintson a **Létrehozás** gombra.

    7.  Ha a Traffic Manager-profil globális üzembe helyezése befejeződött, akkor szerepel a megfelelő erőforráscsoportban egy erőforrást.

    ![Az erőforráscsoportok Traffic Manager-profil létrehozása](media/azure-stack-solution-geo-distributed/image45.png)

### <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

1. A portálok keresősávban keressen a **Traffic Manager-profil** az előző szakaszban, és válasszon a traffic manager-profilt a megjelenített eredmények létrehozott nevét.

2. A **Traffic Manager-profil**, a a **beállítások** szakaszban jelölje be **végpontok**.

3. Válassza a **Hozzáadás** lehetőséget.

4. Az Azure Stack-végpont hozzáadása.

5. A **típus**válassza **külső végpont**.

6. Adjon meg egy **neve** ezen a végponton, ideális esetben az Azure Stack nevét.

7. A teljes tartománynév (**FQDN**), a külső URL-cím használata az Azure Stack Web App for.

8. A földrajzi hozzárendelés válassza az a régió/kontinens, ahol az erőforrás megtalálható. Ha például **Európa.**

9. Az ország/régió listából, amely akkor jelenik meg válassza az ország, amely ezt a végpontot vonatkozik. Ha például **Németország**.

10. A **Beállítás letiltottként** jelölőnégyzetet ne jelölje ki.

11. Kattintson az **OK** gombra.

12. Az Azure-végpont hozzáadása:

    1.  A **típus**válassza **Azure-végpont**.

    2.  Adjon meg egy **neve** a végponthoz.

    3.  A **célerőforrás típusánál**válassza **App Service-ben**.

    4.  A **Célerőforrásnál**válassza **alkalmazásszolgáltatás kiválasztása** megjelenítéséhez a listában, a Web Apps egy előfizetésen belül. A **erőforrás**, válassza ki az App service, az első végpontként használt.

13. A földrajzi hozzárendelés válassza az a régió/kontinens, ahol az erőforrás megtalálható. Ha például **Észak-Amerika/Közép-Amerika/Karib-szigetek.**

14. Alatt az ország/régió listából, amely akkor jelenik meg hagyja üresen a helyszíni üres, válassza ki az összes a fenti regionális csoportosítást.

15. A **Beállítás letiltottként** jelölőnégyzetet ne jelölje ki.

16. Kattintson az **OK** gombra.

    > [!Note]  
    >  Hozzon létre legalább egy végpontot az összes (globális) az alapértelmezett végpont az erőforrás egyikükön földrajzi területét.

1. Mindkét végpont hozzáadása befejeződése után jelennek meg a **Traffic Manager-profil** azok figyelési állapotát, valamint **Online**.

    ![A TRAFFIC Manager-profil végpont állapota](media/azure-stack-solution-geo-distributed/image46.png)

**Globális vállalati Azure Geo-eloszlás képességeket támaszkodik.**

Adatok forgalmat az Azure Traffic Manager és a geography-specifikus végpontok keresztül lehetővé teszi a regionális előírások betartását és az adatok megfelelő és nem biztonságos, amely elengedhetetlen a sikeres, a helyi és távoli vállalati helyek a cégek számára.

## <a name="next-steps"></a>További lépések

- Az Azure-minták Felhőkhöz kapcsolatos további információkért lásd: [tervezési minták Felhőkhöz](https://docs.microsoft.com/azure/architecture/patterns).
