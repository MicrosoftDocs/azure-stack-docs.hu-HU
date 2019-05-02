---
title: Szolgáltatói erőforrás-használati API |} A Microsoft Docs
description: Az erőforrás-használati API, amely az Azure Stack-használati adatait kérdezi le referenciája
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 01/25/2018
ms.openlocfilehash: b6e026732e75b3271053a00946c2b72accc85b0d
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985144"
---
# <a name="provider-resource-usage-api"></a>Szolgáltatói erőforrás-használati API

Az előfizetési időszak *szolgáltató* a szolgáltatás-rendszergazda, és minden olyan delegált szolgáltatók vonatkozik. Az Azure Stack-operátorok és a delegált szolgáltatók használhatják a szolgáltatói használati API közvetlen bérlők, a használati adatok megtekintéséhez. Például az ábrán látható módon csatlakoztatta, P0 teljesítményszintű meghívhatja a szolgáltató API-t a P1 a használati adatok lekérése, és P2 a közvetlen használatának és P1 meghívhatja P3 és P4 szintű használati információt.

![A szolgáltató hierarchia fogalmi modellhez](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>API-hívás referencia
### <a name="request"></a>Kérés
A kérést a kért előfizetéseket és a kért időkeretet felhasználási részletek beolvasása. Nincs nincs a kérelem törzsében.

A használati API, a szolgáltató API-t, így a hívó egy a szolgáltatót az előfizetésben tulajdonos, közreműködő vagy olvasó szerepkört kell hozzárendelni.

| **Metódus** | **Kérés URI-ja** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity} & subscriberId {sub1.1} = & api-version = 2015-06-01-preview & continuationtoken argumentumot használja = {token-value} |

### <a name="arguments"></a>Argumentumok

| **Argumentum** | **Leírás** |
| --- | --- |
| *armendpoint* |Az Azure Stack-környezet az Azure Resource Manager végpontját. Az Azure Stack egyezmény, hogy az Azure Resource Manager-végpont neve a következő formátumban van-e `https://adminmanagement.{domain-name}`. Például a development Kitet, ha a tartománynév *local.azurestack.external*, majd a Resource Manager-végpont `https://adminmanagement.local.azurestack.external`. |
| *subId* |Előfizetés-azonosítója a felhasználó, aki a hívást. |
| *reportedStartTime* |A lekérdezés kezdete. Az érték *DateTime* az egyezményes világidő (UTC) szerint, és ha például 13:00 óra kezdetén kell lennie. A napi aggregációs éjfélkor (UTC) értékre állítja. A formátum *escape-karakterrel* ISO 8601. Például *2015-06-16T18 % 3a53 % 3a11 % 2b00 % 3a00Z*, ahol a kettőspont escape-karakterrel megjelölve a *% 3a* és a plusz escape-karakterrel megjelölve a *: % 2/b.* úgy, hogy az URI rövid. |
| *reportedEndTime* |A lekérdezés vége. A korlátozásokat, amelyek a alkalmazni *reportedStartTime* ezt az argumentumot is vonatkoznak. Az érték *reportedEndTime* vagy az aktuális dátum későbbi lehet. Ha igen, az eredmény értéke "feldolgozása nem teljes." |
| *aggregationGranularity* |Nem kötelező paraméter, amely két különálló lehetséges értéke van: napi vagy óránkénti. Javasolt értékek, mint egy napi részletességgel az adatot adja vissza, a másik pedig egy óránkénti megoldás. A napi beállítás az alapértelmezett érték. |
| *subscriberId* |Előfizetés-azonosítójára. A szűrt adatokat kíván, az előfizetés-azonosító, a szolgáltató közvetlen bérlő szükség. Ha nincs előfizetés-azonosító paraméter van megadva, a hívás a használati adatok a szolgáltató közvetlen bérlők adja vissza. |
| *api-version* |Ez a kérés használt protokoll verziója. Ez az érték *2015-06-01-preview*. |
| *continuationToken* |Token lekért legutóbbi hívásának a használati API-szolgáltató. Ez a token van szükség, ha a válasz nagyobb, mint 1000 sort, és úgy működik, mint a folyamat állapotát a könyvjelző. Ha a jogkivonat nem található, az adatok lekérésének forrása a nap kezdete vagy átadott óra, a részletesség alapján. |

