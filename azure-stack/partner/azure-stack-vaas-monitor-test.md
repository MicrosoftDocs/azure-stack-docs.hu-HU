---
title: Tesztek kezelése az Azure Stack hub ellenőrzési portálján
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan kezelheti a teszteket az Azure Stack hub ellenőrzési portálján.
author: mattbriggs
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 28791e9687dead6a571097e23258fb5b6aff1200
ms.sourcegitcommit: 4922a14fdbc8a3b67df065336e8a21a42f224867
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2020
ms.locfileid: "88764766"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>Tesztek monitorozása és kezelése az alapkonfiguráció-portálon

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az Azure Stack hub-megoldásra vonatkozó tesztek ütemezése után a szolgáltatásként történő érvényesítés megkezdi a teszt végrehajtási állapotának bejelentését. Ezek az információk az Azure Stack hub ellenőrzési portálján érhetők el, valamint olyan műveletek, mint például a tesztek újraütemezése és megszakítása.

## <a name="navigate-to-the-workflow-tests-summary-page"></a>Navigáljon a munkafolyamat-tesztek összegzése lapra

1. A megoldások irányítópultján válasszon ki egy meglévő megoldást, amelynek legalább egy munkafolyamata van.

    ![Munkafolyamat-csempék a megoldások irányítópultján](media/tile_all-workflows.png)

1. Válassza a **kezelés** lehetőséget a munkafolyamat csempén. A következő oldalon a kiválasztott megoldáshoz létrehozott munkafolyamatok szerepelnek.

1. Válassza ki a munkafolyamat nevét a teszt összegzésének megnyitásához.

## <a name="change-workflow-parameters"></a>Munkafolyamat paramétereinek módosítása

