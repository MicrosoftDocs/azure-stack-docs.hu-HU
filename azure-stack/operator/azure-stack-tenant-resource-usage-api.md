---
title: Bérlői erőforrás-használati API-k leírása
titleSuffix: Azure Stack
description: Az erőforrás-használati API-k referenciája Azure Stack hub használati adatainak beolvasása.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 5e6fd1042edcf59955a6e766d2ffb215c49c2949
ms.sourcegitcommit: c4368652f0dd68c432aa1dabddbabf161a4a6399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75914720"
---
# <a name="tenant-resource-usage-api-reference"></a>Bérlői erőforrás-használati API-referenciák

A bérlő a bérlői API-k használatával megtekintheti a bérlő saját erőforrás-használati adatait. Ezek az API-k konzisztensek az Azure használati API-kkal.

Használhatja a [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) Windows PowerShell-parancsmagot a használati adatok lekéréséhez, akárcsak az Azure-ban.

## <a name="api-call"></a>API-hívás

### <a name="request"></a>Kérelem

A kérelem lekéri a kért előfizetések és a kért időkeret felhasználásának részleteit. Nincs kérelem törzse.

| **Metódus** | **Kérelem URI-ja** |
| --- | --- |
| GET |https://{armendpoint}/Subscriptions/{subId}/Providers/Microsoft. Commerce/usageAggregates? reportedStartTime = {reportedStartTime} & reportedEndTime = {reportedEndTime} & aggregationGranularity = {részletesség} & API-Version = 2015-06 -01-előzetes verzió & Continuationtoken argumentumot használja = {token-Value} |

### <a name="parameters"></a>Paraméterek

| **Paraméter** | **Leírás** |
| --- | --- |
| Armendpoint |Azure Resource Manager a Azure Stack hub-környezet végpontját. Az Azure Stack hub-egyezmény azt adja meg, hogy Azure Resource Manager végpont neve `https://management.{domain-name}`formátumú. A fejlesztői csomag esetében például a tartománynév helyi. azurestack. external, majd a Resource Manager-végpont `https://management.local.azurestack.external`. |
| subId |A hívást végző felhasználó előfizetés-azonosítója. Ezt az API-t csak egyetlen előfizetés használatának lekérdezésére használhatja. A szolgáltatók használhatják a szolgáltatói erőforrás-használati API-t az összes bérlő használatának lekérdezéséhez. |
| reportedStartTime |A lekérdezés kezdési időpontja. A *datetime* értékének UTC és az óra elején kell lennie; például 13:00. A napi összesítéshez állítsa ezt az értéket UTC éjfélre. A formátum megmenekült ISO 8601; Tegyük fel például, hogy **2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z**, ahol a kettőspont megmenekült a (z)% 3A rendszerbe, és plusz megmenekült a (z)% 2b rendszerbe, hogy URI-barát legyen |
| reportedEndTime |A lekérdezés befejezési időpontja. A **reportedStartTime** vonatkozó korlátozások érvényesek erre a paraméterre is. A **reportedEndTime** értéke nem lehet későbbi. |
| aggregationGranularity |Opcionális paraméter, amely két különálló lehetséges értékkel rendelkezik: **naponta** és **óránként**. Az értékek azt sugallják, hogy az egyik napi részletességgel adja vissza az adatokat, a másik pedig óradíjas megoldás. A **napi** beállítás az alapértelmezett. |
| api-verzió |A kérelem elvégzéséhez használt protokoll verziója. A **2015-06-01-Preview**verziót kell használnia. |
| Continuationtoken argumentumot használja |A rendszer a használati API-szolgáltató utolsó hívásával lekért tokent. Erre a tokenre akkor van szükség, ha a válasz nagyobb, mint 1 000 sor. A folyamat könyvjelzőként működik. Ha nincs jelen, az adatok a nap vagy az óra elejétől kezdve, az átadott részletesség alapján kerülnek beolvasásra. |

### <a name="response"></a>Válasz

```html
GET
/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0
```

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

...
```

### <a name="response-details"></a>Válasz részletei

| **Paraméter** | **Leírás** |
| --- | --- |
| id |A használati összesítés egyedi azonosítója. |
| név |A használati összesítés neve. |
| type |Erőforrás-definíció. |
| subscriptionId |Az Azure-felhasználó előfizetés-azonosítója. |
| usageStartTime |Annak a használati gyűjtőnek az UTC szerinti kezdési időpontja, amelyhez ez a használati összesítés tartozik. |
| usageEndTime |Azon használati gyűjtő UTC-befejezési időpontja, amelyhez ez a használati összesítés tartozik. |
| instanceData |Példány részleteinek kulcs-érték párok (új formátumban):<br>  *resourceUri*: teljes erőforrás-azonosító, beleértve az erőforráscsoportok és a példánynév nevét. <br>  *hely*: az a régió, amelyben a szolgáltatást futtatták. <br>  *címkék*: a felhasználó által megadott erőforrás-címkék. <br>  *AdditionalInfo*: További információ a felhasznált erőforrásról. Például operációs rendszer verziója vagy rendszerkép típusa. |
| quantity |Az adott időkeretben bekövetkezett erőforrás-felhasználás mennyisége. |
| meterId |A felhasznált erőforrás egyedi azonosítója (más néven **ResourceId**). |

## <a name="next-steps"></a>Következő lépések

- [Szolgáltatói erőforrás-használati API](azure-stack-provider-resource-api.md)
- [Használattal kapcsolatos gyakori kérdések](azure-stack-usage-related-faq.md)
