---
title: API-verzióprofilok használata Java az Azure Stackben |} A Microsoft Docs
description: Ismerje meg az Azure Stackben Java API-verzióprofilok használatával.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: fdab3fd9296388f19687e2d7ce1d4af3584640ef
ms.sourcegitcommit: c4507a100eadd9073aed0d537d054e394b34f530
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67198550"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>API-verzióprofilok használata az Azure Stackben Javával

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A Java SDK az Azure Stack Resource Manager biztosít eszközöket és az infrastruktúra kezelését. Az SDK-t az erőforrás-szolgáltatók közé tartozik a számítási, hálózatkezelési, tárolási, alkalmazásszolgáltatások, és [KeyVault](/azure/key-vault/key-vault-whatis). A Java SDK API-profilok szerint, beleértve a függőségeket a Pom.xml fájlt, amely betölti a .java fájl a megfelelő modulokat magában foglalja. Azonban hozzáadhat több profilt függőségei, mint például a **2019-03-01-hibrid**, vagy **legújabb**, mint az Azure-profilra. A függőségek használatával tölti be a helyes modulját, hogy az erőforrás-típus létrehozásakor kijelölheti a API-verziót a használni kívánt egyes profilokhoz. Ez lehetővé teszi, hogy a legújabb verziókat használhatja az Azure-ban, a legújabb API-verzióit ellen az Azure Stack fejlesztése során. Egy igazi hibrid felhőalapú fejlesztői felületet biztosít a Java SDK használata lehetővé teszi. A Java SDK API-profilok engedélyezése a hibrid felhőalapú fejlesztés segít a globális Azure-erőforrások és az Azure Stackben erőforrások közötti váltáshoz.

## <a name="java-and-api-version-profiles"></a>Java- és API-verzióprofilok

Egy API-profil az erőforrás-szolgáltatók és API-verziók. Az API-profilok segítségével egy erőforrás-szolgáltatói csomag az egyes erőforrástípusok legújabb és legnagyobb stabil verziójának beszerzéséhez.

- Az összes szolgáltatást a legújabb verziókat használhatja a **legújabb** profil, a függőség.

  - A legújabb profil használatára, a függőség van **következőt: com.microsoft.azure**.

  - Az Azure Stackben elérhető legújabb támogatott szolgáltatásokat használja a **com.microsoft.azure.profile\_2019\_03\_01\_hibrid** profilt.

    - Ez a adható meg a Pom.xml fájlt egy függőséget alkot, ami automatikusan betölti a modulokat, ha a megfelelő osztályt a legördülő listából válassza el, mint a .NET használatával.

  - Függőségek a következőképpen jelenik meg:

     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Adott API-verziók az erőforrástípushoz az adott erőforrás-szolgáltatón keresztül Intellisense definiált adott API-verziók használatához.

Vegye figyelembe, hogy egyesítheti az összes beállítás ugyanabban az alkalmazásban.

## <a name="install-the-azure-java-sdk"></a>Telepítse az Azure Java SDK

Az alábbi lépések segítségével a Java SDK telepítése:

