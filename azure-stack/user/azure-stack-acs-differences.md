---
title: Azure Stack hub tárolási különbségek és megfontolások | Microsoft Docs
description: Ismerje meg az Azure Stack hub Storage és az Azure Storage közötti különbségeket, valamint Azure Stack hub üzembe helyezési szempontjait.
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
ms.openlocfilehash: 9b945f4cbb5e955c38b18c4bacd18726bbcb5e89
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883864"
---
# <a name="azure-stack-hub-storage-differences-and-considerations"></a>Azure Stack hub Storage: különbségek és megfontolások

A Azure Stack hub Storage a Storage Cloud Services készlete Microsoft Azure Stack központban. Az Azure Stack hub Storage blob-, tábla-, üzenetsor-és fiókkezelés-funkciókat biztosít az Azure-konzisztens szemantikai funkciókhoz.

Ez a cikk az Azure Storage-szolgáltatásokból származó ismert Azure Stack hub-tárolók közötti különbségeket foglalja össze. Azt is felsorolja, hogy milyen szempontokat kell figyelembe venni Azure Stack hub telepítésekor. A globális Azure és az Azure Stack hub közötti magas szintű különbségekről a [legfontosabb szempontokat](azure-stack-considerations.md) ismertető cikkben talál további információt.

## <a name="cheat-sheet-storage-differences"></a>Cheat Sheet: tárolási különbségek

| Szolgáltatás | Azure (globális) | Azure Stack Hub |
| --- | --- | --- |
|File Storage|Felhőalapú SMB-fájlmegosztás támogatott|Még nem támogatott
|Azure Storage szolgáltatás titkosítása inaktív adatok esetén|256 bites AES-titkosítás. Az ügyfél által felügyelt kulcsokkal történő titkosítás támogatása Key Vaultban.|BitLocker 128 bites AES-titkosítás. Az ügyfél által felügyelt kulcsokkal történő titkosítás nem támogatott.
|Tárfiók típusa|Általános célú v1, v2 és blob Storage-fiókok|Csak általános célú v1.
|Replikációs lehetőségek|Helyileg redundáns tárolás, Geo-redundáns tárolás, olvasási hozzáférés a Geo-redundáns tároláshoz és a zóna-redundáns tároláshoz|Helyileg redundáns tárolás.
|Prémium szintű Storage|Nagy teljesítményű és kis késésű tárolást biztosít. Csak a Premium Storage-fiókokban lévő Blobok támogatása.|Kiépíthető, a teljesítmény korlátja és a garancia nem lehetséges. Nem tiltható le a blokk Blobok használata, blobok, táblák és várólisták hozzáfűzése a Premium Storage-fiókokban.
|Managed Disks|Prémium és standard támogatott|Az 1808-es vagy újabb verzió használata esetén támogatott.
|A blob neve|1 024 karakter (2 048 bájt)|880 karakter (1 760 bájt)
|BLOB maximális méretének letiltása|4,75 TB (100 MB X 50 000 blokk)|4,75 TB (100 MB x 50 000 blokk) az 1802 frissítéshez vagy újabb verzióhoz. 50 000 X 4 MB (körülbelül 195 GB) a korábbi verziókhoz.
|Oldal blob-pillanatképének másolata|Azure-beli nem felügyelt virtuálisgép-lemezek biztonsági mentése támogatott futó virtuális gépekhez|Még nem támogatott.
|Oldal blob növekményes pillanatképének másolása|Támogatott prémium és standard Azure-oldal Blobok|Még nem támogatott.
|Oldal blob-számlázása|Az egyedi lapokra vonatkozó díjak a blobban vagy a pillanatképben vannak felszámítva. Nem számít fel további díjat a blobokhoz társított Pillanatképek esetében, amíg az alap blob nem frissül.|Az alap blob-és assiociated-Pillanatképek esetében díjat számítunk fel. Az egyes Pillanatképek esetében további díjakat számítunk fel.
|A blob Storage tárolási szintjei|Gyakori, ritka elérésű és archív tárolási szintek.|Még nem támogatott.
|A blob Storage-hoz készült Soft delete|Általánosan elérhető|Még nem támogatott.
|Oldal blobjának maximális mérete|8 TB|1 TB
|Oldal blob-oldalának mérete|512 bájt|4 KB
|Tábla partíciós kulcsának és a sor kulcsának mérete|1 024 karakter (2 048 bájt)|400 karakter (800 bájt)
|BLOB pillanatképe|Egy blob pillanatképének maximális száma nincs korlátozva.|Egy blob pillanatképének maximális száma 1 000.
|Azure AD-hitelesítés a Storage-hoz|Előzetes verzióban|Még nem támogatott.
|Megváltoztathatatlan Blobok|Általánosan elérhető|Még nem támogatott.
|Tűzfal-és virtuális hálózati szabályok a tároláshoz|Általánosan elérhető|Még nem támogatott.|

A tárolási metrikákkal kapcsolatban is különbségek vannak:

* A tárolási mérőszámokban lévő tranzakciós adatok nem különböztetik meg a belső vagy külső hálózati sávszélességet.
* A tárolási metrikákban lévő tranzakciós adatok nem tartalmazzák a csatlakoztatott lemezekhez tartozó virtuális gépek elérését.

## <a name="api-version"></a>API-verzió

A Azure Stack hub Storage a következő verziókat támogatja:

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
- [2015-05-01 – előzetes verzió](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Korábbi verziók:

- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01 – előzetes verzió](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="powershell-version"></a>PowerShell-verzió

A Storage modul PowerShell-hez vegye figyelembe a REST API kompatibilis verziót. 

| Modul | Támogatott verzió | Használat |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure.Storage | [4.5.0](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0) | Blobok, várólisták, táblák Azure Stack hub Storage-fiókokban való kezelése |
| AzureRM.Storage | [5.0.4](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.4) | Storage-fiókok létrehozása és kezelése Azure Stack központban |


További információ a Azure Stack hub támogatott Storage-kódtárakkal kapcsolatban: Ismerkedés [a Azure stack hub Storage fejlesztői eszközeivel](azure-stack-storage-dev.md).

## <a name="next-steps"></a>Következő lépések

* [Ismerkedés a Azure Stack hub Storage fejlesztői eszközeivel](azure-stack-storage-dev.md)
* [Adatátviteli eszközök használata az Azure Stack hub Storage szolgáltatáshoz](azure-stack-storage-transfer.md)
* [Az Azure Stack hub Storage bemutatása](azure-stack-storage-overview.md)
