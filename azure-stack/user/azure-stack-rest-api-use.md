---
title: API-kérelmek készítése Azure Stack hubhoz
description: Megtudhatja, hogyan kérhet le hitelesítést az Azure-ból, hogy API-kérelmeket Azure Stack hub-ra hozzon.
author: sethmanheim
ms.topic: article
ms.date: 01/23/2020
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 45dd07337aef61a1fcfecb855e0c113315b15271
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883211"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="make-api-requests-to-azure-stack-hub"></a>API-kérelmek készítése Azure Stack hubhoz

Az Azure Stack hub REST API-k segítségével automatizálhat olyan műveleteket, mint például a virtuális gép (VM) hozzáadása a Azure Stack hub-felhőhöz.

Az API-k megkövetelik, hogy az ügyfél hitelesítse magát a Microsoft Azure bejelentkezési végpontján. A végpont visszaadja az Azure Stack hub API-khoz eljuttatott minden kérelem fejlécében használandó tokent. Microsoft Azure a 2,0 OAuth használja.

Ez a cikk olyan példákat tartalmaz, amelyek a **curl** segédprogramot használják Azure stack hub-kérelmek létrehozásához. a cURL egy olyan parancssori eszköz, amely az adatátvitelre szolgáló függvénytárat biztosít. Ezek a példák végigvezetik a tokenek lekérésének folyamatán az Azure Stack hub API-k eléréséhez. A legtöbb programozási nyelv olyan OAuth 2,0 kódtárakat biztosít, amelyek robusztus jogkivonat-felügyelettel rendelkeznek, és olyan feladatokat kezelnek, mint például a jogkivonat frissítése.

Tekintse át az Azure Stack hub REST API-k teljes folyamatát egy általános REST-ügyféllel, például a **curltel**, hogy könnyebben megértse a mögöttes kérelmeket, és hogy mit várhat a válasz hasznos adataiban.

Ez a cikk nem vizsgálja meg a jogkivonatok beolvasásához rendelkezésre álló összes lehetőséget, például az interaktív bejelentkezést vagy a dedikált alkalmazás-azonosítók létrehozását. A témakörökkel kapcsolatos információkért tekintse meg az [Azure REST API referenciát](/rest/api/).

## <a name="get-a-token-from-azure"></a>Token beszerzése az Azure-ból

Hozzáférési jogkivonat beszerzéséhez hozzon létre egy kérelem törzsét, amely a `x-www-form-urlencoded` tartalomtípus használatával van formázva. Küldje el a kérését az Azure REST-hitelesítésre és a bejelentkezési végpontra.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

A **bérlő azonosítója** a következők egyike:

- A bérlői tartomány, például `fabrikam.onmicrosoft.com`
- A bérlő azonosítója, például `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
- A bérlőtől független kulcsok alapértelmezett értéke: `common`

### <a name="post-body"></a>Post törzs

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Minden értéknél:

- **grant_type**:  
   A használni kívánt hitelesítési séma típusa. Ebben a példában az érték `password`.

- **erőforrás**:  
   A jogkivonathoz hozzáférő erőforrás. Az erőforrást az Azure Stack hub felügyeleti metaadatok végpontjának lekérdezésével keresheti meg. Tekintse meg a **célközönségek** szakaszt.

- **Azure stack hub felügyeleti végpontja**:

   ```bash
   https://management.{region}.{Azure Stack Hub domain}/metadata/endpoints?api-version=2015-01-01
   ```

  > [!NOTE]  
  > Ha egy rendszergazda megpróbál hozzáférni a bérlői API-hoz, ügyeljen arra, hogy a bérlői végpontot használja; például `https://adminmanagement.{region}.{Azure Stack Hub domain}/metadata/endpoints?api-version=2015-01-011`.

  Például a Azure Stack Development Kit végpontként:

   ```bash
   curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
   ```

  Válasz:

  ```bash
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
     "loginEndpoint":"https://login.windows.net/",
     "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
     }
  }
  ```

### <a name="example"></a>Példa

  ```bash
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

- **client_id**

  Ez az érték egy alapértelmezett értékre hardcoded:

  ```bash
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Az alternatív lehetőségek az adott forgatókönyvekhez érhetők el:

  | Jelentkezés | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40BE-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000 – C000 – 000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

- **username**

  Például az Azure Stack hub Azure AD-fiók:

  ```bash
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

- **jelszó**

  Az Azure Stack hub Azure AD-rendszergazdai jelszava.

### <a name="example"></a>Példa

Kérés:

```bash
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

Válasz:

```bash
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>API-lekérdezések

A hozzáférési jogkivonat beszerzése után minden API-kérelemhez vegye fel a fejlécet. Fejlécként való hozzáadásához hozzon létre egy **engedélyezési** fejlécet a következő értékkel: `Bearer <access token>`. Példa:

Kérés:

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Válasz:

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>URL-struktúra és lekérdezési szintaxis

Általános kérelem URI-ja, a következőkből áll: `{URI-scheme} :// {URI-host} / {resource-path} ? {query-string}`

- **URI-séma**:  
Az URI a kérelem küldéséhez használt protokollt jelzi. Például `http` vagy `https`.
- **URI-gazdagép**:  
A gazdagép megadja annak a kiszolgálónak a tartománynevét vagy IP-címét, amelyen a REST szolgáltatási végpont található, például `graph.microsoft.com` vagy `adminmanagement.local.azurestack.external`.
- **Erőforrás elérési útja**:  
Az elérési út meghatározza az erőforrást vagy az erőforrás-gyűjteményt, amely magában foglalhatja a szolgáltatás által az erőforrások kiválasztásának meghatározásához használt több szegmenst is. Például: a `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` segítségével lekérdezheti a listát egy adott alkalmazás tulajdonosainak az alkalmazások gyűjteményén belül.
- **Lekérdezési karakterlánc**:  
A karakterlánc további egyszerű paramétereket tartalmaz, például az API-verziót vagy az erőforrás-kiválasztási feltételeket.

## <a name="azure-stack-hub-request-uri-construct"></a>Azure Stack hub-kérelem URI-építése

```bash
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>URI-szintaxis

```bash
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>Példa lekérdezési URI-ra

```bash
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>Következő lépések

Az Azure REST-végpontok használatával kapcsolatos további információkért lásd az [azure REST API referenciáját](/rest/api/).
