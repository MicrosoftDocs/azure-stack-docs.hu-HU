---
title: Helyi számlázási mérőszámok – MDC | Microsoft Docs
description: A moduláris adatközpontból (MDC) való működéskor használt helyi Azure Stack mérőszámok listájának olvasása.
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
ms.date: 02/16/2021
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/20/2019
ms.openlocfilehash: e47ba23f0beeadf4117e9172cb0d835ae3a22a6c
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562580"
---
# <a name="local-azure-stack-billing-meters---modular-data-center-mdc"></a>Helyi Azure Stack számlázási mérőműszerek – moduláris adatközpont (MDC)

Ez a cikk a helyi Azure Stack számlázási mérőszámokat sorolja fel. A használatot a következő erőforrás-szolgáltatók jelentik:

## <a name="network"></a>Network (Hálózat)

**FOGYASZTÁSMÉRŐ azonosítója**: F271A8A388C44D93956A063E1D2FA80B  
**Fogyasztásmérő neve**: statikus IP-cím használata  
**Egység**: IP-címek  
**Megjegyzések**: a használt IP-címek száma. Ha napi részletességgel hívja meg a használati API-t, a mérő az IP-címet adja vissza, szorozva az órák számával.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 9E2739BA86744796B465F64674B822BA  
**Fogyasztásmérő neve**: dinamikus IP-cím használata  
**Egység**: IP-címek  
**Megjegyzések**: a használt IP-címek száma. Ha napi részletességgel hívja meg a használati API-t, a mérő az IP-címet adja vissza, szorozva az órák számával.

## <a name="storage"></a>Tárolás

**FOGYASZTÁSMÉRŐ azonosítója**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Fogyasztásmérő neve**: TableCapacity  
**Egység**: GB \* óra  
**Megjegyzések**: a táblák által felhasznált teljes kapacitás.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Fogyasztásmérő neve**: PageBlobCapacity  
**Egység**: GB \* óra  
**Megjegyzések**: az oldal Blobok által felhasznált teljes kapacitás.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Fogyasztásmérő neve**: QueueCapacity  
**Egység**: GB \* óra  
**Megjegyzések**: a várólista által felhasznált teljes kapacitás.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Fogyasztásmérő neve**: BlockBlobCapacity  
**Egység**: GB * óra  
**Megjegyzések**: a blokk Blobok által felhasznált teljes kapacitás.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Fogyasztásmérő neve**: TableTransactions  
**Egység**: kérelmek száma * 10 000  
**Megjegyzések**: table Service kérelmek (* 10 000).  
  
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
**Egység**: kérelmek száma * 10 000 **Megjegyzés**: Blob service kérelmek (* 10 000).  
  
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
**Egység**: kérelmek száma * 10 000 **Megjegyzés**: Queue szolgáltatás kérelmek (* 10 000).  
  
**FOGYASZTÁSMÉRŐ azonosítója**: E518E809-E369-4A45-9274-2017B29FFF25  
**Fogyasztásmérő neve**: QueueDataTransIn  
**Egység**: a bejövő adatforgalom GB-ban  
**Megjegyzések**: Queue szolgáltatás a bejövő adatforgalom GB-ban.  
  
**FOGYASZTÁSMÉRŐ azonosítója**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Fogyasztásmérő neve**: QueueDataTransOut  
**Egység**: kimenő forgalom GB-ban  
**Megjegyzések**: Queue szolgáltatás a kimenő adatforgalom GB-ban

## <a name="compute"></a>Compute

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
**Megjegyzések**: az alapszintű és a Windows RENDSZERű virtuális gépeket is rögzíti. A nem igazodik a magokhoz.

## <a name="managed-disks"></a>Managed Disks

Mérőszám **azonosítója**: 380874F9-300c-48e0-95a0-d2d9a21ade8f- **fogyasztásmérő neve**: S4 **egység**: lemezek száma * havi **Megjegyzések**: standard szintű felügyelt lemez – 32 GB

