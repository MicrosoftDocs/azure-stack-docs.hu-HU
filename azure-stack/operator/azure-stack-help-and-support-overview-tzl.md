---
title: Microsoft Azure Stack hub Súgó és támogatás áttekintése
description: Microsoft Azure Stack hub támogatásának beszerzése.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: 2026593dff049906d4c176ac10324f9b74206830
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81624924"
---
# <a name="microsoft-azure-stack-hub-help-and-support"></a>Microsoft Azure Stack hub Súgó és támogatás


::: moniker range=">= azs-2002"

Azure Stack hub-kezelők a **Súgó és támogatás** segítségével gyűjthetik össze a diagnosztikai naplókat, és elküldhetik azokat a Microsoftnak a hibaelhárításhoz. A Azure Stack hub portál **Súgó + támogatás** szolgáltatását a felügyeleti portálról érheti el. Olyan erőforrásokkal rendelkezik, amelyekkel a kezelők további információt kaphatnak a Azure Stackről, megtekinthetik támogatási lehetőségeiket, és szakértői segítséget kérhetnek.  

![Képernyőkép a Súgó és támogatás eléréséről a felügyeleti portálon](media/azure-stack-help-and-support/help-and-support.png)

## <a name="help-resources"></a>Súgó erőforrásai 

A kezelők a **Súgó + támogatás** segítségével többet tudhatnak meg Azure stack hub-ról, a támogatási lehetőségekről, valamint a szakértői segítségről. 

### <a name="things-to-try-first"></a>Az első kipróbálható dolgok

A **Súgó és támogatás** tetején olyan dolgokra mutató hivatkozásokat talál, amelyeket érdemes először kipróbálni, például egy új koncepció megismerésére, a számlázás működésének megismerésére vagy a rendelkezésre álló támogatási lehetőségek megtekintésére. 

![Önkiszolgáló támogatás](media/azure-stack-help-and-support/get-support-tiles.png)

- **Dokumentáció**. [Azure stack hub-kezelő dokumentációja](index.yml) olyan fogalmakat, útmutatókat és oktatóanyagokat tartalmaz, amelyek bemutatják, hogyan kínálhat olyan Azure stack hub-szolgáltatásokat, mint például a Virtual Machines, az SQL Database, a Web Apps és más szolgáltatások. 

- **További információ a számlázásról**. Tippek a [használat és a számlázáshoz](azure-stack-billing-and-chargeback.md).

