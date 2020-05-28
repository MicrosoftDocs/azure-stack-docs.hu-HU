---
title: Azure Stack hub tárolási különbségek és megfontolások
titleSuffix: Azure Stack Hub
description: Ismerje meg az Azure Stack hub Storage és az Azure Storage közötti különbségeket, valamint Azure Stack hub üzembe helyezési szempontjait.
author: mattbriggs
ms.topic: conceptual
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2020
ms.openlocfilehash: 4206ebb6e8195c43ad043f09567d0911222c1e20
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111931"
---
# <a name="azure-stack-hub-storage-differences-and-considerations"></a>Azure Stack hub Storage: különbségek és megfontolások

A Azure Stack hub Storage a Storage Cloud Services készlete Microsoft Azure Stack központban. Az Azure Stack hub Storage blob-, tábla-, üzenetsor-és fiókkezelés-funkciókat biztosít az Azure-konzisztens szemantikai funkciókhoz.

Ez a cikk az Azure Storage-szolgáltatásokból származó ismert Azure Stack hub-tárolók közötti különbségeket foglalja össze. Azt is felsorolja, hogy milyen szempontokat kell figyelembe venni Azure Stack hub telepítésekor. A globális Azure és az Azure Stack hub közötti magas szintű különbségekről a [legfontosabb szempontokat](azure-stack-considerations.md) ismertető cikkben talál további információt.

## <a name="cheat-sheet-storage-differences"></a>Cheat Sheet: tárolási különbségek

| Funkció | Azure (globális) | Azure Stack hub |
| --- | --- | --- |
|File Storage|A felhőalapú SMB-fájlmegosztás támogatott. | Még nem támogatott.
|Azure Storage szolgáltatás titkosítása inaktív adatok esetén|256 bites AES-titkosítás. Az ügyfél által felügyelt kulcsokkal történő titkosítás támogatása Key Vaultban.|BitLocker 128 bites AES-titkosítás. Az ügyfél által felügyelt kulcsokkal történő titkosítás nem támogatott.
|Tárfiók típusa|Általános célú v1, v2 és blob Storage-fiókok. |Csak általános célú v1.
|Replikációs lehetőségek|Helyileg redundáns tárolás, Geo-redundáns tárolás, olvasási hozzáférés a Geo-redundáns tároláshoz és a zóna-redundáns tároláshoz. |Helyileg redundáns tárolás.
|Prémium szintű Storage|Nagy teljesítményű és kis késésű tárolást biztosít. Csak a Premium Storage-fiókokban lévő Blobok támogatása.|Kiépíthető, a teljesítmény korlátja és a garancia nem lehetséges. A Premium Storage-fiókokban nem tiltható le a blokk-Blobok, a Blobok, a táblák és a várólisták hozzáfűzése.
|Felügyelt lemezek|Prémium és standard támogatott. |Az 1808-es vagy újabb verzió használata esetén támogatott.
|A blob neve|1 024 karakter (2 048 bájt). |880 karakter (1 760 bájt).
|BLOB maximális méretének letiltása|4,75 TB (100 MB X 50 000 blokk). |4,75 TB (100 MB x 50 000 blokk) az 1802 frissítéshez vagy újabb verzióhoz. 50 000 X 4 MB (körülbelül 195 GB) a korábbi verziókhoz.
|Oldal blob-pillanatképének másolata|Az Azure biztonsági mentése nem felügyelt, futó virtuális géphez csatlakoztatott virtuálisgép-lemezeket támogat. |Még nem támogatott.
|Oldal blob növekményes pillanatképének másolása|A prémium és standard szintű Azure-oldal Blobok támogatottak. |Még nem támogatott.
|Oldal blob-számlázása|Az egyedi lapokra, akár a blobban, akár a pillanatképből számítunk fel díjat. Nem számítunk fel további díjat a blobokhoz társított Pillanatképek kiszámításához, amíg az alap blob nem frissül.|Az alap blobok és a hozzájuk tartozó Pillanatképek esetében díjat számítunk fel. Az egyes Pillanatképek esetében további díjakat számítunk fel.
|A blob Storage tárolási szintjei|Gyakori, ritka elérésű és archív tárolási szintek.|Még nem támogatott.
|A blob Storage-hoz készült Soft delete|Általánosan elérhető. |Még nem támogatott.
|Oldal blobjának maximális mérete|8 TB. |1 TB. 
|Oldal blob-oldalának mérete|512 bájt. |4 KB. 
|Tábla partíciós kulcsának és a sor kulcsának mérete|1 024 karakter (2 048 bájt).|400 karakter (800 bájt).
|BLOB pillanatképe|Egy blob pillanatképének maximális száma nincs korlátozva.|Egy blob pillanatképének maximális száma 1 000.
|Azure AD-hitelesítés a Storage-hoz|Előzetes verzióban. |Még nem támogatott.
|Megváltoztathatatlan Blobok|Általánosan elérhető. |Még nem támogatott.
|Tűzfal-és virtuális hálózati szabályok a tároláshoz|Általánosan elérhető. |Még nem támogatott.|

A tárolási metrikákkal kapcsolatban is különbségek vannak:

* A tárolási mérőszámokban lévő tranzakciós adatok nem különböztetik meg a belső vagy külső hálózati sávszélességet.
* A tárolási mérőszámokban lévő tranzakciós adatok nem tartalmazzák a virtuális gép a csatlakoztatott lemezekhez való hozzáférését.

## <a name="api-version"></a>API-verzió

A Azure Stack hub Storage a következő verziókat támogatja:

Azure Storage Services API-k:

1811 frissítés vagy újabb verzió:

- [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
- [2017. 4. 17.](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Korábbi verziók:

- [2017. 4. 17.](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
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
- [2015-05-01 – előzetes verzió](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Korábbi verziók:

- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01 – előzetes verzió](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="powershell-version"></a>PowerShell-verzió

A Storage-modul PowerShell-hez vegye figyelembe a REST API kompatibilis verziót.

| Modul | Támogatott verzió | Használat |
|---|---|---|
| Azure.Storage | [4.5.0](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0) | A Azure Stack hub Storage-fiókokban lévő blobokat, várólistákat és táblákat kezeli. |
| AzureRM.Storage | [5.0.4](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.4) | Tároló-fiókok létrehozása és kezelése Azure Stack központban. |

További információ a Azure Stack hub támogatott Storage-kódtárakkal kapcsolatban: Ismerkedés [a Azure stack hub Storage fejlesztői eszközeivel](azure-stack-storage-dev.md).

## <a name="next-steps"></a>Következő lépések

* [Ismerkedés a Azure Stack hub Storage fejlesztői eszközeivel](azure-stack-storage-dev.md)
* [Adatátviteli eszközök használata az Azure Stack hub Storage szolgáltatáshoz](azure-stack-storage-transfer.md)
* [Az Azure Stack hub Storage bemutatása](azure-stack-storage-overview.md)
