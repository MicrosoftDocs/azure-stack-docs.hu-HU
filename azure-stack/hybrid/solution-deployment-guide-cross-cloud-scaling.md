---
title: Felhőben futó alkalmazások üzembe helyezése az Azure-ban és Azure Stack hub használatával
description: Ismerje meg, hogyan helyezhet üzembe olyan alkalmazást, amely többfelhős méretezést biztosít az Azure és a Azure Stack hub használatával.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 54d79742ab39a2732a774401a1765b7a264e1fab
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73638452"
---
# <a name="deploy-an-app-that-scales-cross-cloud-using-azure-and-azure-stack-hub"></a>Felhőben futó alkalmazások üzembe helyezése az Azure-ban és Azure Stack hub használatával

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek és Azure Stack hub Development Kit*

Megtudhatja, hogyan hozhat létre többfelhős megoldást egy olyan manuálisan aktivált folyamat megadására, amely egy Azure Stack hub által üzemeltetett webalkalmazásból egy Azure-ban üzemeltetett webalkalmazásba való váltásra szolgál az automatikus skálázással a Traffic Manager használatával. Ez a folyamat rugalmas és méretezhető felhőalapú segédprogramot biztosít a betöltés alatt.

Ezzel a mintával előfordulhat, hogy a bérlő nem áll készen az alkalmazás futtatására a nyilvános felhőben. Előfordulhat azonban, hogy nem valósítható meg gazdaságosan a vállalat számára, hogy fenntartsa a helyszíni környezetben szükséges kapacitást, hogy kezelni tudja az alkalmazás iránti igényt. A bérlő a nyilvános felhő rugalmasságát a helyszíni megoldással is használhatja.

Ebben a megoldásban egy példaként szolgáló környezetet fog kiépíteni a következőkre:

> [!div class="checklist"]
> - Hozzon létre egy több csomópontot tartalmazó webalkalmazást.
> - Konfigurálja és felügyelje a folyamatos üzembe helyezés (CD) folyamatát.
> - Tegye közzé a webalkalmazást Azure Stack hubhoz.
> - Hozzon létre egy kiadást.
> - Ismerje meg az üzemelő példányok figyelését és nyomon követését.

