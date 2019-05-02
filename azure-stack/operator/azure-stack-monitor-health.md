---
title: Figyelni és riasztásokat az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan figyelni és riasztásokat az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/18/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 3e37b8f45d7068586d20b9c4b3ccdabb017d0416
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64296610"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>A figyelő állapotát és a riasztások az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack tartalmaz infrastruktúramonitorozási megoldást kínál, amelyekkel megtekintheti az állapotát és a riasztásokat az adott Azure Stack-régióban. A **régiók kezelése** csempe, a felügyeleti portálon alapértelmezés szerint az alapértelmezett szolgáltatója előfizetés ki van tűzve, sorolja fel az Azure Stack üzembe helyezett minden régióban. A csempe minden régió aktív kritikus és figyelmeztetési riasztások számát jeleníti meg. A csempe állapotával és az Azure Stack riasztási funkciójának belépési pontjához.

![A régiók kezelése csempe](media/azure-stack-monitor-health/image1.png)

## <a name="understand-health-in-azure-stack"></a>Az Azure Stackben health ismertetése

Az egészségügyi erőforrás-szolgáltató kezeli, állapot és riasztások. Az Azure Stack infrastruktúra-összetevőket az egészségügyi erőforrás-szolgáltató Azure Stack központi telepítése és konfigurálása során regisztrálja. Ez a regisztráció lehetővé teszi az az állapot és riasztások az egyes összetevők. Az Azure Stackben üzemállapota egy egyszerű fogalomra utal. Ha egy összetevő regisztrált példánya esetében riasztások létezik, az összetevő állapotát tükrözi a legrosszabb aktív riasztás súlyossága: figyelmeztetési vagy kritikus fontosságú.

## <a name="alert-severity-definition"></a>Riasztás súlyossága definíciója

Az Azure Stackben riasztást csak két súlyossági szinten pedig a: **figyelmeztetés** és **kritikus**.

- **Figyelmeztetés**  
  Az operátornak kezelheti a figyelmeztető riasztás ütemezett módon. A riasztás általában nem érinti a felhasználó számítási feladatokhoz.

- **Critical**  
  Az operátornak olyan sürgősen foglalkoznia kell kritikus riasztást. Ezek a problémák, amelyek jelenleg hatással van, vagy hamarosan befolyásolja az Azure Stack felhasználóinak.


## <a name="view-and-manage-component-health-state"></a>Megtekintheti és kezelheti az összetevő állapotát

Megtekintheti az összetevő állapotát a felügyeleti portálon, és a REST API-t és a PowerShell segítségével.

A portálon az állapot megtekintéséhez kattintson a régiót, amelyben meg szeretné tekinteni a a **régiók kezelése** csempére. Infrastruktúra-szerepkörök és az erőforrás-szolgáltatók állapotát tekintheti meg.

![Infrastruktúra-szerepkörök listája](media/azure-stack-monitor-health/image2.png)

Kattinthat, hogy egy erőforrás-szolgáltató vagy az infrastruktúra-szerepkör részletesebb adatok megtekintéséhez.

