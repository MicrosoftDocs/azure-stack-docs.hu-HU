---
title: A használati API-val kapcsolatos gyakori kérdések | Microsoft Docs
description: Azure Stack mérőszámok listája, összehasonlítás az Azure használati API-val, a használati idő és a jelentett idő, hibakódok.
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
ms.date: 09/25/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 02/26/2019
ms.openlocfilehash: b4780077f015c060c63abc3abd33bd3e71c63e15
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019320"
---
# <a name="frequently-asked-questions-about-azure-stack-usage"></a>Gyakori kérdések a Azure Stack használatáról

Ez a cikk a Azure Stack használattal és a Azure Stack használati API-val kapcsolatos gyakori kérdésekre ad választ.

## <a name="what-meter-ids-can-i-see"></a>Milyen mérési azonosítókat láthatok?

A használatot a következő erőforrás-szolgáltatók jelentik:

### <a name="network"></a>Network (Hálózat)
  
**FOGYASZTÁSMÉRŐ azonosítója**: F271A8A388C44D93956A063E1D2FA80B  
**Fogyasztásmérő neve**: Statikus IP-címek használata  
**Egység**: IP-címek  
**Megjegyzések**: A használt IP-címek száma. Ha napi részletességgel hívja meg a használati API-t, a mérő az IP-címet adja vissza, szorozva az órák számával.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 9E2739BA86744796B465F64674B822BA  
**Fogyasztásmérő neve**: Dinamikus IP-címek használata  
**Egység**: IP-címek  
**Megjegyzések**: A használt IP-címek száma. Ha napi részletességgel hívja meg a használati API-t, a mérő az IP-címet adja vissza, szorozva az órák számával.  
  
### <a name="storage"></a>Storage
  
**FOGYASZTÁSMÉRŐ azonosítója**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Fogyasztásmérő neve**: TableCapacity  
**Egység**: GB @ no__t – 0hours  
**Megjegyzések**: A táblák által felhasznált teljes kapacitás.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Fogyasztásmérő neve**: PageBlobCapacity  
**Egység**: GB @ no__t – 0hours  
**Megjegyzések**: Az oldal Blobok által felhasznált teljes kapacitás.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Fogyasztásmérő neve**: QueueCapacity  
**Egység**: GB @ no__t – 0hours  
**Megjegyzések**: A várólista által felhasznált teljes kapacitás.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Fogyasztásmérő neve**: BlockBlobCapacity  
**Egység**: GB @ no__t – 0hours  
**Megjegyzések**: A blokk Blobok által felhasznált teljes kapacitás.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Fogyasztásmérő neve**: TableTransactions  
**Egység**: Kérelmek száma 10000-ben  
**Megjegyzések**: Table service kérelmek (10 10E).  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Fogyasztásmérő neve**: TableDataTransIn  
**Egység**: Bejövő adatforgalom GB-ban  
**Megjegyzések**: Table service a bejövő adatforgalom GB-ban.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Fogyasztásmérő neve**: TableDataTransOut  
**Egység**: Kimenő forgalom GB-ban  
**Megjegyzések**: Table service a kimenő adatforgalom GB-ban.
  
**FOGYASZTÁSMÉRŐ azonosítója**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Fogyasztásmérő neve**: BlobTransactions  
**Egység**: A kérelmek száma 10000-ben  
**Megjegyzések**: Blob service kérelmek (10 10E).  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Fogyasztásmérő neve**: BlobDataTransIn  
**Egység**: Bejövő adatforgalom GB-ban  
**Megjegyzések**: Blob service a bejövő adatforgalom GB-ban.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Fogyasztásmérő neve**: BlobDataTransOut  
**Egység**: Kimenő forgalom GB-ban  
**Megjegyzések**: Blob service a kimenő adatforgalom GB-ban.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Fogyasztásmérő neve**: QueueTransactions  
**Egység**: A kérelmek száma 10000-ben  
**Megjegyzések**: Queue szolgáltatás kérelmek (10 10E).  
  
