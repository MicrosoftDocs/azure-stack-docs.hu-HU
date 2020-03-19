---
title: Azure Stack hub diagnosztikai naplóinak áttekintése
description: A Azure Stack hub Súgó és támogatás szolgáltatásának diagnosztikai naplóinak gyűjtését ismerteti, beleértve az igény szerinti és a proaktív naplózási gyűjteményt is.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: 8f97ecd20e7ef8db69033268baf96060e1315751
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520429"
---
# <a name="overview-of-azure-stack-hub-diagnostic-log-collection"></a>Azure Stack hub diagnosztikai naplók gyűjteményének áttekintése 

Az Azure Stack hub a Windows-összetevők és a helyszíni Azure-szolgáltatások nagy gyűjteménye, amelyek egymással együttműködnek. Ezek az összetevők és szolgáltatások saját naplókat hoznak létre. Ha engedélyezni szeretné, hogy a Microsoft ügyfélszolgálata (CSS) hatékonyan diagnosztizálja a problémákat, zökkenőmentesen használható a diagnosztikai naplók gyűjtése. 

A Súgó és támogatás diagnosztikai naplójának gyűjteménye segíti a kezelők számára a diagnosztikai naplók gyors gyűjtését és megosztását a Microsoft ügyfél-támogatási szolgáltatásaival (CSS), egy egyszerű felhasználói felülettel, amely nem igényel PowerShellt. A naplók gyűjtése akkor is történik, ha más infrastrukturális szolgáltatások nem állnak le.  
 
Javasoljuk, hogy ezt a módszert használja a naplók gyűjtésére, és csak [a Kiemelt jogosultságú végpont (PEP)](azure-stack-get-azurestacklog.md) használatára legyen lehetőség, ha a felügyeleti portál vagy a Súgó és támogatás panel nem érhető el. 

>[!NOTE]
>Azure Stack hub-t regisztrálni kell, és internetkapcsolattal kell rendelkeznie a diagnosztikai naplók használatához. Ha Azure Stack hub nincs regisztrálva, [használja a Kiemelt végpontot (PEP)](azure-stack-get-azurestacklog.md) a naplók megosztásához. 

