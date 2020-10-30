---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: ff18909ef7586d8099f0e01f29d53f3dbc3677f1
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2020
ms.locfileid: "93049763"
---
Végül határozza meg az erőforrás-szolgáltató legújabb telepítési tulajdonságait, és használja őket a titkos elforgatási folyamat befejezéséhez.

### <a name="determine-deployment-properties"></a>Központi telepítési tulajdonságok meghatározása

Az erőforrás-szolgáltatók a Azure Stack hub-környezetbe telepíthetők. A csomagokat a rendszer egyedi csomag-azonosítót rendeli hozzá a formátumban `'<product-id>.<installed-version>'` . Ahol az egy `<product-id>` egyedi karakterlánc, amely az erőforrás-szolgáltatót jelképezi, és `<installed-version>` egy adott verziót jelöl. Az egyes csomagokhoz társított titkokat a Azure Stack hub Key Vault szolgáltatás tárolja. 

Nyisson meg egy rendszergazda jogú PowerShell-konzolt, és hajtsa végre a következő lépéseket az erőforrás-szolgáltató titkos kódok elforgatásához szükséges tulajdonságok meghatározásához:

1. Jelentkezzen be az Azure Stack hub-környezetbe a kezelői hitelesítő adataival. Lásd: [kapcsolódás Azure stack hubhoz a](../operator/azure-stack-powershell-configure-admin.md) PowerShell használatával a PowerShell bejelentkezési parancsfájlhoz. Ügyeljen arra, hogy a PowerShellt (a AzureRM helyett) használja, és cserélje le az összes helyőrző értéket, például a végponti URL-címeket és a címtár-bérlő nevét.

2. Futtassa a `Get-AzsProductDeployment` parancsmagot a legújabb erőforrás-szolgáltatói üzemelő példányok listájának lekéréséhez. A visszaadott `"value"` gyűjtemény minden telepített erőforrás-szolgáltatóhoz tartalmaz egy elemet. Keresse meg az erőforrás-szolgáltatót, és jegyezze fel a tulajdonságok értékeit:
   - `"name"` -az erőforrás-szolgáltató termékazonosítóját tartalmazza az érték második szegmensében. 
   - `"properties"."deployment"."version"` -a jelenleg telepített verziószámot tartalmazza. 

   A következő példában a gyűjtemény első elemében figyelje meg a Event Hubs RP-telepítést, amely a termék AZONOSÍTÓját `"microsoft.eventhub"` és verzióját tartalmazta `"1.2003.0.0"` :

   ```powershell
   PS C:\WINDOWS\system32> Get-AzsProductDeployment -AsJson
   VERBOSE: GET https://adminmanagement.myregion.mycompany.com/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productDeployments?api-version=2019-01-01 with 0-char payload
   VERBOSE: Received 2656-char response, StatusCode = OK
   {
       "value":  [
                     {
                         "id":  "/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productDeployments/microsoft.eventhub",
                         "name":  "global/microsoft.eventhub",
                         "type":  "Microsoft.Deployment.Admin/locations/productDeployments",
                         "properties":  {
                                            "status":  "DeploymentSucceeded",
                                            "subscriptionId":  "b37ae55a-a6c6-4474-ba97-81519412adf5",
                                            "deployment":  {
                                                               "version":  "1.2003.0.0",
                                                               "actionPlanInstanceResourceId":"/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/actionplans/abcdfcd3-fef0-z1a3-z85d-z6ceb0f31e36",
                                                               "parameters":  {
   
                                                                              }
                                                           },
                                            "lastSuccessfulDeployment":  {
                                                                             "version":  "1.2003.0.0",
                                                                             "actionPlanInstanceResourceId":"/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/actionplans/abcdfcd3-fef0-z1a3-z85d-z6ceb0f31e36",
                                                                             "parameters":  {
   
                                                                                            }
                                                                         },
                                            "provisioningState":  "Succeeded"
                                        }
                     },
                     {
                     ...
                     }
                 ]
   }
   ```

3. Hozza létre az erőforrás-szolgáltató csomag-AZONOSÍTÓját az erőforrás-szolgáltató termékazonosítójának és verziójának összefűzésével. Például az előző lépésben származtatott értékek használatával a Event Hubs RP-csomag azonosítója: `microsoft.eventhub.1.2003.0.0` . 

