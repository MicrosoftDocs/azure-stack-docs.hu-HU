---
title: Microsoft Azure Stack hub Súgó és támogatás áttekintése | Microsoft Docs
description: Microsoft Azure Stack hub támogatásának beszerzése.
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
ms.openlocfilehash: 65e4879be05141540e8f2e20e2a5a156f6368629
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75818162"
---
# <a name="microsoft-azure-stack-hub-help-and-support"></a>Microsoft Azure Stack hub Súgó és támogatás

A **Súgó és támogatás** az Azure stack hub portálon rendelkezik olyan erőforrásokkal, amelyek segítségével a kezelők további információt kaphatnak a Azure stack hub szolgáltatásról, a támogatási lehetőségeiket és a szakértői Súgó beszerzéséről. Az 1907-es kiadástól kezdve a kezelők a Súgó és támogatás segítségével is gyűjthetik a hibaelhárításhoz szükséges diagnosztikai naplókat.  

## <a name="help-resources"></a>Súgóerőforrások 

A kezelők a **Súgó + támogatás** segítségével többet tudhatnak meg Azure stack hub-ról, a támogatási lehetőségekről, valamint a szakértői Súgó beszerzéséről. 

### <a name="things-to-try-first"></a>Az első kipróbálható dolgok

A **Súgó és támogatás** tetején olyan dolgokra mutató hivatkozásokat talál, amelyeket érdemes először kipróbálni, például egy új koncepció megismerésére, a számlázás működésének megismerésére vagy a rendelkezésre álló támogatási lehetőségek megtekintésére. 

![Önkiszolgáló támogatás](media/azure-stack-help-and-support/get-support-tiles.png)

- **Dokumentáció**. [Azure stack hub-kezelő dokumentációja](index.yml) olyan fogalmakat, útmutatókat és oktatóanyagokat tartalmaz, amelyek bemutatják, hogyan kínálhat olyan Azure stack hub-szolgáltatásokat, mint például a Virtual Machines, az SQL Database, a Web Apps és más szolgáltatások. 

- **További információ a számlázásról**. Tippek a [használat és a számlázáshoz](azure-stack-billing-and-chargeback.md).

