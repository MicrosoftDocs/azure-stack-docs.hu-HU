---
title: Az Azure stack tárolási különbségek és szempontok |} A Microsoft Docs
description: Az Azure stack storage és az Azure storage, Azure Stack üzembe helyezési szempontok együtt közötti különbségek megértéséhez.
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
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: 3d1fb9e7a3d25e9eeab0f1f2a0cc1a48ed6112a2
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985983"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Az Azure Stack-tár: Különbségek és szempontok

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack storage a storage cloud services a Microsoft Azure Stackhez. Az Azure Stack storage biztosít a blob, table, queue és fiók felügyeleti funkciók az Azure-konzisztens szemantikáját.

Ez a cikk az Azure Storage szolgáltatások ismert Azure Stack Storage különbségeket foglalja össze. Szintén megfontolandó szempontok az Azure Stack telepítésekor sorolja fel. Globális Azure és az Azure Stack közötti magas szintű különbségek kapcsolatos további információkért tekintse meg a [szempontok kulcs](azure-stack-considerations.md) cikk.

## <a name="cheat-sheet-storage-differences"></a>Hasznos tanácsok: Tárolási különbségek

| Szolgáltatás | Azure (globális) | Azure Stack |
| --- | --- | --- |
|File Storage|Felhőalapú SMB-fájlmegosztások támogatott|Még nem támogatott
|Az Azure storage service encryption az inaktív adatok|256 bites AES-titkosítás. Támogatja a titkosítást, felhasználó által kezelt kulcsok használata a Key Vaultban.|BitLocker 128 bites AES-titkosítást. Titkosítás az ügyfél által kezelt kulcsok használata nem támogatott.
|Tárfiók típusa|Általános célú V1, V2 és Blob storage-fiókok|Csak általános célú V1.
|Replikációs beállítások|Helyileg redundáns tárolás, georedundáns tárolás, georedundáns írásvédett tárolás és zónaredundáns tárolás|Helyileg redundáns tárolás.
|Prémium szintű Storage|Teljes mértékben támogatott.|Bővítheti, de nincs teljesítményszint vagy garantálja.
|Felügyelt lemezek|Prémium és standard szintű támogatott|1808 vagy újabb verzió használata esetén támogatott.
|A blob neve|1024 karakter hosszúságú (2048 bájt)|880 karakter (1,760 bájt)
|Block blob maximális mérete|4,75 TB (100 MB X 50 000 blokk)|Az 1802-es frissítés vagy újabb verzió 4,75 TB (100 MB x 50 000 blokk) 50 000 x 4 MB (KB. 195 GB), a korábbi verziók.
|Blob pillanatkép-másolás lap|Biztonsági mentés nem felügyelt virtuális Géphez csatolt Azure lemezeken futó virtuális gépek támogatott|Még nem támogatott.
|Blob növekményes pillanatkép-másolás lap|Prémium és standard oldala az Azure-blobok támogatott|Még nem támogatott.
|Blob Számlázás lapon|Díjait egyedi lapok attól a blob vagy a pillanatkép. További díjakat csak alap blob frissítése folyamatban van egy blob társított pillanatképekhez nem járna.|Az alap blob és assiociated pillanatképek számítunk fel díjat. Járna minden egyes pillanatkép felül további díjakat.
|A blob Storage tárolási rétegek|Gyakori és ritka elérésű és archív tárolási szintek.|Még nem támogatott.
|A blob Storage a helyreállítható törlés|Általános elérhető|Még nem támogatott.
|Blob maximális mérete|8 TB|1 TB
|Lapblob oldal méretét|512 bájt|4 KB
|Tábla partíciós kulcs és a sor kulcsméret|1024 karakter hosszúságú (2048 bájt)|400 karakter (800 bájt)
|BLOB-pillanatkép|Egy blob pillanatképeinek maximális számát. nem korlátozódik.|Egy blob pillanatképeinek maximális száma 1000.
|Tárolás az Azure AD-hitelesítés|Előzetes verzióban|Még nem támogatott.
|Blobok nem módosítható|Általános elérhető|Még nem támogatott.
|Tűzfal- és tárolási virtuális hálózati szabályok|Általános elérhető|Még nem támogatott.|

A storage-mérőszámok különbségek is vannak:

* A storage-mérőszámok tranzakciós adatok nem tesznek különbséget a belső vagy külső hálózati sávszélességet.
* A tranzakciós adatokat a storage-mérőszámok nem tartalmazza a virtuális gép hozzá a csatlakoztatott lemezeket.

## <a name="api-version"></a>API-verzió

Azure Stack Storage a következő verziók támogatottak:

Az Azure Storage szolgáltatási API-kkal:

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

Az Azure Storage felügyeleti API-k szolgáltatásaihoz:

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

Támogatott az Azure Stack-tároló ügyfélkódtárai kapcsolatos további információkért lásd: [Ismerkedés az Azure Stack tárolásfejlesztési eszközök](azure-stack-storage-dev.md).

## <a name="next-steps"></a>További lépések

* [Ismerkedés az Azure Stack tárolásfejlesztési eszközök](azure-stack-storage-dev.md)
* [Adatok átvitele tools for Azure Stack-tároló használata](azure-stack-storage-transfer.md)
* [Az Azure Stack Storage bemutatása](azure-stack-storage-overview.md)
