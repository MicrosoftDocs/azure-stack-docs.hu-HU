---
title: Használati API kapcsolatos gyakori kérdések |} A Microsoft Docs
description: Azure Stack-mérőszámok, összehasonlító Azure usage API-t, a használati idő és a jelzett idő, hibakódok listáját.
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
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 02/26/2019
ms.openlocfilehash: 6a5ef529d2eabf8039be1da6c53da907c0b7aaaf
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2019
ms.locfileid: "66459041"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Gyakori kérdések az Azure Stack-használati API

Ebben a cikkben megválaszolunk néhány – gyakori kérdések az Azure Stack-használati API.

## <a name="what-meter-ids-can-i-see"></a>Milyen mérőszám azonosítók felszámított díj?

A következő erőforrás-szolgáltatók a jelentett használat:

### <a name="network"></a>Network (Hálózat)
  
**Mérőszám azonosítója**: F271A8A388C44D93956A063E1D2FA80B  
**Fogyasztásmérő neve**: Static IP Address Usage  
**Egység**: IP-címek  
**Megjegyzések**: Használt száma az IP-címek. Ha napi részletességgel API használatának felhívja, mérőszám órák száma szorozva IP-címet adja vissza.  
  
**Mérőszám azonosítója**: 9E2739BA86744796B465F64674B822BA  
**Fogyasztásmérő neve**: Dynamic IP Address Usage  
**Egység**: IP-címek  
**Megjegyzések**: Használt száma az IP-címek. Ha napi részletességgel API használatának felhívja, mérőszám órák száma szorozva IP-címet adja vissza.  
  
### <a name="storage"></a>Storage
  
**Mérőszám azonosítója**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Fogyasztásmérő neve**: TableCapacity  
**Egység**: GB-os\*óra  
**Megjegyzések**: Táblák által igénybe vett teljes kapacitás.  
  
**Mérőszám azonosítója**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Fogyasztásmérő neve**: PageBlobCapacity  
**Egység**: GB-os\*óra  
**Megjegyzések**: A lapblobok által igénybe vett teljes kapacitás.  
  
**Mérőszám azonosítója**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Fogyasztásmérő neve**: QueueCapacity  
**Egység**: GB-os\*óra  
**Megjegyzések**: Üzenetsor által igénybe vett teljes kapacitás.  
  
**Mérőszám azonosítója**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Fogyasztásmérő neve**: BlockBlobCapacity  
**Egység**: GB-os\*óra  
**Megjegyzések**: Teljes kapacitás használja fel a blokkblobok használatát támogatják.  
  
**Mérőszám azonosítója**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Fogyasztásmérő neve**: TableTransactions  
**Egység**: A 10 000's kérések száma  
**Megjegyzések**: A szolgáltatáskérések tábla (in 10,000s).  
  
**Mérőszám azonosítója**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Fogyasztásmérő neve**: TableDataTransIn  
**Egység**: Bejövő adatok GB-ban  
**Megjegyzések**: TABLE service beérkező adatok GB-ban.  
  
**Mérőszám azonosítója**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Fogyasztásmérő neve**: TableDataTransOut  
**Egység**: Kimenő GB-ban  
**Megjegyzések**: TABLE service kimenő adatforgalom GB-ban.
  
**Mérőszám azonosítója**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Fogyasztásmérő neve**: BlobTransactions  
**Egység**: 10., 000's kérelmek száma  
**Megjegyzések**: BLOB service-kérelmek (in 10,000s).  
  
**Mérőszám azonosítója**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Fogyasztásmérő neve**: BlobDataTransIn  
**Egység**: Bejövő adatok GB-ban  
**Megjegyzések**: A BLOB szolgáltatás beérkező adatok GB-ban.  
  
**Mérőszám azonosítója**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Fogyasztásmérő neve**: BlobDataTransOut  
**Egység**: Kimenő GB-ban  
**Megjegyzések**: A BLOB service a kimenő adatforgalom GB-ban.  
  
**Mérőszám azonosítója**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Fogyasztásmérő neve**: QueueTransactions  
**Egység**: 10., 000's kérelmek száma  
**Megjegyzések**: Queue szolgáltatás kérelmek száma (in 10,000s).  
  
