---
title: Azure-beli bejelentkezési művelet használata az Azure CLI-vel és a PowerShell-lel Azure Stack központban
description: Azure-beli bejelentkezési művelet az Azure CLI-vel és a PowerShell-lel a folyamatos integrációs, folyamatos üzembe helyezési (CI/CD) munkafolyamatok létrehozásához Azure Stack hub-on
author: mattbriggs
ms.topic: how-to
ms.date: 1/11/2021
ms.author: mabrigg
ms.reviewer: gara
ms.lastreviewed: 1/11/2021
ms.openlocfilehash: 1421917f870d09d61f665a2cee6eb9b617ae75f3
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187350"
---
# <a name="use-the-azure-login-action-with-azure-cli-and-powershell-on-azure-stack-hub"></a>Azure-beli bejelentkezési művelet használata az Azure CLI-vel és a PowerShell-lel Azure Stack központban

Megadhat GitHub-műveleteket a Azure Stack hub-példányba való bejelentkezéshez, a PowerShell futtatásához, majd egy Azure CLI-parancsfájl futtatásához. Ezt használhatja a folyamatos integráció, a folyamatos üzembe helyezés (CI/CD) munkafolyamatának alapjaként a megoldásához Azure Stack hub használatával. Ezzel a munkafolyamattal automatizálhatja a megoldás összeállítását, tesztelését és üzembe helyezését, így a kód írásához is koncentrálhat. Például egy másik művelet hozzáadásával felhasználhatja ezt a munkafolyamatot egy Azure Resource Manager sablonnal egy virtuális gép kiépítéséhez, érvényesítheti az alkalmazás-tárházat, majd üzembe helyezhet egy alkalmazást az adott virtuális gépre, amikor a GitHub adott ágában egyesít. Ez a cikk most segítséget nyújt a GitHub-műveletekhez és Azure Stack hub-hoz való igazodás megszerzéséhez.

A GitHub-műveletek olyan műveletekből álló munkafolyamatok, amelyek lehetővé teszik az automatizálást a programkód-tárházon belül. A GitHub-fejlesztési folyamat eseményeivel aktiválhatja a munkafolyamatokat. Elvégezheti a gyakori DevOps-automatizálási feladatokat, például a tesztelést, az üzembe helyezést és a folyamatos integrációt.

Ha a GitHub-műveleteket Azure Stack hubhoz szeretné használni, konkrét követelményekkel kell használnia egy egyszerű szolgáltatásnevet (SPN). Ebben a cikkben egy *saját* üzemeltetésű futtatót fog létrehozni. A GitHub segítségével bármely olyan gépet használhat, amelyet a GitHub a GitHub-műveletekben elérhetővé tehet. Létrehozhat egy virtuális gépet (VM) az Azure-ban, Azure Stack hub-ban vagy máshol is.

A példában szereplő munkafolyamat a következőket tartalmazza:
- Útmutatás az egyszerű szolgáltatásnév létrehozásához és ellenőrzéséhez.
- Windows 2016 Server Core rendszerű gép konfigurálása a saját üzemeltetésű, saját üzemeltetésű futtatók számára az Azure Stack hub használatával való együttműködéshez.
- Olyan munkafolyamat, amely a következőket használja:
    - Az Azure bejelentkezési művelete
    - A PowerShell-parancsfájl művelete

### <a name="azure-stack-hub-github-actions"></a>Azure Stack hub GitHub-műveletei

Az alábbi ábrán a különböző környezetek és azok kapcsolatai láthatók.

![Azure Stack hub GitHub műveleti ](.\media\ci-cd-github-action-login-cli\ash-github-actions-v1d1.svg) részei a saját üzemeltetésű Runner használatával:

- Githubon futó GitHub-műveletek
- Saját üzemeltetésű, az Azure-on üzemeltetett futó
- Azure Stack Hub

A GitHub-műveletek Azure Stack hubhoz való használatának korlátozása az, hogy a folyamathoz a webes kapcsolathoz csatlakoztatott Azure Stack hub használatára van szükség. A munkafolyamat egy GitHub-tárházban aktiválódik. A Azure Active Directory (Azure AD) vagy az Active Directory összevont szolgáltatások (AD FS) is használhatók az identitás-szolgáltatóként.

