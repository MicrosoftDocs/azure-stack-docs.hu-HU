---
title: Figyelése és felügyelete az Azure Stack VaaS Portalon tesztek |} A Microsoft Docs
description: Figyelheti és kezelheti az Azure Stack VaaS Portalon teszteket.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 9b64631b274423edda35189a942e2f142ae591f1
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64299745"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>Figyelheti és kezelheti a VaaS portálon tesztek

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Feladatütemezést, a tesztek alapján az Azure Stack megoldás után az érvényesítési (VaaS) szolgáltatás elkezdi teszt végrehajtási állapotát. Ezek az információk a műveleteket, például újraütemezése és tesztek megszakítása mellett VaaS portálon érhető el.

## <a name="navigate-to-the-workflow-tests-summary-page"></a>Keresse meg a munkafolyamat-teszt összefoglaló lap

1. A megoldás irányítópultján válassza ki egy meglévő megoldás, amely legalább egy munkafolyamat rendelkezik.

    ![A munkafolyamat-csempék](media/tile_all-workflows.png)

1. Válassza ki **kezelés** a munkafolyamat csempére. A következő oldalon a munkafolyamatok létrehozása a kiválasztott megoldáshoz tartozó sorolja fel.

1. Válassza ki a munkafolyamat nevének, nyissa meg a teszt összegzése.

## <a name="change-workflow-parameters"></a>Munkafolyamat-paraméterek módosítása

