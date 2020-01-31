---
title: Azure Stack hub diagnosztikai naplóinak áttekintése
description: A Azure Stack hub Súgó és támogatás szolgáltatásának diagnosztikai naplóinak gyűjtését ismerteti, beleértve az igény szerinti és az automatikus naplózási gyűjteményt is.
author: justinha
ms.topic: article
ms.date: 11/07/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: facbff4bdddc284c4f547c4bf5941678d2c8bcd7
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882375"
---
# <a name="overview-of-azure-stack-hub-diagnostic-log-collection"></a>Azure Stack hub diagnosztikai naplók gyűjteményének áttekintése 

Az Azure Stack hub az összetevők nagy gyűjteménye, amely együttműködik egymással, és interakciót végez. Ezek az összetevők saját egyedi naplókat hoznak. Ez kihívást jelenthet a problémák diagnosztizálásában, különösen a több, egymással kapcsolatban álló Azure Stack hub-összetevőktől érkező hibák esetén. A probléma megoldása érdekében diagnosztikai napló-gyűjtési élményt alakítottunk ki. 

Az 1907-as előtt a [test-AzureStack](azure-stack-diagnostic-test.md) használatával ellenőrizheti a rendszerállapotot és [a Kiemelt jogosultságú végpont (PEP)](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs) használatával gyűjti össze a hibakeresési naplókat. 

Az 1907-es kiadástól kezdve a **Súgó és támogatás** lap egy egyszerűbb felhasználói élményt nyújt a **diagnosztikai naplók gyűjtésével**. 
A **diagnosztikai naplók gyűjteménye** egy folyamatos beruházás része, amely a hibaelhárítási folyamattal javítja Azure stack hub-kezelői élményét. Ezeknek a fejlesztéseknek köszönhetően a kezelők gyorsan gyűjthetik és oszthatják meg a diagnosztikai naplókat a Microsoft ügyfél-támogatási szolgáltatásaival (CSS). A naplók tárolhatók az Azure-beli blob-tárolóban, ahol a hozzáférés igény szerint testreszabható.    
   
A **diagnosztikai naplók gyűjtése** két különböző módon működik:

- **Automatikus gyűjtés**: Ha engedélyezve van (ajánlott), a rendszer automatikusan aktiválja a naplózási gyűjteményt, és az Azure Storage-fiókban tárolja az adott riasztást.
- **Naplók összegyűjtése most**: ez egy igény szerinti lehetőség, ahol a naplókat összegyűjtheti egy 1-4 órás csúszó ablakból az elmúlt hét napban

![A diagnosztikai naplók gyűjtési lehetőségeinek képernyőképe](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

A **diagnosztikai napló gyűjteménye** egyszerű felhasználói felülettel rendelkezik, és nem igényel PowerShellt. A naplók gyűjtése megbízhatóan történik, még akkor is, ha az infrastrukturális szolgáltatások nem állnak le.
Ha a szabályzat lehetővé teszi a diagnosztikai naplók CSS-sel való megosztását, a rendszer a 1907 kiadástól kezdődően az ajánlott gyűjtési módszert alkalmazza a **diagnosztikai naplók gyűjtésére** . Csak [a PEP](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs) használatával gyűjthet naplókat, ha a súgóban és a támogatásban nem érhető el a **diagnosztikai naplók gyűjteménye** .

## <a name="automatic-diagnostic-log-collection"></a>Diagnosztikai naplók automatikus gyűjtése 

Ha egy [adott riasztás](azure-stack-configure-automatic-diagnostic-log-collection.md#automatic-diagnostic-log-collection-alerts) aktív, az automatikus diagnosztikai napló gyűjtése elindul, és proaktív módon feltölti a diagnosztikai naplókat a Azure stack hub-ból egy Azure-beli tárolási blobba, ami jelentősen csökkenti a diagnosztikai naplók CSS-sel való megosztásához szükséges időt. A rendszer csak akkor gyűjt diagnosztikai naplókat, ha riasztást vált ki.  

Az automatikus naplók gyűjtésével kapcsolatos további információkért lásd: az [automatikus Azure stack hub diagnosztikai naplók konfigurálása](azure-stack-configure-automatic-diagnostic-log-collection.md).

## <a name="on-demand-diagnostic-log-collection"></a>Igény szerinti diagnosztikai naplók gyűjtése

Az igény szerinti gyűjtemény használatával a rendszer a diagnosztikai naplókat a Azure Stack hub-ból egy Azure Storage-blobba feltölti, ha egy Azure Stack hub-kezelő manuálisan elindítja a gyűjteményt.
A CSS közös hozzáférés-aláírási (SAS) URL-címet biztosít a CSS tulajdonú Storage-blobhoz. Egy Azure Stack hub-operátor rákattinthat a **naplók összegyűjtése** lehetőségre, és megadhatja a sas URL-címét. A diagnosztikai naplók ezután közvetlenül a CSS-blobba lesznek feltöltve, anélkül, hogy közbenső megosztásra lenne szükség. 

A naplók igény szerinti gyűjtésével kapcsolatos további információkért lásd: [Azure stack hub diagnosztikai naplók gyűjtése most](azure-stack-configure-on-demand-diagnostic-log-collection.md).

## <a name="bandwidth-considerations"></a>Sávszélességgel kapcsolatos megfontolások

A diagnosztikai napló-gyűjtemény átlagos mérete attól függően változik, hogy igény szerint vagy automatikus-e fut. Az automatikus naplózási gyűjtemény átlagos mérete körülbelül 2 GB, míg az igény szerinti napló-gyűjtés mérete attól függ, hogy hány órát gyűjt a rendszer. 

Az alábbi táblázat az Azure-hoz korlátozott vagy mért kapcsolattal rendelkező környezetekre vonatkozó szempontokat sorolja fel.

| Hálózati kapcsolatok | Hatás |
|--------------------|--------|
| Alacsony sávszélességű/nagy késleltetésű kapcsolat | A napló feltöltése hosszabb időt vesz igénybe | 
| Megosztott kapcsolatok | A feltöltés hatással lehet más alkalmazásokra, illetve a hálózati kapcsolatokat megosztó felhasználókra is. |
| Mért kapcsolatok | Az INTERNETSZOLGÁLTATÓ további hálózati használatért díjköteles lehet. |

További információ: [ajánlott eljárások az automatikus Azure stack hub-naplók gyűjtéséhez](azure-stack-best-practices-automatic-diagnostic-log-collection.md).

## <a name="see-also"></a>Lásd még:

[Azure Stack hub-napló és az ügyfelek adatkezelése](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[Közös hozzáférésű jogosultságkódok (SAS) használata](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[Ajánlott eljárások az automatikus Azure Stack hub-naplók gyűjtéséhez](azure-stack-best-practices-automatic-diagnostic-log-collection.md)