**FOGYASZTÁSMÉRŐ azonosítója**: E518E809-E369-4A45-9274-2017B29FFF25  
**Fogyasztásmérő neve**: QueueDataTransIn  
**Egység**: Bejövő adatforgalom GB-ban  
**Megjegyzések**: Queue szolgáltatás a bejövő adatforgalom GB-ban.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Fogyasztásmérő neve**: QueueDataTransOut  
**Egység**: Kimenő forgalom GB-ban  
**Megjegyzések**: Adatforgalom Queue szolgáltatás GB-ban  

### <a name="compute"></a>Compute
  
**FOGYASZTÁSMÉRŐ azonosítója**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Fogyasztásmérő neve**: Alapszintű virtuális gép mérete (óra)  
**Egység**: Virtuális mag órája  
**Megjegyzések**: A virtuális magok száma a virtuális gép futási idejének szorzata.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Fogyasztásmérő neve**: Windows rendszerű virtuális gép mérete (óra)  
**Egység**: Virtuális mag órája  
**Megjegyzések**: A virtuális magok száma a virtuális gép által futtatott órákkal szorozva.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Fogyasztásmérő neve**: Virtuális gép mérete (óra)  
**Egység**: VIRTUÁLIS gépek órája  
**Megjegyzések**: Az alapszintű és a Windows rendszerű virtuális gépeket is rögzíti. A nem igazodik a magokhoz.  
  
### <a name="managed-disks"></a>Felügyelt lemezek

**FOGYASZTÁSMÉRŐ azonosítója**: 380874f9-300c-48e0-95a0-d2d9a21ade8f   
**Fogyasztásmérő neve**: S4   
**Egység**: Lemezek száma @ no__t-0month   
**Megjegyzések**: Standard szintű felügyelt lemez – 32 GB 

**FOGYASZTÁSMÉRŐ azonosítója**: 1b77d90f-427b-4435-b4f1-d78adec53222   
**Fogyasztásmérő neve**: S6   
**Egység**: Lemezek száma @ no__t-0month   
**Megjegyzések**: Standard szintű felügyelt lemez – 64 GB 

**FOGYASZTÁSMÉRŐ azonosítója**: d5f7731b-f639-404A-89d0-e46186e22c8d   
**Fogyasztásmérő neve**: S10   
**Egység**: Lemezek száma @ no__t-0month   
**Megjegyzések**: Standard szintű felügyelt lemez – 128 GB 

**FOGYASZTÁSMÉRŐ azonosítója**: ff85ef31-da5b-4eac-95dd-a69d6f97b18a   
**Fogyasztásmérő neve**: S15   
**Egység**: Lemezek száma @ no__t-0month   
**Megjegyzések**: Standard szintű felügyelt lemez – 256 GB 

**FOGYASZTÁSMÉRŐ azonosítója**: 88ea9228-457a-4091-adc9-ad5194f30b6e   
**Fogyasztásmérő neve**: S20   
**Egység**: Lemezek száma @ no__t-0month      
**Megjegyzések**: Standard szintű felügyelt lemez – 512 GB 

**FOGYASZTÁSMÉRŐ azonosítója**: 5b1db88a-8596-4002-8052-347947c26940   
**Fogyasztásmérő neve**: S30   
**Egység**: Lemezek száma @ no__t-0month   
**Megjegyzések**: Standard szintű felügyelt lemez – 1024 GB 

**FOGYASZTÁSMÉRŐ azonosítója**: 7660b45b-b29d-49cb-b816-59f30fbab011   
**Fogyasztásmérő neve**: P4   
**Egység**: Lemezek száma @ no__t-0month   
**Megjegyzések**: Prémium szintű felügyelt lemez – 32 GB 

