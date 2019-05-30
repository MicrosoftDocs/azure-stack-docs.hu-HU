---
title: API-verzióprofilok használatával nyissa meg az Azure Stackben |} A Microsoft Docs
description: Ismerje meg az API-verzióprofilok használatával nyissa meg az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/26/2019
ms.openlocfilehash: 4a7e36fda318c1987a39427c5ef1f5e5e307d1b6
ms.sourcegitcommit: d04a93e913ff069e17f6d56811681804a6422b58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66373014"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>API-verzióprofilok használata nyissa meg az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

## <a name="go-and-version-profiles"></a>A go és a verzió profilok

A profil a különböző szolgáltatásokhoz különböző verziójú különböző erőforrástípusok kombinációját. A profil használatával segít keverheti a különböző erőforrástípusok között. Profilok a következő előnyöket biztosítja:

- Az adott API-verziók zárolja az alkalmazás stabilitását.
- Az alkalmazás az Azure Stack és a regionális Azure-adatközpontok való kompatibilitást.

A Go SDK profilok érhetők el a profilok elérési úton, a verziójuk a **éééé-hh-nn** formátumban. Most, a legújabb Azure Stack API profil verzió **2019-03-01**. Az adott szolgáltatás importálására egy profilt, a megfelelő modul importálása a profil. Például az importálandó **számítási** a szolgáltatás **2019-03-01** profil, a következő kóddal:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/compute/mgmt/compute"
```

## <a name="install-azure-sdk-for-go"></a>A Góhoz készült Azure SDK telepítése

1. A Git telepítése. Útmutatásért lásd: [első lépések – Git telepítése](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Telepítse a [Go programozási nyelvhez](https://golang.org/dl). Az Azure API-profilok a Go 1.9 vagy újabb verziója szükséges.
3. Telepítse a Go Azure SDK és annak függőségeit az alábbi bash-parancs futtatásával:

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>The Go SDK

Az alábbi hivatkozásokon további információ az Azure a GO SDK találja meg:

- Az Azure SDK a Go [Góhoz készült Azure SDK telepítése](/go/azure/azure-sdk-go-install).
- Az Azure a Go SDK nyilvánosan elérhető a githubon a [azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) adattárat.

### <a name="go-autorest-dependencies"></a>Go-AutoRest függőségek

Az Azure függ a Go SDK **Go-AutoRest** modulok REST-kérelmeket küldhet az Azure Resource Manager-végpontok. Importálnia kell az Azure **Go-AutoRest** modul függőségeknek [Azure Go-AutoRest a Githubon](https://github.com/Azure/go-autorest). A telepítés a bash-parancsok is megtalálhatja a **telepítése** szakaszban.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Go SDK profilok használata az Azure Stackben

Go-kódokat a minta futtatásához az Azure Stacken, kövesse az alábbi lépéseket:

1. Telepítse az Azure SDK a Go és annak függőségeit. Útmutatásért lásd az előző szakaszban [Góhoz készült Azure SDK telepítése](#install-azure-sdk-for-go).
2. A metaadatok információinak lekérése a Resource Manager-végpontot. A végpont egy JSON-fájlt a Go kód futtatásához szükséges adatokat adja vissza.

   > [!NOTE]  
   > A **ResourceManagerUrl** van az Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/`  
   > A **ResourceManagerUrl** integrált rendszerek van: `https://management.<region>.<fqdn>/`  
   > A szükséges metaadatokat lekéréséhez: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
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

3. Ha nem érhető el, hozzon létre egy előfizetést és az előfizetés-azonosító későbbi felhasználás céljából. Előfizetés létrehozásával kapcsolatos információkért lásd: [ajánlatok, előfizetések létrehozása az Azure Stackben](../operator/azure-stack-subscribe-plan-provision-vm.md).

4. Az egyszerű szolgáltatás létrehozása **előfizetés** hatókör és **tulajdonosa** szerepkör. Mentse a szolgáltatásnév-Azonosítót és a titkos kulcsot. További információ az Azure stack-beli szolgáltatásnév létrehozása: [egyszerű szolgáltatás létrehozása](azure-stack-create-service-principals.md). Most már az Azure Stack-környezet van beállítva.

5. A modul importálása a Go SDK-profillal a kódban. Az Azure Stack profil aktuális verziója: **2019-03-01**. Ha például a hálózati modul importálásához **2019-03-01** profil típusa, a következő kóddal:

   ```go
   package main
    import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
   ```

