---
title: Állapot és riasztások figyelése a Azure Stack-MDC | Microsoft Docs
description: Megtudhatja, hogyan figyelheti Azure Stack az állapotot és a riasztásokat egy moduláris adatközpont (MDC) számára.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: mabrigg
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: bdc027e8c61053fddddd5ad365eff8069acb840d
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910822"
---
# <a name="monitor-health-and-alerts-in-azure-stack---modular-data-center-mdc"></a>Állapot és riasztások figyelése Azure Stack-moduláris adatközpontban (MDC)

A Azure Stack infrastruktúra-figyelési funkciókat is tartalmaz, amelyek segítségével megtekintheti egy Azure Stack régió állapotát és riasztásait. A **régió-felügyeleti** csempe a Azure stack összes telepített régióját listázza. Alapértelmezés szerint az alapértelmezett szolgáltatói előfizetéshez a felügyeleti portálon van rögzítve. A csempe az egyes régiókban az aktív kritikus és figyelmeztető riasztások számát jeleníti meg. A csempe a Azure Stack állapotának és riasztási funkciójának belépési pontja.

![A Azure Stack felügyeleti portál régió-felügyeleti csempéje](media/azure-stack-monitor-health/region-management.png)

## <a name="understand-health-in-azure-stack"></a>A Azure Stack állapotának megismerése

Az állapot-erőforrás-szolgáltató kezeli az állapotot és a riasztásokat. Azure Stack infrastruktúra-összetevők regisztrálják az állapot-erőforrás-szolgáltatót Azure Stack telepítés és konfigurálás során. Ez a regisztráció lehetővé teszi az egyes összetevők állapotának és értesítéseinek megjelenítését. A Azure Stack állapota egyszerű fogalom. Ha egy összetevő regisztrált példányára vonatkozó riasztások léteznek, az adott összetevő állapota a legrosszabb aktív riasztás súlyosságát tükrözi: figyelmeztetés vagy kritikus.

## <a name="alert-severity-definition"></a>Riasztás súlyossági meghatározása

Azure Stack a riasztásokat csak két megszakítással emeli ki: **Figyelmeztetés** és **kritikus**.

- **Figyelmeztetés**  
  Az operátorok a figyelmeztető riasztást ütemezett módon tudják kezelni. A riasztás általában nem érinti a felhasználói munkaterheléseket.

- **Kritikus**  
  Az operátornak sürgősen kell foglalkoznia a kritikus riasztással. Ezek a riasztások olyan problémákat jeleznek, amelyek jelenleg hatással vannak, vagy hamarosan hatással lesznek Azure Stack felhasználókra.


## <a name="view-and-manage-component-health-state"></a>Összetevő állapotának megtekintése és kezelése

Megtekintheti az összetevők állapotát a felügyeleti portálon, valamint a REST API és a PowerShell segítségével.

Ha meg szeretné tekinteni az állapotot a portálon, kattintson arra a régióra, amelyet meg szeretne jeleníteni a **régió-felügyeleti** csempén. Megtekintheti az infrastruktúra-szerepkörök és az erőforrás-szolgáltatók állapotát.

![Infrastruktúra-szerepkörök listája](media/azure-stack-monitor-health/roles.png)

Az erőforrás-szolgáltatóra vagy az infrastruktúra-szerepkörre kattintva részletesebb információkat jeleníthet meg.

> [!WARNING]  
> Ha az infrastruktúra-szerepkörre kattint, majd a szerepkör-példányra kattint, az **Indítás**, az **Újraindítás** vagy a **Leállítás** lehetőségek közül választhat. Ne használja ezeket a műveleteket, amikor egy integrált rendszerre alkalmazza a frissítéseket. <!-- TZLASDKFIXAlso, do **not** use these options in an Azure Stack Development Kit (ASDK) environment. These options are only designed for an integrated systems environment, where there's more than one role instance per infrastructure role. Restarting a role instance (especially AzS-Xrp01) in the ASDK causes system instability.--> A hibaelhárítással kapcsolatos segítségért tegye fel a problémát a [Azure stack fórumba](https://aka.ms/azurestackforum).
>

## <a name="view-alerts"></a>Riasztások megtekintése

Az egyes Azure Stack régiók aktív riasztások listája közvetlenül a **régió-felügyeleti** panelen érhető el. Az alapértelmezett konfigurációban az első csempe a **riasztások** csempéje, amely a régió kritikus és figyelmeztető értesítéseinek összegzését jeleníti meg. Az irányítópulton rögzítheti a riasztások csempét, például a panel bármely más csempéjét a gyors eléréshez.

