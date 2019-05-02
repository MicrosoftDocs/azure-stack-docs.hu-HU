---
title: A felhőbe irányuló méretezési megoldások létrehozása az Azure-ral |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre a több felhőre kiterjedő méretezési megoldások az Azure-ral.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: a82912b3acf18ce3be206e33da752bf8a03a4858
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64303672"
---
# <a name="tutorial-create-cross-cloud-scaling-solutions-with-azure"></a>Oktatóanyag: A felhőbe irányuló méretezési megoldások létrehozása az Azure-ral

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ismerje meg, hogyan hozhat létre a több felhőre kiterjedő megoldásokat biztosít egy manuálisan aktivált folyamatot az Azure Stackkel való váltás futtatott webalkalmazás, egy Azure-ban üzemeltetett web Apps és az automatikus skálázást annak biztosítása, rugalmas és skálázható felhőalapú segédprogram terhelés alatt, a traffic manager-n keresztül.

Ezzel a mintával a bérlő nem lehet az alkalmazás futtatható a nyilvános felhőben. Azonban nem lehet az alkalmazás iránti kereslet kiugrások kezelésére a helyszíni környezetben szükséges kapacitást fenntartásához a vállalati gazdaságosan megvalósítható. A bérlő is igénybe vehet a nyilvános felhő rugalmasságát használata a saját helyszíni megoldás.

Ebben az oktatóanyagban egy mintául szolgáló környezet fog létrehozni:

> [!div class="checklist"]
> - Több csomópontos webalkalmazás létrehozása.
> - Konfigurálja és kezeli a folyamatos üzembe helyezés (CD) folyamatot.
> - A webalkalmazás közzététele az Azure Stackhez.
> - Hozzon létre egy kiadást.
> - Ismerje meg, figyeléséhez és nyomon követéséhez az üzemelő példányok.

> [!Tip]  
> ![hibrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> A Microsoft Azure Stack az Azure bővítménye. Az Azure Stack hatékonyságával és innovációjával a felhőalapú számítástechnika a helyszíni környezetben, és a egyetlen olyan hibrid felhő, amely lehetővé teszi, hogy létrehozása és üzembe helyezése bárhol a hibrid alkalmazások engedélyezésével biztosítható.  
> 
> A tanulmány [hibrid alkalmazások kapcsolatos kialakítási szempontok](https://aka.ms/hybrid-cloud-applications-pillars) szoftverminőség alappillérei (elhelyezését, a méretezhetőség, rugalmasság, kezelhetőségi és biztonsági) felülvizsgálatai kialakítása, üzembe helyezése és működtetése hibrid alkalmazások. A kialakítási szempontokat segítséget nyújt a hibrid alkalmazások kialakítását, minimálisra csökkentik az éles környezetben kihívások optimalizálása.

## <a name="prerequisites"></a>Előfeltételek

-   Egy Azure-előfizetés. Szükség esetén hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) megkezdése előtt.

- Az Azure Stackkel integrált rendszereknél vagy az Azure Stack fejlesztői készletének telepítése.
    - Látja a történő telepítése, Azure Stack [az Azure Stack fejlesztői készletének telepítése](../asdk/asdk-install.md).
    - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) Ez a telepítés néhány órát lehet szükség.

-   Üzembe helyezése [App Service-ben](../operator/azure-stack-app-service-deploy.md) PaaS-szolgáltatások az Azure Stackhez.

-   [Csomag vagy ajánlat létrehozásához](../operator/azure-stack-plan-offer-quota-overview.md) belül az Azure Stack-környezet.

-   [Hozzon létre a bérlő előfizetés](../operator/azure-stack-subscribe-plan-provision-vm.md) belül az Azure Stack-környezet.

-   Hozzon létre egy webalkalmazást a bérlő az előfizetésen belül. Jegyezze fel az új webes alkalmazás URL-CÍMÉT a későbbi használatra.

-   A bérlői előfizetéshez tartozó Azure folyamatok virtuális gépet üzembe helyezni.

-   A Windows Server 2016 virtuális gép a .NET 3.5 szükséges. Ez a virtuális gép a bérlő előfizetés az Azure Stacken, a saját build ügynök lesz felépítve.