> [!Tip]  
> ![Hybrid-Pillars. png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack hub az Azure kiterjesztése. Azure Stack hub a felhő-számítástechnika rugalmasságát és innovációját a helyszíni környezetbe helyezi, így az egyetlen hibrid felhő, amely lehetővé teszi a hibrid alkalmazások bárhol történő létrehozását és üzembe helyezését.  
> 
> A [hibrid alkalmazásokkal kapcsolatos tervezési szempontok](overview-app-design-considerations.md) a szoftverek minőségének (elhelyezés, skálázhatóság, rendelkezésre állás, rugalmasság, kezelhetőség és biztonság) pilléreit tekintik át a hibrid alkalmazások tervezéséhez, üzembe helyezéséhez és üzemeltetéséhez. A kialakítási szempontok segítik a hibrid alkalmazások kialakításának optimalizálását, ami minimalizálja az éles környezetekben felmerülő kihívásokat.

## <a name="prerequisites"></a>Előfeltételek

-   Egy Azure-előfizetés. Ha szükséges, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.

- Egy Azure Stack hub-beli integrált rendszer vagy Azure Stack hub fejlesztői csomag üzembe helyezése.
    - Az Azure Stack hub telepítésével kapcsolatos utasításokért lásd: [az Azure stack hub fejlesztői csomag telepítése](../asdk/asdk-install.md).
    - Az üzembe helyezés utáni automatizálási szkriptek ASDK válassza a következőt: [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) 
    - Előfordulhat, hogy a telepítés elvégzéséhez néhány óra szükséges.

-   [App Service](../operator/azure-stack-app-service-deploy.md) Péter-szolgáltatások üzembe helyezése Azure stack hubhoz.

-   [Hozzon létre egy csomagot/ajánlatokat](../operator/service-plan-offer-subscription-overview.md) a Azure stack hub-környezeten belül.

-   [Bérlői előfizetés létrehozása](../operator/azure-stack-subscribe-plan-provision-vm.md) az Azure stack hub-környezeten belül.

-   Hozzon létre egy webalkalmazást a bérlői előfizetésen belül. Jegyezze fel az új webalkalmazás URL-címét későbbi használatra.

-   Az Azure-folyamatok virtuális gép (VM) üzembe helyezése a bérlői előfizetésen belül.

-   A Windows Server 2016 rendszerű virtuális gépeket .NET 3,5-tel kell megadnia. Ez a virtuális gép az Azure Stack hub bérlői előfizetésében lesz felépítve, mint a privát Build ügynök.

-   A [Windows Server 2016 és az SQL 2017](../operator/azure-stack-add-vm-image.md#add-a-vm-image-as-an-azure-stack-operator-using-the-portal) virtuálisgép-rendszerkép a Azure stack hub piactéren érhető el. Ha ez a rendszerkép nem érhető el, működjön együtt egy Azure Stack hub-kezelővel, és győződjön meg arról, hogy hozzá van adva a környezethez.

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

### <a name="scalability"></a>Skálázhatóság

A többfelhős méretezés kulcsfontosságú összetevője az, hogy a nyilvános és a helyszíni felhőalapú infrastruktúra között azonnali és igény szerinti skálázást lehessen biztosítani, amely konzisztens és megbízható szolgáltatást biztosít.

### <a name="availability"></a>Elérhetőség

Győződjön meg arról, hogy a helyileg telepített alkalmazások magas rendelkezésre állásra vannak konfigurálva a helyszíni hardverkonfiguráció és a szoftverek központi telepítése révén.

### <a name="manageability"></a>Kezelhetőség

A többfelhős megoldás zökkenőmentes felügyeletet és ismerős felületet biztosít a környezetek között. A PowerShell használata a platformok közötti felügyelethez ajánlott.

## <a name="cross-cloud-scaling"></a>Felhőn átívelő méretezés

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Egyéni tartomány beszerzése és a DNS konfigurálása

Frissítse a tartományhoz tartozó DNS-zónafájl fájlját. Az Azure AD ellenőrzi az Egyéni tartománynév tulajdonjogát. Az Azure-ban az Azure/Office 365/External DNS-rekordok [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) használhatók, vagy a DNS-bejegyzést [egy másik DNS-regisztrálónál](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)adja hozzá.

1.  Egyéni tartomány regisztrálása nyilvános regisztrálóval.

2.  Jelentkezzen be a tartomány tartománynév-regisztrálójába. A DNS-frissítések elvégzéséhez egy jóváhagyott rendszergazdára lehet szükség.

3.  Frissítse a tartományhoz tartozó DNS-zónát az Azure AD által biztosított DNS-bejegyzés hozzáadásával. (A DNS-bejegyzés nem befolyásolja a levelek útválasztását vagy a webes üzemeltetési viselkedést.)

### <a name="create-a-default-multi-node-web-app-in-azure-stack-hub"></a>Alapértelmezett több csomópontos Webalkalmazás létrehozása Azure Stack hub-ban

Hibrid folyamatos integráció és folyamatos üzembe helyezés (CI/CD) beállításával webalkalmazásokat telepíthet az Azure-ba és Azure Stack hub-ra, valamint a felhőbe történő autopush-módosításokat.

> [!Note]  
> Azure Stack központ futtatásához (Windows Server és SQL) és a App Service üzembe helyezéshez szükséges megfelelő rendszerképekkel. További információkért tekintse át a App Service dokumentációját, [mielőtt megkezdené a app Serviceét Azure stack hub-on](../operator/azure-stack-app-service-before-you-get-started.md).

### <a name="add-code-to-azure-repos"></a>Kód hozzáadása az Azure Reposhez

Azure-beli adattárak

1. Jelentkezzen be az Azure Reposba egy olyan fiókkal, amely projekt-létrehozási jogokkal rendelkezik az Azure Reposban.

    A hibrid CI/CD az alkalmazás kódjára és az infrastruktúra kódjára is alkalmazható. A saját és a szolgáltatott felhő fejlesztéséhez [Azure Resource Manager sablonokat](https://azure.microsoft.com/resources/templates/) is használhat.

    ![Kapcsolódás egy projekthez az Azure Reposban](media/solution-deployment-guide-cross-cloud-scaling/image1.JPG)

2. **A tárház klónozása** az alapértelmezett webalkalmazás létrehozásával és megnyitásával.

    ![Adattár klónozása az Azure-webalkalmazásban](media/solution-deployment-guide-cross-cloud-scaling/image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Saját tulajdonú webalkalmazások létrehozása a App Services mindkét felhőben

1.  Szerkessze a **webalkalmazás. csproj** fájlt. Válassza a `Runtimeidentifier` lehetőséget, és adja hozzá a `win10-x64` értéket. (Lásd az [önálló központi telepítési](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentációt.) 

    ![Webalkalmazás-projektfájl szerkesztése](media/solution-deployment-guide-cross-cloud-scaling/image3.png)

2.  Az Team Explorer használatával keresse meg a kódot az Azure Reposban.

3.  Győződjön meg róla, hogy az alkalmazás kódja be lett jelölve az Azure Reposban.

## <a name="create-the-build-definition"></a>A Build definíciójának létrehozása

1. Jelentkezzen be az Azure-folyamatokba, és erősítse meg a Build-definíciók létrehozásának lehetőségét.

2. Add **-r win10-x64** kód. Ez a Hozzáadás szükséges a .NET Core-hoz készült önálló telepítés elindításához.

    ![Kód hozzáadása a webalkalmazáshoz](media/solution-deployment-guide-cross-cloud-scaling/image4.png)

3. Futtassa a buildet. A [saját üzemeltetésű üzembe helyezési](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) folyamat az Azure-ban és Azure stack hub-on futó összetevőket teszi közzé.

## <a name="use-an-azure-hosted-agent"></a>Azure-beli üzemeltetett ügynök használata

Az üzemeltetett Build-ügynök használata az Azure-folyamatokban kényelmes megoldás webalkalmazások létrehozására és üzembe helyezésére. A karbantartást és a frissítéseket a Microsoft Azure automatikusan végzi el, így folyamatos és zavartalan fejlesztési ciklust tesz lehetővé.

### <a name="manage-and-configure-the-cd-process"></a>A CD-folyamat kezelése és konfigurálása

Az Azure-folyamatok és az Azure DevOps-szolgáltatások kiválóan konfigurálható és kezelhető folyamatokat biztosítanak több környezet, például fejlesztési, átmeneti, MINŐSÉGBIZTOSÍTÁSi és éles környezetek kiadásához; többek között a jóváhagyások megkövetelése adott fázisokban.

## <a name="create-release-definition"></a>Kiadás definíciójának létrehozása

1.  Kattintson a **plusz** gombra, és adjon hozzá egy új kiadást az VSO **Build és Release** szakaszának **kiadások** lapján.

    ![Kiadási definíció létrehozása](media/solution-deployment-guide-cross-cloud-scaling/image5.png)

2. Alkalmazza a Azure App Service központi telepítési sablont.

   ![Azure App Service központi telepítési sablon alkalmazása](meDia/solution-deployment-guide-cross-cloud-scaling/image6.png)

3. Az összetevő **hozzáadása**területen adja hozzá az Azure Cloud Build alkalmazáshoz tartozó összetevőt.

   ![Összetevő hozzáadása az Azure Cloud buildhez](media/solution-deployment-guide-cross-cloud-scaling/image7.png)

4. A folyamat lapon válassza ki a **fázist, a feladat** hivatkozását, és állítsa be az Azure Cloud Environment értékeit.

   ![Az Azure Cloud Environment értékeinek beállítása](media/solution-deployment-guide-cross-cloud-scaling/image8.png)

5. Adja meg a **környezet nevét** , és válassza ki az Azure-beli Felhőbeli végponthoz tartozó **Azure-előfizetést** .

      ![Azure-előfizetés kiválasztása Azure Felhőbeli végponthoz](media/solution-deployment-guide-cross-cloud-scaling/image9.png)

6. Az **app Service neve**alatt állítsa be a szükséges Azure app Service-nevet.

      ![Az Azure app Service nevének beállítása](media/solution-deployment-guide-cross-cloud-scaling/image10.png)

7. Adja meg a "üzemeltetett VS2017" kifejezést az Azure Cloud üzemeltetett környezet **ügynök-várólistájában** .

      ![Az Azure Cloud üzemeltetett környezet ügynök-várólistájának beállítása](media/solution-deployment-guide-cross-cloud-scaling/image11.png)

8. A telepítés Azure App Service menüben válassza ki a környezet érvényes **csomagját vagy mappáját** . Kattintson **az OK** gombra a **mappa helyének**megadásához.
  
      ![Azure App Service-környezethez tartozó csomag vagy mappa kiválasztása](media/solution-deployment-guide-cross-cloud-scaling/image12.png)

      ![Azure App Service-környezethez tartozó csomag vagy mappa kiválasztása](media/solution-deployment-guide-cross-cloud-scaling/image13.png)

9. Mentse az összes módosítást, és térjen vissza a **kiadási folyamathoz**.

    ![A kiadási folyamat módosításainak mentése](media/solution-deployment-guide-cross-cloud-scaling/image14.png)

10. Adjon hozzá egy új összetevőt, amely kiválasztja az Azure Stack hub alkalmazás buildjét.
    
    ![Új összetevő hozzáadása Azure Stack hub-alkalmazáshoz](media/solution-deployment-guide-cross-cloud-scaling/image15.png)


11. Vegyen fel még egy környezetet a Azure App Service központi telepítés alkalmazásával.
    
    ![Környezet hozzáadása Azure App Service központi telepítéshez](media/solution-deployment-guide-cross-cloud-scaling/image16.png)

12. Nevezze el az új környezetet Azure Stack.
    
    ![Név környezet Azure App Service üzemelő példányban](media/solution-deployment-guide-cross-cloud-scaling/image17.png)

13. Keresse meg a Azure Stack környezetet a **feladat** lapon.
    
    ![Azure Stack környezet](media/solution-deployment-guide-cross-cloud-scaling/image18.png)

14. Válassza ki az Azure Stack-végpont előfizetését.
    
    ![Válassza ki az Azure Stack-végpont előfizetését](media/solution-deployment-guide-cross-cloud-scaling/image19.png)

15. Adja meg a Azure Stack webalkalmazás nevét az App Service neveként.

    ![Azure Stack webalkalmazás nevének beállítása](media/solution-deployment-guide-cross-cloud-scaling/image20.png)

16. Válassza ki a Azure Stack ügynököt.
    
    ![Azure Stack ügynök kiválasztása](media/solution-deployment-guide-cross-cloud-scaling/image21.png)

17. A Azure App Service telepítése szakaszban válassza ki a környezet érvényes **csomagját vagy mappáját** . Kattintson **az OK** gombra a mappa helyének megadásához.

    ![Mappa kiválasztása Azure App Service központi telepítéshez](media/solution-deployment-guide-cross-cloud-scaling/image22.png)

    ![Mappa kiválasztása Azure App Service központi telepítéshez](media/solution-deployment-guide-cross-cloud-scaling/image23.png)

18. A változó lapon adjon hozzá egy `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`nevű változót, állítsa az értékét **igaz**értékre, és a hatókört a Azure Stackra.
    
    ![Változó hozzáadása az Azure-alkalmazások üzembe helyezéséhez](media/solution-deployment-guide-cross-cloud-scaling/image24.png)

19. Válassza ki a **folyamatos** üzembe helyezési trigger ikont mindkét összetevőben, és **engedélyezze a folytatás** üzembe helyezési triggert.
    
    ![Folyamatos üzembe helyezési trigger kiválasztása](media/solution-deployment-guide-cross-cloud-scaling/image25.png)

20. Válassza ki az **üzembe helyezés előtti** feltételek ikont a Azure stack környezetben, és állítsa be a triggert a **kiadás után.**
    
    ![Központi telepítés előtti feltételek kiválasztása](media/solution-deployment-guide-cross-cloud-scaling/image26.png)

21. Mentse az összes módosítást.

> [!Note]  
> Előfordulhat, hogy a feladatok egyes beállításai automatikusan [környezeti változókként](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) vannak definiálva, amikor kiadási definíciót hoz létre egy sablonból. Ezek a beállítások nem módosíthatók a feladat beállításaiban. Ehelyett a szülő környezeti elemet kell kiválasztani a beállítások szerkesztéséhez.

## <a name="publish-to-azure-stack-hub-via-visual-studio"></a>Közzététel Azure Stack hub-on a Visual Studióval

A végpontok létrehozásával a Visual Studio online-(VSTO-) buildek üzembe helyezhetik az Azure-szolgáltatások alkalmazásait Azure Stack hubhoz. Az Azure-folyamatok összekapcsolják a Build ügynököt, amely Azure Stack hubhoz csatlakozik.

1.  Jelentkezzen be a VSTO-be, és navigáljon az Alkalmazásbeállítások lapra.

2.  A **Beállítások**területen válassza a **Biztonság**elemet.

3.  A **vsts-csoportok**területen válassza a **végpont-létrehozók**lehetőséget.

4.  A **tagok** lapon válassza a **Hozzáadás**lehetőséget.

5.  A **felhasználók és csoportok hozzáadása**lapon adja meg a felhasználónevet, és válassza ki a felhasználót a felhasználók listájából.

6.  Válassza a **módosítások mentése**lehetőséget.

7.  A **vsts-csoportok** listában válassza ki a **végponti rendszergazdák**elemet.

8.  A **tagok** lapon válassza a **Hozzáadás**lehetőséget.

9.  A **felhasználók és csoportok hozzáadása**lapon adja meg a felhasználónevet, és válassza ki a felhasználót a felhasználók listájából.

10. Válassza a **módosítások mentése**lehetőséget.

Most, hogy a végponti információ már létezik, az Azure-folyamatok Azure Stack hub-kapcsolatok használatra készen állnak. Az Azure Stack hub Build ügynöke beolvassa az Azure-folyamatok utasításait, majd az ügynök az Azure Stack hub-vel folytatott kommunikációhoz végponti információkat továbbít.

## <a name="develop-the-application-build"></a>Az alkalmazás létrehozásának fejlesztése

> [!Note]  
> Azure Stack központ futtatásához (Windows Server és SQL) és a App Service üzembe helyezéshez szükséges megfelelő rendszerképekkel. További információkért tekintse át a App Service dokumentációját, [mielőtt megkezdené a app Serviceét Azure stack hub-on](../operator/azure-stack-app-service-before-you-get-started.md).

A felhőben való üzembe helyezéshez használjon [Azure Resource Manager sablonokat](https://azure.microsoft.com/resources/templates/) , például a webalkalmazás kódját az Azure reposből.

### <a name="add-code-to-an-azure-repos-project"></a>Kód hozzáadása egy Azure Repos-projekthez

1.  Jelentkezzen be az Azure Reposba egy olyan fiókkal, amely Azure Stack hub-beli projekt-létrehozási jogokkal rendelkezik. A következő képernyőfelvétel azt mutatja be, hogyan lehet csatlakozni a HybridCICD-projekthez.

2.  **A tárház klónozása** az alapértelmezett webalkalmazás létrehozásával és megnyitásával.

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Saját tulajdonú webalkalmazások létrehozása a App Services mindkét felhőben

1.  Szerkessze a **webalkalmazás. csproj** fájlt: válassza a `Runtimeidentifier` lehetőséget, majd adja hozzá a `win10-x64` elemet. További információ: [önálló telepítési](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentáció.

2.  Az Team Explorer segítségével keresse meg a kódot az Azure Reposban.

3.  Győződjön meg róla, hogy az alkalmazás kódja be lett jelölve az Azure Repos-ben.

### <a name="create-the-build-definition"></a>A Build definíciójának létrehozása

1.  Jelentkezzen be az Azure-folyamatokba egy olyan fiókkal, amely létrehoz egy Build-definíciót.

2.  Navigáljon a projekthez tartozó **Webalkalmazás létrehozása** lapra.

3.  Az **argumentumokban**adja hozzá az **-r win10-x64** kódot. Ez a kiegészítés szükséges a .NET Core-ban lévő, önálló telepítés elindításához.

4.  Futtassa a buildet. A [saját üzemeltetésű üzembe helyezési](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) folyamat olyan összetevőket tesz közzé, amelyek az Azure-ban és a Azure stack hub-ban is futtathatók.

#### <a name="use-an-azure-hosted-build-agent"></a>Azure-beli üzemeltetett Build-ügynök használata

Az üzemeltetett Build-ügynök használata az Azure-folyamatokban kényelmes megoldás webalkalmazások létrehozására és üzembe helyezésére. A karbantartást és a frissítéseket a Microsoft Azure automatikusan végzi el, így folyamatos és zavartalan fejlesztési ciklust tesz lehetővé.

### <a name="configure-the-continuous-deployment-cd-process"></a>A folyamatos üzembe helyezés (CD) folyamatának konfigurálása

Az Azure-folyamatok és az Azure DevOps-szolgáltatások kiválóan konfigurálható és kezelhető folyamatot biztosítanak több környezet, például a fejlesztés, az előkészítés, a minőségbiztosítás (QA) és a termelés számára. Ez a folyamat a jóváhagyások megkövetelését is magában foglalhatja az alkalmazás életciklusának adott szakaszaiban.

#### <a name="create-release-definition"></a>Kiadás definíciójának létrehozása

A kiadás definíciójának létrehozása az alkalmazás-létrehozási folyamat utolsó lépése. Ez a kiadási definíció a kiadás létrehozásához és a buildek üzembe helyezéséhez használható.

1.  Jelentkezzen be az Azure-folyamatokba, és navigáljon a projekt **létrehozásához és felszabadításához** .

2.  A **kiadások** lapon válassza a **[+]** elemet, majd válassza a **Létrehozás kiadás definíciója**lehetőséget.

3.  A **sablon kiválasztása**lapon válassza **Azure app Service központi telepítés**lehetőséget, majd kattintson az **alkalmaz**gombra.

4.  Az összetevő **hozzáadása**lapon a **forrás (összeállítás definíciója)** területen válassza ki az Azure Cloud Build alkalmazást.

5.  A **folyamat** lapon válassza az **1 fázis**, **1 feladat** hivatkozás lehetőséget a **környezeti feladatok megtekintéséhez**.

6.  A **feladatok** lapon adja meg az Azure-t a **környezet neveként** , majd válassza a AzureCloud Traders-web EP elemet az **Azure-előfizetések** listájából.

7.  Adja meg az **Azure app Service nevét**, amely a következő képernyőfelvételen `northwindtraders`.

8.  Az ügynök fázisa esetében az **ügynök-várólista** listából válassza az **üzemeltetett VS2017** lehetőséget.

9.  A **telepítés Azure app Service**területen válassza ki a környezet érvényes **csomagját vagy mappáját** .

10. A **fájl vagy mappa kiválasztása**lapon kattintson **az OK** gombra a **helyhez**.

11. Mentse az összes módosítást, és térjen vissza a **folyamathoz**.

12. A **folyamat** lapon válassza az összetevő **hozzáadása**lehetőséget, majd válassza ki a **NorthwindCloud Traders-hajót** a **forrás (összeállítás definíciója)** listából.

13. A **sablon kiválasztása**lapon adjon hozzá egy másik környezetet. Válassza a **Azure app Service központi telepítés** lehetőséget, majd kattintson az **alkalmaz**gombra.

14. Adja meg a `Azure Stack Hub` **nevet a környezet neveként**.

15. A **feladatok** lapon keresse meg és válassza ki Azure stack hubot.

16. Az **Azure-előfizetések** listájában válassza a **AzureStack Traders-HAJÓval kapcsolatos EP** elemet az Azure stack hub-végponthoz.

17. Adja meg az Azure Stack hub-webalkalmazás nevét az **app Service neveként**.

18. Az **ügynök kiválasztása**területen válassza ki az **AzureStack-b Douglas Fir** elemet az **ügynök-várólista** listából.

19. **Azure app Service üzembe helyezéséhez**válassza ki a környezet érvényes **csomagját vagy mappáját** . A **fájl vagy mappa kiválasztása**lapon válassza az **OK** lehetőséget a mappa **helyeként**.

20. A **változó** lapon keresse meg `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`nevű változót. Állítsa a változó értékét **igaz**értékre, és állítsa a hatókörét **Azure stack hubhoz**.

21. A **folyamat** lapon válassza a **folyamatos üzembe helyezés trigger** ikont a NorthwindCloud Traders-web összetevőhöz, és állítsa a **folyamatos üzembe helyezési triggert** **engedélyezve**értékre. Tegye ugyanezt a **NorthwindCloud-kereskedők** számára.

22. Az Azure Stack hub-környezethez válassza az indítás **előtti feltételek** ikont az aktiválás a **kiadás után**beállításnál.

23. Mentse az összes módosítást.

> [!Note]  
> A kiadási feladatok egyes beállításai automatikusan [környezeti változókként](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) vannak definiálva a kiadási definíciók sablonból való létrehozásakor. Ezek a beállítások nem módosíthatók a feladat beállításaiban, de módosíthatók a szülő környezeti elemekben.

## <a name="create-a-release"></a>Kiadás létrehozása

1.  A **folyamat** lapon nyissa meg a **kiadási** listát, és válassza a **kiadás létrehozása**lehetőséget.

2.  Adja meg a kiadás leírását, ellenőrizze, hogy a megfelelő összetevők van-e kiválasztva, majd válassza a **Létrehozás**lehetőséget. Néhány pillanat múlva megjelenik egy szalagcím, amely azt jelzi, hogy az új kiadás létrejött, és a kiadás neve hivatkozásként jelenik meg. Válassza ki a hivatkozást, és tekintse meg a kiadás összegzése lapot.

3.  A kiadás összegzése lap a kiadás részleteit jeleníti meg. A "Release-2" képernyőn az alábbi képernyőfelvételen a **környezetek** szakasz a "folyamatban" állapotú Azure **telepítési állapotát** jeleníti meg, az Azure stack hub állapota pedig "sikeres". Ha az Azure-környezet központi telepítési állapota "sikeres" állapotúra változik, megjelenik egy szalagcím, amely azt jelzi, hogy a kiadás készen áll a jóváhagyásra. Ha egy üzemelő példány függőben van vagy sikertelen, kék **(i)** információs ikon jelenik meg. Vigye az egérmutatót a ikon fölé egy előugró ablak megjelenítéséhez, amely a késés vagy a hiba okát tartalmazza.

4.  Más nézetek, például a kiadások listája, egy ikon is megjelenik, amely jelzi, hogy a jóváhagyás függőben van. Az ikon előugró ablaka a környezet nevét és a telepítéssel kapcsolatos további részleteket jeleníti meg. A rendszergazdák egyszerűen megtekinthetik a kiadások általános előrehaladását, és megtekinthetik, hogy mely kiadások várnak jóváhagyásra.

## <a name="monitor-and-track-deployments"></a>Központi telepítések figyelése és nyomon követése

1.  A **Release-2** Összegzés lapon válassza a **naplók**lehetőséget. Az üzembe helyezés során ez az oldal az ügynökből származó élő naplót jeleníti meg. A bal oldali ablaktábla megjeleníti az egyes műveletek állapotát a központi telepítésben az egyes környezetekben.

2.  A **művelet** oszlopban jelölje ki a személy ikont az üzembe helyezés előtti vagy a telepítés utáni jóváhagyáshoz, és tekintse meg, hogy ki hagyta jóvá (vagy utasította el) a központi telepítést, illetve hogy milyen üzenetet adott meg.

3.  Az üzembe helyezés befejeződése után a teljes naplófájl megjelenik a jobb oldali ablaktáblán. A bal oldali ablaktábla bármelyik **lépését** kiválasztva megtekintheti a naplófájlt egy adott lépéshez, például a **feladatok inicializálásához**. Az egyes naplók megtekintésének képessége megkönnyíti a teljes telepítés részeinek nyomon követését és hibakeresését. **Mentse** a naplófájlt egy lépéshez, vagy **töltse le az összes naplót zip**-fájlként.

4.  Nyissa meg az **Összefoglalás** lapot a kiadással kapcsolatos általános információk megtekintéséhez. Ez a nézet a kiépítés, a környezetek üzembe helyezési állapota és a kiadással kapcsolatos egyéb információk részleteit jeleníti meg.

5.  Válasszon egy környezeti hivatkozást (**Azure** vagy **Azure stack hub**) a meglévő és a függőben lévő központi telepítésekkel kapcsolatos információk megtekintéséhez egy adott környezetben. Ezekkel a nézetekkel gyorsan ellenőrizheti, hogy ugyanazon Build mindkét környezetbe lett-e telepítve.

6.  Nyissa meg az **üzembe helyezett éles alkalmazást** egy böngészőben. Az Azure App Services webhelyén például nyissa meg az URL-címet `https://[your-app-name\].azurewebsites.net`.

**Az Azure és a Azure Stack hub integrációja méretezhető, többfelhős megoldást kínál**

A rugalmas és robusztus többfelhős szolgáltatás adatbiztonságot, biztonsági mentést és redundanciát, egységes és gyors rendelkezésre állást, méretezhető tárolást és elosztást, valamint geo-kompatibilis útválasztást biztosít. Ez a manuálisan aktivált folyamat megbízható és hatékony terhelést biztosít a szolgáltatott webalkalmazások között, és azonnal elérhetővé teszi a kritikus fontosságú adatmennyiséget.

## <a name="next-steps"></a>Következő lépések
- Az Azure Cloud Patterns szolgáltatással kapcsolatos további információkért lásd: [Felhőbeli tervezési minták](https://docs.microsoft.com/azure/architecture/patterns).
