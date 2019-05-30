---
title: API-verzióprofilok használatával a Ruby használatával az Azure Stackben |} A Microsoft Docs
description: Ismerje meg az API-verzióprofilok használata az Azure Stackben Ruby használatával.
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
ms.openlocfilehash: 625ddb8341b58c98e47b37701547de5d6acea45b
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269327"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>API-verzióprofilok használata az Azure Stackben Ruby használatával

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

## <a name="ruby-and-api-version-profiles"></a>Ruby- és API-verzióprofilok

A Ruby SDK az Azure Stack Resource Manager biztosít eszközöket és az infrastruktúra kezelését. Az SDK-t az erőforrás-szolgáltató például számítási, a virtuális hálózatok és a tárolási Ruby nyelven. A Ruby SDK API-profilok engedélyezése a hibrid felhőalapú fejlesztés segít a globális Azure-erőforrások és az Azure Stacken erőforrások közötti váltáshoz.

Egy API-profilt az erőforrás-szolgáltatókat és szolgáltatásverziók kombinációját. Használhatja egy API-profilt egyesítheti a különböző erőforrástípusok.

- Az összes szolgáltatást a legújabb verziókat használhatja a **legújabb** az Azure SDK összesítő gem profiljában.
- Az Azure Stack kompatibilis szolgáltatások használatához a **V2019_03_01_Hybrid** vagy **V2018_03_01** az Azure SDK összesítő gem profiljában.
- A legújabb **api-version** egy szolgáltatást, használja a **legújabb** az adott gem profiljában. Például, ha szeretné, hogy a legújabb **api-version** számítási szolgáltatás önálló, használja a **legújabb** profiljának a **számítási** gem.
- Egy adott használandó **api-version** egy szolgáltatást, használja az adott API-verziók a gem definiált.

> [!NOTE]
> Kombinálhatja a lehetőségek a ugyanazt az alkalmazást.

## <a name="install-the-azure-ruby-sdk"></a>Azure Ruby SDK telepítése

