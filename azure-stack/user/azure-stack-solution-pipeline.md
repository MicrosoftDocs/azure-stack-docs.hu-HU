---
title: Az oktatóanyag&#58; alkalmazások üzembe helyezése az Azure és az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan telepíthet alkalmazásokat az Azure és az Azure Stack egy hibrid CI/CD-folyamat.
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
ms.date: 03/11/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/07/2018
ms.openlocfilehash: 9f0f25e5810fc4c9a27d3607defbaca9dcfc0388
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692083"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Oktatóanyag: Alkalmazások üzembe helyezése az Azure-ban és az Azure Stacken

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ismerje meg, hogyan telepíthet alkalmazásokat az Azure és az Azure Stack egy hibrid folyamatos integráció/folyamatos teljesítés (CI/CD) folyamat használatával.

Ebben az oktatóanyagban egy mintául szolgáló környezetet hozunk létre:

> [!div class="checklist"]
> * Kód véglegesítése a az Azure DevOps-szolgáltatásokkal adattár alapján új build kezdeményezni.
> * A felhasználói tesztelés az Azure globális üzembe helyezhetők az alkalmazást.
> * A kód továbbítja, tesztelés, üzembe helyezhetők az alkalmazás az Azure Stackhez.

## <a name="benefits-of-the-hybrid-delivery-build-pipeline"></a>A hibrid kézbesítési előnyei folyamat létrehozása

Folytonosságát, biztonságot és megbízhatóságot az alkalmazás üzembe helyezése a legfontosabb elemei. Ezek az elemek a következők: alapvető, a szervezet számára és a fejlesztői csapat számára kritikus. Egy hibrid CI/CD-folyamat lehetővé teszi a build csövek összevonni a helyszíni környezet és a nyilvános felhőben. A hibrid kézbesítési modell is lehetővé teszi a központi telepítés módosítása az alkalmazás módosítása nélkül.

A hibrid megközelítés egyéb előnyei a következők:

* A konzisztens fejlesztési eszközök akkor is fenntartható a helyszíni Azure Stack-környezet és az Azure nyilvános felhő között.  Eszköz tulajdonságkészlettel megkönnyíti a CI/CD patterns and practices nevű megvalósításához.
* Alkalmazások és szolgáltatások Azure-ban üzembe helyezett felcserélhető, és ugyanazt a kódot futtathatja az egyik helyen. A helyszíni és a nyilvánosfelhő-funkciók és képességek előnyeit is igénybe vehet.

További információ a CI és CD-ről további információt:

