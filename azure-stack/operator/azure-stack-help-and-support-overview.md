---
title: Azure Stack hub Súgó és támogatás
description: Microsoft Azure Stack hub támogatásának beszerzése.
author: PatAltimore
ms.topic: article
ms.date: 01/19/2021
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 01/19/2021
ms.openlocfilehash: fbe9221e61de46c2af28bf50268d0e23b20d2844
ms.sourcegitcommit: 69c700a456091adc31e4a8d78e7a681dfb55d248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100013251"
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

- **Dokumentáció**. [Azure stack hub-kezelő dokumentációja](index.yml) olyan fogalmakat, útmutatásokat és oktatóanyagokat tartalmaz, amelyek bemutatják, hogyan kínálhat Azure stack hub-szolgáltatásokat. Ezek a szolgáltatások többek között a következők: Virtual Machines, SQL Database, Web Apps és sok más.

- **További információ a számlázásról**. Tippek a [használat és a számlázáshoz](azure-stack-billing-and-chargeback.md).

- **Támogatási lehetőségek**. Azure Stack hub-operátorok számos olyan Azure- [támogatási lehetőség](./azure-stack-manage-basics.md) közül választhatnak, amelyek bármely vállalat igényeihez illeszkednek.

### <a name="get-expert-help"></a>Szakértői segítség kérése

Az integrált rendszerek esetében a Microsoft és az eredeti berendezésgyártó (OEM) hardveres partnerei között összehangolt eszkalációs és megoldási folyamat áll rendelkezésre.

Ha Cloud Services-probléma merül fel, a támogatás Microsoft ügyfélszolgálataon keresztül érhető el. Kiválaszthatja a **Súgó** (kérdőjel) elemet a felügyeleti portál jobb felső sarkában, majd a Súgó + **támogatás** elemre kattintva megnyithatja a **Súgó + támogatás áttekintést** , és elküldheti az új támogatási kérelmet. A támogatási kérelem létrehozásakor Azure Stack hub szolgáltatást kell megjelölnie. Javasoljuk, hogy az ügyfelek ezt a lehetőséget használják a jegyek küldésére a globális Azure Portal használata helyett.

Ha probléma merül fel az üzembe helyezéssel, a javítással és a frissítéssel, a hardverrel (beleértve a mezőre cserélhető egységeket is) és az összes hardveres szoftvert (például a hardver életciklus-állomásán futó szoftvert), először lépjen kapcsolatba az OEM hardver gyártójával. Minden más esetben lépjen kapcsolatba Microsoft ügyfélszolgálata.

![Az integrált rendszerek szakértői súgójának beolvasása](media/azure-stack-help-and-support/get-support-integrated.png)

A Azure Stack Development Kit (ASDK) esetében a támogatással kapcsolatos kérdéseket a [Azure stack hub MSDN fórumában](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)teheti meg.

Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd a Súgó + **támogatás** elemre kattintva nyissa meg a Súgó **+ támogatás áttekintést**, amely a fórumra mutató hivatkozást tartalmaz. Az MSDN-fórumok rendszeres figyelése. Mivel a ASDK egy kiértékelési környezet, Microsoft ügyfélszolgálataon keresztül nem érhető el hivatalos támogatás.

A probléma megoldásához, illetve az online képzések fejlesztéséhez és a saját képességeinek javításához az MSDN-fórumokat is elérheti.

