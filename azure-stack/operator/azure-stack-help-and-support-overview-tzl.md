---
title: Azure Stack hub Súgó és támogatás
description: Microsoft Azure Stack hub támogatásának beszerzése.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: 20d20e5e53a8826ea0514e894bacbfeeeda6c540
ms.sourcegitcommit: 3fd4a38dc8446e0cdb97d51a0abce96280e2f7b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580130"
---
# <a name="azure-stack-hub-help-and-support"></a>Azure Stack hub Súgó és támogatás

::: moniker range=">= azs-2002"

Azure Stack hub-kezelők a **Súgó és támogatás** segítségével gyűjthetik össze a diagnosztikai naplókat, és elküldhetik azokat a Microsoftnak a hibaelhárításhoz. A Azure Stack hub portál **Súgó + támogatás** szolgáltatását a felügyeleti portálról érheti el. Olyan erőforrásokkal rendelkezik, amelyekkel a kezelők további információt kaphatnak a Azure Stackről, megtekinthetik támogatási lehetőségeiket, és szakértői segítséget kérhetnek.  

![Súgó és támogatás elérése Azure Stack hub felügyeleti portálján](media/azure-stack-help-and-support/help-and-support.png)

## <a name="help-resources"></a>Súgó erőforrásai

A kezelők a **Súgó + támogatás** segítségével többet tudhatnak meg Azure stack hub-ról, a támogatási lehetőségekről, valamint a szakértői segítségről.

### <a name="things-to-try-first"></a>Az első kipróbálható dolgok

A **Súgó és támogatás** felső részén olyan dolgokat érdemes kipróbálni, mint például az új fogalmak megismerése, a számlázás működésének megismerése, illetve a rendelkezésre álló támogatási lehetőségek megtekintése.

![Önkiszolgáló támogatás az Azure Stack hub-ban](media/azure-stack-help-and-support/get-support-tiles.png)

- **Dokumentáció**. [Azure stack hub-kezelő dokumentációja](index.yml) olyan fogalmakat, útmutatókat és oktatóanyagokat tartalmaz, amelyek bemutatják, hogyan kínálhat Azure stack hub-szolgáltatásokat. Ezek a szolgáltatások többek között a következők: Virtual Machines, SQL Database, Web Apps és sok más.

- **További információ a számlázásról**. Tippek a [használat és a számlázáshoz](azure-stack-billing-and-chargeback.md).

