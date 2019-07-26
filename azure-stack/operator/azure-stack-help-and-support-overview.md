---
title: Microsoft Azure Stack Súgó és támogatás áttekintése | Microsoft Docs
description: Microsoft Azure Stack támogatásának beszerzése.
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
ms.date: 07/24/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/24/2019
ms.openlocfilehash: ff7911f64cf7c1413e623cd84cebe3d26585a3e5
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493858"
---
# <a name="microsoft-azure-stack-help-and-support"></a>Microsoft Azure Stack Súgó és támogatás

A **Súgó és támogatás** a Azure stack portálon rendelkezik olyan erőforrásokkal, amelyek segítségével a kezelők további információkat kaphatnak a Azure Stackről, megtekinthetik a támogatási lehetőségeiket és szakértői segítséget kérhetnek. Az 1907-es kiadástól kezdve a kezelők a Súgó és támogatás segítségével is gyűjthetik a hibaelhárításhoz szükséges diagnosztikai naplókat.  

## <a name="help-resources"></a>Súgó erőforrásai 

A kezelők a **Súgó + támogatás** segítségével további információkat kaphatnak a Azure Stackről, megtekinthetik támogatási lehetőségeiket, és szakértői segítséget kérhetnek. 

### <a name="things-to-try-first"></a>Az első kipróbálható dolgok

A **Súgó és támogatás** tetején olyan dolgokra mutató hivatkozásokat talál, amelyeket érdemes először kipróbálni, például egy új koncepció megismerésére, a számlázás működésének megismerésére vagy a rendelkezésre álló támogatási lehetőségek megtekintésére. 

![Önkiszolgáló támogatás](media/azure-stack-help-and-support/get-support-tiles.png)

- **Dokumentáció**. [Azure stack operátor dokumentációja](index.yml) olyan fogalmakat, útmutatókat és oktatóanyagokat tartalmaz, amelyek bemutatják, hogyan kínálhat olyan Azure stack-szolgáltatásokat, mint például a Virtual Machines, az SQL Database, a Web Apps és más szolgáltatások. 

- **További információ a számlázásról**. Tippek a [használat és](azure-stack-billing-and-chargeback.md)a számlázáshoz.

- **Támogatási lehetőségek**. Azure Stack operátorok számos olyan [Azure-támogatási lehetőség](https://aka.ms/azstacksupport) közül választhatnak, amelyek bármely vállalat igényeihez illeszkednek. 

### <a name="get-expert-help"></a>Szakértői segítség kérése 

Integrált rendszer esetén a Microsoft és az eredeti berendezésgyártó (OEM) hardveres partnerei között összehangolt eszkalációs és megoldási folyamat áll rendelkezésre.

Ha Cloud Services-probléma merül fel, a támogatás a Microsoft ügyfél-támogatási szolgálatán (CSS) keresztül érhető el. Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd kattintson a **Súgó + támogatás** lehetőségre a Súgó és támogatás **Áttekintés** megnyitásához és egy új támogatási kérelem elküldéséhez. A támogatási kérelem létrehozásakor a rendszer előjelöli Azure Stack szolgáltatást. Azt javasoljuk, hogy az ügyfelek a nyilvános Azure Portal használata helyett jegyet küldjenek. 

Ha probléma merül fel az üzembe helyezéssel, a javítással és a frissítéssel, a hardverrel (beleértve a mezőre cserélhető egységeket is) és az összes hardveres szoftvert, például a hardver életciklus-állomásán futó szoftvert, először forduljon az OEM hardver gyártójához. Bármilyen más esetben forduljon a Microsoft CSS-hez.

![Az integrált rendszerek szakértői súgójának beolvasása](media/azure-stack-help-and-support/get-support-integrated.png)

A ASDK a támogatással kapcsolatos kérdéseket a [Azure stack MSDN fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)teheti meg. 

Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd kattintson a **Súgó + támogatás** elemre a **Súgó és támogatás áttekintés**megnyitásához, amelyhez a fórumra mutató hivatkozás is tartozik. Az MSDN-fórumok rendszeres figyelése.  
Mivel a fejlesztői készlet egy kiértékelési környezet, a Microsoft CSS-ben nem érhető el hivatalos támogatás.

![A ASDK szakértői súgójának beolvasása](media/azure-stack-help-and-support/get-support-asdk.png)

A probléma megoldásához, illetve az online képzések fejlesztéséhez és a saját képességeinek javításához az MSDN-fórumokat is elérheti. 

![Szakértői segítség kérése](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack"></a>Gyors használat Azure Stack

Ez az oktatóanyag testreszabott, attól függően, hogy a ASDK vagy az integrált rendszert futtatja-e, így gyorsan elérheti a környezetét. 

![Támogatási útmutatók beszerzése](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>Diagnosztikai naplók gyűjteménye

A 1907-es kiadástól kezdve két új módon gyűjthet naplókat a **súgóban és támogatásban**:

- **Automatikus gyűjtés**: Ha engedélyezve van, a naplófájlok gyűjtését meghatározott állapotú riasztások aktiválják 
- **Naplók gyűjtése most**: Az elmúlt hét napban 1-4 óra csúszó ablakot választhat

![A diagnosztikai naplók gyűjtési lehetőségeinek képernyőképe](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

Az integrált rendszerek megoszthatják a diagnosztikai naplókat a Microsoft terméktámogatási szolgáltatásával (CSS). Mivel a Azure Stack Development Kit (ASDK) egy kiértékelési környezet, a CSS nem támogatja. További információ: [Azure stack diagnosztikai napló gyűjteményének áttekintése](azure-stack-diagnostic-log-collection-overview.md).



## <a name="help-and-support-for-earlier-releases-azure-stack-pre-1905"></a>Súgó és támogatás a korábbi kiadásokhoz Azure Stack (pre-1905)

A korábbi Azure Stack kiadásokban egy hivatkozás is található, amely **segítséget nyújt** a [Azure stack kezelői dokumentációhoz](https://aka.ms/adminportaldocs)való átirányítások támogatásához.

![Támogatási útmutatók beszerzése](media/azure-stack-help-and-support/get-support-previous.png)

Ha Cloud Services-probléma merül fel, a támogatás a Microsoft ügyfél-támogatási szolgálatán (CSS) keresztül érhető el. Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd kattintson a **Súgó és támogatás**elemre, majd az **új támogatási kérés** lehetőségre kattintva közvetlenül elküldheti a CSS-sel egy új támogatási kérést.

Az integrált rendszerek esetében a Microsoft és az OEM-partnereink között összehangolt eszkalációs és megoldási folyamat áll rendelkezésre. Ha Cloud Services-probléma merül fel, a támogatás a Microsoft CSS-ben érhető el. 

Ha probléma merül fel az üzembe helyezéssel, a javítással és a frissítéssel, a hardverrel (beleértve a mezőre cserélhető egységeket is) és az összes hardveres szoftvert, például a hardver életciklus-állomásán futó szoftvert, először forduljon az OEM hardver gyártójához. Bármilyen más esetben forduljon a Microsoft CSS-hez.

A fejlesztői csomaggal kapcsolatos kérdéseket a [Azure stack MSDN fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)teheti fel. Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd kattintson az **új támogatási kérelem** elemre, hogy segítséget kapjon a Azure stack Közösség szakértőitől.
Mivel a fejlesztői készlet egy kiértékelési környezet, a Microsoft CSS-ben nem érhető el hivatalos támogatás.

## <a name="next-steps"></a>További lépések

- Tudnivalók a [hibaelhárítási Azure stack](azure-stack-troubleshooting.md)