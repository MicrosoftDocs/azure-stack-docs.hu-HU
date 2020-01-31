---
title: AI-alapú lépés hangja-észlelési megoldás üzembe helyezése az Azure és Azure Stack hub használatával
description: Ismerje meg, hogyan helyezhet üzembe egy lépés hangja-észlelési megoldást az Azure és az Azure Stack hub használatával. Ez a megoldás a látogatói forgalom elemzésére szolgál a kiskereskedelmi áruházakban.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: d81fdf315e9bc834c46161d901d6ac10d736f7ab
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875300"
---
# <a name="deploy-an-ai-based-footfall-detection-solution-using-azure-and-azure-stack-hub"></a>AI-alapú lépés hangja-észlelési megoldás üzembe helyezése az Azure és Azure Stack hub használatával

Ez a cikk azt ismerteti, hogyan helyezhet üzembe olyan megoldást, amely az Azure, az Azure Stack hub és a Custom Vision AI fejlesztői csomag használatával bepillantást nyerhet a valós világbeli műveletekkel.

Ebben a megoldásban a következőket sajátíthatja el:

> [!div class="checklist"]
> - Felhőbeli natív alkalmazáscsomag (CNAB-EK) üzembe helyezése az Edge-ben. 
> - Felhőbeli határokat átölelő alkalmazás üzembe helyezése.
> - Használja a Custom Vision AI dev Kit for következtetést a szélén.

