---
title: Alkalmazások üzembe helyezése az Azure-ban és az Azure Stacken
description: Megtudhatja, hogyan helyezhet üzembe alkalmazásokat az Azure-ban, és hogyan Azure Stack egy hibrid CI/CD-folyamattal.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/23/2019
ms.topic: conceptual
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/07/2018
ms.openlocfilehash: eb9ed23437d5fd708d3f98d5a5b601f3ed1a02a0
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283721"
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Alkalmazások üzembe helyezése az Azure-ban és az Azure Stacken

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ez a megoldás bemutatja, hogyan helyezhetők üzembe alkalmazások az Azure-ban, és hogyan Azure Stack az Azure-folyamatok hibrid folyamatos integráció és folyamatos teljesítés (CI/CD) használatával.

Miután beállította a Azure Stack, az Azure DevOps és a Web Apps alkalmazást az [Előfeltételek](#prerequisites)alapján, a következőket teheti:

> [!div class="checklist"]
> - Regisztrálja webalkalmazását, és állítsa be az Azure-folyamatok elérését Azure Active Directory (Azure AD) szolgáltatásban. 
> - Azure-adatcsatornák végpontjának létrehozása az Azure AD-hez és a Active Directory összevonási szolgáltatások (AD FS)hoz (AD FS). 
> - Telepítse az Azure-folyamatokat a Azure Stack Build-kiszolgálóra. 
> - Önálló alkalmazások telepítése az Azure-ba és a Azure Stack.
> - Hozzon létre egy olyan Build-folyamatot, amely kód alapján véglegesíti a projektet az automatikus buildek futtatásával.
> - Hozzon létre egy kiadási folyamatot, amely automatikusan üzembe helyezi a buildeket az Azure AD-ben és a Azure Stackon. 
> - Kiadások manuális létrehozása és üzembe helyezése, valamint a kiadási összegzések és naplók megtekintése. 

A CI/CD-vel kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Mi a folyamatos integráció?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [Mi a folyamatos kézbesítés?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="azure-stack-and-hybrid-cicd"></a>Azure Stack és hibrid CI/CD

Microsoft Azure Stack az Azure kiterjesztése, amely a felhő-számítástechnika rugalmasságát és innovációját teszi lehetővé a helyszíni környezetben. Ez az egyetlen hibrid felhő, amely lehetővé teszi hibrid alkalmazások kialakítását és üzembe helyezését a helyszínen, valamint a nyilvános Felhőbeli környezetekben. Létrehozhat egy alkalmazást Azure Stack, majd üzembe helyezheti Azure Stackba, az Azure-ba vagy az Azure hibrid felhőbe. 

Az alkalmazások üzembe helyezésének folytonossága, biztonsága és megbízhatósága kritikus fontosságú elemek a szervezet és a fejlesztői csapat számára. Az Azure-folyamatok hibrid CI/CD-kézbesítési modellje lehetővé teszi, hogy összevonja a felépítési folyamatokat a helyszíni környezetben és a nyilvános felhőben, és az alkalmazás módosítása nélkül módosítsa a telepítési helyeit. A hibrid megközelítés használatának egyéb előnyei a következők:

- A helyszíni Azure Stack környezetében és az Azure nyilvános felhőben is megőrizheti a fejlesztői eszközök egységes készletét.  Egy közös eszközkészlet megkönnyíti a CI/CD-minták és-gyakorlatok megvalósítását.
- Az Azure-ban vagy Azure Stackban üzembe helyezett alkalmazások és szolgáltatások felcserélhetők, és ugyanazon kód mindkét helyen futtatható. Igénybe veheti a helyszíni és a nyilvános Felhőbeli funkciókat és képességeket.

> [!TIP]
> @no__t -0hybrid-Pillars. png @ no__t-1  
> A hibrid [felhőalapú tervezési minták a Azure stack](azure-stack-edge-pattern-overview.md) a hibrid alkalmazások tervezéséhez, üzembe helyezéséhez és üzemeltetéséhez szükséges szoftverek minőségi pilléreit ismertetik. A minőségi feltételek közé tartozik az elhelyezés, a méretezhetőség, a rendelkezésre állás, a rugalmasság, a kezelhetőség és a biztonság. Ezek a kialakítási szempontok segítik a hibrid alkalmazások kialakításának optimalizálását, ami minimalizálja az éles környezetekben felmerülő kihívásokat.

## <a name="prerequisites"></a>Előfeltételek

- Az Azure és a Azure Stack alapszintű ismerete. Ha többet szeretne megtudni a megoldás üzembe helyezése előtt, olvassa el a következő cikkeket:
  
  - [Bevezetés az Azure-ba](https://azure.microsoft.com/overview/what-is-azure/)
  - [Azure Stack áttekintése](../operator/azure-stack-overview.md)
  
- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.
  
- Az Azure-ban létrehozott webalkalmazás. [Azure Resource Manager sablonnal](https://azure.microsoft.com/resources/templates/) létrehozhat egy webalkalmazást, amely a helyszínen és a nyilvános felhőben is üzembe helyezhető. Jegyezze fel az alkalmazás URI azonosítóját, hogy később használhassa. 
  
- A Visual Studio 2019 [telepítve van](/visualstudio/install/install-visual-studio).
  
- Rendszergazdai hozzáférés egy olyan [Azure DevOps](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) -szervezethez, amely folyamatokat hozhat létre, valamint egy DevOps- [projektet](/azure/devops/organizations/projects/create-project) vagy- [munkaterületet](/azure/devops/repos/tfvc/create-work-workspaces). 
  
- Egy Windows Server 2016-es rendszerkép a .NET 3,5-es verzióval a privát Build Agent virtuális gép (VM) Azure-folyamatai a Azure Stackra épülnek.
  
- Egy Azure Stack integrált rendszer vagy a Azure Stack Development Kit (ASDK), amelyet az alábbi utasítások szerint helyeztek üzembe és konfiguráltak. 
  
  
  A Azure Stack Development Kit (ASDK) a Azure Stack egyetlen csomópontos telepítése, amelyet ingyenesen letöltheti és használhatja. A ASDK lehetővé teszi az Azure API-k és az eszközök nem éles környezetben való használatának kiértékelését Azure Stack és használatát.
  
  Az Azure AD vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) rendszergazdai hitelesítő adatokkal rendelkező felhasználók telepíthetik a ASDK. Egy Azure OEM/Hardware partner üzembe helyezhet egy éles Azure Stack. A következő Azure Stack konfigurációs feladatok elvégzéséhez Azure Stack operátornak kell lennie: 
  
  - Az Azure App Service üzembe helyezése
  - Csomagok és ajánlatok létrehozása
  - Bérlői előfizetés létrehozása
  - Windows Server 2016-rendszerkép alkalmazása
  
  > [!Note]
  > A ASDK telepítése körülbelül hét órát vesz igénybe, ezért tervezze meg ennek megfelelően.
    
  A ASDK telepítése és konfigurálása:
  
  1. Kövesse a központi telepítési utasításokat a [ASDK telepítése a telepítő használatával](../asdk/asdk-install.md)című témakörben.
     
  1. A [ConfigASDK. ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) PowerShell-parancsfájl segítségével automatizálhatja a ASDK utáni lépéseket.
     
  1. [Azure app Service](../operator/azure-stack-app-service-deploy.md) Péter-szolgáltatások üzembe helyezése Azure stack.
     
  1. Hozzon létre egy [csomagot és egy ajánlatot](../operator/service-plan-offer-subscription-overview.md) a Azure stack.
     
  1. Hozzon létre egy [bérlői előfizetést](../operator/azure-stack-subscribe-plan-provision-vm.md) az ajánlathoz Azure Stackban. 
     
  1. Hozzon létre egy webalkalmazást a bérlői előfizetésben. Jegyezze fel az új webalkalmazás URL-címét későbbi használatra.
     
  1. Telepítsen egy Windows Server 2016 virtuális gépet a .NET 3,5-tel a bérlői előfizetésben, hogy az Azure-folyamatokat futtató Build kiszolgáló legyen.
  
  > [!Note]
  > A Azure Stack-környezetnek megfelelő rendszerképekre van szüksége a Windows Server és a SQL Server futtatásához. Emellett App Service központilag telepítve kell lennie.

## <a name="register-your-web-app-and-give-it-access-to-resources"></a>Webalkalmazás regisztrálása és hozzáférés biztosítása az erőforrásokhoz 

Azure Active Directory (Azure AD) esetében az Azure-folyamatok egy *egyszerű szolgáltatásnév*használatával végzik el a hitelesítést Azure Resource Manager. Ahhoz, hogy erőforrásokat lehessen kiépíteni az Azure-folyamatokhoz, az egyszerű szolgáltatásnak rendelkeznie kell a **közreműködő** szerepkörrel az Azure-előfizetésben. 

A Azure Portal az alkalmazás hitelesítésének konfigurálására is használható. 

1. Az alkalmazás regisztrálása egyszerű szolgáltatásnév létrehozásához.
1. *Szerepköralapú hozzáférés-vezérlés (RBAC)* használatával adja meg az egyszerű szolgáltatásnév (SPN) a **közreműködő** szerepkört.
1. Másolja ki és mentse el az alkalmazás-azonosító és a bérlői azonosító értékeit, amelyekhez végpontokat kell létrehoznia az Azure-folyamatok számára. 
1. Hozzon létre és mentsen egy alkalmazás titkos kulcsának értékét.

[Egy PowerShell-parancsfájllal](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5) is létrehozhat egy egyszerű szolgáltatásnevet és végpontokat. A [Azure Resource Manager-szolgáltatási kapcsolatok létrehozása egy meglévő egyszerű szolgáltatással](/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) című cikk ismerteti ezt a folyamatot.

 > [!Note]  
 > Ha a PowerShell-parancsfájllal Azure Stack Azure Resource Manager végpontot hoz létre, át kell adnia a **-azureStackManagementURL** paramétert és a **-environmentName** paramétert. Példa:  
 > `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="register-your-app-in-azure-ad"></a>Az alkalmazás regisztrálása az Azure AD-ben 

1. A [Azure Portal](https://portal.azure.com)válassza a **Azure Active Directory**lehetőséget, majd a bal oldali navigációs menüben válassza a **Alkalmazásregisztrációk** lehetőséget.
   
1. Válassza az **új regisztráció**lehetőséget.
   
1. Az **alkalmazás regisztrálása** lapon
   1. Adja meg a webalkalmazás nevét.
   1. Válasszon egy támogatott számlatípust. 
   1. Az **átirányítási URI**területen válassza a **web** lehetőséget a létrehozni kívánt alkalmazás típusához, és adja meg a webalkalmazás URI-ját. 
   1. Kattintson a **Register** (Regisztrálás) elemre.
      
      ![Alkalmazás regisztrálása](./media/azure-stack-solution-pipeline/create-app.png) 

### <a name="assign-the-app-to-a-role"></a>Az alkalmazás társítása szerepkörhöz

Az alkalmazásnak hozzá kell rendelnie egy szerepkörhöz ahhoz, hogy hozzáférjen az előfizetéséhez tartozó erőforrásokhoz. Az Azure RBAC lehetővé teszi a felhasználóknak a feladatok végrehajtásához szükséges hozzáférési szint szabályozását. További információ a RBAC: az [Azure-előfizetések erőforrásaihoz való hozzáférés kezelése](/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json). Az elérhető szerepkörökről a [RBAC: beépített szerepkörök](/azure/role-based-access-control/built-in-roles)című témakörben olvashat bővebben.

Az Azure-folyamatoknak a **közreműködő** szerepkörrel kell rendelkezniük ahhoz, hogy erőforrásokat lehessen kiépíteni egy Azure stack-előfizetésben. 

Megadhatja a szerepkör hatókörét az előfizetés, az erőforráscsoport vagy az erőforrás szintjén. Az engedélyek a hatókör alacsonyabb szintjein vannak örökölve. Ha például hozzáad egy alkalmazást az **olvasó** szerepkörhöz egy erőforráscsoport számára, azt jelenti, hogy az alkalmazás elolvashatja az erőforráscsoportot és annak bármely erőforrását.

Az alkalmazás társítása a **közreműködő** szerepkörhöz:

1. A Azure Portal navigáljon a kívánt hatókör szintjéhez. Ha például egy szerepkört szeretne hozzárendelni az előfizetés hatókörében, válassza a **minden szolgáltatás** és **előfizetés**lehetőséget.
   
   ![Szerepkör társítása az előfizetés hatókörében](./media/azure-stack-solution-pipeline/select-subscription.png)
   
1. Válassza ki azt az előfizetést, amelyhez hozzá kívánja rendelni az alkalmazást.
   
1. A bal oldali navigációs sávon válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
   
1. Válassza **a szerepkör-hozzárendelés hozzáadása**lehetőséget.
   
1. A **szerepkör-hozzárendelés hozzáadása** párbeszédpanelen válassza ki a **közreműködői** szerepkört. Alapértelmezés szerint az Azure AD-alkalmazások nem jelennek meg az elérhető beállítások között. Az alkalmazás megkereséséhez keresse meg a nevet, és jelölje ki.
   
   ![Válassza ki a szerepkört és az alkalmazást](./media/azure-stack-solution-pipeline/select-role.png)
   
1. A szerepkör hozzárendelésének befejezéséhez kattintson a **Mentés** gombra. Az alkalmazást az adott hatókörhöz tartozó szerepkörhöz rendelt felhasználók listájában tekintheti meg.

Az egyszerű szolgáltatásnév be van állítva. A következő szakasz bemutatja, hogyan kérheti le az Azure-folyamatok programozott módon történő bejelentkezéséhez szükséges értékeket.

### <a name="get-values-for-signing-in"></a>Bejelentkezések értékeinek beolvasása

Az Azure-folyamatok végpontjának létrehozásakor meg kell adnia a bérlő AZONOSÍTÓját és az alkalmazás AZONOSÍTÓját. A következő értékek beolvasása:

1. Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
   
1. A bal oldali navigációs sávon válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza ki az alkalmazást.
   
1. Másolja és mentse a **könyvtár (bérlő) azonosítóját** és a végpontok létrehozásához használandó **alkalmazás-(ügyfél-) azonosítót** .
   
   ![A címtár (bérlői azonosító) és az alkalmazás (ügyfél) AZONOSÍTÓjának másolása](./media/azure-stack-solution-pipeline/copy-app-id.png)

### <a name="create-a-new-application-secret"></a>Új alkalmazás-titok létrehozása

Az Azure-folyamatok végpontjának létrehozásakor az alkalmazásnak hitelesítenie kell magát. [Használhatja a tanúsítványt](/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) vagy az alkalmazás titkos kulcsát. Ha a Azure Stackt AD FS identitás-szolgáltatóként telepítette, akkor tanúsítványt kell használnia.

Új tanúsítvány létrehozásához és feltöltéséhez kövesse a [tanúsítványok és a titkok](/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) című témakör lépéseit. 

Vagy új alkalmazás-titkos kód létrehozásához:

1. Az Azure Portalon válassza az **Azure Active Directory** lehetőséget.
   
1. A bal oldali navigációs sávon válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza ki az alkalmazást.
   
1. A bal oldali navigációs sávon válassza a **tanúsítványok & Secrets**elemet.
   
1. Az **ügyfél**titkos kulcsa területen válassza az **új ügyfél titka**lehetőséget.
   
1. Az **ügyfél titkos kulcsának hozzáadása**lapon írja be a leírást, válasszon ki egy lejáratot, és válassza a **Hozzáadás**lehetőséget.
   
1. Másolja az új titok **értékét** . Meg kell adnia az alkalmazásként való bejelentkezéshez szükséges értéket. Fontos, hogy most mentse ezt az értéket, mert nem jelenik meg többé az oldal elhagyását követően.
   
   ![Másolja a titkos értéket, mert később nem tudja lekérni](./media/azure-stack-solution-pipeline/copy-secret.png)

## <a name="create-endpoints"></a>Végpontok létrehozása

A végpontok létrehozásával az Azure-folyamatok buildek üzembe helyezhetik az Azure AD-alkalmazásokat a Azure Stack. Az Azure-folyamatok összekapcsolják a Build ügynököt, amely a Azure Stackhoz csatlakozik.

A végpont-létrehozási engedélyek beállítása után létrehozhat végpontokat az Azure AD-hez vagy a AD FShoz. 

- Ha a Azure Stack az Azure AD-ben az identitás-szolgáltatóként telepítette, akkor a tanúsítvány vagy az alkalmazás titkát használhatja az Azure-beli üzemelő példányokhoz tartozó Azure Resource Manager-szolgáltatási kapcsolatok létrehozásához. 
  
- Ha Active Directory összevonási szolgáltatások (AD FS) (AD FS) azonosítót használt a Azure stack identitás-szolgáltatója, akkor a hitelesítéshez a-ügyfél helyett tanúsítvány használatával kell létrehoznia a szolgáltatási kapcsolatokat. 

### <a name="set-endpoint-creation-permissions"></a>Végpont-létrehozási engedélyek beállítása

1. Az Azure DevOps-szervezetben és-projektben válassza a **projekt beállításai**lehetőséget. 
   
1. Válassza a **Biztonság**lehetőséget, majd az **Azure DevOps csoportok**területen válassza ki a **végponti rendszergazdák**elemet.
   
1. A **tagok** lapon válassza a **Hozzáadás**lehetőséget.
   
1. A **felhasználók és csoportok hozzáadása**lapon válassza a felhasználónevek lehetőséget a listából, és válassza a saját maga is lehetőséget, majd kattintson a **módosítások mentése**gombra.
   
   ![Tag hozzáadása](./media/azure-stack-solution-pipeline/endpoint-permissions.png)
   
1. Az **Azure DevOps-csoportok** listában válassza ki a **végpont-létrehozók**lehetőséget, és ismételje meg az előző lépéseket, hogy felhasználókat vegyen fel a **Endpoint Creators** csoportba. 

### <a name="create-an-endpoint-for-azure-ad-or-ad-fs-deployments"></a>Végpont létrehozása az Azure AD-hez vagy AD FS üzemelő példányokhoz

A szolgáltatás kapcsolódási végpontjának létrehozásához kövesse az [Azure Resource Manager-szolgáltatási kapcsolatok létrehozása meglévő egyszerű szolgáltatással](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) című témakör utasításait.  

Az űrlap kitöltéséhez használja a következő értékeket: 

- **Kapcsolódási név**: adjon meg egy felhasználóbarát nevet, amelyet a rendszer a szolgáltatási kapcsolatban való hivatkozáshoz használ.
  
- **Környezet**: válassza ki a környezet nevét, például **AzureCloud** vagy **AzureStack**. Ha nem látja a AzureStack a legördülő menüben, tekintse meg a [kapcsolódás Azure Stackhoz](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops#connect-to-azure-stack)című témakört.
  
- **Környezet URL-címe**: Ha nem választotta ki a **AzureCloud**, adja meg a környezet URL-címét (például *https: \//Management. local. azurestack. external*).
  
- **Hatókör szintje**: válassza ki a szükséges hatóköri szintet, például az **előfizetést**. 
  
- **Előfizetés azonosítója**: adja meg az előfizetés-azonosítóját.
  
- **Előfizetés neve**: írja be a felhasználónevét Azure stack.
  
- **Egyszerű szolgáltatásnév ügyfél-azonosítója**: adja meg a korábban mentett **alkalmazás (ügyfél) azonosítóját** . 
  
- **Egyszerű szolgáltatás kulcsa** vagy **tanúsítványa**: válasszon egyet vagy másik lehetőséget. 
  
  > [!NOTE]
  > AD FS végpont létrehozásához tanúsítványt kell használnia a hitelesítéshez. 
  
  - Az **egyszerű szolgáltatásnév kulcsa**mezőben adja meg a korábban mentett ügyfél titkos kulcs értékét.
  - Ha a **tanúsítvány**lehetőséget választja, adja meg a *. PEM* tanúsítványfájl tanúsítvány és titkos kulcs részének tartalmát. 
    
    > [!NOTE]
    > Ha *. pfx* fájlt szeretne átalakítani egy *. PEM* tanúsítványfájl-fájlba, futtassa a következőt: `openssl pkcs12 -in file.pfx -out file.pem -nodes -password pass:<password_here>`.
  
- **Bérlő azonosítója**: adja meg a korábban mentett **címtár-(bérlői) azonosítót** .
  
- **Kapcsolódás: nincs ellenőrizve**: válassza a **Kapcsolódás ellenőrzése** lehetőséget a kapcsolódási beállítások érvényesítéséhez az egyszerű szolgáltatáshoz.
  
  > [!NOTE]
  > Ha az Azure Resource Manager-végpont nem érhető el az internethez, a kapcsolódás ellenőrzése sikertelen lesz. Ez várható, és egy egyszerű feladattal rendelkező kiadási folyamat létrehozásával ellenőrizheti a kapcsolatokat.

## <a name="install-and-configure-the-build-agent"></a>A Build-ügynök telepítése és konfigurálása 

Az üzemeltetett Build-ügynök használata az Azure-folyamatokban kényelmes megoldás a webalkalmazások létrehozásához és üzembe helyezéséhez. Az Azure automatikusan végrehajtja az ügynökök karbantartását és frissítéseit, amelyek lehetővé teszik a folyamatos és zavartalan fejlesztési ciklust.

Az Azure DevOps hozzon létre egy személyes hozzáférési jogkivonatot (PAT) a Azure Stackhez való használatra. Ezután a PAT használatával telepítse és konfigurálja a Build ügynököt a Azure Stack Build Server virtuális gépen. 
   
### <a name="create-a-personal-access-token"></a>Személyes hozzáférési jogkivonat létrehozása

1. Jelentkezzen be az Azure DevOps, és válassza a **saját profil** lehetőséget a jobb felső sarokban. 
   
1. A profil lapon bontsa ki a Azure Stack szervezet neve melletti legördülő menüt, és válassza a **biztonság kezelése**lehetőséget. 
   
   ![Biztonság kezelése](media/azure-stack-solution-pipeline/managesecurity.png)
   
1. A **személyes hozzáférési tokenek** lapon válassza az **új jogkivonat**lehetőséget.
   
   ![Személyes hozzáférési jogkivonatok](media/azure-stack-solution-pipeline/pats.png)
   
1. Az **új személyes hozzáférési jogkivonat létrehozása** lapon töltse ki a jogkivonat adatait, és válassza a **Létrehozás**lehetőséget. 
   
   ![PAT létrehozása](media/azure-stack-solution-pipeline/createpat.png)
   
1. Másolja ki és mentse a jogkivonatot. A weblap elhagyása után nem jelenik meg többé.
   
   ![PAT-figyelmeztetés](media/azure-stack-solution-pipeline/patwarning.png)
   
### <a name="install-and-configure-the-agent-on-the-build-server"></a>Az ügynök telepítése és konfigurálása a Build-kiszolgálón

1. Kapcsolódjon a Azure Stack gazdagépen üzembe helyezett Build Server-alapú virtuális géphez.
   
1. Töltse le a Build Agent rendszerképét. 
   
1. A rendszergazda parancssorában telepítse az ügynököt szolgáltatásként a PAT használatával, majd futtassa.
   
1. Navigáljon a kibontott Build-ügynök mappájához, és futtassa a *config. cmd* fájlt. A *config. cmd* a Build Agent mappát további fájlokkal frissíti.
   
   ![A Build-ügynök telepítése és konfigurálása](media/azure-stack-solution-pipeline/buildagent.png)

Most, hogy létrehozta a végpontot, és telepítette az Azure-folyamatokat a Build-kiszolgálón, az Azure-folyamatok Azure Stack a kapcsolódás készen áll a használatra. A Azure Stack Build ügynöke beolvassa az Azure-folyamatok utasításait, majd az ügynök a Azure Stacksal folytatott kommunikációhoz végponti információkat továbbít.

Ahelyett, hogy az egyes ügynököket külön-külön kezelhesse, az ügynököket *ügynök-készletekbe*rendezheti. Az ügynök-készlet a készletben lévő összes ügynök megosztási határát határozza meg. Az ügynök-készletek az Azure DevOps-szervezetre vannak korlátozva, ami azt jelenti, hogy a projektek között megosztható egy ügynök készlete. Az ügynökökkel kapcsolatos további tudnivalókért tekintse meg az [ügynök-készletek és-várólisták létrehozása](/azure/devops/pipelines/agents/pools-queues)című témakört.

## <a name="create-build-and-release-pipelines"></a>Build és kiadási folyamatok létrehozása 

Az Azure-folyamatok kiválóan konfigurálható és kezelhető folyamatokat biztosítanak több környezet, például fejlesztés, előkészítés, minőségbiztosítás (QA) és éles környezetben történő kiadásokhoz. A kiadási folyamat tartalmazhat jóváhagyásokat az alkalmazás életciklusának adott szakaszaiban.

A megoldás ezen részében a következőket látja el:

- Kód klónozása, összekötése és hozzáadása az Azure DevOps-projekthez a Visual Studióban.
- Hozzon létre egy önálló, webalkalmazás-telepítést.
- Konfigurálja a CI/CD Build és a kiadási folyamatokat.

A hibrid CI/CD az alkalmazás kódjára és az infrastruktúra kódjára is alkalmazható. [Azure Resource Manager hibrid sablonok](https://azure.microsoft.com/resources/templates/) használatával üzembe helyezheti Azure-webalkalmazási kódját a helyszíni és a nyilvános felhőkben.

### <a name="clone-your-project"></a>A projekt klónozása

1. A Visual Studio **Team Explorer**válassza a **Kapcsolódás** ikont, és jelentkezzen be az Azure DevOps-szervezetbe. 
   
1. Válassza a **kapcsolatok kezelése** > **Csatlakozás projekthez**lehetőséget. 
   
   ![Kapcsolódás projekthez Team Explorer](media/azure-stack-solution-pipeline/connecttoprojectteamexp.png)

1. A **Kapcsolódás projekthez** párbeszédpanelen válassza ki a webalkalmazás-projektet, állítsa be a helyi elérési utat, majd válassza a **klónozás** lehetőséget a tárház helyi klónozásához.
   
   ![Adattárház klónozása projekthez való kapcsolódással](media/azure-stack-solution-pipeline/cloneproject.png)

### <a name="create-a-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Saját tulajdonú webalkalmazás-telepítés létrehozása a App Services mindkét felhőben

1. A Visual Studio **megoldáskezelő**nyissa meg a *webalkalmazás. csproj* fájlt, és adja hozzá a `<RuntimeIdentifier>win10-x64</RuntimeIdentifier>`. További információ erről a lépésről: [önmagába foglalt központi telepítés](/dotnet/core/deploying/#self-contained-deployments-scd).
   
   ![RuntimeIdentifier konfigurálása](media/azure-stack-solution-pipeline/runtimeidentifier.png)
   
1. Mentse a munkáját, és **Team Explorer** használatával keresse meg a kódot a projektben.

### <a name="create-a-build-pipeline-and-run-a-build"></a>Build folyamat létrehozása és Build futtatása

1. A böngészőben nyissa meg az Azure DevOps-szervezetét és-projektjét.
   
1. Válassza a **folyamatok** > **buildek** lehetőséget a bal oldali navigációs sávon, majd válassza az **új folyamat**elemet. 
   
1. A **sablon kiválasztása**területen válassza ki a **ASP.net Core** sablont, majd válassza az **alkalmaz**lehetőséget. 
   
1. A konfiguráció lapon válassza a **Közzététel** lehetőséget a bal oldali ablaktáblán.
   
1. A jobb oldali ablaktábla argumentumok területén adja hozzá a `-r win10-x64` **paramétert**a konfigurációhoz. 
   
   ![Build folyamati argumentum hozzáadása](media/azure-stack-solution-pipeline/buildargument.png)
   
1. Válassza az oldal tetején található **&-várólista mentése** lehetőséget.
   
1. A **folyamat futtatása** párbeszédpanelen válassza a **Mentés és Futtatás**lehetőséget. 
   
A [saját üzemeltetésű üzembe helyezési Build](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) közzéteszi az Azure-ban és a Azure stackon futtatható összetevőket is.

### <a name="create-a-release-pipeline"></a>Kiadási folyamat létrehozása

A kiadási folyamat létrehozása a hibrid CI/CD-konfiguráció utolsó lépése. A kiadási folyamat használatával kiadást hozhat létre, és üzembe helyezheti a buildet.

1. Az Azure DevOps-projektben válassza a **folyamatok** > **kiadás** lehetőséget a bal oldali navigációs sávon, majd válassza az **új folyamat**elemet. 
   
1. A **sablon kiválasztása** lapon válassza a **Azure app Service központi telepítés**lehetőséget, majd kattintson az **alkalmaz**gombra.
   
   ![A kiadási sablon kiválasztása](media/azure-stack-solution-pipeline/releasetemplate.png)
   
1. A **folyamat** lapon válassza az **összetevő hozzáadása** elemet a bal oldali ablaktáblán. A jobb oldali ablaktáblában válassza ki az imént létrehozott webalkalmazás-összeállítást a **forrás (folyamat létrehozása)** legördülő menüből, és válassza a **Hozzáadás**lehetőséget.
   
   ![Build-összetevő hozzáadása](media/azure-stack-solution-pipeline/addartifact.png)
   
1. A **folyamat** lap **szakaszok**területén válassza ki az **1. fázisban** található hiperhivatkozást a **szakasz feladatainak megtekintéséhez**.
   
   ![Szakasz feladatainak megtekintése](media/azure-stack-solution-pipeline/viewstagetasks.png)
   
1. A **feladatok** lapon adja meg az *Azure* -t a **fázis neveként**. 
   
1. A **Paraméterek**területen válassza ki az előfizetését az **Azure-előfizetés** legördülő listából, és írja be az **app Service-nevet**.
   
   ![Válassza ki az előfizetést, és adja meg App Service nevét](media/azure-stack-solution-pipeline/stage1.png)
   
1. A bal oldali ablaktáblán válassza a **Futtatás az ügynökön**lehetőséget. Ha még nincs kiválasztva, a jobb oldali ablaktáblában válassza a **tárolt VS2017** elemet az **ügynök készlet** legördülő listából.
   
   ![Üzemeltetett ügynök kiválasztása](media/azure-stack-solution-pipeline/agentjob.png)
   
1. A bal oldali ablaktáblán válassza a **központi telepítés Azure app Service**lehetőséget, majd a jobb oldali ablaktáblában keresse meg az Azure-webalkalmazás-Build **csomagját vagy mappáját** .
   
   ![Csomag vagy mappa kiválasztása](media/azure-stack-solution-pipeline/packageorfolder.png)
   
1. A **fájl vagy mappa kiválasztása** párbeszédpanelen kattintson **az OK gombra**.
   
1. Válassza a **Mentés** lehetőséget a jobb felső sarokban az **új kiadási folyamat** oldalon.
   
   ![Módosítások mentése](media/azure-stack-solution-pipeline/save-devops-icon.png)
   
1. A **folyamat** lapon válassza az **összetevő hozzáadása**elemet. Válassza ki a projektet, és válassza ki a Azure Stack épít a **forrás (folyamat létrehozása)** legördülő menüből. Válassza a **Hozzáadás** lehetőséget. 
   
1. A **folyamat** lap **szakaszok**területén válassza a **Hozzáadás**lehetőséget.
   
1. Az új szakaszban válassza ki a hivatkozást a **szakasz feladatainak megtekintéséhez**. Adja meg a *Azure stackt* a szakasz neveként. 
   
   ![Új szakasz megtekintése](media/azure-stack-solution-pipeline/newstage.png)
   
1. A **Paraméterek**területen válassza ki a Azure stack végpontot, és adja meg a Azure stack webalkalmazás nevét az **app Service neveként**.
   
1. A **Futtatás ügynök**alapján területen válassza ki a Azure stack Build kiszolgáló ügynököt az **ügynök készlet** legördülő listából.
   
1. **Azure app Service üzembe helyezéséhez**válassza ki a Azure stack Build **csomagját vagy mappáját** , majd kattintson az **OK gombra** a **fájl vagy mappa kiválasztása** párbeszédpanelen.
   
1. A **változók** lapon keresse meg a **VSTS_ARM_REST_IGNORE_SSL_ERRORS**nevű változót. Állítsa a változó értékét **igaz**értékre, és állítsa a hatókörét **Azure Stackra**.
   
   ![Változó konfigurálása](media/azure-stack-solution-pipeline/setvariable.png)
   
1. A **folyamat** lapon válassza ki a **folyamatos üzembe helyezési trigger** ikont az egyes összetevőknél, és állítsa be az **engedélyezett**értékre.  
   
   ![Folyamatos üzembe helyezési trigger beállítása](media/azure-stack-solution-pipeline/contindeploymentenabled.png)
   
1. Válassza ki az **üzembe helyezés előtti feltételek** ikont a Azure stack fázisban, és állítsa be a triggert a **kiadás után**.
   
   ![Üzembe helyezés előtti feltételek beállítása trigger](media/azure-stack-solution-pipeline/predeployafterrelease.png)
   
1. Válassza a **Mentés** lehetőséget a jobb felső sarokban az **új kiadási folyamat** lapon a kiadási folyamat mentéséhez.

> [!Note]
> Előfordulhat, hogy a kiadási feladatok egyes beállításai automatikusan [Egyéni változókként](/azure/devops/pipelines/release/variables?view=vsts#custom-variables) vannak definiálva, amikor létrehozta a kiadási folyamatot a sablonból. Ezek a beállítások nem módosíthatók a feladat beállításaiban, de a fölérendelt szakasz elemeiben szerkesztheti őket.

## <a name="release-the-app"></a>Az alkalmazás kiadása

Most, hogy már rendelkezik egy kiadási folyamattal, felhasználhatja egy kiadás létrehozásához és az alkalmazás üzembe helyezéséhez. 

Mivel a folyamatos üzembe helyezési trigger be van állítva a kiadási folyamatba, a forráskód módosítása új buildet indít el, és automatikusan létrehoz egy új kiadást. Az új kiadást azonban manuálisan kell létrehoznia és futtatnia.

Kiadás létrehozása és üzembe helyezése:

1. Az új kiadási folyamat lapon válassza a **kiadás létrehozása** a jobb felső sarokban lehetőséget. 
   
   ![Kiadás létrehozása ](media/azure-stack-solution-pipeline/createreleaseicon.png)
   
1. Az **új kiadás létrehozása** oldalon:
   1. A **folyamat**alatt válassza ki az **Azure** -szakaszt az trigger automatikusról manuálisra változtatásához. 
   1. Az **összetevők területen győződjön meg arról, hogy**a megfelelő összetevők vannak kiválasztva.
   1. Adja meg a **kiadás leírását**, majd válassza a **Létrehozás**lehetőséget. 
   
   A szalagcím azt jelzi, hogy az új kiadás létrejött. A kiadás neve hivatkozásra kattintva megtekintheti a kiadási összefoglalás oldalt, amely a kiadás részleteit mutatja be, például a telepítési állapotot.
   
1. A manuális kiadás üzembe helyezéséhez válassza ki az **Azure** -szakaszt, válassza a **telepítés**lehetőséget, majd a szakasz párbeszédpanelen válassza a **telepítés** lehetőséget. 
   
1. Amikor az üzembe helyezés sikeresen befejeződik, nyissa meg a telepített alkalmazást a böngészőben. Az Azure App Services webhelyén például nyissa meg `https://<your-app-name>.azurewebsites.net` URL-címet.

### <a name="monitor-and-track-releases"></a>Kiadások monitorozása és nyomon követése

Kiválaszthatja a hiperhivatkozásos állapotot a kiadási fázisban, így további információkat tekinthet meg a központi telepítésről. 

![Kiadás összegzése lap](media/azure-stack-solution-pipeline/releasesummary.png)

A rendszergazdák egyszerűen nyomon követhetik a kiadások általános előrehaladását, és megtekinthetik, hogy mely kiadások várnak jóváhagyásra.

![A függőben lévő jóváhagyást mutató kiadás összegzése lap](media/azure-stack-solution-pipeline/releasepending.png)

A kiadási naplókat az összes üzemelő példányról megtekintheti: 

1. Az Azure DevOps-projektben válassza a **folyamatok** > **kiadások** lehetőséget a bal oldalon, majd válasszon ki egy kiadást. 
   
1. A kiadás összegzése lapon vigye a kurzort egy szakaszra, vagy válasszon ki egy szakaszt, majd válassza a **naplók**lehetőséget. 
   
   A kiadási naplóban a bal oldali ablaktábla az egyes műveletek állapotát jeleníti meg az egyes fázisokban. A központi telepítés során a jobb oldali ablaktábla az ügynökből származó élő naplót jeleníti meg. Az üzembe helyezés befejeződése után a teljes naplófájl megjelenik a jobb oldali ablaktáblán. 
   
1. A bal oldali ablaktábla bármelyik lépését kiválasztva megtekintheti az adott lépéshez tartozó naplófájlt, például az **üzembe helyezés előtti jóváhagyásokat**. 
   
1. A jóváhagyások megtekintéséhez válassza a jobb oldali ablaktáblán a **jóváhagyás megtekintése** lehetőséget, hogy megtekintse, ki hagyta jóvá vagy utasította el a kiadást, valamint egyéb részleteket. 
   
Az egyes lépések naplóinak megtekintésével könnyebben lehet nyomon követni és hibakeresést végezni a teljes telepítés részeit. Az **összes naplót** *. zip* -fájlként is letöltheti.
   
![Kiadási napló](media/azure-stack-solution-pipeline/releaselog.png)

## <a name="next-steps"></a>Következő lépések

Az Azure Cloud Patterns szolgáltatással kapcsolatos további információkért lásd: [Felhőbeli tervezési minták](/azure/architecture/patterns).
