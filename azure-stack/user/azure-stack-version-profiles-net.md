---
title: Az API-verziók profiljainak használata a .NET-ben Azure Stack hub-ban
description: Ismerje meg, hogyan használható az API-verziók profiljai a .NET SDK-val Azure Stack hub-ban.
author: sethmanheim
ms.topic: article
ms.date: 12/2/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 629c50225bc7544512b0ccb1333e56ef1e7cf07a
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525812"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack-hub"></a>Az API-verziók profiljainak használata a .NET-ben Azure Stack hub-ban

A Azure Stack hub Resource Managerhez készült .NET SDK olyan eszközöket biztosít, amelyek segítenek az infrastruktúra kiépítésében és kezelésében. Az SDK erőforrás-szolgáltatói közé tartozik a számítás, a hálózatkezelés, a tárolás, a App Services és a [Key Vault](/azure/key-vault/key-vault-whatis). A .NET SDK 14 NuGet-csomagot tartalmaz. Ezeket a csomagokat a projekt minden egyes fordításakor le kell töltenie a megoldásba. Azonban pontosan letöltheti a **2019-03-01 hibrid** vagy **2018-03-01-hibrid** verzióhoz használni kívánt erőforrás-szolgáltatót, hogy optimalizálja a memóriát az alkalmazás számára. Minden csomag egy erőforrás-szolgáltatóból, a megfelelő API-verzióból és az API-profilból áll, amelyhez tartozik. A .NET SDK-ban található API-profilok lehetővé teszik a hibrid felhőalapú fejlesztést azáltal, hogy a Azure Stack hub globális Azure-erőforrásai és-erőforrásai közötti váltást segítik.

## <a name="net-and-api-version-profiles"></a>.NET-és API-verziók profiljai

Az API-profilok erőforrás-szolgáltatók és API-verziók kombinációja. Az erőforrás-szolgáltatói csomagban található egyes erőforrástípusok legújabb, legstabilabb verzióját az API-profil segítségével szerezheti be.

- Az összes szolgáltatás legújabb verziójának használatához használja a csomagok **legújabb** profilját. Ez a profil a **Microsoft. Azure. Management** NuGet csomag része.

- Azure Stack hub-kompatibilis szolgáltatások használatához használja az alábbi csomagok egyikét:
  - **Microsoft. Azure. Management. profiles. Hybrid \_ 2019 \_ 03 \_ 01. <*ResourceProvider*>.0.9.0 – előzetes verzió. nupkg**
  - **Microsoft. Azure. Management. profiles. Hybrid \_ 2018 \_ 03 \_ 01. <*ResourceProvider*>.0.9.0 – előzetes verzió. nupkg**

  Győződjön meg arról, hogy a fenti NuGet-csomag **ResourceProvider** része a megfelelő szolgáltatóra módosul.

- A szolgáltatás legújabb API-verziójának használatához használja az adott NuGet-csomag **legújabb** profilját. Ha például csak a számítási szolgáltatás **legújabb API-** verzióját szeretné használni, használja a **számítási** csomag **legújabb** profilját. A **legújabb** profil a **Microsoft. Azure. Management** NuGet csomag része.

- Ha adott erőforrás-szolgáltatóhoz adott API-verziókat szeretne használni, használja a csomagban definiált adott API-verziókat.

Az alkalmazás összes beállítását egyesítheti.

## <a name="install-the-azure-net-sdk"></a>Az Azure .NET SDK telepítése

- Telepítse a git-t. Útmutatásért lásd: [első lépések – a git telepítése][].

- A megfelelő NuGet-csomagok telepítéséhez lásd: [csomagok megkeresése és telepítése][].

- A telepítendő csomagok a használni kívánt profil verziójától függenek. A profilok nevei a következők:

  - **Microsoft. Azure. Management. profiles. Hybrid \_ 2019 \_ 03 \_ 01. <*ResourceProvider*>.0.9.0 – előzetes verzió. nupkg**

  - **Microsoft. Azure. Management. profiles. Hybrid \_ 2018 \_ 03 \_ 01. <*ResourceProvider*>.0.9.0 – előzetes verzió. nupkg**

- A Visual Studio Code-hoz készült helyes NuGet-csomagok telepítéséhez tekintse meg a következő hivatkozást, ahol letöltheti a [NuGet Package Manager utasításait][].

