---
title: Diagnosztikai naplók gyűjteménye Azure Stack hub-ban
description: Ismerje meg a diagnosztikai naplók gyűjtését Azure Stack hub Súgó + támogatás szolgáltatásában.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: 2fcc31625fc0f508532c5c6a524f26e9cef31760
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836036"
---
# <a name="diagnostic-log-collection-in-azure-stack-hub"></a>Diagnosztikai naplók gyűjteménye Azure Stack hub-ban

::: moniker range=">= azs-2002"

Az Azure Stack hub a Windows-összetevők és a helyszíni Azure-szolgáltatások nagy gyűjteménye, amelyek egymással együttműködnek. Ezek az összetevők és szolgáltatások saját naplókat hoznak létre. Ha engedélyezni szeretné, hogy a Microsoft ügyfélszolgálata (CSS) hatékonyan diagnosztizálja a problémákat, zökkenőmentesen megtapasztaljuk a diagnosztikai naplók gyűjtését.

A **Súgó és támogatás** diagnosztikai naplóinak gyűjteménye segíti a kezelők számára a CSS-sel való gyors összegyűjtését és megosztását egy egyszerű felhasználói felületen, amely nem igényel PowerShellt. A naplók gyűjtése akkor is történik, ha más infrastrukturális szolgáltatások nem állnak le.  

Javasoljuk, hogy ezt a módszert használja a naplók gyűjtésére, és csak [a Kiemelt jogosultságú végpont (PEP)](azure-stack-get-azurestacklog.md) használatára legyen lehetőség, ha a felügyeleti portál vagy a **Súgó + támogatás** panel nem érhető el.

>[!NOTE]
>Azure Stack hub-t regisztrálni kell, és internetkapcsolattal kell rendelkeznie a diagnosztikai naplók használatához. Ha Azure Stack hub nincs regisztrálva, [használja a Kiemelt végpontot (PEP)](azure-stack-get-azurestacklog.md) a naplók megosztásához.

