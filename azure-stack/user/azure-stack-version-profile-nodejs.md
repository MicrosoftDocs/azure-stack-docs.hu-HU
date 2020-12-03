---
title: API-verziók profiljainak használata a Azure Stack hub Node.js
description: Ismerje meg, hogyan használhatja az API-verziók profiljait Node.jsekkel Azure Stack hub-ban.
author: mattbriggs
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: c6b781b61a8738bd86379d9fb035680189693565
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525931"
---
# <a name="use-api-version-profiles-with-nodejs-software-development-kit-sdk-in-azure-stack-hub"></a>Az API-verziók profiljainak használata Node.js szoftverfejlesztői készlettel (SDK) Azure Stack hub-ban

## <a name="nodejs-and-api-version-profiles"></a>Node.js és API-verziók profiljai

Az alkalmazások infrastruktúrájának létrehozásához és kezeléséhez használhatja a Node.js SDK-t. A Node.js SDK-ban található API-profilok segítenek a hibrid felhőalapú megoldásokban azáltal, hogy átváltanak a globális Azure-erőforrások és a Azure Stack hub-erőforrások között. A kódot egyszer is megadhatja, majd a globális Azure-t és Azure Stack hubot is megcélozhatja. 

Ebben a cikkben a [Visual Studio Code](https://code.visualstudio.com/) -ot használhatja fejlesztői eszközként. A Visual Studio Code képes a Node.js SDK hibakeresésére, és lehetővé teszi az alkalmazás futtatását, és az alkalmazás leküldését a Azure Stack hub-példányba. A Visual Studio Code-ból vagy a parancsot futtató terminál-ablakból is végezhet hibakeresést `node <nodefile.js>` .

## <a name="the-nodejs-sdk"></a>Az Node.js SDK

A Node.js SDK Azure Stack hub Resource Manager-eszközöket biztosít. Az SDK erőforrás-szolgáltatói közé tartozik a számítás, a hálózatkezelés, a tárolás, az App Services és a kulcstartó. A node.js alkalmazásban 10 erőforrás-szolgáltatói ügyfél-függvénytár telepíthető. Letöltheti azt is, hogy melyik erőforrás-szolgáltatót fogja használni az **2018-03-01-Hybrid** vagy a **2019-03-01-profilhoz** az alkalmazáshoz tartozó memória optimalizálása érdekében. Minden modul egy erőforrás-szolgáltatót, a megfelelő API-verziót és az API-profilt tartalmaz. 

Az API-profilok erőforrás-szolgáltatók és API-verziók kombinációja. Az egyes erőforrástípusok legújabb, legstabilabb verzióját az erőforrás-szolgáltatói csomagban található API-profil segítségével szerezheti be.

  -   Az összes szolgáltatás legújabb verziójának használatához használja a csomagok **legújabb** profilját.

  -   Az Azure Stack hub szolgáltatással kompatibilis szolgáltatások használatához használja az **\@ Azure/ARM-Resources-Profile-Hybrid-2019-03-01** vagy az **\@ Azure/ARM-Storage-Profile-2019-03 -01-Hybrid**

### <a name="packages-in-npm"></a>Csomagok a NPM

Minden erőforrás-szolgáltató saját csomaggal rendelkezik. A csomagot a [NPM beállításjegyzékből](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid)kérheti le.

A következő csomagokat találja:

| Erőforrás-szolgáltató | Csomag |
| --- | --- |
| [APP SERVICE](https://www.npmjs.com/package/@azure/arm-appservice-profile-2019-03-01-hybrid) | @azure/arm-appservice-profile-2019-03-01-hybrid |
| [Előfizetések Azure Resource Manager](https://www.npmjs.com/package/@azure/arm-subscriptions-profile-hybrid-2019-03-01) | @azure/arm-subscriptions-profile-hybrid-2019-03-01  |
| [Azure Resource Manager házirend](https://www.npmjs.com/package/@azure/arm-policy-profile-hybrid-2019-03-01) | @azure/arm-policy-profile-hybrid-2019-03-01
| [Azure Resource Manager DNS](https://www.npmjs.com/package/@azure/arm-dns-profile-2019-03-01-hybrid) | @azure/arm-dns-profile-2019-03-01-hybrid  |
| [Engedélyezés](https://www.npmjs.com/package/@azure/arm-authorization-profile-2019-03-01-hybrid) | @azure/arm-authorization-profile-2019-03-01-hybrid  |
| [Számítás](https://www.npmjs.com/package/@azure/arm-compute-profile-2019-03-01-hybrid) | @azure/arm-compute-profile-2019-03-01-hybrid |
| [Storage](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid) | @azure/arm-storage-profile-2019-03-01-hybrid |
| [Hálózat](https://www.npmjs.com/package/@azure/arm-network-profile-2019-03-01-hybrid) | @azure/arm-network-profile-2019-03-01-hybrid |
| [Erőforrások](https://www.npmjs.com/package/@azure/arm-resources-profile-hybrid-2019-03-01) | @azure/arm-resources-profile-hybrid-2019-03-01 |
 | [Keyvault](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid) | @azure/arm-keyvault-profile-2019-03-01-hybrid |

A szolgáltatás legújabb API-verziójának használatához használja az adott ügyféloldali függvénytár **legújabb** profilját. Ha például az erőforrás-szolgáltatás legújabb API-verzióját szeretné használni, használja az `azure-arm-resource` **erőforrás-kezelési ügyfél függvénytárának** profilját. csomag.

A csomagban meghatározott API-verziókat használhatja az erőforrás-szolgáltatók adott API-verzióihoz.

  > [!NOTE]  
  > Az alkalmazás összes beállítását egyesítheti.

## <a name="install-the-nodejs-sdk"></a>Az Node.js SDK telepítése

1. Telepítse a git-t. Útmutatásért lásd: [első lépések – a git telepítése](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Telepítse vagy frissítse a [Node.js](https://nodejs.org/en/download/)aktuális verzióját. A Node.js tartalmazza a [NPM](https://www.npmjs.com/) JavaScript Package Managert is.

3. Telepítse vagy frissítse a [Visual Studio Code](https://code.visualstudio.com/) -ot, és telepítse a [Node.js-bővítményt](https://code.visualstudio.com/docs/nodejs/nodejs-debugging) a Visual Studio Code-hoz.

2.  Telepítse az Azure Stack hub Resource Manger-ügyfél csomagjait. További információ: [az ügyféloldali kódtárak telepítése](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid).

3.  A telepítendő csomagok a használni kívánt profil verziójától függenek. Az erőforrás-szolgáltatók listáját a [csomagok a NPM](#packages-in-npm) szakaszban találja.

4. Telepítse az erőforrás-szolgáltató ügyféloldali függvénytárát a NPM használatával. A parancssorból futtassa a következőt: `npm install <package-name>` . A futtatásával például `npm install @azure/arm-authorization-profile-2019-03-01-hybrid` telepítheti az engedélyezési erőforrás-szolgáltatói függvénytárat.

5.  Hozzon létre egy előfizetést, és jegyezze fel az előfizetés AZONOSÍTÓját, ha az SDK-t használja. Útmutatásért lásd: [előfizetések létrehozása Azure stack hub-beli ajánlatokhoz](/azure/azure-stack/azure-stack-subscribe-plan-provision-vm).

6.  Hozzon létre egy szolgáltatásnevet, és mentse az ügyfél-azonosítót és az ügyfél titkos kulcsát. Az ügyfél-azonosító a szolgáltatásnév létrehozásakor az alkalmazás-azonosító néven is ismert. Útmutatásért lásd: [alkalmazások Azure stack hubhoz való hozzáférésének biztosítása](../operator/azure-stack-create-service-principals.md).

7.  Győződjön meg arról, hogy az egyszerű szolgáltatás közreműködői/tulajdonosi szerepkörrel rendelkezik az előfizetésében. A szerepkör az egyszerű szolgáltatáshoz való hozzárendelésével kapcsolatos utasításokért lásd: [alkalmazások Azure stack hubhoz való hozzáférésének biztosítása](../operator/azure-stack-create-service-principals.md).

### <a name="nodejs-prerequisites"></a>Előfeltételek Node.js 

Ha a Node.js Azure SDK-t Azure Stack hub használatával szeretné használni, a következő értékeket kell megadnia, majd értékeket kell beállítania környezeti változókkal. A környezeti változók megadásához tekintse meg az operációs rendszer táblázatának utasításait.

| Érték | Környezeti változók | Leírás |
| --- | --- | --- |
| Bérlőazonosító | Bérlő \_ azonosítója | Az Azure Stack hub- [bérlő azonosítójának](/azure/azure-stack/azure-stack-identity-overview)értéke. |
| Ügyfél-azonosító | ÜGYFÉL- \_ azonosító | Az egyszerű szolgáltatásnév alkalmazásának azonosítója, amely akkor lett mentve, amikor a jelen dokumentum előző szakaszában a szolgáltatásnév lett létrehozva.  |
| Előfizetés azonosítója | \_ \_ Az [előfizetés-AZONOSÍTÓval](../operator/service-plan-offer-subscription-overview.md#subscriptions) rendelkező AZURE-előfizetés az Azure stack hub-ban található ajánlatok elérését mutatja be.  |
| Titkos ügyfélkulcs | ALKALMAZÁS \_ titka | Az egyszerű szolgáltatásnév alkalmazásának titka mentve, amikor a szolgáltatásnév létrejött. |
| Resource Manager-végpont | ARM- \_ végpont | Tekintse meg [az Azure stack hub Resource Manager-végpontot](/azure/azure-stack/user/azure-stack-version-profiles-ruby#the-azure-stack-hub-resource-manager-endpoint). |

#### <a name="set-your-environmental-variables-for-nodejs"></a>Környezeti változók beállítása Node.js

A környezeti változók beállítása:

  - **Microsoft Windows**  

    A környezeti változók beállításához a Windows-parancssorban használja a következő formátumot:
      
      `set Tenant_ID=<Your_Tenant_ID>`

  - **macOS-, Linux-és UNIX-alapú rendszerek**  

    A környezeti változók a bash-parancssorból való beállításához használja a következő formátumot:

    `export Azure_Tenant_ID=<Your_Tenant_ID>`

**Az Azure Stack hub Resource Manager-végpont**

A Microsoft Azure Resource Manager egy felügyeleti keretrendszer, amely lehetővé teszi a rendszergazdák számára az Azure-erőforrások üzembe helyezését, kezelését és figyelését. A Azure Resource Manager a feladatokat nem külön, hanem csoportként, egyetlen művelet során is kezelhetik.

A metaadat-információkat a Resource Manager-végpontból szerezheti be. A végpont egy JSON-fájlt ad vissza, amelyben a kód futtatásához szükséges adatok szerepelnek.

> [!NOTE]  
> A Azure Stack Development Kit **ResourceManagerUrl** (ASDK) a következő: `https://management.local.azurestack.external` a **ResourceManagerUrl** az integrált rendszerekben:, ahol a a `https://management.region.<fqdn>/` `<fqdn>` teljes tartománynév.
A szükséges metaadatok beolvasása: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

Példa JSON-fájlra:

```JSON  
{
    "galleryEndpoint": "https://portal.local.azurestack.external/",

    "graphEndpoint": "https://graph.windowsNode.js/",

    "portal Endpoint": "https://portal.local.azurestack.external/",

    "authentication": {

        "loginEndpoint": "https://login.windowsNode.js/",

        "audiences": ["https://management.<yourtenant>.onmicrosoft.com/"]

    }

}
```

### <a name="existing-api-profiles"></a>Meglévő API-profilok

-  **\@Azure/ARM-resourceprovider-Profile-2019-03 -01-Hybrid**

    Azure Stack hub-hoz készült legújabb profil. Ezt a profilt úgy használhatja, hogy a szolgáltatások a leghatékonyabban kompatibilisek legyenek Azure Stack hubhoz, ha 1808 Stamp vagy tovább van.

-  **\@Azure-ARM-Resource**

    A profil az összes szolgáltatás legújabb verzióit tartalmazza. Használja az Azure összes szolgáltatásának legújabb verzióit.

Az Azure Stack hub-és API-profilokkal kapcsolatos további információkért tekintse meg [az API-profilok összegzését](/azure/azure-stack/user/azure-stack-version-profiles#summary-of-api-profiles)ismertető témakört.

### <a name="azure-nodejs-sdk-api-profile-usage"></a>Azure Node.js SDK API-profil használata

A profil-ügyfél létrehozásához a következő sorokat kell használni. Ez a paraméter csak Azure Stack hub vagy más privát felhők esetén szükséges. A globális Azure-ban a (vagy) alapértelmezés szerint már rendelkezik ezekkel a beállításokkal @azure-arm-resource @azure-arm-storage .

```Node.js  
var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;

var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
````

Az alábbi kód szükséges az egyszerű szolgáltatásnév Azure Stack hubhoz való hitelesítéséhez. Létrehoz egy jogkivonatot a bérlő azonosítója és a hitelesítési alap alapján, amely az Azure Stack hub-ra vonatkozik.

```Node.js  
var clientId = process.env['AZURE_CLIENT_ID'];
var tenantId = process.env['AZURE_TENANT_ID']; //"adfs"
var secret = process.env['AZURE_CLIENT_SECRET'];
var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
var base_url = process.env['ARM_ENDPOINT'];
var resourceClient, storageClient;
```

Ez lehetővé teszi, hogy az API-profil ügyféloldali függvénytárát az alkalmazás sikeres üzembe helyezéséhez Azure Stack hubhoz telepítse.

Az alábbi kódrészlet a Azure Stack hub-példányhoz megadott Azure Resource Manager végpontot használja, és a fent látható adatokat, például a katalógus végpontját, a Graph-végpontot, a célközönségeket és a portál végpontot gyűjti.

```Node.js  
var map = {};
const fetchUrl = base_url + 'metadata/endpoints?api-version=1.0'
```

## <a name="environment-settings"></a>Környezeti beállítások

Az egyszerű szolgáltatásnév Azure Stack hub-környezetbe való hitelesítéséhez használja a következő kódot: ezt a kódot használva és a környezeti változók a parancssorban történő beállításával automatikusan létrehozza ezt a leképezést a fejlesztő számára.

```Node.js  
function main() {
  var initializePromise = initialize();
  initializePromise.then(function (result) {
    var userDetails = result;
    console.log("Initialized user details");
    // Use user details from here
    console.log(userDetails)
    map["name"] = "AzureStack"
    map["portalUrl"] = userDetails.portalEndpoint 
    map["resourceManagerEndpointUrl"] = base_url 
    map["galleryEndpointUrl"] = userDetails.galleryEndpoint 
    map["activeDirectoryEndpointUrl"] = userDetails.authentication.loginEndpoint.slice(0, userDetails.authentication.loginEndpoint.lastIndexOf("/") + 1) 
    map["activeDirectoryResourceId"] = userDetails.authentication.audiences[0] 
    map["activeDirectoryGraphResourceId"] = userDetails.graphEndpoint 
    map["storageEndpointSuffix"] = "." + base_url.substring(base_url.indexOf('.'))  
    map["keyVaultDnsSuffix"] = ".vault" + base_url.substring(base_url.indexOf('.')) 
    map["managementEndpointUrl"] = userDetails.authentication.audiences[0] 
    map["validateAuthority"] = "false"
    Environment.Environment.add(map);
```

## <a name="samples-using-api-profiles"></a>API-profilokat használó minták

A következő mintákat használhatja referenciáként Node.js és Azure Stack hub API-profillal rendelkező megoldások létrehozásához. A minták a GitHubról a következő adattárakban szerezhetők be:

- [A tárolási csomópont erőforrás-szolgáltatójának első lépései](https://github.com/Azure-Samples/hybrid-storage-nodejs-create-storageaccount)
- [Számítási csomópont kezelése](https://github.com/Azure-Samples/Hybrid-compute-nodejs-create-vm)
- [Erőforrás-kezelő csomópont-erőforrások és-csoportok](https://github.com/Azure-Samples/Hybrid-resourcegroups-nodejs-manageresources)

### <a name="sample-create-storage-account"></a>Minta létrehozása Storage-fiók 

1.  A tárház klónozása.

    ```bash  
    git clone https://github.com/sijuman/storage-node-resource-provider-getting-started.git
    ```

1. `cd` a tárházban lévő klónba.

2.  Hozzon létre egy Azure-szolgáltatásnevet, és rendeljen hozzá egy szerepkört az előfizetéshez való hozzáféréshez. Útmutatásért lásd: a [Azure PowerShell használata egy egyszerű szolgáltatásnév létrehozásához tanúsítvánnyal](/azure/azure-stack/azure-stack-create-service-principals).

3.  Kérje le a következő szükséges értékeket:
    - Bérlőazonosító
    - Ügyfél-azonosító (alkalmazás azonosítója)
    - Titkos ügyfélkulcs
    - Azure-előfizetés azonosítója
    - Azure Stack hub Resource Manager-végpont

4.  Állítsa be az alábbi környezeti változókat a parancssorból létrehozott egyszerű szolgáltatásnév használatával lekért információk alapján:

    > [!NOTE]  
    > Windows rendszeren használja az **Exportálás** helyett a **készletet** .

    ```bash  
    export TENANT_ID=<your tenant id>
    export CLIENT_ID=<your client id>
    export APPLICATION_SECRET=<your client secret>K
    export AZURE_SUBSCRIPTION_ID=<your subscription id>
    export ARM_ENDPOINT=<your Azure Stack Hub Resource manager URL>
    ```

5.  Nyissa meg a `index.js` minta alkalmazás fájlját.

6.  Állítsa a Location változót a Azure Stack hub helyére. Például: `LOCAL = "local"`.

7.  Állítsa be a hitelesítő adatokat, amelyek lehetővé teszik a hitelesítését Azure Stack hub-ban. A kód ezen részében szerepel a minta a index.js fájlon.

    ```Node.js  
    var clientId = process.env['CLIENT_ID'];
    var tenantId = process.env['TENANT_ID']; //"adfs"
    var secret = process.env['APPLICATION_SECRET'];
    var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
    var base_url = process.env['ARM_ENDPOINT'];
    var resourceClient, storageClient;
    ```

8.  Ellenőrzi, hogy a megfelelő ügyféloldali kódtárakat állította-e be a fent meghatározott ügyféloldali kódtárak kombinációjával. Ebben a példában a következőt használtuk:

    ```Node.js
    var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;
    var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
    ```

9.  A [NPM-modulok keresésével](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid)keresse meg a **2019-03-01-Hybrid** értéket, és telepítse a profilhoz társított csomagokat a számítási, hálózati, tárolási, kulcstartó és app Services erőforrás-szolgáltatók számára.

    Ehhez nyissa meg a parancssort, átirányítja a tárház gyökérkönyvtárához, és futtassa az `npm install @azure/arm-keyvault-profile-2019-03-01-hybrid` összes használt erőforrás-szolgáltatót.

10.  A parancssorban futtassa a parancsot az `npm install` összes node.js-modul telepítéséhez.

11.  Futtassa a mintát.

        ```Node.js  
        node index.js
        ```

12.  Ha index.js után szeretné megtisztítani a karbantartási parancsfájlt, futtassa a következőt:.

        ```Node.js  
        Node cleanup.js <resourceGroupName> <storageAccountName>
        ```

13.  A minta sikeresen befejeződött. A mintával kapcsolatos további információkért lásd alább.

### <a name="what-does-indexjs-do"></a>Mit tesz index.js?

A minta létrehoz egy új Storage-fiókot, felsorolja az előfizetés vagy az erőforráscsoport Storage-fiókjait, felsorolja a Storage-fiók kulcsait, újragenerálja a Storage-fiók kulcsait, beolvassa a Storage-fiók tulajdonságait, frissíti a Storage-fiók SKU-t, és ellenőrzi a tárolási fiók nevét.

A minta a szolgáltatásnév használatával indul, és **ResourceManagementClient** és **StorageManagementClient** objektumokat hoz létre a hitelesítő adatai és az előfizetés azonosítója alapján.

A minta ezután Beállítja azt az erőforráscsoportot, amelyben létre fogja hozni az új Storage-fiókot.

```Node.js  
function createResourceGroup(callback) {
  var groupParameters = { location: location, tags: { sampletag: 'sampleValue' } };
  console.log('\nCreating resource group: ' + resourceGroupName);
  return resourceClient.resourceGroups.createOrUpdate(resourceGroupName, groupParameters, callback);
}
```

### <a name="create-a-new-storage-account"></a>Új tárfiók létrehozása

Ezután a minta egy új Storage-fiókot hoz létre, amely az előző lépésben létrehozott erőforráscsoporthoz van társítva.

Ebben az esetben a rendszer véletlenszerűen generálja a Storage-fiók nevét az egyediség biztosítása érdekében. Az új Storage-fiók létrehozásának meghívása azonban sikeres lesz, ha már van ilyen nevű fiók az előfizetésben.

```Node.js  
var createParameters = {
    location: location,
    sku: {
        name: accType,
    },
    kind: 'Storage',
    tags: {
        tag1: 'val1',
        tag2: 'val2'
    }
};


console.log('\\n--&gt;Creating storage account: ' + storageAccountName + ' with parameters:\\n' + util.inspect(createParameters));

return storageClient.storageAccounts.create(resourceGroupName, storageAccountName, createParameters, callback);
```

### <a name="list-storage-accounts-in-the-subscription-or-resource-group"></a>Az előfizetéshez vagy az erőforráscsoporthoz tartozó Storage-fiókok listázása

A minta felsorolja az adott előfizetéshez tartozó összes Storage-fiókot:

```Node.js  
console.log('\\n--&gt;Listing storage accounts in the current subscription.');

return storageClient.storageAccounts.list(callback);

It also lists storage accounts in the resource group:

    console.log('\\n--&gt;Listing storage accounts in the resourceGroup : ' + resourceGroupName);

return storageClient.storageAccounts.listByResourceGroup(resourceGroupName, callback);
```

### <a name="read-and-regenerate-storage-account-keys"></a>A Storage-fiók kulcsainak olvasása és újragenerálása

A minta Listázza a Storage-fiókok kulcsait az újonnan létrehozott Storage-fiókhoz és erőforráscsoporthoz:

```Node.js  
console.log('\\n--&gt;Listing storage account keys for account: ' + storageAccountName);

return storageClient.storageAccounts.listKeys(resourceGroupName, storageAccountName, callback);
```

Emellett újragenerálja a fiók hozzáférési kulcsait:

```Node.js  
console.log('\\n--&gt;Regenerating storage account keys for account: ' + storageAccountName);

return storageClient.storageAccounts.regenerateKey(resourceGroupName, storageAccountName, 'key1', callback);
```

### <a name="get-storage-account-properties"></a>Storage-fiók tulajdonságainak beolvasása

A minta beolvassa a Storage-fiók tulajdonságait:

```Node.js  
console.log('\\n--&gt;Getting info of storage account: ' + storageAccountName);

return storageClient.storageAccounts.getProperties(resourceGroupName, storageAccountName, callback);
```

### <a name="check-storage-account-name-availability"></a>A Storage-fiók nevének rendelkezésre állásának keresése

A minta ellenőrzi, hogy az adott tárolási fiók neve elérhető-e az Azure-ban:

```Node.js  
console.log('\\n--&gt;Checking if the storage account name : ' + storageAccountName + ' is available.');

return storageClient.storageAccounts.checkNameAvailability(storageAccountName, callback);
```

### <a name="delete-the-storage-account-and-resource-group"></a>A Storage-fiók és az erőforráscsoport törlése

A (z) cleanup.js futtatása törli a minta által létrehozott Storage-fiókot:

```Node.js  
console.log('\\nDeleting storage account : ' + storageAccountName);
return storageClient.storageAccounts.deleteMethod(resourceGroupName, storageAccountName, callback);
```

Emellett törli azt az erőforráscsoportot is, amelyet a minta hozott létre:

```Node.js  
console.log('\\nDeleting resource group: ' + resourceGroupName);

return resourceClient.resourceGroups.deleteMethod(resourceGroupName, callback);
```

## <a name="next-steps"></a>További lépések

Az API-profilokról további információt a következő témakörben talál:

- [API-verziók profiljainak kezelése Azure Stack hub-ban](azure-stack-version-profiles.md)
- [A profilok által támogatott erőforrás-szolgáltatói API-verziók](azure-stack-profiles-azure-resource-manager-versions.md)