6. A függvényben létrehozásához, és az ügyfél hitelesítéséhez egy **új** ügyfél függvény hívásához szükséges. Hozzon létre egy virtuális hálózati ügyfél, a következő kódot használhatja:  

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"

   func main() {
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
   ```

   Állítsa be `<baseURI>` , a **ResourceManagerUrl** a 2. lépésben használt érték. Állítsa be `<subscriptionID>` , a **SubscriptionID** érték 3. lépésben mentett.

   A következő részben a token létrehozásához.  

7. API-metódusokat hívhat meg az előző lépésben létrehozott az ügyfél használatával. Például egy virtuális hálózat létrehozása az előző lépésben az ügyfél használatával, lásd az alábbi példát:

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

Virtuális hálózat létrehozása az Azure Stackben a Go SDK-profillal egy teljes példa: a [példa](#example).

## <a name="authentication"></a>Hitelesítés

Az első a **hitelesítő** tulajdonság az Azure Active Directoryból, a Go SDK segítségével telepítse a **Go-AutoRest** modulok. Ezeket a modulokat kell már telepítették a "Go SDK" telepítés; Ha nem, telepítse a [hitelesítési csomag a Githubról](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

A hitelesítő kell beállítani a hitelesítő az erőforrás-ügyfél. Többféleképpen tokenekhez hitelesítő az Azure Stacken ügyfél-hitelesítő adatok használatával:

1. Ha egy egyszerű szolgáltatást az előfizetésben tulajdonosi szerepkörrel rendelkező érhető el, hagyja ki ezt a lépést. Máskülönben hozzon létre egy [szolgáltatásnév](azure-stack-create-service-principals.md) , és rendelje hozzá egy "owner" szerepkör [hatóköre úgy, hogy az előfizetés](azure-stack-create-service-principals.md#assign-the-service-principal-to-a-role). Mentse a szolgáltatás egyszerű Alkalmazásazonosítót és titkos kulcsot.

2. Importálás a **adal** Go-AutoRest csomagot a kódban.

   ```go
   package main
   import "github.com/Azure/go-autorest/autorest/adal"
   ```

3. Hozzon létre egy **oauthConfig** NewOAuthConfig módszerrel használatával **adal** modul.

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/ada1"

   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
   }
   ```

   Állítsa be `<activeDirectoryEndpoint>` értékéhez a `loginEndpoint` tulajdonságot a `ResourceManagerUrl` lekéri a metaadatokat az előző szakaszban ebben a dokumentumban. Állítsa be a `<tenantID>` érték, az Azure Stack-bérlő azonosítója.

4. Végül hozzon létre egy egyszerű szolgáltatás-jogkivonat használatával a `NewServicePrincipalToken` módszerrel a **adal** modul:

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

    Állítsa be `<activeDirectoryResourceID>` a "célközönség" listában szereplő értékek egyikére a **ResourceManagerUrl** Ez a cikk korábbi szakaszában lekéri a metaadatokat.
    Állítsa be `<clientID>` a szolgáltatás egyszerű alkalmazást azonosító mentése egyszerű szolgáltatás létrehozásakor ez a cikk korábbi szakaszában.
    Állítsa be `<clientSecret>` , az egyszerű szolgáltatás létrehozásakor ez a cikk az előző szakaszban mentett szolgáltatás egyszerű alkalmazás titkos kulcsát.

## <a name="example"></a>Példa

Ez a példa létrehoz egy virtuális hálózatot az Azure Stacken Go-kódokat mintáját szemlélteti. A Go SDK teljes példákért lásd a [Azure Go SDK mintaadattárban](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Azure Stack-minták az adattár szolgáltatás mappákban hibrid útvonalon érhetők el.

> [!NOTE]  
> Ebben a példában a kód futtatásához, győződjön meg arról, hogy rendelkezik-e a használt előfizetés a **hálózati** állapottal erőforrás-szolgáltató **regisztrált**. Győződjön meg arról, az előfizetés az Azure Stack portálon keresse meg és válassza ki a **erőforrás-szolgáltatók.**

1. Importálja a szükséges csomagokat a kódban. A modul importálása a legújabb elérhető profilt az Azure Stacken használni:

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

2. A környezeti változókat határozhat meg. Hozzon létre egy virtuális hálózatot, egy erőforráscsoportot kell rendelkeznie.

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

3. Most, hogy a környezeti változók meghatározta, adjon meg egy metódust hozhat létre egy hitelesítési tokent a **adal** csomagot. A hitelesítéssel kapcsolatos további információkért lásd az előző szakaszt.

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

4. Adja hozzá a `main` metódust. A `main` metódus első kér le egy tokent az előző lépésben meghatározott módon. Egy ügyfél ezután a profil hálózati modul használatával létrehoz. Végül létrehoz egy virtuális hálózatot.

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
Elérhető az Azure Stack használatával a Go SDK Kódminták a következők:
- [Virtuális gép létrehozása](https://github.com/Azure-Samples/Hybrid-Compute-Go-Create-VM).
- [Tárolási Adatsík](https://github.com/Azure-Samples/Hybrid-Storage-Go-Dataplane).
- [Felügyelt lemezek használata](https://github.com/Azure-Samples/Hybrid-Compute-Go-ManagedDisks), (mintát, használja a 2019-03-01-profilt, amely a legújabb API-verziókat célzó támogatja az Azure Stack)
## <a name="next-steps"></a>További lépések

- [A PowerShell telepítése az Azure Stack szolgáltatáshoz](../operator/azure-stack-powershell-install.md)
- [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md)  
