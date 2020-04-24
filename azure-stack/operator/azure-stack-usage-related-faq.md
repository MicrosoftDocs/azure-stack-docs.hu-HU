---
title: Használati API – gyakori kérdések
titleSuffix: Azure Stack Hub
description: A Azure Stack hub-használattal kapcsolatos gyakori kérdések listája, beleértve a mérőszámokat, az Azure használati API-hoz való összehasonlítást, a használati időt vs a jelentett időt és a hibakódokat.
services: azure-stack
documentationcenter: ''
author: sethmanheim
ms.topic: article
ms.date: 01/27/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 02/26/2019
ms.openlocfilehash: 9f7fbb8657ec13aa6c7768e1db4d62f36fbb46cb
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77696447"
---
# <a name="frequently-asked-questions-about-azure-stack-hub-usage"></a>Azure Stack hub-használattal kapcsolatos gyakori kérdések

Ez a cikk az Azure Stack hub-használattal és a Azure Stack hub használati API-val kapcsolatos gyakori kérdésekre ad választ.

## <a name="what-meter-ids-can-i-see"></a>Milyen mérési azonosítókat láthatok?

A használatot a következő erőforrás-szolgáltatók jelentik:

### <a name="network"></a>Network (Hálózat)
  
**FOGYASZTÁSMÉRŐ azonosítója**: F271A8A388C44D93956A063E1D2FA80B  
**Fogyasztásmérő neve**: statikus IP-cím használata  
**Egység**: IP-címek  
**Megjegyzések**: a használt IP-címek száma. Ha napi részletességgel hívja meg a használati API-t, a mérő az IP-címet adja vissza, szorozva az órák számával.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 9E2739BA86744796B465F64674B822BA  
**Fogyasztásmérő neve**: dinamikus IP-cím használata  
**Egység**: IP-címek  
**Megjegyzések**: a használt IP-címek száma. Ha napi részletességgel hívja meg a használati API-t, a mérő az IP-címet adja vissza, szorozva az órák számával.  
  
### <a name="storage"></a>Storage
  
**FOGYASZTÁSMÉRŐ azonosítója**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Fogyasztásmérő neve**: TableCapacity  
**Egység**: GB\*óra  
**Megjegyzések**: a táblák által felhasznált teljes kapacitás.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Fogyasztásmérő neve**: PageBlobCapacity  
**Egység**: GB\*óra  
**Megjegyzések**: az oldal Blobok által felhasznált teljes kapacitás.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Fogyasztásmérő neve**: QueueCapacity  
**Egység**: GB\*óra  
**Megjegyzések**: a várólista által felhasznált teljes kapacitás.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Fogyasztásmérő neve**: BlockBlobCapacity  
**Egység**: GB\*óra  
**Megjegyzések**: a blokk Blobok által felhasznált teljes kapacitás.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Fogyasztásmérő neve**: TableTransactions  
**Egység**: a kérelmek száma 10, 10E  
**Megjegyzések**: table Service kérelmek (10, 10E).  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Fogyasztásmérő neve**: TableDataTransIn  
**Egység**: a bejövő adatforgalom GB-ban  
**Megjegyzések**: table Service a bejövő adatforgalom GB-ban.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Fogyasztásmérő neve**: TableDataTransOut  
**Egység**: kimenő forgalom GB-ban  
**Megjegyzések**: table Service a kimenő adatforgalom GB-ban.
  
**FOGYASZTÁSMÉRŐ azonosítója**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Fogyasztásmérő neve**: BlobTransactions  
**Egység**: a kérelmek száma 10, 10E  
**Megjegyzések**: blob Service kérelmek (10, 10E).  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Fogyasztásmérő neve**: BlobDataTransIn  
**Egység**: a bejövő adatforgalom GB-ban  
**Megjegyzések**: blob Service a bejövő adatforgalom GB-ban.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Fogyasztásmérő neve**: BlobDataTransOut  
**Egység**: kimenő forgalom GB-ban  
**Megjegyzések**: blob Service a kimenő adatforgalom GB-ban.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Fogyasztásmérő neve**: QueueTransactions  
**Egység**: a kérelmek száma 10, 10E  
**Megjegyzések**: Queue szolgáltatás kérelmek (10, 10E).  
  
