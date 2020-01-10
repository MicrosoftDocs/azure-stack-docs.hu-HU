---
title: Azure Stack hub-szolgáltató erőforrás-használati API | Microsoft Docs
description: Az erőforrás-használati API-ra vonatkozó hivatkozás, amely lekéri Azure Stack hub használati adatait.
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
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/25/2018
ms.openlocfilehash: f7d39ce1c2a33a6a693a56a6c5c86b7e1275993d
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75810069"
---
# <a name="provider-resource-usage-api"></a>Szolgáltató erőforrás-használat API

A *szolgáltató* kifejezés a szolgáltatás-rendszergazdára és a delegált szolgáltatókra is érvényes. Azure Stack hub-operátorok és a delegált szolgáltatók használhatják a szolgáltatói használati API-t a közvetlen bérlők használatának megtekintésére. Például az alábbi ábrán látható módon a P0 meghívhatja a szolgáltatói API-t a közvetlen használati adatok lekérésére a P1 és P2 címen, a P1 pedig a P3-as és a P4-es használati adatokat hívhatja meg.

![A szolgáltatói hierarchia fogalmi modellje](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>API-hívás referenciája

### <a name="request"></a>Kérelem

A kérelem lekéri a kért előfizetések és a kért időkeret felhasználásának részleteit. Nincs kérelem törzse.

Ez a használati API egy szolgáltatói API, így a hívónak hozzá kell rendelnie egy **tulajdonos**, **közreműködő**vagy **olvasó** szerepkört a szolgáltató előfizetésében.

| Módszer | Kérelem URI-ja |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value}` |

### <a name="arguments"></a>Argumentumok

| Argumentum | Leírás |
| --- | --- |
| `armendpoint` |Azure Resource Manager a Azure Stack hub-környezet végpontját. Az Azure Stack hub-egyezmény azt adja meg, hogy a Azure Resource Manager végpont neve `https://adminmanagement.{domain-name}`formátumú. Például a Azure Stack Development Kit (ASDK) esetében, ha a tartománynév *helyi. azurestack. external*, akkor a Resource Manager-végpont `https://adminmanagement.local.azurestack.external`. |
| `subId` |Annak a felhasználónak az előfizetés-azonosítója, aki a hívást kezdeményezi. |
| `reportedStartTime` |A lekérdezés kezdési időpontja. `DateTime` értékének az egyezményes világidő (UTC) és az óra elején kell lennie. például 13:00. A napi összesítéshez állítsa ezt az értéket UTC éjfélre. A formátum megmenekült ISO 8601; például `2015-06-16T18%3a53%3a11%2b00%3a00Z`, ahol a kettőspont megmenekült a `%3a`, és a plusz megmenekül a `%2b`, hogy URI-barát legyen. |
| `reportedEndTime` |A lekérdezés befejezési időpontja. A `reportedStartTime` vonatkozó korlátozások erre az argumentumra is érvényesek. `reportedEndTime` értéke nem lehet a jövőben vagy az aktuális dátum. Ha igen, az eredmény "feldolgozás nem fejeződött be" értékre van állítva. |
| `aggregationGranularity` |Opcionális paraméter, amely két különálló lehetséges értékkel rendelkezik: **naponta** és **óránként**. Az értékek azt sugallják, hogy az egyik napi részletességgel adja vissza az adatokat, a másik pedig óradíjas megoldás. A **napi** beállítás az alapértelmezett. |
| `subscriberId` |Előfizetés azonosítója. A szűrt adatlekérdezéshez a szolgáltató közvetlen bérlője előfizetés-AZONOSÍTÓjának megadása szükséges. Ha nincs megadva előfizetés-azonosító paraméter, a hívás az összes szolgáltató közvetlen bérlője használati adatait adja vissza. |
| `api-version` |A kérelem elvégzéséhez használt protokoll verziója. Ez az érték `2015-06-01-preview`re van állítva. |
| `continuationToken` |A rendszer a használati API-szolgáltató utolsó hívásával lekért tokent. Erre a tokenre akkor van szükség, ha a válasz nagyobb, mint 1 000 sor. A folyamat könyvjelzőként működik. Ha a jogkivonat nincs jelen, az adatok a nap vagy az óra elejétől kezdve, az átadott részletesség alapján kerülnek beolvasásra. |

