---
title: Az Azure Web Apps üzembe helyezési műveletének használata az Azure Stack hub használatával
description: Az Azure Web Apps üzembe helyezési műveletének használatával folyamatos integrációs, folyamatos üzembe helyezési (CI/CD) munkafolyamatot hozhat létre Azure Stack hubhoz
author: mattbriggs
ms.topic: how-to
ms.date: 2/16/2021
ms.author: mabrigg
ms.reviewer: gara
ms.lastreviewed: 2/16/2021
ms.openlocfilehash: fec9acb7a3e156a8646aef88c53e681eed9e53da
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100655255"
---
# <a name="use-the-azure-web-app-deploy-action-with-azure-stack-hub"></a>Az Azure webalkalmazás-üzembe helyezési művelet használata az Azure Stack hub használatával

Megadhat GitHub-műveleteket a webalkalmazások üzembe helyezéséhez az Azure Stack hub-példányon. Ez lehetővé teszi az alkalmazás folyamatos integrációjának és üzembe helyezésének beállítását. Ez a cikk segítséget nyújt az automatikus üzembe helyezéshez a GitHub-műveletek és a Azure Stack hub használatával. Hozzon létre egy webalkalmazást, és a közzétételi profil használatával hozza létre a webalkalmazást az alkalmazás üzemeltetéséhez.

A GitHub-műveletek olyan műveletekből álló munkafolyamatok, amelyek lehetővé teszik az automatizálást közvetlenül a Code repositoryban. A GitHub-fejlesztési folyamat eseményeivel aktiválhatja a munkafolyamatokat. Megadhat általános DevOps-automatizálási feladatokat, például tesztelési, üzembe helyezési és folyamatos integrációt.

A példában szereplő munkafolyamat a következőket tartalmazza:
- Útmutatás az egyszerű szolgáltatásnév (SPN) létrehozásához és ellenőrzéséhez.
- Útmutató a webalkalmazás-közzétételi profil létrehozásához
- Futtatókörnyezet-specifikus Munkafolyamat hozzáadása
- Megfelelő Munkafolyamat hozzáadása a webalkalmazás-telepítéssel
## <a name="get-service-principal"></a>Egyszerű szolgáltatásnév beolvasása

Az SPN szerepköralapú hitelesítő adatokat biztosít, így az Azure-on kívüli folyamatok csatlakozni tudnak az erőforrásokhoz, és kezelhetik azokat. Szüksége lesz egy olyan SPN-re, amelynek közreműködői hozzáférése van, és az utasításokban megadott attribútumok a GitHub-műveletekhez használhatók.

Azure Stack hub felhasználója nem rendelkezik engedéllyel az egyszerű szolgáltatásnév létrehozásához. Ezt az elvet a felhő üzemeltetőjétől kell kérnie. Itt megtalálhatja az itt található utasításokat, így Ön is létrehozhatja az egyszerű szolgáltatásnevet, ha Ön Felhőbeli operátor. Ha Ön fejlesztő, akkor ellenőrizheti a felhőalapú operátor által biztosított SPN-t.

A Felhőbeli operátornak létre kell hoznia az SPN-t az Azure CLI használatával.

A következő kódrészletek egy Windows rendszerű gépre íródnak, amely az Azure CLI-vel ellátott PowerShell-parancssort használja. Ha Linux rendszerű gépen és bashen használ parancssori felületet, távolítsa el a sor kiterjesztését, vagy cserélje le őket a-re `\` .

1. Készítse elő az egyszerű szolgáltatásnév létrehozásához használt következő paraméterek értékeit:

    | Paraméter | Példa | Leírás |
    | --- | --- | --- |
    végpont – erőforrás-kezelő | "https://management.orlando.azurestack.corp.microsoft.com"  | Az erőforrás-kezelési végpont. |
    utótag – tárolás – végpont | "orlando.azurestack.corp.microsoft.com"  | A Storage-fiókok végpont-utótagja. |
    utótag – kulcstartó – DNS | ". vault.orlando.azurestack.corp.microsoft.com"  | A Key Vault szolgáltatás DNS-utótagja. |
    végpont-Active-Directory-Graph-erőforrás-azonosító | "https://graph.windows.net/"  | Az Active Directory erőforrás-azonosító. |
    végpont – SQL-kezelés | https://notsupported  | Az SQL Server felügyeleti végpontja. Beállítás beállítása a következőre `https://notsupported` |
    profil | 2019-03-01 – hibrid | A felhőhöz használni kívánt profil. |

