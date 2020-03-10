---
title: Az API-verziók profiljainak használata a Ruby használatával Azure Stack hub-ban
description: Ismerje meg, hogyan használhatók az API-verziók profiljai a Ruby használatával Azure Stack hub-ban.
author: sethmanheim
ms.topic: article
ms.date: 01/23/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 5b79c676b922f0e76ed75e3ad043f53c1fb9d6a5
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364145"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack-hub"></a>Az API-verziók profiljainak használata a Ruby használatával Azure Stack hub-ban

## <a name="ruby-and-api-version-profiles"></a>Ruby és API-verziók profiljai

Az Azure Stack hub Resource Managerhez készült Ruby SDK olyan eszközöket biztosít, amelyek segítenek az infrastruktúra kiépítésében és kezelésében. Az SDK erőforrás-szolgáltatói közé tartozik a Ruby nyelve: számítási, virtuális hálózatok és tárterület. A Ruby SDK-ban található API-profilok lehetővé teszik a hibrid felhőalapú fejlesztést azáltal, hogy a Azure Stack hub globális Azure-erőforrásai és-erőforrásai közötti váltást segítik.

Az API-profilok erőforrás-szolgáltatók és szolgáltatási verziók kombinációja. Az API-profilokkal kombinálhatja a különböző erőforrástípusok használatát.

- Az összes szolgáltatás legújabb verziójának használatához használja az Azure SDK összegző gem **legújabb** profilját.
- Az Azure Stack hub szolgáltatással kompatibilis szolgáltatások használatához használja az Azure SDK összegző gyöngyszemének **V2019_03_01_Hybrid** vagy **V2018_03_01** profilját.
- A szolgáltatás legújabb **API-verziójának** használatához használja az adott gem **legújabb** profilját. Ha például csak a számítási szolgáltatás legújabb **API-verzióját** szeretné használni, használja a **számítási** gem **legújabb** profilját.
- Ha egy szolgáltatáshoz adott **API-verziót** kíván használni, használja a gem-ban meghatározott API-verziókat.

> [!NOTE]
> Az alkalmazás összes beállítását egyesítheti.

## <a name="install-the-azure-ruby-sdk"></a>Az Azure Ruby SDK telepítése