![Diagnosztikai naplók gyűjtési lehetőségei Azure Stack központban](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

## <a name="collection-options-and-data-handling"></a>Gyűjtési lehetőségek és adatkezelés

A diagnosztikai napló gyűjteménye szolgáltatás két lehetőséget kínál a naplók küldésére. A következő táblázat ismerteti az egyes beállításokat, valamint az egyes esetekben az adatkezelés módját.

### <a name="send-logs-proactively"></a>Naplók interaktív küldése

A [proaktív naplók gyűjtése](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) egyszerűsíti és leegyszerűsíti a diagnosztikai naplók gyűjtését, így az ügyfelek a támogatási eset megnyitása előtt küldhetnek naplókat a Microsoftnak. A diagnosztikai naplók proaktív módon vannak feltöltve az Azure Stack hub-ból elemzésre. Ezeket a naplókat csak akkor gyűjti a rendszer, ha egy [rendszerállapot-riasztást](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md#proactive-diagnostic-log-collection-alerts) vált ki, és a CSS-t csak egy támogatási eset kontextusában érik el.

#### <a name="how-the-data-is-handled"></a>Az adatkezelés módja

Elfogadja, hogy a Microsoft rendszeres időközönként automatikus naplózási gyűjteményeket fogad el a Azure Stack hub rendszerállapot-riasztásai alapján. A naplók feltöltését és megőrzését egy, a Microsoft által kezelt és felügyelt Azure Storage-fiókban is elfogadja és megtartja.

A rendszer csak a rendszerállapot-riasztások hibaelhárítását fogja használni, és nem használja fel az Ön engedélye nélkül marketing-, reklámozási vagy egyéb kereskedelmi célokra. Az adatok akár 90 napig is megtekinthetők, a Microsoft által gyűjtött adatok pedig az [általános adatvédelmi gyakorlatnak](https://privacy.microsoft.com/)megfelelően lesznek kezelve.

Az engedély visszavonása a korábban a beleegyezéssel gyűjtött adatokat nem érinti.

A **proaktív** naplók használatával gyűjtött naplókat a Microsoft által kezelt és felügyelt Azure Storage-fiókba feltölti a rendszer. Ezeket a naplókat a Microsoft egy támogatási eset kontextusában, valamint Azure Stack hub állapotának javításához is elérheti.

### <a name="send-logs-now"></a>Naplók küldése most

A [naplók elküldése most](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) egy manuális lehetőség, amelyben a rendszer csak akkor küldi el a diagnosztikai naplókat a Azure stack hub-ból, ha az ügyfél kezdeményezi a gyűjteményt, általában a támogatási eset megnyitása előtt.

Azure Stack operátorok igény szerinti diagnosztikai naplókat küldhetnek a Microsoft ügyfélszolgálati szolgáltatásainak (CSS) a felügyeleti portál vagy a PowerShell használatával. Ha Azure Stack hub csatlakozik az Azure-hoz, a [naplók küldése most a felügyeleti portálon](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) lehetőséggel ajánlott, mert ez a legegyszerűbb módszer a naplók közvetlen elküldésére a Microsoftnak. Ha a portál nem érhető el, a kezelőknek Ehelyett a [PowerShell használatával kell elküldeni a naplókat](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md).

Ha nem csatlakozik az internethez, vagy csak helyileg szeretné menteni a naplókat, használja a [Get-AzureStackLog](azure-stack-get-azurestacklog.md) metódust a naplók elküldéséhez. A következő folyamatábra azt mutatja be, hogy mely lehetőség használható a diagnosztikai naplók küldésére az egyes esetekben.

![A folyamatábra bemutatja, hogyan küldhet naplókat most a Microsoftnak](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

#### <a name="how-the-data-is-handled"></a>Az adatkezelés módja

A Azure Stack hub-ból származó diagnosztikai naplók összegyűjtésének kezdeményezésével elfogadja és elfogadja a naplók feltöltését és a Microsoft által kezelt és felügyelt Azure Storage-fiókban való megőrzését. A Microsoft CSS a támogatási esettel azonnal elérheti ezeket a naplókat anélkül, hogy az ügyfelet be kellene vonni a naplózási gyűjteménybe.

A rendszer csak a rendszerállapot-riasztások hibaelhárításához használja az információkat, és az Ön beleegyezike nélkül nem használja marketing-, reklámozási vagy egyéb kereskedelmi célra. Az adatok akár 90 napig is megtekinthetők, a Microsoft által gyűjtött adatok pedig az [általános adatvédelmi gyakorlatnak](https://privacy.microsoft.com/)megfelelően lesznek kezelve.

A **küldési naplók** használatával gyűjtött naplók mostantól a Microsoft által felügyelt és vezérelt tárolóba lesznek feltöltve. Ezeket a naplókat a Microsoft egy támogatási eset kontextusában, valamint Azure Stack hub állapotának javítása érdekében éri el.

## <a name="bandwidth-considerations"></a>Sávszélességgel kapcsolatos megfontolások

A diagnosztikai napló-gyűjtemény átlagos mérete attól függően változik, hogy proaktív módon vagy manuálisan fut-e. A **proaktív naplók** átlagos mérete körülbelül 2 GB. A **küldési naplók** gyűjteményének mérete mostantól attól függ, hogy hány órát gyűjt a rendszer.

Az alábbi táblázat az Azure-hoz korlátozott vagy mért kapcsolattal rendelkező környezetekre vonatkozó szempontokat sorolja fel.

| Hálózati kapcsolat | Hatás |
|----|---|
| Alacsony sávszélességű/nagy késleltetésű kapcsolat | A napló feltöltése hosszabb időt vesz igénybe. |
| Megosztott kapcsolatok | A feltöltés hatással lehet más alkalmazásokra, illetve a hálózati kapcsolatokat megosztó felhasználókra is. |
| Mért kapcsolatok | A további hálózati használatért az INTERNETSZOLGÁLTATÓ felár ellenében vehető igénybe. |

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="collecting-logs-from-multiple-azure-stack-hub-systems"></a>Naplók gyűjtése több Azure Stack hub-rendszerből

Állítson be egy BLOB-tárolót minden olyan Azure Stack hub-méretezési egységhez, amelyhez naplókat kíván gyűjteni. A blob-tároló konfigurálásával kapcsolatos további információkért lásd: az [automatikus Azure stack hub diagnosztikai naplójának konfigurálása](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). Ajánlott eljárásként csak a diagnosztikai naplókat mentse ugyanabból a Azure Stack hub-méretezési egységből egyetlen blob-tárolón belül.

## <a name="retention-policy"></a>Retention szabályzat

Hozzon létre egy Azure Blob Storage [életciklus-kezelési szabályt](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) a napló adatmegőrzési házirendjének kezeléséhez. Javasoljuk, hogy 30 napig őrizze meg a diagnosztikai naplókat. Életciklus-kezelési szabály létrehozásához az Azure Storage-ban jelentkezzen be a Azure Portalba, válassza a **Storage-fiókok**lehetőséget, válassza ki a BLOB-tárolót, majd a **blob Service**területen válassza az **életciklus-kezelés**lehetőséget.

![Életciklus-kezelés a Azure Portal](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)

## <a name="sas-token-expiration"></a>SAS-jogkivonat lejárata

Állítsa be a SAS URL-címét két évre. Ha bármikor megújítja a Storage-fiók kulcsait, ne felejtse el újragenerálni az SAS URL-címét. Az SAS-tokent az ajánlott eljárásoknak megfelelően kell kezelnie. További információ: [ajánlott eljárások az SAS használatakor](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas).

## <a name="bandwidth-consumption"></a>Sávszélesség-felhasználás

A diagnosztikai napló-gyűjtemény átlagos mérete attól függően változik, hogy a naplózási gyűjtemény igény szerinti vagy automatikus.

Az igény szerinti naplózási gyűjtemény esetében a naplók gyűjteményének mérete attól függ, hogy hány órát gyűjt a rendszer. Az elmúlt hét napban bármelyik 1-4 óra csúszó ablakot kiválaszthatja.

Ha engedélyezve van az automatikus diagnosztikai napló gyűjtése, a szolgáltatás figyeli a kritikus riasztásokat. Ha a kritikus riasztás körülbelül 30 percet vesz igénybe, és a szolgáltatás a megfelelő naplókat gyűjti és tölti fel. Ez a naplózási gyűjtemény átlagos mérete körülbelül 2 GB. Ha a javítás és a frissítés sikertelen, az automatikus naplók gyűjtése csak akkor indul el, ha kritikus riasztást vált ki, és körülbelül 30 percet vesz igénybe. Javasoljuk, hogy kövesse a [javítás és frissítés figyelésével kapcsolatos útmutatást](azure-stack-updates.md). A riasztások figyelése, a naplók gyűjtése és a feltöltés transzparens a felhasználó számára.

Az egészséges rendszerekben a naplók egyáltalán nem lesznek összegyűjtve. A nem kifogástalan állapotú rendszerekben a naplózási gyűjtemény naponta kettő vagy három alkalommal futhat, de általában csak egyszer. Legtöbbször előfordulhat, hogy a legrosszabb esetben akár 10 alkalommal is futhat egy nap alatt.  

Az alábbi táblázat az Azure-ba korlátozott vagy mért kapcsolatokkal rendelkező környezeteket segíthet figyelembe venni az automatikus napló-gyűjtés engedélyezésének következményeit.

| Hálózati kapcsolat | Hatás |
|---|---|
| Alacsony sávszélességű/nagy késleltetésű kapcsolat | A napló feltöltése hosszabb időt vesz igénybe. | 
| Megosztott kapcsolatok | A feltöltés hatással lehet más alkalmazásokra, illetve a hálózati kapcsolatokat megosztó felhasználókra is. |
| Mért kapcsolatok | A további hálózati használatért az INTERNETSZOLGÁLTATÓ felár ellenében vehető igénybe. |

## <a name="managing-costs"></a>Költségek kezelése

Az Azure [blob Storage díjai](https://azure.microsoft.com/pricing/details/storage/blobs/) attól függnek, hogy a havonta hány adatmentést és más tényezőket, például az adatredundanciát használják. Ha nem rendelkezik meglévő Storage-fiókkal, bejelentkezhet a Azure Portalba, kiválaszthatja a **Storage-fiókokat**, és követheti az [Azure Blob-tároló sas URL-címének létrehozásához](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)szükséges lépéseket.

Ajánlott eljárásként hozzon létre egy Azure Blob Storage [életciklus-kezelési szabályzatot](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) a folyamatos tárolási költségek csökkentése érdekében. A Storage-fiók beállításával kapcsolatos további információkért lásd: az [automatikus Azure stack hub diagnosztikai naplójának konfigurálása](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)

::: moniker-end

## <a name="see-also"></a>Lásd még

[Azure Stack hub-napló és az ügyfelek adatkezelése](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)
