---
title: Azure Stack Azure Monitor támogatott mérőszámai | Microsoft Docs
description: További információk a támogatott mérőszámok az Azure Monitor az Azure Stacken.
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
ms.date: 07/23/2019
ms.author: mabrigg
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: a66b6fce646a591efac17a5b6e4ed804dba211e7
ms.sourcegitcommit: bf4d265a3522cbfdd9dd295a0f4ad0daf2ed5eca
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68692145"
---
# <a name="supported-metrics-for-azure-monitor-on-azure-stack"></a>A Azure Stack Azure Monitor támogatott mérőszámai

*Vonatkozik: Integrált rendszerek Azure Stack*

Az Azure monitor Azure Stackról származó mérőszámai ugyanúgy olvashatók be, mint a globális Azure-ban. A mértékek létrehozása a portálon, beszerezheti őket a REST API-ból, vagy lekérdezheti, ha a PowerShell vagy parancssori felület.

Az alábbi táblázatok a Azure Stack Azure Monitor metrikai folyamatában elérhető metrikákat sorolja fel. A metrikák lekérdezéséhez és eléréséhez használja az API-profil **2018-01-01** API-Version verzióját. API-profilok és az Azure Stack kapcsolatos további információkért lásd: [kezelése API-verzióprofilok az Azure Stackben](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Average | A virtuális gép (ek) által jelenleg használt lefoglalt számítási egységek százalékos aránya. | Nincsenek méretek |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Használt kapacitás | Bájt | Average | A fiók felhasznált kapacitása. | Nincsenek méretek |
| Tranzakciók | Tranzakciók | Darabszám | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Bejövő forgalom | Bejövő forgalom | Bájt | Összes | A bejövő adatok (bájt) mennyisége. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag | Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag | A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag | A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|--------|---------------------|------|------------------|-------------|------------|
| BlobCapacity | Blob-kapacitása | Bájt | Összes | A Storage-fiók Blob service által felhasznált tárterület mérete bájtban kifejezve. | BlobType |
| BlobCount | Blobok száma | Darabszám | Összes | A Storage-fiók Blob serviceban található Blobok száma. | BlobType |
| ContainerCount | Blobtárolók száma | Darabszám | Average | A Storage-fiók Blob service lévő tárolók száma. | Nincs dimenzió |
| Tranzakciók | Tranzakciók | Darabszám | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Bejövő forgalom | Bejövő forgalom | Bájt | Összes | A bejövő adatok (bájt) mennyisége. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag | Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag | A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag | A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Table Storage kapacitása | Bájt | Average | A Storage-fiók Table service által felhasznált tárterület mérete bájtban kifejezve. | Nincs dimenzió |
| TableCount | Táblák száma | Darabszám | Average | A Storage-fiók Table serviceban található táblák száma. | Nincs dimenzió |
| TableEntityCount | Táblaentitások száma | Darabszám | Average | A Storage-fiók Table serviceban szereplő táblák entitások száma. | Nincs dimenzió |
| Tranzakciók | Tranzakciók | Darabszám | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Bejövő forgalom | Bejövő forgalom | Bájt | Összes | A bejövő adatok (bájt) mennyisége. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag | Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag | A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag | A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Queue Storage kapacitása | Bájt | Average | A Storage-fiók Queue szolgáltatás által felhasznált tárterület mérete bájtban kifejezve. | Nincs dimenzió |
| QueueCount | Üzenetsorok száma | Darabszám | Average | A Storage-fiók Queue szolgáltatás várólistáinak száma. | Nincs dimenzió |
| QueueMessageCount | Üzenetsorbeli üzenetek száma | Darabszám | Average | A tárolási fiók Queue szolgáltatásban lévő üzenetsor-üzenetek hozzávetőleges száma. | Nincs dimenzió |
| Tranzakciók | Tranzakciók | Darabszám | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám sikeres és sikertelen kérelmeket tartalmaz, valamint a létrehozott hibákat is. Használjon ResponseType dimenziót a különböző típusú válaszok számához. | ResponseType, GeoType, ApiName |
| Bejövő forgalom | Bejövő forgalom | Bájt | Összes | A bejövő adatok (bájt) mennyisége. Ez a szám magában foglalja a külső ügyfél és az Azure Storage közötti bejövő forgalmat, valamint az Azure-on belüli beáramlást is. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatok (bájt) mennyisége. Ez a szám a külső ügyfél Azure Storage-ba való bejutását, valamint az Azure-ban való kijutást is magában foglalja. Ennek eredményeképpen ez a szám nem tükrözi a számlázható kimenő forgalmat. | GeoType, ApiName |
| SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag | Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a AverageE2ELatency megadott hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag | A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag | A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Kiszámíthatja a rendelkezésre állást a TotalBillableRequests érték kiszámításával és a vonatkozó kérések számával, beleértve a váratlan hibákat okozó kérelmeket. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez. | GeoType, ApiName |

## <a name="next-steps"></a>További lépések

Tudjon meg többet [Azure monitorozása az Azure Stacken](azure-stack-metrics-azure-data.md).