### <a name="response"></a>Válasz

```http
GET
/subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&subscriberId=sub1.1&api-version=1.0
```

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce.Admin/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce.Admin/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

. . .
```

### <a name="response-details"></a>Válasz részletei

| Argumentum | Leírás |
| --- | --- |
|`id` |A használati összesítés egyedi azonosítója. |
|`name` |A használati összesítés neve. |
|`type` |Erőforrás-definíció. |
|`subscriptionId` |Az Azure Stack hub-felhasználó előfizetés-azonosítója. |
|`usageStartTime`|Annak a használati gyűjtőnek az UTC szerinti kezdési időpontja, amelyhez ez a használati összesítés tartozik.|
|`usageEndTime`|Azon használati gyűjtő UTC-befejezési időpontja, amelyhez ez a használati összesítés tartozik. |
|`instanceData` |Példány részleteinek kulcs-érték párok (új formátumban):<br> `resourceUri`: teljes erőforrás-azonosító, amely magában foglalja az erőforráscsoportot és a példánynév nevét. <br> `location`: az a régió, amelyben a szolgáltatást futtatták. <br> `tags`: a felhasználó által megadott erőforrás-címkék. <br> `additionalInfo`: További információ a felhasznált erőforrásról; például az operációs rendszer verziója vagy a rendszerkép típusa. |
|`quantity`|Az adott időkeretben bekövetkezett erőforrás-felhasználás mennyisége. |
|`meterId` |A felhasznált erőforrás egyedi azonosítója (más néven `ResourceID`). |

## <a name="retrieve-usage-information"></a>Használati adatok beolvasása

### <a name="powershell"></a>PowerShell

A használati adatok létrehozásához a rendszert futtató és aktívan használt erőforrásokkal kell rendelkeznie. például egy aktív virtuális gép (VM) vagy egy bizonyos adathalmazt tartalmazó Storage-fiók. Ha nem biztos abban, hogy rendelkezik-e a Azure Stack hub piactéren futó erőforrásokkal, helyezzen üzembe egy virtuális gépet, és ellenőrizze, hogy fut-e a virtuális gép figyelése panel. Használja a következő PowerShell-parancsmagokat a használati adatok megtekintéséhez:

1. [Telepítse a powershellt Azure stack hubhoz](azure-stack-powershell-install.md).
2. [Konfigurálja a Azure stack hub-felhasználót](../user/azure-stack-powershell-configure-user.md) vagy a [Azure stack hub-kezelő](azure-stack-powershell-configure-admin.md) PowerShell-környezetét.
3. A használati adatok lekéréséhez hívja meg a [Get-AzsSubscriberUsage](/powershell/module/azs.commerce.admin/get-azssubscriberusage) PowerShell-parancsmagot:

   ```powershell
   Get-AzsSubscriberUsage -ReportedStartTime "2017-09-06T00:00:00Z" -ReportedEndTime "2017-09-07T00:00:00Z"
   ```

### <a name="rest-api"></a>REST API

A törölt előfizetésekre vonatkozó használati adatokat a **Microsoft. Commerce. admin** szolgáltatás meghívásával gyűjthet.

#### <a name="return-all-tenant-usage-for-deleted-for-active-users"></a>Az összes bérlői használat visszaküldése aktív felhasználók számára

| Módszer | Kérelem URI-ja |
| --- | --- |
| GET | `https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version=2015-06-01-preview` |

#### <a name="return-usage-for-deleted-or-active-tenant"></a>Törölt vagy aktív bérlő használatának visszaküldése

| Módszer | Kérelem URI-ja |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={subscriber-id}&api-version=2015-06-01-preview` |

## <a name="next-steps"></a>Következő lépések

- [Bérlői erőforrás-használati API-referenciák](azure-stack-tenant-resource-usage-api.md)
- [Használattal kapcsolatos gyakori kérdések](azure-stack-usage-related-faq.md)