> [!WARNING]  
> Kattintson egy infrastruktúra-szerepkört, majd a szerepkörpéldány van-e lehetőség **Start**, **indítsa újra a**, vagy **leállítási**. Amikor a frissítések alkalmazása az integrált rendszer ne használja ezeket a műveleteket. Ezenkívül tegye **nem** Azure Stack Development Kit környezetben használja az alábbi beállításokat. Ezek a beállítások csak tervezett integrált rendszerek környezetekben, ahol az infrastruktúra-szerepkör példányonként egynél több szerepkörhöz. A csomagban (különösen AzS-Xrp01) szerepkörpéldány újraindítása hatására a rendszer instabil. Hibaelhárítási, közzé a problémát a [Azure Stack-fórum](https://aka.ms/azurestackforum).
>

## <a name="view-alerts"></a>Riasztások megtekintése

Aktív riasztások az egyes Azure Stack régiók listája érhető el közvetlenül a **régiók kezelése** panelen. Az alapértelmezett beállítás az első csempe a **riasztások** csempe megjeleníti a kritikus fontosságú összegzését, és a régió figyelmeztető riasztások. A riasztások csempén, mint bármely más csempe ezen a panelen a gyors hozzáférés az irányítópulton is rögzíthet.

![Riasztások csempe, amely akkor vált figyelmeztetési állapotra](media/azure-stack-monitor-health/image3.png)

A felső részén válassza a **riasztások** csempe, lépjen a régió összes aktív riasztás listáját. Ha bármelyik gombra a **kritikus** vagy **figyelmeztetés** sortételt belül a csempét, lépjen a riasztások (kritikus vagy figyelmeztetés) szűrt listáját. 

A **riasztások** panel képes szűrést, egyszerre (aktív vagy lezárt) állapot és súlyosság (kritikus vagy figyelmeztetés) támogatja. Az alapértelmezett nézet az összes aktív riasztásokat jeleníti meg. Az összes lezárt riasztások hét nap után törlődnek a rendszerből.

![Szűrő ablaktábla szűrés kritikus vagy figyelmeztetési állapot](media/azure-stack-monitor-health/alert-view.png)

A **nézet API** műveletet a REST API, amely a lista nézet létrehozásához használt jeleníti meg. Ez a művelet itt gyorsan megismerkedhet a lekérdezési riasztások segítségével REST API-val szintaxist. Az automation vagy -integráció a figyelési, jelentéskészítési és megoldások jegykiadás növelje meglévő adatközpontjának is használhat az API-t.

Kattintson egy meghatározott riasztást a riasztás részleteinek megtekintéséhez. A riasztás részleteinek megjelenítése társítva a riasztást, és engedélyezze az érintett összetevőt, és a riasztás forrására gyors navigációs mezők. Például a következő riasztás történik, ha egy, az infrastruktúra-szerepkör példányainak offline állapotba kerül, vagy nem érhető el.  

![A riasztás részleteit megjelenítő panelen](media/azure-stack-monitor-health/alert-detail.png)

## <a name="repair-alerts"></a>Riasztás javítása

Választhat **javítási** egyes riasztásokban.

Kiválasztásakor az **javítási** művelet, a probléma megoldásához próbálja meg a riasztásra adott lépést hajt végre. Miután választott állapotát a **javítási** művelet akkor érhető el, mint a portál értesítései.

![A javítás folyamatban](media/azure-stack-monitor-health/repair-in-progress.png)

A **javítási** jelenteni a művelet sikeres vagy sikertelen ugyanaz a portál értesítései panel a művelet végrehajtásához.  A javítási művelet sikertelen, riasztás, ha újra futtathatja a **javítási** a riasztás részleteinek műveletet. Ha a javítási művelet sikeresen befejeződött, **nem** futtassa újra a **javítási** művelet.

![A javítás sikeresen befejeződött](media/azure-stack-monitor-health/repair-completed.png)

Miután infrastruktúra szerepkörpéldány újra online állapotba kerül, ez a riasztás automatikusan bezáródik. Számos, de nem minden riasztás automatikusan bezárja, ha a mögöttes probléma megoldódott. Ha az Azure Stack megoldja a problémát a javítási művelet gomb nyújtó riasztások automatikusan bezáródik.  Válassza ki az összes többi riasztás **riasztás bezárása** javítási lépések végrehajtása után. Ha a probléma tartósan fennáll, az Azure Stack egy új riasztást állít elő. Ha a probléma megoldásához, a riasztás lezárt marad, és nincs több lépésre van szükség.

## <a name="next-steps"></a>További lépések

[Frissítések kezelése az Azure Stackben](azure-stack-updates.md)

[Régiók kezelése az Azure Stackben](azure-stack-region-management.md)

<!-- Update_Description: wording update -->