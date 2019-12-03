---
title: A naplók és az ügyfelek adatkezelésének Azure Stacka | Microsoft Docs
description: Megtudhatja, hogyan gyűjti a Azure Stack az ügyféladatok és az információk gyűjtését.
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
ms.openlocfilehash: ff633133b7d0fd0489b3e81295ea53351968ac8f
ms.sourcegitcommit: 7817d61fa34ac4f6410ce6f8ac11d292e1ad807c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74690197"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>A naplók és az ügyfelek adatkezelésének Azure Stack 
*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*  

Ahhoz, hogy a Microsoft a Azure Stack-mel kapcsolatos személyes adatfeldolgozót vagy alfeldolgozót is feldolgozza, a Microsoft a következő kötelezettségvállalásokkal rendelkezik az összes ügyfél számára, amely a 2018. május 25-én lép érvénybe:

- A személyes adatainak feldolgozása; GDPR "az [online szolgáltatások használati feltételei](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)" adatvédelmi feltételek "szakaszában.
- Az Európai Unió Általános adatvédelmi rendelet az [online szolgáltatások használati feltételeinek](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)4. mellékletében foglalt feltételeket.

Ahogy Azure Stack az ügyfél-adatközpontokban található, a Microsoft az adatkezelő kizárólag a Microsofttal közösen a [diagnosztika](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs), a [telemetria](azure-stack-telemetry.md)és a [számlázás](azure-stack-usage-reporting.md)útján megosztva tárolt adat.  

## <a name="data-access-controls"></a>Adathozzáférési vezérlők 
Az adott támogatási eset kivizsgálásához hozzárendelt Microsoft-alkalmazottak csak olvasási hozzáférést kapnak a titkosított adathoz. A Microsoft alkalmazottai is hozzáférhetnek az adattörléshez használt eszközökhöz, ha szükségesek. A rendszer naplózza és naplózza az ügyféladatok összes hozzáférését.  

Adathozzáférési vezérlők:
- Az adat csak a kis-és nagybetűk zárása után 90 napig tart.
- Az ügyfélnek mindig lehetősége van arra, hogy a 90 napos időszakban bármikor eltávolítsa az adatvesztést.
- A Microsoft alkalmazottai eseti alapon, és csak a támogatási probléma megoldásához szükséges módon férhetnek hozzá az adathoz.
- Abban az esetben, ha a Microsoftnak meg kell osztania az ügyféladatokat az OEM-partnerekkel, az ügyfél beleegyezik a kötelező.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>Milyen adattulajdonosi kérelmek (DSR) típusú vezérlőkkel rendelkeznek az ügyfelek?
A Microsoft az igény szerinti adattörlést az ügyfél kérésére támogatja. Az ügyfelek kérhetik, hogy a támogatási mérnökök egyike bármikor törölje az adott esethez tartozó naplókat, mielőtt az adat véglegesen törlődik.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>A Microsoft értesíti az ügyfeleket az adattörlésről?
Az automatikus adattörlési művelethez (a kis-és nagybetűk után 90 nappal) nem proaktív módon kell felvennie az ügyfeleket, és értesítenie kell őket a törlésről.

Az igény szerinti adattörlési művelethez a Microsoft támogatási szakemberei hozzáférhetnek az eszközhöz, amellyel igény szerint törölhetik az adatvesztést. Megerősítik a telefont az ügyféllel, ha elkészült.

## <a name="diagnostic-data"></a>Diagnosztikai adatszolgáltatások
A támogatási folyamat részeként Azure Stack operátorok [megoszthatják a diagnosztikai naplókat](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs) Azure stack támogatási és mérnöki csapatokkal, hogy segítsenek a hibaelhárításban.

A Microsoft egy eszközt és parancsfájlt biztosít az ügyfeleknek a kért diagnosztikai naplófájlok gyűjtéséhez és feltöltéséhez. Az adatgyűjtés után a rendszer a naplófájlokat HTTPS-védelemmel ellátott, titkosított kapcsolaton keresztül továbbítja a Microsoftnak. Mivel a HTTPS biztosítja a titkosítást a hálózaton keresztül, nincs szükség jelszóra az átvitelhez szükséges titkosításhoz. A fogadást követően a rendszer titkosítja és tárolja a naplókat, amíg a támogatási eset bezárása után 90 nappal automatikusan törlődnek.

## <a name="telemetry-data"></a>Telemetria-adathalmazok
[Azure stack a telemetria](azure-stack-telemetry.md) automatikusan feltölti a rendszeradatokat a Microsoftnak a csatlakoztatott felhasználói felületen keresztül. Azure Stack operátorok rendelkeznek a telemetria szolgáltatásainak és adatvédelmi beállításainak bármikori testreszabásához szükséges vezérlőkkel.

A Microsoft nem szeretne bizalmas adatokat gyűjteni, például hitelkártyaszám, felhasználónevek és jelszavak, e-mail-címek és így tovább. Ha azt állapítjuk meg, hogy a bizalmas adatokat véletlenül fogadták, töröljük.

## <a name="billing-data"></a>Számlázási információk
[Azure stack számlázás](azure-stack-usage-reporting.md) a globális Azure számlázási és használati folyamatát használja, ezért a Microsoft megfelelőségi irányelveinek megfelelően van összhangban.

Azure Stack operátorok a használati információk Azure-ba történő továbbításához konfigurálhatják Azure Stack a számlázáshoz. Ez a konfiguráció szükséges az olyan Azure Stack integrált rendszerek ügyfelei számára, akik az Ön által használt fizetési számlázási modellt választják. A használati jelentések a telemetria függetlenül vannak szabályozva, és nem szükségesek az olyan integrált rendszerű ügyfelek számára, akik a kapacitás modellt vagy Azure Stack Development Kit felhasználókat választják. Ezekben a forgatókönyvekben a használati jelentéskészítés kikapcsolható [a regisztrációs parancsfájl](azure-stack-usage-reporting.md)használatával.


## <a name="next-steps"></a>Következő lépések 
[További tudnivalók az Azure Stack biztonságáról](azure-stack-security-foundations.md) 