**FOGYASZTÁSMÉRŐ azonosítója**: E518E809-E369-4A45-9274-2017B29FFF25  
**Fogyasztásmérő neve**: QueueDataTransIn  
**Egység**: a bejövő adatforgalom GB-ban  
**Megjegyzések**: Queue szolgáltatás a bejövő adatforgalom GB-ban.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Fogyasztásmérő neve**: QueueDataTransOut  
**Egység**: kimenő forgalom GB-ban  
**Megjegyzések**: Queue szolgáltatás a kimenő adatforgalom GB-ban.

### <a name="compute"></a>Compute
  
**FOGYASZTÁSMÉRŐ azonosítója**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Fogyasztásmérő neve**: alapszintű virtuális gép mérete (óra)  
**Egység**: virtuális mag órája  
**Megjegyzések**: a virtuális magok száma a virtuális gép futási idejének szorzata.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Fogyasztásmérő neve**: WINDOWSOS virtuális gép mérete (óra)  
**Egység**: virtuális mag órája  
**Megjegyzések**: a virtuális magok száma a virtuális gép által futtatott órákkal szorozva.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Fogyasztásmérő neve**: virtuális gép mérete (óra)  
**Egység**: virtuális gép órája  
**Megjegyzések**: az alapszintű és a Windows RENDSZERű virtuális gépeket is rögzíti. Nem igazodik a magokhoz.  
  
### <a name="managed-disks"></a>Felügyelt lemezek

**MÉRŐSZÁM azonosítója**: 380874F9-300c-48e0-95a0-d2d9a21ade8f **-fogyasztásmérő neve**: S4 **egység**: lemezek\*száma hónap **Megjegyzés**: standard szintű felügyelt lemez – 32 GB

Mérőszám **azonosítója**: 1b77d90f-427b-4435-b4f1-d78adec53222 mérőszám **neve**: S6 **egység**: a lemezek\*száma hónap **megjegyzései**: standard szintű felügyelt lemez – 64 GB

Mérőszám **azonosítója**: D5f7731b-f639-404A-89d0-e46186e22c8d **-fogyasztásmérő neve**: S10- **egység**: a\*lemezek száma hónap **Megjegyzés**: standard szintű felügyelt lemez – 128 GB

**MÉRŐSZÁM azonosítója**: Ff85ef31-da5b-4eac-95dd-a69d6f97b18a **-fogyasztásmérő neve**: S15 **egység**: lemezek\*havi száma **Megjegyzés**: standard szintű felügyelt lemez – 256 GB

**FOGYASZTÁSMÉRŐ azonosítója**: 88ea9228-457a-4091-adc9-ad5194f30b6e mérőszám **neve**: S20 **egység**: a lemezek\*száma hónap **Megjegyzés**: standard szintű felügyelt lemez – 512 GB

**MÉRŐSZÁM azonosítója**: 5B1db88a-8596-4002-8052-347947c26940 **-fogyasztásmérő neve**: S30 **egység**: lemezek\*havi száma **Megjegyzés**: standard szintű felügyelt lemez – 1024 GB

**FOGYASZTÁSMÉRŐ azonosítója**: 7660B45b-b29d-49cb-b816-59f30fbab011- **fogyasztásmérő neve**: P4 **egység**: lemezek\*száma hónap **Megjegyzés**: prémium szintű felügyelt lemez – 32 GB

**MÉRŐSZÁM azonosítója**: 817007Fd-a077-477f-bc01-b876f27205fd **-fogyasztásmérő neve**: P6 **egység**: lemezek\*havi száma **Megjegyzés**: prémium szintű felügyelt lemez – 64 GB

**FOGYASZTÁSMÉRŐ azonosítója**: e554b6bc-96cd-4938-a5b5-0da990278519 mérőszám **neve**: P10 **egység**: lemezek\*száma hónap **Megjegyzés**: prémium szintű felügyelt lemez – 128 GB  

**MÉRŐSZÁM azonosítója**: Cdc0f53a-62a9-4472-a06c-e99a23b02907 **-fogyasztásmérő neve**: P15 **egység**: lemezek\*havi száma **Megjegyzés**: prémium szintű felügyelt lemez – 256 GB

**MÉRŐSZÁM azonosítója**: B9cb2d1a-84c2-4275-aa8b-70d2145d59aa **-fogyasztásmérő neve**: P20 **egység**: lemezek\*havi száma **Megjegyzés**: prémium szintű felügyelt lemez – 512 GB