> [!Tip]  
> ![Hybrid-Pillars. png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack hub az Azure kiterjesztése. Azure Stack hub a felhő-számítástechnika rugalmasságát és innovációját a helyszíni környezetbe helyezi, így az egyetlen hibrid felhő, amely lehetővé teszi a hibrid alkalmazások bárhol történő létrehozását és üzembe helyezését.  
> 
> A [hibrid alkalmazásokkal kapcsolatos tervezési szempontok](overview-app-design-considerations.md) a szoftverek minőségének (elhelyezés, skálázhatóság, rendelkezésre állás, rugalmasság, kezelhetőség és biztonság) pilléreit tekintik át a hibrid alkalmazások tervezéséhez, üzembe helyezéséhez és üzemeltetéséhez. A kialakítási szempontok segítik a hibrid alkalmazások kialakításának optimalizálását, ami minimalizálja az éles környezetekben felmerülő kihívásokat.

## <a name="prerequisites"></a>Előfeltételek 

Az üzembe helyezési útmutató első lépéseinek megkezdése előtt győződjön meg arról, hogy:

- Tekintse át a [lépés hangja-észlelési megoldás áttekintését](pattern-retail-footfall-detection.md) 
- Felhasználói hozzáférés beszerzése egy Azure Stack hub Development Kit (ASDK) vagy Azure Stack hub integrált rendszer-példányhoz a következővel:
  - A [Azure stack hub erőforrás-szolgáltatón telepített Azure app Service](../operator/azure-stack-app-service-overview.md) . Az Azure Stack hub-példányhoz a kezelőhöz való hozzáférésre van szükség, vagy a telepítéshez a rendszergazdával kell dolgoznia.
  - App Service-és tárolási kvótát biztosító ajánlat előfizetése. Ajánlat létrehozásához operátori hozzáférésre van szükség.
- Azure-előfizetéshez való hozzáférés beszerzése
  - Ha nem rendelkezik Azure-előfizetéssel, regisztráljon az [ingyenes próbaverziós fiókra](https://azure.microsoft.com/free/) , mielőtt megkezdené.
- Hozzon létre két egyszerű szolgáltatásnevet a címtárban:
  - Az egyik az Azure-erőforrásokkal való használatra van konfigurálva, az Azure-előfizetések hatókörén keresztül. 
  - Egy konfigurált Azure Stack hub-erőforrásokkal való használatra, az Azure Stack hub-előfizetés hatókörében való hozzáféréssel. 
  - Az egyszerű szolgáltatások létrehozásával és a hozzáférés engedélyezésével kapcsolatos további tudnivalókért lásd: [alkalmazás-identitás használata az erőforrásokhoz való hozzáféréshez](../operator/azure-stack-create-service-principals.md). Ha szívesebben szeretné használni az Azure CLI-t, tekintse meg [Az Azure-szolgáltatás létrehozása az Azure CLI-vel](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)című témakört.
- Azure Cognitive Services üzembe helyezése az Azure-ban vagy Azure Stack hub-ban.
  - Először is tájékozódjon [Cognitive Servicesról](https://azure.microsoft.com/services/cognitive-services/).
  - Ezután látogasson el az [Azure Cognitive Services üzembe helyezése Azure stack hubhoz](../user/azure-stack-solution-template-cognitive-services.md) az Azure stack hub-beli Cognitive Services üzembe helyezéséhez. Először regisztrálnia kell az előzetes verzióhoz való hozzáféréshez.
- A nem konfigurált Azure Custom Vision AI fejlesztői csomag klónozása vagy letöltése. Részletekért tekintse meg a következő témakört: [AI fejlesztői készlet](https://azure.github.io/Vision-AI-DevKit-Pages/).
- Regisztráljon Power BI fiókra.
- Az Azure Cognitive Services Face API az előfizetési kulcs és a végpont URL-címe. A [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api) ingyenes próbaverzióját is elérheti. Vagy kövesse a [Cognitive Services fiók létrehozása](/azure/cognitive-services/cognitive-services-apis-create-account)című témakör utasításait.
- Telepítse a következő fejlesztői erőforrásokat:
  - [Azure CLI 2.0](../user/azure-stack-version-profiles-azurecli2.md)
  - [Docker CE](https://hub.docker.com/search/?type=edition&offering=community).
  - [Porter](https://porter.sh/). A Porter használatával Felhőbeli alkalmazásokat helyezhet üzembe az Ön számára biztosított CNAB-csomagbeli jegyzékfájlok használatával.
  - [Visual Studio Code](https://code.visualstudio.com/)
  - [A Visual Studio Code-hoz készült Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  - [Python-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - [Python](https://www.python.org/)

## <a name="deploy-the-hybrid-cloud-application"></a>A hibrid felhőalapú alkalmazás üzembe helyezése

Először a Porter CLI használatával hozzon létre egy hitelesítőadat-készletet, majd telepítse a felhőalapú alkalmazást.  

1. A megoldáshoz tartozó mintakód klónozása vagy letöltése https://github.com/azure-samples/azure-intelligent-edge-patterns ból. 

1. A Porter hitelesítő adatokat állít elő, amelyek automatizálják az alkalmazás üzembe helyezését. A hitelesítő adatok generálására szolgáló parancs futtatása előtt győződjön meg arról, hogy a következők állnak rendelkezésre:

    - Az Azure-erőforrások elérésére szolgáló egyszerű szolgáltatás, beleértve az egyszerű szolgáltatásnév, a kulcs és a bérlői DNS-t.
    - Az Azure-előfizetéshez tartozó előfizetés-azonosító.
    - Egy egyszerű szolgáltatásnév, amely az Azure Stack hub erőforrásainak elérésére szolgál, beleértve az egyszerű szolgáltatás AZONOSÍTÓját, kulcsát és a bérlői DNS-t.
    - Az Azure Stack hub-előfizetés előfizetés-azonosítója.
    - Az Azure Cognitive Services Face API kulcs-és erőforrás-végpont URL-címét.

1. Futtassa a Porter hitelesítő adatok létrehozásának folyamatát, és kövesse az utasításokat:

   ```porter
   porter creds generate --tag intelligentedge/footfall-cloud-deployment:0.1.0
   ```

1. A Porternek a futtatandó paramétereket is meg kell adnia. Hozzon létre egy paraméter szöveges fájlt, és adja meg a következő név/érték párokat. Ha segítségre van szüksége a szükséges értékekkel kapcsolatban, kérdezze meg Azure Stack hub-rendszergazdáját.

   > [!NOTE] 
   > A `resource suffix` érték annak biztosítására szolgál, hogy a központi telepítési erőforrások egyedi névvel rendelkezzenek az Azure-ban. Nem lehet hosszabb 8 karakternél, és csak egyedi betűkből és számokból állhat.

    ```
    azure_stack_tenant_arm="Your Azure Stack Hub tenant endpoint"
    azure_stack_storage_suffix="Your Azure Stack Hub storage suffix"
    azure_stack_keyvault_suffix="Your Azure Stack Hub keyVault suffix"
    resource_suffix="A unique string to identify your deployment"
    azure_location="A valid Azure region"
    azure_stack_location="Your Azure Stack Hub location identifier"
    powerbi_display_name="Your first and last name"
    powerbi_principal_name="Your Power BI account email address"
    ```
   Mentse a szövegfájlt, és jegyezze fel az elérési útját.

1. Most már készen áll a hibrid felhőalapú alkalmazás üzembe helyezésére a Porter használatával. Futtassa az install parancsot, és figyelje meg, hogy az erőforrások üzembe helyezése az Azure-ban és Azure Stack hub:

    ```porter
    porter install footfall-cloud –tag intelligentedge/footfall-cloud-deployment:0.1.0 –creds footfall-cloud-deployment –param-file "path-to-cloud-parameters-file.txt"
    ```

1. Az üzembe helyezés befejezése után jegyezze fel a következő értékeket:
    - A kamera csatlakoztatási karakterlánca.
    - A rendszerkép Storage-fiókjának kapcsolatainak karakterlánca.
    - Az erőforráscsoport neve.

## <a name="prepare-the-custom-vision-ai-dev-kit"></a>A Custom Vision AI fejlesztői csomag előkészítése

Ezután állítsa be a Custom Vision AI fejlesztői csomagot, ahogyan az a [jövőkép AI fejlesztői készlet](https://azure.github.io/Vision-AI-DevKit-Pages/docs/quick_start/)rövid útmutatójában látható. A kamerát úgy is beállíthatja és tesztelheti, hogy az előző lépésben megadott kapcsolatok sztringjét használja.

## <a name="deploy-the-camera-application"></a>A kamera alkalmazás üzembe helyezése

A Porter CLI használatával hozzon létre egy hitelesítőadat-készletet, majd telepítse a kamera alkalmazást.

1. A Porter hitelesítő adatokat állít elő, amelyek automatizálják az alkalmazás üzembe helyezését. A hitelesítő adatok generálására szolgáló parancs futtatása előtt győződjön meg arról, hogy a következők állnak rendelkezésre:
    
    - Az Azure-erőforrások elérésére szolgáló egyszerű szolgáltatás, beleértve az egyszerű szolgáltatásnév, a kulcs és a bérlői DNS-t.
    - Az Azure-előfizetéshez tartozó előfizetés-azonosító.
    - A Felhőbeli alkalmazás üzembe helyezésekor megadott Rendszerképbeli Storage-fiók kapcsolódási karakterlánca.

1. Futtassa a Porter hitelesítő adatok létrehozásának folyamatát, és kövesse az utasításokat:

    ```porter
    porter creds generate --tag intelligentedge/footfall-camera-deployment:0.1.0
    ```

1. A Porternek a futtatandó paramétereket is meg kell adnia. Hozzon létre egy paraméter szöveges fájlt, és adja meg a következő szöveget. Ha nem ismeri a szükséges értékeket, kérdezze meg Azure Stack hub-rendszergazdáját.

    > [!NOTE]
    > A `deployment suffix` érték annak biztosítására szolgál, hogy a központi telepítési erőforrások egyedi névvel rendelkezzenek az Azure-ban. Nem lehet hosszabb 8 karakternél, és csak egyedi betűkből és számokból állhat.

    ```
    iot_hub_name="Name of the IoT Hub deployed"
    deployment_suffix="Unique string here"
    ```

    Mentse a szövegfájlt, és jegyezze fel az elérési útját.

4. Most már készen áll a kamera alkalmazás üzembe helyezésére a Porter használatával. Futtassa az install parancsot, és figyelje meg, hogy a IoT Edge központi telepítés létrejött.

    ```porter
    porter install footfall-camera –tag intelligentedge/footfall-camera-deployment:0.1.0 –creds footfall-camera-deployment –param-file "path-to-camera-parameters-file.txt"
    ```

5. Győződjön meg arról, hogy a kamera üzembe helyezése befejeződött, ha megtekinti a `https://<camera-ip>:3000/`található kamera-hírcsatornát, ahol a `<camara-ip>` a kamera IP-címe. Ez a lépés akár 10 percet is igénybe vehet.

## <a name="configure-azure-stream-analytics"></a>Azure Stream Analytics konfigurálása

Most, hogy az adatok a kamerából Azure Stream Analyticsnek, manuálisan engedélyeznie kell, hogy kommunikáljon a Power BIval.

1.  A Azure Portal nyissa meg az **összes erőforrást**és a *process-lépés hangja\[yoursuffix\]* feladatot.

2.  A Stream Analytics-feladat panel **Feladattopológia** szakaszában válassza a **Kimenetek** lehetőséget.

3.  Válassza ki a **forgalom** kimeneti kimenetét.

4.  Válassza az **Engedélyezés megújítása** lehetőséget, majd jelentkezzen be Power bi-fiókjába.
    
    ![engedélyezési kérés megújítása](./media/solution-deployment-guide-retail-footfall-detection/image2.png)

5.  Mentse a kimeneti beállításokat.

6.  Lépjen az **Áttekintés** panelre, és válassza az **Indítás** lehetőséget az adatok Power BIba való küldésének megkezdéséhez.

7.  Válassza a **Most** beállítást a feladatkimenet kezdési idejeként, majd válassza az **Indítás** lehetőséget. A feladat állapotát az értesítési sávban tekintheti meg.

## <a name="create-a-power-bi-dashboard"></a>Power BI irányítópult létrehozása

1.  Miután a feladat sikeresen végre lett hajtva, lépjen a [Power BI](https://powerbi.com/)-ba, és lépjen be munkahelyi vagy iskolai fiókjával. Ha az Stream Analytics feladatok lekérdezése az eredményeket jeleníti meg, a létrehozott *lépés hangja-adatkészlet* az **adatkészletek** lapon található.

2.  A Power BI munkaterületen válassza a **+ Létrehozás** elemet a *lépés hangja Analysis* nevű új irányítópult létrehozásához.

3.  Válassza a **Csempe felvétele** lehetőséget az ablak tetején. Ezután válassza az **Egyedi folyamatos átviteli adatok**, majd a **Tovább** lehetőséget. Válassza ki a **lépés hangja** az **adatkészletek alatt.** Válassza a **kártya** lehetőséget a **vizualizáció típusa** legördülő listából, és adja hozzá a **kor** **mezőt a mezőkhöz**. Kattintson a **Tovább** gombra, és nevezze el a csempét, majd kattintson az **Alkalmaz** elemre a csempe létrehozásához.

4.  Igény szerint további mezőket és kártyákat is hozzáadhat.

## <a name="test-your-solution"></a>A megoldás tesztelése

Figyelje meg, hogy az Power BI-ben létrehozott kártyákon lévő adatmennyiségek hogyan jelennek meg a kamera előtt. A következtetések a rögzítés után akár 20 másodpercet is igénybe vehetnek.

## <a name="remove-your-solution"></a>Megoldás eltávolítása

Ha el szeretné távolítani a megoldást, futtassa a következő parancsokat a Porter használatával, ugyanazokkal a paraméterekkel, amelyeket az üzembe helyezéshez hozott létre: 

```porter
porter uninstall footfall-cloud –tag intelligentedge/footfall-cloud-deployment:0.1.0 –creds footfall-cloud-deployment –param-file "path-to-cloud-parameters-file.txt"

porter uninstall footfall-camera –tag intelligentedge/footfall-camera-deployment:0.1.0 –creds footfall-camera-deployment –param-file "path-to-camera-parameters-file.txt"
```
## <a name="next-steps"></a>Következő lépések

- További információ a [hibrid felhőalapú alkalmazások kialakításával kapcsolatos szempontokról](overview-app-design-considerations.md)
- Tekintse át és javasolja [a githubon a minta kódjának](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/footfall-analysis)tökéletesítését.
