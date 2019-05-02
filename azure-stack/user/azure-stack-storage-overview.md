---
title: A Microsoft Azure Stack storage bemutatása
description: Azure Stack-tárolás
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 10cb9a2b1449c8cb280bf4787b009b6d2eb21619
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64299558"
---
# <a name="introduction-to-azure-stack-storage"></a>A Microsoft Azure Stack storage bemutatása

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

## <a name="overview"></a>Áttekintés

Az Azure Stack Storage egy felhőalapú tárolási szolgáltatásokról, amely tartalmazza a Blobok, táblák és üzenetsorok, amelyek az Azure Storage-szolgáltatások.

## <a name="azure-stack-storage-services"></a>Az Azure Stack-tárolási szolgáltatások

Az Azure Stack storage a következő három szolgáltatást biztosítja:

- **Blob Storage**

    A BLOB storage a strukturálatlan objektumadatokat tárolja. Egy blob állhat bármilyen szövegből vagy bináris adatból, lehet például egy dokumentum, egy médiafájl vagy egy alkalmazástelepítő.

- **Table Storage**

    A TABLE storage strukturált adatkészleteket tárolja. A Table Storage a NoSQL-kulcsattribútumok adattára, amely gyors fejlesztési lehetőségeket és nagy adatmennyiségek gyors elérését biztosítja.

- **Queue Storage**

    A Queue storage megbízható üzenetküldést, a munkafolyamat-feldolgozáshoz és a felhőszolgáltatás összetevői közötti kommunikációhoz biztosít.

Az Azure Stack tárfiókok olyan biztonságos fiók, amely lehetővé teszi az Azure Stack Storage szolgáltatásaihoz való hozzáférést. A tárfiók biztosítja az egyedi névteret a tárterület erőforrásainak. Az alábbi ábrán látható, a storage-fiókban, az Azure Stack storage erőforrásainak kapcsolatai:

![Az Azure Stack Storage áttekintése](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Blob Storage

Felhasználók, a nagy mennyiségű strukturálatlan Objektumadat tárolása a felhőben a blob storage-bA egy hatékony és méretezhető megoldást kínál. Tartalom tárolásához például használhatja a blob storage-bA:

- Dokumentumok
- Közösségi adatok (fényképek, videók, zene és blogok)
- Fájlok, számítógépek, adatbázisok és eszközök biztonsági másolatai
- Webalkalmazásokhoz tartozó képek és szövegek
- Konfigurációs adatok a felhőalapú alkalmazásokhoz
- Big data (naplók és egyéb nagy adatkészletek)

Minden blob egy tárolóba van rendezve. A tárolók nagy előnye, hogy az objektumok csoportjaihoz biztonsági házirendeket lehet rendelni. Egy tárfiók tetszőleges számú tárolót tartalmazhat, és a egy tároló korlátlan számú blobot a korlátig tárfiók is tartalmazhat.

A BLOB storage háromféle blobot biztosít:

- **Blokkblobok**

    A blokkblobok streamelési és a felhő típusú objektumok tárolására vannak optimalizálva, és alkalmasak tárolására, dokumentumok, médiafájlok, biztonsági mentések és stb.

- **Hozzáfűző blobok**

    A hozzáfűző blobok a blokkblobokhoz hasonló, de a műveletek hozzáfűzésére optimalizált blobok. A naplóíró blobok csak a végükhöz hozzáadott új blokkal frissíthetők. A naplóíró blobok olyan forgatókönyvekhez felelnek meg leginkább, mint például a naplózás, ahol az új adatokat csak a blob végéhez kell hozzáírni.

- **Lapblobok**

    Lapblobok infrastruktúra-szolgáltatási lemezek megjelenítésére vannak optimalizálva, és ez akár 1 TB méretű támogató véletlenszerű írások. Az Azure Stack-virtuális gép IaaS lemez egy lapblobként tárolt VHD csatolva.

### <a name="table-storage"></a>Table Storage

A modern alkalmazásokhoz gyakran az előző generációs szoftvereknél jobban méretezhető és rugalmasabb adattárolók szükségesek. A Table Storage magas rendelkezésre állású, nagymértékben méretezhető tárolót kínál, így alkalmazása a felhasználói igények függvényében automatikusan átméretezheti magát. A TABLE storage a Microsoft NoSQL kulcs-/ attribútumtár – rendelkezik egy séma nélküli Tervező, kivitelezésében különbözik a hagyományos relációs adatbázisoktól. A séma nélküli adattárral az adatok könnyedén alkalmazkodnak az alkalmazás igényeinek fejlődéséhez. A Table Storage könnyen használható, így a fejlesztők gyorsan létrehozhatják benne alkalmazásaikat.

A TABLE storage egy kulcs-/ attribútumtár, ami azt jelenti, hogy egy típusos tulajdonságnévvel tárolja egy tábla minden egyes értékhez. A tulajdonságnév felhasználható szűréshez vagy a kiválasztási feltételek megadásához is. A tulajdonságok és értékeik gyűjteménye egy entitást alkot. Mivel a table storage séma nélküli, ugyanaz a tábla két entitása a Tulajdonságok különböző gyűjteményeit is tartalmazhat, és azokat a tulajdonságokat, különböző típusú is lehet.

A table storage segítségével rugalmas adatkészleteket tárolhat, például webalkalmazások, címjegyzékek, eszközadatokat és bármilyen más típusú metaadatokat, amelyek a szolgáltatásnak szüksége van a felhasználói adatok. A mai internetalapú alkalmazások például a table storage NoSQL-adatbázisok a hagyományos relációs adatbázisok népszerű alternatíváját kínálják.

Egy tárfiók korlátlan számú táblát tartalmazhat, és a egy táblát tartalmazhat korlátlan számú entitást, a tárfiók kapacitásán belül.

### <a name="queue-storage"></a>Queue Storage

A méretezhető alkalmazások tervezésekor az alkalmazás összetevői gyakran le vannak választva, hogy egymástól függetlenül lehessen őket méretezni. Queue storage megbízható üzenetkezelési megoldást biztosít a alkalmazás-összetevők közötti aszinkron kommunikációhoz a felhőben, az asztalon, egy helyszíni kiszolgálón vagy egy mobileszközön futnak-e. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

Egy tárfiók tetszőleges számú üzenetsort tartalmazhat, és a egy üzenetsor korlátlan számú üzenetet, a tárfiók kapacitásán belül is tartalmazhat. Az egyes üzenetek akár a 64 KB-os méretet is elérhetik.

## <a name="next-steps"></a>További lépések

- [Azure-konzisztens tároló: különbségek és szempontok](azure-stack-acs-differences.md)

- Az Azure Storage szolgáltatással kapcsolatos további tudnivalókért lásd: [a Microsoft Azure Storage bemutatása](/azure/storage/common/storage-introduction)
