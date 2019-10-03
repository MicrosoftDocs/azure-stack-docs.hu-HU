---
title: Az Azure stack Storage eltérései és szempontjai | Microsoft Docs
description: Ismerje meg az Azure stack Storage és az Azure Storage közötti különbségeket, valamint Azure Stack üzembe helyezési megfontolásokat.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/2/2019
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: e2680a91aa2b9232eb86de4338d1198fb515e6d3
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824726"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure Stack tárterület: Különbségek és szempontok

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Azure Stack Storage a Storage Cloud Services készlete Microsoft Azure Stackban. A Azure Stack Storage blob-, tábla-, üzenetsor-és fiókkezelés-funkciókat biztosít az Azure-konzisztens szemantikai funkciókhoz.

Ez a cikk az Azure Storage-szolgáltatásokkal kapcsolatos ismert Azure Stack tárolási különbségeket foglalja össze. Emellett felsorolja azokat a dolgokat, amelyeket figyelembe kell venni a Azure Stack telepítésekor. A globális Azure és a Azure Stack közötti magas szintű különbségek megismeréséhez tekintse meg a [legfontosabb szempontokat](azure-stack-considerations.md) ismertető cikket.

## <a name="cheat-sheet-storage-differences"></a>Cheat Sheet: Tárolási különbségek

| Funkció | Azure (globális) | Azure Stack |
| --- | --- | --- |
|File Storage|Felhőalapú SMB-fájlmegosztás támogatott|Még nem támogatott
|Azure Storage szolgáltatás titkosítása inaktív adatok esetén|256 bites AES-titkosítás. Az ügyfél által felügyelt kulcsokkal történő titkosítás támogatása Key Vaultban.|BitLocker 128 bites AES-titkosítás. Az ügyfél által felügyelt kulcsokkal történő titkosítás nem támogatott.
|Tárfiók típusa|Általános célú v1, v2 és blob Storage-fiókok|Csak általános célú v1.
|Replikációs lehetőségek|Helyileg redundáns tárolás, Geo-redundáns tárolás, olvasási hozzáférés a Geo-redundáns tároláshoz és a zóna-redundáns tároláshoz|Helyileg redundáns tárolás.
|Prémium szintű Storage|Nagy teljesítményű és kis késésű tárolást biztosít. Csak a Premium Storage-fiókokban lévő Blobok támogatása.|Kiépíthető, a teljesítmény korlátja és a garancia nem lehetséges. Nem tiltható le a blokk Blobok használata, blobok, táblák és várólisták hozzáfűzése a Premium Storage-fiókokban.
|Felügyelt lemezek|Prémium és standard támogatott|Az 1808-es vagy újabb verzió használata esetén támogatott.
|A blob neve|1 024 karakter (2 048 bájt)|880 karakter (1 760 bájt)
|BLOB maximális méretének letiltása|4,75 TB (100 MB X 50 000 blokk)|4,75 TB (100 MB x 50 000 blokk) az 1802 frissítéshez vagy újabb verzióhoz. 50 000 X 4 MB (körülbelül 195 GB) a korábbi verziókhoz.
|Oldal blob-pillanatképének másolata|Azure-beli nem felügyelt virtuálisgép-lemezek biztonsági mentése támogatott futó virtuális gépekhez|Még nem támogatott.
|Oldal blob növekményes pillanatképének másolása|Támogatott prémium és standard Azure-oldal Blobok|Még nem támogatott.
|Oldal blob-számlázása|Az egyedi lapokra vonatkozó díjak a blobban vagy a pillanatképben vannak felszámítva. Nem számít fel további díjat a blobokhoz társított Pillanatképek esetében, amíg az alap blob nem frissül.|Az alap blob-és assiociated-Pillanatképek esetében díjat számítunk fel. Az egyes Pillanatképek esetében további díjakat számítunk fel.
|A blob Storage tárolási szintjei|Gyakori, ritka elérésű és archív tárolási szintek.|Még nem támogatott.
|A blob Storage-hoz készült Soft delete|Általánosan elérhető|Még nem támogatott.
|Oldal blobjának maximális mérete|8 TB|1 TB
|Oldal blob-oldalának mérete|512 bájt|4 KB
|Tábla partíciós kulcsának és a sor kulcsának mérete|1 024 karakter (2 048 bájt)|400 karakter (800 bájt)
|BLOB pillanatképe|Egy blob pillanatképének maximális száma nincs korlátozva.|Egy blob pillanatképének maximális száma 1 000.
|Azure AD-hitelesítés a Storage-hoz|Előzetes verzióban|Még nem támogatott.
|Megváltoztathatatlan Blobok|Általánosan elérhető|Még nem támogatott.
|Tűzfal-és virtuális hálózati szabályok a tároláshoz|Általánosan elérhető|Még nem támogatott.|

A tárolási metrikákkal kapcsolatban is különbségek vannak:

* A tárolási mérőszámokban lévő tranzakciós adatok nem különböztetik meg a belső vagy külső hálózati sávszélességet.
* A tárolási metrikákban lévő tranzakciós adatok nem tartalmazzák a csatlakoztatott lemezekhez tartozó virtuális gépek elérését.

## <a name="api-version"></a>API-verzió

A Azure Stack Storage a következő verziókat támogatja:

Azure Storage Services API-k:

1811 frissítés vagy újabb verzió:

- [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Korábbi verziók:

- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Az Azure Storage Services kezelési API-jai:

1811 frissítés vagy újabb verzió:

- [2017-10-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2017-06-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-12-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-05-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Korábbi verziók:

- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

További információ a Azure Stack támogatott tárolási ügyféloldali kódtárakkal kapcsolatban: [Ismerkedés a Azure stack Storage fejlesztői eszközeivel](azure-stack-storage-dev.md).

## <a name="next-steps"></a>További lépések

* [Ismerkedés a Azure Stack Storage fejlesztői eszközeivel](azure-stack-storage-dev.md)
* [Adatátviteli eszközök használata a Azure Stack Storage szolgáltatáshoz](azure-stack-storage-transfer.md)
* [A Azure Stack Storage bemutatása](azure-stack-storage-overview.md)
