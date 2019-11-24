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
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>API-verzióprofilok használata a .NET-tel az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A .NET SDK-t az Azure Stack Resource Manager biztosít eszközöket és az infrastruktúra kezelését. Az SDK erőforrás-szolgáltatói közé tartozik a számítás, a hálózatkezelés, a tárolás, a App Services és a [Key Vault](/azure/key-vault/key-vault-whatis). A .NET SDK 14 NuGet-csomagot tartalmaz. Ezeket a csomagokat a projekt minden egyes fordításakor le kell töltenie a megoldásba. Azonban pontosan letöltheti a **2019-03-01 hibrid** vagy **2018-03-01-hibrid** verzióhoz használni kívánt erőforrás-szolgáltatót, hogy optimalizálja a memóriát az alkalmazás számára. Minden csomag áll egy erőforrás-szolgáltató, a megfelelő API-verzió és az API-profilt, amelyhez tartozik. A .NET SDK API-profilok engedélyezése a hibrid felhőalapú fejlesztés segít a globális Azure-erőforrások és az Azure Stacken erőforrások közötti váltáshoz.

## <a name="net-and-api-version-profiles"></a>.NET- és API-verzióprofilok

Egy API-profil az erőforrás-szolgáltatók és API-verziók. Az erőforrás-szolgáltatói csomagban található egyes erőforrástípusok legújabb, legstabilabb verzióját az API-profil segítségével szerezheti be.

- Az összes szolgáltatás legújabb verzióját használni, hogy a **legújabb** profil olyan csomagot. Ez a profil része a **Microsoft.Azure.Management** NuGet-csomagot.

- A Azure Stack-kompatibilis szolgáltatások használatához használja az alábbi csomagok egyikét:
  - **Microsoft.Azure.Management.Profiles.hybrid\_2019\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg** 
  - **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**

  Ügyeljen arra, hogy a **ResourceProvider** a fenti NuGet-csomag része a megfelelő szolgáltató értékűre változik.

- A szolgáltatás legújabb API-verziójának használatához használja az adott NuGet-csomag **legújabb** profilját. Ha például csak a számítási szolgáltatás **legújabb API-** verzióját szeretné használni, használja a **számítási** csomag **legújabb** profilját. A **legújabb** profil része a **Microsoft.Azure.Management** NuGet-csomagot.

- Ha adott erőforrás-szolgáltatóhoz adott API-verziókat szeretne használni, használja a csomagban definiált adott API-verziókat.

Az alkalmazás összes beállítását egyesítheti.

## <a name="install-the-azure-net-sdk"></a>Az Azure .NET SDK telepítése

- A Git telepítése. Útmutatásért lásd: [Első lépések – a Git telepítése][].

- A megfelelő NuGet-csomagok telepítése: [keresés és a egy csomag telepítése][].

- A telepítendő csomagok a használni kívánt profil verziójától függenek. A profilok nevei a következők:

   - **Microsoft.Azure.Management.Profiles.hybrid\_2019\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**

   - **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**

- A megfelelő NuGet-csomagok telepítése a Visual Studio Code, tekintse meg a következő hivatkozásra kattintva töltse le a [NuGet-Csomagkezelő utasítások][].

- Ha nem érhető el, hozzon létre egy előfizetést és az előfizetés-azonosító későbbi felhasználás céljából. További információ az előfizetések létrehozásáról: [Ajánlatok, előfizetések létrehozása az Azure Stackben][].

- Hozzon létre egy szolgáltatásnevet, és mentse az ügyfél-azonosítót és az ügyfél titkos kulcsát. További információ az Azure Stack egyszerű szolgáltatásának létrehozásáról: [Alkalmazások elérése az Azure Stackhez][]. Az ügyfél-azonosító a szolgáltatásnév létrehozásakor az alkalmazás-azonosító néven is ismert.

- Ellenőrizze, hogy az egyszerű szolgáltatást a közreműködői és tulajdonosi szerepkör-előfizetésében. További információ a szerepkör az egyszerű szolgáltatáshoz való hozzárendeléséről: [Alkalmazások elérése az Azure Stackhez][].

## <a name="prerequisites"></a>Előfeltételek

Ha a .NET Azure SDK-t Azure Stack használatával szeretné használni, a következő értékeket kell megadnia, majd az értékeket környezeti változókkal kell beállítania. A környezeti változók megadásához tekintse meg az adott operációs rendszer táblázatát követő utasításokat.

| Érték                     | Környezeti változók   | Leírás                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Bérlőazonosító                 | `AZURE_TENANT_ID `      | Az Azure Stack értékét [ *bérlőazonosító*][].                                                                          |
| Ügyfél-azonosító                 | `AZURE_CLIENT_ID `      | Az egyszerű szolgáltatásnév a jelen cikk előző szakaszában a szolgáltatásnév létrehozásakor mentve. |
| Előfizetés azonosítója           | `AZURE_SUBSCRIPTION_ID` | A [ *előfizetés-azonosító* ][] van, hogy miként férhetnek hozzá az ajánlatok az Azure Stackben.                                                      |
| Titkos ügyfélkulcs             | `AZURE_CLIENT_SECRET`   | A szolgáltatás egyszerű alkalmazásának titkos kulcsa a szolgáltatásnév létrehozásakor mentve.                                      |
| Resource Manager-végpont | `ARM_ENDPOINT`          | Lásd [*a Azure stack Resource Manager-végpontot*][].                                                                    |
| Tartózkodási hely                  | `RESOURCE_LOCATION`     | Azure Stack helye.