**MÉRŐSZÁM azonosítója**: 06Bde724-9f94-43c0-84c3-d0fc54538369 **-fogyasztásmérő neve**: P30 **egység**: lemezek\*havi száma **Megjegyzés**: prémium szintű felügyelt lemez – 1024 GB

**FOGYASZTÁSMÉRŐ azonosítója**: 7Ba084ec-ef9c-4d64-a179-7732c6cb5e28- **fogyasztásmérő neve**: ActualStandardDiskSize **egység**:\*GB hónap **Megjegyzés**: a normál felügyelt lemez lemezének tényleges mérete.

**FOGYASZTÁSMÉRŐ azonosítója**: daef389a-06e5-4684-a7f7-8813d9f792d5  
Mérőszám **neve**: ActualPremiumDiskSize **egység**: GB\*havi **Megjegyzések**: a prémium szintű felügyelt lemez tényleges mérete.

**FOGYASZTÁSMÉRŐ azonosítója**: 108fa95b-be0d-4cd9-96e8-5b0d59505df1  
Mérőszám **neve**: ActualStandardSnapshotSize **egység**: GB\*hónap **megjegyzései**: a felügyelt normál pillanatkép méretének tényleges mérete.  

**MÉRŐSZÁM azonosítója**: 578Ae51d-4ef9-42f9-85ae-42b52d3d83ac **-fogyasztásmérő neve**: ActualPremiumSnapshotSize **egység**:\*GB hónap **Megjegyzés**: a felügyelt prémium pillanatkép lemezének tényleges mérete.

Mérőszám **azonosítója**: 5D76e09f-4567-452a-94cc-7d1f097761f0 **-fogyasztásmérő neve**: S4- **egység**: lemezes\*órák száma, **Megjegyzések**: standard szintű felügyelt lemez – 32 GB (elavult)

Mérőszám **azonosítója**: dc9fc6a9-0782-432a-b8dc-978130457494 **fogyasztásmérő neve**: S6- **egység**: a lemez\*órája **megjegyzései**: standard szintű felügyelt lemez – 64 GB (elavult)

Mérőszám **azonosítója**: E5572fce-9f58-49d7-840c-b168c0f01fff **-fogyasztásmérő neve**: S10- **egység**: a\*lemezek száma óránkénti **Megjegyzés**: standard szintű felügyelt lemez – 128 GB (elavult)

**MÉRŐSZÁM azonosítója**: 9A8caedd-1195-4cd5-80b4-a4c22f9302b8 **-fogyasztásmérő neve**: S15 **egység**: a lemezek\*száma óránkénti **Megjegyzés**: standard szintű felügyelt lemez – 256 GB (elavult)

**MÉRŐSZÁM azonosítója**: 5938F8da-0ecd-4C48-8d5a-c7c6c23546be **-fogyasztásmérő neve**: S20 **egység**: a lemezek\*száma óránkénti **Megjegyzés**: standard szintű felügyelt lemez – 512 GB (elavult)

**MÉRŐSZÁM azonosítója**: 7705A158-bd8b-4b2b-b4c2-0782343b81e6 **-fogyasztásmérő neve**: S30 **egység**: a lemezek\*száma óránkénti **Megjegyzés**: standard szintű felügyelt lemez – 1024 GB (elavult)

Mérőszám **azonosítója**: 5C105f5f-cbdf-435c-b49b-3c7174856dcc **-fogyasztásmérő neve**: P4- **egység**: a\*lemezek száma óránkénti **Megjegyzés**: prémium szintű felügyelt lemez – 32 GB (elavult)

**MÉRŐSZÁM azonosítója**: 518B412b-1927-4f25-985f-4aea24e55c4f **-fogyasztásmérő neve**: P6 **egység**: a lemezek\*száma óránkénti **Megjegyzés**: prémium szintű felügyelt lemez – 64 GB (elavult)

**MÉRŐSZÁM azonosítója**: 5Cfb1fed-0902-49e3-8217-9add946fd624 **-fogyasztásmérő neve**: P10 **egység**: a lemezek\*száma óránkénti **Megjegyzés**: prémium szintű felügyelt lemez – 128 GB (elavult)  

Mérőszám **azonosítója**: 8De91c94-f740-4d9a-b665-bd5974fa08d4- **fogyasztásmérő neve**: P15  
**Egység**: lemezes\*órák száma **Megjegyzések**: prémium szintű felügyelt lemez – 256 GB (elavult)

