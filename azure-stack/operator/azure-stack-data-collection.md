---
title: A naplók és az adatkezelés Azure Stacka | Microsoft Docs
description: További információ a Azure Stack információk gyűjtéséről.
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
ms.openlocfilehash: 495b75359cb8c859e532885a1c9fa284691bd90f
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493814"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>A naplók és az ügyfelek adatkezelésének Azure Stack 
*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*  

Ahhoz, hogy a Microsoft a Azure Stack-mel kapcsolatos személyes adatfeldolgozót vagy alfeldolgozót is feldolgozza, a Microsoft minden ügyfelének, a személyes adat feldolgozásának (a) a (a) részében foglalt kötelezettségeknek megfelelően 2018. GDPR "az [online](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) szolgáltatások feltételeinek és (b) az európai Unió általános adatvédelmi rendelet az [online szolgáltatások használati feltételeinek](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)4. mellékletében foglalt" adatvédelmi feltételek "című szakaszának kiépítése. 

Ahogy Azure Stack az ügyfél-adatközpontokban található, a Microsoft az adatkezelő kizárólag a Microsofttal közösen a [diagnosztika](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep), a [telemetria](azure-stack-telemetry.md)és a [számlázás](azure-stack-usage-reporting.md)útján megosztva tárolt adat.  

## <a name="data-access-controls"></a>Adathozzáférési vezérlők 
Az adott támogatási eset kivizsgálásához hozzárendelt Microsoft-alkalmazottak csak olvasási hozzáférést kapnak a titkosított adathoz. A Microsoft alkalmazottai is hozzáférhetnek az adattörléshez használt eszközökhöz, ha szükségesek. A rendszer naplózza és naplózza az ügyféladatok összes hozzáférését.  

Adathozzáférési vezérlők:
- Az adat csak a kis-és nagybetűk zárása után 90 napig tartható meg.
- Az ügyfélnek mindig lehetősége van arra, hogy a 90 napos időszakban bármikor eltávolítsa az adatvesztést.
- A Microsoft alkalmazottai eseti alapon, és csak a támogatási probléma megoldásához szükséges módon férhetnek hozzá az adathoz. 
- Abban az esetben, ha a Microsoftnak meg kell osztania az ügyféladatokat az OEM-partnerekkel, az ügyfél beleegyezik a kötelező.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>Milyen adattulajdonosi kérelmek (DSR) típusú vezérlőkkel rendelkeznek az ügyfelek?
Ahogy azt korábban említettük, a Microsoft az igény szerinti adattörlést is támogatja az ügyfelek kérésére. Az ügyfelek kérhetik, hogy a támogatási szakemberük az adott esethez tartozó naplókat az ügyfél választása szerint bármikor törölje az adat végleges törlése előtt.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>A Microsoft értesíti az ügyfeleket az adattörlésről?
Az automatikus adattörlési művelethez (90 nappal az eset lezárása után) nem proaktív módon kell felvenni a kapcsolatot az ügyfelekkel, és értesíteni őket a törlésről. 

Az igény szerinti adattörlési művelethez a Microsoft támogatási szakembere hozzáfér az eszközhöz, ahol igény szerint elindíthatják az adattörlést, és megerősítik a telefont az ügyféllel, ha elkészült.

## <a name="diagnostic-data"></a>Diagnosztikai adatszolgáltatások
A támogatási folyamat részeként Azure Stack operátorok megoszthatják a [diagnosztikai naplókat](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep) Azure stack támogatási és mérnöki csapatokkal a hibaelhárítás érdekében.

A Microsoft egy eszközt és parancsfájlt biztosít az ügyfeleknek a kért diagnosztikai naplófájlok gyűjtéséhez és feltöltéséhez. Az adatgyűjtés után a rendszer a naplófájlokat HTTPS-védelemmel ellátott, titkosított kapcsolaton keresztül továbbítja a Microsoftnak. Mivel a HTTPS biztosítja a titkosítást a hálózaton keresztül, nincs szükség jelszóra az átvitelhez szükséges titkosításhoz. A fogadást követően a rendszer titkosítja és tárolja a naplókat, amíg a támogatási eset bezárása után 90 nappal automatikusan törlődnek.

## <a name="telemetry-data"></a>Telemetria-adathalmazok
[Azure stack a telemetria](azure-stack-telemetry.md) automatikusan feltölti a rendszeradatokat a Microsoftnak a csatlakoztatott felhasználói felületen keresztül. Azure Stack operátorok rendelkeznek a telemetria szolgáltatásainak és adatvédelmi beállításainak bármikori testreszabásához szükséges vezérlőkkel.

A Microsoft nem szeretne bizalmas adatokat gyűjteni, például hitelkártyaszám, felhasználónevek és jelszavak, e-mail-címek vagy hasonló bizalmas információk gyűjtésére. Ha azt állapítjuk meg, hogy a bizalmas adatokat véletlenül fogadták, töröljük. 

## <a name="billing-data"></a>Számlázási információk
[Azure stack számlázás](azure-stack-usage-reporting.md) a globális Azure számlázási és használati folyamatát használja, ezért a Microsoft megfelelőségi irányelveinek megfelelően van összhangban.

Azure Stack operátorok a használati információk Azure-ba történő továbbításához konfigurálhatják Azure Stack a számlázáshoz. Ez olyan többcsomópontos Azure Stack ügyfelek esetében szükséges, akik az Ön által használt fizetési számlázási modellt választják. A használati jelentéseket a telemetria-től függetlenül kell vezérelni, és nem szükséges a több csomópontot használó ügyfelek számára, akik kiválasztják a kapacitás modellt vagy Azure Stack Development Kit felhasználók számára. Ezekben a forgatókönyvekben a használati jelentéskészítés kikapcsolható [a regisztrációs parancsfájl](azure-stack-usage-reporting.md)használatával.


## <a name="next-steps"></a>További lépések 
[További tudnivalók az Azure Stack biztonságáról](azure-stack-security-foundations.md) 