Bár ez a cikk hatálya kívül esik, a saját üzemeltetésű futói virtuális magánhálózat használatával is csatlakozhatnak a Azure Stack hubhoz a tűzfal mögött.

## <a name="get-service-principal"></a>Egyszerű szolgáltatásnév beolvasása

Az SPN szerepköralapú hitelesítő adatokat biztosít, így az Azure-on kívüli folyamatok csatlakozni tudnak az erőforrásokhoz, és kezelhetik azokat. Szüksége lesz egy olyan SPN-re, amelynek közreműködői hozzáférése van, és az utasításokban megadott attribútumok a GitHub-műveletekhez használhatók.

Azure Stack hub felhasználója nem rendelkezik az egyszerű szolgáltatásnév létrehozásához szükséges engedéllyel. Ezt az elvet a felhő üzemeltetőjétől kell kérnie. Itt megtalálhatja az itt található utasításokat, így az egyszerű szolgáltatásnevet létrehozhatja, ha Ön egy felhőalapú operátor, vagy ha Ön a felhőalapú operátor által nyújtott munkafolyamatban SPN-t használó fejlesztőnek tekinti az SPN-t.

A Felhőbeli operátornak létre kell hoznia az SPN-t az Azure CLI használatával.

A következő kódrészletek egy Windows rendszerű gépre íródnak, amely az Azure CLI-vel ellátott PowerShell-parancssort használja. Ha Linux rendszerű gépen és bashen használ parancssori felületet, távolítsa el a sor kiterjesztését, vagy cserélje le őket a következőre: `\` .

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

## <a name="add-service-principal-to-repository"></a>Egyszerű szolgáltatásnév hozzáadása adattárhoz

A GitHub Secrets használatával titkosíthatja a műveletekben használt bizalmas adatokat. Létre fog hozni egy titkos kulcsot, amely tartalmazza az SPN-t, hogy a művelet bejelentkezzen a Azure Stack hub-példányba.

> [!WARNING]  
> A GitHub azt javasolja, hogy ne használja a saját üzemeltetésű, nyilvános adattárakkal rendelkező, saját üzemeltetésű futókat a saját maga által üzemeltetett futtató gépen, és hozzon létre egy lekéréses kérelmet, amely végrehajtja a kódot egy munkafolyamatban. További információ: "saját üzemeltetésű[futók](https://docs.github.com/en/free-pro-team@latest/github/automating-your-workflow-with-github-actions/about-self-hosted-runners#self-hosted-runner-security-with-public-repositories)".

1. Nyisson meg vagy hozzon létre egy GitHub-tárházat. Ha segítségre van szüksége a tárház létrehozásához a GitHubban, [a GitHub-docs utasításait](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/create-a-repo)is megtalálhatja.
1. Állítsa a tárházat magánjellegűre.
    1. Válassza a **Beállítások**  >  **változás adattár láthatóság** lehetőséget.
    1. Válassza a **magánjellegűvé tétele** lehetőséget.
    1. Írja be a tárház nevét.
    1. Válassza **a megértettem lehetőséget, módosítsa a tárház láthatóságát**.
1. Válassza a **Beállítások** lehetőséget.
1. Válassza a **titkok** lehetőséget.
1. Válassza az **új adattár titkot**.
    ![A GitHub-műveletek titkos kódjának hozzáadása](.\media\ci-cd-github-action-login-cli\github-action-secret.png)
1. Nevezze el a titkot `AZURE_CREDENTIALS` .
1. Illessze be az SPN-t jelölő JSON-objektumot.
1. Válassza az **Add secret** (Titkos kód hozzáadása) lehetőséget.

## <a name="create-your-vm-and-install-prerequisites"></a>Hozza létre a virtuális gépet, és telepítse az előfeltételeket

1. Hozza létre saját üzemeltetésű futtatójét. 

    Ezek az utasítások Windows rendszerű virtuális gépekként hoznak létre egy futót az Azure-ban. Ha egy adatközpontban üzemeltetett Azure Stack hubhoz szeretne csatlakozni, VPN-kapcsolatra lehet szükség. A kapcsolat engedélyezéséről a következő szakaszban talál útmutatást: [Azure stack hub-eszközök telepítése a saját](#optional-install-azure-stack-hub-tools-on-your-self-hosted-runner) üzemeltetésű virtuális gépen, amely VPN-kapcsolatra lehet szükség.
    - A Windows rendszerű virtuális gépek Azure-beli létrehozásával kapcsolatos útmutatásért lásd: gyors útmutató [: Windowsos virtuális gép létrehozása a Azure Portal](/azure/virtual-machines/windows/quick-create-portal). Ha követi ezeket az utasításokat, telepítse a Windows Server 2016 Core-ot.
    - A Windows rendszerű virtuális gépek Azure Stack hub-beli létrehozásával kapcsolatos útmutatásért lásd: gyors útmutató [: Windows Server rendszerű virtuális gép létrehozása az Azure stack hub Portalon](./azure-stack-quick-windows-portal.md). Ha követi ezeket az utasításokat, telepítse a Windows Server 2016 Core-ot.
1. Távoli kapcsolat használata a Windows 2016 kiszolgálóhoz való csatlakozáshoz a számítógép létrehozásakor megadott kiszolgálói IP-cím, Felhasználónév és jelszó használatával.
1. Telepítse a chocolatey-t. A chocolatey egy Windowsos csomagkezelő, amely a parancssorból való függőségek telepítésére és kezelésére használható. Egy rendszergazda jogú PowerShell-parancssorba írja be a következőt:
    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
    ```