Mérőszám **azonosítója**: 1b77d90f-427b-4435-b4f1-d78adec53222 mérőszám **neve**: S6- **egység**: lemezek száma * havi **Megjegyzések**: standard szintű felügyelt lemez – 64 GB

Mérőszám **azonosítója**: D5f7731b-f639-404A-89d0-e46186e22c8d- **fogyasztásmérő neve**: S10- **egység**: lemezek száma * hónap **Megjegyzés**: standard szintű felügyelt lemez – 128 GB

Mérőszám **azonosítója**: Ff85ef31-da5b-4eac-95dd-a69d6f97b18a- **fogyasztásmérő neve**: S15 **egység**: lemezek száma * havi **Megjegyzések**: standard szintű felügyelt lemez – 256 GB

Mérőszám **azonosítója**: 88ea9228-457a-4091-adc9-ad5194f30b6e mérőszám **neve**: S20 **egység**: lemezek száma * havi **Megjegyzések**: standard szintű felügyelt lemez – 512 GB

Mérőszám **azonosítója**: 5B1db88a-8596-4002-8052-347947c26940- **fogyasztásmérő neve**: S30 **egység**: lemezek száma * havi **Megjegyzések**: standard szintű felügyelt lemez – 1024 GB

Mérőszám **azonosítója**: 7660B45b-b29d-49cb-b816-59f30fbab011- **fogyasztásmérő neve**: P4 **egység**: lemezek száma * havi **Megjegyzések**: prémium szintű felügyelt lemez – 32 GB

Mérőszám **azonosítója**: 817007Fd-a077-477f-bc01-b876f27205fd- **fogyasztásmérő neve**: P6 **egység**: lemezek száma * havi **Megjegyzések**: prémium szintű felügyelt lemez – 64 GB

Mérőszám **azonosítója**: e554b6bc-96cd-4938-a5b5-0da990278519 mérőszám **neve**: P10 **egység**: lemezek száma * havi **Megjegyzések**: prémium szintű felügyelt lemez – 128 GB  

Mérőszám **azonosítója**: Cdc0f53a-62a9-4472-a06c-e99a23b02907- **fogyasztásmérő neve**: P15 **egység**: lemezek száma * havi **Megjegyzések**: prémium szintű felügyelt lemez – 256 GB

Mérőszám **azonosítója**: B9cb2d1a-84c2-4275-aa8b-70d2145d59aa- **fogyasztásmérő neve**: P20 **egység**: lemezek száma * havi **Megjegyzések**: prémium szintű felügyelt lemez – 512 GB

Mérőszám **azonosítója**: 06Bde724-9f94-43c0-84c3-d0fc54538369- **fogyasztásmérő neve**: P30 **egység**: lemezek száma * havi **Megjegyzések**: prémium szintű felügyelt lemez – 1024 GB

**FOGYASZTÁSMÉRŐ azonosítója**: 7Ba084ec-ef9c-4d64-a179-7732c6cb5e28- **fogyasztásmérő neve**: ActualStandardDiskSize **egység**: GB * hónap **Megjegyzés**: a normál felügyelt lemez lemezének tényleges mérete

Mérőszám **azonosítója**: Daef389a-06e5-4684-a7f7-8813d9f792d5- **fogyasztásmérő neve**: ActualPremiumDiskSize **egység**: GB * hónap **Megjegyzések**: a prémium szintű felügyelt lemez lemezének tényleges mérete

Mérőszám **azonosítója**: 108Fa95b-be0d-4cd9-96e8-5b0d59505df1- **fogyasztásmérő neve**: ActualStandardSnapshotSize **egység**: GB * hónap **Megjegyzés**: a tényleges méret a felügyelt normál Pillanatképek lemezén.

