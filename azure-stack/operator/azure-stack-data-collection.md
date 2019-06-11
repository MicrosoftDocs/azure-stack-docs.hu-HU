---
title: Azure Stack-naplók és adatkezelés |} A Microsoft Docs
description: Ismerje meg az Azure Stack hogyan adatokat gyűjt.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: chengwei
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 9da7ced492a86cb97a1b90a2224383d834073423
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828346"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Az Azure Stack napló- és felhasználói adatok kezelése 
*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*  

A mértékben a Microsoft a processzor- és a személyes adatok a gyűjthetünk Azure Stack subprocessor, minden ügyfelünk számára, hatékony teszi a Microsoft 25 2018 május, a kötelezettségvállalás az (a) a személyes adatok; "feldolgozása Általános adatvédelmi rendelet"kiépítés"Adatvédelmi feltételeket"szakaszának a [Online szolgáltatások használati feltételeit](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) és (b) a az Európai Unió általános adatvédelmi rendelet feltételeket melléklet 4 a [Online szolgáltatások használati feltételeit](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). 

Azure Stack vevő adatközpontokban található, mint a Microsoft a keresztül megosztott adatok, kizárólag az adatkezelő [diagnosztikai](azure-stack-diagnostics.md), [Telemetriai](azure-stack-telemetry.md), és [Számlázási](azure-stack-usage-reporting.md).  

## <a name="data-access-controls"></a>Hozzáférés-vezérlést 
Microsoft-alkalmazottak számára, aki hozzá van rendelve egy adott esetet vizsgálatára, a titkosított adatok csak olvasási hozzáféréssel fog kapni. Microsoft-alkalmazottak számára is törli az adatokat, szükség esetén használt eszközök eléréséhez. Összes hozzáférést az ügyféladatokhoz naplózza, és bejelentkezett.  

Adatok hozzáférés-vezérlés:
1.  Csak őrizzük meg legfeljebb 90 nap elteltével eset zárja be az adatokat.
2.  Az ügyfél mindig a legfrissebb adatokat eltávolítja az adott 90 napos időszakban bármikor lehetősége van.
3.  Microsoft-alkalmazottak számára az adatok-eseti alapon kapjanak hozzáférést, és segítségével igény szerint csak a hiba elhárításához támogatási. 
4.  Az esemény, a Microsoft kell az ügyféladatokat osztja a OEM-partnerekkel való ügyfél beleegyezése megadása kötelező.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>Adatok tulajdonos kérelmek (DSR) vezérlők do ügyfeleink van?
Ahogy korábban említettük, a Microsoft támogatja az igény szerinti Adattörlés ügyfél kérelmenként. Ügyfelek kérheti, hogy a támogatási szakember egy adott esetben az összes naplók törlése bármikor, az ügyfél választja, mielőtt az adat véglegesen törlődik.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>Nem Microsoft ügyfelek értesítése az adatok törlése?
Az automatikus törlés művelet (90 nap után zárja be a eset) azt nem proaktív módon ügyfelekkel és értesíteni őket a törlést. 

Az igény szerinti adatok törlés művelet a Microsoft támogatási szakértő hozzáfér az eszközt, ahol kezdeményezhetnek, hogy az adatok törlése az igény szerinti és azok megadhatják megerősítést a telefonján az ügyfélnél Amikor kész van.

## <a name="diagnostic-data"></a>Diagnosztikai adatok
A támogatási folyamat részeként az Azure Stack-operátorok is [diagnosztikai naplók megosztása](azure-stack-diagnostics.md) Azure Stack támogatása és mérnöki csapataival a hibaelhárítás megkönnyítése érdekében.

A Microsoft biztosít olyan eszköz, és parancsfájl gyűjtése, és töltse fel az ügyfelek számára a kért diagnosztikai naplófájlok. Követően a naplófájlok továbbítja a HTTPS védett a Microsoftnak titkosított kapcsolaton. HTTPS a titkosítást biztosít a hálózaton keresztül, mert nincs jelszavát nem szükséges a titkosítás az átvitel során. Miután érkeznek, naplók titkosítva, és tárolja, amíg azok a lezárt támogatási kérés után 90 nappal automatikusan törlődnek.

## <a name="telemetry-data"></a>Telemetriai adatok
[Az Azure Stack-telemetria](azure-stack-telemetry.md) automatikusan feltölti a rendszer adatokat a Microsoft csapatához, a kapcsolódó felhasználói élményt. Az Azure Stack-operátorok bármikor telemetriafunkciók és adatvédelmi beállítások testreszabása vezérlők rendelkezik.

A Microsoft nem kíván a bizalmas adatokat, például hitelkártyaszámokat tartalmazó, felhasználónevek és jelszavak, e-mail-címeket vagy hasonló bizalmas adatokat gyűjteni. Ha azt állapítja meg, hogy bizalmas adatok véletlenül érkezett, hogy törölje azt. 

## <a name="billing-data"></a>A számlázás
[Az Azure Stack számlázási](azure-stack-usage-reporting.md) globális Azure-számlázás és felhasználás folyamat, és ezért a Microsoft megfelelőségi irányelveinek ciklustól használja.

Az Azure Stack-operátorok konfigurálhatja az Azure Stack használatával továbbítja a használati adatokat az Azure-bA a számlázás. Ez azért szükséges, a több csomópontos Azure Stack ajánljuk, akik használatalapú mint-akkor-használható számlázási modellt. Használati jelentések szabályozott egymástól függetlenül telemetriai adatokból, és, nem szükséges, több csomópontos ajánljuk, akik a kapacitás modellt vagy az Azure Stack Development Kit felhasználókhoz. Ebben az esetben használati jelentések is ki legyen kapcsolva használatával [a regisztrációs parancsfájl](azure-stack-usage-reporting.md).


## <a name="next-steps"></a>További lépések 
[További tudnivalók az Azure Stack biztonságáról](azure-stack-security-foundations.md) 
