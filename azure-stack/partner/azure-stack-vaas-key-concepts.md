---
title: Az érvényesítés Azure Stack szolgáltatás kulcsának fogalmai | Microsoft Docs
description: A Azure Stack érvényesítésének főbb fogalmait ismerteti szolgáltatásként.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a79b6e6f34c8293febf30a9cb48ddbfae9c99816
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167329"
---
# <a name="validation-as-a-service-key-concepts"></a>Érvényesítés szolgáltatási kulcs fogalmai szerint

Ez a cikk az érvényesítési szolgáltatásként ("az"

## <a name="solutions"></a>Megoldások

Az alapszolgáltatási megoldás egy adott hardveres anyagjegyzék (AJ) Azure Stack megoldást jelöl. Az adatbázis-alapú megoldás tárolóként működik a Azure Stack megoldáson futó munkafolyamatok számára.

### <a name="create-a-solution-in-the-vaas-portal"></a>Megoldás létrehozása az Varga-portálon

1. Jelentkezzen be az [Varga portálra](https://azurestackvalidation.com).
2. A megoldások irányítópulton válassza az **új megoldás**lehetőséget.
3. Adja meg a megoldás nevét. A névadási javaslatokért lásd: az alaprendszerek [elnevezési konvenciója](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions).
4. A megoldás létrehozásához válassza a **Mentés** lehetőséget.

## <a name="workflows"></a>Munkafolyamatok

Az alapszolgáltatási folyamat egy, az egy-egy, az Olyan tesztelési csomagok készletét jelöli, amelyek egy Azure Stack üzemelő példány funkcióit gyakorolják. Létre kell hozni egy munkafolyamatot egy Azure Stack-megoldás minden központi telepítéséhez vagy szoftverfrissítési frissítéséhez.

A munkafolyamatok tesztelési forgatókönyv típusa szerint vannak kategorizálva. A nem hivatalos tesztelés során a **test pass** munkafolyamat lehetővé teszi, hogy kiválassza a teszteket az összes elérhető. A hivatalos tesztelés során az **ellenőrzési** munkafolyamatok a Microsoft által kiválasztott, konkrét tesztelési forgatókönyveket céloznak meg.

![Az Varga munkafolyamatok csempéi](media/tile_all-workflows.png)

> [!NOTE]
> A **csomag-ellenőrzési** munkafolyamat jelenleg két forgatókönyvet támogat: az [OEM-csomagok érvényesítése](azure-stack-vaas-validate-oem-package.md) és [a Microsofttól származó szoftverfrissítések ellenőrzése](azure-stack-vaas-validate-microsoft-updates.md).

A munkafolyamat-típusokkal kapcsolatos további információkért tekintse meg a [Mi az érvényesítési szolgáltatás a Azure stack?](azure-stack-vaas-overview.md)című témakört.

### <a name="getting-started-with-vaas-workflows"></a>Első lépések az Varga-munkafolyamatokkal

1. A megoldások irányítópulton hozzon létre egy új megoldást, vagy válasszon ki egy meglévőt. Ez frissíti és aktiválja a munkafolyamat-csempéket.
2. Ha új munkafolyamatot szeretne létrehozni, válassza az **Indítás** bármely csempén lehetőséget. Az egyes munkafolyamatokkal kapcsolatos információkért tekintse meg a következő cikkeket:
    - Test pass: gyors útmutató [: az ellenőrzés használata a szolgáltatás-portálon az első teszt beütemezett végrehajtásához](azure-stack-vaas-schedule-test-pass.md)
    - Megoldás ellenőrzése: [új Azure stack megoldás érvényesítése](azure-stack-vaas-validate-solution-new.md)
    - Csomag ellenőrzése (havi frissítés): [szoftverfrissítések érvényesítése a Microsofttól](azure-stack-vaas-validate-microsoft-updates.md)
    - Csomag ellenőrzése (csomag aláírása): [OEM-csomagok ellenőrzése](azure-stack-vaas-validate-oem-package.md)

3. Meglévő munkafolyamat kezeléséhez vagy figyeléséhez válassza a **kezelés** lehetőséget a munkafolyamat csempén. Válassza ki a munkafolyamat nevét, és a **Szerkesztés** gombbal tekintse meg a tulajdonságokat, vagy módosítsa a gyakori tesztelési paramétereket.

További információ a munkafolyamat-tulajdonságokról és-paraméterekről: [Azure stack érvényesítése szolgáltatásként az általános munkafolyamat-paraméterekkel](azure-stack-vaas-parameters.md).

## <a name="tests"></a>Tesztek

Az adatbázis-tesztelési Teszt olyan műveletekből áll, amelyek egy Azure Stack megoldáson futnak. A tesztek különböző felhasználási célokra vannak meghatározva, mint például a funkcionális vagy a megbízhatóság, valamint a Azure Stack egy vagy több szolgáltatásának megcélzása. Az egyes tesztek saját paramétereket határoznak meg, amelyek némelyikét a tartalmazó munkafolyamathoz tartozó általános paraméterek határozzák meg.

A tesztek kezelésével és figyelésével kapcsolatos további információkért lásd: [tesztek figyelése és kezelése az Varga-portálon](azure-stack-vaas-monitor-test.md).

A teszt paraméterekkel kapcsolatos további információkért tekintse [meg a gyakori munkafolyamat-paraméterek Azure stack érvényesítéshez szolgáltatásként](azure-stack-vaas-parameters.md)című témakört.

## <a name="agents"></a>Ügynökök

Az alaprendszer-ügynök teszteli A teszt végrehajtását. Az ügynökök két típusa futtatja az alapszintű teszteket:

- A **felhőalapú ügynök**. Ez az alapértelmezett ügynök, amely az alapszolgáltatásban érhető el. Nincs szükség beállításra, de ehhez kötött kapcsolatra van szükség a környezethez, és Azure Stack végpontokat fel kell oldani az internetről. Bizonyos tesztek nem kompatibilisek a felhőalapú ügynökkel.
- **Helyi ügynök**. Ez lehetővé teszi az érvényesítés futtatását olyan helyzetekben, amikor a környezethez kötött kapcsolat nem valósítható meg. Egyes tesztek végrehajtásához a helyi ügynöknek kell elvégeznie.

A helyi ügynökök nem kötődnek egy adott Azure Stack-vagy az Az ajánlott eljárás az, hogy egy Azure Stack környezeten kívül kell futniuk.

Helyi ügynök hozzáadásával kapcsolatos utasításokért lásd: [a helyi ügynök üzembe helyezése](azure-stack-vaas-local-agent.md).

## <a name="next-steps"></a>Következő lépések

- [Ajánlott eljárások szolgáltatásként történő érvényesítéshez](azure-stack-vaas-best-practice.md)