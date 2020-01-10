---
title: API-verziók profiljainak használata a GO in Azure Stack hub használatával | Microsoft Docs
description: Ismerje meg, hogyan használható az API-verziók profiljai a GO in Azure Stack hub használatával.
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
ms.openlocfilehash: 0ae8d2e9b91a06cdd88541f2b6922c66847e8593
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75815612"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack-hub"></a>Az API-verziók profiljainak használata a go in Azure Stack hub használatával

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek és Azure Stack Development Kit*

## <a name="go-and-version-profiles"></a>A Go és a Version profilok

A profil különböző típusú erőforrástípusok kombinációja, különböző szolgáltatásokból származó különböző verziókkal. A profilok használata segít a különböző erőforrástípusok összekeverésében és egyeztetésében. A profilok a következő előnyöket nyújtják:

- Az alkalmazás stabilitása egy adott API-verzióra való zárolással.
- Az alkalmazás kompatibilitása Azure Stack hub és regionális Azure-adatközpontokkal.

A go SDK-ban a profilok a profilok elérési útja alatt érhetők el. A profilok verziószáma az **éééé-hh-nn** formátumban van megjelölve. A legújabb Azure Stack hub API-profil verziója **2019-03-01** Azure Stack hub 1904-es vagy újabb verziójában. Egy adott szolgáltatás profilból történő importálásához importálja a megfelelő modult a profilból. Ha például a **2019-03-01** -profilból kívánja importálni a **számítási** szolgáltatást, használja a következő kódot:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/compute/mgmt/compute"
```

## <a name="install-the-azure-sdk-for-go"></a>A Góhoz készült Azure SDK telepítése

1. Telepítse a git-t. Útmutatásért lásd: [első lépések – a git telepítése](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
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

## <a name="how-to-use-go-sdk-profiles-on-azure-stack-hub"></a>A go SDK-profilok használata az Azure Stack hub-on

Ha Azure Stack hub-beli go Code-mintát szeretne futtatni, kövesse az alábbi lépéseket:

1. Telepítse a Go nyelvhez készült Azure SDK és annak függőségeit. Útmutatásért tekintse meg az előző szakaszt, majd [telepítse a go nyelvhez készült Azure SDK](#install-the-azure-sdk-for-go).
2. A metaadatok információinak beolvasása a Resource Manager-végpontból. A végpont egy JSON-fájlt ad vissza, amely a go-kód futtatásához szükséges információval rendelkezik.

   > [!NOTE]  
   > A Azure Stack Development Kit **ResourceManagerUrl** (ASDK) a következő: `https://management.local.azurestack.external/`  
   > Az integrált rendszerek **ResourceManagerUrl** : `https://management.<region>.<fqdn>/`  
   > A szükséges metaadatok beolvasása: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
   Példa JSON-fájlra:

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

3. Ha nem érhető el, hozzon létre egy előfizetést, és mentse az előfizetés-azonosítót későbbi használatra. Az előfizetések létrehozásával kapcsolatos információkért lásd: [előfizetések létrehozása Azure stack hub-beli ajánlatokhoz](../operator/azure-stack-subscribe-plan-provision-vm.md).

4. Hozzon létre egy olyan egyszerű szolgáltatásnevet, amely az **előfizetés** hatókörével és a **tulajdonosi** szerepkörrel rendelkező ügyfél-titkot használ. Mentse az egyszerű szolgáltatás AZONOSÍTÓját és a titkos kulcsot. Az Azure Stack hub egyszerű szolgáltatásának létrehozásával kapcsolatos információkért lásd: [alkalmazás-identitás használata az erőforrásokhoz való hozzáféréshez](../operator/azure-stack-create-service-principals.md). A Azure Stack hub-környezet már be van állítva.

5. Importáljon egy Service-modult a kód go SDK-profiljából. Azure Stack hub-profil jelenlegi verziója **2019-03-01**. Ha például egy hálózati modult szeretne importálni a **2019-03-01** -es profil típusból, használja a következő kódot:

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

   `<baseURI>` beállítása a 2. lépésben használt **ResourceManagerUrl** értékre. Állítsa `<subscriptionID>` értéket a 3. lépésből mentett **SubscriptionID** értékre.

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

Ha a go SDK-profillal kívánja létrehozni a virtuális hálózatot Azure Stack hubhoz, tekintse meg a [példát](#example).

## <a name="authentication"></a>Hitelesítés

Ha a go SDK-val szeretné beolvasni a Azure Active Directory **engedélyező** tulajdonságát, telepítse a **Go-autorest-** modulokat. Ezeket a modulokat már telepítve kell lennie a "Go SDK" telepítésének. Ha nem, telepítse a [hitelesítési csomagot a githubról](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Az engedélyezést be kell állítani az erőforrás-ügyfél engedélyezéseként. Az ügyfél hitelesítő adataival különböző módokon szerezhet be Azure Stack hub-beli engedélyező jogkivonatokat:

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

   Állítsa a `<activeDirectoryEndpoint>` értéket a dokumentum előző szakaszában lekért `ResourceManagerUrl` metaadatok `loginEndpoint` tulajdonságának értékére. Állítsa a `<tenantID>` értéket a Azure Stack hub-bérlői AZONOSÍTÓra.

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

    A jelen cikk előző szakaszában lekért **ResourceManagerUrl** metaadatok közül a "célközönség" listán szereplő értékek egyikére `<activeDirectoryResourceID>` beállítani.
    `<clientID>` beállítása az egyszerű szolgáltatásnév alkalmazás-AZONOSÍTÓJÁRA, amelyet a jelen cikk előző szakaszában hoztak létre.
    `<clientSecret>` beállítása az egyszerű szolgáltatásnév alkalmazás titkos kódjára, amelyet a jelen cikk előző szakaszában hoztak létre.

## <a name="example"></a>Példa

Ez a példa egy olyan go-kódot mutat be, amely egy virtuális hálózatot hoz létre Azure Stack hub-on. A go SDK-val kapcsolatos teljes példákért tekintse meg az [Azure go SDK Samples adattárát](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Azure Stack hub-minták a tárház szolgáltatási mappáiban található hibrid útvonalon érhetők el.

> [!NOTE]  
> Az ebben a példában szereplő kód futtatásához ellenőrizze, hogy a használt előfizetésben **regisztrálva**van-e a **hálózati** erőforrás-szolgáltató. Az ellenőrzéshez keresse meg az előfizetést az Azure Stack hub portálon, és válassza az **erőforrás-szolgáltatók lehetőséget.**

1. Importálja a szükséges csomagokat a kódban. A hálózati modul importálásához használja a Azure Stack hub legújabb elérhető profilját:

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

A go SDK-val a Azure Stack hub számára elérhető mintakód-minták a következők:

- [Virtuális gép létrehozása](https://github.com/Azure-Samples/Hybrid-Compute-Go-Create-VM)
- [Tárolási Adatsík](https://github.com/Azure-Samples/Hybrid-Storage-Go-Dataplane)
- [Managed Disks használata](https://github.com/Azure-Samples/Hybrid-Compute-Go-ManagedDisks) (a 2019-03-01 profilt használó minta, amely az Azure stack hub által támogatott legújabb API-verziókat célozza)

## <a name="next-steps"></a>Következő lépések

- [A PowerShell telepítése Azure Stack hubhoz](../operator/azure-stack-powershell-install.md)
- [A Azure Stack hub felhasználói PowerShell-környezetének konfigurálása](azure-stack-powershell-configure-user.md)
