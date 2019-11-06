---
title: API-verziók profiljainak használata a Node. js-sel Azure Stackban | Microsoft Docs
description: Tudnivalók az API-verziók profiljainak a Node. js-sel való használatáról Azure Stackban.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 07/30/2019
ms.openlocfilehash: 65ea0b4f6f7f7cb3769e83bf9052ac2953668e48
ms.sourcegitcommit: 20d1c0ab3892e9c4c71d5b039457f1e15b1c84c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73618232"
---
# <a name="use-api-version-profiles-with-nodejs-software-development-kit-sdk-in-azure-stack"></a>API-verziók profiljainak használata a Node. js szoftverfejlesztői készlettel (SDK) Azure Stack

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

## <a name="nodejs-and-api-version-profiles"></a>Node. js-és API-verziók profiljai

A Node. js SDK segítségével az alkalmazások infrastruktúráját hozhatja létre és kezelheti. A Node. js SDK-ban található API-profilok segítenek a hibrid felhőalapú megoldásokban azáltal, hogy átváltanak a globális Azure-erőforrások és a Azure Stack erőforrások között. A kódot egyszer is elvégezheti, és a globális Azure-t és Azure Stack is megcélozhatja. 

Ebben a cikkben a [Visual Studio Code](https://code.visualstudio.com/) -ot használhatja fejlesztői eszközként. A Visual Studio Code képes a Node. js SDK hibakeresésére, és lehetővé teszi az alkalmazás futtatását, és az alkalmazás leküldését az Azure Stack-példányba. A Visual Studio Code-ból vagy egy, a `node <nodefile.js>` parancsot futtató terminálon keresztül végezhet hibakeresést.

## <a name="the-nodejs-sdk"></a>A Node. js SDK

A Node. js SDK Azure Stack Resource Manager-eszközöket biztosít. Az SDK erőforrás-szolgáltatói közé tartozik a számítás, a hálózatkezelés, a tárolás, az App Services és a kulcstartó. A Node. js-alkalmazásban 10 erőforrás-szolgáltatói ügyfél-függvénytár telepíthető. Letöltheti azt is, hogy melyik erőforrás-szolgáltatót fogja használni az **2018-03-01-Hybrid** vagy a **2019-03-01-profilhoz** az alkalmazáshoz tartozó memória optimalizálása érdekében. Minden modul egy erőforrás-szolgáltatót, a megfelelő API-verziót és az API-profilt tartalmaz. 

Az API-profilok erőforrás-szolgáltatók és API-verziók kombinációja. Az egyes erőforrástípusok legújabb, legstabilabb verzióját az erőforrás-szolgáltatói csomagban található API-profil segítségével szerezheti be.

  -   Az összes szolgáltatás legújabb verziójának használatához használja a csomagok **legújabb** profilját.

  -   A Azure Stack-kompatibilis szolgáltatások használatához használja a **\@azure/ARM-Resources-Profile-Hybrid-2019-03-01** vagy a **\@azure/ARM-Storage-profil-2019-03 -01-Hybrid**

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
| [Forrásanyagok](https://www.npmjs.com/package/@azure/arm-resources-profile-hybrid-2019-03-01) | @azure/arm-resources-profile-hybrid-2019-03-01 |
 | [Keyvault](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid) | @azure/arm-keyvault-profile-2019-03-01-hybrid |

A szolgáltatás legújabb API-verziójának használatához használja az adott ügyféloldali függvénytár **legújabb** profilját. Ha például az erőforrás-szolgáltatás legújabb API-verzióját szeretné használni, használja az **Erőforrás-kezelés ügyféloldali kódtár** `azure-arm-resource` profilját. csomag.

A csomagban meghatározott API-verziókat használhatja az erőforrás-szolgáltatók adott API-verzióihoz.

  > [!Note]  
  > Az alkalmazás összes beállítását egyesítheti.

## <a name="install-the-nodejs-sdk"></a>A Node. js SDK telepítése

1. Telepítse a git-t. Útmutatásért lásd: [első lépések – a git telepítése](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Telepítse vagy frissítsen a [Node. js](https://nodejs.org/en/download/)aktuális verziójára. A Node. js a [NPM](https://www.npmjs.com/) JavaScript Package Managert is tartalmazza.

3. Telepítse vagy frissítse a [Visual Studio Code](https://code.visualstudio.com/) -ot, és telepítse a [Node. js-bővítményt](https://code.visualstudio.com/docs/nodejs/nodejs-debugging) a Visual Studio Code-hoz.

2.  Telepítse az Azure Stack Resource Manger-ügyfél csomagjait. További információ: [az ügyféloldali kódtárak telepítése](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid).

3.  A telepítendő csomagok a használni kívánt profil verziójától függenek. Az erőforrás-szolgáltatók listáját a [csomagok a NPM](#packages-in-npm) szakaszban találja.

4. Telepítse az erőforrás-szolgáltató ügyféloldali függvénytárát a NPM használatával. A parancssorból futtassa a következő parancsot: `npm install <package-name>`. A `npm install @azure/arm-authorization-profile-2019-03-01-hybrid` futtatásával például telepítheti az engedélyezési erőforrás-szolgáltatói függvénytárat.

5.  Hozzon létre egy előfizetést, és jegyezze fel az előfizetés AZONOSÍTÓját, ha az SDK-t használja. Útmutatásért lásd: [előfizetések létrehozása az ajánlatokhoz Azure stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm).

6.  Hozzon létre egy szolgáltatásnevet, és mentse az ügyfél-azonosítót és az ügyfél titkos kulcsát. Az ügyfél-azonosító a szolgáltatásnév létrehozásakor az alkalmazás-azonosító néven is ismert. Útmutatásért lásd: [alkalmazások Azure Stackhoz való hozzáférésének biztosítása](../operator/azure-stack-create-service-principals.md).

7.  Győződjön meg arról, hogy az egyszerű szolgáltatás közreműködői/tulajdonosi szerepkörrel rendelkezik az előfizetésében. A szerepkör az egyszerű szolgáltatáshoz való hozzárendelésével kapcsolatos utasításokért lásd: [alkalmazások Azure Stackhoz való hozzáférésének biztosítása](../operator/azure-stack-create-service-principals.md).

### <a name="nodejs-prerequisites"></a>A Node. js előfeltételei 

Ha a Node. js Azure SDK-t Azure Stack használatával szeretné használni, meg kell adnia a következő értékeket, majd értékeket kell beállítania környezeti változókkal. A környezeti változók megadásához tekintse meg az operációs rendszer táblázatának utasításait.

| Value (Díj) | Környezeti változók | Leírás |
| --- | --- | --- |
| Bérlőazonosító | BÉRLŐi\_azonosítója | A Azure Stack [bérlő azonosítójának](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview)értéke. |
| Ügyfél-azonosító | ÜGYFÉL\_azonosítója | Az egyszerű szolgáltatásnév alkalmazásának azonosítója, amely akkor lett mentve, amikor a jelen dokumentum előző szakaszában a szolgáltatásnév lett létrehozva.  |
| Subscription ID (Előfizetés azonosítója) | Az AZURE\_előfizetése\_ID azonosító az [előfizetés azonosítója](/azure-stack/operator/service-plan-offer-subscription-overview#subscriptions) , hogyan érheti el az ajánlatokat a Azure Stackban.  |
| Ügyfél titka | ALKALMAZÁS\_TITKa | Az egyszerű szolgáltatásnév alkalmazásának titka mentve, amikor a szolgáltatásnév létrejött. |
| Resource Manager-végpont | ARM\_VÉGPONT | Lásd [a Azure stack Resource Manager-végpontot](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles-ruby#the-azure-stack-resource-manager-endpoint). |

#### <a name="set-your-environmental-variables-for-nodejs"></a>Környezeti változók beállítása a Node. js-hez

A környezeti változók beállítása:

  - **Microsoft Windows**  

    A környezeti változók beállításához a Windows-parancssorban használja a következő formátumot:
      
      `set Tenant_ID=<Your_Tenant_ID>`

  - **macOS-, Linux-és UNIX-alapú rendszerek**  

    A környezeti változók a bash-parancssorból való beállításához használja a következő formátumot:

    `export Azure_Tenant_ID=<Your_Tenant_ID>`

**A Azure Stack Resource Manager-végpont**

A Microsoft Azure Resource Manager egy felügyeleti keretrendszer, amely lehetővé teszi a rendszergazdák számára az Azure-erőforrások üzembe helyezését, kezelését és figyelését. A Azure Resource Manager a feladatokat nem külön, hanem csoportként, egyetlen művelet során is kezelhetik.

A metaadat-információkat a Resource Manager-végpontból szerezheti be. A végpont egy JSON-fájlt ad vissza, amelyben a kód futtatásához szükséges adatok szerepelnek.

> [!Note]  
> A Azure Stack Development Kit **ResourceManagerUrl** (ASDK) a következő: `https://management.local.azurestack.external` a **ResourceManagerUrl** az integrált rendszerekben: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com` a szükséges metaadatok lekéréséhez: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

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

-  **\@azure/ARM-resourceprovider-Profile-2019-03 -01-Hybrid**

    A Azure Stackhez készült legújabb profil. Használja ezt a profilt a szolgáltatásokhoz, hogy a leghatékonyabban kompatibilisek legyenek Azure Stackval, feltéve, hogy az 1808-es bélyegzőn vagy tovább.

-  **\@azure-ARM-Resource**

    A profil az összes szolgáltatás legújabb verzióit tartalmazza. Használja az Azure összes szolgáltatásának legújabb verzióit.

A Azure Stack-és API-profilokkal kapcsolatos további információkért tekintse meg [az API-profilok összegzését](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles#summary-of-api-profiles)ismertető témakört.

### <a name="azure-nodejs-sdk-api-profile-usage"></a>Azure Node. js SDK API-profil használata

A profil-ügyfél létrehozásához a következő sorokat kell használni. Ez a paraméter csak Azure Stack vagy más privát felhők esetén szükséges. A globális Azure-ban alapértelmezés szerint a @azure-arm-resource vagy a @azure-arm-storage érték szerepel ezekkel a beállításokkal.

```Node.js  
var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;

var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
````

Az alábbi kód szükséges az egyszerű szolgáltatásnév hitelesítéséhez Azure Stackon. Létrehoz egy jogkivonatot a bérlő azonosítója és a hitelesítési alap alapján, amely a Azure Stackre vonatkozik.

```Node.js  
var clientId = process.env['AZURE_CLIENT_ID'];
var tenantId = process.env['AZURE_TENANT_ID']; //"adfs"
var secret = process.env['AZURE_CLIENT_SECRET'];
var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
var base_url = process.env['ARM_ENDPOINT'];
var resourceClient, storageClient;
```

Ez lehetővé teszi, hogy az API-profil ügyféloldali függvénytárát az alkalmazás sikeres üzembe helyezéséhez Azure Stack.

Az alábbi kódrészlet a Azure Stack példányhoz megadott Azure Resource Manager végpontot használja, és a fent látható adatokat, például a katalógus végpontját, a Graph-végpontot, a célközönségeket és a portál végpontot gyűjti.

```Node.js  
var map = {};
const fetchUrl = base_url + 'metadata/endpoints?api-version=1.0'
```

## <a name="environment-settings"></a>Környezeti beállítások

Az egyszerű szolgáltatásnév Azure Stack-környezetbe való hitelesítéséhez használja a következő kódot: ezt a kódot használva és a környezeti változók a parancssorban történő beállításával automatikusan létrehozza ezt a leképezést a fejlesztő számára.

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

A következő példák használhatók a Node. js-és Azure Stack API-profilokkal rendelkező megoldások létrehozásához. A minták a GitHubról a következő adattárakban szerezhetők be:

- [A tárolási csomópont erőforrás-szolgáltatójának első lépései](https://github.com/sijuman/storage-node-resource-provider-getting-started)
- [Számítási csomópont kezelése](https://github.com/sijuman/compute-node-manage-vm)
- [Erőforrás-kezelő csomópont-erőforrások és-csoportok](https://github.com/sijuman/resource-manager-node-resources-and-groups)

### <a name="sample-create-storage-account"></a>Minta létrehozása Storage-fiók 

1.  A tárház klónozása.

    ```bash  
    git clone https://github.com/sijuman/storage-node-resource-provider-getting-started.git
    ```

2.  Hozzon létre egy Azure-szolgáltatásnevet, és rendeljen hozzá egy szerepkört az előfizetéshez való hozzáféréshez. Útmutatásért lásd: a [Azure PowerShell használata egy egyszerű szolgáltatásnév létrehozásához tanúsítvánnyal](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

3.  Kérje le a következő szükséges értékeket:
    - Bérlőazonosító
    - Ügyfél-azonosító
    - Titkos ügyfélkulcs
    - Azure-előfizetés azonosítója
    - Azure Stack Resource Manager-végpont

4.  Állítsa be az alábbi környezeti változókat a parancssorból létrehozott egyszerű szolgáltatásnév használatával lekért információk alapján:

    ```bash  
    export TENANT_ID=<your tenant id>
    export CLIENT_ID=<your client id>
    export APPLICATION_SECRET=<your client secret>K
    export AZURE_SUBSCRIPTION_ID=<your subscription id>
    export ARM_ENDPOINT=<your Azure Stack Resource manager URL>
    ```

    > [!Note]  
    > Windows rendszeren használja az **Exportálás**helyett a **készletet** .

5.  Nyissa meg a minta alkalmazás `index.js` fájlját.

6.  Állítsa a Location változót a Azure Stack helyére. Például: `LOCAL = "local"`.

7.  Állítsa be a hitelesítő adatokat, amelyek lehetővé teszik a hitelesítését Azure Stack. A kód ezen részében szerepel ebben a példában az index. js fájlban.

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

    Ehhez nyissa meg a parancssort, majd irányítsa át a tárház gyökérkönyvtárához, és futtassa `npm install @azure/arm-keyvault-profile-2019-03-01-hybrid` értéket minden egyes használt erőforrás-szolgáltatónál.

10.  A parancssorban futtassa a `npm install` parancsot az összes Node. js-modul telepítéséhez.

11.  Futtassa a mintát.

        ```Node.js  
        node index.js
        ```

12.  Az index. js törlése után futtassa a razzia szkriptet.

        ```Node.js  
        Node cleanup.js <resourceGroupName> <storageAccountName>
        ```

13.  A minta sikeresen befejeződött. A mintával kapcsolatos további információkért lásd alább.

### <a name="what-does-indexjs-do"></a>Mit jelent az index. js?

A minta létrehoz egy új Storage-fiókot, felsorolja az előfizetés vagy az erőforráscsoport Storage-fiókjait, felsorolja a Storage-fiók kulcsait, újragenerálja a Storage-fiók kulcsait, beolvassa a Storage-fiók tulajdonságait, frissíti a Storage-fiók SKU azonosítóját, és ellenőrzi a a Storage-fiók neve rendelkezésre állása.

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

A cleanup. js futtatása törli azt a Storage-fiókot, amelyet a minta hozott létre:

```Node.js  
console.log('\\nDeleting storage account : ' + storageAccountName);
return storageClient.storageAccounts.deleteMethod(resourceGroupName, storageAccountName, callback);
```

Emellett törli azt az erőforráscsoportot is, amelyet a minta hozott létre:

```Node.js  
console.log('\\nDeleting resource group: ' + resourceGroupName);

return resourceClient.resourceGroups.deleteMethod(resourceGroupName, callback);
```

## <a name="next-steps"></a>Következő lépések

Az API-profilokról további információt a következő témakörben talál:

- [API-verzióprofilok kezelése az Azure Stackben](azure-stack-version-profiles.md)
- [A profilok által támogatott erőforrás-szolgáltatói API-verziók](azure-stack-profiles-azure-resource-manager-versions.md)