1. Telepítse a PowerShell Core-t. Egy rendszergazda jogú PowerShell-parancssorba írja be a következőt:
    ```powershell  
    choco install powershell-core
    ```
1. Telepítse az Azure CLI-t. Egy rendszergazda jogú PowerShell-parancssorba írja be a következőt:
    ```powershell  
    choco install azure-cli
    ```
1. Telepítse Azure Stack hub PowerShellt. Egy rendszergazda jogú PowerShell-parancssorba írja be a következőt:
    ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

    Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
    Install-AzProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease
    ```
    Az Azure Stack hub az modulok használatával kapcsolatos további információkért lásd: a [PowerShell telepítése az Azure stack hub modulhoz](../operator/powershell-install-az-module.md).
7. Indítsa újra a gépet. Egy rendszergazda jogú PowerShell-parancssorba írja be a következőt:
    ```powershell  
    shutdown /r
    ```
8. Adja hozzá a gépet saját üzemeltetésű futóként a GitHub-tárházhoz. A saját üzemeltetésű futtatók a GitHub-dokumentumokban való hozzáadásával kapcsolatos utasításokat itt találja. További információ: saját üzemeltetésű [futók hozzáadása](https://docs.github.com/en/free-pro-team@latest/actions/hosting-your-own-runners/adding-self-hosted-runners).

    ![A futó figyelő](.\media\ci-cd-github-action-login-cli\github-action-runner-listen.png)

9. Ha elkészült, ellenőrizze, hogy fut-e a szolgáltatás, és figyeli-e a szolgáltatást. Dupla ellenőrzés `/run.cmd` a futó címtárból való futtatással.

### <a name="optional-install-azure-stack-hub-tools-on-your-self-hosted-runner"></a>Opcionális: telepítse az Azure Stack hub-eszközöket a saját üzemeltetésű futtató eszközre

A cikkben szereplő utasítások nem igénylik a [Azure stack hub-eszközök](../operator/azure-stack-powershell-download.md?tabs=az)elérését, de a saját munkafolyamatok fejlesztése során előfordulhat, hogy az eszközöket kell használnia. Az alábbi utasítások segítségével telepítheti az eszközöket a Windows saját üzemeltetésű futtató rendszerére. További információ az Azure Stack Hub-eszközökről: [Azure stack hub-eszközök letöltése a githubról](../operator/azure-stack-powershell-download.md?tabs=az). Ezek az utasítások feltételezik, hogy telepítette a csomagkezelő csokoládét.

1. Telepítse a git-t.
    ```powershell  
    choco install git
    ```

2. Egy rendszergazda jogú PowerShell-parancssorba írja be a következőt:
    ```powershell
    # Change directory to the root directory.
    cd \
    
    # Download the tools archive.
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
    invoke-webrequest `
      https://github.com/Azure/AzureStack-Tools/archive/az.zip `
      -OutFile az.zip
    
    # Expand the downloaded files.
    expand-archive az.zip `
      -DestinationPath . `
      -Force
    
    # Change to the tools directory.
    cd AzureStack-Tools-az
    ```

3. Ha az Azure Stack hub-példányhoz való kapcsolódásra van szüksége, a PowerShellt használhatja. Az útmutató a [kapcsolódás Azure stack hubhoz a PowerShell](../operator/azure-stack-powershell-configure-admin.md?tabs=az1%2Caz2%2Caz3)használatával című cikkben található.

## <a name="create-a-self-hosted-runner"></a>Saját üzemeltetésű Runner létrehozása

Saját üzemeltetésű futtatót is beállíthat a GitHub-dokumentumokban. A saját üzemeltetésű futók bármely olyan gépen futtathatók, amely képes a GitHubhoz való kapcsolódásra. Ha olyan automatizálási feladattal rendelkezik a munkafolyamatban, amely kiterjedt függőségeket igényel, akkor dönthet úgy, hogy saját üzemeltetésű futtatót használ, például egy szoftverlicenc esetében egy USB dongle-t, vagy más számítógép-vagy szoftver-specifikus igényeket. A gép lehet fizikai gép, virtuális gép vagy tároló. A futót elhelyezheti az adatközpontban vagy a felhőben is.

Ebben a cikkben egy Azure-ban üzemeltetett Windowsos virtuális gépet fog használni, amely Azure Stack Hub-specifikus PowerShell-követelményekkel lesz konfigurálva.

A saját üzemeltetésű, a tárházhoz való beállításával, konfigurálásával és csatlakoztatásával kapcsolatos utasításokért lásd a GitHub-dokumentumok "a saját üzemeltetésű[futók](https://docs.github.com/en/free-pro-team@latest/actions/hosting-your-own-runners/about-self-hosted-runners)" című témakörét.

![Saját üzemeltetésű Runner csatlakoztatva](.\media\ci-cd-github-action-login-cli\github-actions-self-hosted-runner.png)

Jegyezze fel a saját üzemeltetésű futók nevét és címkéit. A cikkben szereplő munkafolyamat a címkével hívja meg `self-hosted` .

## <a name="add-the-workflow-to-your-repository"></a>A Munkafolyamat hozzáadása a tárházhoz

Hozzon létre egy új munkafolyamatot a szakasz YAML használatával a munkafolyamat létrehozásához.

1. Nyissa meg a GitHub-tárházat.
2. Válassza a **műveletek** lehetőséget.
3. Hozzon létre egy új munkafolyamatot.
    - Ha ez az első munkafolyamat, válassza a munkafolyamat **beállítása** a **munkafolyamat-sablon választása** alatt lehetőséget.
    - Ha már rendelkezik munkafolyamatokkal, válassza az **Új munkafolyamat**  >  **saját munkafolyamat beállítása** lehetőséget.

4. Az elérési úton nevezze el a fájlt `workflow.yml` .
5. Másolja és illessze be a munkafolyamat YML.
    ```yaml  
    on: [push]
    
    env:
      ACTIONS_ALLOW_UNSECURE_COMMANDS: 'true'
     
    jobs: 
      azurestack-test:
        runs-on: self-hosted
        steps:
    
          - name: Login to AzureStack with Az Powershell
            uses: azure/login@releases/v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
              environment: 'AzureStack'
              enable-AzPSSession: true
          
          - name: Run Az PowerShell Script Against AzureStack
            uses: azure/powershell@v1
            with:
              azPSVersion: '3.1.0'
              inlineScript: |
                hostname
                Get-AzContext
                Get-AzResourceGroup
          
          - name: Login to AzureStack with CLI
            uses: azure/login@releases/v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
              environment: 'AzureStack'
              enable-AzPSSession: false
          
          - name: Run Azure CLI Script Against AzureStack
            run: |
              hostname
              az group list --output table
    ```
6. Válassza a **Start commit** (Véglegesítés indítása) lehetőséget.
7. Adja hozzá a commit title és a nem kötelező adatokat, majd válassza az **új fájl véglegesítés** lehetőséget.

A művelet futtatásakor ellenőrizze, hogy sikeresen lefutott-e.

1. Nyissa meg a GitHub-tárházat. A munkafolyamatot elindíthatja, ha lenyomja a tárházat.
1. Válassza a **műveletek** lehetőséget.
1. Válassza ki a végrehajtás nevét az **összes munkafolyamatban**.

    ![A véglegesítés összegzésének áttekintése](.\media\ci-cd-github-action-login-cli\github-actions-review-log-summary.png)
1. Válassza ki a feladattípus nevét, a **azurestack-test** elemet.

    ![A véglegesítés részleteinek áttekintése](.\media\ci-cd-github-action-login-cli\github-action-success-screen.png)
1. Bontsa ki a szakaszt a PowerShell-és CLI-parancsok visszatérési értékeinek áttekintéséhez.

Megjegyzések a munkafolyamat-fájlhoz és a művelethez:

- A munkafolyamat egyetlen nevű feladatot tartalmaz `azurestack-test` .
- A leküldéses esemény elindítja a munkafolyamatot.
- A művelet egy saját üzemeltetésű futtatót használ, amely be van állítva a tárházban, és a munkafolyamatban a Runner címkéje hívja meg a sort: `runs on: self-hosted` .
- A munkafolyamat három műveletet tartalmaz.
- Az első művelet meghívja az Azure bejelentkezési műveletét a PowerShell-lel való bejelentkezéshez az Azure-hoz készült GitHub-műveletekkel, létrehozhat olyan munkafolyamatokat, amelyek a tárházban állíthatók be, tesztelésre, csomagolásra, kiadásra és üzembe helyezésre az Azure-ban. Ez a művelet a Azure Stack SPN hitelesítő adatait használja a Azure Stack hub-környezethez való kapcsolódáshoz és a munkamenet megnyitásához. További információt a művelet használatáról a GitHubon, az [Azure bejelentkezési műveletében](https://github.com/marketplace/actions/azure-login)talál.
- A második művelet Azure PowerShell használ. A művelet az az PowerShell-modulok használatával működik együtt a Government és a Azure Stack hub-Felhőkkel. A munkafolyamat futtatása után tekintse át a feladatot annak ellenőrzéséhez, hogy a parancsfájl összegyűjtötte-e az erőforráscsoportot az Azure Stack hub-környezetben. További információ: [Azure PowerShell művelet](https://github.com/marketplace/actions/azure-powershell-action)
- A harmadik művelet az Azure CLI használatával jelentkezik be, és csatlakozik az Azure Stack hubhoz az erőforráscsoportok összegyűjtéséhez. További információ: [Azure CLI-művelet](https://github.com/marketplace/actions/azure-cli-action).
- A GitHub-műveletek és a saját üzemeltetésű futtatók használatáról további információt a [GitHub-műveletek](https://github.com/features/actions) dokumentációjában talál.

## <a name="next-steps"></a>Következő lépések

- További műveleteket a [GitHub piactéren](https://github.com/marketplace)találhat.
- További információ [az Azure stack hub általános telepítéséről](azure-stack-dev-start-deploy-app.md)  
- Tudnivalók a [Azure stack Hub Azure Resource Manager sablonjainak használatáról](azure-stack-arm-templates.md)  
- Tekintse át a DevOps Hybrid Cloud Pattern, [DevOps minta](/hybrid/app-solutions/pattern-cicd-pipeline)
