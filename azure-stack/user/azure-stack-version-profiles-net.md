---
title: API-verziók profiljainak használata a .NET-ben Azure Stackban | Microsoft Docs
description: Ismerje meg, hogyan használhatók az API-verziók profiljai a .NET SDK-val a Azure Stackban.
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
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 6c2fd698efb6990862887a758ce1b44021bd13a7
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72282880"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Az API-verziók profiljainak használata a .NET-ben Azure Stack

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Azure Stack Resource Managerhez készült .NET SDK eszközöket biztosít az infrastruktúra kiépítéséhez és kezeléséhez. Az SDK erőforrás-szolgáltatói közé tartozik a számítás, a hálózatkezelés, a tárolás, a App Services és a [Key Vault](/azure/key-vault/key-vault-whatis). A .NET SDK 14 NuGet-csomagot tartalmaz. Ezeket a csomagokat a projekt minden egyes fordításakor le kell töltenie a megoldásba. Azonban pontosan letöltheti a **2019-03-01 hibrid** vagy **2018-03-01-hibrid** verzióhoz használni kívánt erőforrás-szolgáltatót, hogy optimalizálja a memóriát az alkalmazás számára. Minden csomag egy erőforrás-szolgáltatóból, a megfelelő API-verzióból és az API-profilból áll, amelyhez tartozik. A .NET SDK-ban található API-profilok lehetővé teszik a hibrid felhőalapú fejlesztést azáltal, hogy a Azure Stack globális Azure-erőforrásai és-erőforrásai közötti váltást segítik.

## <a name="net-and-api-version-profiles"></a>.NET-és API-verziók profiljai

Az API-profilok erőforrás-szolgáltatók és API-verziók kombinációja. Az erőforrás-szolgáltatói csomagban található egyes erőforrástípusok legújabb, legstabilabb verzióját az API-profil segítségével szerezheti be.

- Az összes szolgáltatás legújabb verziójának használatához használja a csomagok **legújabb** profilját. Ez a profil a **Microsoft. Azure. Management** NuGet csomag része.

- A Azure Stack-kompatibilis szolgáltatások használatához használja az alábbi csomagok egyikét:
  - **Microsoft. Azure. Management. profiles. Hybrid @ no__t-12019 @ no__t-203 @ no__t-301. <*ResourceProvider*>. 0.9.0-preview. nupkg** 
  - **Microsoft. Azure. Management. profiles. Hybrid @ no__t-12018 @ no__t-203 @ no__t-301. <*ResourceProvider*>. 0.9.0-preview. nupkg**

  Győződjön meg arról, hogy a fenti NuGet-csomag **ResourceProvider** része a megfelelő szolgáltatóra módosul.

- A szolgáltatás legújabb API-verziójának használatához használja az adott NuGet-csomag **legújabb** profilját. Ha például csak a számítási szolgáltatás **legújabb API-** verzióját szeretné használni, használja a **számítási** csomag **legújabb** profilját. A **legújabb** profil a **Microsoft. Azure. Management** NuGet csomag része.

- Ha adott erőforrás-szolgáltatóhoz adott API-verziókat szeretne használni, használja a csomagban definiált adott API-verziókat.

Az alkalmazás összes beállítását egyesítheti.

## <a name="install-the-azure-net-sdk"></a>Az Azure .NET SDK telepítése

- Telepítse a git-t. Útmutatásért lásd: [Első lépések – git telepítése][].

- A megfelelő NuGet-csomagok telepítéséhez lásd: [Csomag keresése és telepítése][].

- A telepítendő csomagok a használni kívánt profil verziójától függenek. A profilok nevei a következők:

   - **Microsoft. Azure. Management. profiles. Hybrid @ no__t-12019 @ no__t-203 @ no__t-301. <*ResourceProvider*>. 0.9.0-preview. nupkg**

   - **Microsoft. Azure. Management. profiles. Hybrid @ no__t-12018 @ no__t-203 @ no__t-301. <*ResourceProvider*>. 0.9.0-preview. nupkg**

- A Visual Studio Code-hoz készült helyes NuGet-csomagok telepítéséhez tekintse meg a következő hivatkozást, ahol letöltheti a [A NuGet csomagkezelő utasításai][].

- Ha nem érhető el, hozzon létre egy előfizetést, és mentse az előfizetés-azonosítót későbbi használatra. További információ az előfizetések létrehozásáról: [Előfizetések létrehozása a Azure Stack ajánlatokhoz][].

- Hozzon létre egy szolgáltatásnevet, és mentse az ügyfél-azonosítót és az ügyfél titkos kulcsát. További információ az Azure Stack egyszerű szolgáltatásának létrehozásáról: [alkalmazások Azure Stackhoz való hozzáférésének biztosítása][]. Az ügyfél-azonosító a szolgáltatásnév létrehozásakor az alkalmazás-azonosító néven is ismert.

- Győződjön meg arról, hogy a szolgáltatásnév közreműködői/tulajdonosi szerepkörrel rendelkezik az előfizetésében. További információ a szerepkör az egyszerű szolgáltatáshoz való hozzárendeléséről: [alkalmazások Azure Stackhoz való hozzáférésének biztosítása][].

## <a name="prerequisites"></a>Előfeltételek

Ha a .NET Azure SDK-t Azure Stack használatával szeretné használni, a következő értékeket kell megadnia, majd az értékeket környezeti változókkal kell beállítania. A környezeti változók megadásához tekintse meg az adott operációs rendszer táblázatát követő utasításokat.