Mérőszám **azonosítója**: 578Ae51d-4ef9-42f9-85ae-42b52d3d83ac- **fogyasztásmérő neve**: ActualPremiumSnapshotSize **egység**: GB * hónap **Megjegyzés**: a felügyelt prémium pillanatfelvétel lemezének tényleges mérete.

Mérőszám **azonosítója**: 5D76e09f-4567-452a-94cc-7d1f097761f0- **fogyasztásmérő neve**: S4- **egység**: lemezek száma * óra **Megjegyzés**: standard szintű felügyelt lemez – 32 GB (elavult)

**FOGYASZTÁSMÉRŐ azonosítója**: dc9fc6a9-0782-432a-b8dc-978130457494 **Meter neve**: S6- **egység**: lemezek száma * óra **Megjegyzés**: standard szintű felügyelt lemez – 64 GB (elavult)

Mérőszám **azonosítója**: E5572fce-9f58-49d7-840c-b168c0f01fff- **fogyasztásmérő neve**: S10- **egység**: lemezek száma * óra **Megjegyzés**: standard szintű felügyelt lemez – 128 GB (elavult)

Mérőszám **azonosítója**: 9A8caedd-1195-4cd5-80b4-a4c22f9302b8- **fogyasztásmérő neve**: S15 **egység**: lemezek száma * óra **Megjegyzés**: standard szintű felügyelt lemez – 256 GB (elavult)

Mérőszám **azonosítója**: 5938F8da-0ecd-4C48-8d5a-c7c6c23546be- **fogyasztásmérő neve**: S20 **egység**: lemezek száma * óra **Megjegyzés**: standard szintű felügyelt lemez – 512 GB (elavult)

Mérőszám **azonosítója**: 7705A158-bd8b-4b2b-b4c2-0782343b81e6- **fogyasztásmérő neve**: S30 **egység**: lemezek száma * óra **Megjegyzés**: standard szintű felügyelt lemez – 1024 GB (elavult)

**FOGYASZTÁSMÉRŐ azonosítója**: 5C105f5f-cbdf-435c-b49b-3c7174856dcc- **fogyasztásmérő neve**: P4- **egység**: lemezek száma * óra **Megjegyzés**: prémium szintű felügyelt lemez – 32 GB (elavult)

Mérőszám **azonosítója**: 518B412b-1927-4f25-985f-4aea24e55c4f- **fogyasztásmérő neve**: P6 **egység**: lemezek száma * óra **Megjegyzés**: prémium szintű felügyelt lemez – 64 GB (elavult)

Mérőszám **azonosítója**: 5Cfb1fed-0902-49e3-8217-9add946fd624- **fogyasztásmérő neve**: P10 **egység**: lemezek száma * óra **Megjegyzés**: prémium szintű felügyelt lemez – 128 GB (elavult)

Mérőszám **azonosítója**: 8De91c94-f740-4d9a-b665-bd5974fa08d4- **fogyasztásmérő neve**: P15 **egység**: lemezek száma * óra **Megjegyzés**: prémium szintű felügyelt lemez – 256 GB (elavult)

**FOGYASZTÁSMÉRŐ azonosítója**: c7e7839c-293b-4761-ae4c-848eda91130b mérőszám **neve**: P20 **egység**: lemezek száma * óra **Megjegyzés**: prémium szintű felügyelt lemez – 512 GB (elavult)

Mérőszám **azonosítója**: 9F502103-adf4-4488-b494-456c95d23a9f- **fogyasztásmérő neve**: P30 **egység**: lemezek száma * óra **Megjegyzés**: prémium szintű felügyelt lemez – 1024 GB (elavult)

Mérőszám **azonosítója**: 8A409390-1913-40ae-917b-08d0f16f3c38- **fogyasztásmérő neve**: ActualStandardDiskSize **egység**: bájt * óra **Megjegyzés**: a normál felügyelt lemez lemezének tényleges mérete (elavult)