- Ha nem érhető el, hozzon létre egy előfizetést, és mentse az előfizetés-azonosítót későbbi használatra. További információ az előfizetések létrehozásáról: [előfizetések létrehozása az Azure stack hub-beli ajánlatokhoz][].

- Hozzon létre egy szolgáltatásnevet, és mentse az ügyfél-azonosítót és az ügyfél titkos kulcsát. További információ az Azure Stack hub egyszerű szolgáltatásának létrehozásáról: [alkalmazások Azure stack hub elérésének biztosítása][]. Az ügyfél-azonosító a szolgáltatásnév létrehozásakor az alkalmazás-azonosító néven is ismert.

- Győződjön meg arról, hogy a szolgáltatásnév közreműködői/tulajdonosi szerepkörrel rendelkezik az előfizetésében. További információ a szerepkör az egyszerű szolgáltatáshoz való hozzárendeléséről: [alkalmazások Azure stack hubhoz való hozzáférésének biztosítása][].

## <a name="prerequisites"></a>Előfeltételek

Ha Azure Stack hubhoz szeretné használni a .NET Azure SDK-t, a következő értékeket kell megadnia, majd az értékeket környezeti változókkal kell beállítania. A környezeti változók megadásához tekintse meg az adott operációs rendszer táblázatát követő utasításokat.

| Érték                     | Környezeti változók   | Leírás                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Bérlőazonosító                 | `AZURE_TENANT_ID `      | Az Azure Stack hub- [*bérlő azonosítójának*][]értéke.                                                                          |
| Ügyfél-azonosító                 | `AZURE_CLIENT_ID `      | Az egyszerű szolgáltatásnév a jelen cikk előző szakaszában a szolgáltatásnév létrehozásakor mentve. |
| Előfizetés azonosítója           | `AZURE_SUBSCRIPTION_ID` | Az [*előfizetés-azonosító*][] az Azure stack hub-ban található ajánlatok elérésének módja.                                                      |
| Titkos ügyfélkulcs             | `AZURE_CLIENT_SECRET`   | A szolgáltatás egyszerű alkalmazásának titkos kulcsa a szolgáltatásnév létrehozásakor mentve.                                      |
| Resource Manager-végpont | `ARM_ENDPOINT`          | Tekintse meg [*az Azure stack hub Resource Manager-végpontot*][].                                                                    |
| Hely                  | `RESOURCE_LOCATION`     | Azure Stack hub helye.

Az Azure Stack hub bérlői AZONOSÍTÓjának megkereséséhez kövesse az [ebben a cikkben található](../operator/azure-stack-csp-ref-operations.md)utasításokat. A környezeti változók beállításához tegye a következőket:

### <a name="windows"></a>Windows

A környezeti változók Windows-parancssorban történő beállításához használja a következő formátumot:

```shell
set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>MacOS-, Linux-és UNIX-alapú rendszerek

UNIX-alapú rendszerekben használja a következő parancsot:

```shell
export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-hub-resource-manager-endpoint"></a>Az Azure Stack hub Resource Manager-végpont

A Azure Resource Manager egy felügyeleti keretrendszer, amely lehetővé teszi a rendszergazdák számára az Azure-erőforrások üzembe helyezését, kezelését és figyelését. A Azure Resource Manager a feladatokat nem külön, hanem csoportként, egyetlen művelet során is kezelhetik.

A metaadat-információkat a Resource Manager-végpontból kérheti le. A végpont egy JSON-fájlt ad vissza, amely a kód futtatásához szükséges adatokkal rendelkezik.

Vegye figyelembe az alábbi szempontokat:

- A Azure Stack Development Kit **ResourceManagerUrl** (ASDK) a következő: `https://management.local.azurestack.external/` .