1. Kövesse a Git telepítése a hivatalos utasításokat. Útmutatásért lásd: [első lépések – Git telepítése](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Kövesse az utasításokat követve telepítse a [Java SDK](https://zulu.org/download/) és [Maven](https://maven.apache.org/). A megfelelő verzió legyen az Java Developer Kit 8 verzióját. A megfelelő Apache Maven a 3.0-s verzió vagy újabb. A JAVA_HOME környezeti változót a rövid útmutató a Java Development Kitet telepítési helyére kell beállítani. További információkért lásd: [az első függvény létrehozása a Java és Maven](/azure/azure-functions/functions-create-first-java-maven).

3. A megfelelő függőségi csomagokat telepíteni, nyissa meg a Pom.xml fájlt a Java-alkalmazás. Adja hozzá a függőség, az alábbi kódban látható módon:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4. A készlet, amelyet telepíteni kell a csomagok használni kívánt profilt verziójától függ. A csomag nevét, a profil-verziók a következők:

   - **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**
   - **com.microsoft.azure**
     - **latest**

5. Ha nem érhető el, hozzon létre egy előfizetést és az előfizetés-azonosító későbbi használatra. Előfizetés létrehozásával kapcsolatos útmutatásért lásd: [ajánlatok, előfizetések létrehozása az Azure Stackben](../operator/azure-stack-subscribe-plan-provision-vm.md).

6. Hozzon létre egy egyszerű szolgáltatást, és mentse az ügyfél, Azonosítóját és a titkos ügyfélkulcsot. Az Azure stack-beli szolgáltatásnév létrehozása az utasításokért lásd: [alkalmazások elérést biztosíthat az Azure Stack](../operator/azure-stack-create-service-principals.md). Vegye figyelembe, hogy az ügyfél-azonosító néven is ismert az Alkalmazásazonosítót egyszerű szolgáltatás létrehozásakor.

7. Ellenőrizze, hogy az egyszerű szolgáltatást a közreműködői és tulajdonosi szerepkör-előfizetésében. Szerepkör hozzárendelése egyszerű szolgáltatást, lásd: [alkalmazások elérést biztosíthat az Azure Stack](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure Java SDK használata az Azure Stack használatával, adja meg a következő értékeket, és adja meg az értékeket a környezeti változókat. A környezeti változók beállítása, tekintse meg az alábbi táblázat az operációs rendszerének utasításokat.

| Érték                     | Környezeti változók | Leírás                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Bérlőazonosító                 | AZURE_TENANT_ID            | Az Azure Stack értékét [bérlőazonosító](../operator/azure-stack-identity-overview.md).                                                          |
| Ügyfél-azonosító                 | AZURE_CLIENT_ID             | A szolgáltatás egyszerű Alkalmazásazonosítót az egyszerű szolgáltatás létrehozása az előző szakaszban mentett.                                                                                              |
| Előfizetés azonosítója           | AZURE_SUBSCRIPTION_ID      | A [előfizetés-azonosító](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) van, hogy miként férhetnek hozzá az ajánlatok az Azure Stackben.                |
| Titkos ügyfélkulcs             | AZURE_CLIENT_SECRET        | A szolgáltatás egyszerű alkalmazás titkos kulcs mentése az egyszerű szolgáltatás létrehozásakor.                                                                                                                                   |
| Resource Manager-végpont | ARM_ENDPOINT              | Lásd: [az Azure Stack Resource Manager-végpontot](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Location egység                  | RESOURCE_LOCATION    | **Helyi** az Azure Stackhez.                                                                                                                                                                                                |

Az Azure stack a bérlő Azonosítójának megkereséséhez tekintse meg a [Itt](../operator/azure-stack-csp-ref-operations.md). A környezeti változók beállítása, tegye a következőket:

### <a name="microsoft-windows"></a>Microsoft Windows

A környezeti változók beállítása egy Windows parancssorban, használja a következő formátumot:

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS, Linux és Unix-alapú rendszerek

Unix-alapú rendszerekben használja a következő parancsot:

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Az Azure Stack hitelesítésszolgáltató főtanúsítványát a megbízható

Ha a ASDK használ, meg kell bíznia a hitelesítésszolgáltató főtanúsítványát a távoli gépen. Nem kell ehhez az integrált rendszerekkel.

#### <a name="windows"></a>Windows

1. Az Azure Stack önaláírt tanúsítvány exportálása az asztalra.

1. Egy parancssorban módosítsa a könyvtár % JAVA_HOME%\bin.

1. Futtassa a következő parancsot:

   ```shell
   .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
   ```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Az Azure Stack Resource Manager-végpont

A Microsoft Azure Resource Manager-keretrendszert, amely lehetővé teszi a rendszergazdák üzembe helyezése, kezelése és monitorozása az Azure-erőforrások számára. Az Azure Resource Manager képes kezelni ezeket a feladatokat, csoportként, nem pedig külön-külön, egyetlen művelettel.

A metaadat-információkat is kérhet a Resource Manager-végpontot. A végpont egy JSON-fájlt a kód futtatásához szükséges adatokat adja vissza.

Vegye figyelembe az alábbiakat:

- A **ResourceManagerUrl** az Azure Stack Development Kit (ASDK) van: https://management.local.azurestack.external/.

- A **ResourceManagerUrl** integrált rendszerek van: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`.

A szükséges metaadatok lekérése céljából: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

JSON-mintafájlt:

```json
{
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication":
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Meglévő API-profilok

- **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**: Az Azure Stackhez készült legújabb profil. Ez a profil Services kompatibilis az Azure Stack mindaddig, amíg a 1904 blokk vagy a további használhatják.

- **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**: Az Azure Stackhez készült profil. Ez a profil szolgáltatások használatával kompatibilis az Azure Stack a stamp verziójával 1808 vagy újabb.

- **com.microsoft.azure**: Az összes szolgáltatást a legújabb verziókat álló profilt. Az összes szolgáltatást a legújabb verziókat használhatja.

Az Azure Stacket és API-profilokkal kapcsolatos további információkért lásd: a [összegzése az API-profilok](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Azure Java SDK API profil használata

Az alábbi kód hitelesíti az Azure Stacken a szolgáltatásnevet. Létrehoz egy jogkivonatot a bérlő Azonosítóját és hitelesítési alap, amelyet kifejezetten az Azure Stack használatával:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
```

Ez lehetővé teszi, hogy az API-profiltól függ az alkalmazás telepítése sikeresen megtörtént az Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Azure Stack-környezet beállítás függvények definiálása

Az Azure Stack-felhő a megfelelő végpontokkal rendelkező regisztrálásához használja a következő kódot:

```java
// Get Azure Stack cloud endpoints
final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);

AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceID", settings.get("audience"));
                    put("activeDirectoryGraphResourceID", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".vault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

A `getActiveDirectorySettings` hívás a fenti kód lekéri a végpontok a metaadatok végpontokból. Meghatározza a végrehajtott hívás környezeti változókat:

```java
public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {

    HashMap<String, String> adSettings = new HashMap<String, String>();
    try {

        // create HTTP Client
        HttpClient httpClient = HttpClientBuilder.create().build();

        // Create new getRequest with below mentioned URL
        HttpGet getRequest = new HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
                             armEndpoint));

        // Add additional header to getRequest which accepts application/xml data
        getRequest.addHeader("accept", "application/xml");

        // Execute request and catch response
        HttpResponse response = httpClient.execute(getRequest);
        
        // Check for HTTP response code: 200 = success
        if (response.getStatusLine().getStatusCode() != 200) {
            throw new RuntimeException("Failed : HTTP error code : " + response.getStatusLine().getStatusCode());
        }
        
        String responseStr = EntityUtils.toString(response.getEntity());
        JSONObject responseJson = new JSONObject(responseStr);
        adSettings.put("galleryEndpoint", responseJson.getString("galleryEndpoint"));
        JSONObject authentication = (JSONObject) responseJson.get("authentication");
        String audience = authentication.get("audiences").toString().split("\"")[1];
        adSettings.put("login_endpoint", authentication.getString("loginEndpoint"));
        adSettings.put("audience", audience);
        adSettings.put("graphEndpoint", responseJson.getString("graphEndpoint"));

    } catch (ClientProtocolException cpe) {
        cpe.printStackTrace();
        throw new RuntimeException(cpe);
    } catch (IOException ioe) {
        ioe.printStackTrace();
        throw new RuntimeException(ioe);
    }
    return adSettings;
}
```

## <a name="samples-using-api-profiles"></a>API-profilok használatával minták

Az alábbi GitHub-minták hivatkozásként használható megoldások létrehozása a .NET-keretrendszer és az Azure Stack API profilok:

- [Erőforráscsoportok kezelése](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

- [Storage-fiókok kezelése](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

- [Virtuális gép kezelése](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm) (a 2019-03-01-hibrid profillal frissítve).

### <a name="sample-unit-test-project"></a>Minta egység tesztelő projektet

1. Klónozza a tárházat, a következő paranccsal:

   `git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`

2. Azure-beli szolgáltatásnév létrehozása, és rendelje hozzá egy szerepkörhöz az előfizetés eléréséhez. Egyszerű szolgáltatás létrehozásával kapcsolatos útmutatóért lásd: [tanúsítvánnyal egyszerű szolgáltatás létrehozása az Azure PowerShell használatával](../operator/azure-stack-create-service-principals.md).

3. Kérje le a következő kötelező környezeti változó értéke:

   - AZURE_TENANT_ID
   - AZURE_CLIENT_ID
   - AZURE_CLIENT_SECRET
   - AZURE_SUBSCRIPTION_ID
   - ARM_ENDPOINT
   - RESOURCE_LOCATION

4. Állítsa be az alábbi környezeti változókat a parancssor használatával létrehozott egyszerű szolgáltatás lekért információk segítségével:

   - Exportálás AZURE_TENANT_ID = {a bérlő Azonosítóját}
   - Exportálás AZURE_CLIENT_ID = {az ügyfél-azonosító}
   - Exportálás AZURE_CLIENT_SECRET = {az Ügyfélkód}
   - export AZURE_SUBSCRIPTION_ID={your subscription ID}
   - Exportálás ARM_ENDPOINT = {az Azure Stack Resource Manager URL-cím}
   - Exportálás RESOURCE_LOCATION = {location helyezhető Azure stacket}

   A Windows, használja **beállítása** helyett **exportálása**.

5. Használja a `getActiveDirectorySettings` funkció lekéréséhez az Azure Resource Manager-metaadatok végpontok.

    ```java
    // Get Azure Stack cloud endpoints
    final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);
    ```

6. A Pom.xml fájlt adja hozzá a következő függőséget használja a **2019-03-01-hibrid** Azure Stack-profilt. Ezt a függőséget telepíti a modulokat a profilhoz tartozó számítási, hálózatkezelési, tárolási, KeyVault és App Services erőforrás-szolgáltatókat:

   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <vers1s.0.0-beta</version>
   </dependency>
   ```

7. A parancssor használatával, amely meg volt nyitva, a környezeti változókat adja meg a következő parancsot:

   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>További lépések

API-profilokkal kapcsolatos további információkért lásd:

- [Az Azure Stackben verzióprofilok](azure-stack-version-profiles.md)
- [Erőforrás-szolgáltató API-ja verziókat támogatja profilok](azure-stack-profiles-azure-resource-manager-versions.md)