Mérőszám **azonosítója**: c7e7839c-293b-4761-ae4c-848eda91130b-mérőszám **neve**: P20 **egység**: a lemezek\*órája **Megjegyzés**: prémium szintű felügyelt lemez – 512 GB (elavult)

**MÉRŐSZÁM azonosítója**: 9F502103-adf4-4488-b494-456c95d23a9f **-fogyasztásmérő neve**: P30 **egység**: a lemezek\*száma óránkénti **Megjegyzés**: prémium szintű felügyelt lemez – 1024 GB (elavult)

**FOGYASZTÁSMÉRŐ azonosítója**: 8A409390-1913-40ae-917b-08d0f16f3c38- **fogyasztásmérő neve**: ActualStandardDiskSize **egység**:\*byte hours **Megjegyzés**: a normál felügyelt lemez (elavult) lemezének tényleges mérete.  

**FOGYASZTÁSMÉRŐ azonosítója**: 1273b16f-8458-4c34-8ce2-a515de551ef6  
**Fogyasztásmérő neve**: ActualPremiumDiskSize **egység**: byte\*hours **Megjegyzés**: a prémium szintű felügyelt lemez (elavult) lemezének tényleges mérete.

**FOGYASZTÁSMÉRŐ azonosítója**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**Fogyasztásmérő neve**: ActualStandardSnapshotSize **egység**: byte\*hours **Megjegyzés**: a felügyelt standard pillanatkép (elavult) lemezének tényleges mérete.

**MÉRŐSZÁM azonosítója**: 95B0c03f-8a82-4524-8961-ccfbf575f536 **-fogyasztásmérő neve**: ActualPremiumSnapshotSize **egység**:\*byte hours **Megjegyzés**: a felügyelt prémium pillanatkép (elavult) lemezének tényleges mérete.

**MÉRŐSZÁM azonosítója**: 75D4b707-1027-4403-9986-6ec7c05579c8 **-fogyasztásmérő neve**: ActualStandardSnapshotSize **egység**:\*GB hónap **Megjegyzés**: a felügyelt standard pillanatkép (elavult) lemezének tényleges mérete.

**FOGYASZTÁSMÉRŐ azonosítója**: 5Ca1cbb9-6f14-4e76-8be8-1ca91547965e- **fogyasztásmérő neve**: ActualPremiumSnapshotSize **egység**:\*GB hónap **Megjegyzés**: a felügyelt prémium pillanatkép (elavult) lemezének tényleges mérete.

### <a name="sql-rp"></a>SQL RP
  
**FOGYASZTÁSMÉRŐ azonosítója**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Fogyasztásmérő neve**: DatabaseSizeHourSqlMeter  
**Egység**: MB\*óra  
**Megjegyzések**: az adatbázis teljes kapacitása a létrehozáskor. Ha napi részletességgel hívja meg a használati API-t, a mérő a MB-ot adja meg, szorozva az órák számával.  
  
### <a name="mysql-rp"></a>MySql RP
  
**FOGYASZTÁSMÉRŐ azonosítója**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Fogyasztásmérő neve**: DatabaseSizeHourMySqlMeter  
**Egység**: MB\*óra  
**Megjegyzések**: az adatbázis teljes kapacitása a létrehozáskor. Ha napi részletességgel hívja meg a használati API-t, a mérő a MB-ot adja meg, szorozva az órák számával.

### <a name="key-vault"></a>Key Vault
  
**FOGYASZTÁSMÉRŐ azonosítója**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Fogyasztásmérő neve**: Key Vault tranzakció  
**Egység**: a kérelmek száma 10, 10E  
**Megjegyzések**: Key Vault adatsíkon fogadott REST API kérelmek száma.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Fogyasztásmérő neve**: speciális kulcsok tranzakciói  
**Egység**: 10k tranzakció  
**Megjegyzések**: RSA 3K/4K, ECC Key Transactions (előzetes verzió).  
  
### <a name="app-service"></a>App Service
  
**FOGYASZTÁSMÉRŐ azonosítója**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Fogyasztásmérő neve**: app Service  
**Egység**: virtuális mag órája  
**Megjegyzések**: az App Service futtatásához használt virtuális magok száma.

