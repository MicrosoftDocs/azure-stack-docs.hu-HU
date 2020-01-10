---
title: Azure Stack hub Azure Monitor támogatott mérőszámai | Microsoft Docs
description: Az Azure Stack hub Azure Monitor támogatott metrikáinak megismerése.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: df3644be6384bd264dc78ec80f8119c9cd2ff630
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75819743"
---
# <a name="supported-metrics-for-azure-monitor-on-azure-stack-hub"></a>Azure Stack hub Azure Monitor támogatott mérőszámai

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek*

Az Azure monitor Azure Stack hub-on lévő mérőszámait ugyanúgy kell lekérni, mint a globális Azure-ban. Létrehozhat mértékeket a portálon, beolvashatja őket a REST API, vagy lekérdezheti őket a PowerShell vagy a parancssori felület használatával.

Az alábbi táblázatok felsorolják az Azure Stack hub Azure Monitor metrikai folyamatában elérhető metrikákat. A metrikák lekérdezéséhez és eléréséhez használja az API-profil **2018-01-01** API-Version verzióját. További információ az API-profilokról és az Azure Stack hub-ról: [az API-verziók profiljainak kezelése Azure stack központban](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Metrika | Metrika megjelenítendő neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Átlag | A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya. | Nincsenek méretek |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Metrika | Metrika megjelenítendő neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Felhasznált kapacitás | Bájt | Átlag | A fiók felhasznált kapacitása. | Nincsenek méretek |
| Tranzakciók | Tranzakciók | Mennyiség | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Belépő | Belépő | Bájt | Összes | A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodperc | Átlag | Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres E2E késés | Ezredmásodperc | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Elérhetőség | Elérhetőség | Százalék | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

| Metrika | Metrika megjelenítendő neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|--------|---------------------|------|------------------|-------------|------------|
| BlobCapacity | Blob-kapacitása | Bájt | Összes | A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve. | blobType |
| BlobCount | Blobok száma | Mennyiség | Összes | A Storage-fiók Blob serviceban található Blobok száma. | blobType |
| ContainerCount | Blobtárolók száma | Mennyiség | Átlag | A Storage-fiók Blob service lévő tárolók száma. | Nincsenek méretek |
| Tranzakciók | Tranzakciók | Mennyiség | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Belépő | Belépő | Bájt | Összes | A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodperc | Átlag | Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres E2E késés | Ezredmásodperc | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Elérhetőség | Elérhetőség | Százalék | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

| Metrika | Metrika megjelenítendő neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Tábla kapacitása | Bájt | Átlag | A Storage-fiók Table service által felhasznált tárterület mérete bájtban kifejezve. | Nincsenek méretek |
| TableCount | Táblák száma | Mennyiség | Átlag | A Storage-fiók Table serviceban található táblák száma. | Nincsenek méretek |
| TableEntityCount | Tábla entitások száma | Mennyiség | Átlag | A Storage-fiók Table serviceban szereplő táblák entitások száma. | Nincsenek méretek |
| Tranzakciók | Tranzakciók | Mennyiség | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Belépő | Belépő | Bájt | Összes | A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodperc | Átlag | Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres E2E késés | Ezredmásodperc | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Elérhetőség | Elérhetőség | Százalék | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

| Metrika | Metrika megjelenítendő neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Várólista kapacitása | Bájt | Átlag | A Storage-fiók Queue szolgáltatás által felhasznált tárterület mérete bájtban kifejezve. | Nincsenek méretek |
| QueueCount | Várólista száma | Mennyiség | Átlag | A Storage-fiók Queue szolgáltatás várólistáinak száma. | Nincsenek méretek |
| QueueMessageCount | Üzenetsor-üzenetek száma | Mennyiség | Átlag | A tárolási fiók Queue szolgáltatásban lévő üzenetsor-üzenetek hozzávetőleges száma. | Nincsenek méretek |
| Tranzakciók | Tranzakciók | Mennyiség | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Belépő | Belépő | Bájt | Összes | A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodperc | Átlag | Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres E2E késés | Ezredmásodperc | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Elérhetőség | Elérhetőség | Százalék | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. | GeoType, ApiName |

## <a name="next-steps"></a>Következő lépések

További információ az [Azure monitor Azure stack hub](azure-stack-metrics-azure-data.md)-ról.
