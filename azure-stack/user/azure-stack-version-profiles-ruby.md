---
title: Az API-verziók profiljainak használata a Ruby használatával Azure Stack | Microsoft Docs
description: Ismerje meg, hogyan használhatók az API-verziók profiljai a Ruby használatával Azure Stackban.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: c0faaa7be69ad8d23dc94eec1107362a7a7eadfa
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959342"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Az API-verziók profiljainak használata a Ruby használatával Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

## <a name="ruby-and-api-version-profiles"></a>Ruby és API-verziók profiljai

A Azure Stack Resource Managerhez készült Ruby SDK eszközöket biztosít az infrastruktúra kiépítéséhez és kezeléséhez. Az SDK erőforrás-szolgáltatói közé tartozik a kiszámítások, a virtuális hálózatok és a Ruby nyelvű tárterület. A Ruby SDK API-profiljai lehetővé teszik a hibrid felhőalapú fejlesztést azáltal, hogy a Azure Stack globális Azure-erőforrásai és-erőforrásai közötti váltást segítik.

Az API-profilok erőforrás-szolgáltatók és szolgáltatási verziók kombinációja. Az API-profilokkal kombinálhatja a különböző erőforrástípusok használatát.

- Az összes szolgáltatás legújabb verziójának használatához használja az Azure SDK összegző gem **legújabb** profilját.
- Ha a Azure Stack kompatibilis szolgáltatásokat szeretné használni, használja az Azure SDK összegző gyöngyszemének **V2019_03_01_Hybrid** vagy **V2018_03_01** -profilját.
- A szolgáltatás legújabb **API-verziójának** használatához használja az adott gem **legújabb** profilját. Ha például csak a számítási szolgáltatás legújabb **API-verzióját** szeretné használni, használja a **számítási** gem **legújabb** profilját.
- Ha egy szolgáltatáshoz adott **API-verziót** kíván használni, használja a gem-ban meghatározott API-verziókat.

> [!NOTE]
> Az alkalmazás összes beállítását egyesítheti.

## <a name="install-the-azure-ruby-sdk"></a>Az Azure Ruby SDK telepítése

