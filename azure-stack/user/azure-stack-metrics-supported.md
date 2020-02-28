---
title: Azure Stack hub Azure Monitor támogatott mérőszámai
description: Az Azure Stack hub Azure Monitor támogatott metrikáinak megismerése.
author: mattbriggs
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: b90458e58054cd0f42564558fd3cb5a8226d6c5d
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702261"
---
# <a name="supported-metrics-for-azure-monitor-on-azure-stack-hub"></a>Azure Stack hub Azure Monitor támogatott mérőszámai

Az Azure monitor Azure Stack hub-on lévő mérőszámait ugyanúgy kell lekérni, mint a globális Azure-ban. A mértékek létrehozása a portálon, beszerezheti őket a REST API-ból, vagy lekérdezheti, ha a PowerShell vagy parancssori felület.

Az alábbi táblázatok felsorolják az Azure Stack hub Azure Monitor metrikai folyamatában elérhető metrikákat. A metrikák lekérdezéséhez és eléréséhez használja az API-profil **2018-01-01** API-Version verzióját. További információ az API-profilokról és az Azure Stack hub-ról: [az API-verziók profiljainak kezelése Azure stack központban](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Átlag | A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya. | Nincsenek méretek |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Használt kapacitás | Bájt | Átlag | A fiók felhasznált kapacitása. | Nincsenek méretek |
| Tranzakciók | Tranzakciók | Darabszám | Összesen | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Bejövő forgalom | Bejövő forgalom | Bájt | Összesen | A bejövő adatok (bájt) mennyisége. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összesen | A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag | Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag | A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag | A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|--------|---------------------|------|------------------|-------------|------------|
| BlobCapacity | Blob-kapacitása | Bájt | Összesen | A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve. | BlobType |
| BlobCount | Blobok száma | Darabszám | Összesen | A Storage-fiók Blob serviceban található Blobok száma. | BlobType |
| ContainerCount | Blobtárolók száma | Darabszám | Átlag | A Storage-fiók Blob service lévő tárolók száma. | Nincs dimenzió |
| Tranzakciók | Tranzakciók | Darabszám | Összesen | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Bejövő forgalom | Bejövő forgalom | Bájt | Összesen | A bejövő adatok (bájt) mennyisége. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összesen | A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag | Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag | A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag | A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Table Storage kapacitása | Bájt | Átlag | A Storage-fiók Table service által felhasznált tárterület mérete bájtban kifejezve. | Nincs dimenzió |
| TableCount | Táblák száma | Darabszám | Átlag | A Storage-fiók Table serviceban található táblák száma. | Nincs dimenzió |
| TableEntityCount | Táblaentitások száma | Darabszám | Átlag | A Storage-fiók Table serviceban szereplő táblák entitások száma. | Nincs dimenzió |
| Tranzakciók | Tranzakciók | Darabszám | Összesen | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Bejövő forgalom | Bejövő forgalom | Bájt | Összesen | A bejövő adatok (bájt) mennyisége. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összesen | A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag | Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag | A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag | A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Queue Storage kapacitása | Bájt | Átlag | A Storage-fiók Queue szolgáltatás által felhasznált tárterület mérete bájtban kifejezve. | Nincs dimenzió |
| QueueCount | Üzenetsorok száma | Darabszám | Átlag | A Storage-fiók Queue szolgáltatás várólistáinak száma. | Nincs dimenzió |
| QueueMessageCount | Üzenetsorbeli üzenetek száma | Darabszám | Átlag | A tárolási fiók Queue szolgáltatásban lévő üzenetsor-üzenetek hozzávetőleges száma. | Nincs dimenzió |
| Tranzakciók | Tranzakciók | Darabszám | Összesen | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Bejövő forgalom | Bejövő forgalom | Bájt | Összesen | A bejövő adatok (bájt) mennyisége. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összesen | A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag | Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag | A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag | A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. | GeoType, ApiName |

## <a name="next-steps"></a>Következő lépések

További információ az [Azure monitor Azure stack hub](azure-stack-metrics-azure-data.md)-ról.