**Mérőszám azonosítója**: E518E809-E369-4A45-9274-2017B29FFF25  
**Fogyasztásmérő neve**: QueueDataTransIn  
**Egység**: Bejövő adatok GB-ban  
**Megjegyzések**: Queue szolgáltatás beérkező adatok GB-ban.  
  
**Mérőszám azonosítója**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Fogyasztásmérő neve**: QueueDataTransOut  
**Egység**: Kimenő GB-ban  
**Megjegyzések**: Queue szolgáltatás régiós adatmozgás GB-ban  

### <a name="compute"></a>Compute
  
**Mérőszám azonosítója**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Fogyasztásmérő neve**: Alap VM-méret órák  
**Egység**: Virtuális processzor-üzemóra  
**Megjegyzések**: Virtuális Processzormagok száma szorozva az óra, a virtuális gépen.  
  
**Mérőszám azonosítója**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Fogyasztásmérő neve**: Windows virtuális gép mérete óra  
**Egység**: Virtuális processzor-üzemóra  
**Megjegyzések**: A virtuális magok számának szorzata óra, a virtuális gépen.  
  
**Mérőszám azonosítója**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Fogyasztásmérő neve**: Virtuális gép mérete óra  
**Egység**: VM-órák  
**Megjegyzések**: Mindkét rögzíti kiinduló és a Windows virtuális Gépet. Nem módosíthatja a magok.  
  
### <a name="managed-disks"></a>Felügyelt lemezek

**Mérőszám azonosítója**: 380874f9-300c-48e0-95a0-d2d9a21ade8f   
**Fogyasztásmérő neve**: S4   
**Egység**: A lemezek száma\*hónap   
**Megjegyzések**: Standard szintű felügyelt lemez – 32 GB 

**Mérőszám azonosítója**: 1b77d90f-427b-4435-b4f1-d78adec53222   
**Fogyasztásmérő neve**: S6   
**Egység**: A lemezek száma\*hónap   
**Megjegyzések**: Standard szintű felügyelt lemez – 64 GB-os 

**Mérőszám azonosítója**: d5f7731b-f639-404a-89d0-e46186e22c8d   
**Fogyasztásmérő neve**: S10   
**Egység**: A lemezek száma\*hónap   
**Megjegyzések**: Standard szintű felügyelt lemez – 128 GB-os 

**Meter ID**: ff85ef31-da5b-4eac-95dd-a69d6f97b18a   
**Fogyasztásmérő neve**: S15   
**Egység**: A lemezek száma\*hónap   
**Megjegyzések**: Standard szintű felügyelt lemez – 256 GB 

**Mérőszám azonosítója**: 88ea9228-457a-4091-adc9-ad5194f30b6e   
**Fogyasztásmérő neve**: S20   
**Egység**: A lemezek száma\*hónap      
**Megjegyzések**: Standard szintű felügyelt lemez – 512 GB 

**Mérőszám azonosítója**: 5b1db88a-8596-4002-8052-347947c26940   
**Fogyasztásmérő neve**: S30   
**Egység**: A lemezek száma\*hónap   
**Megjegyzések**: Standard szintű felügyelt lemez – 1024 GB 

**Mérőszám azonosítója**: 7660b45b-b29d-49cb-b816-59f30fbab011   
**Fogyasztásmérő neve**: P4   
**Egység**: A lemezek száma\*hónap   
**Megjegyzések**: Prémium szintű felügyelt lemez – 32 GB 

**Mérőszám azonosítója**: 817007fd-a077-477f-bc01-b876f27205fd   
**Fogyasztásmérő neve**: P6   
**Egység**: A lemezek száma\*hónap   
**Megjegyzések**: Prémium szintű felügyelt lemez – 64 GB-os 

**Meter ID**: e554b6bc-96cd-4938-a5b5-0da990278519   
**Fogyasztásmérő neve**: P10   
**Egység**: A lemezek száma\*hónap   
**Megjegyzések**: Prémium szintű felügyelt lemez – 128 GB-os  

**Meter ID**: cdc0f53a-62a9-4472-a06c-e99a23b02907   
**Fogyasztásmérő neve**: P15  
**Egység**: A lemezek száma\*hónap   
**Megjegyzések**: Prémium szintű felügyelt lemez – 256 GB 