- A [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)telepítéséhez kövesse a hivatalos utasításokat.
- A [Ruby](https://www.ruby-lang.org/en/documentation/installation/)telepítéséhez kövesse a hivatalos utasításokat.
  - A telepítésekor válassza **a Ruby to PATH változó hozzáadása**lehetőséget.
  - Ha a rendszer a Ruby telepítésekor kéri, telepítse a fejlesztői csomagot.
  - Ezután telepítse a köteget a következő parancs használatával: 

       ```Ruby
       Gem install bundler
       ```

- Ha nem érhető el, hozzon létre egy előfizetést és az előfizetés-azonosító későbbi felhasználás céljából. Az előfizetés létrehozásához szükséges utasítások a [Azure stack hub-ban található előfizetések létrehozása](../operator/azure-stack-subscribe-plan-provision-vm.md) című cikkben találhatók.
- Hozzon létre egy egyszerű szolgáltatásnevet, és mentse az azonosítót és a titkos kulcsot. Az Azure Stack hub szolgáltatáshoz tartozó egyszerű szolgáltatásnév létrehozásához az [alkalmazás identitás használata az erőforrások eléréséhez](../operator/azure-stack-create-service-principals.md) című cikk nyújt útmutatást.
- Győződjön meg arról, hogy a szolgáltatásnév rendelkezik az előfizetéshez hozzárendelt közreműködő/tulajdonos szerepkörrel. A szerepkör egy egyszerű szolgáltatáshoz való hozzárendelésével kapcsolatos utasítások az alkalmazás- [identitás használata az erőforrásokhoz való hozzáféréshez](../operator/azure-stack-create-service-principals.md)című témakörben találhatók.

## <a name="install-the-rubygem-packages"></a>A RubyGem-csomagok telepítése

Az Azure RubyGem-csomagokat közvetlenül is telepítheti.

```Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
```

Vagy használhatja őket a Gemfile.

```Ruby
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

A Azure Resource Manager Ruby SDK előzetes verzióban érhető el, és valószínűleg a közelgő kiadásokban az interfészek változásait fogja megszakítani. Az alverzióban megnövekedett szám a megszakított változásokat jelezheti.

## <a name="use-the-azure_sdk-gem"></a>A azure_sdk gem használata

A **azure_sdk** gem a Ruby SDK által támogatott drágakövek összesítése. Ez a gyöngyszem egy **legújabb** -profilt tartalmaz, amely az összes szolgáltatás legújabb verzióját támogatja. Az Azure Stack hub-hoz készült, **V2017_03_09** és **V2019_03_01_Hybrid**verziószámú profilokat tartalmaz.

A következő paranccsal telepítheti a azure_sdk összesítő gem-t:  

```Ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>Előfeltételek

Ha a Ruby Azure SDK-t Azure Stack hubhoz szeretné használni, meg kell adnia a következő értékeket, majd az értékeket környezeti változók alapján kell beállítania. A környezeti változók megadásához tekintse meg az adott operációs rendszer táblázatát követő utasításokat.

| Érték | Környezeti változók | Leírás |
| --- | --- | --- |
| Bérlőazonosító | `AZURE_TENANT_ID` | Az Azure Stack hub- [bérlő azonosítója](../operator/azure-stack-identity-overview.md). |
| Ügyfél-azonosító | `AZURE_CLIENT_ID` | Az egyszerű szolgáltatásnév a jelen cikk előző szakaszában a szolgáltatásnév létrehozásakor mentve.  |
| Előfizetés azonosítója | `AZURE_SUBSCRIPTION_ID` | Az előfizetés- [azonosítóval](../operator/service-plan-offer-subscription-overview.md#subscriptions) érheti el az ajánlatokat az Azure stack hub szolgáltatásban. |
| Titkos ügyfélkulcs | `AZURE_CLIENT_SECRET` | A szolgáltatás egyszerű alkalmazásának titkos kulcsa a szolgáltatásnév létrehozásakor mentve. |
| Resource Manager-végpont | `ARM_ENDPOINT` | Tekintse meg [az Azure stack hub Resource Manager-végpontot](#the-azure-stack-hub-resource-manager-endpoint).  |

### <a name="the-azure-stack-hub-resource-manager-endpoint"></a>Az Azure Stack hub Resource Manager-végpont

A Microsoft Azure Resource Manager egy felügyeleti keretrendszer, amely lehetővé teszi a rendszergazdáknak az Azure-erőforrások üzembe helyezését, kezelését és figyelését. Az Azure Resource Manager képes kezelni ezeket a feladatokat, csoportként, nem pedig külön-külön, egyetlen művelettel.

A metaadat-információkat a Resource Manager-végpontból kérheti le. A végpont egy JSON-fájlt ad vissza, amely a kód futtatásához szükséges adatokkal rendelkezik.

 > [!NOTE]  
 > A Azure Stack Development Kit **ResourceManagerUrl** (ASDK) a következő: `https://management.local.azurestack.external/` a **ResourceManagerUrl** az integrált rendszerekben: `https://management.region.<fqdn>/`, ahol `<fqdn>` a teljes tartománynév.  
 > A szükséges metaadatok beolvasása: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
 JSON-mintafájlt:

 ```json
 {
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
   "graphEndpoint": "https://graph.windows.net/",  
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": {
     "loginEndpoint": "https://login.windows.net/",
     "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
   }
 }
```

### <a name="set-environment-variables"></a>Környezeti változók beállítása

#### <a name="microsoft-windows"></a>Microsoft Windows

A környezeti változók beállításához használja a következő formátumot egy Windows-parancssorban:

```shell
set AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

#### <a name="macos-linux-and-unix-based-systems"></a>macOS-, Linux-és UNIX-alapú rendszerek

UNIX-alapú rendszerekben használja a következő parancsot:

```bash
export AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

## <a name="existing-api-profiles"></a>Meglévő API-profilok

A **Azure_sdk** összesített gem a következő 3 profillal rendelkezik:

- **V2019_03_01_Hybrid**: az Azure stack hub-hoz készült profil. Ezt a profilt a Azure Stack hub 1904-es vagy újabb verziójában elérhető szolgáltatások legújabb verzióihoz használhatja.
- **V2017_03_09**: az Azure stack hub-hoz készült profil. Használja ezt a profilt, hogy a szolgáltatások a leghatékonyabban kompatibilisek legyenek Azure Stack hub 1808-es vagy korábbi verziójával.
- **Legújabb**: a profil az összes szolgáltatás legújabb verzióit tartalmazza. Használja az összes szolgáltatás legújabb verzióit.

Azure Stack hub-és API-profilokkal kapcsolatos további információkért tekintse meg az [API-profilok összefoglalását](azure-stack-version-profiles.md#summary-of-api-profiles)ismertető témakört.

## <a name="azure-ruby-sdk-api-profile-usage"></a>Azure Ruby SDK API-profil használata

A profil-ügyfél létrehozásához használja a következő kódot. Ez a paraméter csak Azure Stack hub vagy más privát felhők esetén szükséges. Globális Azure alapértelmezés szerint ezek a beállítások már rendelkezik.

```Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
  ENV['AZURE_TENANT_ID'],
  ENV['AZURE_CLIENT_ID'],
  ENV['AZURE_CLIENT_SECRET'],
  active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
  credentials: credentials,
  subscription_id: subscription_id,
  active_directory_settings: active_directory_settings,
  base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack Hub
client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
```

A profil ügyfelet az egyes erőforrás-szolgáltatók, például a számítási, tárolási és hálózati szolgáltatások eléréséhez használhatja:

```Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2019_03_01_Hybrid::Compute::Mgmt::Models::PurchasePlan.new
```

## <a name="define-azure-stack-hub-environment-setting-functions"></a>Azure Stack hub-környezet beállítási funkcióinak meghatározása

Az egyszerű szolgáltatásnév Azure Stack hub-környezetbe való hitelesítéséhez `get_active_directory_settings()`használatával határozza meg a végpontokat. Ez a metódus a korábban beállított **ARM_Endpoint** környezeti változót használja:

```Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
  settings = MsRestAzure::ActiveDirectoryServiceSettings.new
  response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
  status_code = response.code
  response_content = response.body
  unless status_code == "200"
    error_model = JSON.load(response_content)
    fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Hub Metadata Endpoints", response, error_model)
  end
  result = JSON.load(response_content)
  settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
  settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
  settings
end
```

## <a name="samples-using-api-profiles"></a>API-profilokat használó minták

A GitHubon a következő mintákat használhatja a Ruby és a Azure Stack hub API-profillal rendelkező megoldások létrehozásához:

- [Azure-erőforrások és-erőforráscsoportok kezelése a Ruby-](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)mel.
- [Virtuális gépek kezelése a Ruby használatával](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM) (a 2019-03-01-hibrid profilt használó minta, amely az Azure stack hub által támogatott legújabb API-verziókat célozza meg).
- [Egy SSH-t használó virtuális gép üzembe helyezése a rubyban található sablonnal](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment).

### <a name="sample-resource-manager-and-groups"></a>A Resource Manager és a csoportok mintája

A minta futtatásához győződjön meg arról, hogy a Ruby alkalmazást telepítette. Ha Visual Studio Code-ot használ, töltse le a Ruby SDK bővítményt is.

> [!NOTE]  
> A minta tárháza a [Hybrid-Resource-Manager-Ruby-Resources-and-groups](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups).

1. A tárház klónozása:

   ```bash
   git clone https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups.git
   ```

2. A függőségek telepítése a Bundle használatával:

   ```Bash
   cd Hybrid-Resource-Manager-Ruby-Resources-And-Groups
   bundle install
   ```

3. Hozzon létre egy Azure-beli szolgáltatásnevet a PowerShell használatával, és kérje le a szükséges értékeket.

   Az egyszerű szolgáltatásnév létrehozásával kapcsolatos utasításokért lásd: [Azure PowerShell használata egy egyszerű szolgáltatásnév létrehozásához tanúsítvánnyal](../operator/azure-stack-create-service-principals.md).

   A szükséges értékek a következők:

   - Bérlőazonosító
   - Ügyfél-azonosító
   - Titkos ügyfélkulcs
   - Előfizetés azonosítója
   - Resource Manager-végpont

   Állítsa be az alábbi környezeti változókat az Ön által létrehozott szolgáltatásnév alapján lekért információk alapján:

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Hub Resource Manager URL}`

   > [!NOTE]  
   > Windows rendszeren `export`helyett használja a `set`.

4. Győződjön meg arról, hogy a Location változó a Azure Stack hub helyére van beállítva; például `LOCAL="local"`.

5. A megfelelő Active Directory-végpontok célzásához adja hozzá a következő kódrészletet, ha Azure Stack hubot vagy más privát felhőket használ:

   ```Ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. A `options` változóban adja hozzá a Active Directory beállításokat és az alap URL-címet, hogy működjön az Azure Stack hub használatával:

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. Hozzon létre egy profil-ügyfelet, amely az Azure Stack hub-profilt célozza meg:

   ```ruby  
   client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
   ```

8. Az egyszerű szolgáltatásnév Azure Stack hubhoz való hitelesítéséhez a végpontokat **get_active_directory_settings ()** használatával kell meghatározni. Ez a metódus a korábban beállított **ARM_Endpoint** környezeti változót használja:

   ```ruby  
   def get_active_directory_settings(armEndpoint)
     settings = MsRestAzure::ActiveDirectoryServiceSettings.new
     response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
     status_code = response.code
     response_content = response.body
     unless status_code == "200"
       error_model = JSON.load(response_content)
       fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Hub Metadata Endpoints", response, error_model)
     end
     result = JSON.load(response_content)
     settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
     settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
     settings
   end
   ```

9. Futtassa a mintát.

   ```Ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>Következő lépések

- [A PowerShell telepítése Azure Stack hubhoz](../operator/azure-stack-powershell-install.md)
- [A Azure Stack hub felhasználói PowerShell-környezetének konfigurálása](azure-stack-powershell-configure-user.md)  
