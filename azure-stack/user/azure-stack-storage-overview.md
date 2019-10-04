---
title: A Azure Stack Storage bemutatása | Microsoft Docs
description: Ismerkedjen meg Azure Stack Storage-szolgáltatásokkal.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: dfa9666387093f60dfd41e371707dff23119dc9a
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909592"
---
# <a name="introduction-to-azure-stack-storage"></a>A Azure Stack Storage bemutatása

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

## <a name="overview"></a>Áttekintés

Azure Stack Storage az Azure Storage által biztosított szolgáltatásokkal konzisztens felhőalapú tárolási szolgáltatások készlete. Ezek a szolgáltatások blobokat, táblákat és várólistákat tartalmaznak.

## <a name="azure-stack-storage-services"></a>Azure Stack Storage-szolgáltatások

Azure Stack Storage a következő három szolgáltatást biztosítja:

- **Blob Storage**

    A blob Storage strukturálatlan objektumokat tárol. A Blobok bármilyen típusú szöveg-vagy bináris adattípust tartalmazhatnak, például dokumentumot, médiafájlt vagy az alkalmazás telepítőjét.

- **Table Storage**

    A Table Storage a strukturált adatkészleteket tárolja. A Table Storage egy NoSQL-attribútum, amely lehetővé teszi a gyors fejlesztést és a nagy mennyiségű adattal való gyors hozzáférést.

- **Queue Storage**

    A üzenetsor-tárolás megbízható üzenetküldést biztosít a munkafolyamat-feldolgozáshoz és a Cloud Services összetevői közötti kommunikációhoz.

Az Azure Stack Storage-fiók egy biztonságos fiók, amely hozzáférést biztosít a Azure Stack-tárolóban található szolgáltatásokhoz. A tárfiók biztosítja az egyedi névteret a tárterület erőforrásainak. A következő ábrán a Storage-fiók Azure Stack tárolási erőforrásai közötti kapcsolatok láthatók:

![Azure Stack Storage – áttekintés](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

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

    Az oldal Blobok a IaaS-lemezek ábrázolására vannak optimalizálva, és a véletlenszerű írásokat legfeljebb 1 TB méretűre támogatják. Az Azure Stack virtuális gép csatolt IaaS lemeze egy lapozófájlként tárolt VHD.

### <a name="table-storage"></a>Táblatároló

A modern alkalmazások gyakran olyan adattárakat igényelnek, amelyek nagyobb skálázhatóságot és rugalmasságot igényelnek, mint a szoftver korábbi generációja. A Table Storage a nagy rendelkezésre állású, nagy mértékben méretezhető tárolást biztosít, így az alkalmazás automatikusan méretezhető a felhasználói igények kielégítése érdekében. A Table Storage a Microsoft NoSQL kulcs-és attribútum-tárolója, amely séma nélküli kialakítással rendelkezik, így eltér a hagyományos és a hagyományos kapcsolatok adatbázisaitól. A séma nélküli adattárral egyszerűen alkalmazkodhat az adataihoz az alkalmazás igényeinek megfelelően. A Table Storage egyszerűen használható, így a fejlesztők gyorsan hozhatnak létre alkalmazásokat.

A Table Storage egy kulcs-attribútum tároló, ami azt jelenti, hogy egy tábla minden értékét egy begépelt tulajdonságnév tárolja. A tulajdonság neve a szűréshez és a kiválasztási feltételek megadásához használatos. A tulajdonságok és értékeik gyűjteménye egy entitást alkot. Mivel a Table Storage séma nélküli, az ugyanabban a táblában lévő két entitás különböző tulajdonságokat tartalmazhat, és ezek a tulajdonságok különböző típusúak lehetnek.

A Table Storage használatával rugalmas adatkészleteket tárolhat, például a webalkalmazások felhasználói adatait, a címjegyzékeket, az eszköz adatait, valamint a szolgáltatás által igényelt más típusú metaadatokat. A mai internetalapú alkalmazások esetében a NoSQL-adatbázisok (például a Table Storage) népszerű alternatívát kínálnak a hagyományos, összehasonlítható adatbázisok számára.

A Storage-fiók tetszőleges számú táblát tartalmazhat, és egy tábla tetszőleges számú entitást tartalmazhat, a Storage-fiók kapacitási korlátján felül.

### <a name="queue-storage"></a>Queue Storage

A méretezhető alkalmazások tervezésekor az alkalmazás-összetevők gyakran le vannak választva, így egymástól függetlenül méretezhetők. A várólista-tároló megbízható üzenetkezelési megoldást biztosít az alkalmazás-összetevők közötti aszinkron kommunikációhoz, függetlenül attól, hogy a felhőben, az asztalon, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

A Storage-fiókok tetszőleges számú várólistát tartalmazhatnak, és a várólista tetszőleges számú üzenetet tartalmazhat (a Storage-fiók kapacitási korlátja alapján). Az egyes üzenetek akár a 64 KB-os méretet is elérhetik.

## <a name="next-steps"></a>További lépések

- [Azure-konzisztens tárolás: különbségek és megfontolások](azure-stack-acs-differences.md)

- További információ az Azure Storage-ról: [Bevezetés a Microsoft Azure Storageba](/azure/storage/common/storage-introduction)
