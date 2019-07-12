---
title: Támogatott mérőszámok az Azure Monitor az Azure Stackben |} A Microsoft Docs
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
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: 442fc6080f9b0aba87e0141257f79cdf910e0a41
ms.sourcegitcommit: b36d078e699c7924624b79641dbe9021af9606ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67816210"
---
# <a name="supported-metrics-for-azure-monitor-on-azure-stack"></a>A támogatott mérőszámok az Azure Monitor az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek*

Az Azure-ból metrikák figyelése az Azure Stacken blobnevet ugyanolyan módon, mivel ezek a globális Azure-ban. A mértékek létrehozása a portálon, beszerezheti őket a REST API-ból, vagy lekérdezheti, ha a PowerShell vagy parancssori felület.

Az alábbi táblázatok sorolják fel a metrikák elérhető az Azure Monitor metrika folyamatot az Azure Stacken. Lekérdezés, és hozzáférhetnek ezekhez a metrikákhoz, használja a **2018-01-01** api-version verzióját az API-profil. API-profilok és az Azure Stack kapcsolatos további információkért lásd: [kezelése API-verzióprofilok az Azure Stackben](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Százalékos processzorhasználat | Százalékos processzorhasználat | Százalék | Average | Jelenleg a virtuális gép által használt lefoglalt számítási egységek százalékos értéke. | Nincs dimenzió |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Használt kapacitás | Bájt | Average | A fiók felhasznált kapacitása. | Nincs dimenzió |
| Tranzakciók | Tranzakciók | Darabszám | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérések és hibák kéréseket is tartalmaz. Különböző típusú válaszok számának használja a ResponseType dimenziót. | ResponseType, GeoType, ApiName |
| Bejövő forgalom | Bejövő forgalom | Bájt | Összes | A bejövő adatok (bájt) mennyisége. Ez a szám bejövő adatait egy külső ügyfél Azure Storage és Azure-on belüli is tartalmaz. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatok (bájt) mennyisége. Ez a szám kimenő egy külső ügyfél Azure Storage és Azure-on belüli kimenő forgalom is tartalmaz. Ennek eredményeképpen Ez a szám nem tükrözi a számlázható kimenő forgalom. | GeoType, ApiName |
| SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag | Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a megadott AverageE2ELatency hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag | A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag | A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állás kiszámítása a TotalBillableRequests értékét és elosztjuk, többek között ezeket a kérelmeket, váratlan hibára, vonatkozó kérelmek száma alapján. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| BlobCapacity | Blob-kapacitása | Bájt | Összes | A tárfiók Blob service bájtban által felhasznált tárterület mennyisége. | BlobType |
| BlobCount | Blobok száma | Darabszám | Összes | A tárfiók Blob service-ben blobok száma. | BlobType |
| ContainerCount | Blobtárolók száma | Darabszám | Average | A tárfiók Blob service-ben tárolók száma. | Nincs dimenzió |
| Tranzakciók | Tranzakciók | Darabszám | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérések és hibák kéréseket is tartalmaz. Különböző típusú válaszok számának használja a ResponseType dimenziót. | ResponseType, GeoType, ApiName |
| Bejövő forgalom | Bejövő forgalom | Bájt | Összes | A bejövő adatok (bájt) mennyisége. Ez a szám bejövő adatait egy külső ügyfél Azure Storage és Azure-on belüli is tartalmaz. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatok (bájt) mennyisége. Ez a szám kimenő egy külső ügyfél Azure Storage és Azure-on belüli kimenő forgalom is tartalmaz. Ennek eredményeképpen Ez a szám nem tükrözi a számlázható kimenő forgalom. | GeoType, ApiName |
| SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag | Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a megadott AverageE2ELatency hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag | A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag | A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állás kiszámítása a TotalBillableRequests értékét és elosztjuk, többek között ezeket a kérelmeket, váratlan hibára, vonatkozó kérelmek száma alapján. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Table Storage kapacitása | Bájt | Average | A tárfiók Table service bájtban által felhasznált tárterület mennyisége. | Nincs dimenzió |
| TableCount | Táblák száma | Darabszám | Average | A tárfiók Table service-ben a táblák száma. | Nincs dimenzió |
| TableEntityCount | Táblaentitások száma | Darabszám | Average | A tárfiók Table Storage-szolgáltatás található táblaentitások száma. | Nincs dimenzió |
| Tranzakciók | Tranzakciók | Darabszám | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérések és hibák kéréseket is tartalmaz. Különböző típusú válaszok számának használja a ResponseType dimenziót. | ResponseType, GeoType, ApiName |
| Bejövő forgalom | Bejövő forgalom | Bájt | Összes | A bejövő adatok (bájt) mennyisége. Ez a szám bejövő adatait egy külső ügyfél Azure Storage és Azure-on belüli is tartalmaz. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatok (bájt) mennyisége. Ez a szám kimenő egy külső ügyfél Azure Storage és Azure-on belüli kimenő forgalom is tartalmaz. Ennek eredményeképpen Ez a szám nem tükrözi a számlázható kimenő forgalom. | GeoType, ApiName |
| SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag | Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a megadott AverageE2ELatency hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag | A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag | A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állás kiszámítása a TotalBillableRequests értékét és elosztjuk, többek között ezeket a kérelmeket, váratlan hibára, vonatkozó kérelmek száma alapján. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| Metrika | Metrika megjelenített neve | Unit (Egység) | Aggregáció típusa | Leírás | Dimenziók |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Queue Storage kapacitása | Bájt | Average | A tárfiók Queue-szolgáltatás (bájt) által felhasznált tárterület mennyisége. | Nincs dimenzió |
| QueueCount | Üzenetsorok száma | Darabszám | Average | A tárfiók Queue szolgáltatás az üzenetsorok száma. | Nincs dimenzió |
| QueueMessageCount | Üzenetsorbeli üzenetek száma | Darabszám | Average | A tárfiók Queue szolgáltatás üzenetsorbeli üzenetek hozzávetőleges száma. | Nincs dimenzió |
| Tranzakciók | Tranzakciók | Darabszám | Összes | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez a szám a sikeres és sikertelen kérések és hibák kéréseket is tartalmaz. Különböző típusú válaszok számának használja a ResponseType dimenziót. | ResponseType, GeoType, ApiName |
| Bejövő forgalom | Bejövő forgalom | Bájt | Összes | A bejövő adatok (bájt) mennyisége. Ez a szám bejövő adatait egy külső ügyfél Azure Storage és Azure-on belüli is tartalmaz. | GeoType, ApiName |
| Kimenő forgalom | Kimenő forgalom | Bájt | Összes | A kimenő adatok (bájt) mennyisége. Ez a szám kimenő egy külső ügyfél Azure Storage és Azure-on belüli kimenő forgalom is tartalmaz. Ennek eredményeképpen Ez a szám nem tükrözi a számlázható kimenő forgalom. | GeoType, ApiName |
| SuccessServerLatency | Sikeres kiszolgálói kérések késése | Ezredmásodperc | Átlag | Az átlagos várakozási használt Azure Storage által feldolgozott sikeres kérések, ezredmásodpercben. Ez az érték nem tartalmazza a megadott AverageE2ELatency hálózati késést. | GeoType, ApiName |
| SuccessE2ELatency | Sikeres kérések végpontok közötti késése | Ezredmásodperc | Átlag | A társzolgáltatásnak vagy a megadott API-művelet (MS) számára elküldött sikeres kérések átlagos végpontok közötti késését. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. | GeoType, ApiName |
| Rendelkezésre állás | Rendelkezésre állás | Százalék | Átlag | A társzolgáltatás vagy a megadott API-művelet rendelkezésre állási százaléka. Rendelkezésre állás kiszámítása a TotalBillableRequests értékét és elosztjuk, többek között ezeket a kérelmeket, váratlan hibára, vonatkozó kérelmek száma alapján. Minden nem várt hiba a társzolgáltatás vagy a megadott API-művelet romlik a rendelkezésre állás eredményez. | GeoType, ApiName |

## <a name="next-steps"></a>További lépések

Tudjon meg többet [Azure monitorozása az Azure Stacken](azure-stack-metrics-azure-data.md).