**FOGYASZTÁSMÉRŐ azonosítója**: 817007fd-a077-477f-bc01-b876f27205fd   
**Fogyasztásmérő neve**: P6   
**Egység**: Lemezek száma @ no__t-0month   
**Megjegyzések**: Prémium szintű felügyelt lemez – 64 GB 

**FOGYASZTÁSMÉRŐ azonosítója**: e554b6bc-96cd-4938-a5b5-0da990278519   
**Fogyasztásmérő neve**: P10   
**Egység**: Lemezek száma @ no__t-0month   
**Megjegyzések**: Prémium szintű felügyelt lemez – 128 GB  

**FOGYASZTÁSMÉRŐ azonosítója**: cdc0f53a-62a9-4472-a06c-e99a23b02907   
**Fogyasztásmérő neve**: P15  
**Egység**: Lemezek száma @ no__t-0month   
**Megjegyzések**: Prémium szintű felügyelt lemez – 256 GB 

**Meter ID**: b9cb2d1a-84c2-4275-aa8b-70d2145d59aa   
**Fogyasztásmérő neve**: P20   
**Egység**: Lemezek száma @ no__t-0month   
**Megjegyzések**: Prémium szintű felügyelt lemez – 512 GB 

**FOGYASZTÁSMÉRŐ azonosítója**: 06bde724-9f94-43c0-84c3-d0fc54538369   
**Fogyasztásmérő neve**: P30   
**Egység**: Lemezek száma @ no__t-0month   
**Megjegyzések**: Prémium szintű felügyelt lemez – 1024 GB 

**FOGYASZTÁSMÉRŐ azonosítója**: 7ba084ec-ef9c-4d64-a179-7732c6cb5e28   
**Fogyasztásmérő neve**: ActualStandardDiskSize   
**Egység**: GB @ no__t – 0month      
**Megjegyzések**: A normál felügyelt lemez lemezének tényleges mérete  

**FOGYASZTÁSMÉRŐ azonosítója**: daef389a-06e5-4684-a7f7-8813d9f792d5  
**Fogyasztásmérő neve**: ActualPremiumDiskSize   
**Egység**: GB @ no__t – 0month      
**Megjegyzések**: A prémium szintű felügyelt lemez lemezének tényleges mérete 

**FOGYASZTÁSMÉRŐ azonosítója**: 108fa95b-be0d-4cd9-96e8-5b0d59505df1  
**Fogyasztásmérő neve**: ActualStandardSnapshotSize   
**Egység**: GB @ no__t – 0month   
**Megjegyzések**: A felügyelt normál pillanatkép lemezének tényleges mérete.  

**FOGYASZTÁSMÉRŐ azonosítója**: 578ae51d-4ef9-42f9-85ae-42b52d3d83ac   
**Fogyasztásmérő neve**: ActualPremiumSnapshotSize   
**Egység**: GB @ no__t – 0month   
**Megjegyzések**: A felügyelt prémium pillanatkép lemezének tényleges mérete.   

**FOGYASZTÁSMÉRŐ azonosítója**: 5d76e09f-4567-452a-94cc-7d1f097761f0   
**Fogyasztásmérő neve**: S4   
**Egység**: Lemezek száma @ no__t-0hours   
**Megjegyzések**: Standard szintű felügyelt lemez – 32 GB (elavult) 

**Meter ID**: dc9fc6a9-0782-432a-b8dc-978130457494   
**Fogyasztásmérő neve**: S6   
**Egység**: Lemezek száma @ no__t-0hours   
**Megjegyzések**: Standard szintű felügyelt lemez – 64 GB (elavult) 

**FOGYASZTÁSMÉRŐ azonosítója**: e5572fce-9f58-49d7-840c-b168c0f01fff   
**Fogyasztásmérő neve**: S10   
**Egység**: Lemezek száma @ no__t-0hours   
**Megjegyzések**: Standard szintű felügyelt lemez – 128 GB (elavult) 