- **Támogatási lehetőségek**. Azure Stack hub-operátorok számos olyan Azure- [támogatási lehetőség](https://aka.ms/azstacksupport) közül választhatnak, amelyek bármely vállalat igényeihez illeszkednek. 


### <a name="get-expert-help"></a>Szakértői segítség kérése 

Integrált rendszer esetén a Microsoft és az eredeti berendezésgyártó (OEM) hardveres partnerei között összehangolt eszkalációs és megoldási folyamat áll rendelkezésre.

Ha Cloud Services-probléma merül fel, a támogatás a Microsoft ügyfél-támogatási szolgálatán (CSS) keresztül érhető el. 
Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd kattintson a **Súgó + támogatás** lehetőségre a Súgó és támogatás **Áttekintés** megnyitásához és egy új támogatási kérelem elküldéséhez. A támogatási kérelem létrehozásakor Azure Stack hub szolgáltatást kell megjelölnie. Javasoljuk, hogy az ügyfelek ezt a lehetőséget használják a jegyek küldésére a globális Azure Portal használata helyett. 

Ha probléma merül fel az üzembe helyezéssel, a javítással és a frissítéssel, a hardverrel (beleértve a mezőre cserélhető egységeket is) és az összes hardveres szoftvert, például a hardver életciklus-állomásán futó szoftvert, először forduljon az OEM hardver gyártójához. 
Bármilyen más esetben forduljon a Microsoft CSS-hez.

![Az integrált rendszerek szakértői súgójának beolvasása](media/azure-stack-help-and-support/get-support-integrated.png)

A ASDK kapcsolatban a [Azure stack hub MSDN fórumának](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)támogatással kapcsolatos kérdéseire kérhet segítséget. 

Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd kattintson a **Súgó + támogatás** elemre a **Súgó és támogatás áttekintés**megnyitásához, amelyhez a fórumra mutató hivatkozás is tartozik. Az MSDN-fórumok rendszeres figyelése.  
Mivel a fejlesztői készlet egy kiértékelési környezet, a Microsoft CSS-ben nem érhető el hivatalos támogatás.

A probléma megoldásához, illetve az online képzések fejlesztéséhez és a saját képességeinek javításához az MSDN-fórumokat is elérheti. 

![Szakértői segítség kérése](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Az Azure Stack hub gyors üzembe lépésének lépései

Ez az oktatóanyag testreszabott, attól függően, hogy a ASDK vagy az integrált rendszert futtatja-e, így gyorsan elérheti a környezetét. 

![Támogatási útmutatók beszerzése](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>Diagnosztikai naplók gyűjteménye

A Microsoft számára kétféleképpen küldhet diagnosztikai naplókat: 

- [Naplók elküldése proaktív módon](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md): Ha engedélyezve van, a rendszer a napló-gyűjtést meghatározott állapot-riasztások alapján indítja el 
- [Naplók elküldése most](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md): manuálisan is kiválaszthatja a naplózási időszakot a naplófájlok időkeretének megfelelően.

![A diagnosztikai naplók gyűjtési lehetőségeinek képernyőképe](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="diagnostic-log-collection"></a>Diagnosztikai naplók gyűjteménye

A 1907-es kiadástól kezdve két új módon gyűjthet naplókat a **súgóban és támogatásban**:

- **Automatikus gyűjtés**: Ha engedélyezve van, a naplófájlok gyűjtését meghatározott állapotú riasztások aktiválják 
- **Naplók gyűjtése most**: kiválaszthatja az elmúlt hét nap 1-4 óra csúszó ablakát.

![A diagnosztikai naplók gyűjtési lehetőségeinek képernyőképe](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

Az integrált rendszerek megoszthatják a diagnosztikai naplókat a Microsoft terméktámogatási szolgáltatásával (CSS). Mivel a Azure Stack Development Kit (ASDK) egy kiértékelési környezet, a CSS nem támogatja. További információ: [Azure stack hub diagnosztikai naplójának gyűjteménye – áttekintés](azure-stack-diagnostic-log-collection-overview.md).



## <a name="help-and-support-for-earlier-releases-azure-stack-hub-pre-1905"></a>Súgó és támogatás az Azure Stack hub korábbi kiadásaihoz (pre-1905)

A korábbi Azure Stack hub-kiadások is tartalmaz egy hivatkozást, amely **segítséget nyújt** a [Azure stack hub-kezelő dokumentációjának](https://aka.ms/adminportaldocs)átirányításához.

![Támogatási útmutatók beszerzése](media/azure-stack-help-and-support/get-support-previous.png)

Ha Cloud Services-probléma merül fel, a támogatás a Microsoft ügyfél-támogatási szolgálatán (CSS) keresztül érhető el. 
Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd kattintson a **Súgó és támogatás**elemre, majd az **új támogatási kérés** lehetőségre kattintva közvetlenül elküldheti a CSS-sel egy új támogatási kérést.

Az integrált rendszerek esetében a Microsoft és az OEM-partnereink között összehangolt eszkalációs és megoldási folyamat áll rendelkezésre. 
Ha Cloud Services-probléma merül fel, a támogatás a Microsoft CSS-ben érhető el. 

Ha probléma merül fel az üzembe helyezéssel, a javítással és a frissítéssel, a hardverrel (beleértve a mezőre cserélhető egységeket is) és az összes hardveres szoftvert, például a hardver életciklus-állomásán futó szoftvert, először forduljon az OEM hardver gyártójához. 
Bármilyen más esetben forduljon a Microsoft CSS-hez.

A fejlesztői csomaggal kapcsolatban a [Azure stack hub MSDN fórumában](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)a támogatással kapcsolatos kérdéseket teheti fel. 
Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd az **új támogatási kérés** lehetőségre kattintva kérjen segítséget az Azure stack hub Közösség szakértőitől.
Mivel a fejlesztői készlet egy kiértékelési környezet, a Microsoft CSS-ben nem érhető el hivatalos támogatás.

::: moniker-end


## <a name="next-steps"></a>További lépések

- A [diagnosztikai naplók gyűjtésének](azure-stack-diagnostic-log-collection-overview-tzl.md) ismertetése
- Megtudhatja, hogyan [keresheti meg a Felhőbeli azonosítóját](azure-stack-find-cloud-id.md)
- Tudnivalók a [Azure stack hub hibaelhárításáról](azure-stack-troubleshooting.md)
