---
title: Az Azure Stack API használata |} A Microsoft Docs
description: Ismerje meg, hogyan kérheti le a hitelesítést, hogy API-kéréseket az Azure Stack Azure-ból.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 1773083a5d02f3bb988ac3e5cef6528a5d49b94e
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64300603"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="use-the-azure-stack-api"></a>Az Azure Stack API használata

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az alkalmazásprogramozási felület (API) segítségével automatizálja a műveleteket, mint egy virtuális gép hozzáadása az Azure Stack-felhőben.

Az API megköveteli az ügyfél a Microsoft Azure bejelentkezési végpont hitelesítéséhez. A végpont használata minden egyes az Azure Stack API-nak küldött kérés fejlécében jogkivonatot ad vissza. A Microsoft Azure az Oauth 2.0 használja.

Ez a cikk példákat, amelyek használják a **cURL** segédprogram az Azure Stack-kérelmek létrehozására. A kérelmet a cURL, egy olyan parancssori eszköz tartozik egy kódtár, az adatok átviteléhez. Ezekben a példákban végig egy tokent az Azure Stack API eléréséhez lekérését jelenti. Szinte bármelyik programozási nyelvével adja meg az Oauth 2.0-könyvtárak, amelyeknek robusztus token felügyeleti és leíró feladatok ilyen a jogkivonat frissítését.

Tekintse át az Azure Stack – REST API használatával olyan általános REST ügyfelekkel, mint például a teljes folyamatán **cURL**, segítenek megérteni a mögöttes kéréseket, és bemutatja, hogy mire számíthat egy válasz hasznos adatok fogadására.

Ez a cikk nem érhető el a jogkivonatokat, például az interaktív bejelentkezéshez beolvasása, vagy dedikált Alkalmazásazonosítók létrehozásának minden lehetőségek közül válogathat. Ezek a témakörök kapcsolatos információk lekéréséhez lásd: [Azure REST API-referencia](https://docs.microsoft.com/rest/api/).

## <a name="get-a-token-from-azure"></a>Az Azure-ból egy token beszerzése

Hozzon létre egy kérelem törzse formázva a tartalomtípus x-www-form-urlencoded hozzáférési jogkivonat beszerzése. Az Azure REST hitelesítési és bejelentkezési végpont kérelem KÜLDÉSE.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**Bérlőazonosító** kisebb, mint:

 - A bérlő tartománya, mint például `fabrikam.onmicrosoft.com`
 - A bérlő AZONOSÍTÓJÁT, például `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
 - Független bérlői kulcsok alapértelmezett értéke: `common`

### <a name="post-body"></a>Bejegyzés törzse

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Minden egyes érték:

- **grant_type**  
   A hitelesítési séma használatával fogja típusát. Ebben a példában a értéke `password`

- **resource**  
   Az erőforráshoz fér hozzá a jogkivonat. Az erőforrás megtalálhatja az Azure Stack felügyeleti metaadatok végpontja lekérdezésével. Tekintse meg a **célközönséggel** szakasz

- **Az Azure Stack-felügyeleti végpont**  
   ```
   https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
   ```

  > [!NOTE]  
  > Ha Ön rendszergazda megpróbál hozzáférni a bérlői API-hoz majd ellenőrizze, a bérlő a végpontot, például használja: `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`  

  Ha például az az Azure Stack Development Kit végpontként:

   ```bash
   curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
   ```

  Válasz:

  ```
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

  ```
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

  **client_id**

  Ez az érték nem változtatható az alapértelmezett érték:

  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Másik lehetőség is elérhető, bizonyos forgatókönyvek esetén:

  
  | Alkalmazás | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **felhasználónév**

  Ha például az Azure Stack AAD-fiók:

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **jelszó**

  Az Azure Stack AAD-rendszergazdai jelszót.

### <a name="example"></a>Példa

Kérés:

```
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

Válasz:

```
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

A hozzáférési jogkivonatot kap, miután hozzá kell azt fejlécnek egyes az API-kérelmek. Ehhez hozzon létre egy fejlécet kell **engedélyezési** értékkel: `Bearer <access token>`. Példa:

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

### <a name="url-structure-and-query-syntax"></a>URL-cím szerkezete és lekérdezési szintaxis

Általános kérés URI-t, áll: {URI-scheme} :// {URI-gazdagép} / {erőforrás-elérési út}? {lekérdezés-karakterlánc}

- **Schéma identifikátoru URI**:  
Az URI-t a kérelem elküldéséhez használt protokollt jelzi. Ha például `http` vagy `https`.
- **URI-gazdagép**:  
Az állomás tartománynév vagy IP-címet, ahol a REST-szolgáltatásvégpontot futtató, mint például a kiszolgáló `graph.microsoft.com` vagy `adminmanagement.local.azurestack.external`.
- **Erőforrás elérési útja**:  
Az elérési utat adja meg, az erőforrás vagy erőforrás-gyűjtemény, amely ezeket az erőforrásokat a választott meghatározásakor a szolgáltatás által használt több szegmenst. Például: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` lekérdezéslistában egy adott alkalmazás tulajdonosok az alkalmazások gyűjteményen belül használható.
- **Lekérdezési karakterlánc**:  
A karakterlánc a további egyszerű paramétereket, például az API verziót vagy az erőforrások kiválasztási feltételek biztosít.

## <a name="azure-stack-request-uri-construct"></a>Az Azure Stack kérés URI-szerkezet

```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>URI-szintaxis

```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>Lekérdezési URI-példa

```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>További lépések

Az Azure RESTful végpontokon használatával kapcsolatos további információkért lásd: [Azure REST API-referencia](https://docs.microsoft.com/rest/api/).