-   [A Windows Server 2016 SQL 2017 Virtuálisgép-lemezkép](../operator/azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal) érhető el az Azure Stack piactéren. Ha a kép nem érhető el, ahol az Azure Stack-operátorokról annak érdekében, hogy felveszi a környezetbe.

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

### <a name="scalability-considerations"></a>Méretezési szempontok

A kulcsfontosságú alkotóeleme a több felhőre kiterjedő méretezés lehetővé teszi, hogy azonnali, igény szerinti méretezés a nyilvános között és a helyszíni felhőinfrastruktúrák, az igény szerinti által előírt, amelyek bizonyítják következetes és megbízható szolgáltatás.

### <a name="availability-considerations"></a>Rendelkezésre állási szempontok

Győződjön meg arról helyileg telepített alkalmazások magas rendelkezésre állású keresztül a helyszíni hardver konfiguráció és a szoftverek központi telepítési vannak konfigurálva.

### <a name="manageability-considerations"></a>Felügyeleti szempontok

A több felhőre kiterjedő megoldás biztosítja, gördülékeny kezelése és ismerős felületet környezetek között. Platformok közötti felügyeleti PowerShell használata ajánlott.

## <a name="cross-cloud-scaling"></a>A felhőbe irányuló méretezése

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Szerezze be az egyéni tartomány és a DNS konfigurálása

Frissítse a tartomány DNS-zónafájljában. Az Azure AD ellenőrzi, hogy az egyéni tartománynév tulajdonjogát. Használja [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) az Azure vagy Office 365 és a külső DNS-rekordok Azure-ban, vagy adja hozzá a DNS-bejegyzést, [egy másik DNS-regisztráló webhelyén](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1.  Egyéni tartomány regisztrálása egy nyilvános regisztrálójánál.

2.  Jelentkezzen be a tartomány tartománynév-regisztrálójába. Jóváhagyott rendszergazda fiókdíjat, hogy a DNS-frissítések. 

3.  A tartomány DNS-zónafájljában frissítése az Azure AD által biztosított DNS-bejegyzés hozzáadásával. (A DNS-bejegyzés nem érinti levélkezelési útválasztást vagy a webes üzemeltetési viselkedéseket.) 

### <a name="create-a-default-multi-node-web-app-in-azure-stack"></a>A több csomópontos webalkalmazás létrehozása az Azure Stackben

Webes alkalmazás üzembe helyezése az Azure és az Azure Stack, hibrid folyamatos integráció és készregyártás (CI/CD) beállítása és automatikus mindkét felhőben, küldje el a módosításokat.

> [!Note]  
> Az Azure Stack megfelelő képekkel hírcsatorna-futtassa (Windows Server és SQL) és az App Service-környezet szükség. Tekintse át az App Service – dokumentáció "[az App Service-ben az Azure Stack használatának megkezdése előtt](../operator/azure-stack-app-service-before-you-get-started.md)" szakaszban az Azure Stack-operátorokról.

### <a name="add-code-to-azure-repos"></a>Adja hozzá a kódot az Azure-Adattárakkal

Azure-beli adattárak

1. Jelentkezzen be Azure-kódtárak egy olyan fiókkal, amely az Azure-kódtárak a projekt létrehozása jogosultságokkal rendelkezik.

    CI/CD hibrid alkalmazás kódja és az infrastruktúra kódjának alkalmazhatja. Használat [Azure Resource Manager-sablonok](https://azure.microsoft.com/resources/templates/) mindkét magán- és üzemeltetett felhőalapú fejlesztéshez.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image1.JPG)

2. **A tárház klónozása** létrehozásával és az alapértelmezett webes alkalmazás megnyitásával.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Önálló webes alkalmazás üzembe helyezése az App Services létrehozása az mindkét felhőben

1.  Szerkessze a **WebApplication.csproj** fájlt. Válassza ki **Runtimeidentifier** , és adja hozzá **win10-x64**. (Lásd: [Self-contained telepítési](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentációja.) 

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image3.png)

2.  Ellenőrizze, hogy az Azure tárházra Team Explorer a kódot.

3.  Győződjön meg arról, hogy az alkalmazáskódban az Azure-Adattárakkal ellenőrizve.

## <a name="create-the-build-definition"></a>A build definíció létrehozása

1. Jelentkezzen be Azure-folyamatok létrehozására megerősítéséhez definíciókat hozhat létre.

2. Adjon hozzá **- r win10-x64** kódot. Ez azért szükséges, egy önálló telepítés a .NET Core használatával aktiválásához.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image4.png)

3. Futtassa a build. A [önálló telepítés build](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) folyamat közzéteszi az összetevők, amelyek futhatnak az Azure és az Azure Stackben.

## <a name="use-an-azure-hosted-agent"></a>Azure-beli használatra üzemeltetett ügynök

Egy Azure-folyamatokban üzemeltetett ügynök használata kényelmes beállítás készíthet és helyezhet üzembe webalkalmazásokat. A Microsoft Azure-folyamatos, megszakítás nélküli fejlesztési, tesztelési és üzembe helyezés engedélyezése automatikusan végzi a karbantartása és frissítései.

### <a name="manage-and-configure-the-cd-process"></a>Kezelheti és konfigurálhatja a CD-folyamat

Az Azure folyamatok és Azure DevOps-kiszolgáló biztosít hatékonyan konfigurálható és kezelhető folyamat kiadásokban több környezethez például fejlesztési, előkészítési, QA és éles környezetek; amely jóváhagyások adott szintjén is.

## <a name="create-release-definition"></a>Kiadási definíció létrehozása

![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image5.png)

1.  Válassza ki a **plusz** gombra kattintva adhat hozzá egy új kiadása alatt a **kiadások lapra** VSO, a Build és kiadás oldalon.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image6.png)