**Meter ID**: b9cb2d1a-84c2-4275-aa8b-70d2145d59aa   
**Fogyasztásmérő neve**: P20   
**Egység**: A lemezek száma\*hónap   
**Megjegyzések**: Prémium szintű felügyelt lemez – 512 GB 

**Mérőszám azonosítója**: 06bde724-9f94-43c0-84c3-d0fc54538369   
**Fogyasztásmérő neve**: P30   
**Egység**: A lemezek száma\*hónap   
**Megjegyzések**: Prémium szintű felügyelt lemez – 1024 GB 

**Mérőszám azonosítója**: 7ba084ec-ef9c-4d64-a179-7732c6cb5e28   
**Fogyasztásmérő neve**: ActualStandardDiskSize   
**Egység**: GB-os\*hónap      
**Megjegyzések**: A standard szintű felügyelt lemez lemezen tényleges méret  

**Meter ID**: daef389a-06e5-4684-a7f7-8813d9f792d5  
**Fogyasztásmérő neve**: ActualPremiumDiskSize   
**Egység**: GB-os\*hónap      
**Megjegyzések**: A tényleges méret a lemez prémium szintű felügyelt lemez 

**Mérőszám azonosítója**: 108fa95b-be0d-4cd9-96e8-5b0d59505df1  
**Fogyasztásmérő neve**: ActualStandardSnapshotSize   
**Egység**: GB-os\*hónap   
**Megjegyzések**: A standard szintű felügyelt pillanatképek a lemez a tényleges méret.  

**Mérőszám azonosítója**: 578ae51d-4ef9-42f9-85ae-42b52d3d83ac   
**Fogyasztásmérő neve**: ActualPremiumSnapshotSize   
**Egység**: GB-os\*hónap   
**Megjegyzések**: A tényleges mérete a felügyelt prémium szintű pillanatképek lemezen.   

**Mérőszám azonosítója**: 5d76e09f-4567-452a-94cc-7d1f097761f0   
**Fogyasztásmérő neve**: S4   
**Egység**: A lemezek száma\*óra   
**Megjegyzések**: Standard szintű felügyelt lemez – 32 GB (elavult) 

**Meter ID**: dc9fc6a9-0782-432a-b8dc-978130457494   
**Fogyasztásmérő neve**: S6   
**Egység**: A lemezek száma\*óra   
**Megjegyzések**: Standard szintű felügyelt lemez – 64 GB-os (elavult) 

**Meter ID**: e5572fce-9f58-49d7-840c-b168c0f01fff   
**Fogyasztásmérő neve**: S10   
**Egység**: A lemezek száma\*óra   
**Megjegyzések**: Standard szintű felügyelt lemez – 128 GB-os (elavult) 

**Mérőszám azonosítója**: 9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**Fogyasztásmérő neve**: S15   
**Egység**: A lemezek száma\*óra   
**Megjegyzések**: Standard szintű felügyelt lemez – 256 GB (elavult) 

**Mérőszám azonosítója**: 5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**Fogyasztásmérő neve**: S20   
**Egység**: A lemezek száma\*óra      
**Megjegyzések**: Standard szintű felügyelt lemez – 512 GB (elavult) 

**Mérőszám azonosítója**: 7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**Fogyasztásmérő neve**: S30   
**Egység**: A lemezek száma\*óra   
**Megjegyzések**: Standard szintű felügyelt lemez – 1024 GB (elavult) 

**Mérőszám azonosítója**: 5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**Fogyasztásmérő neve**: P4   
**Egység**: A lemezek száma\*óra   
**Megjegyzések**: Prémium szintű felügyelt lemez – 32 GB (elavult) 

**Mérőszám azonosítója**: 518b412b-1927-4f25-985f-4aea24e55c4f   
**Fogyasztásmérő neve**: P6   
**Egység**: A lemezek száma\*óra   
**Megjegyzések**: Prémium szintű felügyelt lemez – 64 GB-os (elavult) 

**Mérőszám azonosítója**: 5cfb1fed-0902-49e3-8217-9add946fd624   
**Fogyasztásmérő neve**: P10   
**Egység**: A lemezek száma\*óra   
**Megjegyzések**: Prémium szintű felügyelt lemez – 128 GB-os (elavult)  