- **Támogatási lehetőségek**. Azure Stack hub-operátorok számos olyan Azure- [támogatási lehetőség](https://aka.ms/azstacksupport) közül választhatnak, amelyek bármely vállalat igényeihez illeszkednek.

### <a name="get-expert-help"></a>Szakértői segítség kérése

Az integrált rendszerek esetében a Microsoft és az eredeti berendezésgyártó (OEM) hardveres partnerei között összehangolt eszkalációs és megoldási folyamat áll rendelkezésre.

Ha Cloud Services-probléma merül fel, a támogatás a Microsoft ügyfél-támogatási szolgálatán (CSS) keresztül érhető el. Kiválaszthatja a **Súgó** (kérdőjel) elemet a felügyeleti portál jobb felső sarkában, majd a Súgó + **támogatás** elemre kattintva megnyithatja a **Súgó + támogatás áttekintést** , és elküldheti az új támogatási kérelmet. A támogatási kérelem létrehozásakor Azure Stack hub szolgáltatást kell megjelölnie. Javasoljuk, hogy az ügyfelek ezt a lehetőséget használják a jegyek küldésére a globális Azure Portal használata helyett.

Ha probléma merül fel az üzembe helyezéssel, a javítással és a frissítéssel, a hardverrel (beleértve a mezőre cserélhető egységeket is) és az összes hardveres szoftvert (például a hardver életciklus-állomásán futó szoftvert), először lépjen kapcsolatba az OEM hardver gyártójával. Bármilyen más esetben forduljon a Microsoft CSS-hez.

![Az integrált rendszerek szakértői súgójának beolvasása](media/azure-stack-help-and-support/get-support-integrated.png)

A Azure Stack Development Kit (ASDK) esetében a támogatással kapcsolatos kérdéseket a [Azure stack hub MSDN fórumában](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)teheti meg.

Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd a Súgó + **támogatás** elemre kattintva nyissa meg a Súgó **+ támogatás áttekintést**, amely a fórumra mutató hivatkozást tartalmaz. Az MSDN-fórumok rendszeres figyelése. Mivel a ASDK egy kiértékelési környezet, a Microsoft CSS-ben nem érhető el hivatalos támogatás.

A probléma megoldásához, illetve az online képzések fejlesztéséhez és a saját képességeinek javításához az MSDN-fórumokat is elérheti.

![Azure Stack hub szakértői súgójának beolvasása](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Az Azure Stack hub gyors üzembe lépésének lépései

Ez az oktatóanyag testreszabott, attól függően, hogy a ASDK vagy az integrált rendszert futtatja-e, így gyorsan elérheti a környezetét.

![Támogatási útmutatók beszerzése Azure Stack hub-hoz](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>Diagnosztikai naplók gyűjteménye

A következő két módon küldhet diagnosztikai naplókat a Microsoftnak:

- [Naplók elküldése proaktív módon](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md): Ha engedélyezve van, a rendszer bizonyos állapot-riasztásokkal aktiválja a naplózási gyűjteményt.
- [Naplók elküldése most](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md): manuálisan is kiválaszthat egy adott csúszó ablakot a naplók időkeretén belül.

![Diagnosztikai naplók gyűjtésének beállításai](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="diagnostic-log-collection"></a>Diagnosztikai naplók gyűjteménye

A 1907-es kiadástól kezdve két új módon gyűjthet naplókat a **súgóban és támogatásban**:

- **Automatikus gyűjtés**: Ha engedélyezve van, a rendszer a naplók gyűjtését meghatározott állapot-riasztások alapján indítja el.
- **Naplók gyűjtése most**: kiválaszthatja az elmúlt hét nap 1-4 óra csúszó ablakát.

![Diagnosztikai naplók gyűjtésének beállításai](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

Az integrált rendszerek megoszthatják a diagnosztikai naplókat a Microsoft terméktámogatási szolgáltatásával (CSS). Mivel a Azure Stack Development Kit (ASDK) egy kiértékelési környezet, a CSS nem támogatja. További információ: [Azure stack hub diagnosztikai naplójának gyűjteménye – áttekintés](azure-stack-diagnostic-log-collection-overview.md).

## <a name="help-and-support-for-earlier-releases-azure-stack-hub-pre-1905"></a>Súgó és támogatás az Azure Stack hub korábbi kiadásaihoz (pre-1905)

A korábbi Azure Stack hub-kiadások is tartalmaz egy hivatkozást, amely **segítséget nyújt** a [Azure stack hub-kezelő dokumentációjának](https://aka.ms/adminportaldocs)átirányításához.

![Támogatási útmutatók beszerzése](media/azure-stack-help-and-support/get-support-previous.png)

Ha Cloud Services-probléma merül fel, a támogatás a Microsoft ügyfél-támogatási szolgálatán (CSS) keresztül érhető el. Válassza a **Súgó** (kérdőjel) lehetőséget a felügyeleti portál jobb felső sarkában, majd válassza a **Súgó és támogatás**lehetőséget, majd válassza az **új támogatási kérés** lehetőséget, hogy közvetlenül nyújtson be egy új támogatási kérést a CSS használatával.

Az integrált rendszerek esetében a Microsoft és az OEM-partnereink közötti összehangolt eszkalációs és megoldási folyamat áll rendelkezésre. Ha Cloud Services-probléma merül fel, a támogatás a Microsoft CSS-ben érhető el.

Ha probléma merül fel az üzembe helyezéssel, a javítással és a frissítéssel, a hardverrel (beleértve a mezőre cserélhető egységeket is) és az összes hardveres szoftvert, például a hardver életciklus-állomásán futó szoftvert, először lépjen kapcsolatba az OEM hardver gyártójával. Bármilyen más esetben forduljon a Microsoft CSS-hez.

A ASDK kapcsolatban a [Azure stack hub MSDN fórumának](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)támogatással kapcsolatos kérdéseire kérhet segítséget.

Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd válassza az **új támogatási kérelem** lehetőséget, hogy segítséget kapjon a Azure stack hub Közösség szakértőitől. Mivel a ASDK egy kiértékelési környezet, a Microsoft CSS-ben nem érhető el hivatalos támogatás.

::: moniker-end

## <a name="next-steps"></a>További lépések

- További információ a [diagnosztikai naplók gyűjtéséről](azure-stack-diagnostic-log-collection-overview-tzl.md).
- Megtudhatja, hogyan [keresheti meg a Felhőbeli azonosítóját](azure-stack-find-cloud-id.md).
- Tudnivalók a [Azure stack hub hibaelhárításáról](azure-stack-troubleshooting.md).