### <a name="response"></a>Válasz
ELSŐ /subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00: % 3a00 % 3a00 % 2b00 % 3a00 & aggregationGranularity napi & subscriberId = = sub1.1 & api-version = 1.0

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

| **Argumentum** | **Leírás** |
| --- | --- |
| *id* |A használati összesítés egyedi azonosítója. |
| *name* |A használati összesítés neve. |
| *type* |Erőforrás-definícióban. |
| *subscriptionId* |Az Azure Stack felhasználói előfizetés azonosítója. |
| *usageStartTime* |(UTC) kezdési időpontja a használati gyűjtőhöz, amelyhez a használat összesítés tartozik.|
| *usageEndTime* |A használati gyűjtőhöz, amelyhez a használat összesítés tartozik befejezési időpontja (UTC). |
| *instanceData* |Példány adatai (a új formátum) kulcs-érték párt:<br> *resourceUri*: Teljesen minősített erőforrás-azonosító, amely tartalmazza az erőforráscsoportok és a példány nevét. <br> *Hely*: A régió, amelyben ez a szolgáltatás futtatták. <br> *A címkék*: A felhasználó által megadott erőforrás-címkék. <br> *additionalInfo*: Adja meg az erőforrás a felhasznált, például az operációs rendszer verziója vagy a kép kapcsolatos további részletekért. |
| *Mennyiség* |Ezen az időn a következő erőforrás-használat mennyisége. |
| *meterId* |A felhasznált erőforrás egyedi azonosítója (más néven *ResourceID*). |


## <a name="retrieve-usage-information"></a>Használati adatok lekérése

### <a name="powershell"></a>PowerShell

A használati adatok létrehozásához, erőforrások, amelyek fut, és használja a rendszer, például aktívan, egy aktív virtuális géphez vagy egy bizonyos adatok stb tartalmazó tárfiókot kell rendelkeznie. Ha nem biztos hogy rendelkezik bármely Azure Stack piactéren-ban futó erőforrásokat, üzembe helyezése egy virtuális gépet (VM), és ellenőrizze a virtuális gép panelen ellenőrizze, hogy a figyelés, fut. A használati adatok megtekintéséhez használja a következő PowerShell-parancsmagokat:

1. [Az Azure Stack PowerShell telepítése.](azure-stack-powershell-install.md)
2. [Az Azure Stack-felhasználó konfigurálása](../user/azure-stack-powershell-configure-user.md) vagy a [Azure Stack-operátorokról](azure-stack-powershell-configure-admin.md) PowerShell-környezet 
3. A használati adatok lekéréséhez használja a [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) PowerShell-parancsmagot:
   ```powershell
   Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
   ```

### <a name="rest-api"></a>REST API

Törölt előfizetésekre vonatkozó használati adatokat gyűjthet a Microsoft.Commerce.Admin szolgáltatás meghívásával. 

**Vissza az összes bérlői használat törölni az aktív felhasználók:**

| **Metódus** | **Kérés URI-ja** |
| --- | --- |
| GET | https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version= 2015-06-01-előzetes verzió |

**Vissza a törölt vagy aktív bérlői használat:**

| **Metódus** | **Kérés URI-ja** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={ előfizető-azonosító} & api-version = 2015-06-01-előzetes verzió |


## <a name="next-steps"></a>További lépések
[Bérlői erőforrás-használati API-referencia](azure-stack-tenant-resource-usage-api.md)

[Használattal kapcsolatos gyakori kérdések](azure-stack-usage-related-faq.md)
