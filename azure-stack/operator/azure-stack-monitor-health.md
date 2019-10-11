---
title: Állapot és riasztások figyelése Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan figyelheti Azure Stack állapotát és riasztásait.
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
ms.date: 10/2/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 1747be1c97a706aae5d49889949fd0b0f9a70da3
ms.sourcegitcommit: dfaf0126bc9975ca1643d55f06c71df9e32ea976
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72164963"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Állapot és riasztások figyelése Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit @ no__t-0

A Azure Stack infrastruktúra-figyelési funkciókat is tartalmaz, amelyek segítségével megtekintheti egy Azure Stack régió állapotát és riasztásait. Az alapértelmezett szolgáltatói előfizetéshez tartozó felügyeleti portálon alapértelmezés szerint rögzített **régió-felügyeleti** csempe felsorolja Azure stack összes telepített régióját. A csempe az egyes régiókban az aktív kritikus és figyelmeztető riasztások számát jeleníti meg. A csempe a Azure Stack állapotának és riasztási funkciójának belépési pontja.

![A régió kezelése csempe](media/azure-stack-monitor-health/image1.png)

## <a name="understand-health-in-azure-stack"></a>A Azure Stack állapotának megismerése

Az állapot-erőforrás-szolgáltató kezeli az állapotot és a riasztásokat. Azure Stack infrastruktúra-összetevők regisztrálják az állapot-erőforrás-szolgáltatót Azure Stack telepítés és konfigurálás során. Ez a regisztráció lehetővé teszi az egyes összetevők állapotának és értesítéseinek megjelenítését. A Azure Stack állapota egyszerű fogalom. Ha egy összetevő regisztrált példányára vonatkozó riasztások léteznek, az adott összetevő állapota a legrosszabb aktív riasztás súlyosságát tükrözi: figyelmeztetés vagy kritikus.

## <a name="alert-severity-definition"></a>Riasztás súlyossági meghatározása

Azure Stack a riasztásokat csak két megszakítással emeli: **Figyelmeztetés** és **kritikus**.

- **Figyelmeztetés**  
  Az operátorok a figyelmeztető riasztást ütemezett módon tudják kezelni. A riasztás általában nem érinti a felhasználói munkaterheléseket.

- **Kritikus**  
  Az operátornak sürgősen kell foglalkoznia a kritikus riasztással. Ezek olyan problémák, amelyek jelenleg hatással vannak, vagy hamarosan hatással lesznek Azure Stack felhasználókra.


## <a name="view-and-manage-component-health-state"></a>Összetevő állapotának megtekintése és kezelése

Megtekintheti az összetevők állapotát a felügyeleti portálon, valamint a REST API és a PowerShell segítségével.

Ha meg szeretné tekinteni az állapotot a portálon, kattintson arra a régióra, amelyet meg szeretne jeleníteni a **régió-felügyeleti** csempén. Megtekintheti az infrastruktúra-szerepkörök és az erőforrás-szolgáltatók állapotát.

![Infrastruktúra-szerepkörök listája](media/azure-stack-monitor-health/image2.png)

Az erőforrás-szolgáltatóra vagy az infrastruktúra-szerepkörre kattintva részletesebb információkat jeleníthet meg.