Mérőszám **azonosítója**: 1273B16f-8458-4c34-8ce2-a515de551ef6- **fogyasztásmérő neve**: ActualPremiumDiskSize **egység**: bájt * óra **Megjegyzés**: a prémium szintű felügyelt lemez lemezének tényleges mérete (elavult)

**Mérési azonosító**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c-mérőszám **neve**: ActualStandardSnapshotSize **egység**: byte * hours **Megjegyzés**: a felügyelt standard pillanatkép (elavult) lemezének tényleges mérete

Mérőszám **azonosítója**: 95B0c03f-8a82-4524-8961-ccfbf575f536- **fogyasztásmérő neve**: ActualPremiumSnapshotSize **egység**: bájt * óra **Megjegyzés**: a felügyelt prémium pillanatkép lemezének tényleges mérete (elavult)

Mérőszám **azonosítója**: 75D4b707-1027-4403-9986-6ec7c05579c8- **fogyasztásmérő neve**: ActualStandardSnapshotSize **egység**: GB * hónap **Megjegyzés**: a felügyelt standard pillanatkép (elavult) lemezének tényleges mérete

Mérőszám **azonosítója**: 5Ca1cbb9-6f14-4e76-8be8-1ca91547965e- **fogyasztásmérő neve**: ActualPremiumSnapshotSize **egység**: GB * hónap **Megjegyzés**: a felügyelt prémium pillanatkép lemezének tényleges mérete (elavult)

### <a name="sql-rp"></a>SQL RP
  
**FOGYASZTÁSMÉRŐ azonosítója**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Fogyasztásmérő neve**: DatabaseSizeHourSqlMeter  
**Egység**: MB * óra  
**Megjegyzések**: az adatbázis teljes kapacitása a létrehozáskor. Ha napi részletességgel hívja meg a használati API-t, a mérő a MB-ot adja meg, szorozva az órák számával.

## <a name="mysql-rp"></a>MySql RP
  
Mérőszám **azonosítója**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3- **fogyasztásmérő neve**: DatabaseSizeHourMySqlMeter **egység**: MB * óra **Megjegyzés**: az összes adatbázis kapacitása a létrehozáskor. Ha napi részletességgel hívja meg a használati API-t, a mérő a MB-ot adja meg, szorozva az órák számával.

## <a name="key-vault"></a>Key Vault
  
Mérőszám **azonosítója**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4- **fogyasztásmérő neve**: Key Vault tranzakciós **egység**: kérelmek száma * 10 000 **megjegyzés**: Key Vault adatsík által fogadott REST API kérelmek száma.  
  
Mérőszám **azonosítója**: 2C354225-B2FE-42E5-AD89-14F0EA302C87- **mérő neve**: speciális kulcsok tranzakciós **egysége**: 10 000 tranzakciós **Megjegyzés**: RSA 3k/4k, ECC Key Transactions. (előzetes verzió).

## <a name="app-service"></a>App Service
  
Mérőszám **azonosítója**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA- **fogyasztásmérő neve**: app Service **egység**: Virtual Core hours **Megjegyzés**: az App Service futtatásához használt virtuális magok száma. Megjegyzés: a Microsoft ezt a mérőszámot használja a App Service Azure Stackre való feltöltéséhez. A felhőalapú megoldások szolgáltatói a többi App Service mérőszámot használhatják a bérlők használatának kiszámításához.
  
**FOGYASZTÁSMÉRŐ azonosítója**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Mérőműszer neve**: functions kérelmek **egysége**: 10 kérelem **Megjegyzés**: a kért végrehajtások teljes száma (10 végrehajtás). A rendszer minden alkalommal megszámolja a végrehajtásokat, amikor egy függvény egy eseményre válaszol, vagy ha egy kötés aktiválódik.
  