**Mérőszám azonosítója**: 8de91c94-f740-4d9a-b665-bd5974fa08d4   
**Fogyasztásmérő neve**: P15  
**Egység**: A lemezek száma\*óra   
**Megjegyzések**: Premium Managed Disk - 256 GB (Deprecated) 

**Mérőszám azonosítója**: c7e7839c-293b-4761-ae4c-848eda91130b   
**Fogyasztásmérő neve**: P20   
**Egység**: A lemezek száma\*óra   
**Megjegyzések**: Prémium szintű felügyelt lemez – 512 GB (elavult) 

**Mérőszám azonosítója**: 9f502103-adf4-4488-b494-456c95d23a9f   
**Fogyasztásmérő neve**: P30   
**Egység**: A lemezek száma\*óra   
**Megjegyzések**: Prémium szintű felügyelt lemez – 1024 GB (elavult) 

**Mérőszám azonosítója**: 8a409390-1913-40ae-917b-08d0f16f3c38   
**Fogyasztásmérő neve**: ActualStandardDiskSize   
**Egység**: Bájt\*óra      
**Megjegyzések**: Standard szintű felügyelt lemez (elavult) a lemezen a tényleges méret  

**Mérőszám azonosítója**: 1273b16f-8458-4c34-8ce2-a515de551ef6  
**Fogyasztásmérő neve**: ActualPremiumDiskSize   
**Egység**: Bájt\*óra      
**Megjegyzések**: A tényleges méret a lemez prémium szintű felügyelt lemez (elavult) 

**Mérőszám azonosítója**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**Fogyasztásmérő neve**: ActualStandardSnapshotSize   
**Egység**: Bájt\*óra   
**Megjegyzések**: A lemezen felügyelt standard pillanatkép (elavult) a tényleges méret 

**Mérőszám azonosítója**: 95b0c03f-8a82-4524-8961-ccfbf575f536   
**Fogyasztásmérő neve**: ActualPremiumSnapshotSize   
**Egység**: Bájt\*óra   
**Megjegyzések**: A tényleges mérete a lemezen felügyelt prémium szintű pillanatképek (elavult) 

**Mérőszám azonosítója**: 75d4b707-1027-4403-9986-6ec7c05579c8 **fogyasztásmérő neve**: ActualStandardSnapshotSize **egység**: GB-os\*hónap **megjegyzések**: A lemezen felügyelt standard pillanatkép (elavult) a tényleges méret  

**Mérőszám azonosítója**: 5ca1cbb9-6f14-4e76-8be8-1ca91547965e **fogyasztásmérő neve**: ActualPremiumSnapshotSize **egység**: GB-os\*hónap **megjegyzések**: A tényleges mérete a lemezen felügyelt prémium szintű pillanatképek (elavult)  

### <a name="sql-rp"></a>SQL-RP
  
**Mérőszám azonosítója**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Fogyasztásmérő neve**: DatabaseSizeHourSqlMeter  
**Egység**: MB\*óra  
**Megjegyzések**: Teljes adatbázis-kapacitás létrehozása. Napi részletességgel-használati API hívásakor, mérőszám MB, szorozva az órák számát adja vissza.  
  
### <a name="mysql-rp"></a>MySql RP   
  
**Mérőszám azonosítója**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Fogyasztásmérő neve**: DatabaseSizeHourMySqlMeter  
**Egység**: MB\*óra  
**Megjegyzések**: Teljes adatbázis-kapacitás létrehozása. Napi részletességgel-használati API hívásakor, mérőszám MB, szorozva az órák számát adja vissza.    
### <a name="key-vault"></a>Key Vault   
  
**Mérőszám azonosítója**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Fogyasztásmérő neve**: Key Vault-tranzakciókra  
**Egység**: A 10 000's kérések száma  
**Megjegyzések**: A Key Vault-adatsík által fogadott REST API-kérések száma.  
  
**Mérőszám azonosítója**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Fogyasztásmérő neve**: Speciális kulcsok tranzakciók  
**Egység**:  10e tranzakció  
**Megjegyzések**: 3-K vagy 4K, ECC RSA key tranzakciók. (előzetes verzió).  
  
