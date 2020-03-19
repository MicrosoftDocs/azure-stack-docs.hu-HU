---
title: Azure Stack hub-napló és az ügyfelek adatkezelése
description: Ismerje meg, hogyan gyűjti a Azure Stack hub az ügyféladatokat és az információkat.
author: JustinHall
ms.topic: article
ms.date: 02/24/2020
ms.author: justinha
ms.reviewer: chengwei
ms.lastreviewed: 02/24/2020
ms.openlocfilehash: fc7d819bce237b98c359bc7c4bc43bc478d84952
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2020
ms.locfileid: "79512575"
---
# <a name="azure-stack-hub-log-and-customer-data-handling"></a>Azure Stack hub-napló és az ügyfelek adatkezelése 

Ahhoz, hogy a Microsoft az Azure Stack hub-vel kapcsolatban a személyes adat processzora vagy alfolyamata legyen, a Microsoft minden ügyfelünk számára elérhetővé teszi a következő kötelezettségvállalásokat: 2018. május 25.

- A személyes adatainak feldolgozása; GDPR "az [online szolgáltatások használati feltételei](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)" adatvédelmi feltételek "szakaszában.
- Az Európai Unió Általános adatvédelmi rendelet az [online szolgáltatások használati feltételeinek](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)4. mellékletében foglalt feltételeket.

Ahogy Azure Stack hub található az ügyfél-adatközpontokban, a Microsoft az adatkezelő kizárólag a Microsofttal közösen a [diagnosztika](azure-stack-diagnostic-log-collection-overview-tzl.md), a [telemetria](azure-stack-telemetry.md)és a [számlázás](azure-stack-usage-reporting.md)útján megosztva tárolt adat.  

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
A támogatási folyamat részeként Azure Stack hub-operátorok [megoszthatják a diagnosztikai naplókat](azure-stack-diagnostic-log-collection-overview-tzl.md) a Azure stack hub támogatási és mérnöki csapatával, hogy segítséget nyújtson a hibaelhárításhoz.

A Microsoft egy eszközt és parancsfájlt biztosít az ügyfeleknek a kért diagnosztikai naplófájlok gyűjtéséhez és feltöltéséhez. Az adatgyűjtés után a rendszer a naplófájlokat HTTPS-védelemmel ellátott, titkosított kapcsolaton keresztül továbbítja a Microsoftnak. Mivel a HTTPS biztosítja a titkosítást a hálózaton keresztül, nincs szükség jelszóra az átvitelhez szükséges titkosításhoz. A fogadást követően a rendszer titkosítja és tárolja a naplókat, amíg a támogatási eset bezárása után 90 nappal automatikusan törlődnek.

## <a name="telemetry-data"></a>Telemetria-adathalmazok
[Azure stack hub telemetria](azure-stack-telemetry.md) automatikusan feltölti a rendszeradatokat a Microsoftnak a csatlakoztatott felhasználói felületen keresztül. Azure Stack hub-operátorok rendelkeznek a telemetria szolgáltatásainak és adatvédelmi beállításainak bármikori testreszabásához szükséges vezérlőkkel.

A Microsoft nem szeretne bizalmas adatokat gyűjteni, például hitelkártyaszám, felhasználónevek és jelszavak, e-mail-címek és így tovább. Ha azt állapítjuk meg, hogy a bizalmas adatokat véletlenül fogadták, töröljük.

## <a name="billing-data"></a>Számlázási információk
[Azure stack hub számlázása](azure-stack-usage-reporting.md) a globális Azure számlázási és használati folyamatát használja, ezért a Microsoft megfelelőségi irányelveinek megfelelően van összhangban.

Azure Stack hub-operátorok a használati adatok Azure-ba való továbbításához konfigurálhatják Azure Stack hub-t a számlázáshoz. Ez a konfiguráció szükséges az olyan Azure Stack hub-beli integrált rendszerek ügyfelei számára, akik az Ön által használt fizetési számlázási modellt választják. A használati jelentések a telemetria függetlenül vannak szabályozva, és nem szükségesek az olyan integrált rendszerű ügyfelek számára, akik a kapacitás modellt vagy Azure Stack Development Kit felhasználókat választják. Ezekben a forgatókönyvekben a használati jelentéskészítés kikapcsolható [a regisztrációs parancsfájl](azure-stack-usage-reporting.md)használatával.


## <a name="next-steps"></a>Következő lépések 
[További információ a Azure Stack hub biztonságáról](azure-stack-security-foundations.md) 