- Az integrált rendszerek **ResourceManagerUrl** a (z `https://management.region.<fqdn>/` ):, ahol a a `<fqdn>` teljes tartománynév.
A szükséges metaadatok beolvasása: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` .

Példa JSON-fájlra:

```json
{
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
         "loginEndpoint": "https://login.windows.net/",
         "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Meglévő API-profilok

- **Microsoft. Azure. Management. profiles. hybrid \_ 2019 \_ 03 \_ 01. <*ResourceProvider*>.0.9.0-preview. nupkg**: az Azure stack hub-hoz készült legújabb profil. Ezt a profilt olyan szolgáltatásokhoz használhatja, amelyek kompatibilisek Azure Stack hubhoz, feltéve, hogy a 1904-es vagy újabb verzióval rendelkezik.

- **Microsoft. Azure. Management. profiles. hybrid \_ 2018 \_ 03 \_ 01. <*ResourceProvider*>.0.9.0-preview. nupkg**: használja ezt a profilt, hogy a szolgáltatások kompatibilisek legyenek az Azure stack hub 1808-es vagy újabb verzióival.

- **Legújabb**: az összes szolgáltatás legújabb verzióit tartalmazó profil. Használja az összes szolgáltatás legújabb verzióit. Ez a profil a **Microsoft. Azure. Management** NuGet csomag része.

Az Azure Stack hub-és API-profilokkal kapcsolatos további információkért tekintse meg az [API-profilok összefoglalását][]ismertető témakört.

## <a name="azure-net-sdk-api-profile-usage"></a>Azure .NET SDK API-profil használata

Az alábbi kód használatával hozhat létre erőforrás-kezelési ügyfelet. Hasonló kód használható más erőforrás-szolgáltatói ügyfelek (például számítás, hálózat és tárolás) létrehozásához.

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId;
};
```

Az `credentials` ügyfél létrehozásához az ebben a kódban szereplő paraméter szükséges. A következő kód létrehoz egy hitelesítési jogkivonatot a bérlő azonosítója és az egyszerű szolgáltatásnév szerint:

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```

A `getActiveDirectoryServiceSettings` kód hívása Azure stack hub-végpontot kér le a metaadat-végpontból. A megadott hívás környezeti változóit állítja be:

```csharp
public static ActiveDirectoryServiceSettings getActiveDirectoryServiceSettings(string armEndpoint)
{
    var settings = new ActiveDirectoryServiceSettings();
    try
    {
        var request = (HttpWebRequest)HttpWebRequest.Create(string.Format("{0}/metadata/endpoints?api-version=1.0", armEndpoint));
        request.Method = "GET";
        request.UserAgent = ComponentName;
        request.Accept = "application/xml";
        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            using (StreamReader sr = new StreamReader(response.GetResponseStream()))
            {
                var rawResponse = sr.ReadToEnd();
                var deserialized = JObject.Parse(rawResponse);
                var authenticationObj = deserialized.GetValue("authentication").Value<JObject>();
                var loginEndpoint = authenticationObj.GetValue("loginEndpoint").Value<string>();
                var audiencesObj = authenticationObj.GetValue("audiences").Value<JArray>();
                settings.AuthenticationEndpoint = new Uri(loginEndpoint);
                settings.TokenAudience = new Uri(audiencesObj[0].Value<string>());
                settings.ValidateAuthority = loginEndpoint.TrimEnd('/').EndsWith("/adfs", StringComparison.OrdinalIgnoreCase) ? false : true;
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(String.Format("Could not get AD service settings. Exception: {0}", ex.Message));
    }
    return settings;
}
```

Ezekkel a lépésekkel az API-profil NuGet csomagjaival telepítheti az alkalmazást Azure Stack hub-ra.

## <a name="samples-using-api-profiles"></a>API-profilokat használó minták

A következő mintákat használhatja a .NET-tel és az Azure Stack hub API-profilokkal való megoldások létrehozásához:

- [Erőforráscsoportok kezelése](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Storage-fiókok kezelése](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Virtuális gép kezelése](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm): Ez a példa a Azure stack hub által támogatott **2019-03-01-hibrid** profilt használja.

## <a name="next-steps"></a>További lépések

További információ az API-profilokról:

- [API-verziók profiljainak kezelése Azure Stack hub-ban](azure-stack-version-profiles.md)
- [A profilok által támogatott erőforrás-szolgáltatói API-verziók](azure-stack-profiles-azure-resource-manager-versions.md)

  [Első lépések – git telepítése]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Csomag keresése és telepítése]: /nuget/tools/package-manager-ui
  [A NuGet csomagkezelő utasításai]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Előfizetések létrehozása a Azure Stack hub ajánlatai számára]: ../operator/azure-stack-subscribe-plan-provision-vm.md
  [Azure Stack hub elérésének biztosítása az alkalmazások számára]: ../operator/azure-stack-create-service-principals.md
  [* Bérlő azonosítója *]: ../operator/azure-stack-identity-overview.md
  [* előfizetés azonosítója *]: ../operator/service-plan-offer-subscription-overview.md#subscriptions
  [* Az Azure Stack hub Resource Manager-végpont *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint
  [API-profilok összefoglalása]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../operator/azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