**FOGYASZTÁSMÉRŐ azonosítója**: 9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**Fogyasztásmérő neve**: S15   
**Egység**: Lemezek száma @ no__t-0hours   
**Megjegyzések**: Standard szintű felügyelt lemez – 256 GB (elavult) 

**FOGYASZTÁSMÉRŐ azonosítója**: 5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**Fogyasztásmérő neve**: S20   
**Egység**: Lemezek száma @ no__t-0hours      
**Megjegyzések**: Standard szintű felügyelt lemez – 512 GB (elavult) 

**FOGYASZTÁSMÉRŐ azonosítója**: 7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**Fogyasztásmérő neve**: S30   
**Egység**: Lemezek száma @ no__t-0hours   
**Megjegyzések**: Standard szintű felügyelt lemez – 1024 GB (elavult) 

**FOGYASZTÁSMÉRŐ azonosítója**: 5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**Fogyasztásmérő neve**: P4   
**Egység**: Lemezek száma @ no__t-0hours   
**Megjegyzések**: Prémium szintű felügyelt lemez – 32 GB (elavult) 

**FOGYASZTÁSMÉRŐ azonosítója**: 518b412b-1927-4f25-985f-4aea24e55c4f   
**Fogyasztásmérő neve**: P6   
**Egység**: Lemezek száma @ no__t-0hours   
**Megjegyzések**: Prémium szintű felügyelt lemez – 64 GB (elavult) 

**FOGYASZTÁSMÉRŐ azonosítója**: 5cfb1fed-0902-49e3-8217-9add946fd624   
**Fogyasztásmérő neve**: P10   
**Egység**: Lemezek száma @ no__t-0hours   
**Megjegyzések**: Prémium szintű felügyelt lemez – 128 GB (elavult)  

**FOGYASZTÁSMÉRŐ azonosítója**: 8de91c94-f740-4d9a-b665-bd5974fa08d4   
**Fogyasztásmérő neve**: P15  
**Egység**: Lemezek száma @ no__t-0hours   
**Megjegyzések**: Prémium szintű felügyelt lemez – 256 GB (elavult) 

**FOGYASZTÁSMÉRŐ azonosítója**: c7e7839c-293b-4761-ae4c-848eda91130b   
**Fogyasztásmérő neve**: P20   
**Egység**: Lemezek száma @ no__t-0hours   
**Megjegyzések**: Prémium szintű felügyelt lemez – 512 GB (elavult) 

**FOGYASZTÁSMÉRŐ azonosítója**: 9f502103-adf4-4488-b494-456c95d23a9f   
**Fogyasztásmérő neve**: P30   
**Egység**: Lemezek száma @ no__t-0hours   
**Megjegyzések**: Prémium szintű felügyelt lemez – 1024 GB (elavult) 

**FOGYASZTÁSMÉRŐ azonosítója**: 8a409390-1913-40ae-917b-08d0f16f3c38   
**Fogyasztásmérő neve**: ActualStandardDiskSize   
**Egység**: Bájt @ no__t – 0hours      
**Megjegyzések**: A normál felügyelt lemez lemezének tényleges mérete (elavult)  

**FOGYASZTÁSMÉRŐ azonosítója**: 1273b16f-8458-4c34-8ce2-a515de551ef6  
**Fogyasztásmérő neve**: ActualPremiumDiskSize   
**Egység**: Bájt @ no__t – 0hours      
**Megjegyzések**: A prémium szintű felügyelt lemez lemezének tényleges mérete (elavult) 

**FOGYASZTÁSMÉRŐ azonosítója**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**Fogyasztásmérő neve**: ActualStandardSnapshotSize   
**Egység**: Bájt @ no__t – 0hours   
**Megjegyzések**: A felügyelt standard pillanatkép (elavult) lemezének tényleges mérete 

