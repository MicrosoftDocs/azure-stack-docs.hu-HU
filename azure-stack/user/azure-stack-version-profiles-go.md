---
title: API-verziók profiljainak használata a GO in Azure Stack használatával | Microsoft Docs
description: Ismerje meg, hogyan használható az API-verziók profiljai a GO in Azure Stack használatával.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/26/2019
ms.openlocfilehash: 0636f3069db80613f02e979b5a102a471f12efad
ms.sourcegitcommit: 3d14ae30ce3ee44729e5419728cce14b3000e968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71814458"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Az API-verziók profiljainak használata a go in Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

## <a name="go-and-version-profiles"></a>A Go és a Version profilok

A profil különböző típusú erőforrástípusok kombinációja, különböző szolgáltatásokból származó különböző verziókkal. A profilok használata segít a különböző erőforrástípusok összekeverésében és egyeztetésében. A profilok a következő előnyöket nyújtják:

- Az alkalmazás stabilitása egy adott API-verzióra való zárolással.
- Az alkalmazás kompatibilitása Azure Stack és regionális Azure-adatközpontokkal.

A go SDK-ban a profilok a profilok elérési útja alatt érhetők el. A profilok verziószáma az **éééé-hh-nn** formátumban van megjelölve. A legújabb Azure Stack API-profil verziószáma **2019-03-01** Azure stack 1904-es vagy újabb verziójú. Egy adott szolgáltatás profilból történő importálásához importálja a megfelelő modult a profilból. Ha például a **2019-03-01** -profilból kívánja importálni a **számítási** szolgáltatást, használja a következő kódot:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/compute/mgmt/compute"
```

## <a name="install-the-azure-sdk-for-go"></a>A Góhoz készült Azure SDK telepítése

1. A Git telepítése. Útmutatásért lásd: [első lépések – Git telepítése](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Telepítse a [Go programozási nyelvet](https://golang.org/dl). Az Azure-hoz készült API-profilokhoz a 1,9-es vagy újabb verzió szükséges.
3. Telepítse az Azure go SDK-t és annak függőségeit a következő bash-parancs futtatásával:

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>A go SDK

Az Azure go SDK-val kapcsolatos további információkért tekintse meg az alábbi hivatkozásokat:

- Az Azure go SDK a [Go nyelvhez készült Azure SDK telepítéséhez](/go/azure/azure-sdk-go-install).
- Az Azure go SDK nyilvánosan elérhető a GitHubon az [Azure-SDK-for-go](https://github.com/Azure/azure-sdk-for-go) adattárban.

### <a name="go-autorest-dependencies"></a>Go-autorest-függőségek

A go SDK az Azure **Go-autorest-** moduloktól függ, hogy Azure Resource Manager-végpontoknak küldje el a REST-kérelmeket. Importálnia kell az Azure go **-autorest** modul függőségeit az [Azure go-autorest](https://github.com/Azure/go-autorest)szolgáltatásból a githubon. A bash-parancsokat a **telepítés** szakaszban találja.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>A go SDK-profilok használata a Azure Stackon

Ha Azure Stackon szeretné futtatni a go Code mintát, kövesse az alábbi lépéseket:

1. Telepítse a Go nyelvhez készült Azure SDK és annak függőségeit. Útmutatásért tekintse meg az előző szakaszt, majd [telepítse a go nyelvhez készült Azure SDK](#install-the-azure-sdk-for-go).
2. A metaadatok információinak beolvasása a Resource Manager-végpontból. A végpont egy JSON-fájlt ad vissza, amely a go-kód futtatásához szükséges információval rendelkezik.

   > [!NOTE]  
   > A **ResourceManagerUrl** van az Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/`  
   > Az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/`  
   > A szükséges metaadatok beolvasása: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
   JSON-mintafájlt:

   ```json
   { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
     "graphEndpoint": "https://graph.windows.net/",  
     "portal Endpoint": "https://portal.local.azurestack.external/",
     "authentication": {
       "loginEndpoint": "https://login.windows.net/",
       "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
     }
   }
   ```

3. Ha nem érhető el, hozzon létre egy előfizetést és az előfizetés-azonosító későbbi felhasználás céljából. Az előfizetés létrehozásával kapcsolatos információkért lásd: [előfizetések létrehozása az ajánlatokhoz Azure stack](../operator/azure-stack-subscribe-plan-provision-vm.md).

4. Hozzon létre egy olyan egyszerű szolgáltatásnevet, amely az **előfizetés** hatókörével és a **tulajdonosi** szerepkörrel rendelkező ügyfél-titkot használ. Mentse az egyszerű szolgáltatás AZONOSÍTÓját és a titkos kulcsot. Az Azure Stack egyszerű szolgáltatásnév létrehozásával kapcsolatos információkért lásd: [alkalmazás-identitás használata az erőforrásokhoz való hozzáféréshez](../operator/azure-stack-create-service-principals.md). A Azure Stack-környezet beállítása már megtörtént.

5. Importáljon egy Service-modult a kód go SDK-profiljából. Azure Stack profil jelenlegi verziója **2019-03-01**. Ha például egy hálózati modult szeretne importálni a **2019-03-01** -es profil típusból, használja a következő kódot:

   ```go
   package main
    import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
   ```

6. A függvényben hozzon létre és hitelesítse az ügyfelet egy **új** ügyfél-függvény hívásával. Virtuális hálózati ügyfél létrehozásához használja a következő kódot:  

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"

   func main() {
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
   ```

   Állítsa a `<baseURI>` értéket a 2. lépésben használt **ResourceManagerUrl** értékre. Állítsa @no__t – 0 értéket a 3. lépésben mentett **SubscriptionID** értékre.

   A jogkivonat létrehozásához tekintse meg a következő szakaszt.  