### <a name="app-service"></a>App Service-ben   
  
**Mérőszám azonosítója**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Fogyasztásmérő neve**: App Service  
**Egység**: Virtuális processzor-üzemóra  
**Megjegyzések**: App Service-ben futtatásához használt virtuális magok számát. Megjegyzés: Microsoft Ez az érték használja az App Service az Azure Stacken díja szerint számítjuk fel. Felhőszolgáltatók segítségével az App Service (alább) mérőszámok használat számítása a bérlők.  
  
**Mérőszám azonosítója**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Fogyasztásmérő neve**: Functions-kérelmek  
**Egység**: 10 kérést  
**Megjegyzések**: (/ 10 végrehajtás) kért végrehajtások száma. Végrehajtásnak számít minden alkalommal, amikor egy függvényt egy esemény bekövetkeztekor futtat, illetve van egy kötés által kiváltott.  
  
**Mérőszám azonosítója**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Fogyasztásmérő neve**: Functions - számítás  
**Egység**:  GB-s  
**Megjegyzések**:  A gigabájtmásodpercben (GB/s) mért erőforrás-használat. **Erőforrás-használat megfigyelt** szorzata átlagos memória méretét GB-ban a ideje (MS), a függvény végrehajtásához szükséges. Függvény által felhasznált memória mérjük a végrehajtási időt a legközelebbi 128 MB, legfeljebb 1 536 MB maximális memória mérete legfeljebb kerekítési legfeljebb a legközelebbi 1 ms. A minimális végrehajtási idő és a egy adott függvény végrehajtása a memória, 100 ms és 128 mb jelölik.  
  
**Mérőszám azonosítója**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Fogyasztásmérő neve**: Megosztott App Service-órák  
**Egység**: 1 óra  
**Megjegyzések**: App Service-csomag szegmensek használat óra. Tarifacsomagok díjszabása.  
  
**Mérőszám azonosítója**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Fogyasztásmérő neve**: Ingyenes App Service-órák  
**Egység**: 1 óra  
**Megjegyzések**: / Óra használata ingyenes App Service-csomag. Tarifacsomagok díjszabása.  
  
**Mérőszám azonosítója**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Fogyasztásmérő neve**: Small standard szintű App Service-órák  
**Egység**: 1 óra  
**Megjegyzések**: Méret és a példányok száma alapján számítja ki.  
  
**Mérőszám azonosítója**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Fogyasztásmérő neve**: Közepes méretű standard szintű App Service-órák  
**Egység**: 1 óra  
**Megjegyzések**: Méret és a példányok száma alapján számítja ki.  
  
**Mérőszám azonosítója**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Fogyasztásmérő neve**: Nagy méretű standard szintű App Service-órák  
**Egység**: 1 óra  
**Megjegyzések**: Méret és a példányok száma alapján számítja ki.  
  
### <a name="custom-worker-tiers"></a>Egyéni feldolgozói rétegek   
  
**Mérőszám azonosítója**: *Egyéni feldolgozói rétegek*  
**Fogyasztásmérő neve**: Egyéni feldolgozói rétegek  
**Egység**: Óra  
**Megjegyzések**: Determinisztikus mérőszám azonosítója Termékváltozat és egyéni feldolgozói réteg neve alapján jön létre. Ez a mérőszám azonosítója egy egyedülálló megoldás minden egyes egyéni feldolgozói réteg.  
  
**Mérőszám azonosítója**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Fogyasztásmérő neve**: SNI SSL – SNI-célú SSL-tanúsítvány  
**Egység**: SNI SSL-kötés kiszolgálónként  
**Megjegyzések**: App Service kétféle SSL-kapcsolatot támogat: Kiszolgálónév jelzése (SNI) SSL és az IP-cím alapú SSL. Az SNI-alapú SSL a modern böngészőkben használható, az IP-alapú SSL mindegyikben.  
  
**Mérőszám azonosítója**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Fogyasztásmérő neve**: IP SSL – IP-célú SSL-tanúsítvány  
**Egység**: / IP-cím alapú SSL-kötés  
**Megjegyzések**: App Service kétféle SSL-kapcsolatot támogat: Kiszolgálónév jelzése (SNI) SSL és az IP-cím alapú SSL. Az SNI-alapú SSL a modern böngészőkben használható, az IP-alapú SSL mindegyikben.  
  