A munkafolyamat létrehozása során megadott [tesztelési paramétereket](azure-stack-vaas-parameters.md#test-parameters) bármilyen típusú munkafolyamathoz módosíthatja.

1. A tesztek összegzése lapon válassza a **Szerkesztés**lehetőséget.

1. Adja meg az új értékeket a [Azure stack hub érvényesítéséhez szolgáltatásként használt általános munkafolyamat-paramétereknek](azure-stack-vaas-parameters.md)megfelelően.

1. Az értékek mentéséhez válassza a **Submit (Küldés** ) lehetőséget.

> [!NOTE]
> A **test pass** munkafolyamatban be kell fejeznie a teszt kijelölését, és a felülvizsgálati oldalon kell megadnia az új paraméterérték mentését.

### <a name="add-tests-test-pass-only"></a>Tesztek hozzáadása (csak tesztelési menet)

A **teszt** - **átadási** munkafolyamatokban a tesztek és **szerkesztési** gombok hozzáadása lehetővé teszi, hogy új teszteket ütemezzen a munkafolyamatban.

> [!TIP]
> Válassza a **tesztek hozzáadása** lehetőséget, ha új teszteket szeretne ütemezni, és nem szükséges módosítania a **tesztelési fázisok** munkafolyamat paramétereit.

## <a name="manage-test-instances"></a>Tesztelési példányok kezelése

A nem hivatalos futtatások (a **test pass** munkafolyamat) esetében a tesztek összegzése lap felsorolja az Azure stack hub-megoldásra ütemezett teszteket.

A hivatalos futtatások (az **ellenőrzési** munkafolyamatok) esetében a tesztek összegzése lap felsorolja az Azure stack hub-megoldás érvényesítésének végrehajtásához szükséges teszteket. Az érvényesítési tesztek ezen a lapon vannak ütemezve.

Minden ütemezett tesztelési példány a következő információkat jeleníti meg:

| Oszlop | Leírás |
| --- | --- |
| Teszt neve | A teszt neve és verziója. |
| Kategória | A teszt célja. |
| Létrehozva | A teszt ütemezésének időpontja. |
| Első lépések | A teszt elindításának időpontja. |
| Időtartam | A teszt futtatásának időtartama. |
| Állapot | A teszt állapota vagy eredménye. A végrehajtás előtti vagy folyamatban lévő állapotok a következők: `Pending` , `Running` . A terminál állapota a következők: `Cancelled` ,, `Failed` `Aborted` , `Succeeded` . |
| Ügynök neve | Annak az ügynöknek a neve, amely a tesztet futtatta. |
| Összes művelet | A teszt során megkísérelt műveletek teljes száma. |
| Átadott műveletek | A teszt során sikeres műveletek száma. |
|  Sikertelen műveletek | A teszt során meghiúsult műveletek száma. |

### <a name="actions"></a>Műveletek

Minden egyes tesztelési példány felsorolja az elérhető műveleteket, amelyeket a tesztelési példányok tábla helyi menüjének **[...]** kiválasztásakor végezhet el.

#### <a name="view-information-about-the-test-definition"></a>A tesztelési definícióval kapcsolatos információk megtekintése

A helyi menüben válassza az **információk megtekintése** lehetőséget a tesztelési definícióval kapcsolatos általános információk megtekintéséhez. Ezt az információt az egyes tesztelési példányok ugyanazzal a névvel és verzióval osztják meg.

| Teszt tulajdonság | Leírás |
| -- | -- |
| Teszt neve | A teszt neve. |
| Teszt verziója | A teszt verziója. |
| Publisher | A teszt közzétevője. |
| Kategória |  A teszt célja. |
| Megcélzott szolgáltatások | A tesztelt Azure Stack hub-szolgáltatások. |
| Leírás | A teszt leírása. |
| Becsült időtartam (perc) | A teszt várt futtatókörnyezete. |
| Hivatkozások | A kapcsolat tesztelésére vagy a kapcsolattartási pontokra vonatkozó információk. |

#### <a name="view-test-instance-parameters"></a>Teszt példány paramétereinek megtekintése

A helyi menüben válassza a **paraméterek megjelenítése** lehetőséget, hogy megtekintse a tesztelési példányhoz megadott paramétereket az ütemezett időpontban. A bizalmas karakterláncok, például a jelszavak nem jelennek meg. Ez a művelet csak ütemezett tesztek esetén érhető el.

Ez az ablak a következő metaadatokat tartalmazza az összes tesztelési példányhoz:

| Teszt példány tulajdonsága | Leírás |
| -- | -- |
| Teszt neve | A teszt neve. |
| Teszt verziója | A teszt verziója. |
| Tesztelési példány azonosítója | A teszt adott példányát azonosító GUID. |

#### <a name="view-test-instance-operations"></a>Tesztelési példányok műveleteinek megtekintése

A teszt során végrehajtott műveletek részletes állapotának megtekintéséhez válassza a helyi menüben a **műveletek megtekintése** lehetőséget. Ez a művelet csak ütemezett tesztek esetén érhető el.

![Műveletek megtekintése](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>Befejezett tesztelési példány naplóinak letöltése

A helyi menüből válassza a **naplók letöltése** lehetőséget, hogy letöltse `.zip` a naplók kimenetét a tesztek végrehajtása során. Ez a művelet csak a befejezett tesztek esetében érhető el, ami azt jelenti, hogy az állapota:,, `Cancelled` `Failed` `Aborted` vagy `Succeeded` .

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>Tesztelési példány újraütemezett vagy tesztelési tervének beosztása

Az ütemezési tesztek a felügyeleti lapról attól függnek, hogy milyen típusú munkafolyamaton fut a teszt.

##### <a name="test-pass-workflow"></a>Tesztelési menet munkafolyamata

A teszt-átadási munkafolyamatban a tesztelési példányok **átütemezése** ugyanazt a paramétert használja, mint az eredeti tesztelési példány, és az eredeti eredményt *váltja* fel, beleértve a naplókat is. Újra kell adnia a bizalmas karakterláncokat, például a jelszavakat az újraütemezett időpontokban.

1. Válassza a helyi menü **újraütemezés** elemét, hogy megnyissa a tesztelési példány újraütemezésére vonatkozó kérést.

1. Adja meg a megfelelő paramétereket.

1. Válassza a **Submit (Küldés** ) lehetőséget a teszt példányának átadásához és a meglévő példány cseréjéhez.

##### <a name="validation-workflows"></a>Ellenőrzési munkafolyamatok

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>Tesztelési példány megszakítása

Az ütemezett teszt megszakítható, ha állapota `Pending` vagy `Running` .  

1. A helyi menüből válassza a **Mégse** lehetőséget a tesztelési példány megszakítására vonatkozó kérdés megnyitásához.

1. A tesztelési példány megszakításához válassza a **Küldés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- [A szolgáltatás érvényesítésének megoldása](azure-stack-vaas-troubleshoot.md)
