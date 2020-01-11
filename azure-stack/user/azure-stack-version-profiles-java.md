---
title: Az API-verziók profiljainak használata Javával Azure Stack hub-ban | Microsoft Docs
description: Ismerje meg, hogyan használhatók az API-verziók profiljai a Javával Azure Stack hub-ban.
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
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 37481cee1e7bc5b9bee0e68878077e084369ca39
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883184"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack-hub"></a>Az API-verziók profiljainak használata Javával Azure Stack hub-ban

A Azure Stack hub Resource Managerhez készült Java SDK eszközöket biztosít az infrastruktúra kiépítéséhez és kezeléséhez. Az SDK erőforrás-szolgáltatói közé tartozik a számítás, a hálózatkezelés, a tárolás, a App Services és a [Key Vault](/azure/key-vault/key-vault-whatis).

A Java SDK API-profilokat tartalmaz, a **Pom. XML** fájlban lévő függőségekkel együtt, amelyek betöltik a megfelelő modulokat a **. Java** fájlban. Ugyanakkor több profilt is hozzáadhat függőségként, például az **2019-03-01-Hybrid**vagy a **Latest**Azure-profilként. Ezeknek a függőségeknek a használatával a megfelelő modult tölti be, így az erőforrástípus létrehozásakor kiválaszthatja, hogy melyik API-verziót szeretné használni. Ez lehetővé teszi, hogy a legújabb verziókat használja az Azure-ban, miközben az Azure Stack hub legújabb API-verzióit fejleszti.

A Java SDK használata lehetővé teszi a hibrid felhőalapú fejlesztői élményt. A Java SDK-ban található API-profilok lehetővé teszik a hibrid felhőalapú fejlesztést azáltal, hogy a Azure Stack hub globális Azure-erőforrásai és-erőforrásai közötti váltást segítik.

## <a name="java-and-api-version-profiles"></a>Java-és API-verziók profiljai

Az API-profilok erőforrás-szolgáltatók és API-verziók kombinációja. Az erőforrás-szolgáltatói csomagban található egyes erőforrástípusok legújabb, legstabilabb verzióját az API-profil segítségével szerezheti be.

- Az összes szolgáltatás legújabb verziójának használatához használja a **legújabb** profilt függőségként.

  - A legújabb profil használatához a függőség a következő: **com. microsoft. Azure**.

  - A Azure Stack hub-ban elérhető legújabb támogatott szolgáltatások használatához használja a **com. microsoft. Azure. profil\_2019\_03\_01\_hibrid** profilt.

    - A profil a **Pom. XML** fájlban van megadva függőségként, amely automatikusan betölti a modulokat, ha a legördülő listából kiválasztja a megfelelő osztályt (a .net-tel együtt).

  - A függőségek az alábbiak szerint jelennek meg:

     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Ha adott erőforrás-szolgáltatóhoz adott API-verziókat szeretne használni, használja az IntelliSense által meghatározott API-verziókat.

Az alkalmazás összes beállítását egyesítheti.

## <a name="install-the-azure-java-sdk"></a>Az Azure Java SDK telepítése

A Java SDK telepítéséhez kövesse az alábbi lépéseket:

1. A git telepítéséhez kövesse a hivatalos utasításokat. Lásd: [első lépések – a git telepítése](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. A [Java SDK](https://zulu.org/download/) és a [Maven](https://maven.apache.org/)telepítéséhez kövesse az utasításokat. A megfelelő verzió a Java Developer Kit 8-as verziója. Az Apache Maven megfelelő verziója 3,0 vagy újabb. A rövid útmutató elvégzéséhez a `JAVA_HOME` környezeti változót a Java Development Kit telepítési helyére kell beállítani. További információ: [az első függvény létrehozása a Java és a Maven](/azure/azure-functions/functions-create-first-java-maven)használatával.

3. A megfelelő függőségi csomagok telepítéséhez nyissa meg a **Pom. XML** fájlt a Java-alkalmazásban. Vegyen fel egy függőséget, ahogy az a következő kódban látható:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4. A telepítendő csomagok készlete a használni kívánt profil verziójától függ. A profilok nevei a következők:

   - **com. microsoft. Azure. profil\_2019\_03\_01\_Hybrid**
   - **com. microsoft. Azure**
     - **legújabb**

5. Ha nem érhető el, hozzon létre egy előfizetést, és mentse az előfizetés-azonosítót későbbi használatra. Az előfizetések létrehozásával kapcsolatos utasításokért lásd: [előfizetések létrehozása az Azure stack hub-beli ajánlatokhoz](../operator/azure-stack-subscribe-plan-provision-vm.md).

6. Hozzon létre egy szolgáltatásnevet, és mentse az ügyfél-azonosítót és az ügyfél titkos kulcsát. Az Azure Stack hub szolgáltatáshoz való létrehozásával kapcsolatos utasításokért lásd: [alkalmazások Azure stack hub elérésének biztosítása](../operator/azure-stack-create-service-principals.md). Az ügyfél-azonosító a szolgáltatásnév létrehozásakor az alkalmazás-azonosító néven is ismert.

7. Győződjön meg arról, hogy a szolgáltatásnév közreműködői/tulajdonosi szerepkörrel rendelkezik az előfizetésében. A szerepkör az egyszerű szolgáltatáshoz való hozzárendelésével kapcsolatos utasításokért lásd: [alkalmazások Azure stack hubhoz való hozzáférésének biztosítása](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure Java SDK-t Azure Stack hubhoz szeretné használni, meg kell adnia a következő értékeket, majd értékeket kell beállítania környezeti változókkal. A környezeti változók megadásához tekintse meg az operációs rendszer táblázatának utasításait.

| Value (Díj)                     | Környezeti változók | Leírás                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Bérlőazonosító                 | `AZURE_TENANT_ID`            | Az Azure Stack hub- [bérlő azonosítója](../operator/azure-stack-identity-overview.md).                                                          |
| Ügyfél-azonosító                 | `AZURE_CLIENT_ID`             | Az egyszerű szolgáltatásnév alkalmazásának azonosítója, amely akkor lett mentve, amikor a szolgáltatásnév az előző szakaszban lett létrehozva.                                                                                              |
| Subscription ID (Előfizetés azonosítója)           | `AZURE_SUBSCRIPTION_ID`      | Az előfizetés- [azonosítóval](../operator/service-plan-offer-subscription-overview.md#subscriptions) érheti el az ajánlatokat az Azure stack hub szolgáltatásban.                |
| Titkos ügyfélkulcs             | `AZURE_CLIENT_SECRET`        | Az egyszerű szolgáltatás alkalmazásának titkos kulcsa a szolgáltatásnév létrehozásakor mentve.                                                                                                                                   |
| Resource Manager-végpont | `ARM_ENDPOINT`              | Lásd a [Azure stack hub Resource Manager-végpontot](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint) ismertető cikket. |
| Földrajzi egység                  | `RESOURCE_LOCATION`    | **Helyi** Azure stack hub.                                                                                                                                                                                                |

Az Azure Stack hub bérlői AZONOSÍTÓjának megkereséséhez tekintse meg az [itt](../operator/azure-stack-csp-ref-operations.md)található utasításokat. A környezeti változók beállításához használja a következő részben ismertetett eljárásokat:

### <a name="microsoft-windows"></a>Microsoft Windows

A környezeti változók Windows-parancssorban történő beállításához használja a következő formátumot:

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>MacOS-, Linux-és UNIX-alapú rendszerek

UNIX-alapú rendszerek esetén használja a következő parancsot:

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="trust-the-azure-stack-hub-ca-root-certificate"></a>Az Azure Stack hub HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványának megbízhatósága

Ha a Azure Stack Development Kit (ASDK) használja, meg kell bíznia a távoli gépen lévő HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványban. Nem kell megbíznia a HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványban Azure Stack hub integrált rendszerekkel.

#### <a name="windows"></a>Windows

1. Exportálja az Azure Stack hub önaláírt tanúsítványát az asztalra.

1. A parancssorban módosítsa a könyvtárat `%JAVA_HOME%\bin`ra.

1. Futtassa az alábbi parancsot:

   ```shell
   .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
   ```

### <a name="the-azure-stack-hub-resource-manager-endpoint"></a>Az Azure Stack hub Resource Manager-végpont

A Azure Resource Manager egy felügyeleti keretrendszer, amely lehetővé teszi a rendszergazdáknak az Azure-erőforrások üzembe helyezését, kezelését és figyelését. A Azure Resource Manager a feladatokat nem külön, hanem csoportként, egyetlen művelet során is kezelhetik.

A metaadat-információkat a Resource Manager-végpontból kérheti le. A végpont egy JSON-fájlt ad vissza, amely a kód futtatásához szükséges adatokkal rendelkezik.

Vegye figyelembe az alábbi szempontokat:

- A ASDK lévő **ResourceManagerUrl** a következőket eredményezi: `https://management.local.azurestack.external/`.

- Az integrált rendszerek **ResourceManagerUrl** : `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`.

A szükséges metaadatok beolvasása: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

Példa JSON-fájlra:

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

- **com. microsoft. Azure. profil\_2019\_03\_01\_Hybrid**: az Azure stack hub-hoz készült legújabb profil. Ezt a profilt olyan szolgáltatásokhoz használhatja, amelyek kompatibilisek Azure Stack hubhoz, feltéve, hogy az 1904-es vagy újabb verzióban van.

- **com. microsoft. Azure. profil\_2018\_03\_01\_Hybrid**: az Azure stack hub-hoz készült profil. Használja ezt a profilt, hogy a szolgáltatások kompatibilisek legyenek Azure Stack hub 1808-es vagy újabb verziójával.

- **com. microsoft. Azure**: profil, amely az összes szolgáltatás legújabb verzióit tartalmazza. Használja az összes szolgáltatás legújabb verzióit.

Az Azure Stack hub-és API-profilokkal kapcsolatos további információkért tekintse meg az [API-profilok összefoglalását](../user/azure-stack-version-profiles.md#summary-of-api-profiles)ismertető témakört.

## <a name="azure-java-sdk-api-profile-usage"></a>Azure Java SDK API-profil használata

A következő kód hitelesíti a szolgáltatásnevet Azure Stack hub-on. Létrehoz egy jogkivonatot a bérlői azonosító és a hitelesítési alap használatával, amely az Azure Stack hub-ra vonatkozik:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
```

Ez a kód lehetővé teszi az API-profilok függőségeinek használatát az alkalmazás sikeres üzembe helyezéséhez Azure Stack hubhoz.

## <a name="define-azure-stack-hub-environment-setting-functions"></a>Azure Stack hub-környezet beállítási funkcióinak meghatározása

Az Azure Stack hub-felhő helyes végpontokkal való regisztrálásához használja a következő kódot:

```java
// Get Azure Stack Hub cloud endpoints
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

Az előző kódban lévő `getActiveDirectorySettings` hívás lekéri a végpontokat a metaadat-végpontokból. A megadott hívás környezeti változóit állítja be:

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

## <a name="samples-using-api-profiles"></a>API-profilokat használó minták

Használja a következő GitHub-mintákat referenciáként a .NET-és Azure Stack hub API-profilokkal való megoldások létrehozásához:

- [Erőforráscsoportok kezelése](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

- [Tárfiókok kezelése](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

- [Virtuális gép kezelése](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm) (2019-03-01-hibrid profillal frissítve)

### <a name="sample-unit-test-project"></a>Minta egység tesztelési projekt

1. A tárház klónozása a következő paranccsal:

   ```shell
   git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`
   ```

2. Hozzon létre egy Azure-szolgáltatásnevet, és rendeljen hozzá egy szerepkört az előfizetéshez való hozzáféréshez. Az egyszerű szolgáltatásnév létrehozásával kapcsolatos utasításokért lásd: [Azure PowerShell használata egy egyszerű szolgáltatásnév létrehozásához tanúsítvánnyal](../operator/azure-stack-create-service-principals.md).

3. Kérje le a következő szükséges környezeti változókat:

   - `AZURE_TENANT_ID`
   - `AZURE_CLIENT_ID`
   - `AZURE_CLIENT_SECRET`
   - `AZURE_SUBSCRIPTION_ID`
   - `ARM_ENDPOINT`
   - `RESOURCE_LOCATION`

4. Állítsa be az alábbi környezeti változókat a parancssorból létrehozott egyszerű szolgáltatásból beolvasott adatok használatával:

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Hub Resource Manager URL}`
   - `export RESOURCE_LOCATION={location of Azure Stack Hub}`

   A Windows rendszerben az **Exportálás**helyett a **készletet** használja.

5. Az Azure Resource Manager metaadat-végpontok beolvasásához használja a `getActiveDirectorySettings` függvényt.

    ```java
    // Get Azure Stack Hub cloud endpoints
    final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);
    ```

6. A **Pom. XML** fájlban adja hozzá a következő függőséget a Azure Stack hub **2019-03-01-Hybrid** profiljának használatához. Ez a függőség telepíti a profilhoz társított modulokat a számítási, hálózati, tárolási, Key Vault és App Services erőforrás-szolgáltatók számára:

   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <vers1s.0.0-beta</version>
   </dependency>
   ```

7. A környezeti változók beállításához megnyíló parancssorba írja be a következő parancsot:

   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Következő lépések

Az API-profilokról további információt a következő témakörben talál:

- [A Azure Stack hub verziójának profiljai](azure-stack-version-profiles.md)
- [A profilok által támogatott erőforrás-szolgáltatói API-verziók](azure-stack-profiles-azure-resource-manager-versions.md)