>[!NOTE]
>A Microsoft ezt a mérőszámot használja a App Service Azure Stack hub-on való feltöltéséhez. A felhőalapú megoldások szolgáltatói a többi App Service mérőszámot használhatják a bérlők használatának kiszámításához.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Mérőműszer neve**: functions-kérések  
**Egység**: 10 kérelem  
**Megjegyzések**: a kért végrehajtások teljes száma (10 végrehajtás esetén). A rendszer minden alkalommal megszámolja a végrehajtásokat, amikor egy függvény egy eseményre válaszol, vagy ha egy kötés aktiválódik.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Mérőműszer neve**: functions – számítás  
**Egység**: GB-s  
**Megjegyzések**: az erőforrás-használat Gigabyte másodpercben (GB/s) mérve. A **megfigyelt erőforrás** -használat kiszámítása úgy történik, hogy a függvény végrehajtásához szükséges idő (ezredmásodpercben) szorozza meg az átlagos memória méretét GB-ban. A függvény által használt memóriát 128 MB-ig, a maximális memória mérete (1 536 MB) alapján mérjük, a végrehajtási időt pedig a legközelebbi 1 MS-ra kerekítve számítjuk ki. Egyetlen függvény végrehajtásának minimális végrehajtási ideje és memóriája 100 MS és 128 MB.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Fogyasztásmérő neve**: megosztott app Service óra  
**Egység**: 1 óra **Megjegyzés**: a szegmens app Service csomag óránkénti használata. A csomagok díjszabása alkalmazási alapon történik.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Fogyasztásmérő neve**: ingyenes app Service óra  
**Egység**: 1 óra **Megjegyzés**: óránkénti ingyenes app Service csomag használata. A csomagok díjszabása alkalmazási alapon történik.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Fogyasztásmérő neve**: kis standard app Service óra  
**Egység**: 1 órás **Megjegyzés**: a példányok mérete és száma alapján számítható ki.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Fogyasztásmérő neve**: közepes standard app Service óra  
**Egység**: 1 órás **Megjegyzés**: a példányok mérete és száma alapján számítható ki.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Fogyasztásmérő neve**: nagy standard app Service óra  
**Egység**: 1 órás **Megjegyzés**: a példányok mérete és száma alapján számítható ki.  
  
### <a name="custom-worker-tiers"></a>Egyéni feldolgozói szintek
  
**Fogyasztásmérő azonosítója**: *Egyéni feldolgozói rétegek*
mérőszámának**neve**: egyéni feldolgozói szintek  
**Egység**: óra **Megjegyzés**: az DETERMINISZTIKUS mérőszám azonosítója az SKU és az egyéni feldolgozói rétegek neve alapján jön létre. Ez a mérőszám-azonosító minden egyéni feldolgozói szinten egyedi.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Fogyasztásmérő neve**: SNI SSL  
**Egység**:/SNI SSL kötés  
**Megjegyzések**: app Service kétféle SSL-kapcsolatot támogat: kiszolgálónév jelzése (SNI) SSL-kapcsolatokat és IP-cím SSL-kapcsolatokat. A SNI-alapú SSL a modern böngészőkben működik, miközben az IP-alapú SSL minden böngészőben működik.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
Mérőszám **neve**: IP SSL **egység**:/IP-alapú SSL-kötési **Megjegyzések**: app Service két típusú SSL-kapcsolatot támogat: kiszolgálónév jelzése (SNI) SSL-kapcsolatok és IP-cím SSL-kapcsolatok. A SNI-alapú SSL a modern böngészőkben működik, miközben az IP-alapú SSL minden böngészőben működik.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Fogyasztásmérő neve**: webes folyamat **egysége**:  
**Megjegyzések**: aktív helyenként számított/óra.
  
**FOGYASZTÁSMÉRŐ azonosítója**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Fogyasztásmérő neve**: külső kimenő forgalom sávszélessége  
**Egység**: GB  
**Megjegyzések**: összes bejövő kérelemre adott válasz bájtjai és összes kimenő kérelem bájtjainak száma + összes bejövő FTP-kérelemre adott válasz bájtjai és a teljes beérkező webes üzembe helyezési kérelmekre adott válasz bájtjai.  
  
## <a name="how-do-the-azure-stack-hub-usage-apis-compare-to-the-azure-usage-api-currently-in-public-preview"></a>Hogyan hasonlítható össze az Azure Stack hub használati API-k az [Azure használati API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) -val (jelenleg nyilvános előzetes verzióban)?

