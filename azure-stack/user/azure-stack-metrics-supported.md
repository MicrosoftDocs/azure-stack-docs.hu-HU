---
title: Azure Stack Azure Monitor támogatott mérőszámai | Microsoft Docs
description: A Azure Stack Azure Monitor támogatott metrikáinak megismerése.
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
ms.openlocfilehash: c3824b80ddd6d980b408bfdb2a2c4a55f599e6d0
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955685"
---
# <a name="supported-metrics-for-azure-monitor-on-azure-stack"></a>A Azure Stack Azure Monitor támogatott mérőszámai

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Az Azure monitor Azure Stackról származó mérőszámai ugyanúgy olvashatók be, mint a globális Azure-ban. Létrehozhat mértékeket a portálon, beolvashatja őket a REST API, vagy lekérdezheti őket a PowerShell vagy a parancssori felület használatával.

Az alábbi táblázatok a Azure Stack Azure Monitor metrikai folyamatában elérhető metrikákat sorolja fel. A metrikák lekérdezéséhez és eléréséhez használja az API-profil **2018-01-01** API-Version verzióját. További információ az API-profilokról és a Azure Stackekről: [az API-verziók profiljainak kezelése a Azure Stackban](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Metrika | Metrika megjelenítendő neve | Unit (Egység) | Összesítés típusa | Leírás | Dimenziók |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Átlag | A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya. | Nincsenek méretek |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Metrika | Metrika megjelenítendő neve | Unit (Egység) | Összesítés típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Felhasznált kapacitás | Bájt | Átlag | A fiók felhasznált kapacitása. | Nincsenek méretek |
| Tranzakciók | Tranzakciók | Mennyiség | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Belépő | Belépő | Bájt | Összes | A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodpercben | Átlag | Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres E2E késés | Ezredmásodpercben | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Elérhetőség | Elérhetőség | Százalék | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

| Metrika | Metrika megjelenítendő neve | Unit (Egység) | Összesítés típusa | Leírás | Dimenziók |
|--------|---------------------|------|------------------|-------------|------------|
| BlobCapacity | BLOB kapacitása | Bájt | Összes | A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve. | BlobType |
| BlobCount | Blobok száma | Mennyiség | Összes | A Storage-fiók Blob serviceban található Blobok száma. | BlobType |
| ContainerCount | BLOB-tárolók száma | Mennyiség | Átlag | A Storage-fiók Blob service lévő tárolók száma. | Nincsenek méretek |
| Tranzakciók | Tranzakciók | Mennyiség | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Belépő | Belépő | Bájt | Összes | A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodpercben | Átlag | Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres E2E késés | Ezredmásodpercben | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Elérhetőség | Elérhetőség | Százalék | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

| Metrika | Metrika megjelenítendő neve | Unit (Egység) | Összesítés típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Tábla kapacitása | Bájt | Átlag | A Storage-fiók Table service által felhasznált tárterület mérete bájtban kifejezve. | Nincsenek méretek |
| TableCount | Táblák száma | Mennyiség | Átlag | A Storage-fiók Table serviceban található táblák száma. | Nincsenek méretek |
| TableEntityCount | Tábla entitások száma | Mennyiség | Átlag | A Storage-fiók Table serviceban szereplő táblák entitások száma. | Nincsenek méretek |
| Tranzakciók | Tranzakciók | Mennyiség | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Belépő | Belépő | Bájt | Összes | A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodpercben | Átlag | Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres E2E késés | Ezredmásodpercben | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Elérhetőség | Elérhetőség | Százalék | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

| Metrika | Metrika megjelenítendő neve | Unit (Egység) | Összesítés típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Várólista kapacitása | Bájt | Átlag | A Storage-fiók Queue szolgáltatás által felhasznált tárterület mérete bájtban kifejezve. | Nincsenek méretek |
| QueueCount | Várólista száma | Mennyiség | Átlag | A Storage-fiók Queue szolgáltatás várólistáinak száma. | Nincsenek méretek |
| QueueMessageCount | Üzenetsor-üzenetek száma | Mennyiség | Átlag | A tárolási fiók Queue szolgáltatásban lévő üzenetsor-üzenetek hozzávetőleges száma. | Nincsenek méretek |
| Tranzakciók | Tranzakciók | Mennyiség | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Belépő | Belépő | Bájt | Összes | A bejövő adatforgalom mennyisége bájtban kifejezve. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatforgalom mennyisége bájtban kifejezve. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikerességi kiszolgáló késése | Ezredmásodpercben | Átlag | Az Azure Storage által a sikeres kérések feldolgozásához használt átlagos késés ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres E2E késés | Ezredmásodpercben | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet sikeres kéréseinek átlagos végpontok közötti késése ezredmásodpercben. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Elérhetőség | Elérhetőség | Százalék | Átlag | A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. | GeoType, ApiName |

## <a name="next-steps"></a>Következő lépések

További információ az [Azure monitorról Azure Stackról](azure-stack-metrics-azure-data.md).