**Mérőszám azonosítója**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Fogyasztásmérő neve**:  Web Process (Webes folyamat)  
**Egység**:  
**Megjegyzések**: Aktív hely óránként kiszámítása alapján történik.  
  
**Mérőszám azonosítója**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Fogyasztásmérő neve**: Külső kimenő adatforgalmat  
**Egység**: GB  
**Megjegyzések**: Teljes bejövő kérelemre adott válasz bájtjai + kimenő kérelmek teljes bájt + a teljes bejövő FTP kérelem adott válasz bájtjai + teljes bejövő webes üzembe helyezése a kérelemre adott válasz bájtjai.  
  
## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsdocsmicrosoftcomazurebillingbilling-usage-rate-card-overviewazure-resource-usage-api-preview-currently-in-public-preview"></a>Az API-k hasonlítsa össze az Azure Stack használati módja a [Azure-használati API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) (jelenleg nyilvános előzetes verzióban)?

* A bérlői használati API összhangban az Azure API, egy kivétellel: a *showDetails* jelző jelenleg nem támogatott az Azure Stackben.
* A szolgáltatói használati API csak azokra az Azure Stack.
* Jelenleg a [RateCard API](/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview) , amely elérhető az Azure-ban nem érhető el az Azure Stackben.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Mi a különbség a használati és jelentett idő között?

Használati jelentések két fő időértékek rendelkezik:

* **Jelentett idő**. Ha a használati esemény a használati rendszer megadott idő
* **Használati idő**. Az idő, amikor az Azure Stack erőforrás használatának

Megjelenhet értékeket eltérést használat ideje, és jelentett idő egy adott használati esemény. A késleltetés mindaddig bármilyen környezetben több órát is lehet.

Jelenleg, lekérdezheti, ha kizárólag a *jelentett idő*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Mit jelentenek a használati API hibakódok?

| **HTTP-állapotkód:** | **Hibakód:** | **Leírás** |
| --- | --- | --- |
| / 400 – Hibás kérelem |*NoApiVersion* |A *api-version* lekérdezési paraméter hiányzik. |
| / 400 – Hibás kérelem |*InvalidProperty* |Tulajdonság hiányzik vagy érvénytelen értékkel rendelkezik. Az üzenet a válasz törzsében hibakód azonosítja a hiányzó tulajdonság. |
| / 400 – Hibás kérelem |*RequestEndTimeIsInFuture* |Az érték *ReportedEndTime* a jövőben van. Értékek a későbbiekben nem engedélyezettek ehhez az argumentumhoz. |
| / 400 – Hibás kérelem |*SubscriberIdIsNotDirectTenant* |A szolgáltató API-hívás egy előfizetés-azonosító, amely nem egy érvényes bérlő a hívó használatban van. |
| / 400 – Hibás kérelem |*SubscriptionIdMissingInRequest* |A hívó az előfizetés-azonosító nincs megadva. |
| / 400 – Hibás kérelem |*InvalidAggregationGranularity* |A kért egy érvénytelen összesítési pontossággal. Érvényes értékek: naponta, és óránként. |
| 503 |*ServiceUnavailable* |Egy újrapróbálkozást lehetővé tevő hiba történt, mert a szolgáltatás foglalt, vagy a hívás szabályozás alatt áll. |

## <a name="what-is-the-policy-for-charging-for-vms"></a>Mi az a virtuális gépek díjszabási szabályzat?

Futó vagy leállított virtuális gépeket hozhat létre a használati adatokat. Egységes Azure-ral, felszabadítási van szükség a használati adatok kibocsátási leállítása. A helyzet, amelyben a portálon nem érhető el, de a számítási erőforrás-szolgáltató továbbra is fut, a használati fog rendelkezésre.

## <a name="next-steps"></a>További lépések

* [A számlázás a vásárlók és a költséghelyi elszámolás az Azure Stackben](azure-stack-billing-and-chargeback.md)
* [Szolgáltatói erőforrás-használati API](azure-stack-provider-resource-api.md)
* [Bérlői erőforrás-használati API](azure-stack-tenant-resource-usage-api.md)