2. Nyissa meg a parancssori eszközt, például a Windows PowerShellt vagy a bash-t, és jelentkezzen be. Használja az alábbi parancsot:

    ```azurecli  
    az login
    ```

3. `register`Ha meglévő környezetet használ, használja a parancsot egy új környezethez vagy a `update` parancshoz. Használja az alábbi parancsot.

    ```azurecli  
    az cloud register `
        -n "AzureStackUser" `
        --endpoint-resource-manager "https://management.<local>.<FQDN>" `
        --suffix-storage-endpoint ".<local>.<FQDN>" `
        --suffix-keyvault-dns ".vault.<local>.<FQDN>" `
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" `
        --endpoint-sql-management https://notsupported  `
        --profile 2019-03-01-hybrid
    ```

4. Szerezze be az SPN-hez használni kívánt előfizetés-azonosítót és erőforráscsoportot.

5. Hozza létre az egyszerű szolgáltatásnevet a következő paranccsal az előfizetés-AZONOSÍTÓval és az erőforráscsoporthoz:

    ```azurecli  
    az ad sp create-for-rbac --name "myApp" --role contributor `
        --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} `
        --sdk-auth
    ```

    Ha nem rendelkezik a Felhőbeli operátori jogosultságokkal, bejelentkezhet a felhőalapú operátor által megadott egyszerű szolgáltatásnév használatával is. Szüksége lesz az ügyfél-AZONOSÍTÓra, a titkos kulcsra és a bérlői AZONOSÍTÓra. Ezekkel az értékekkel a következő Azure CLI-parancsokkal hozhatja létre a GitHub-adattárba felvehető JSON-objektumot titkos kulcsként.

    ```azurecli  
    az login --service-principal -u "<client-id>" -p "<secret>" --tenant "<tenant-ID>" --allow-no-subscriptions
    az account show --sdk-auth
    ```

6. Keresse meg az eredményül kapott JSON-objektumot. A JSON-objektummal hozza létre a titkot a GitHub-tárházban, amely tartalmazza a műveletet. A JSON-objektumnak a következő attribútumokkal kell rendelkeznie:

    ```json
    {
      "clientId": <Application ID for the SPN>,
      "clientSecret": <Client secret for the SPN>,
      "subscriptionId": <Subscription ID for the SPN>,
      "tenantId": <Tenant ID for the SPN>,
      "activeDirectoryEndpointUrl": "https://login.microsoftonline.com/",
      "resourceManagerEndpointUrl": "https://management.<FQDN>",
      "activeDirectoryGraphResourceId": "https://graph.windows.net/",
      "sqlManagementEndpointUrl": "https://notsupported",
      "galleryEndpointUrl": "https://providers.<FQDN>:30016/",
      "managementEndpointUrl": "https://management.<FQDN>"
    }
    ```

## <a name="create-the-web-app-publish-profile"></a>A webalkalmazás-közzétételi profil létrehozása

### <a name="open-the-create-web-app-blade"></a>A webalkalmazás létrehozása panel megnyitása

1. Jelentkezzen be Azure Stack hub-portálra.
1. Válassza **az erőforrás létrehozása**  >  **web és mobil**  >  **webalkalmazás** lehetőséget.
    ![Webalkalmazás létrehozása Azure Stack hub-ban](\media\ci-cd-github-action-webapp\create-web-app.png)
### <a name="to-create-your-web-app"></a>Webalkalmazás létrehozása

1. Válassza ki az **előfizetését**.
1. Hozzon létre vagy válasszon ki egy **erőforráscsoportot**.
1. Írja be az alkalmazás **nevét** . Az alkalmazás neve megjelenik az alkalmazás URL-címében, például: `yourappname.appservice.<region>.<FQDN>`
1. Válassza ki az alkalmazás **futásidejű veremét** . A futtatókörnyezetnek meg kell egyeznie a közzétételi profil célzásához használt munkafolyamattal.
1. Válassza ki azt az **operációs rendszert** (os), amely a futtatókörnyezetet és az alkalmazást fogja üzemeltetni.
1. Válassza ki vagy írja be a Azure Stack hub-példány **régióját** .
1. Válassza ki a tervet a Azure Stack hub-példány, a régió és az App operációs rendszer alapján.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Tekintse át a webalkalmazást. Válassza a **Létrehozás** lehetőséget.
    ![Webalkalmazás áttekintése Azure Stack hub-ban](\media\ci-cd-github-action-webapp\review-azure-stack-hub-web-app.png)
1. Válassza az **Erőforrás megnyitása** lehetőséget.
    ![Közzétételi profil beolvasása az Azure Stack központban](\media\ci-cd-github-action-webapp\get-azure-stack-hub-web-app-publish-profile.png)
1. Válassza a **közzétételi profil beolvasása** elemet. A közzétételi profil letöltése és neve `<yourappname>.PublishSettings` . A fájl egy olyan XML-t tartalmaz, amely a webalkalmazás célként megadott értékeit tartalmazza.
1. Tárolja a közzétételi profilt, hogy hozzáférhessen a tárház titkainak létrehozásakor.

## <a name="add-your-secrets-to-the-repository"></a>A titkok hozzáadása a tárházhoz

A GitHub Secrets használatával titkosíthatja a műveletekben használt bizalmas adatokat. Létre fog hozni egy titkos kulcsot, amely tartalmazza az SPN-t és egy másik titkot, hogy tartalmazza a webalkalmazás-közzétételi profilt, hogy a művelet bejelentkezzen a Azure Stack hub-példányba, és felépítse az alkalmazást a webalkalmazási célra.

1. Nyisson meg vagy hozzon létre egy GitHub-tárházat. Ha segítségre van szüksége a tárház létrehozásához a GitHubban, [a GitHub-docs utasításait](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/create-a-repo)is megtalálhatja.
1. Válassza a **Beállítások** lehetőséget.
1. Válassza a **titkok** lehetőséget.
1. Válassza az **új adattár titkot**.
    ![A GitHub-műveletek titkos kódjának hozzáadása](.\media\ci-cd-github-action-login-cli\github-action-secret.png)
1. Nevezze el a titkot `AZURE_CREDENTIALS` .
1. Illessze be az SPN-t jelölő JSON-objektumot.
1. Válassza az **Add secret** (Titkos kód hozzáadása) lehetőséget.
1. Válassza az **új adattár titkot**.
1. Nevezze el a titkot `AZURE_WEBAPP_PUBLISH_PROFILE` .
1. Nyisson meg `<yourappname>.PublishSettings` egy szövegszerkesztőben, majd másolja és illessze be az XML-fájlt a tárház titkos kódjába.
1. Válassza az **Add secret** (Titkos kód hozzáadása) lehetőséget.

## <a name="add-a-runtime-workflow"></a>Futásidejű Munkafolyamat hozzáadása

1. Válasszon sablont a webalkalmazás-futtatókörnyezet táblájában.

    | Futtatókörnyezet | Sablon |
    | --- | --- |
    | DotNet | [DotNet. YML](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/asp.net-core-webapp-on-azure.yml) |
    | NodeJS | [Node. YML](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/node.js-webapp-on-azure.yml) |
    | Java | [java_jar. YML](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/java-jar-webapp-on-azure.yml) |
    | Java | [java_war. YML](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/java-war-webapp-on-azure.yml) |
    | Python | [Python. YML](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/python-webapp-on-azure.yml) |
    | PHP | [php. YML](https://github.com/Azure/actions-workflow-samples/blob/master/AppService/php-webapp-on-azure.yml) |
    | Docker | [Docker. YML](https://github.com/Azure/actions-workflow-samples/blob/master/AppService/docker-webapp-container-on-azure.yml) |

2. Helyezze el a sablon GitHub-műveleti munkafolyamat könyvtárát a projekt adattárában: `.github/workflows/<runtime.yml>` a munkafolyamat könyvtára két munkafolyamatot fog tartalmazni.

## <a name="add-the-web-app-deploy-action"></a>A webalkalmazás-üzembe helyezési művelet hozzáadása

Hozzon létre egy második munkafolyamatot a szakasz YAML használatával. Ebben a példában egy Python-webalkalmazást telepítünk. A munkafolyamat alapján ki kell választania egy telepítési műveletet. A művelet létrehozásához szükséges lépéseket követve megkeresheti a táblázat különböző futtatókörnyezetei műveleteinek beállítására vonatkozó hivatkozásokat, a [különböző futtatókörnyezetek telepítési műveleteit](#setup-actions-for-different-runtimes).

### <a name="example-github-action-workflow"></a>Példa GitHub-műveleti munkafolyamatra

1. Nyissa meg a GitHub-tárházat. Ha még nem tette hozzá a webalkalmazás-alkalmazás erőforrásait, adja hozzá őket. Ebben a példában a [Python-lombikot Hello World](https://github.com/Azure-Samples/python-docs-hello-world) mintát használom, és felvettem a Pythont, `.gitignore` és a `app.py` `requirements.txt` fájlokat.

    ![Példában a Python-lombikot Azure Stack hub használatával](\media\ci-cd-github-action-webapp\example-of-repo.png)
1. Válassza a **műveletek** lehetőséget.
1. Válassza az **Új munkafolyamat** lehetőséget.
    - Ha ez az első munkafolyamat, válassza a munkafolyamat **beállítása** a **munkafolyamat-sablon választása** alatt lehetőséget.
    - Ha már rendelkezik munkafolyamatokkal, válassza az **Új munkafolyamat**  >  **saját munkafolyamat beállítása** lehetőséget.

4. Az elérési úton nevezze el a fájlt `workflow.yml` .
5. Másolja és illessze be a munkafolyamat YML.
    ```yaml  
    # .github/workflows/worfklow.yml
    on: push

    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout Github Action' 
          uses: actions/checkout@master
    
        - name: Setup Python 3.6
          uses: actions/setup-node@v1
          with:
            python-version: '3.6'
        - name: 'create a virtual environment and install dependencies'
          run: |
            python3 -m venv .venv
            source .venv/bin/activate
            pip install -r requirements.txt
    
        - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with:
            app-name: <YOURAPPNAME>
            publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
    ```

6. A munkafolyamat. YAML frissítése az `<YOURAPPNAME>` alkalmazás nevével.
7. Válassza a **Start commit** (Véglegesítés indítása) lehetőséget.
8. Adja hozzá a commit title és a nem kötelező adatokat, majd válassza az **új fájl véglegesítés** lehetőséget.


### <a name="setup-actions-for-different-runtimes"></a>Különböző futtatókörnyezetek telepítési műveletei

Az alkalmazás kódjának egy adott nyelvi környezetben való létrehozásához használja a telepítési műveleteket:

|  Futtatókörnyezet | Műveletek beállítása |
|------------|---------|
| DotNet     | [A telepítő DotNet](https://github.com/actions/setup-dotnet) |
| NodeJS     | [Telepítési csomópont](https://github.com/actions/setup-node) |
| Java     | [Java beállítása  ](https://github.com/actions/setup-java) |
| Python     | [A Python telepítése ](https://github.com/actions/setup-python) |
| Docker | [Docker – bejelentkezés ](https://github.com/Azure/docker-login) |

A bejelentkezési művelet elvégzése után a munkafolyamat következő műveletei olyan feladatokat végezhetnek el, mint például a tárolók kiépítése, címkézése és kitolása. További információkért tekintse meg az [Azure WebApp művelet](https://github.com/marketplace/actions/azure-webapp)dokumentációját.
## <a name="trigger-your-deployment"></a>Az üzemelő példány elindítása

A művelet futtatásakor ellenőrizze, hogy sikeresen lefutott-e.

1. Nyissa meg a GitHub-tárházat. A munkafolyamatot elindíthatja, ha lenyomja a tárházat.
1. Válassza a **műveletek** lehetőséget.
1. Válassza ki a végrehajtás nevét az **összes munkafolyamatban**. Mindkét munkafolyamat naplózta az állapotát.
    ![A GitHub-művelet állapotának áttekintése](\media\ci-cd-github-action-webapp\workflow-success.png)
1. Válassza ki a központi telepítéshez tartozó feladatoknak a nevét `.github/workflows/workflow.yml` .
1. Bontsa ki a szakaszt a munkafolyamat-műveletek visszatérési értékeinek áttekintéséhez. Keresse meg az üzembe helyezett webalkalmazás URL-címét.
    ![Az Azure Stack hub webalkalmazás URL-címének megkeresése](\media\ci-cd-github-action-webapp\review-cli-log-and-get-url.png)
1. Nyisson meg egy webböngészőt, és töltse be az URL-címet.
## <a name="next-steps"></a>Következő lépések

- További műveleteket a [GitHub piactéren](https://github.com/marketplace)találhat.
- További információ [az Azure stack hub általános telepítéséről](azure-stack-dev-start-deploy-app.md)  
- Tudnivalók a [Azure stack Hub Azure Resource Manager sablonjainak használatáról](azure-stack-arm-templates.md)  
- Tekintse át a DevOps Hybrid Cloud Pattern, [DevOps minta](https://docs.microsoft.com/hybrid/app-solutions/pattern-cicd-pipeline)