2. A alkalmazni az Azure App Service központi telepítési sablont.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image7.png)

3. Az Add-összetevőt adja meg az Azure Cloud build alkalmazás összetevő.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image8.png)

4. A folyamat fülre, válassza a **fázisba, a feladat** a környezet hivatkozásra, és állítsa be az Azure-felhő környezet értékeket.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image9.png)

5. Állítsa be a **környezet neve** , és válassza ki az Azure **előfizetés** az Azure Felhőbeli végpont számára.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image10.png)

6. A környezet neve, állítsa be a szükséges **az Azure app service neve**.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image11.png)

7. Adja meg **Hosted VS2017** alatt fronta Agenta Azure-felhőben üzemeltetett környezetben.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image12.png)

8. Az Azure App Service üzembe helyezése menüben válassza a érvényes **csomag vagy a mappa** a környezethez. Válassza ki **OK** való **mappába**.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image13.png)

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image14.png)

9. Mentse az összes módosítást, és térjen vissza a **kibocsátási folyamatok**.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image15.png)

10. Adjon hozzá egy új összetevő, a build, az Azure Stack-alkalmazás kiválasztása.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image16.png)

11. Adjon hozzá egy további környezet alkalmazása az Azure App Service üzembe helyezéséhez.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image17.png)

12. Az Azure Stack új környezet nevét.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image18.png)

13. Keresse meg az Azure Stack-környezet alapján **feladat** fülre.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image19.png)

14. Válassza ki az előfizetést az Azure Stack-végpont.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image20.png)

15. Az Azure Stack webalkalmazás nevét állítja be az App service nevét.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image21.png)

16. Válassza ki az Azure Stack-ügynök.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image22.png)

17. Az üzembe helyezése az Azure App Service szakaszban válassza a érvényes **csomag vagy a mappa** a környezethez. Válassza ki **OK** mappába.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image23.png)

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image24.png)

