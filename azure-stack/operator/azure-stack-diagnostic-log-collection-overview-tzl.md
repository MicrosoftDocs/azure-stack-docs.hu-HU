---
title: Azure Stack hub diagnosztikai naplóinak áttekintése
description: A Azure Stack hub Súgó és támogatás szolgáltatásának diagnosztikai naplóinak gyűjtését ismerteti, beleértve az igény szerinti és a proaktív naplózási gyűjteményt is.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: b1c1048a8ad8bdb8d16d2e86c82febc8c74b03af
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81624941"
---
# <a name="overview-of-azure-stack-hub-diagnostic-log-collection"></a>Azure Stack hub diagnosztikai naplók gyűjteményének áttekintése 

::: moniker range=">= azs-2002"

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

| Hálózati kapcsolat | Hatás |
|--------------------|--------|
| Alacsony sávszélességű/nagy késleltetésű kapcsolat | A napló feltöltése hosszabb időt vesz igénybe | 
| Megosztott kapcsolatok | A feltöltés hatással lehet más alkalmazásokra, illetve a hálózati kapcsolatokat megosztó felhasználókra is. |
| Mért kapcsolatok | Az INTERNETSZOLGÁLTATÓ további hálózati használatért díjköteles lehet. | 

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="collecting-logs-from-multiple-azure-stack-hub-systems"></a>Naplók gyűjtése több Azure Stack hub-rendszerből

Állítson be egy BLOB-tárolót minden olyan Azure Stack hub-méretezési egységhez, amelyhez naplókat kíván gyűjteni. A blob-tároló konfigurálásával kapcsolatos további információkért lásd: az [automatikus Azure stack hub diagnosztikai naplójának konfigurálása](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). Ajánlott eljárásként csak a diagnosztikai naplókat mentse ugyanabból a Azure Stack hub-méretezési egységből egyetlen blob-tárolón belül. 

## <a name="retention-policy"></a>Retention szabályzat

Hozzon létre egy Azure Blob Storage [életciklus-kezelési szabályt](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) a napló adatmegőrzési házirendjének kezeléséhez. Javasoljuk, hogy 30 napig őrizze meg a diagnosztikai naplókat. Életciklus-kezelési szabály létrehozásához az Azure Storage-ban jelentkezzen be a Azure Portalba, kattintson a **Storage-fiókok**elemre, kattintson a blob-tárolóra, majd a **blob Service**területen kattintson az **életciklus-kezelés**elemre.

![A Azure Portal életciklus-kezelését bemutató képernyőkép](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)


## <a name="sas-token-expiration"></a>SAS-jogkivonat lejárata

Állítsa be a SAS URL-címét két évre. Ha bármikor megújítja a Storage-fiók kulcsait, ne felejtse el újragenerálni az SAS URL-címét. Az SAS-tokent az ajánlott eljárásoknak megfelelően kell kezelnie. További információ: [ajánlott eljárások az SAS használatakor](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas).


## <a name="bandwidth-consumption"></a>Sávszélesség-felhasználás

A diagnosztikai napló-gyűjtemény átlagos mérete attól függően változik, hogy a naplózási gyűjtemény igény szerinti vagy automatikus. 

Az igény szerinti naplózási gyűjtemény esetében a naplók gyűjteményének mérete attól függ, hogy hány órát gyűjt a rendszer. Az elmúlt hét napban bármelyik 1-4 óra csúszó ablakot kiválaszthatja. 

Ha engedélyezve van az automatikus diagnosztikai napló gyűjtése, a szolgáltatás figyeli a kritikus riasztásokat. 
Ha a kritikus riasztás körülbelül 30 percet vesz igénybe, és a szolgáltatás a megfelelő naplókat gyűjti és tölti fel. 
Ez a naplózási gyűjtemény átlagos mérete körülbelül 2 GB. 
Ha a javítás és a frissítés sikertelen, akkor az automatikus naplók gyűjtése csak akkor indul el, ha kritikus riasztás történik, és körülbelül 30 percet vesz igénybe. Javasoljuk, hogy kövesse a [javítás és frissítés figyelésével kapcsolatos útmutatást](azure-stack-updates.md).
A riasztások figyelése, a naplók gyűjtése és a feltöltés transzparens a felhasználó számára. 



Az egészséges rendszerekben a naplók egyáltalán nem lesznek összegyűjtve. 
A nem kifogástalan állapotú rendszerekben a naplózási gyűjtemény naponta kettő vagy három alkalommal futhat, de általában csak egyszer. 
A legtöbb esetben előfordulhat, hogy egy nap alatt akár tízszer is futhat egy legrosszabb forgatókönyvben.  

Az alábbi táblázat az Azure-ba korlátozott vagy mért kapcsolatokkal rendelkező környezeteket segíthet figyelembe venni az automatikus napló-gyűjtés engedélyezésének következményeit.

| Hálózati kapcsolat | Hatás |
|--------------------|--------|
| Alacsony sávszélességű/nagy késleltetésű kapcsolat | A napló feltöltése hosszabb időt vesz igénybe | 
| Megosztott kapcsolatok | A feltöltés hatással lehet más alkalmazásokra, illetve a hálózati kapcsolatokat megosztó felhasználókra is. |
| Mért kapcsolatok | Az INTERNETSZOLGÁLTATÓ további hálózati használatért díjköteles lehet. |


## <a name="managing-costs"></a>Költségek kezelése

Az Azure [blob Storage díjai](https://azure.microsoft.com/pricing/details/storage/blobs/) attól függnek, hogy a havonta hány adatmentést és egyéb tényezőket, például az adatredundanciát. 
Ha nem rendelkezik meglévő Storage-fiókkal, jelentkezzen be a Azure Portalba, kattintson a **Storage-fiókok**lehetőségre, és kövesse az [Azure Blob Container sas URL-címének létrehozásához](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)szükséges lépéseket.

Ajánlott eljárásként hozzon létre egy Azure Blob Storage [életciklus-kezelési szabályzatot](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) a folyamatos tárolási költségek csökkentése érdekében. A Storage-fiók beállításával kapcsolatos további információkért lásd: az [automatikus Azure stack hub diagnosztikai naplójának konfigurálása](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)

::: moniker-end

## <a name="see-also"></a>Lásd még

[Azure Stack hub-napló és az ügyfelek adatkezelése](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