* [Mi a folyamatos integráció?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [Mi a folyamatos teljesítés?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

> [!Tip]  
> ![hibrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> A Microsoft Azure Stack az Azure bővítménye. Az Azure Stack hatékonyságával és innovációjával emeli a felhő-számítástechnika a helyszíni környezetet biztosít. Az egyetlen olyan hibrid felhős, amely lehetővé teszi, hogy bárhol a hibrid alkalmazások telepítése legyen.  
> 
> A tanulmány [hibrid alkalmazások kapcsolatos kialakítási szempontok](https://aka.ms/hybrid-cloud-applications-pillars) áttekinti a szoftverminőség alappillérei (elhelyezési, méretezhetőség, rendelkezésre állás, rugalmasság, kezelhetőségi és biztonsági) a kialakítása, üzembe helyezése és működtetése hibrid alkalmazások. A kialakítási szempontokat segít az alkalmazás kialakítása optimalizálása amely minimálisra csökkenti az éles környezetben kihívásokat.

## <a name="prerequisites"></a>Előfeltételek

Hozhat létre egy hibrid CI/CD-folyamat összetevőit rendelkezniük kell. A következő összetevők előkészítése időt vesz igénybe:

* Egy Azure-beli OEM vagy hardveres partner üzembe helyezhet egy éles környezetben az Azure Stack. Összes felhasználó telepítheti az Azure Stack Development Kit (ASDK).
* Az Azure Stack-operátorokról kell végeznie a következő elemek: az App Service üzembe helyezése, hozhat létre csomagokat és ajánlatokat, hozzon létre egy bérlői előfizetéshez, és a Windows Server 2016 rendszerképet.

>[!NOTE]
>Már van ezek az összetevők telepített némelyikét, hogy azok megfelelnek az összes oktatóanyag elkezdése előtt.

Ez az oktatóanyag feltételezi, hogy néhány alapvető ismeretek az Azure és az Azure Stack. Az oktatóanyag megkezdése előtt további tudnivalókért olvassa el a következő cikkeket:

* [Bevezetés az Azure-bA](https://azure.microsoft.com/overview/what-is-azure/)
* [Az Azure Stack főbb fogalmak](../operator/azure-stack-overview.md)

### <a name="azure-requirements"></a>Azure-követelmények

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
* Hozzon létre egy [Web App](https://docs.microsoft.com/azure/app-service/overview) az Azure-ban. Győződjön meg, vegye figyelembe a Web App URL-címének kell, hogy az oktatóanyag részében használni.

### <a name="azure-stack-requirements"></a>Azure Stack-követelmények

* Használja az Azure Stackkel integrált rendszer, vagy üzembe helyezése az Azure Stack Development Kit (ASDK). A ASDK telepítéséhez:
  * A [oktatóanyag: A telepítő a ASDK üzembe](../asdk/asdk-install.md) cikk nyújt részletes telepítési utasításokat.
  * Használja a [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) PowerShell-parancsprogram ASDK üzembe helyezés utáni lépések automatizálásához.

    > [!Note]
    > ASDK telepítése befejeződik, így ennek megfelelően tervezze körülbelül hét órát vesz igénybe.

  * Üzembe helyezése [App Service-ben](../operator/azure-stack-app-service-deploy.md) PaaS-szolgáltatások az Azure Stackhez.
  * Hozzon létre [csomag/ajánlatok](../operator/azure-stack-plan-offer-quota-overview.md) az Azure Stackben.
  * Hozzon létre egy [bérlői előfizetéshez](../operator/azure-stack-subscribe-plan-provision-vm.md) az Azure Stackben.
  * Webalkalmazás létrehozása a bérlői előfizetéshez. Jegyezze fel az új webes alkalmazás URL-CÍMÉT a későbbi használatra.
  * Telepítse a Windows Server 2012 virtuális gépet a bérlő az előfizetésben. Ez a kiszolgáló a lemezképfájl-kiszolgálóján, és futtassa az Azure fejlesztési és üzemeltetési szolgáltatásokat fogja használni.
* Adja meg a .NET 3.5 a Windows Server 2016 rendszerképet a virtuális gép (VM). Ez a virtuális gép, egy privát fordító-ügynökhöz az Azure Stack lesz felépítve.

### <a name="developer-tool-requirements"></a>Fejlesztői eszköz követelményei

* Hozzon létre egy [Azure fejlesztési és üzemeltetési szolgáltatások munkaterület](https://docs.microsoft.com/azure/devops/repos/tfvc/create-work-workspaces). A regisztrációs folyamat hoz létre a projekt neve **MyFirstProject**.
* [Telepítse a Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) és [jelentkezzen be az Azure DevOps-szolgáltatásokkal](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
* Csatlakozás a projekthez, és [helyben klónozhatja](https://www.visualstudio.com/docs/git/gitquickstart).

  > [!Note]
  > Az Azure Stack környezettel kell a megfelelő rendszerképek hírcsatorna-a Windows Server és SQL Server futtatásához. App Service-ben üzembe helyezett is kell rendelkeznie.

## <a name="prepare-the-private-azure-pipelines-agent-for-azure-devops-services-integration"></a>A privát Azure folyamatok ügynök előkészítése az Azure DevOps Services integrációja

### <a name="prerequisites"></a>Előfeltételek

Az Azure DevOps-szolgáltatásokkal szemben az Azure Resource Manager egy egyszerű szolgáltatás használatával hitelesíti magát. Rendelkeznie kell az Azure DevOps-szolgáltatásokkal a **közreműködői** szerepkör erőforrások kiépítése az Azure Stack-előfizetéshez.

Az alábbi lépések bemutatják, hogy mi szükséges hitelesítés konfigurálása:

1. Hozzon létre egy egyszerű szolgáltatást, vagy használjon egy meglévő egyszerű szolgáltatást.
2. Hitelesítési kulcsok számára az egyszerű szolgáltatás létrehozása.
3. Ellenőrizze az Azure Stack-előfizetéshez szerepköralapú hozzáférés-vezérlést, hogy a egyszerű szolgáltatásnév (SPN), a közreműködő szerepkör részét.
4. Az Azure DevOps-szolgáltatásokkal az Azure Stack-végpontok és egyszerű szolgáltatásnév segítségével, hozzon létre egy új szolgáltatás definíciójában.

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Tekintse meg a [egyszerű szolgáltatás létrehozása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) utasításokat az egyszerű szolgáltatás létrehozásához. Válasszon **Web App és az API** az alkalmazás típusához vagy [a PowerShell-szkripttel](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5) a cikkben leírtak szerint [hozzon létre egy meglévő szolgáltatás Azure Resource Manager-szolgáltatás kapcsolatot egyszerű ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal).

 > [!Note]  
 > Ha a parancsfájl használatával hozzon létre egy Azure Stack az Azure Resource Manager-végpontot, teljesítenie kell a a **- azureStackManagementURL** paraméter és **- environmentName** paraméter. Példa:  
> `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="create-an-access-key"></a>Hozzáférési kulcs létrehozása

Egy egyszerű szolgáltatást a hitelesítési kulcs szükséges. Az alábbi lépések segítségével hozzon létre egy kulcsot:

1. A **alkalmazásregisztrációk** az Azure Active Directoryban, válassza ki az alkalmazást.

    ![Válassza ki az alkalmazást – az Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_01.png)

2. Jegyezze fel az értékét **Alkalmazásazonosító**. Ezt az értéket fogja használni, az Azure DevOps-szolgáltatásokkal a szolgáltatásvégpont konfigurálásakor.

    ![Alkalmazás azonosítója – az Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_02.png)

3. A hitelesítési kulcs létrehozásához válassza a **Beállítások** elemet.

    ![Alkalmazásbeállítások – Azure Active Directory szerkesztése](media/azure-stack-solution-hybrid-pipeline/000_03.png)

4. A hitelesítési kulcs létrehozásához válassza a **Kulcsok** elemet.

    ![Kulcs-beállítások – Azure Active Directory konfigurálása](media/azure-stack-solution-hybrid-pipeline/000_04.png)

5. Adjon meg egy leírást a kulcshoz, és a kulcs időtartam beállítása. Ha elkészült, kattintson a **Mentés** elemre.

    ![Kulcs leírása és időtartama – az Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_05.png)

    A kulcs, a kulcs mentése után **érték** jelenik meg. Másolja ezt az értéket, mert ezt az értéket később nem kapott. Azt adja meg a **kulcs értékét** az a **Alkalmazásazonosító** jelentkezzen be az alkalmazást, hogy. A kulcs értékét, ahol az alkalmazás le tudja kérni Store.

    ![Kulcs értéke – az Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_06.png)

### <a name="get-the-tenant-id"></a>A Bérlőazonosító beszerzése

A Szolgáltatásvégpontok konfigurálásának részeként az Azure DevOps-szolgáltatásokkal igényel a **bérlőazonosító** , amely megfelel az AAD-címtárában, amelyet az Azure Stack stamp. A következő lépések segítségével a bérlői azonosító beszerzése

1. Válassza az **Azure Active Directory** elemet.

    ![Az Azure Active Directory-bérlő](media/azure-stack-solution-hybrid-pipeline/000_07.png)

2. A bérlőazonosító lekéréséhez válassza ki az Azure AD-bérlőjéhez tartozó **Tulajdonságok** elemet.

    ![Bérlő tulajdonságai – Azure Active Directory megtekintése](media/azure-stack-solution-hybrid-pipeline/000_08.png)

3. Másolja ki a **Címtár-azonosítót**. Ez az érték a bérlőazonosítója.

    ![Címtár-azonosító – az Azure Active Directory](media/azure-stack-solution-hybrid-pipeline/000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>A szolgáltatás egyszerű engedélyeket az Azure Stack-előfizetéshez erőforrásokat helyezhet üzembe

Szeretné elérni az erőforrást az előfizetésében, egy olyan szerepkörhöz kell rendelni az alkalmazást. Döntse el, melyik szerepkör jelenti a legjobb az alkalmazás engedélyeit. Az elérhető szerepkörök kapcsolatos további információkért lásd: [RBAC: Beépített szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Beállíthatja a hatókör szintjén is az előfizetés, erőforráscsoport vagy erőforrás. Alacsonyabb szintű hatókör, az engedélyek öröklődnek. Például egy alkalmazást ad hozzá egy erőforráscsoportot az Olvasó szerepkör azt jelenti, hogy azt az erőforráscsoportot és a hozzá tartozó erőforrások olvashatja.

1. Keresse meg a hatókör az alkalmazást hozzárendelni kívánt szintjét. Válassza ki például az előfizetések szintjén szerepkör hozzárendelése **előfizetések**.

    ![Válassza ki azt az előfizetést -, az Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_10.png)

2. A **előfizetés**, válassza ki a Visual Studio Enterprise.

    ![Visual Studio Enterprise - Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_11.png)

3. Válassza ki a Visual Studio Enterprise **hozzáférés-vezérlés (IAM)** .

4. Válassza ki **szerepkör-hozzárendelés hozzáadása**.

    ![Szerepkör-hozzárendelés – Azure Stack hozzáadása](media/azure-stack-solution-hybrid-pipeline/000_13.png)

5. A **engedélyek hozzáadása**, válassza ki a szerepkört, hogy az alkalmazáshoz hozzárendelni kívánt. Ebben a példában ez a **tulajdonosa** szerepkör.

    ![Tulajdonosi szerepkör engedélyekkel – Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_14.png)

6. Alapértelmezés szerint az Azure Active Directory-alkalmazások nem megjelenik az elérhető lehetőségek közül. Keresse meg az alkalmazást, meg kell adnia annak nevét a a **kiválasztása** mezőt a keresési funkciót. Válassza ki az alkalmazást.

    ![Alkalmazás keresési eredmény – Azure Stack](media/azure-stack-solution-hybrid-pipeline/000_16.png)

7. Válassza ki **mentése** befejeződik, a szerepkör hozzárendelése. Láthatja, hogy az alkalmazás az adott hatókörnél szerepkörhöz tartozó felhasználók listájában.

### <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést az Azure biztosít. Az RBAC használatával szabályozhatja, amelyek a feladataik elvégzéséhez szükséges felhasználói hozzáférési szintjét. Szerepköralapú hozzáférés-vezérléssel kapcsolatos további információkért lásd: [Azure-előfizetések erőforrásaihoz való hozzáférés kezelése](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="azure-devops-services-agent-pools"></a>Az Azure DevOps Services Ügynökkészletek

Ahelyett, hogy minden ügynök külön-külön, ügynökök is szervezheti ügynökkészletek. Az ügynökkészlet a megosztási határt a készlet az összes ügynöknek határozza meg. Az Azure DevOps-szolgáltatásokkal ügynökkészletek hatóköre a szervezet az Azure DevOps-szolgáltatásokkal, ami azt jelenti, hogy az ügynökkészlet megoszthatja projektek között. Ügynökkészletek kapcsolatos további információkért lásd: [Ügynökkészletek létrehozása és az üzenetsorok](https://docs.microsoft.com/azure/devops/pipelines/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Adjon hozzá egy személyes hozzáférési jogkivonat (PAT) az Azure Stackhez

Hozzon létre egy személyes hozzáférési tokent az Azure DevOps-szolgáltatásokhoz való hozzáférést.

1. Jelentkezzen be a szervezet az Azure DevOps-szolgáltatásokkal, és válassza ki a szervezet profil neve.

2. Válassza ki **biztonság kezeléséhez** hozzáférési token létrehozása oldalra. 

    ![Biztonság – az Azure Stack kezelése](media/azure-stack-solution-hybrid-pipeline/000_18.png)

3. Kattintson a **Hozzáadás** hozhat létre egy új személyes hozzáférési tokent.

    ![Adja hozzá az Azure Stack - használt személyes hozzáférési tokent](media/azure-stack-solution-hybrid-pipeline/000_18a.png)

    ![Azure Stack--token létrehozásához](media/azure-stack-solution-hybrid-pipeline/000_18b.png)

4. Másolja a jogkivonatot.

    > [!Note]
    > Mentse a token információkat. Ezt az információt nem tárolja, és nem jelennek meg újra amikor hagyja meg a weblapot.

    ![Azure Stack - használt személyes hozzáférési tokent](media/azure-stack-solution-hybrid-pipeline/000_19.png)

### <a name="install-the-azure-devops-services-build-agent-on-the-azure-stack-hosted-build-server"></a>Telepítse az Azure DevOps-szolgáltatásokkal fordító-ügynökhöz az üzemeltetett Azure Stack buildelési kiszolgáló

1. Csatlakozzon az Azure Stack gazdagépen telepített a lemezképfájl-kiszolgálóján.
2. Töltse le és a build-ügynök telepítése a személyes hozzáférési jogkivonat (PAT) használatával szolgáltatásként, és futtassa azt a virtuális gép rendszergazdaként

    ![A build-ügynök letöltése](media/azure-stack-solution-hybrid-pipeline/010_downloadagent.png)

3. Keresse meg azt a mappát a kibontott fordító-ügynökhöz. Futtassa a **config.cmd** fájl egy rendszergazda jogú parancssorból.

    ![Kivont fordító-ügynökhöz](media/azure-stack-solution-hybrid-pipeline/000_20.png)

    ![Regisztrálja a fordító-ügynökhöz](media/azure-stack-solution-hybrid-pipeline/000_21.png)

4. Amikor befejezi a config.cmd, további fájlok frissül a build-ügynök mappájába. A mappát a kibontott tartalma az alábbihoz hasonlóan kell kinéznie:

    ![Az ügynök mappa frissítés létrehozása](media/azure-stack-solution-hybrid-pipeline/009_token_file.png)

    Az ügynök az Azure DevOps-szolgáltatásokkal mappában tekintheti meg.

## <a name="endpoint-creation-permissions"></a>Végpont létrehozása engedélyek

Végpontok létrehozása a Visual Studio online-hoz (VSTO) build helyezzen üzembe Azure Service-alkalmazások az Azure Stackhez. Az Azure DevOps-szolgáltatásokkal a fordító-ügynökhöz, amely csatlakoztatja az Azure Stackhez csatlakozik.

![A VSTO NorthwindCloud mintaalkalmazás](media/azure-stack-solution-hybrid-pipeline/012_securityendpoints.png)

1. Jelentkezzen be a VSTO, és nyissa meg az alkalmazás beállítások oldalát.
2. A **beállítások**válassza **biztonsági**.
3. A **Azure fejlesztési és üzemeltetési szolgáltatások csoportok**válassza **végpont alkotói**.

    ![NorthwindCloud Endpoint Creators](media/azure-stack-solution-hybrid-pipeline/013_endpoint_creators.png)

4. Az a **tagok** lapon jelölje be **Hozzáadás**.

    ![Egy tag hozzáadása](media/azure-stack-solution-hybrid-pipeline/014_members_tab.png)

5. Az a **felhasználók és csoportok hozzáadása** lapon írjon be egy felhasználónevet, és válassza ki, hogy a felhasználó a felhasználók listájából.
6. Válassza ki **módosítások mentése**.
7. Az a **Azure fejlesztési és üzemeltetési szolgáltatások csoportok** listáról válassza ki **végpont rendszergazdák**.

    ![NorthwindCloud Endpoint Administrators](media/azure-stack-solution-hybrid-pipeline/015_save_endpoint.png)

8. Az a **tagok** lapon jelölje be **Hozzáadás**.
9. Az a **felhasználók és csoportok hozzáadása** lapon írjon be egy felhasználónevet, és válassza ki, hogy a felhasználó a felhasználók listájából.
10. Válassza ki **módosítások mentése**.

Most, hogy létezik a végpont adatait, az Azure DevOps-szolgáltatásokkal az Azure Stack kapcsolat készen áll a használatra. A fordító-ügynökhöz az Azure Stackben utasításokat olvas be az Azure DevOps-szolgáltatásokkal, majd az ügynök közvetíti a kommunikációt az Azure Stack-végpontjának információit.

## <a name="create-an-azure-stack-endpoint"></a>Az Azure Stack-végpont létrehozása

### <a name="create-an-endpoint-for-azure-ad-deployments"></a>Az Azure AD központi telepítésekhez végpont létrehozása

Az utasítások a [kapcsolatot hozhat létre egy Azure Resource Manager szolgáltatás egy meglévő szolgáltatás egyszerű ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) hozzon létre egy szolgáltatás-kapcsolatot egy meglévő szolgáltatás egyszerű, és használja a következő társítás a cikk:

| Name (Név) | Példa | Leírás |
| --- | --- | --- |
| Kapcsolat neve | Az Azure Stack Azure ad-ben | A kapcsolat neve. |
| Környezet | AzureStack | A környezet neve. |
| Környezet URL-címe | `https://management.local.azurestack.external` | A felügyeleti végpontra. |
| Hatókör szintjén | Előfizetés | A kapcsolat hatókörének. |
| Előfizetés azonosítója | 65710926-XXXX-4F2A-8FB2-64C63CD2FAE9 | Az Azure Stack felhasználói előfizetés azonosítója |
| Előfizetés neve | name@contoso.com | Az Azure Stack felhasználói előfizetés neve |
| Egyszerű szolgáltatás ügyfél-azonosító | FF74AACF-XXXX-4776-93FC-C63E6E021D59 | A résztvevő-Azonosítóval [ez](azure-stack-solution-pipeline.md#create-a-service-principal) szakasz ebben a cikkben. |
| Egyszerű szolgáltatásnév kulcsa | THESCRETGOESHERE= | A kulcs az ugyanebben a cikkben (vagy a jelszót, ha a szkriptet használta). |
| Bérlőazonosító | D073C21E-XXXX-4AD0-B77E-8364FCA78A94 | A Bérlőazonosító kérheti le az utasítás a következő [a Bérlőazonosító beszerzése](azure-stack-solution-pipeline.md#get-the-tenant-id).  |
| Kapcsolat: | Nincs ellenőrizve | Ellenőrizze az egyszerű szolgáltatás kapcsolati beállításokat. |

Most, hogy a végpont a létrehozás, a DevOps-kapcsolat Azure Stackhez készen áll a használatra. A fordító-ügynökhöz az Azure Stackben utasításokat olvas be a DevOps, majd az ügynök közvetíti a kommunikációt az Azure Stack-végpontjának információit.

![Az Azure AD ügynök létrehozása](media/azure-stack-solution-hybrid-pipeline/016_save_changes.png)

### <a name="create-an-endpoint-for-ad-fs"></a>Hozzon létre egy végpontot az AD FS-hez

A legújabb frissítést az Azure fejlesztési és üzemeltetési kapcsolatot hozza létre a szolgáltatás egyszerű szolgáltatás tanúsítvánnyal hitelesítési teszi lehetővé. Ez a kapcsolat nem szükséges, az Azure Stack telepítésekor és az AD FS Identitásszolgáltatóként. 

![Ügynök Active Directory összevonási szolgáltatások létrehozása](media/azure-stack-solution-hybrid-pipeline/image06.png)

Használatával a következő hozzárendelést szolgáltatás kapcsolatot hozhat létre:

| Name (Név) | Példa | Leírás |
| --- | --- | --- |
| Kapcsolat neve | Az Azure Stack ADFS | A kapcsolat neve. |
| Környezet | AzureStack | A környezet neve. |
| Környezet URL-címe | `https://management.local.azurestack.external` | A felügyeleti végpontra. |
| Hatókör szintjén | Előfizetés | A kapcsolat hatókörének. |
| Előfizetés azonosítója | 65710926-XXXX-4F2A-8FB2-64C63CD2FAE9 | Az Azure Stack felhasználói előfizetés azonosítója |
| Előfizetés neve | name@contoso.com | Az Azure Stack felhasználói előfizetés neve |
| Egyszerű szolgáltatás ügyfél-azonosító | FF74AACF-XXXX-4776-93FC-C63E6E021D59 | Az egyszerű szolgáltatás ügyfél-Azonosítót hozott létre az AD FS-hez. |
| Tanúsítvány | `<certificate>` |  A tanúsítványfájl konvertálása PFX PEM. Tanúsítvány PEM-fájl tartalmának illessze be ezt a mezőt. <br> PEM konvertálása PFX:<br>`openssl pkcs12 -in file.pfx -out file.pem -nodes -password pass:<password_here>` |
| Bérlőazonosító | D073C21E-XXXX-4AD0-B77E-8364FCA78A94 | A Bérlőazonosító kérheti le az utasítás a következő [a Bérlőazonosító beszerzése](azure-stack-solution-pipeline.md#get-the-tenant-id). |
| Kapcsolat: | Nincs ellenőrizve | Ellenőrizze az egyszerű szolgáltatás kapcsolati beállításokat. |

Most, hogy a végpont a létrehozás, az Azure DevOps-kapcsolat Azure Stackhez készen áll a használatra. A fordító-ügynökhöz az Azure Stackben utasításokat olvas be az Azure DevOps, majd az ügynök közvetíti a kommunikációt az Azure Stack-végpontjának információit.

> [!Note]
> Az Azure Resource Manager-végpont nem lesz közzétéve az interneten, ha a kapcsolat érvényesítése sikertelen lesz. Ez várható, és a egy egyszerű feladatlista-kiadási folyamatok létrehozásával ellenőrizheti a kapcsolatot. 

## <a name="develop-your-application-build"></a>Fejlesztés a sestavení aplikace

Az oktatóanyag ezen részében jelennek meg:

* Az Azure DevOps-szolgáltatásokkal projekthez adja hozzá a kódot.
* Hozzon létre önálló webes alkalmazás üzembe helyezése.
* A folyamatos üzembe helyezési folyamat beállítása

> [!Note]
 > Az Azure Stack környezettel kell a megfelelő rendszerképek hírcsatorna-a Windows Server és SQL Server futtatásához. App Service-ben üzembe helyezett is kell rendelkeznie. Tekintse meg az App Service-ben dokumentációt "Előfeltételek" szakaszban az Azure Stack-kezelők követelményeinek.

CI/CD hibrid alkalmazás kódja és az infrastruktúra kódjának alkalmazhatja. Használat [Azure Resource Manager-sablonokat, mint webes](https://azure.microsoft.com/resources/templates/) mindkét felhőben való üzembe helyezéséhez az Azure DevOps-szolgáltatásokkal kódját.

### <a name="add-code-to-an-azure-devops-services-project"></a>Adja hozzá a kódot az Azure DevOps-szolgáltatásokkal projekthez

1. Jelentkezzen be egy szervezet rendelkező projekt létrehozása az Azure Stack az Azure DevOps-szolgáltatásokkal. A következő képernyőfelvétel bemutatja, hogyan kapcsolódjon a HybridCICD.

    ![Csatlakozás egy projekthez – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/017_connect_to_project.png)

2. **A tárház klónozása** létrehozásával és az alapértelmezett webes alkalmazás megnyitásával.

    ![Klónozza a tárházat – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Önálló webes alkalmazás üzembe helyezése az App Services létrehozása az mindkét felhőben

1. Szerkessze a **WebApplication.csproj** fájlt: Válassza ki `Runtimeidentifier` majd `win10-x64.` további információkért lásd: [önálló telepítés](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentációját.

    ![Runtimeidentifier konfigurálása](media/azure-stack-solution-hybrid-pipeline/019_runtimeidentifer.png)

2. Team Explorer segítségével ellenőrizze a kódot az Azure DevOps-szolgáltatásokkal.

3. Győződjön meg arról, hogy az alkalmazáskódban az Azure DevOps-szolgáltatásokkal lett érvényesítve.

### <a name="create-the-build-pipeline"></a>A létrehozási folyamat létrehozása

1. Jelentkezzen be a szervezet, amely egy build folyamatot hozhat létre az Azure DevOps-szolgáltatásokkal.

2. Keresse meg a **webes alkalmazás készítése** a projekt lapját.

3. A **argumentumok**, adjon hozzá **- r win10-x64** kódot. Ez a lépés egy önálló telepítés a .NET Core használatával aktiválásához szükséges.

    ![Argument buildelési folyamat hozzáadása](media/azure-stack-solution-hybrid-pipeline/020_publish_additions.png)

4. Futtassa a build. A [önálló telepítés build](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) folyamat közzéteszi az összetevők, amelyek futhatnak az Azure és az Azure Stackben.

### <a name="use-an-azure-hosted-build-agent"></a>Egy Azure-ban üzemeltetett fordító-ügynökhöz használata

Használatával egy üzemeltetett fordító-ügynökhöz az Azure DevOps-szolgáltatásokkal a lehetőség egy kényelmes és webes alkalmazások üzembe. Ügynök karbantartása és frissítései automatikusan végzi a Microsoft Azure, amely lehetővé teszi a folyamatos és zavartalan fejlesztési ciklus.

### <a name="configure-the-continuous-deployment-cd-process"></a>Konfigurálja a folyamatos készregyártás (CD) folyamatot

Az Azure DevOps-szolgáltatásokkal és a Team Foundation Server (TFS) adja meg egy hatékonyan konfigurálható és kezelhető folyamat több, például fejlesztési, átmeneti, minőségbiztosítási (Tesztelés) és éles környezetek számára. Ez a folyamat tartalmazhat jóváhagyásokat igénylő az alkalmazás életciklusát az adott szakaszában.

### <a name="create-release-pipeline"></a>Kibocsátási folyamatok létrehozása

Kiadási folyamatok létrehozásába az utolsó lépés az alkalmazás létrehozása folyamatban. A kibocsátási folyamat kiadás létrehozása és üzembe helyezése a build szolgál.

1. Jelentkezzen be az Azure DevOps-szolgáltatásokkal, és navigáljon a **Azure folyamatok** a projekthez.
2. Az a **kiadásokban** lapon jelölje be  **\[ +]** majd válasszon **kiadási definíció létrehozása**.

   ![Hozzon létre a kibocsátási folyamat – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/021a_releasedef.png)

3. Az a **válasszon ki egy sablont** lapon a **Azure App Service üzembe helyezési**, majd válassza ki **alkalmaz**.

    ![A alkalmazni sablon – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/102.png)

4. A a **Hozzáadás összetevő** lapon, a **forrás (builddefiníció)** legördülő menüből, válassza ki az Azure Cloud hozhat létre alkalmazást.

    ![Összetevő - Azure-fejlesztési és üzemeltetési szolgáltatások hozzáadása](media/azure-stack-solution-hybrid-pipeline/103.png)

5. Az a **folyamat** lapon jelölje be a **1. fázis**, **1 feladat** mutató hivatkozás **környezettel kapcsolatos feladatok megtekintéséhez**.

    ![Folyamat-feladatok megtekintése – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/104.png)

6. Az a **feladatok** lapra, adja meg az Azure-t a **környezet neve** , és válassza ki az AzureCloud Traders-webalkalmazás EP a **Azure-előfizetés** legördülő listából válassza ki.

    ![Állítsa be a környezeti változókat – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/105.png)

7. Adja meg a **az Azure app service neve**, azaz a következő képernyőfelvétel-készítés "northwindtraders".

    ![App service neve – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/106.png)

8. Válassza ki az ügynök fázis **Hosted VS2017** származó a **fronta Agenta** legördülő listából válassza ki.

    ![Üzemeltetett ügynök – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/107.png)

9. A **üzembe helyezése az Azure App Service**, válassza ki a érvényes **csomag vagy a mappa** a környezethez.

    ![Válassza ki a csomag vagy a mappa – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/108.png)

10. Az a **válassza ki a fájl vagy mappa** lapra, jelölje be **OK** mappa helyét.

    ![Válassza ki a fájl vagy mappa – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/109.png)

11. Mentse az összes módosítást, és térjen vissza a **folyamat**.

    ![Mentse a módosításokat – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/110.png)

12. A a **folyamat** lapon jelölje be **Hozzáadás összetevő**, és válassza a **NorthwindCloud Traders-hajó** a a **Source (Build definíció)** legördülő listából.

    ![Adja hozzá az új összetevők – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/111.png)

13. Az a **válasszon ki egy sablont** lapon, adjon hozzá egy másik környezetre. Válasszon **Azure App Service üzembe helyezési** majd **alkalmaz**.

    ![Válassza ki a sablon – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/112.png)

14. Adja meg az "Azure Stack" a **környezetnevet**.

    ![Környezet neve – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/113.png)

15. Az a **feladatok** lapon keresse meg és válassza ki az Azure Stack.

    ![Az Azure Stack-környezet – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/114.png)

16. Az a **Azure-előfizetés** legördülő listára, válassza ki a "AzureStack Traders-hajó EP" az Azure Stack-végpont.

    ![Azure-előfizetés legördülő – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/115.png)

17. Adja meg az Azure Stack webes alkalmazás nevét, a **App service neve**.

    ![App service neve – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/116.png)

18. A **kijelölése**, "AzureStack - bDouglas részéhez" választhatok a **fronta Agenta** legördülő listából.

    ![Válassza ki az ügynök – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/117.png)

19. A **üzembe helyezése az Azure App Service**, válassza ki a érvényes **csomag vagy a mappa** a környezethez. A **fájl vagy mappa kiválasztása**válassza **OK** mappa **hely**.

    ![Válasszon csomagot vagy a mappa – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/118.png)

    ![Hagyja jóvá a hely – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/119.png)

20. Az a **változók** lapra, keresse meg a következő változót **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. A változó értékét állítsa **igaz**, és állítsa annak hatókörét **Azure Stack**.

    ![Változó - Azure-fejlesztési és üzemeltetési szolgáltatások konfigurálása](media/azure-stack-solution-hybrid-pipeline/120.png)

21. Az a **folyamat** lapon jelölje be a **a folyamatos készregyártás eseményindítója** ikonjára a NorthwindCloud Traders-webalkalmazás-összetevő és a készlet a **a folyamatos készregyártás eseményindítója** , **Engedélyezve**.  Járjon el ugyanígy "NorthwindCloud Traders-hajó" összetevőre.

    ![Állítsa be a folyamatos készregyártás eseményindítója – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/121.png)

22. Az Azure Stack-környezet, válassza a **központi telepítés előtti feltételek** ikon beállítani az eseményindítót **kiadás után**.

    ![Állítsa be a központi telepítés előtti feltételek eseményindító – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/122.png)

23. Mentse az összes módosítást.

> [!Note]
> Néhány beállítás, a feladatkiadási tevékenységeket előfordulhat, hogy automatikusan meghatározott [környezeti változók](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts#custom-variables) létrehozásakor egy kiadási folyamatot sablonból. Ezek a beállítások nem módosíthatók az a tevékenység beállításait. Azonban ezeket a beállításokat a környezetben a szülőelemektől szerkesztheti.

## <a name="create-a-release"></a>Hozzon létre egy kiadás

Most, hogy végrehajtotta a módosításokat a kiadási folyamathoz, azt az idő a üzembe helyezésének megkezdéséhez. Üzembe helyezés megkezdéséhez hozzon létre egy a kiadásra a kiadási folyamathoz. A kiadás automatikusan; előfordulhat, hogy hozható létre például, ha a folyamatos készregyártás eseményindítója van megadva a kiadási folyamathoz. A beállítás a trigger azt jelenti, hogy a Forráskód módosítása elindul egy új létrehozást, majd az új kiadás. Azonban ebben a szakaszban létre fog hozni egy új kiadási manuálisan.

1. Az a **folyamat** lap meg van nyitva a **kiadási** legördülő listából válassza ki, és válassza ki **kiadás létrehozása**.

    ![Hozzon létre egy kiadás – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/200.png)

2. Adjon meg egy leírást a kiadás, ellenőrizze, hogy, hogy a helyes összetevők vannak-e jelölve, és válassza **létrehozás**. Néhány pillanat múlva egy szalagcím jelenik meg, amely azt jelzi, hogy az új kiadásban lett létrehozva, és a kiadási név hivatkozásként jelenik meg. Válassza ki a hivatkozásra kattintva megtekintheti a kiadási összegző lapja.

    ![Kiadás létrehozása szalagcím – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/201.png)

3. A kiadási összefoglaló lapja a kiadással kapcsolatos részleteket jeleníti meg. "Release-2", az alábbi képernyőfelvételen a **környezetek** szakasz látható a **központi telepítési állapot** az Azure-t a "Folyamatban", és az Azure Stack állapota "sikeres". Ha az Azure-környezetre vonatkozó üzembe helyezési állapotra vált, "SUCCEEDED", egy szalagcím jelenik meg arról, hogy a kiadás nem áll készen a jóváhagyásra. Amikor a központi telepítés függőben, vagy sikertelen volt, a kék **(i)** információs ikon jelenik meg. A kurzort a az ikonra kattintva megtekintheti a egy előugró ablak, amely tartalmazza a késleltetés vagy a hiba okát.

    ![Kiadási összegző lap – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/202.png)

Más nézetekhez, például a listát a kiadások, is látható ikon jelzi, a jóváhagyási folyamatban. Erre az ikonra az előugró a környezet nevét, és üzembe helyezésével kapcsolatos további részleteket jeleníti meg. Könnyebbé vált a rendszergazda, lásd: a kiadások és hogy mely kiadásai jóváhagyásra várnak összesített állapotát.

### <a name="monitor-and-track-deployments"></a>A figyelő és nyomon követésére központi telepítések

Ez a szakasz bemutatja, hogyan figyelheti és nyomon követheti a központi telepítések. A kiadás a két Azure App Services-webhelyek üzembe jó példa biztosít.

1. A "Release-2" összesítő lapon válassza ki a **naplók**. A telepítés során ezen a lapon látható az élő napló az ügynöktől. A bal oldali panelen az egyes környezetekhez a központi telepítésben lévő egyes műveletek állapotát jeleníti meg.

    A személy ikonra a **művelet** oszlop ki a központi telepítés jóváhagyása (vagy elutasítása), és azokat a megadott üzenet központi telepítés előtti vagy utáni jóváhagyásra.

2. Az üzembe helyezés befejezését követően a teljes log fájl a jobb oldali ablaktáblán jelenik meg. Válassza ki bármelyik **lépés** a naplófájl az egyetlen lépésben, például a "Feladat inicializálása" megtekintéséhez a bal oldali panelen. Egyéni naplók megtekinthetik megkönnyíti a követése és hibakeresése az általános üzembe helyezési részeit. Emellett **mentése** egy lépést, a naplófájl vagy **az összes napló letöltése zip-fájlként**.

    ![Kiadási naplók – Azure DevOps-szolgáltatásokkal](media/azure-stack-solution-hybrid-pipeline/203.png)

3. Nyissa meg a **összefoglalás** lapján megtekintheti a kiadással kapcsolatos általános információkat. Ez a nézet a build, a környezetekben, telepítve van, üzembe helyezési állapota és egyéb adatainak a kiadása kapcsolatos részleteket jeleníti meg.

4. Jelöljön ki egy környezetet hivatkozást (**Azure** vagy **Azure Stack**) és a egy adott környezetben való központi telepítés meglévő kapcsolatos információk megtekintéséhez. Ezek a nézetek, győződjön meg arról, hogy az azonos buildjét mindkét környezetben telepített egyszerűen használhatja.

5. Nyissa meg a **éles-WebApp üzembe helyezését** a böngészőben. Ha például az Azure App Services-webhely URL-címre `https://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>További lépések

* Az Azure-minták Felhőkhöz kapcsolatos további információkért lásd: [tervezési minták Felhőkhöz](https://docs.microsoft.com/azure/architecture/patterns).
