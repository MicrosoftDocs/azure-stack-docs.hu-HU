---
title: API-verzióprofilok használata a .NET SDK-t az Azure Stackben |} A Microsoft Docs
description: Ismerje meg az API-verzióprofilok használata a .NET-tel az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 1b81836c6262a73611ebfb2cc771ab74fd9f03fc
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782729"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>API-verzióprofilok használata a .NET-tel az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A .NET SDK-t az Azure Stack Resource Manager biztosít eszközöket és az infrastruktúra kezelését. Az SDK-t az erőforrás-szolgáltatók közé tartozik a számítási, hálózatkezelési, tárolási, alkalmazásszolgáltatások, és [KeyVault](/azure/key-vault/key-vault-whatis). A .NET SDK NuGet-csomagok 14 tartalmazza. Ezeket a csomagokat kell letölteni a projekt megoldás, amely magában foglalja a profiladatok minden alkalommal, amikor. Azonban kifejezetten letöltheti melyik erőforrás-szolgáltatót fogja használni a 2018-03-01-hibrid vagy 2017-03-09-profile annak érdekében, hogy az alkalmazás memóriájának optimalizálása. Minden csomag áll egy erőforrás-szolgáltató, a megfelelő API-verzió és az API-profilt, amelyhez tartozik. A .NET SDK API-profilok engedélyezése a hibrid felhőalapú fejlesztés segít a globális Azure-erőforrások és az Azure Stacken erőforrások közötti váltáshoz.

## <a name="net-and-api-version-profiles"></a>.NET- és API-verzióprofilok

Egy API-profil az erőforrás-szolgáltatók és API-verziók. Az API-profilok segítségével egy erőforrás-szolgáltatói csomag az egyes erőforrástípusok legújabb és legnagyobb stabil verziójának beszerzéséhez.

-   Az összes szolgáltatás legújabb verzióját használni, hogy a **legújabb** profil olyan csomagot. Ez a profil része a **Microsoft.Azure.Management** NuGet-csomagot.

-   Az Azure Stack kompatibilis szolgáltatások használatához a **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg** vagy **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg** csomagokat.

    -   Nincsenek mindegyik erőforrás-szolgáltató az egyes profilok két csomagot.

    -   Ügyeljen arra, hogy a **ResourceProvider** a fenti NuGet-csomag része a megfelelő szolgáltató értékűre változik.

-   A szolgáltatás legújabb API-verzióját használja, használja a **legújabb** profiljának adott NuGet-csomag. Például, ha használni szeretné a **legújabb API-** a számítási szolgáltatás önálló, használja a **legújabb** profiljának a **számítási** csomagot. A **legújabb** profil része a **Microsoft.Azure.Management** NuGet-csomagot.

-   API-verzióját az adott erőforrás-szolgáltató az erőforrástípushoz az adott API-verziók a csomagon belül definiált használatához.

Kombinálhatja a lehetőségek a ugyanazt az alkalmazást.

## <a name="install-the-azure-net-sdk"></a>Az Azure .NET SDK telepítése

1.  A Git telepítése. Útmutatásért lásd: [Első lépések – a Git telepítése][].

2.  A megfelelő NuGet-csomagok telepítése: [keresés és a egy csomag telepítése][].

3.  A csomagokat, amelyeket telepíteni kell a használni kívánt profilt verziójától függ. A csomag nevét, a profil-verziók a következők:

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**

4.  A megfelelő NuGet-csomagok telepítése a Visual Studio Code, tekintse meg a következő hivatkozásra kattintva töltse le a [NuGet-Csomagkezelő utasítások][].

5.  Ha nem érhető el, hozzon létre egy előfizetést és az előfizetés-azonosító későbbi felhasználás céljából. Hozzon létre egy előfizetést az utasításokért lásd: [ajánlatok, előfizetések létrehozása az Azure Stackben][].

6.  Hozzon létre egy egyszerű szolgáltatást, és mentse az ügyfél-Azonosítót és a titkos Ügyfélkulcsot. Az Azure stack-beli szolgáltatásnév létrehozása az utasításokért lásd: [Alkalmazások elérése az Azure Stackhez][]. Az ügyfél-azonosító esetén más néven az Alkalmazásazonosítót létrehozni egy szolgáltatásnevet.

7.  Ellenőrizze, hogy az egyszerű szolgáltatást a közreműködői és tulajdonosi szerepkör-előfizetésében. Szerepkör hozzárendelése egyszerű szolgáltatást, lásd: [Alkalmazások elérése az Azure Stackhez][].

## <a name="prerequisites"></a>Előfeltételek

Azure .NET SDK használata az Azure Stack használatával, adja meg a következő értékeket, és adja meg az értékeket a környezeti változókat. A környezeti változók beállítása, tekintse meg az alábbi táblázat az operációs rendszerének utasításokat.