- Hivatalos utasításokat követve telepítse [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- Hivatalos utasításokat követve telepítse [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
  - Válassza ki a telepítés során **adja hozzá a Ruby használatával PATH változóhoz**.
  - A csomag telepítése során Ruby telepítésének, amikor a rendszer kéri.
  - Ezután telepítse a bundler, a következő paranccsal:  
    `Gem install bundler`
- Ha nem érhető el, hozzon létre egy előfizetést és az előfizetés-azonosító későbbi felhasználás céljából. Utasítások alapján hozhat létre olyan előfizetést [Itt](../operator/azure-stack-subscribe-plan-provision-vm.md).
- Hozzon létre egy egyszerű szolgáltatást, és mentse a azonosítója és kulcsa. Az Azure stack-beli szolgáltatásnév létrehozása az útmutatás [Itt](../operator/azure-stack-create-service-principals.md).
- Ellenőrizze, hogy az egyszerű szolgáltatás rendelkezik közreműködői vagy tulajdonosi szerepkört az előfizetésben. Utasítások a szolgáltatásnév szerepkör hozzárendelése [Itt](../operator/azure-stack-create-service-principals.md).

## <a name="install-the-rubygem-packages"></a>A Rubygem csomagok telepítése

Az Azure Rubygem csomag közvetlenül is telepítheti.

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

Az Azure Resource Manager Ruby SDK előzetes verzióként érhető el, és valószínűleg a jövőbeni verziókból használhatatlanná tévő felület módosításokat fog rendelkezni. A kisebb verzió számának kompatibilitástörő változásokat utalhat.

## <a name="use-the-azuresdk-gem"></a>A azure_sdk gem használata

A gem **azure_sdk**, a Ruby SDK az összes támogatott gems összesített van. A gem áll egy **legújabb** profilt, amely támogatja az összes szolgáltatást a legújabb verzióra. Rendszerverzióval ellátott profilok tartalmaz **V2017_03_09** és **V2019_03_01_Hybrid**, amely az Azure Stack beépített.

A következő paranccsal telepíthető a azure_sdk összesítő gem:  

```Ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>Előfeltételek

Azure Ruby SDK használatához az Azure Stack használatával, adja meg a következő értékeket, és adja meg a következő értékeket a környezeti változókat. Az utasításokat lásd a táblázat az operációs rendszerének a a környezeti változók beállítása után.

| Érték | Környezeti változók | Leírás |
| --- | --- | --- |
| Bérlőazonosító | AZURE_TENANT_ID | Az Azure Stack értékét [bérlőazonosító](../operator/azure-stack-identity-overview.md). |
| Ügyfélazonosító | AZURE_CLIENT_ID | A szolgáltatás egyszerű alkalmazást azonosító mentése az előző szakaszban Ez a dokumentum az egyszerű szolgáltatás létrehozásakor.  |
| Előfizetés azonosítója | AZURE_SUBSCRIPTION_ID | A [előfizetés-azonosító](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) van, hogy miként férhetnek hozzá az ajánlatok az Azure Stackben. |
| Titkos ügyfélkulcs | AZURE_CLIENT_SECRET | A szolgáltatás egyszerű alkalmazás titkos kulcs mentése egyszerű szolgáltatás létrehozásakor. |
| Resource Manager-végpont | ARM_ENDPOINT | Lásd: [az Azure Stack resource manager-végpont](#the-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Az Azure Stack resource manager-végpont

A Microsoft Azure Resource Manager-keretrendszert, amely lehetővé teszi a rendszergazdák üzembe helyezése, kezelése és monitorozása az Azure-erőforrások számára. Az Azure Resource Manager képes kezelni ezeket a feladatokat, csoportként, nem pedig külön-külön, egyetlen művelettel.

A metaadat-információkat is kérhet a Resource Manager-végpontot. A végpont egy JSON-fájlt a kód futtatásához szükséges adatokat adja vissza.

 > [!NOTE]  
 > A **ResourceManagerUrl** van az Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/` A **ResourceManagerUrl** integrált rendszerek van: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
 > A szükséges metaadatokat lekéréséhez: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
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
A környezeti változók beállítása egy Windows parancssorban, használja a következő formátumot:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS, Linux és Unix-alapú rendszerek** a Unix-alapú rendszerek, a következő parancsot használja:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Meglévő API-profilok

A Azure_sdk összesítő gem a következő három profil van:

1. **V2019_03_01_Hybrid** az Azure Stackhez készült profil. Használja ezt a profilt használó szolgáltatások az Azure Stackben elérhető legfrissebb verzióit.
2. **V2017_03_09**  
  Az Azure Stackhez készült profil. Ez a profil szolgáltatások használatával lehet a leginkább kompatibilis az Azure Stack.
3. **legfrissebb**  
  Profil áll az összes szolgáltatás legújabb verzióit. Az összes szolgáltatást a legújabb verziókat használhatja.

Az Azure Stacket és API-profilokkal kapcsolatos további információkért lásd: a [összegzése az API-profilok](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Azure Ruby SDK API profile usage

Az alábbi kód használatával hozza létre a profil ügyfél. Ez a paraméter csak akkor szükséges, az Azure Stack vagy egyéb privát felhők. Globális Azure alapértelmezés szerint ezek a beállítások már rendelkezik.

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

A profil ügyfél segítségével egyéni erőforrás-szolgáltatók, például számítási, tárolási és hálózati hozzáférés:

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

Az Azure Stack-környezet, az egyszerű szolgáltatásnév hitelesítése, definiálni kell a végpontok segítségével `get_active_directory_settings()`. Ezt a módszert használja a **ARM_Endpoint** a környezeti változók létrehozásakor beállított környezeti változót:

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

## <a name="samples-using-api-profiles"></a>API-profilok használatával minták

A következő minták a Githubon található, mint a Ruby és az Azure Stack API profilok megoldások létrehozására szolgáló hivatkozások használhatja:

- [Azure-erőforrások és -erőforráscsoportok kezelése Ruby használatával](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)
- [Virtuális gépek, a Ruby használata kezelheti](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM) (az Azure Stack által támogatott legújabb API-verziók a 2019-03-01-hibrid profilt használó minta).
- [Üzembe helyezése ssh-t használó virtuális gép, a Ruby-sablonnal](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment)

### <a name="sample-resource-manager-and-groups"></a>Mintául szolgáló Resource Manager és a csoportok

A minta futtatásához, győződjön meg arról, hogy telepítette a Rubyt. Ha a Visual Studio Code-ot használja, töltse le, valamint a Ruby SDK bővítmény.

> [!NOTE]  
> Akkor kaphat a tárházban, a minta "[Azure-erőforrások és -erőforráscsoportok Ruby használatával](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)".

1. A tárház klónozása:

   ```bash
   git clone https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups.git
   ```

2. Telepítse a csomagot a függőségeket:

   ```Bash
   cd Hybrid-Resource-Manager-Ruby-Resources-And-Groups
   bundle install
   ```

3. Hozzon létre egy Azure-szolgáltatás egyszerű PowerShell-lel, és kérje le a szükséges értékeket.

   Egyszerű szolgáltatás létrehozásával kapcsolatos útmutatóért lásd: [tanúsítvánnyal egyszerű szolgáltatás létrehozása az Azure PowerShell használatával](../operator/azure-stack-create-service-principals.md).

   Szükséges értékek a következők:
   - Bérlőazonosító
   - Ügyfél-azonosító
   - Titkos ügyfélkulcs
   - Előfizetés azonosítója
   - Resource Manager-végpont

   Állítsa be az alábbi környezeti változókat a Szolgáltatásnévből létrehozott lekért információk segítségével.

   - Exportálás AZURE_TENANT_ID = {a bérlő azonosítóját}
   - Exportálás AZURE_CLIENT_ID = {az ügyfél-azonosító}
   - Exportálás AZURE_CLIENT_SECRET = {az Ügyfélkód}
   - Exportálás AZURE_SUBSCRIPTION_ID = {az előfizetés azonosítója}
   - Exportálás ARM_ENDPOINT = {az AzureStack Resource manager URL-cím}

   > [!NOTE]  
   > A Windows használja a set exportálása helyett.

4. Győződjön meg arról, a hely változó értéke az Azure Stack location; Ha például `LOCAL="local"`.

5. Adja hozzá a következő kódsort, ha használja az Azure Stack vagy egyéb privát felhők, amelyekre a megfelelő active directory-végpontokhoz:

   ```Ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. A beállítások változóban adja hozzá az Active Directory-beállítások és az Azure Stack használatának a kiindulási URL-cím:

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. Hozzon létre egy profilt ügyfél, amely az Azure Stack-profil célozza:

   ```ruby  
   client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
   ```

8. Az egyszerű szolgáltatás az Azure Stack használatával hitelesíteni, a végpontok használatával kell meghatározni **get_active_directory_settings()** . Ezt a módszert használja a **ARM_Endpoint** a környezeti változók létrehozásakor beállított környezeti változót:

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
- [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md)  