| Value (Díj)                     | Környezeti változók   | Leírás                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Bérlőazonosító                 | `AZURE_TENANT_ID `      | A Azure Stack [*bérlő azonosítójának*][]értéke.                                                                          |
| Ügyfél-azonosító                 | `AZURE_CLIENT_ID `      | Az egyszerű szolgáltatásnév a jelen cikk előző szakaszában a szolgáltatásnév létrehozásakor mentve. |
| Subscription ID (Előfizetés azonosítója)           | `AZURE_SUBSCRIPTION_ID` | Az [*előfizetés-azonosító*][] az Azure stack-ban elérhető ajánlatok elérési módja.                                                      |
| Ügyfél titka             | `AZURE_CLIENT_SECRET`   | A szolgáltatás egyszerű alkalmazásának titkos kulcsa a szolgáltatásnév létrehozásakor mentve.                                      |
| Resource Manager-végpont | `ARM_ENDPOINT`          | Lásd [*a Azure stack Resource Manager-végpontot*][].                                                                    |
| Földrajzi egység                  | `RESOURCE_LOCATION`     | Azure Stack helye.

A Azure Stack bérlői AZONOSÍTÓjának megkereséséhez kövesse az [ebben a cikkben szereplő](../operator/azure-stack-csp-ref-operations.md)utasításokat. A környezeti változók beállításához tegye a következőket:

### <a name="windows"></a>Windows

A környezeti változók Windows-parancssorban történő beállításához használja a következő formátumot:

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS-, Linux-és UNIX-alapú rendszerek

UNIX-alapú rendszerekben használja a következő parancsot:

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>A Azure Stack Resource Manager-végpont

A Azure Resource Manager egy felügyeleti keretrendszer, amely lehetővé teszi a rendszergazdák számára az Azure-erőforrások üzembe helyezését, kezelését és figyelését. A Azure Resource Manager a feladatokat nem külön, hanem csoportként, egyetlen művelet során is kezelhetik.

A metaadat-információkat a Resource Manager-végpontból kérheti le. A végpont egy JSON-fájlt ad vissza, amely a kód futtatásához szükséges adatokkal rendelkezik.

Vegye figyelembe az alábbi szempontokat:

- A Azure Stack Development Kit **ResourceManagerUrl** (ASDK) a következő: https://management.local.azurestack.external/.

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
         "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Meglévő API-profilok

- **Microsoft. Azure. Management. profiles. Hybrid @ no__t-12019 @ no__t-203 @ no__t-301. <*ResourceProvider*>. 0.9.0-preview. nupkg**: a legújabb, Azure Stackra épülő profil. Ezt a profilt olyan szolgáltatásokhoz használhatja, amelyek kompatibilisek Azure Stackval, feltéve, hogy a 1904-es vagy újabb verziójával rendelkezik.

- **Microsoft. Azure. Management. profiles. Hybrid @ no__t-12018 @ no__t-203 @ no__t-301. <*ResourceProvider*>. 0.9.0-preview. nupkg**: használja ezt a profilt, hogy a szolgáltatások kompatibilisek legyenek a 1808-es vagy újabb verziókkal Azure stack.

- **Legújabb**: az összes szolgáltatás legújabb verzióit tartalmazó profil. Használja az összes szolgáltatás legújabb verzióit. Ez a profil a **Microsoft. Azure. Management** NuGet csomag része.

A Azure Stack-és API-profilokkal kapcsolatos további információkért tekintse meg az [API-profilok összefoglalása][]ismertető témakört.

## <a name="azure-net-sdk-api-profile-usage"></a>Azure .NET SDK API-profil használata

Az alábbi kód használatával hozhat létre erőforrás-kezelési ügyfelet. Hasonló kód használható más erőforrás-szolgáltatói ügyfelek (például számítás, hálózat és tárolás) létrehozásához.

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId;
};
```

Az ügyfél létrehozásához a kódban `credentials` paraméter szükséges. A következő kód létrehoz egy hitelesítési jogkivonatot a bérlő azonosítója és az egyszerű szolgáltatásnév szerint:

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```

A kód `getActiveDirectoryServiceSettings` hívása Azure Stack végpontokat kérdez le a metaadat-végpontból. A megadott hívás környezeti változóit állítja be:

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

Ezek a lépések lehetővé teszik, hogy az API-profil NuGet csomagjaival üzembe helyezse az alkalmazást a Azure Stackához.

## <a name="samples-using-api-profiles"></a>API-profilokat használó minták

A következő mintákat használhatja a .NET-tel és a Azure Stack API-profilokkal való megoldások létrehozásához:

- [Erőforráscsoportok kezelése](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Storage-fiókok kezelése](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Virtuális gép kezelése](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm): Ez a példa a Azure stack által támogatott **2019-03-01-hibrid** profilt használja.

## <a name="next-steps"></a>Következő lépések

További információ az API-profilokról:

- [API-verzióprofilok kezelése az Azure Stackben](azure-stack-version-profiles.md)
- [A profilok által támogatott erőforrás-szolgáltatói API-verziók](azure-stack-profiles-azure-resource-manager-versions.md)

  [Első lépések – git telepítése]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Csomag keresése és telepítése]: /nuget/tools/package-manager-ui
  [A NuGet csomagkezelő utasításai]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Előfizetések létrehozása a Azure Stack ajánlatokhoz]: ../operator/azure-stack-subscribe-plan-provision-vm.md
  [Alkalmazások Azure Stackhoz való hozzáférésének biztosítása]: ../operator/azure-stack-create-service-principals.md
  [* Bérlő azonosítója *]: ../operator/azure-stack-identity-overview.md
  [* előfizetés azonosítója *]: ../operator/service-plan-offer-subscription-overview.md#subscriptions
  [* a Azure Stack Resource Manager-végpont *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [API-profilok összefoglalása]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../operator/azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