![A diagnosztikai naplók gyűjtési lehetőségeinek képernyőképe](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

## <a name="collection-options-and-data-handling"></a>Gyűjtési lehetőségek és adatkezelés

A diagnosztikai napló-gyűjtési szolgáltatás két lehetőséget kínál a naplók küldésére. A következő táblázat ismerteti az egyes beállításokat, valamint az egyes esetekben az adatkezelés módját. 

### <a name="send-logs-proactively"></a>Naplók interaktív küldése

A [proaktív naplók gyűjtése](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) egyszerűsíti és leegyszerűsíti a diagnosztikai naplók gyűjtését, így az ügyfelek a támogatási eset megnyitása előtt küldhetnek naplókat a Microsoftnak. A diagnosztikai naplók proaktív módon vannak feltöltve az Azure Stack hub-ból elemzésre. Ezeket a naplókat csak akkor gyűjti a rendszer, ha egy [rendszerállapot-riasztást](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md#proactive-diagnostic-log-collection-alerts) vált ki, és a CSS-t csak egy támogatási eset kontextusában érik el.


#### <a name="how-the-data-is-handled"></a>Az adatkezelés módja

Elfogadja, hogy a Microsoft rendszeres időközönként automatikus naplózási gyűjteményeket fogad el a Azure Stack hub rendszerállapot-riasztásai alapján. A naplók feltöltését és megőrzését egy, a Microsoft által kezelt és felügyelt Azure Storage-fiókban is elfogadja és megtartja. 

Az adat kizárólag a rendszerállapot-riasztások hibaelhárításának céljára szolgál, és az Ön beleegyezike nélkül nem használható marketing-, reklámozási vagy egyéb kereskedelmi célra. Az adatok akár 90 napig is megmaradnak, és a Microsoft által gyűjtött adatok a [szokásos adatvédelmi gyakorlatnak](https://privacy.microsoft.com/)megfelelően lesznek kezelve.

Az engedély visszavonása a korábban a beleegyezéssel gyűjtött adatokat nem érinti.

A **proaktív** naplók használatával gyűjtött naplókat a Microsoft által kezelt és felügyelt Azure Storage-fiókba feltölti a rendszer. Ezeket a naplókat a Microsoft egy támogatási eset kontextusában, valamint Azure Stack hub állapotának javításához is elérheti. |

### <a name="send-logs-now"></a>Naplók küldése most

A [naplók elküldése most](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) egy manuális lehetőség, amelyben a rendszer csak akkor küldi el a diagnosztikai naplókat a Azure stack hub-ból, ha az ügyfél kezdeményezi a gyűjteményt, általában a támogatási eset megnyitása előtt. 

Azure Stack operátorok igény szerinti diagnosztikai naplókat küldhetnek a Microsoft ügyfélszolgálati szolgáltatásainak (CSS) a felügyeleti portál vagy a PowerShell használatával. Ha Azure Stack hub csatlakozik az Azure-hoz, a [naplók küldése most a felügyeleti portálon](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) lehetőséggel ajánlott, mert ez a legegyszerűbb módszer a naplók közvetlen elküldésére a Microsoftnak. Ha a portál nem érhető el, a kezelőknek Ehelyett a [PowerShell használatával kell elküldeni a naplókat](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md). 

Ha nem kapcsolódik az internethez, vagy csak helyileg szeretné menteni a naplókat, használja a [Get-AzureStackLog](azure-stack-get-azurestacklog.md) metódust a naplók elküldéséhez. A következő folyamatábra azt mutatja be, hogy mely lehetőség használható a diagnosztikai naplók küldésére az egyes esetekben. 

![A folyamatábra bemutatja, hogyan küldhet naplókat most a Microsoftnak](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

#### <a name="how-the-data-is-handled"></a>Az adatkezelés módja

A Azure Stack hub-ból származó diagnosztikai naplók összegyűjtésének kezdeményezésével elfogadja és elfogadja a naplók feltöltését és a Microsoft által kezelt és felügyelt Azure Storage-fiókban való megőrzését. A Microsoft CSS a támogatási esettel azonnal elérheti ezeket a naplókat anélkül, hogy az ügyfelet be kellene vonni a naplózási gyűjteménybe. 

Az adat kizárólag a rendszerállapot-riasztások hibaelhárításának céljára szolgál, és az Ön beleegyezike nélkül nem használható marketing-, reklámozási vagy egyéb kereskedelmi célra. Az adatok akár 90 napig is megmaradnak, és a Microsoft által gyűjtött adatok a [szokásos adatvédelmi gyakorlatnak](https://privacy.microsoft.com/)megfelelően lesznek kezelve. 

A küldési naplók használatával összegyűjtött naplók már fel lesznek töltve egy Microsoft által felügyelt és szabályozott tárolóba. Ezeket a naplókat a Microsoft egy támogatási eset kontextusában, valamint Azure Stack hub állapotának javítása érdekében éri el. 

## <a name="bandwidth-considerations"></a>Sávszélességgel kapcsolatos megfontolások

A diagnosztikai napló-gyűjtemény átlagos mérete attól függően változik, hogy proaktív módon vagy manuálisan fut-e. A **proaktív naplók** átlagos mérete körülbelül 2 GB. A **küldési naplók** gyűjteményének mérete mostantól attól függ, hogy hány órát gyűjt a rendszer.

Az alábbi táblázat az Azure-hoz korlátozott vagy mért kapcsolattal rendelkező környezetekre vonatkozó szempontokat sorolja fel.


| Hálózati kapcsolatok | Hatás |
|--------------------|--------|
| Alacsony sávszélességű/nagy késleltetésű kapcsolat | A napló feltöltése hosszabb időt vesz igénybe | 
| Megosztott kapcsolatok | A feltöltés hatással lehet más alkalmazásokra, illetve a hálózati kapcsolatokat megosztó felhasználókra is. |
| Mért kapcsolatok | Az INTERNETSZOLGÁLTATÓ további hálózati használatért díjköteles lehet. | 

## <a name="see-also"></a>Lásd még

[Azure Stack hub-napló és az ügyfelek adatkezelése](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