A Azure Stack bérlői AZONOSÍTÓjának megkereséséhez kövesse az [ebben a cikkben szereplő](../operator/azure-stack-csp-ref-operations.md)utasításokat. A környezeti változók beállítása, tegye a következőket:

### <a name="windows"></a>Windows

A környezeti változók beállítása egy Windows parancssorban, használja a következő formátumot:

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS, Linux és Unix-alapú rendszerek

UNIX-alapú rendszerekben használja a következő parancsot:

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>A Azure Stack Resource Manager-végpont

A Azure Resource Manager egy felügyeleti keretrendszer, amely lehetővé teszi a rendszergazdák számára az Azure-erőforrások üzembe helyezését, kezelését és figyelését. Az Azure Resource Manager képes kezelni ezeket a feladatokat, csoportként, nem pedig külön-külön, egyetlen művelettel.

A metaadat-információkat a Resource Manager-végpontból kérheti le. A végpont egy JSON-fájlt ad vissza, amely a kód futtatásához szükséges adatokkal rendelkezik.

Vegye figyelembe az alábbiakat:

- A Azure Stack Development Kit **ResourceManagerUrl** (ASDK) a következő: https://management.local.azurestack.external/.

- Az integrált rendszerek **ResourceManagerUrl** : `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`.
A szükséges metaadatok beolvasása: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

JSON-mintafájlt:

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

- **Microsoft. Azure. Management. profiles. hybrid\_2019\_03\_01. <*ResourceProvider*>. 0.9.0-preview. nupkg**: a legújabb profil a Azure stack számára készült. Ezt a profilt olyan szolgáltatásokhoz használhatja, amelyek kompatibilisek Azure Stackval, feltéve, hogy a 1904-es vagy újabb verziójával rendelkezik.

- **Microsoft. Azure. Management. profiles. hybrid\_2018\_03\_01. <*ResourceProvider*>. 0.9.0-preview. nupkg**: használja ezt a profilt, hogy a szolgáltatások kompatibilisek legyenek a 1808-es vagy újabb verziójú Azure Stackekkel.

- **Legújabb**: az összes szolgáltatást a legújabb verziókat álló profilt. Az összes szolgáltatást a legújabb verziókat használhatja. Ez a profil része a **Microsoft.Azure.Management** NuGet-csomagot.

A Azure Stack-és API-profilokkal kapcsolatos további információkért tekintse meg az [API-profilok összefoglalása][]ismertető témakört.

## <a name="azure-net-sdk-api-profile-usage"></a>Azure .NET SDK API-profil használata

Az alábbi kód használatával hozhat létre erőforrás-kezelési ügyfelet. Hasonló kód használható más erőforrás-szolgáltatói ügyfelek (például számítás, hálózat és tárolás) létrehozásához.

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId;
};
```

Az ügyfél létrehozásához az ebben a kódban szereplő `credentials` paraméter szükséges. A következő kód létrehoz egy hitelesítési jogkivonatot a bérlő azonosítója és az egyszerű szolgáltatásnév szerint:

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```

A kód `getActiveDirectoryServiceSettings` hívása lekéri Azure Stack végpontokat a metaadat-végpontból. A megadott hívás környezeti változóit állítja be:

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

## <a name="samples-using-api-profiles"></a>API-profilok használatával minták

A következő mintákat használhatja a .NET-tel és a Azure Stack API-profilokkal való megoldások létrehozásához:

- [Erőforráscsoportok kezelése](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Storage-fiókok kezelése](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Virtuális gép kezelése](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm): Ez a példa a Azure stack által támogatott **2019-03-01-hibrid** profilt használja.

## <a name="next-steps"></a>További lépések

További információ az API-profilokról:

- [API-verzióprofilok kezelése az Azure Stackben](azure-stack-version-profiles.md)
- [Erőforrás-szolgáltató API-ja verziókat támogatja profilok](azure-stack-profiles-azure-resource-manager-versions.md)

  [Első lépések – a Git telepítése]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Keresés és a egy csomag telepítése]: /nuget/tools/package-manager-ui
  [NuGet-Csomagkezelő utasítások]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Ajánlatok, előfizetések létrehozása az Azure Stackben]: ../operator/azure-stack-subscribe-plan-provision-vm.md
  [Alkalmazások elérése az Azure Stackhez]: ../operator/azure-stack-create-service-principals.md
  [* Bérlői azonosító *]: ../operator/azure-stack-identity-overview.md
  [* előfizetési azonosító *]: ../operator/service-plan-offer-subscription-overview.md#subscriptions
  [* a Azure Stack Resource Manager-végpont *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [API-profilok összefoglalása]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../operator/azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