![Azure Stack hub szakértői súgójának beolvasása](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="information-for-a-support-request"></a>Támogatási kérelem információi

A támogatási élmény felgyorsításához a következő információk szükségesek:

 - Ön Azure Stack hub Hardware partner?
 - Hány Azure Stack hub-csomópont van a rendszeren?
 - Mi a rendszer aktuális javítási szintje?
 - Melyik Build-szám a rendszer jelenleg fut?
 - Mi a felhő régiójának neve?
 - Csatlakoztatott vagy leválasztott rendszer?
 - Mikor jelentkezett először a probléma?
 - Megadhatja a pontos időt, amikor a legutóbbi biztonsági mentés meghiúsult?
 - Milyen szerepkörökre van sikertelen a biztonsági mentés?
 - Elvégezte a legutóbbi módosításokat? Például elvégezte a frissítést, a hardver módosítását vagy egy OEM-frissítés alkalmazását?
 - Meg tudja adni a naplókat a probléma kivizsgálásához?

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Az Azure Stack hub gyors üzembe lépésének lépései

Ez az oktatóanyag testreszabott, attól függően, hogy a ASDK vagy az integrált rendszert futtatja-e, így gyorsan elérheti a környezetét.

![Támogatási útmutatók beszerzése Azure Stack hub-hoz](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>Diagnosztikai naplók gyűjteménye

A következő két módon küldhet diagnosztikai naplókat a Microsoftnak:

- [Naplók elküldése proaktív módon](./diagnostic-log-collection.md#send-logs-proactively): Ha engedélyezve van, a rendszer bizonyos állapot-riasztásokkal aktiválja a naplózási gyűjteményt.
- [Naplók elküldése most](./diagnostic-log-collection.md#send-logs-now-with-the-administrator-portal): manuálisan is kiválaszthat egy adott csúszó ablakot a naplók időkeretén belül.

![A diagnosztikai naplók gyűjtésének megkezdését bemutató képernyőkép.](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="diagnostic-log-collection"></a>Diagnosztikai naplók gyűjteménye

A 1907-es kiadástól kezdve két új módon gyűjthet naplókat a **súgóban és támogatásban**:

- **Automatikus gyűjtés**: Ha engedélyezve van, a rendszer a naplók gyűjtését meghatározott állapot-riasztások alapján indítja el.
- **Naplók gyűjtése most**: kiválaszthatja az elmúlt hét nap 1-4 óra csúszó ablakát.

![Diagnosztikai naplók gyűjtésének beállításai](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

Az integrált rendszerek a Microsoft ügyfélszolgálata segítségével oszthatják meg a diagnosztikai naplókat. Mivel a Azure Stack Development Kit (ASDK) egy kiértékelési környezet, Microsoft ügyfélszolgálata nem támogatja. További információ: [Azure stack hub diagnosztikai naplójának gyűjteménye – áttekintés](./diagnostic-log-collection.md).

## <a name="help-and-support-for-earlier-releases-azure-stack-hub-pre-1905"></a>Súgó és támogatás az Azure Stack hub korábbi kiadásaihoz (pre-1905)

A korábbi Azure Stack hub-kiadások is tartalmaz egy hivatkozást, amely **segítséget nyújt** a [Azure stack hub-kezelő dokumentációjának](./index.yml)átirányításához.

![Támogatási útmutatók beszerzése](media/azure-stack-help-and-support/get-support-previous.png)

Ha Cloud Services-probléma merül fel, a támogatás Microsoft ügyfélszolgálataon keresztül érhető el. Válassza a **Súgó** (kérdőjel) lehetőséget a felügyeleti portál jobb felső sarkában, válassza a **Súgó és támogatás** lehetőséget, majd válassza az **új támogatási kérés** lehetőséget, hogy közvetlenül egy új támogatási kérést küldjön Microsoft ügyfélszolgálata.

Az integrált rendszerek esetében a Microsoft és az OEM-partnereink közötti összehangolt eszkalációs és megoldási folyamat áll rendelkezésre. Ha Cloud Services-probléma merül fel, a támogatás Microsoft ügyfélszolgálataon keresztül érhető el.

Ha probléma merül fel az üzembe helyezéssel, a javítással és a frissítéssel, a hardverrel (beleértve a mezőre cserélhető egységeket is) és az összes hardveres szoftvert, például a hardver életciklus-állomásán futó szoftvert, először lépjen kapcsolatba az OEM hardver gyártójával. Minden más esetben lépjen kapcsolatba Microsoft ügyfélszolgálata.

A ASDK kapcsolatban a [Azure stack hub MSDN fórumának](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)támogatással kapcsolatos kérdéseire kérhet segítséget.

Kattintson a **Súgó** (kérdőjel) elemre a felügyeleti portál jobb felső sarkában, majd válassza az **új támogatási kérelem** lehetőséget, hogy segítséget kapjon a Azure stack hub Közösség szakértőitől. Mivel a ASDK egy kiértékelési környezet, Microsoft ügyfélszolgálataon keresztül nem érhető el hivatalos támogatás.

::: moniker-end

## <a name="next-steps"></a>Következő lépések

- További információ a [diagnosztikai naplók gyűjtéséről](./diagnostic-log-collection.md).
- Megtudhatja, hogyan [keresheti meg a Felhőbeli azonosítóját](azure-stack-find-cloud-id.md).
- Tudnivalók a [Azure stack hub hibaelhárításáról](azure-stack-troubleshooting.md).