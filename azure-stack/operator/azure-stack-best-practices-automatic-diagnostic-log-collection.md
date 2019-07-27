---
title: Ajánlott eljárások az automatikus Azure Stack naplók gyűjtéséhez | Microsoft Docs
description: Ajánlott eljárások az automatikus naplók gyűjtéséhez Azure Stack Súgó és támogatás szolgáltatásban
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
ms.date: 07/25/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 03fb0550bfaa41a3336ff17dd1c44e03bcea1402
ms.sourcegitcommit: b752f4e6733d9ebe56dbd171a14528dcb9a693fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68522050"
---
# <a name="best-practices-for-automatic-azure-stack-log-collection"></a>Ajánlott eljárások az automatikus Azure Stack naplók gyűjtéséhez 

*Vonatkozik: Integrált rendszerek Azure Stack*


Ez a témakör a Azure Stack automatikus diagnosztikai naplóinak kezeléséhez ajánlott eljárásokat ismerteti. 

## <a name="collecting-logs-from-multiple-azure-stack-systems"></a>Naplók gyűjtése több Azure Stack rendszerből

Állítson be egy BLOB-tárolót minden olyan Azure Stack skálázási egységhez, amelyhez naplókat kíván gyűjteni. A blob-tároló konfigurálásával kapcsolatos további információkért lásd: [automatikus Azure stack diagnosztikai naplók konfigurálása](azure-stack-configure-automatic-diagnostic-log-collection.md). Ajánlott eljárásként csak a diagnosztikai naplókat mentse ugyanabból a Azure Stack skálázási egységből egyetlen blob-tárolón belül. 

## <a name="retention-policy"></a>Adatmegőrzési szabály

Hozzon létre egy Azure Blob Storage [életciklus-kezelési szabályt](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) a napló adatmegőrzési házirendjének kezeléséhez. Javasoljuk, hogy 30 napig őrizze meg a diagnosztikai naplókat. Életciklus-kezelési szabály létrehozásához az Azure Storage-ban jelentkezzen be a Azure Portalba, kattintson a **Storage-fiókok**elemre, kattintson a blob-tárolóra, majd a **blob Service**területen kattintson az **életciklus-kezelés**elemre.

![A Azure Portal életciklus-kezelését bemutató képernyőkép](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)


## <a name="sas-token-expiration"></a>SAS-jogkivonat lejárata

Állítsa be a SAS URL-címét két évre. Ha bármikor megújítja a Storage-fiók kulcsait, ne felejtse el újragenerálni az SAS URL-címét. Az SAS-tokent az ajánlott eljárásoknak megfelelően kell kezelnie. További információ: [ajánlott eljárások az SAS használatakor](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas).


## <a name="bandwidth-consumption"></a>Sávszélesség-felhasználás

A diagnosztikai napló-gyűjtemény átlagos mérete attól függően változik, hogy a naplózási gyűjtemény igény szerinti vagy automatikus. 

Az igény szerinti naplózási gyűjtemény esetében a naplók gyűjteményének mérete attól függ, hogy hány órát gyűjt a rendszer. Az elmúlt hét napban bármelyik 1-4 óra csúszó ablakot kiválaszthatja. 

Ha engedélyezve van az automatikus diagnosztikai napló gyűjtése, a szolgáltatás figyeli a kritikus riasztásokat. Ha a kritikus riasztás körülbelül 30 percet vesz igénybe, és a szolgáltatás a megfelelő naplókat gyűjti és tölti fel. Ez a naplózási gyűjtemény átlagos mérete körülbelül 2 GB. Ha a javítás és a frissítés sikertelen, akkor az automatikus naplók gyűjtése csak akkor indul el, ha kritikus riasztás történik, és körülbelül 30 percet vesz igénybe. Javasoljuk, hogy kövesse a [javítás és frissítés figyelésével kapcsolatos útmutatást](azure-stack-updates.md).
A riasztások figyelése, a naplók gyűjtése és a feltöltés transzparens a felhasználó számára. 



Az egészséges rendszerekben a naplók egyáltalán nem lesznek összegyűjtve. A nem kifogástalan állapotú rendszerekben a naplózási gyűjtemény naponta kettő vagy három alkalommal futhat, de általában csak egyszer. A legtöbb esetben előfordulhat, hogy egy nap alatt akár tízszer is futhat egy legrosszabb forgatókönyvben.  

Az alábbi táblázat az Azure-ba korlátozott vagy mért kapcsolatokkal rendelkező környezeteket segíthet figyelembe venni az automatikus napló-gyűjtés engedélyezésének következményeit.

| Hálózati kapcsolatok | Hatás |
|--------------------|--------|
| Alacsony sávszélességű/nagy késleltetésű kapcsolat | A napló feltöltése hosszabb időt vesz igénybe | 
| Megosztott kapcsolatok | A feltöltés hatással lehet más alkalmazásokra, illetve a hálózati kapcsolatokat megosztó felhasználókra is. |
| Mért kapcsolatok | Az INTERNETSZOLGÁLTATÓ további hálózati használatért díjköteles lehet. |


## <a name="managing-costs"></a>Költségek kezelése

Az Azure [blob Storage díjai](https://azure.microsoft.com/pricing/details/storage/blobs/) attól függnek, hogy a havonta hány adatmentést és egyéb tényezőket, például az adatredundanciát. Ha nem rendelkezik meglévő Storage-fiókkal, jelentkezzen be a Azure Portalba, kattintson a **Storage-fiókok**lehetőségre, és kövesse az [Azure Blob Container sas URL-címének létrehozásához](azure-stack-configure-automatic-diagnostic-log-collection.md)szükséges lépéseket.

Ajánlott eljárásként hozzon létre egy Azure Blob Storage [életciklus-kezelési szabályzatot](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) a folyamatos tárolási költségek csökkentése érdekében. A Storage-fiók beállításával kapcsolatos további információkért lásd: [automatikus Azure stack diagnosztikai naplók konfigurálása](azure-stack-configure-automatic-diagnostic-log-collection.md)

## <a name="see-also"></a>Lásd még

[Automatikus Azure Stack naplózási gyűjtemény konfigurálása](azure-stack-best-practices-automatic-diagnostic-log-collection.md)