18. Változó lapján nevű változó hozzáadása `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, mint az értékét állítsa **igaz**, és az Azure Stackhez.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image25.png)

19. Válassza ki a **folyamatos** üzembe helyezési eseményindító ikonra az összetevők és a engedélyezése a **folytatja** készregyártás eseményindítója.

    ![Helyettesítő szöveg](media/azure-stack-solution-cloud-burst/image26.png)

20. Válassza ki a **központi telepítés előtti** feltételek ikonra az Azure Stack-környezetben, és állítsa az eseményindító **kiadás után.**

21. Mentse az összes módosítást.

> [!Note]  
> A feladatok egyes beállítások előfordulhat, hogy automatikusan meghatározott [környezeti változók](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) kiadási definíció a sablon létrehozásakor. Ezek a beállítások nem módosíthatók a tevékenység beállításait; Ehelyett a szülő környezet típusú elem szerkesztése ezek a beállítások ki kell választani

## <a name="publish-to-azure-stack-via-visual-studio"></a>Az Azure Stack használatával a Visual Studio közzététele

Végpontok létrehozása a Visual Studio online-hoz (VSTO) build helyezzen üzembe Azure Service-alkalmazások az Azure Stackhez. Az Azure folyamatok csatlakozik a fordító-ügynökhöz, amely csatlakoztatja az Azure Stackhez.

1.  Jelentkezzen be a VSTO, és nyissa meg az alkalmazás beállítások oldalát.

2.  A **beállítások**válassza **biztonsági**.

3.  A **VSTS csoportok**válassza **végpont alkotói**.

4.  Az a **tagok** lapon jelölje be **Hozzáadás**.

5.  A **felhasználók és csoportok hozzáadása**, írjon be egy felhasználónevet, és válassza ki, hogy a felhasználó a felhasználók listájából.

6.  Válassza ki **módosítások mentése**.

7.  Az a **VSTS csoportok** listáról válassza ki **végpont rendszergazdák**.

8.  Az a **tagok** lapon jelölje be **Hozzáadás**.

9.  A **felhasználók és csoportok hozzáadása**, írjon be egy felhasználónevet, és válassza ki, hogy a felhasználó a felhasználók listájából.

10. Válassza ki **módosítások mentése**.

Most, hogy létezik a végpont adatait, az Azure Stack-kapcsolat Azure folyamatok készen áll a használatra. A fordító-ügynökhöz az Azure Stackben utasításokat olvas be az Azure-folyamatokat, és ezután az ügynök közvetíti a kommunikációt az Azure Stack-végpontjának információit.

## <a name="develop-the-application-build"></a>Fejlesztés a sestavení aplikace

> [!Note]  
> Az Azure Stack megfelelő képekkel hírcsatorna-futtassa (Windows Server és SQL) és az App Service-környezet szükség. Tekintse át az App Service – dokumentáció "[az App Service-ben az Azure Stack használatának megkezdése előtt](../operator/azure-stack-app-service-before-you-get-started.md)" szakaszban az Azure Stack-operátorokról.

Használat [Azure Resource Manager-sablonokat, mint webes](https://azure.microsoft.com/resources/templates/) mindkét felhőben való üzembe helyezéséhez az Azure-Adattárakkal kódját.

### <a name="add-code-to-an-azure-repos-project"></a>Adja hozzá a kódot egy Azure-Adattárakkal projekthez

1.  Jelentkezzen be Azure-kódtárak egy olyan fiókkal rendelkező projekt létrehozása az Azure Stacken. A következő képernyőfelvétel bemutatja, hogyan kapcsolódjon a HybridCICD.

2.  **A tárház klónozása** létrehozásával és az alapértelmezett webes alkalmazás megnyitásával.

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Önálló webes alkalmazás üzembe helyezése az App Services létrehozása az mindkét felhőben

1.  Szerkessze a **WebApplication.csproj** fájlt: Válassza ki **Runtimeidentifier** majd adja hozzá a win10-x64. További információkért lásd: [önálló telepítés](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentációját.

2.  Ellenőrizze a kódot az Azure-kódtárak a Team Explorer használatával.

3.  Győződjön meg arról, hogy az alkalmazáskódban az Azure-Adattárakkal lett érvényesítve.

### <a name="create-the-build-definition"></a>A build definíció létrehozása

1.  Jelentkezzen be egy olyan fiókkal, amely a builddefiníció hozhat létre Azure folyamatok.

2.  Keresse meg a **webes alkalmazás készítése** a projekt lapját.

3.  A **argumentumok**, adjon hozzá **- r win10-x64** kódot. Ez egy önálló telepítés a .NET Core használatával aktiválásához szükséges.

4.  Futtassa a build. A [önálló telepítés build](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) folyamat közzéteszi az összetevők, amelyek futhatnak az Azure és az Azure Stackben.

#### <a name="use-an-azure-hosted-build-agent"></a>Azure-beli használatra üzemeltetett fordító-ügynökhöz

Az Azure-folyamatokban üzemeltetett build ügynök használatával beállítás kényelmes és webes alkalmazások üzembe. Ügynök karbantartása és frissítései automatikusan végzi a Microsoft Azure, amely lehetővé teszi a folyamatos és zavartalan fejlesztési ciklus.

### <a name="configure-the-continuous-deployment-cd-process"></a>Konfigurálja a folyamatos készregyártás (CD) folyamatot

Az Azure folyamatok és az Azure DevOps-kiszolgáló hatékonyan konfigurálható és kezelhető folyamat több, például fejlesztési, átmeneti, minőségbiztosítási (Tesztelés) és éles környezetek számára adja meg. Ez a folyamat tartalmazhat jóváhagyásokat igénylő az alkalmazás életciklusát az adott szakaszában.

#### <a name="create-release-definition"></a>Kiadási definíció létrehozása

Kiadási definíció létrehozása az utolsó lépés az alkalmazás létrehozása folyamatban. A kiadási definíció kiadás létrehozása és üzembe helyezése a build szolgál.

1.  Jelentkezzen be Azure-folyamatokat, és navigáljon a **készítése és kiadása** a projekthez.

2.  Az a **kiadásokban** lapon jelölje be **[+]** majd válasszon **kiadási definíció létrehozása**.

3.  A **válasszon ki egy sablont**, válassza a **Azure App Service üzembe helyezési**, majd válassza ki **alkalmaz**.

4.  A **Hozzáadás összetevő**, az a **forrás (builddefiníció)** válassza ki az Azure Cloud hozhat létre alkalmazást.

5.  Az a **folyamat** lapon jelölje be a **1. fázis**, **1 feladat** mutató hivatkozás **környezettel kapcsolatos feladatok megtekintéséhez**.

6.  Az a **feladatok** lapra, adja meg az Azure-t a **környezet neve** , és válassza ki az AzureCloud Traders-webalkalmazás EP a **Azure-előfizetés** listája.

7.  Adja meg a **az Azure app service neve**, amely `northwindtraders` következő képernyőfelvételen.

8.  Válassza ki az ügynök fázis **Hosted VS2017** a a **fronta Agenta** listája.

9.  A **üzembe helyezése az Azure App Service**, válassza ki a érvényes **csomag vagy a mappa** a környezethez.

10. A **válassza ki a fájl vagy mappa**válassza **OK** való **hely**.

11. Mentse az összes módosítást, és térjen vissza a **folyamat**.

12. A a **folyamat** lapon jelölje be **Hozzáadás összetevő**, és válassza a **NorthwindCloud Traders-hajó** a a **Source (Build definíció)** listája.

13. A **válasszon ki egy sablont**, adjon hozzá egy másik környezetre. Válasszon **Azure App Service üzembe helyezési** majd **alkalmaz**.

14. Adja meg `Azure Stack` , a **környezetnevet**.

15. Az a **feladatok** lapon keresse meg és válassza ki az Azure Stack.

16. Az a **Azure-előfizetés** listáról válassza ki **AzureStack Traders-hajó EP** az Azure Stack-végpont.

17. Adja meg az Azure Stack webes alkalmazás nevét, a **App service neve**.

18. Alatt **kijelölése**, válasszon **AzureStack -b Douglas részéhez** származó a **fronta Agenta** listája.

19. A **üzembe helyezése az Azure App Service**, válassza ki a érvényes **csomag vagy a mappa** a környezethez. A **fájl vagy mappa kiválasztása**válassza **OK** mappa **hely**.

20. Az a **változó** lapra, keresse meg a következő változót `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`. A változó értékét állítsa **igaz**, és állítsa annak hatókörét **Azure Stack**.

21. Az a **folyamat** lapon jelölje be a **a folyamatos készregyártás eseményindítója** ikonjára a NorthwindCloud Traders-webalkalmazás-összetevő és a készlet a **a folyamatos készregyártás eseményindítója** , **Engedélyezve**. Végezze el ugyanezt a műveletet a **NorthwindCloud Traders-hajó** összetevő.

22. Az Azure Stack-környezet, válassza a **központi telepítés előtti feltételek** ikon beállítani az eseményindítót **kiadás után**.

23. Mentse az összes módosítást.

> [!Note]  
> A feladatkiadási tevékenységeket egyes beállítások automatikusan vannak meghatározva [környezeti változók](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) kiadási definíció a sablon létrehozásakor. Ezeket a beállításokat nem lehet módosítani a tevékenység beállításait, de a szülőelemektől környezet módosíthatók.

## <a name="create-a-release"></a>Hozzon létre egy kiadás

1.  Az a **folyamat** lap meg van nyitva a **kiadási** listában, és válassza a **kiadás létrehozása**.

2.  Adjon meg egy leírást a kiadás, ellenőrizze, hogy, hogy a helyes összetevők vannak-e jelölve, és válassza **létrehozás**. Néhány pillanat múlva egy szalagcím jelenik meg, amely azt jelzi, hogy az új kiadásban lett létrehozva, és a kiadási név hivatkozásként jelenik meg. Válassza ki a hivatkozásra kattintva megtekintheti a kiadási összegző lapja.

3.  A kiadási összefoglaló lapja a kiadással kapcsolatos részleteket jeleníti meg. "Release-2", az alábbi képernyőfelvételen a **környezetek** szakasz látható a **központi telepítési állapot** esetében az Azure-t a "Folyamatban", és az Azure Stack állapota "sikeres". Ha az Azure-környezetre vonatkozó üzembe helyezési állapotra vált, "SUCCEEDED", egy szalagcím jelenik meg arról, hogy a kiadás nem áll készen a jóváhagyásra. Amikor a központi telepítés függőben, vagy sikertelen volt, a kék **(i)** információs ikon jelenik meg. A kurzort a az ikonra kattintva megtekintheti a egy előugró ablak, amely tartalmazza a késleltetés vagy a hiba okát.

4.  Más nézetekhez, például a listát a kiadások, is megjelenített ikon jelzi, a jóváhagyási folyamatban. Erre az ikonra az előugró a környezet nevét, és üzembe helyezésével kapcsolatos további részleteket jeleníti meg. Könnyebbé vált a rendszergazda, lásd: a kiadások és hogy mely kiadásai jóváhagyásra várnak összesített állapotát.

## <a name="monitor-and-track-deployments"></a>A figyelő és nyomon követésére központi telepítések

1.  Az a **Release-2** összesítő lapon jelölje be **naplók**. A telepítés során ezen a lapon látható az élő napló az ügynöktől. A bal oldali panelen az egyes környezetekhez a központi telepítésben lévő egyes műveletek állapotát jeleníti meg.

2.  Válasszon egy személy ikonra a **művelet** oszlop ki a központi telepítés jóváhagyása (vagy elutasítása), és azokat a megadott üzenet központi telepítés előtti vagy utáni jóváhagyásra.

3.  Az üzembe helyezés befejezését követően a teljes log fájl a jobb oldali ablaktáblán jelenik meg. Válassza ki bármelyik **lépés** a bal oldali panelen, a naplófájl az egyetlen lépésben, például hogy **feladat inicializálása**. Egyéni naplók megtekinthetik megkönnyíti a követése és hibakeresése az általános üzembe helyezési részeit. **Mentés** egy lépést, a naplófájl vagy **az összes napló letöltése zip-fájlként**.

4.  Nyissa meg a **összefoglalás** lapján megtekintheti a kiadással kapcsolatos általános információkat. Ez a nézet a build, a környezetekben, telepítve van, üzembe helyezési állapota és egyéb adatainak a kiadása kapcsolatos részleteket jeleníti meg.

5.  Jelöljön ki egy környezetet hivatkozást (**Azure** vagy **Azure Stack**) és a egy adott környezetben való központi telepítés meglévő kapcsolatos információk megtekintéséhez. Ezek a nézetek gyorsan győződjön meg arról, hogy az azonos buildjét mindkét környezetben telepített használja.

6.  Nyissa meg a **éles-WebApp üzembe helyezését** a böngészőben. Ha például az Azure App Services-webhely URL-címre [https://[saját-alkalmazás-neve\]. azurewebsites.net](https:// [your-app-name].azurewebsites.net).

**Integráció az Azure és az Azure Stack több felhőre kiterjedő méretezhető megoldást kínál**

Rugalmas és hatékony több felhőalapú szolgáltatás biztosít az adatbiztonságot, vissza felfelé és a redundancia, egységes és gyors rendelkezésre állási, méretezhető tárolás és a terjesztési és geo-kompatibilis útválasztó. A manuálisan aktivált folyamatot üzemeltetett Web apps, rendkívül fontos adatok azonnali rendelkezésre állás biztosítása közötti váltáskor, megbízható és hatékony terhelés biztosítja. 

## <a name="next-steps"></a>További lépések
- Az Azure-minták Felhőkhöz kapcsolatos további információkért lásd: [tervezési minták Felhőkhöz](https://docs.microsoft.com/azure/architecture/patterns).