| Érték                     | Környezeti változók   | Leírás                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Bérlőazonosító                 | AZURE_TENANT_ID       | Az Azure Stack értékét [ *bérlőazonosító*][].                                                                          |
| Ügyfél-azonosító                 | AZURE_CLIENT_ID       | A szolgáltatás egyszerű Alkalmazásazonosítót az egyszerű szolgáltatás létrehozásakor ez a cikk az előző szakaszban mentett. |
| Előfizetés azonosítója           | AZURE_SUBSCRIPTION_ID | A [ *előfizetés-azonosító* ][] van, hogy miként férhetnek hozzá az ajánlatok az Azure Stackben.                                                      |
| Titkos ügyfélkulcs             | AZURE_CLIENT_SECRET   | A szolgáltatás egyszerű alkalmazás titkos kulcs mentése az egyszerű szolgáltatás létrehozásakor.                                      |
| Resource Manager-végpont | ARM_ENDPOINT           | Lásd: [ *az Azure Stack Resource Manager-végpontot*][].                                                                    |
| Location egység                  | RESOURCE_LOCATION     | Az Azure Stack helye.

Az Azure stack a bérlő Azonosítójának megkereséséhez kövesse az utasításokat, található [Itt](../operator/azure-stack-csp-ref-operations.md). Állítsa be a környezeti változókat, hajtsa végre a következő lépéseket:

### <a name="microsoft-windows"></a>Microsoft Windows

A környezeti változók beállítása egy Windows parancssorban, használja a következő formátumot:

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS, Linux és Unix-alapú rendszerek

Unix-alapú rendszerekben használhatja a következő parancsot:

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Az Azure Stack Resource Manager-végpont

A Microsoft Azure Resource Manager-keretrendszert, amely lehetővé teszi a rendszergazdák üzembe helyezése, kezelése és monitorozása az Azure-erőforrások számára. Az Azure Resource Manager képes kezelni ezeket a feladatokat, csoportként, nem pedig külön-külön, egyetlen művelettel.

A metaadat-információkat is kérhet a Resource Manager-végpontot. A végpont egy JSON-fájlt a kód futtatásához szükséges adatokat adja vissza.

Vegye figyelembe az alábbiakat:

- A **ResourceManagerUrl** van az Azure Stack Development Kit (ASDK): https://management.local.azurestack.external/

- A **ResourceManagerUrl** integrált rendszerek van: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` A szükséges metaadatokat lekéréséhez: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

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

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg**: Az Azure Stackhez készült legújabb profil. Ez a profil Services kompatibilis az Azure Stack mindaddig, amíg a 1808 blokk vagy a további használhatják.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**: Ha Ön egy alacsonyabb, mint a 1808 build stamp, használja ezt a profilt.

3.  **Legújabb**: Az összes szolgáltatást a legújabb verziókat álló profilt. Az összes szolgáltatást a legújabb verziókat használhatja. Ez a profil része a **Microsoft.Azure.Management** NuGet-csomagot.

Az Azure Stacket és API-profilokkal kapcsolatos további információkért lásd: egy [API-profilok összefoglalása][].

## <a name="azure-net-sdk-api-profile-usage"></a>Azure .NET SDK API-profil-használat

A következő kódot a resource management-ügyfél vezérlőként használandó. Hasonló kód segítségével hozza létre a többi erőforrás-szolgáltató (például számítási, hálózati és tárolási) ügyfeleket. 

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId
};
```

A `credentials` paraméter a fenti kód szükséges ügyfél-példányt létrehozni. A következő kódot állít elő egy hitelesítési tokent a bérlő Azonosítóját és az egyszerű szolgáltatás által.

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```
A `getActiveDirectoryServiceSettings` hívni a kódot az Azure Stack-végpontok lekéri a metaadatok végpontján. Meghatározza a végrehajtott hívás környezeti változókat: 

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
Ez lehetővé teszi, hogy az alkalmazás telepítése sikeresen megtörtént az Azure Stack az API-profil NuGet-csomagok segítségével.

## <a name="samples-using-api-profiles"></a>API-profilok használatával minták

A következő minták referenciaként használható megoldások létrehozásához a .NET-keretrendszer és az Azure Stack API profilokkal.
- [Erőforráscsoportok kezelése](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Storage-fiókok kezelése](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Virtuális gép kezelése](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm)

## <a name="next-steps"></a>További lépések

API-profilokkal kapcsolatos további információkért lásd:

- [Az Azure Stackben API-verzióprofilok kezelése](azure-stack-version-profiles.md)
- [Erőforrás-szolgáltató API-ja verziókat támogatja profilok](azure-stack-profiles-azure-resource-manager-versions.md)

  [Első lépések – a Git telepítése]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Keresés és a egy csomag telepítése]: /nuget/tools/package-manager-ui
  [NuGet-Csomagkezelő utasítások]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Ajánlatok, előfizetések létrehozása az Azure Stackben]: ../operator/azure-stack-subscribe-plan-provision-vm.md
  [Alkalmazások elérése az Azure Stackhez]: ../operator/azure-stack-create-service-principals.md
  [* Bérlői azonosító *]: ../operator/azure-stack-identity-overview.md
  [* előfizetési azonosító *]: ../operator/azure-stack-plan-offer-quota-overview.md#subscriptions
  [* az Azure Stack Resource-Manager endpoint *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [API-profilok összefoglalása]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../operator/azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