![Riasztások csempe, amely a Azure Stack felügyeleti portálon figyelmeztetést jelenít meg](media/azure-stack-monitor-health/alerts.png)

 Ha meg szeretné tekinteni a régió összes aktív riasztásának listáját, válassza ki a **riasztások** csempe felső részét. A riasztások szűrt listájának megtekintéséhez (kritikus vagy figyelmeztetés) válassza a csempe **kritikus** vagy **figyelmeztető** sora elemét.

A **riasztások** panel támogatja az állapot (aktív vagy lezárt) és a súlyosság (kritikus vagy figyelmeztetés) szűrését. Az alapértelmezett nézet az összes aktív riasztást megjeleníti. Hét nap után minden lezárt riasztás el lesz távolítva a rendszerből.

>[!Note]
>Ha egy riasztás aktív marad, de egy nap alatt még nem frissült, futtathatja a [test-AzureStack](../../operator/azure-stack-diagnostic-test.md) , és ha nem jelent problémát, akkor lezárhatja a riasztást.

![Szűrés ablaktábla kritikus vagy figyelmeztetési állapot alapján történő szűréséhez Azure Stack felügyeleti portálon](media/azure-stack-monitor-health/alert-view.png)

A **View API** művelet megjeleníti a listanézet létrehozásához használt REST API. Ez a művelet gyors módszert biztosít a riasztások lekérdezéséhez használható REST API szintaxis megismerésére. Ezt az API-t használhatja automatizálás vagy a meglévő adatközpont-figyelési, jelentéskészítési és jegyeladási megoldásokkal való integrációhoz.

Egy adott riasztásra kattintva megtekintheti a riasztás részleteit. A riasztás részletei megjelenítik a riasztáshoz társított összes mezőt, és lehetővé teszik a gyors navigálást a riasztás érintett összetevőjéhez és forrásához. A következő riasztás például akkor fordul elő, ha az infrastruktúra-szerepkörök egyik példánya offline állapotba kerül, vagy nem érhető el. A cikk megtekintéséhez tekintse meg a riasztás részleteit ismertető **szakaszban található** [Kereszthivatkozási hivatkozást](aks-refer-table-tzl.md) .

![A riasztás részletei panel Azure Stack felügyeleti portálon](media/azure-stack-monitor-health/alert-detail.png)

## <a name="repair-alerts"></a>Riasztások javítása

Néhány riasztásban kiválaszthatja a **javítás** lehetőséget.

Ha bejelöli ezt a beállítást, a **javítási** művelet a riasztásra vonatkozó lépéseket hajt végre a probléma megoldására tett kísérlet során. A kiválasztást követően a **javítási** művelet állapota portál-értesítésként érhető el.

![A javítási riasztás művelete folyamatban van](media/azure-stack-monitor-health/repair-in-progress.png)

A **javítási** művelet bejelenti a sikeres befejezést, vagy nem sikerül befejezni a műveletet ugyanazon portál értesítési paneljén.  Ha egy riasztás esetében sikertelen volt a javítási művelet, a riasztás részleteiben újra futtathatja a **javítási** műveletet. Ha a javítási művelet sikeresen befejeződött, **ne** futtassa újra a **javítási** műveletet.

![A javítási művelet sikeresen befejeződött.](media/azure-stack-monitor-health/repair-completed.png)

Miután az infrastruktúra-szerepkör-példány ismét online állapotba került, a riasztás automatikusan bezáródik. Sok, de nem minden riasztás, automatikusan be lesz zárva, amikor az alapul szolgáló probléma megoldódik. Ha Azure Stack megoldja a problémát, a javítási művelet gomb megadására szolgáló riasztások automatikusan be lesznek zárva. Az összes többi riasztás esetében válassza a **riasztás lezárása** lehetőséget a szervizelés lépéseinek elvégzése után. Ha a probléma továbbra is fennáll, Azure Stack új riasztást hoz létre. Ha megoldja a problémát, a riasztás bezárul, és nem igényel további lépéseket.

## <a name="next-steps"></a>Következő lépések

[Frissítések kezelése az Azure Stackben](../../operator/azure-stack-updates.md)

[Területi felügyelet Azure Stack](../../operator/azure-stack-region-management.md)