> [!WARNING]  
> Ha az infrastruktúra-szerepkörre kattint, majd a szerepkör-példányra kattint, az **Indítás**, az **Újraindítás**vagy a **Leállítás**lehetőségek közül választhat. Ne használja ezeket a műveleteket, amikor egy integrált rendszerre alkalmazza a frissítéseket. Ezeket a beállításokat **ne használja Azure stack Development Kit** -környezetben. Ezek a beállítások csak egy integrált rendszer-környezethez készültek, ahol infrastruktúra-szerepkör esetén több szerepkör-példány is található. A fejlesztői csomagban lévő szerepkör-példány (különösen a AzS-Xrp01) újraindítása a rendszer instabilitását okozza. A hibaelhárítással kapcsolatos segítségért tegye fel a problémát a [Azure stack fórumba](https://aka.ms/azurestackforum).
>

## <a name="view-alerts"></a>Riasztások megtekintése

Az egyes Azure Stack régiók aktív riasztások listája közvetlenül a **régió-felügyeleti** panelen érhető el. Az alapértelmezett konfigurációban az első csempe a **riasztások** csempéje, amely a régió kritikus és figyelmeztető értesítéseinek összegzését jeleníti meg. Az irányítópulton rögzítheti a riasztások csempét, például a panel bármely más csempéjét a gyors eléréshez.

![Riasztások csempéje, amely figyelmeztetést jelenít meg](media/azure-stack-monitor-health/image3.png)

A **riasztások** csempe felső részének kiválasztásával navigáljon a régió összes aktív riasztásának listájához. Ha a csempe **kritikus** vagy **figyelmeztető** sorát választja, navigáljon a riasztások szűrt listájához (kritikus vagy figyelmeztetés). 

A **riasztások** panel támogatja az állapot (aktív vagy lezárt) és a súlyosság (kritikus vagy figyelmeztetés) szűrését. Az alapértelmezett nézet az összes aktív riasztást megjeleníti. Hét nap után minden lezárt riasztás el lesz távolítva a rendszerből.

>[!Note]
>Ha egy riasztás aktív marad, de egy nap alatt még nem frissült, futtathatja a [test-AzureStack](azure-stack-diagnostic-test.md) , és ha nem jelent problémát, akkor lezárhatja a riasztást.

![Szűrés ablaktábla kritikus vagy figyelmeztetési állapot alapján történő szűréshez](media/azure-stack-monitor-health/alert-view.png)

A **View API** művelet megjeleníti a listanézet létrehozásához használt REST API. Ez a művelet gyors módszert biztosít a riasztások lekérdezéséhez használható REST API szintaxis megismerésére. Ezt az API-t használhatja automatizálás vagy a meglévő adatközpont-figyelési, jelentéskészítési és jegyeladási megoldásokkal való integrációhoz.

Egy adott riasztásra kattintva megtekintheti a riasztás részleteit. A riasztás részletei a riasztáshoz társított összes mezőt megjelenítik, és lehetővé teszik a gyors navigálást a riasztás érintett összetevőjéhez és forrásához. A következő riasztás például akkor fordul elő, ha az infrastruktúra-szerepkörök egyik példánya offline állapotba kerül, vagy nem érhető el.  

![A riasztás részletei panel](media/azure-stack-monitor-health/alert-detail.png)

## <a name="repair-alerts"></a>Riasztások javítása

Néhány riasztásban kiválaszthatja a **javítás** lehetőséget.

Ha bejelöli ezt a beállítást, a **javítási** művelet a riasztásra vonatkozó lépéseket hajt végre a probléma megoldására tett kísérlet során. A kiválasztást követően a **javítási** művelet állapota portál-értesítésként érhető el.

![A javítás folyamatban van](media/azure-stack-monitor-health/repair-in-progress.png)

A **javítási** művelet bejelenti a sikeres befejezést, vagy nem sikerül befejezni a műveletet ugyanazon portál értesítési paneljén.  Ha egy riasztás esetében sikertelen volt a javítási művelet, a riasztás részleteiben újra futtathatja a **javítási** műveletet. Ha a javítási művelet sikeresen befejeződött, **ne** futtassa újra a **javítási** műveletet.

![A javítás sikeresen befejeződött](media/azure-stack-monitor-health/repair-completed.png)

Miután az infrastruktúra-szerepkör-példány ismét online állapotba került, a riasztás automatikusan bezáródik. Sok, de nem minden riasztás, a rendszer automatikusan bezárja az alapul szolgáló probléma megoldásakor. Ha Azure Stack megoldja a problémát, a javítási művelet gomb megadására szolgáló riasztások automatikusan be lesznek zárva.  Az összes többi riasztás esetében válassza a **riasztás lezárása** lehetőséget a Szervizelési lépések végrehajtása után. Ha a probléma továbbra is fennáll, Azure Stack új riasztást hoz létre. Ha megoldja a problémát, a riasztás bezárul, és nem igényel további lépéseket.

## <a name="next-steps"></a>További lépések

[Frissítések kezelése az Azure Stackben](azure-stack-updates.md)

[Területi felügyelet Azure Stack](azure-stack-region-management.md)