- A [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)telepítéséhez kövesse a hivatalos utasításokat.
- A [Ruby](https://www.ruby-lang.org/en/documentation/installation/)telepítéséhez kövesse a hivatalos utasításokat.
  - A telepítése közben válassza a **Ruby to PATH változó hozzáadása**lehetőséget.
  - Ha a rendszer kéri, telepítse a fejlesztői csomagot a Ruby telepítésekor.
  - Ezután telepítse a köteget a következő parancs használatával:  
    `Gem install bundler`
- Ha nem érhető el, hozzon létre egy előfizetést és az előfizetés-azonosító későbbi felhasználás céljából. Az előfizetés létrehozásával kapcsolatos utasításokat [itt](../operator/azure-stack-subscribe-plan-provision-vm.md)találja.
- Hozzon létre egy egyszerű szolgáltatásnevet, és mentse az azonosítót és a titkos kulcsot. [Itt](../operator/azure-stack-create-service-principals.md)talál útmutatást az egyszerű szolgáltatásnév létrehozásához a Azure stack.
- Győződjön meg arról, hogy a szolgáltatásnév rendelkezik az előfizetéshez hozzárendelt közreműködő/tulajdonos szerepkörrel. A szerepkör az egyszerű szolgáltatáshoz való hozzárendelésével kapcsolatban [itt talál](../operator/azure-stack-create-service-principals.md)útmutatást.

## <a name="install-the-rubygem-packages"></a>A RubyGem-csomagok telepítése

Az Azure RubyGem-csomagokat közvetlenül is telepítheti.

```Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

A Azure Resource Manager Ruby SDK előzetes verzióban érhető el, és valószínűleg a közelgő kiadásokban az interfészek változásait fogja megszakítani. Az alverzióban megnövekedett szám a megszakított változásokat jelezheti.

## <a name="use-the-azure_sdk-gem"></a>A azure_sdk gem használata

A **azure_sdk** gem a Ruby SDK által támogatott drágakövek összesítése. Ez a gem egy **legújabb** profilt tartalmaz, amely az összes szolgáltatás legújabb verzióját támogatja. Tartalmazza a Azure Stack-hoz készült, a **V2017_03_09** és a **V2019_03_01_Hybrid**verziószámú profilokat is.

A azure_sdk összegző gyöngyszem a következő paranccsal telepíthető:  

```Ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>Előfeltételek

Ha a Ruby Azure SDK-t Azure Stack használatával szeretné használni, a következő értékeket kell megadnia, majd az értékeket környezeti változókkal kell beállítania. A környezeti változók megadásához tekintse meg az adott operációs rendszer táblázatát követő utasításokat.

| Value | Környezeti változók | Leírás |
| --- | --- | --- |
| Bérlőazonosító | `AZURE_TENANT_ID` | A Azure Stack [bérlő azonosítójának](../operator/azure-stack-identity-overview.md)értéke. |
| Ügyfél-azonosító | `AZURE_CLIENT_ID` | Az egyszerű szolgáltatásnév alkalmazásának azonosítója, amely akkor lett mentve, amikor a jelen dokumentum előző szakaszában a szolgáltatásnév lett létrehozva.  |
| Előfizetés azonosítója | `AZURE_SUBSCRIPTION_ID` | Az [előfizetés-azonosító](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) az Azure stack-ban elérhető ajánlatok elérési módja. |
| Titkos ügyfélkulcs | `AZURE_CLIENT_SECRET` | A szolgáltatás egyszerű alkalmazásának titka mentve, amikor a szolgáltatásnév létrejött. |
| Resource Manager-végpont | `ARM_ENDPOINT` | Lásd [a Azure stack Resource Manager](#the-azure-stack-resource-manager-endpoint)-végpontot.  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>A Azure Stack Resource Manager-végpont

A Microsoft Azure Resource Manager egy felügyeleti keretrendszer, amely lehetővé teszi a rendszergazdáknak az Azure-erőforrások üzembe helyezését, kezelését és figyelését. Az Azure Resource Manager képes kezelni ezeket a feladatokat, csoportként, nem pedig külön-külön, egyetlen művelettel.

A metaadat-információkat a Resource Manager-végpontból kérheti le. A végpont egy JSON-fájlt ad vissza, amely a kód futtatásához szükséges adatokkal rendelkezik.

 > [!NOTE]  
 > A Azure Stack Development Kit **ResourceManagerUrl** (ASDK) a következő: `https://management.local.azurestack.external/`Az integrált rendszerek **ResourceManagerUrl** :`https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
 > A szükséges metaadatok beolvasása:`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
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

### <a name="set-environmental-variables"></a>Környezeti változók beállítása

**Microsoft Windows**  
A környezeti változók beállításához használja a következő formátumot egy Windows-parancssorban:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS-, Linux-és UNIX-alapú rendszerek** <br>
UNIX-alapú rendszerek esetén használja a következő parancsot:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Meglévő API-profilok

A Azure_sdk-összesítési gem a következő három profillal rendelkezik:

1. **V2019_03_01_Hybrid** <br>
  A profil a Azure Stackhez készült. Használja ezt a profilt a Azure Stack Stamp 1904-es vagy újabb verziójában elérhető összes szolgáltatás legújabb verziójának kihasználása érdekében.
1. **V2017_03_09**  
  A profil a Azure Stackhez készült. Ezt a profilt olyan szolgáltatásokhoz használhatja, amelyek a Azure Stack Stamp 1808-es vagy korábbi verziójával kompatibilisek.
1. **Legújabb**  
  A profil az összes szolgáltatás legújabb verzióit tartalmazza. Használja az összes szolgáltatás legújabb verzióit.

Azure Stack-és API-profilokkal kapcsolatos további információkért tekintse meg az [API-profilok](azure-stack-version-profiles.md#summary-of-api-profiles)összefoglalását ismertető témakört.

## <a name="azure-ruby-sdk-api-profile-usage"></a>Azure Ruby SDK API-profil használata

A profil-ügyfél létrehozásához használja a következő kódot. Ez a paraméter csak akkor szükséges, az Azure Stack vagy egyéb privát felhők. Globális Azure alapértelmezés szerint ezek a beállítások már rendelkezik.

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

# Target profile built for Azure Stack
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

## <a name="define-azure-stack-environment-setting-functions"></a>Azure Stack-környezet beállítás függvények definiálása

Az egyszerű szolgáltatásnév Azure Stack-környezetbe való hitelesítéséhez adja meg a végpontokat a `get_active_directory_settings()`használatával. Ez a metódus a környezeti változók létrehozásakor beállított **ARM_Endpoint** környezeti változót használja:

```Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
  settings = MsRestAzure::ActiveDirectoryServiceSettings.new
  response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
  status_code = response.code
  response_content = response.body
  unless status_code == "200"
    error_model = JSON.load(response_content)
    fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
  end
  result = JSON.load(response_content)
  settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
  settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
  settings
end
```

## <a name="samples-using-api-profiles"></a>API-profilokat használó minták

A GitHubon található következő mintákat használhatja a Ruby és Azure Stack API-profilokkal rendelkező megoldások létrehozásához:

- [Azure-erőforrások és-erőforráscsoportok kezelése a Ruby-](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)mel.
- [Virtuális gépek kezelése a Ruby használatával](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM) (A 2019-03-01-hibrid profilt használó minta a Azure Stack által támogatott legújabb API-verziókat célozza meg.
- [Egy SSH-t használó virtuális gép üzembe helyezése a rubyban található sablonnal](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment).

### <a name="sample-resource-manager-and-groups"></a>A Resource Manager és a csoportok mintája

A minta futtatásához győződjön meg arról, hogy a Ruby alkalmazást telepítette. Ha Visual Studio Code-ot használ, töltse le a Ruby SDK bővítményt is.

> [!NOTE]  
> Szerezze be a minta tárházát az "[Azure-erőforrások és-erőforráscsoportok kezelése a Ruby szolgáltatással](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)" című résznél.

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

   Egyszerű szolgáltatás létrehozásával kapcsolatos útmutatóért lásd: [tanúsítvánnyal egyszerű szolgáltatás létrehozása az Azure PowerShell használatával](../operator/azure-stack-create-service-principals.md).

   A szükséges értékek a következők:
   - Bérlőazonosító
   - Ügyfél-azonosító
   - Titkos ügyfélkulcs
   - Előfizetés azonosítója
   - Resource Manager-végpont

   Állítsa be az alábbi környezeti változókat az Ön által létrehozott egyszerű szolgáltatásból beolvasott információk alapján.

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Resource Manager URL}`

   > [!NOTE]  
   > Windows rendszeren használja az Exportálás helyett a készletet.

4. Győződjön meg arról, hogy a Location változó a Azure Stack helyére van beállítva; például `LOCAL="local"`:.

5. Adja hozzá a következő kódrészletet, ha Azure Stack vagy más privát felhők használatával célozza meg a megfelelő Active Directory-végpontokat:

   ```Ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. Az Options változóban adja hozzá a Active Directory beállításokat és az alap URL-címet, hogy működjön a Azure Stack:

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. Hozzon létre egy profil-ügyfelet, amely a Azure Stack profilt célozza meg:

   ```ruby  
   client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
   ```

8. Az egyszerű szolgáltatásnév Azure Stack-vel történő hitelesítéséhez a végpontokat a **get_active_directory_settings ()** használatával kell meghatározni. Ez a metódus a környezeti változók létrehozásakor beállított **ARM_Endpoint** környezeti változót használja:

   ```ruby  
   def get_active_directory_settings(armEndpoint)
     settings = MsRestAzure::ActiveDirectoryServiceSettings.new
     response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
     status_code = response.code
     response_content = response.body
     unless status_code == "200"
       error_model = JSON.load(response_content)
       fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
     end
     result = JSON.load(response_content)
     settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
     settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
     settings
   end
   ```

9. Futtassa a mintát.

   ```ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>További lépések

- [A PowerShell telepítése az Azure Stack szolgáltatáshoz](../operator/azure-stack-powershell-install.md)
- [A Azure Stack felhasználó PowerShell-környezetének konfigurálása](azure-stack-powershell-configure-user.md)  