Mérőszám **azonosítója**: D1D04836-075C-4F27-BF65-0A1130EC60ED- **mérő neve**: functions-számítási **egység**: GB **Megjegyzés**: az erőforrás-felhasználás GB/másodpercben (GB/s) mérve. A **megfigyelt erőforrás** -használat kiszámítása úgy történik, hogy a függvény végrehajtásához szükséges idő (ezredmásodpercben) szorozza meg az átlagos memória méretét GB-ban. A függvény által használt memóriát 128 MB-ig, a maximális memória mérete (1 536 MB) alapján mérjük, a végrehajtási időt pedig a legközelebbi 1 MS-ra kerekítve számítjuk ki. Egyetlen függvény végrehajtásának minimális végrehajtási ideje és memóriája 100 MS és 128 MB.
  
Mérőszám **azonosítója**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D- **fogyasztásmérő neve**: Shared app Service hours **Unit**: 1 órás **Megjegyzés**: a szegmens app Service csomag óránkénti használata. A csomagok díjszabása alkalmazási alapon történik.
  
Mérőszám **azonosítója**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9- **fogyasztásmérő neve**: ingyenes app Service óra **egység**: 1 óra **Megjegyzés**: óránkénti használat ingyenes app Service terv. A csomagok díjszabása alkalmazási alapon történik.
  
Mérőszám **azonosítója**: 88039D51-A206-3A89-E9DE-C5117E2D10A6- **mérő neve**: kis standard app Service óra **egység**: 1 óra **Megjegyzés**: a példányok mérete és száma alapján számítható ki.
  
Mérőszám **azonosítója**: 83A2A13E-4788-78DD-5D55-2831B68ED825 mérőszám **neve**: közepes standard app Service óra **egység**: 1 órás **Megjegyzés**: a példányok mérete és száma alapján számítható ki.
  
Mérőszám **azonosítója**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6- **mérő neve**: nagyméretű standard app Service óra **egység**: 1 óra **Megjegyzés**: a példányok mérete és száma alapján számítható ki.

## <a name="custom-worker-tiers"></a>Egyéni feldolgozói szintek
  
**Fogyasztásmérő azonosítója**: *Egyéni feldolgozói rétegek* mérőszámának 
 **neve**: egyéni feldolgozói szintek  
**Egység**: óra **Megjegyzés**: az DETERMINISZTIKUS mérőszám azonosítója az SKU és az egyéni feldolgozói rétegek neve alapján jön létre. Ez a mérőszám-azonosító minden egyéni feldolgozói szinten egyedi.
  
Mérőszám **azonosítója**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473- **fogyasztásmérő neve**: SNI SSL **egység**:/SNI SSL kötési **Megjegyzések**: a App Service két típusú SSL-kapcsolatot támogat: kiszolgálónév jelzése (SNI) SSL-kapcsolatok és IP-cím SSL-kapcsolatok. A SNI-alapú SSL a modern böngészőkben működik, miközben az IP-alapú SSL minden böngészőben működik.
  
**FOGYASZTÁSMÉRŐ azonosítója**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
Mérőszám **neve**: IP SSL **egység**:/IP-alapú SSL-kötési **Megjegyzések**: app Service két típusú SSL-kapcsolatot támogat: kiszolgálónév jelzése (SNI) SSL-kapcsolatok és IP-cím SSL-kapcsolatok. A SNI-alapú SSL a modern böngészőkben működik, miközben az IP-alapú SSL minden böngészőben működik.
  
Mérőszám **azonosítója**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B mérőszám **neve**: webes folyamat **egysége**: **Megjegyzések**: aktív helyenként számított óránként.
  
**FOGYASZTÁSMÉRŐ azonosítója**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
Mérőszám **neve**: külső kimenő forgalom sávszélességi **egysége**: GB **Megjegyzés**: összes bejövő kérelem válaszának bájtjai + összes kimenő kérelem bájtjai + összes bejövő FTP-kérelem válaszának bájtjai és teljes beérkező webes üzembe helyezési kérelmekre adott válaszok bájtjai