**FOGYASZTÁSMÉRŐ azonosítója**: 95b0c03f-8a82-4524-8961-ccfbf575f536   
**Fogyasztásmérő neve**: ActualPremiumSnapshotSize   
**Egység**: Bájt @ no__t – 0hours   
**Megjegyzések**: A felügyelt prémium pillanatkép lemezének tényleges mérete (elavult) 

**FOGYASZTÁSMÉRŐ azonosítója**: 75d4b707-1027-4403-9986-6ec7c05579c8- **fogyasztásmérő neve**: ActualStandardSnapshotSize **egység**: GB @ no__t – 0month **Megjegyzések**: A felügyelt standard pillanatkép (elavult) lemezének tényleges mérete  

**FOGYASZTÁSMÉRŐ azonosítója**: 5ca1cbb9-6f14-4e76-8be8-1ca91547965e- **fogyasztásmérő neve**: ActualPremiumSnapshotSize **egység**: GB @ no__t – 0month **Megjegyzések**: A felügyelt prémium pillanatkép lemezének tényleges mérete (elavult)  

### <a name="sql-rp"></a>SQL RP
  
**FOGYASZTÁSMÉRŐ azonosítója**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Fogyasztásmérő neve**: DatabaseSizeHourSqlMeter  
**Egység**: MB @ no__t – 0hours  
**Megjegyzések**: Az adatbázis teljes kapacitása a létrehozáskor. Ha napi részletességgel hívja meg a használati API-t, a mérő a MB-ot adja meg, szorozva az órák számával.  
  
### <a name="mysql-rp"></a>MySql RP   
  
**FOGYASZTÁSMÉRŐ azonosítója**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Fogyasztásmérő neve**: DatabaseSizeHourMySqlMeter  
**Egység**: MB @ no__t – 0hours  
**Megjegyzések**: Az adatbázis teljes kapacitása a létrehozáskor. Ha napi részletességgel hívja meg a használati API-t, a mérő a MB-ot adja meg, szorozva az órák számával.    
### <a name="key-vault"></a>Key Vault   
  
**FOGYASZTÁSMÉRŐ azonosítója**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Fogyasztásmérő neve**: Tranzakciók Key Vault  
**Egység**: Kérelmek száma 10000-ben  
**Megjegyzések**: Key Vault adatsíkon fogadott REST API kérelmek száma.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Fogyasztásmérő neve**: Speciális kulcsok tranzakciói  
**Egység**:  10K tranzakció  
**Megjegyzések**: RSA 3K/4K, ECC-kulcs tranzakciója. (előzetes verzió).  
  
### <a name="app-service"></a>App Service-ben   
  
**FOGYASZTÁSMÉRŐ azonosítója**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Fogyasztásmérő neve**: App Service  
**Egység**: Virtuális mag órája  
**Megjegyzések**: Az App Service futtatásához használt virtuális magok száma. Megjegyzés: A Microsoft ezt a mérőszámot használja a App Service Azure Stackre való feltöltéséhez. A felhőalapú megoldások szolgáltatói a többi App Service mérőszámot használhatják a bérlők használatának kiszámításához.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Fogyasztásmérő neve**: Függvények kérései  
**Egység**: 10 kérelem  
**Megjegyzések**: A kért végrehajtások teljes száma (10 végrehajtás esetén). A rendszer minden alkalommal megszámolja a végrehajtásokat, amikor egy függvény egy eseményre válaszol, vagy ha egy kötés aktiválódik.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Fogyasztásmérő neve**: Függvények – számítás  
**Egység**:  GB-s  
**Megjegyzések**:  Erőforrás-felhasználás (GB/s) mérve. A **megfigyelt erőforrás** -használat kiszámítása úgy történik, hogy a függvény végrehajtásához szükséges idő (ezredmásodpercben) szorozza meg az átlagos memória méretét GB-ban. A függvény által használt memóriát 128 MB-ig, a maximális memória mérete (1 536 MB) alapján mérjük, a végrehajtási időt pedig a legközelebbi 1 MS-ra kerekítve számítjuk ki. Egyetlen függvény végrehajtásának minimális végrehajtási ideje és memóriája 100 MS és 128 MB.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Fogyasztásmérő neve**: Megosztott App Service óra  
**Egység**: 1 óra  
**Megjegyzések**: A szegmens App Service terv óránkénti használata. A csomagok díjszabása alkalmazási alapon történik.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Fogyasztásmérő neve**: Ingyenes App Service óra  
**Egység**: 1 óra  
**Megjegyzések**: Óránkénti ingyenes App Service csomag használata. A csomagok díjszabása alkalmazási alapon történik.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Fogyasztásmérő neve**: Kis standard App Service óra  
**Egység**: 1 óra  
**Megjegyzések**: A példányok mérete és száma alapján számítható ki.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Fogyasztásmérő neve**: Közepes standard App Service óra  
**Egység**: 1 óra  
**Megjegyzések**: A példányok mérete és száma alapján számítható ki.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Fogyasztásmérő neve**: Nagyméretű standard App Service óra  
**Egység**: 1 óra  
**Megjegyzések**: A példányok mérete és száma alapján számítható ki.  
  