4. Az előző lépésben származtatott csomag AZONOSÍTÓjának használatával futtassa a parancsot az `Get-AzsProductSecret -PackageId` erőforrás-szolgáltató által használt titkos típusok listájának lekéréséhez. A visszaadott `value` gyűjteményben keresse meg a tulajdonság értékét tartalmazó elemet `"Certificate"` `"properties"."secretKind"` . Ez az elem az RP tanúsítvány titkának tulajdonságait tartalmazza. Jegyezze fel a titkos tanúsítványhoz rendelt nevet, amelyet a tulajdonság utolsó szegmense azonosít `"name"` , közvetlenül a fentiek közül `"properties"` . 

   A következő példában a Event Hubs RP-hez visszaadott Secrets gyűjtemény tartalmaz egy `"Certificate"` nevű titkot `aseh-ssl-gateway-pfx` . 

   ```powershell
   PS C:\WINDOWS\system32> Get-AzsProductSecret -PackageId 'microsoft.eventhub.1.2003.0.0' -AsJson
   VERBOSE: GET
   https://adminmanagement.myregion.mycompany.com/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productPackages/microsoft.eventhub.1.2003.0.0/secrets?api-version=2019-01-01 with 0-char payload
   VERBOSE: Received 617-char response, StatusCode = OK
   {
       "value":  [
                     {
                         "id":  "/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productPackages/microsoft.eventhub.1.2003.0.0/secrets/aseh-ssl-gateway-pfx",
                         "name":  "global/microsoft.eventhub.1.2003.0.0/aseh-ssl-gateway-pfx",
                         "type":  "Microsoft.Deployment.Admin/locations/productPackages/secrets",
                         "properties":  {
                                            "secretKind":  "Certificate",
                                            "description":  "Event Hubs gateway SSL certificate.",
                                            "expiresAfter":  "P730D",
                                            "secretDescriptor":  {
   
                                                                 },
                                            "secretState":  {
                                                                "status":  "Deployed",
                                                                "rotationStatus":  "None",
                                                                "expirationDate":  "2022-03-31T00:16:05.3068718Z"
                                                            },
                                            "provisioningState":  "Succeeded"
                                        }
                     },
                     ...
                 ]
   }
   ```

### <a name="rotate-the-secrets"></a>A titkok elforgatása

1. A `Set-AzsProductSecret` parancsmag használatával importálja az új tanúsítványt Key Vaultre, amelyet a rotációs folyamat használni fog. A parancsfájl futtatása előtt cserélje le a változó helyőrző értékeket:

   | Helyőrző | Leírás | Példaérték |
   | ----------- | ----------- | --------------|
   | `<product-id>` | A legújabb erőforrás-szolgáltató telepítésének termékazonosítója. | `microsoft.eventhub` |
   | `<installed-version>` | A legújabb erőforrás-szolgáltató telepítésének verziója. | `1.2003.0.0` |
   | `<cert-secret-name>` | A tanúsítvány titkos kulcsának tárolására szolgáló név. | `aseh-ssl-gateway-pfx` |
   | `<cert-pfx-file-path>` | A tanúsítvány PFX-fájljának elérési útja. | `C:\dir\eh-cert-file.pfx` |
   | `<pfx-password>` | A tanúsítványhoz rendelt jelszó. PFX-fájl. | `strong@CertSecret6` |

   ```powershell
   $productId = '<product-id>'
   $packageId = $productId + '.' + '<installed-version>'
   $certSecretName = '<cert-secret-name>' 
   $pfxFilePath = '<cert-pfx-file-path>'
   $pfxPassword = ConvertTo-SecureString '<pfx-password>' -AsPlainText -Force   
   Set-AzsProductSecret -PackageId $packageId -SecretName $certSecretName -PfxFileName $pfxFilePath -PfxPassword $pfxPassword -Force
   ```

2. Végül használja a `Invoke-AzsProductRotateSecretsAction` parancsmagot a belső és külső titkok elforgatásához:

   > [!NOTE]
   > A rotációs folyamat elvégzése körülbelül 3,5 – 4 órát vesz igénybe.

   ```powershell
   Invoke-AzsProductRotateSecretsAction -ProductId $productId
   ```
   
   A titkos rotációs folyamatot a PowerShell-konzolon vagy a felügyeleti portálon figyelheti, ha kiválasztja az erőforrás-szolgáltatót a piactér szolgáltatásban:

   [![titkos elforgatás – előrehaladás](media/resource-provider-va-rotate-secrets-rotate/secret-rotation-in-progress.png)](media/resource-provider-va-rotate-secrets-rotate/secret-rotation-in-progress.png#lightbox)

