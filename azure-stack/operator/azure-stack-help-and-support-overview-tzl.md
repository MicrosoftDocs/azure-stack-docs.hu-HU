---
title: Microsoft Azure Stack hub Súgó és támogatás áttekintése
description: Microsoft Azure Stack hub támogatásának beszerzése.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: d4d5fbd3979d7f30354cb3716e3277b895ddac78
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520396"
---
# <a name="microsoft-azure-stack-hub-help-and-support"></a>Microsoft Azure Stack hub Súgó és támogatás

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

Ha Cloud Services-probléma merül fel, a támogatás a Microsoft ügyfél-támogatási szolgálatán (CSS) keresztül érhető el. Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd kattintson a **Súgó + támogatás** lehetőségre a Súgó és támogatás **Áttekintés** megnyitásához és egy új támogatási kérelem elküldéséhez. A támogatási kérelem létrehozásakor Azure Stack hub szolgáltatást kell megjelölnie. Javasoljuk, hogy az ügyfelek ezt a lehetőséget használják a jegyek küldésére a globális Azure Portal használata helyett. 

Ha probléma merül fel az üzembe helyezéssel, a javítással és a frissítéssel, a hardverrel (beleértve a mezőre cserélhető egységeket is) és az összes hardveres szoftvert, például a hardver életciklus-állomásán futó szoftvert, először forduljon az OEM hardver gyártójához. Bármilyen más esetben forduljon a Microsoft CSS-hez.

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


## <a name="next-steps"></a>Következő lépések

- A [diagnosztikai naplók gyűjtésének](azure-stack-diagnostic-log-collection-overview-tzl.md) ismertetése
- Megtudhatja, hogyan [keresheti meg a Felhőbeli azonosítóját](azure-stack-find-cloud-id.md)
- Tudnivalók a [Azure stack hub hibaelhárításáról](azure-stack-troubleshooting.md)
