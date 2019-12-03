---
title: Azure Stack diagnosztikai naplók áttekintése | Microsoft Docs
description: Ismerteti a diagnosztikai naplók gyűjtését Azure Stack Súgó és támogatás szolgáltatásban, beleértve az igény szerinti és az automatikus naplózási gyűjteményt is.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 1d40c0c7884773cf1f10341a90c78531c07e13f8
ms.sourcegitcommit: 7817d61fa34ac4f6410ce6f8ac11d292e1ad807c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74690001"
---
# <a name="overview-of-azure-stack-diagnostic-log-collection"></a>Azure Stack diagnosztikai napló gyűjteményének áttekintése 

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Azure Stack az összetevők nagy gyűjteménye, amely együttműködik egymással, és interakciót végez. Ezek az összetevők saját egyedi naplókat hoznak. Ez egy kihívást jelentő feladat kijavítását teszi elérhetővé, különösen a több, interakcióban lévő Azure Stack-összetevőtől érkező hibák esetén. A probléma megoldása érdekében diagnosztikai napló-gyűjtési élményt alakítottunk ki. 

Az 1907-as előtt a [test-AzureStack](azure-stack-diagnostic-test.md) használatával ellenőrizheti a rendszerállapotot és [a Kiemelt jogosultságú végpont (PEP)](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs) használatával gyűjti össze a hibakeresési naplókat. 

Az 1907-es kiadástól kezdve a **Súgó és támogatás** lap egy egyszerűbb felhasználói élményt nyújt a **diagnosztikai naplók gyűjtésével**. 
A **diagnosztikai naplók gyűjteménye** egy folyamatban lévő beruházás része, amely a hibaelhárítási folyamattal Azure stack operátorral kapcsolatos tapasztalatokat fejleszti. Ezeknek a fejlesztéseknek köszönhetően a kezelők gyorsan gyűjthetik és oszthatják meg a diagnosztikai naplókat a Microsoft ügyfél-támogatási szolgáltatásaival (CSS). A naplók tárolhatók az Azure-beli blob-tárolóban, ahol a hozzáférés igény szerint testreszabható.    
   
A **diagnosztikai naplók gyűjtése** két különböző módon működik:

- **Automatikus gyűjtés**: Ha engedélyezve van (ajánlott), a rendszer automatikusan aktiválja a naplózási gyűjteményt, és az Azure Storage-fiókban tárolja az adott riasztást.
- **Naplók összegyűjtése most**: ez egy igény szerinti lehetőség, ahol a naplókat összegyűjtheti egy 1-4 órás csúszó ablakból az elmúlt hét napban

![A diagnosztikai naplók gyűjtési lehetőségeinek képernyőképe](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

A **diagnosztikai napló gyűjteménye** egyszerű felhasználói felülettel rendelkezik, és nem igényel PowerShellt. A naplók gyűjtése megbízhatóan történik, még akkor is, ha az infrastrukturális szolgáltatások nem állnak le.
Ha a szabályzat lehetővé teszi a diagnosztikai naplók CSS-sel való megosztását, a rendszer a 1907 kiadástól kezdődően az ajánlott gyűjtési módszert alkalmazza a **diagnosztikai naplók gyűjtésére** . Csak [a PEP](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs) használatával gyűjthet naplókat, ha a súgóban és a támogatásban nem érhető el a **diagnosztikai naplók gyűjteménye** .

## <a name="automatic-diagnostic-log-collection"></a>Diagnosztikai naplók automatikus gyűjtése 

Ha egy [adott riasztás](azure-stack-configure-automatic-diagnostic-log-collection.md#automatic-diagnostic-log-collection-alerts) aktív, az automatikus diagnosztikai napló gyűjtése elindul, és proaktív módon feltölti a diagnosztikai naplókat a Azure Stackból egy Azure-beli tárolási blobba, ami jelentősen csökkenti a diagnosztikai naplók CSS-sel való megosztásához szükséges időt. A rendszer csak akkor gyűjt diagnosztikai naplókat, ha riasztást vált ki.  

További információ az automatikus naplók gyűjtéséről: [automatikus Azure stack diagnosztikai naplók konfigurálása](azure-stack-configure-automatic-diagnostic-log-collection.md).

## <a name="on-demand-diagnostic-log-collection"></a>Igény szerinti diagnosztikai naplók gyűjtése

Igény szerinti gyűjtemény esetén a rendszer a diagnosztikai naplókat a Azure Stack az Azure-beli Storage-blobba feltölti, ha egy Azure Stack operátor manuálisan indítja el a gyűjteményt.
A CSS közös hozzáférés-aláírási (SAS) URL-címet biztosít a CSS tulajdonú Storage-blobhoz. Egy Azure Stack operátor a **naplók összegyűjtése** lehetőségre kattintva megadhatja az SAS URL-címét. A diagnosztikai naplók ezután közvetlenül a CSS-blobba lesznek feltöltve, anélkül, hogy közbenső megosztásra lenne szükség. 

A naplók igény szerinti gyűjtésével kapcsolatos további információkért lásd: [Azure stack diagnosztikai naplók gyűjtése most](azure-stack-configure-on-demand-diagnostic-log-collection.md).

## <a name="bandwidth-considerations"></a>Sávszélességgel kapcsolatos megfontolások

A diagnosztikai napló-gyűjtemény átlagos mérete attól függően változik, hogy igény szerint vagy automatikus-e fut. Az automatikus naplózási gyűjtemény átlagos mérete körülbelül 2 GB, míg az igény szerinti napló-gyűjtés mérete attól függ, hogy hány órát gyűjt a rendszer. 

Az alábbi táblázat az Azure-hoz korlátozott vagy mért kapcsolattal rendelkező környezetekre vonatkozó szempontokat sorolja fel.

| Hálózati kapcsolatok | Hatás |
|--------------------|--------|
| Alacsony sávszélességű/nagy késleltetésű kapcsolat | A napló feltöltése hosszabb időt vesz igénybe | 
| Megosztott kapcsolatok | A feltöltés hatással lehet más alkalmazásokra, illetve a hálózati kapcsolatokat megosztó felhasználókra is. |
| Mért kapcsolatok | Az INTERNETSZOLGÁLTATÓ további hálózati használatért díjköteles lehet. |

További információ: [ajánlott eljárások az automatikus Azure stack naplók gyűjtéséhez](azure-stack-best-practices-automatic-diagnostic-log-collection.md).

## <a name="see-also"></a>Lásd még:

[A naplók és az ügyfelek adatkezelésének Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[Közös hozzáférésű jogosultságkódok (SAS) használata](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[Ajánlott eljárások az automatikus Azure Stack naplók gyűjtéséhez](azure-stack-best-practices-automatic-diagnostic-log-collection.md)
