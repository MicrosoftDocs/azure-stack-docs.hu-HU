---
title: API-kérelmek készítése Azure Stack | Microsoft Docs
description: Megtudhatja, hogyan kérhet le hitelesítést az Azure-ból, hogy API-kérelmeket Azure Stack.
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
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 822d05c53db2d55b3cddac44fa919c72e9af2efe
ms.sourcegitcommit: bbf3edbfc07603d2c23de44240933c07976ea550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71714651"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="make-api-requests-to-azure-stack"></a>API-kérelmek Azure Stackának elvégzése

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Azure Stack REST API-k segítségével automatizálhat olyan műveleteket, mint például a virtuális gép (VM) hozzáadása a Azure Stack-felhőhöz.

Az API-k megkövetelik, hogy az ügyfél hitelesítse magát a Microsoft Azure bejelentkezési végpontján. A végpont visszaadja a Azure Stack API-khoz elküldett kérelmek fejlécében használandó tokent. Microsoft Azure a 2,0 OAuth használja.

Ez a cikk olyan példákat tartalmaz, amelyek a **curl** segédprogramot használják Azure stack kérelmek létrehozásához. a cURL egy olyan parancssori eszköz, amely az adatátvitelre szolgáló függvénytárat biztosít. Ezek a példák végigvezetik a token lekérésének folyamatán a Azure Stack API-k eléréséhez. A legtöbb programozási nyelv olyan OAuth 2,0 kódtárakat biztosít, amelyek robusztus jogkivonat-felügyelettel rendelkeznek, és olyan feladatokat kezelnek, mint például a jogkivonat frissítése.

Tekintse át a Azure Stack REST API-k használatának teljes folyamatát egy általános REST-ügyféllel, például a **fürtökkel**, hogy segítsen megérteni a mögöttes kérelmeket, és hogy mit várhat a válasz hasznos adataiban.

Ez a cikk nem vizsgálja meg a jogkivonatok beolvasásához rendelkezésre álló összes lehetőséget, például az interaktív bejelentkezést vagy a dedikált alkalmazás-azonosítók létrehozását. A témakörökkel kapcsolatos információkért tekintse meg az [Azure REST API referenciát](/rest/api/).

## <a name="get-a-token-from-azure"></a>Token beszerzése az Azure-ból

Hozzáférési jogkivonat beszerzéséhez hozzon létre egy `x-www-form-urlencoded` tartalomtípus használatával formázott kérelem törzsét. Küldje el a kérését az Azure REST-hitelesítésre és a bejelentkezési végpontra.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

A **bérlő azonosítója** a következők egyike:

- A bérlő tartománya, például`fabrikam.onmicrosoft.com`
- A bérlő azonosítója, például`8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
- Alapértelmezett érték a bérlői független kulcsok esetében:`common`

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
   A használni kívánt hitelesítési séma típusa. Ebben a példában a érték `password`a következő:.

- **erőforrás**:  
   A jogkivonathoz hozzáférő erőforrás. Az erőforrást a Azure Stack felügyeleti metaadatok végpontjának lekérdezésével keresheti meg. Tekintse meg a **célközönségek** szakaszt.

- **Azure stack felügyeleti végpont**:

   ```bash
   https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
   ```

  > [!NOTE]  
  > Ha egy rendszergazda megpróbál hozzáférni a bérlői API-hoz, ügyeljen arra, hogy a bérlői végpontot használja; például `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`.

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

  | Alkalmazás | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

- **felhasználónév**

  Például a Azure Stack Azure AD-fiók:

  ```bash
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

- **jelszó**

  Az Azure AD rendszergazdai jelszavának Azure Stack.

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

Általános kérelem URI-ja, a következőkből áll:`{URI-scheme} :// {URI-host} / {resource-path} ? {query-string}`

- **URI-séma**:  
Az URI a kérelem küldéséhez használt protokollt jelzi. Ha például `http` vagy `https`.
- **URI-gazdagép**:  
A gazdagép megadja annak a kiszolgálónak a tartománynevét vagy IP-címét, amelyen a REST szolgáltatás végpontja üzemel, `graph.microsoft.com` például `adminmanagement.local.azurestack.external`vagy.
- **Erőforrás elérési útja**:  
Az elérési út meghatározza az erőforrást vagy az erőforrás-gyűjteményt, amely magában foglalhatja a szolgáltatás által az erőforrások kiválasztásának meghatározásához használt több szegmenst is. Például: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` a (z) segítségével lekérdezheti egy adott alkalmazás tulajdonosának listáját az alkalmazások gyűjteményén belül.
- **Lekérdezési karakterlánc**:  
A karakterlánc további egyszerű paramétereket tartalmaz, például az API-verziót vagy az erőforrás-kiválasztási feltételeket.

## <a name="azure-stack-request-uri-construct"></a>Azure Stack kérelem URI-építése

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

## <a name="next-steps"></a>További lépések

Az Azure REST-végpontok használatával kapcsolatos további információkért lásd az [azure REST API referenciáját](/rest/api/).