Szerkesztheti a [teszt paramétereiben](azure-stack-vaas-parameters.md#test-parameters) bármilyen munkafolyamat a munkafolyamat létrehozása során megadott.

1. A tesztek összesítő lapon válassza ki a **szerkesztése** gombra.

1. Adja meg új értékeket [munkafolyamat általános paramétereit az Azure Stack érvényesítési szolgáltatásként](azure-stack-vaas-parameters.md).

1. Válassza ki **küldés** , mentse az értékeket.

> [!NOTE]
> Az a **Tesztmenetek** munkafolyamat, szüksége lesz a teszt kijelölés befejeződését, és lépjen az Áttekintés lap az új paraméterértékeket mentése előtt.

### <a name="add-tests-test-pass-only"></a>Adja hozzá a tesztek (csak tesztelési fázis)

A **Tesztmenetek** munkafolyamatokat, mind a **tesztek hozzáadása** és **szerkesztése** gombok lehetővé teszik, hogy a munkafolyamat új vizsgálat ütemezése.

> [!TIP]
> Válassza ki **tesztek hozzáadása** Ha csak új tesztek ütemezése és paramétereinek szerkesztése nem kell egy **Tesztmenetek** munkafolyamat.

## <a name="managing-test-instances"></a>Test-példányok felügyeletére

Munkaköröket külsős informatikusok futtatások (azaz a **Tesztmenetek** munkafolyamat), a tesztek összegző lap felsorolja az Azure Stack megoldás ütemezett vizsgálatok.

Hivatalos futtatások (azt jelenti, a **érvényesítési** munkafolyamatok), a tesztek összegző lap felsorolja a tesztek elvégzése az Azure Stack megoldás érvényesítés szükséges. Ellenőrzések naponkénti gyakoriságra vannak ezen a lapon.

Minden egyes ütemezett vizsgálat példány a következő információkat jelenít meg:

| Oszlop | Leírás |
| --- | --- |
| Teszt neve | A név és a teszt verziója. |
| Category | A teszt célját. |
| Létrehozva | A vizsgálat ütemezett időpontja. |
| Elindítva | Az idő, amikor a teszt végrehajtása elindult. |
| Időtartam | Mennyi ideig futott a vizsgálatot. |
| status | Az állapot vagy a teszt eredménye. Végrehajtás előtti vagy a folyamatban lévő állapotok a: `Pending`, `Running`. A Terminálszolgáltatások állapotok a: `Cancelled`, `Failed`, `Aborted`, `Succeeded`. |
| Ügynök neve | Az ügynök, amely futtatta a teszt neve. |
| Összes művelet | A kísérlet történt a teszt során műveletek teljes száma. |
| Sikeres műveletek | A teszt során sikeresen végrehajtott műveletek száma. |
|  Sikertelen műveletek | A vizsgálat során a sikertelen műveletek száma. |

### <a name="actions"></a>Műveletek

Minden teszt példány felsorolja az elérhető művelet elvégezhető a helyi menü kiválasztásakor **[...]**  a teszt példányok táblában.

#### <a name="view-information-about-the-test-definition"></a>A test-definíció adatainak megtekintése

Válassza ki **információk megtekintéséhez** a helyi menüből a test-definíció kapcsolatos általános információk megtekintéséhez. Ez ugyanaz a neve és verziója minden teszt példány osztozik.

| Teszt tulajdonság | Leírás |
| -- | -- |
| Teszt neve | A teszt neve. |
| Teszt verzió | A teszt verziója. |
| Közzétevő | A közzétevő a teszt. |
| Category |  A teszt célját. |
| Cél-szolgáltatások | Az Azure Stack-tesztelt szolgáltatások. |
| Leírás | A teszt leírása. |
| Becsült időtartam (perc) | A teszt a várt futásidőt. |
| Hivatkozások | A tesztelési vagy kapcsolódási pontokat kapcsolatos információkat. |

#### <a name="view-test-instance-parameters"></a>Példány tesztparaméterekre nézet

Válassza ki **paraméterek megtekintése** ütemezés időpontjában a teszt példányhoz megadott paraméterek megtekintése a helyi menüből. Bizalmas karakterláncok, jelszavak nem jelennek meg. Ez a művelet csak az ütemezett vizsgálatok esetén érhető el.

Ezt az ablakot az összes teszt példány a következő metaadatokat tartalmazza:

| Vlastnost instance tesztelése | Leírás |
| -- | -- |
| Teszt neve | A teszt neve. |
| Teszt verzió | A teszt verziója. |
| Teszt Példányazonosító | GUID, a teszt meghatározott példányának azonosítója. |

#### <a name="view-test-instance-operations"></a>Teszt példány műveletek megtekintése

Válassza ki **műveleteinek megtekintéséhez** a környezetből menü műveletek állapotának részletes leírását megtekintheti a teszt során végrehajtott. Ez a művelet csak az ütemezett vizsgálatok esetén érhető el.

![műveletek megtekintése](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>Befejezett teszt példány naplók letöltése

Válassza ki **naplók letöltéséhez** letöltése a helyi menüből a `.zip` teszt végrehajtása során a naplók kimeneti fájlt. Ez a művelet csak olyan teszteket, befejezett, azaz egy tesztet vagy állapottal érhető el `Cancelled`, `Failed`, `Aborted`, vagy `Succeeded`.

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>Egy teszt példány le, vagy egy vizsgálat ütemezése

A kezelés lapon tesztek ütemezése attól függ, hogy a vizsgálat fut, a munkafolyamat típusát.

##### <a name="test-pass-workflow"></a>Tesztmenetek munkafolyamat

A tesztelési fázisban munkafolyamat **újraütemezése** egy teszt példány újrahasználja ugyanazokat a paramétereket az eredeti teszt példánynévvel és *váltja fel* az eredeti eredmény, beleértve a naplókat. Adja meg az például jelszavakat bizalmas karakterláncok újból meg kell.

1. Válassza ki **átütemezése** , nyisson meg egy parancssort a újraütemezése a test-példány a helyi menüből.

1. Adja meg a megfelelő paramétereket.

1. Válassza ki **küldés** ütemezze újra a teszt-példány, és cserélje le a meglévő példány.

##### <a name="validation-workflows"></a>Érvényesítési munkafolyamatok

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>Egy teszt példány megszakítása

Ütemezett vizsgálat Ha állapota lehet érvényteleníteni `Pending` vagy `Running`.  

1. Válassza ki **Mégse** , nyisson meg egy parancssort a test-példány leállítása a helyi menüből.

1. Válassza ki **küldés** megszakítja a test-példány.

## <a name="next-steps"></a>További lépések

- [Érvényesítési szolgáltatás hibaelhárítása](azure-stack-vaas-troubleshoot.md)