- **Támogatási lehetőségek**. Azure Stack hub-operátorok számos olyan Azure- [támogatási lehetőség](https://aka.ms/azstacksupport) közül választhatnak, amelyek bármely vállalat igényeihez illeszkednek. 

### <a name="get-expert-help"></a>Szakértői segítség kérése 

Integrált rendszer esetén a Microsoft és az eredeti berendezésgyártó (OEM) hardveres partnerei között összehangolt eszkalációs és megoldási folyamat áll rendelkezésre.

Ha Cloud Services-probléma merül fel, a támogatás a Microsoft ügyfél-támogatási szolgálatán (CSS) keresztül érhető el. Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd kattintson a **Súgó + támogatás** lehetőségre a Súgó és támogatás **Áttekintés** megnyitásához és egy új támogatási kérelem elküldéséhez. A támogatási kérelem létrehozásakor Azure Stack hub szolgáltatást kell megjelölnie. Azt javasoljuk, hogy az ügyfelek a nyilvános Azure Portal használata helyett jegyet küldjenek. 

Ha probléma merül fel az üzembe helyezéssel, a javítással és a frissítéssel, a hardverrel (beleértve a mezőre cserélhető egységeket is) és az összes hardveres szoftvert, például a hardver életciklus-állomásán futó szoftvert, először forduljon az OEM hardver gyártójához. Bármilyen más esetben forduljon a Microsoft CSS-hez.

![Az integrált rendszerek szakértői súgójának beolvasása](media/azure-stack-help-and-support/get-support-integrated.png)

A ASDK kapcsolatban a [Azure stack hub MSDN fórumának](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)támogatással kapcsolatos kérdéseire kérhet segítséget. 

Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd kattintson a **Súgó + támogatás** elemre a **Súgó és támogatás áttekintés**megnyitásához, amelyhez a fórumra mutató hivatkozás is tartozik. Az MSDN-fórumok rendszeres figyelése.  
Mivel a fejlesztői készlet egy kiértékelési környezet, a Microsoft CSS-ben nem érhető el hivatalos támogatás.

![A ASDK szakértői súgójának beolvasása](media/azure-stack-help-and-support/get-support-asdk.png)

A probléma megoldásához, illetve az online képzések fejlesztéséhez és a saját képességeinek javításához az MSDN-fórumokat is elérheti. 

![Szakértői segítség kérése](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Az Azure Stack hub gyors üzembe lépésének lépései

Ez az oktatóanyag testreszabott, attól függően, hogy a ASDK vagy az integrált rendszert futtatja-e, így gyorsan elérheti a környezetét. 

![Támogatási útmutatók beszerzése](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>Diagnosztikai naplók gyűjteménye

A 1907-es kiadástól kezdve két új módon gyűjthet naplókat a **súgóban és támogatásban**:

- **Automatikus gyűjtés**: Ha engedélyezve van, a naplófájlok gyűjtését meghatározott állapotú riasztások aktiválják 
- **Naplók gyűjtése most**: kiválaszthatja az elmúlt hét nap 1-4 óra csúszó ablakát.

![A diagnosztikai naplók gyűjtési lehetőségeinek képernyőképe](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

Az integrált rendszerek megoszthatják a diagnosztikai naplókat a Microsoft terméktámogatási szolgáltatásával (CSS). Mivel a Azure Stack Development Kit (ASDK) egy kiértékelési környezet, a CSS nem támogatja. További információ: [Azure stack hub diagnosztikai naplójának gyűjteménye – áttekintés](azure-stack-diagnostic-log-collection-overview.md).



## <a name="help-and-support-for-earlier-releases-azure-stack-hub-pre-1905"></a>Súgó és támogatás az Azure Stack hub korábbi kiadásaihoz (pre-1905)

A korábbi Azure Stack hub-kiadások is tartalmaz egy hivatkozást, amely **segítséget nyújt** a [Azure stack hub-kezelő dokumentációjának](https://aka.ms/adminportaldocs)átirányításához.

![Támogatási útmutatók beszerzése](media/azure-stack-help-and-support/get-support-previous.png)

Ha Cloud Services-probléma merül fel, a támogatás a Microsoft ügyfél-támogatási szolgálatán (CSS) keresztül érhető el. Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd kattintson a **Súgó és támogatás**elemre, majd az **új támogatási kérés** lehetőségre kattintva közvetlenül elküldheti a CSS-sel egy új támogatási kérést.

Az integrált rendszerek esetében a Microsoft és az OEM-partnereink között összehangolt eszkalációs és megoldási folyamat áll rendelkezésre. Ha Cloud Services-probléma merül fel, a támogatás a Microsoft CSS-ben érhető el. 

Ha probléma merül fel az üzembe helyezéssel, a javítással és a frissítéssel, a hardverrel (beleértve a mezőre cserélhető egységeket is) és az összes hardveres szoftvert, például a hardver életciklus-állomásán futó szoftvert, először forduljon az OEM hardver gyártójához. Bármilyen más esetben forduljon a Microsoft CSS-hez.

A fejlesztői csomaggal kapcsolatban a [Azure stack hub MSDN fórumában](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)a támogatással kapcsolatos kérdéseket teheti fel. Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd az **új támogatási kérés** lehetőségre kattintva kérjen segítséget az Azure stack hub Közösség szakértőitől.
Mivel a fejlesztői készlet egy kiértékelési környezet, a Microsoft CSS-ben nem érhető el hivatalos támogatás.

## <a name="next-steps"></a>Következő lépések

- Tudnivalók az [Azure stack hub hibaelhárításáról](azure-stack-troubleshooting.md)