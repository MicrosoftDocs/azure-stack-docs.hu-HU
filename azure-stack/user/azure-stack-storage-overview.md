---
title: Az Azure Stack hub Storage bemutatása
description: Ismerkedjen meg Azure Stack hub tárolási szolgáltatásaival.
author: mattbriggs
ms.topic: conceptual
ms.date: 5/27/2020
ms.author: mabrigg
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: d45b8069915ea920d6366daeefdfb53953301102
ms.sourcegitcommit: db3c9179916a36be78b43a8a47e1fd414aed3c2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2020
ms.locfileid: "84146988"
---
# <a name="introduction-to-azure-stack-hub-storage"></a>Az Azure Stack hub Storage bemutatása

## <a name="overview"></a>Áttekintés

Az Azure Stack hub Storage az Azure Storage által biztosított szolgáltatásokkal konzisztens felhőalapú tárolási szolgáltatások készlete. Ezek a szolgáltatások blobokat, táblákat és várólistákat tartalmaznak.

## <a name="azure-stack-hub-storage-services"></a>Azure Stack hub Storage Services

Azure Stack hub Storage a következő három szolgáltatást nyújtja:

- **Blob Storage**

    A blob Storage strukturálatlan objektumokat tárol. A Blobok bármilyen típusú szöveg-vagy bináris adattípust tartalmazhatnak, például dokumentumot, médiafájlt vagy az alkalmazás telepítőjét.

- **Table Storage**

    A Table Storage a strukturált adatkészleteket tárolja. A Table Storage egy NoSQL-attribútum, amely lehetővé teszi a gyors fejlesztést és a nagy mennyiségű adattal való gyors hozzáférést.

- **Üzenetsor-tárolás**

    A üzenetsor-tárolás megbízható üzenetküldést biztosít a munkafolyamat-feldolgozáshoz és a Cloud Services összetevői közötti kommunikációhoz.

Az Azure Stack hub Storage-fiók egy biztonságos fiók, amely hozzáférést biztosít a Azure Stack hub Storage szolgáltatásaihoz. A tárfiók biztosítja az egyedi névteret a tárterület erőforrásainak. A következő ábra a Azure Stack hub tárolási erőforrásai közötti kapcsolatokat mutatja be egy Storage-fiókban:

![Azure Stack hub Storage – áttekintés](media/azure-stack-storage-overview/azurestackstorageoverview.svg)

### <a name="blob-storage"></a>Blob Storage

Az olyan felhasználók számára, akik nagy mennyiségű strukturálatlan objektumot tárolnak a felhőben, a blob Storage hatékony és méretezhető megoldást kínál. A blob Storage a következő tartalmak tárolására használható:

- Dokumentumok
- Közösségi adatok (fényképek, videók, zene és blogok)
- Fájlok, számítógépek, adatbázisok és eszközök biztonsági másolatai
- Képek és szöveg webes alkalmazásokhoz
- A felhőalapú alkalmazások konfigurációs adatkészletei
- Big data (naplók és egyéb nagy adatkészletek)

Minden blob egy tárolóba van rendezve. A tárolók nagy előnye, hogy az objektumok csoportjaihoz biztonsági házirendeket lehet rendelni. A Storage-fiókok tetszőleges számú tárolót tartalmazhatnak, és a tárolók tetszőleges számú blobot tartalmazhatnak (legfeljebb a Storage-fiókra korlátozva).

A blob Storage háromféle blobot kínál:

- **Blobok letiltása**

    A blokkos Blobok a Felhőbeli objektumok folyamatos átvitelére és tárolására vannak optimalizálva. A dokumentumok, médiafájlok, biztonsági másolatok és más hasonló fájlok tárolására jó választás.

- **Blobok hozzáfűzése**

    A hozzáfűző blobok a blokkblobokhoz hasonló, de a műveletek hozzáfűzésére optimalizált blobok. A naplóíró blobok csak a végükhöz hozzáadott új blokkal frissíthetők. A naplóíró blobok olyan forgatókönyvekhez felelnek meg leginkább, mint például a naplózás, ahol az új adatokat csak a blob végéhez kell hozzáírni.

- **Oldal Blobok**

    Az oldal Blobok a IaaS-lemezek ábrázolására vannak optimalizálva, és a véletlenszerű írásokat legfeljebb 1 TB méretűre támogatják. Az Azure Stack hub virtuális géphez csatolt IaaS lemez egy blobként tárolt VHD.

### <a name="table-storage"></a>Table Storage

A modern alkalmazások gyakran olyan adattárakat igényelnek, amelyek nagyobb skálázhatóságot és rugalmasságot igényelnek, mint a szoftver korábbi generációja. A Table Storage a nagy rendelkezésre állású, nagy mértékben méretezhető tárolást biztosít, így az alkalmazás automatikusan méretezhető a felhasználói igények kielégítése érdekében. A Table Storage a Microsoft NoSQL kulcs-és attribútum-tárolója, amely séma nélküli kialakítással rendelkezik, így eltér a hagyományos és a hagyományos kapcsolatok adatbázisaitól. A séma nélküli adattárral egyszerűen alkalmazkodhat az adataihoz az alkalmazás igényeinek megfelelően. A Table Storage egyszerűen használható, így a fejlesztők gyorsan hozhatnak létre alkalmazásokat.

A Table Storage egy kulcs-attribútum tároló, ami azt jelenti, hogy egy tábla minden értékét egy begépelt tulajdonságnév tárolja. A tulajdonság neve a szűréshez és a kiválasztási feltételek megadásához használatos. A tulajdonságok és értékeik gyűjteménye egy entitást alkot. Mivel a Table Storage séma nélküli, az ugyanabban a táblában lévő két entitás különböző tulajdonságokat tartalmazhat, és ezek a tulajdonságok különböző típusúak lehetnek.

A Table Storage használatával rugalmas adatkészleteket tárolhat, például a webalkalmazások felhasználói adatait, a címjegyzékeket, az eszköz adatait, valamint a szolgáltatás által igényelt más típusú metaadatokat. A mai internetalapú alkalmazások esetében a NoSQL-adatbázisok (például a Table Storage) népszerű alternatívát kínálnak a hagyományos, összehasonlítható adatbázisok számára.

A Storage-fiók tetszőleges számú táblát tartalmazhat, és egy tábla tetszőleges számú entitást tartalmazhat, a Storage-fiók kapacitási korlátján felül.

### <a name="queue-storage"></a>Queue Storage

A méretezhető alkalmazások tervezésekor az alkalmazás-összetevők gyakran le vannak választva, így egymástól függetlenül méretezhetők. A várólista-tároló megbízható üzenetkezelési megoldást biztosít az alkalmazás-összetevők közötti aszinkron kommunikációhoz, függetlenül attól, hogy a felhőben, az asztalon, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

A Storage-fiókok tetszőleges számú várólistát tartalmazhatnak, és a várólista tetszőleges számú üzenetet tartalmazhat (a Storage-fiók kapacitási korlátja alapján). Az egyes üzenetek akár a 64 KB-os méretet is elérhetik.

## <a name="next-steps"></a>Következő lépések

- [Azure-konzisztens tárolás: különbségek és megfontolások](azure-stack-acs-differences.md)

- További információ az Azure Storage-ról: [Bevezetés a Microsoft Azure Storageba](/azure/storage/common/storage-introduction)