* A bérlői használati API konzisztens az Azure API-val, egyetlen kivétellel: az Azure Stack hub jelenleg nem támogatja a *showDetails* jelzőt.
* A szolgáltatói használati API csak Azure Stack hubhoz vonatkozik.
* Jelenleg az Azure-ban elérhető [RATECARD API](/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview) nem érhető el Azure stack központban.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Mi a különbség a használati idő és a jelentett idő között?

A használati adatok jelentéseinek két fő ideje van:

* **Jelentett idő**: az az idő, amikor a használati esemény bekerült a használati rendszerébe.
* **Használati idő**: az Azure stack hub-erőforrás felhasználásának időpontja.

Előfordulhat, hogy a használati idő és a jelentett idő egy adott használati eseménynél eltérést jelez. A késleltetés akár több órát is igénybe vehet bármilyen környezetben.

Jelenleg csak a **jelentett idő**szerint lehet lekérdezni.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Mit jelentenek ezek a használati API-hibakódok?

| **HTTP-állapotkód** | **Hibakód** | **Leírás** |
| --- | --- | --- |
| 400/hibás kérelem |*NoApiVersion* |Hiányzik az *API-Version* lekérdezési paraméter. |
| 400/hibás kérelem |*InvalidProperty* |Hiányzik egy tulajdonság, vagy az értéke érvénytelen. A válasz törzsében lévő hibakódban található üzenet azonosítja a hiányzó tulajdonságot. |
| 400/hibás kérelem |*RequestEndTimeIsInFuture* |A *ReportedEndTime* értéke jövőbeli. A jövőben nem engedélyezettek az argumentumok. |
| 400/hibás kérelem |*SubscriberIdIsNotDirectTenant* |A szolgáltatói API-hívás olyan előfizetés-azonosítót használt, amely nem a hívó érvényes bérlője. |
| 400/hibás kérelem |*SubscriptionIdMissingInRequest* |A hívó előfizetés-azonosítója hiányzik. |
| 400/hibás kérelem |*InvalidAggregationGranularity* |A rendszer érvénytelen összesítési részletességet kért. Az érvényes értékek napi és óránkénti. |
| 503 |*ServiceUnavailable* |Újrapróbálkozást lehetővé tevő hiba történt, mert a szolgáltatás foglalt, vagy a hívás folyamatban van. |

## <a name="what-is-the-policy-for-charging-for-vms"></a>Mi a virtuális gépek díjszabására vonatkozó szabályzat?

A virtuális gépek futtatása és leállítása a használati adatokat eredményezi. Az Azure-val konzisztensen a használati adatok kibocsátásának leállításához le kell állítani a felszabadítást. Abban az esetben, ha a portál nem érhető el, de a számítási erőforrás-szolgáltató még fut, a használatot a rendszer kibocsátja.

## <a name="how-do-i-extract-usage-data-from-the-azure-stack-hub-usage-apis"></a>Hogyan kinyerni a használati adatokat az Azure Stack hub használati API-kkal?

Azure Stack hub helyi használati API-jai használati adatainak kinyerésének legegyszerűbb módja a [githubon a használati összefoglalás parancsfájl](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/Usagesummary.ps1)használatával. A parancsfájlhoz a kezdő és a záró dátumnak bemeneti paramétereknek kell lennie.

Azt is megteheti, hogy a REST API-kat használja a [szolgáltatói erőforrás-használati API](azure-stack-provider-resource-api.md) és a [bérlői erőforrás-használati API](azure-stack-tenant-resource-usage-api.md) cikkeiben leírtak szerint.

## <a name="how-can-i-associate-usage-extracted-from-azure-usage-apis-to-a-specific-azure-stack-hub-user-subscription"></a>Hogyan rendelhetek hozzá az Azure használati API-kból kinyert használati adatokat egy adott Azure Stack hub felhasználói előfizetéshez?

A használati rekordok közé tartozik a **AdditionalInfo**nevű tulajdonság, amely tartalmazza az Azure stack hub előfizetés-azonosítóját. Ez az azonosító a megfelelő használati rekordot kibocsátó felhasználói előfizetés.

## <a name="next-steps"></a>További lépések

* [Ügyfél számlázása és jóváírása Azure Stack központban](azure-stack-billing-and-chargeback.md)
* [Szolgáltatói erőforrás-használati API](azure-stack-provider-resource-api.md)
* [Bérlői erőforrás-használati API](azure-stack-tenant-resource-usage-api.md)