7. Az API-metódusok meghívásához használja az előző lépésben létrehozott ügyfelet. Ha például egy virtuális hálózatot szeretne létrehozni az előző lépésben található ügyféllel, tekintse meg a következő példát:

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

A virtuális hálózatok a go SDK-profillal való létrehozásával kapcsolatos teljes példát a következő [példában](#example)talál: Azure stack.

## <a name="authentication"></a>Authentication

Ha a go SDK-val szeretné beolvasni a Azure Active Directory engedélyező tulajdonságát, telepítse a **Go-autorest-** modulokat. Ezeket a modulokat már telepítve kell lennie a "Go SDK" telepítésének. Ha nem, telepítse a [hitelesítési csomagot a githubról](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Az engedélyezést be kell állítani az erőforrás-ügyfél engedélyezéseként. Az ügyfél hitelesítő adataival többféleképpen lehet beolvasni a Azure Stack hitelesítő jogkivonatait:

1. Ha az előfizetéshez tartozó tulajdonosi szerepkörrel rendelkező egyszerű szolgáltatás elérhető, ugorja át ezt a lépést. Ellenkező esetben lásd: [alkalmazás-identitás használata az erőforrások eléréséhez](../operator/azure-stack-create-service-principals.md) az ügyfél titkos kulcsát használó egyszerű szolgáltatásnév létrehozásával kapcsolatos utasításokért, valamint az előfizetéséhez tartozó tulajdonosi szerepkör hozzárendelésével kapcsolatos segítségért. Ügyeljen arra, hogy rögzítse az egyszerű szolgáltatás alkalmazás-AZONOSÍTÓját és a titkos kulcsot.

2. Importálja a **adal** csomagot a **Go-autorest-** ből a kódban.

   ```go
   package main
   import "github.com/Azure/go-autorest/autorest/adal"
   ```

3. Hozzon létre egy **oauthConfig** a NewOAuthConfig metódus használatával a **adal** modulból.

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/ada1"

   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
   }
   ```

   Állítsa a @no__t – 0 értéket a dokumentum előző szakaszában lekért `ResourceManagerUrl` metaadatok `loginEndpoint` tulajdonságának értékére. Állítsa a `<tenantID>` értéket a Azure Stack bérlői AZONOSÍTÓra.

4. Végül hozzon létre egy egyszerű szolgáltatásnevet a **adal** modul `NewServicePrincipalToken` metódusának használatával:

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/adal"

   func CreateToken() (adal.OAuthTokenProvider, error) {
       var token adal.OAuthTokenProvider
       oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
       token, err = adal.NewServicePrincipalToken(
           *oauthConfig,
           clientID,
           clientSecret,
           activeDirectoryResourceID)
       return token, err
   ```

    Állítsa a `<activeDirectoryResourceID>` értéket a jelen cikk előző szakaszának "célközönség" listájának egyik értékére a **ResourceManagerUrl** metaadataiból.
    Állítsa be a `<clientID>` értéket az egyszerű szolgáltatásnév alkalmazás-AZONOSÍTÓjának megadására, amikor a szolgáltatás a jelen cikk előző szakaszában lett létrehozva.
    Állítsa a `<clientSecret>` értéket az egyszerű szolgáltatásnév alkalmazás titkos kódjára, amelyet a jelen cikk előző szakaszában hozott létre.

## <a name="example"></a>Példa

Ez a példa egy olyan go-kódot mutat be, amely egy virtuális hálózatot hoz létre Azure Stackon. A go SDK-val kapcsolatos teljes példákért tekintse meg az [Azure go SDK Samples adattárát](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Azure Stack minták a tárház szolgáltatási mappáiban található hibrid útvonalon érhetők el.

> [!NOTE]  
> Az ebben a példában szereplő kód futtatásához ellenőrizze, hogy a használt előfizetésben regisztrálva van-e a **hálózati** erőforrás-szolgáltató. Ha ellenőrizni szeretné, keresse meg az előfizetést a Azure Stack-portálon, és válassza az **erőforrás-szolgáltatók lehetőséget.**

1. Importálja a szükséges csomagokat a kódban. A hálózati modul importálásához használja a Azure Stack legújabb elérhető profilját:

   ```go
   package main

   import (
       "context"
       "fmt"
       "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
       "github.com/Azure/go-autorest/autorest"
       "github.com/Azure/go-autorest/autorest/adal"
       "github.com/Azure/go-autorest/autorest/to"
   )
   ```

2. Adja meg a környezeti változókat. Virtuális hálózat létrehozásához erőforráscsoport szükséges.

   ```go
   var (
       activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
       tenantID = "yourAzureStackTenantID"
       clientID = "yourServicePrincipalApplicationID"
       clientSecret = "yourServicePrincipalSecret"
       activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
       subscriptionID = "yourSubscriptionID"
       baseURI = "yourResourceManagerURL"
       resourceGroupName = "existingResourceGroupName"
   )
   ```

3. Most, hogy definiálta a környezeti változókat, adjon hozzá egy metódust a hitelesítési jogkivonat létrehozásához a **adal** -csomag használatával. A hitelesítéssel kapcsolatos további információkért tekintse meg az előző szakaszt.

   ```go
   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }
   ```

4. Adja hozzá a `main` metódust. A `main` metódus először egy jogkivonatot kap az előző lépésben megadott metódus használatával. Ezután létrehoz egy ügyfelet egy hálózati modul használatával a profilból. Végül létrehoz egy virtuális hálózatot.

   ```go
   package main

   import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
   )

   var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
     subscriptionID = "yourSubscriptionID"
     baseURI = "yourResourceManagerURL"
     resourceGroupName = "existingResourceGroupName"
   )

   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }

   func main() {
      token, _ := CreateToken()
      vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
      future, _ := vnetClient.CreateOrUpdate(
          context.Background(),
          resourceGroupName,
          "sampleVnetName",
          network.VirtualNetwork{
              Location: to.StringPtr("local"),
              VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                  AddressSpace: &network.AddressSpace{
                      AddressPrefixes: &[]string{"10.0.0.0/8"},
                  },
                  Subnets: &[]network.Subnet{
                      {
                          Name: to.StringPtr("subnetName"),
                          SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                              AddressPrefix: to.StringPtr("10.0.0.0/16"),
                          },
                      },
                  },
              },
          })
      err := future.WaitForCompletionRef(context.Background(), vnetClient.Client)
      if err != nil {
          fmt.Printf(err.Error())
          return
      }
   }
   ```

A go SDK-val Azure Stack elérhető mintakód-minták a következők:

- [Virtuális gép létrehozása](https://github.com/Azure-Samples/Hybrid-Compute-Go-Create-VM)
- [Tárolási Adatsík](https://github.com/Azure-Samples/Hybrid-Storage-Go-Dataplane)
- [Használja a Managed Disks](https://github.com/Azure-Samples/Hybrid-Compute-Go-ManagedDisks) (a 2019-03-01 profilt használó mintát, amely a Azure stack által támogatott legújabb API-verziókat célozza)

## <a name="next-steps"></a>További lépések

- [A PowerShell telepítése az Azure Stack szolgáltatáshoz](../operator/azure-stack-powershell-install.md)
- [A Azure Stack felhasználó PowerShell-környezetének konfigurálása](azure-stack-powershell-configure-user.md)
