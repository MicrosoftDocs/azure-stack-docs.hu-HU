---
title: Érvényesítés szolgáltatási kulcs fogalmai szerint
titleSuffix: Azure Stack Hub
description: Ismerje meg Azure Stack hub ellenőrzésének főbb fogalmait szolgáltatásként.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 9525f1f6286f5621832db5b20b3593fde6bf3546
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84112101"
---
# <a name="validation-as-a-service-key-concepts"></a>Érvényesítés szolgáltatási kulcs fogalmai szerint

Ez a cikk az érvényesítési szolgáltatásként ("az"

## <a name="solutions"></a>Megoldások

Az alapszolgáltatási megoldás egy adott hardveres anyagjegyzék (AJ) Azure Stack hub-megoldást jelöl. Az adatbázis-alapú megoldás tárolóként működik az Azure Stack hub-megoldáson futó munkafolyamatok számára.

### <a name="create-a-solution-in-the-azure-stack-hub-validation-portal"></a>Megoldás létrehozása az Azure Stack hub ellenőrzési portálján

1. Jelentkezzen be az [Azure stack hub ellenőrzési portálján](https://azurestackvalidation.com).
2. A megoldások irányítópulton válassza az **új megoldás**lehetőséget.
3. Adja meg a megoldás nevét. A névadási javaslatokért lásd: az alaprendszerek [elnevezési konvenciója](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions).
4. A megoldás létrehozásához válassza a **Mentés** lehetőséget.

## <a name="workflows"></a>Munkafolyamatok

Az alapszolgáltatási folyamat egy, az egy-egy, az Olyan tesztelési csomagok készletét jelöli, amelyek egy Azure Stack hub üzemelő példányának működését gyakorolhatják. Létre kell hozni egy munkafolyamatot egy Azure Stack hub-megoldás minden központi telepítéséhez vagy szoftverfrissítési példányához.

A munkafolyamatok tesztelési forgatókönyv típusa szerint vannak kategorizálva. A nem hivatalos tesztelés során a **test pass** munkafolyamat lehetővé teszi, hogy kiválassza a teszteket az összes elérhető. A hivatalos tesztelés során az **ellenőrzési** munkafolyamatok a Microsoft által kiválasztott, konkrét tesztelési forgatókönyveket céloznak meg.

![Az Varga munkafolyamatok csempéi](media/tile_all-workflows.png)

> [!NOTE]
> A **csomag-ellenőrzési** munkafolyamat jelenleg két forgatókönyvet támogat: az [OEM-csomagok érvényesítése](azure-stack-vaas-validate-oem-package.md) és [a Microsofttól származó szoftverfrissítések ellenőrzése](azure-stack-vaas-validate-microsoft-updates.md).

A munkafolyamat-típusokkal kapcsolatos további információkért lásd: [Mi az érvényesítési szolgáltatás az Azure stack hub](azure-stack-vaas-overview.md)-hoz?.

### <a name="getting-started-with-vaas-workflows"></a>Első lépések az Varga-munkafolyamatokkal

1. A megoldások irányítópulton hozzon létre egy új megoldást, vagy válasszon ki egy meglévőt. Ez frissíti és aktiválja a munkafolyamat-csempéket.
2. Új munkafolyamat létrehozásához válassza a **Start** lehetőséget bármely csempén. Az egyes munkafolyamatokkal kapcsolatos információkért tekintse meg a következő cikkeket:
    - Test pass: gyors útmutató [: az első teszt elvégzéséhez használja a Azure stack hub ellenőrzési portált](azure-stack-vaas-schedule-test-pass.md)
    - Megoldás ellenőrzése: [új Azure stack hub-megoldás érvényesítése](azure-stack-vaas-validate-solution-new.md)
    - Csomag ellenőrzése (havi frissítés): [szoftverfrissítések érvényesítése a Microsofttól](azure-stack-vaas-validate-microsoft-updates.md)
    - Csomag ellenőrzése (csomag aláírása): [OEM-csomagok ellenőrzése](azure-stack-vaas-validate-oem-package.md)

3. Meglévő munkafolyamat kezeléséhez vagy figyeléséhez válassza a **kezelés** lehetőséget a munkafolyamat csempén. Válassza ki a munkafolyamat nevét, és a **Szerkesztés** gombbal tekintse meg a tulajdonságokat, vagy módosítsa a gyakori tesztelési paramétereket.

További információ a munkafolyamat-tulajdonságokról és-paraméterekről: [Azure stack hub ellenőrzésének általános munkafolyamat-paraméterei szolgáltatásként](azure-stack-vaas-parameters.md).

## <a name="tests"></a>Tesztek

Az adatbázis-ellenőrzés egy Azure Stack hub-megoldáson futó műveletekből áll. A tesztek különböző felhasználási célokra vannak meghatározva (például a funkcionális vagy a megbízhatósági funkciókat), és egy vagy több Azure Stack hub-szolgáltatást céloznak meg. Az egyes tesztek saját paramétereket határoznak meg, amelyek némelyikét a tartalmazó munkafolyamathoz tartozó általános paraméterek határozzák meg.

További információ a tesztek kezeléséről és figyeléséről: [tesztek figyelése és kezelése az Azure stack hub ellenőrzési portálján](azure-stack-vaas-monitor-test.md).

A tesztelési paraméterekkel kapcsolatos további információkért lásd: [Azure stack hub-érvényesítési szolgáltatáshoz tartozó általános munkafolyamat-paraméterek](azure-stack-vaas-parameters.md).

## <a name="agents"></a>Ügynökök

Az alaprendszer-ügynök teszteli A teszt végrehajtását. Az ügynökök két típusa futtatja az alapszintű teszteket:

- A **felhőalapú ügynök**. Ez az alapértelmezett ügynök, amely az alapszolgáltatásban érhető el. Nincs szükség beállításra, de ehhez a környezethez kötött kapcsolat szükséges, és Azure Stack hub-végpontoknak az internetről kell feloldaniuk. Bizonyos tesztek nem kompatibilisek a felhőalapú ügynökkel.
- **Helyi ügynök**. Ez lehetővé teszi az érvényesítés futtatását olyan helyzetekben, amikor a környezethez kötött kapcsolat nem valósítható meg. Egyes tesztek végrehajtásához a helyi ügynöknek kell elvégeznie.

A helyi ügynökök nem kötődnek egy adott Azure Stack hub-vagy az alaprendszerbeli megoldáshoz. Az ajánlott eljárás a Azure Stack hub-környezeten kívül kell, hogy fusson.

Helyi ügynök hozzáadásával kapcsolatos utasításokért lásd: [a helyi ügynök üzembe helyezése](azure-stack-vaas-local-agent.md).

## <a name="next-steps"></a>Következő lépések

- [Ajánlott eljárások szolgáltatásként történő érvényesítéshez](azure-stack-vaas-best-practice.md)