### <a name="custom-worker-tiers"></a>Egyéni feldolgozói szintek   
  
**FOGYASZTÁSMÉRŐ azonosítója**: *Egyéni feldolgozói szintek*  
**Fogyasztásmérő neve**: Egyéni feldolgozói szintek  
**Egység**: Óra  
**Megjegyzések**: Az determinisztikus mérőszám azonosítója az SKU és az egyéni feldolgozói rétegek neve alapján jön létre. Ez a mérőszám-azonosító minden egyéni feldolgozói szinten egyedi.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Fogyasztásmérő neve**: SNI SSL – SNI-célú SSL-tanúsítvány  
**Egység**: /SNI SSL kötés  
**Megjegyzések**: App Service két típusú SSL-kapcsolatot támogat: Kiszolgálónév jelzése (SNI) SSL és az IP-cím alapú SSL. Az SNI-alapú SSL a modern böngészőkben használható, az IP-alapú SSL mindegyikben.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Fogyasztásmérő neve**: IP SSL – IP-célú SSL-tanúsítvány  
**Egység**: /IP-alapú SSL-kötés  
**Megjegyzések**: App Service két típusú SSL-kapcsolatot támogat: Kiszolgálónév jelzése (SNI) SSL és az IP-cím alapú SSL. Az SNI-alapú SSL a modern böngészőkben használható, az IP-alapú SSL mindegyikben.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Fogyasztásmérő neve**:  Web Process (Webes folyamat)  
**Egység**:  
**Megjegyzések**: Számított aktív hely/óra.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Fogyasztásmérő neve**: Külső kimenő forgalom sávszélessége  
**Egység**: GB  
**Megjegyzések**: Összes bejövő kérelemre adott válasz bájtjai és a kimenő kérelmek teljes száma bájtban és a bejövő FTP-kérelmek teljes száma  
  
## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsdocsmicrosoftcomazurebillingbilling-usage-rate-card-overviewazure-resource-usage-api-preview-currently-in-public-preview"></a>Hogyan hasonlítja össze a Azure Stack használati API-kat az [Azure használati API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) -val (jelenleg nyilvános előzetes verzióban)?

* A bérlői használati API konzisztens az Azure API-val, egyetlen kivétellel: a *showDetails* jelző jelenleg nem támogatott a Azure stack.
* A szolgáltatói használati API csak Azure Stackre vonatkozik.
* Az Azure-ban elérhető [RATECARD API](/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview) jelenleg nem érhető el Azure Stackban.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Mi a különbség a használati idő és a jelentett idő között?

A használati adatok jelentéseinek két fő ideje van:

* **Jelentett idő**. Az az idő, amikor a használati esemény bekerült a használati rendszerébe
* **Használati idő**. Az Azure Stack erőforrás felhasználásának ideje

Előfordulhat, hogy a használati idő és a jelentett idő egy adott használati eseménynél eltérést jelez. A késleltetés akár több órát is igénybe vehet bármilyen környezetben.

Jelenleg csak a *jelentett idő*szerint lehet lekérdezni.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Mit jelentenek ezek a használati API-hibakódok?

| **HTTP-állapotkód** | **Hibakód:** | **Leírás** |
| --- | --- | --- |
| 400/hibás kérelem |*NoApiVersion* |Hiányzik az *API-Version* lekérdezési paraméter. |
| 400/hibás kérelem |*InvalidProperty* |Hiányzik egy tulajdonság, vagy az értéke érvénytelen. A válasz törzsében lévő hibakódban található üzenet azonosítja a hiányzó tulajdonságot. |
| 400/hibás kérelem |*RequestEndTimeIsInFuture* |A *ReportedEndTime* értéke jövőbeli. A jövőbeli értékek nem engedélyezettek ehhez az argumentumhoz. |
| 400/hibás kérelem |*SubscriberIdIsNotDirectTenant* |A szolgáltatói API-hívás olyan előfizetés-azonosítót használt, amely nem a hívó érvényes bérlője. |
| 400/hibás kérelem |*SubscriptionIdMissingInRequest* |A hívó előfizetés-azonosítója hiányzik. |
| 400/hibás kérelem |*InvalidAggregationGranularity* |A rendszer érvénytelen összesítési részletességet kért. Az érvényes értékek napi és óránkénti. |
| 503 |*ServiceUnavailable* |Újrapróbálkozást lehetővé tevő hiba történt, mert a szolgáltatás foglalt, vagy a hívás folyamatban van. |

## <a name="what-is-the-policy-for-charging-for-vms"></a>Mi a virtuális gépek díjszabására vonatkozó szabályzat?

A virtuális gépek futtatása és leállítása a használati adatokat eredményezi. Az Azure-val konzisztensen a használati adatok kibocsátásának leállításához le kell állítani a felszabadítást. Abban az esetben, ha a portál nem érhető el, de a számítási erőforrás-szolgáltató még fut, a használatot a rendszer kibocsátja.

## <a name="how-do-i-extract-usage-data-from-the-azure-stack-usage-apis"></a>Hogyan kinyerni a használati adatokat a Azure Stack használati API-ból?

A helyi használati API-k használati adatainak kinyerésének legegyszerűbb módja egy Azure Stack a [githubon a használati összefoglalás parancsfájl](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/Usagesummary.ps1)használatával. A parancsfájlhoz a kezdő és a záró dátumnak bemeneti paramétereknek kell lennie.

Azt is megteheti, hogy a REST API-kat használja a [szolgáltatói erőforrás-használati API](azure-stack-provider-resource-api.md) és a [bérlői erőforrás-használati API](azure-stack-tenant-resource-usage-api.md) cikkeiben leírtak szerint.

## <a name="how-can-i-associate-usage-extracted-from-azure-usage-apis-to-a-specific-azure-stack-user-subscription"></a>Hogyan rendelhetek hozzá az Azure használati API-kból kinyert használatot egy adott Azure Stack felhasználói előfizetéshez?

A használati rekordok közé tartozik a **AdditionalInfo**nevű tulajdonság, amely tartalmazza a Azure stack előfizetés-azonosítóját. Ez a felhasználói előfizetés, amely a megfelelő használati rekordot bocsátja ki.

## <a name="next-steps"></a>További lépések

* [Ügyfél számlázása és jóváírása Azure Stack](azure-stack-billing-and-chargeback.md)
* [Szolgáltatói erőforrás-használati API](azure-stack-provider-resource-api.md)
* [Bérlői erőforrás-használati API](azure-stack-tenant-resource-usage-api.md)
