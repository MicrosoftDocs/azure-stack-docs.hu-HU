---
title: Egy szolgáltatás fő fogalmait, az Azure Stack érvényesítési |} A Microsoft Docs
description: Azokat a fogalmakat, az Azure Stack érvényesítési szolgáltatásként.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 3830dc15cebe24a8fb910b641b7b986437aeec52
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64298502"
---
# <a name="validation-as-a-service-key-concepts"></a>Mint szolgáltatásra kulcs érvényesítése

Ez a cikk azokat a fogalmakat érvényesítési szolgáltatásként (VaaS).

## <a name="solutions"></a>Megoldások

Egy VaaS megoldás az Azure Stack megoldás egy adott hardver anyagjegyzék (AJ) jelöli. A VaaS megoldás a munkafolyamatok futtatásához az Azure Stack megoldás tárolójaként szolgál.

### <a name="create-a-solution-in-the-vaas-portal"></a>Megoldás létrehozása a VaaS portálon

1. Jelentkezzen be a [VaaS portál](https://azurestackvalidation.com).
2. A megoldás irányítópultján válassza ki a **új megoldás**.
3. Adja meg a megoldás nevét. Javaslatok és elnevezéséről, lásd: [elnevezési szabályainak VaaS megoldások](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions).
4. Válassza ki **mentése** a megoldás létrehozásához.

## <a name="workflows"></a>Munkafolyamatok

VaaS munkafolyamat VaaS megoldás kontextusában működik. Azt jelöli, hogy több teszt a funkció az Azure Stack központi telepítése, amelyek egy készlet. A munkafolyamat minden üzembe helyezés vagy a szoftver frissítése az Azure Stack megoldás kell létrehozni.

A munkafolyamatok kategorizálta a forgatókönyv típusát a teszteléshez. A munkaköröket külsős informatikusok tesztelése a **Tesztmenetek** munkafolyamat lehetővé teszi, hogy tesztek összes rendelkezésre álló VaaS biztosíték közül választhat. A hivatalos vizsgálata a **érvényesítési** munkafolyamatok célként meghatározott, a Microsoft által kiválasztott Tesztelési forgatókönyvek.

![VaaS munkafolyamat csempék](media/tile_all-workflows.png)

> [!NOTE]
> A **csomag** munkafolyamat jelenleg két forgatókönyvet támogatja: [OEM-csomagok érvényesítése](azure-stack-vaas-validate-oem-package.md) és [ellenőrzése a Microsoft szoftverfrissítések](azure-stack-vaas-validate-microsoft-updates.md).

További információ a munkafolyamat-típusok: [szolgáltatását az Azure Stack érvényesítési mi?](azure-stack-vaas-overview.md).

### <a name="getting-started-with-vaas-workflows"></a>VaaS munkafolyamatok első lépései

1. A megoldás irányítópultján egy új megoldás létrehozása, vagy válasszon ki egy meglévőt. Ez frissíti, és aktiválja a munkafolyamat csempék.
2. Új munkafolyamat létrehozásához válassza a **Start** bármely csempén. Minden egyes munkafolyamat vonatkozó információkért lásd: a következő cikkeket:
    - Tesztmenetek: [Rövid útmutató: Az érvényesítés használja egy portált, az első vizsgálat ütemezése](azure-stack-vaas-schedule-test-pass.md)
    - Megoldás-érvényesítés: [Új Azure Stack-megoldás ellenőrzése](azure-stack-vaas-validate-solution-new.md)
    - Csomag érvényességének ellenőrzése (havi frissítésében): [A Microsoft szoftverfrissítések ellenőrzése](azure-stack-vaas-validate-microsoft-updates.md)
    - Csomag érvényesítése (a csomag aláírása): [OEM-csomagok érvényesítése](azure-stack-vaas-validate-oem-package.md)

3. Felügyelheti és figyelheti a meglévő munkafolyamatok, válassza ki a **kezelés** a munkafolyamat csempére. Válassza ki a nevét, a munkafolyamat és a **szerkesztése** tulajdonságainak megtekintése és módosítása a közös tesztparaméterekre gombra.

Munkafolyamatok tulajdonságait és a paraméterekkel kapcsolatos további információkért lásd: [munkafolyamat általános paramétereit az Azure Stack érvényesítési szolgáltatásként](azure-stack-vaas-parameters.md).

## <a name="tests"></a>Tesztek

Egy teszt VaaS a számos műveleti, amely egy Azure Stack-megoldás futtatásához áll. Tesztek rendelkezik különböző célokra használható, kategória által azonosított, például működési vagy megbízhatóságára, és egy vagy több Azure Stack-szolgáltatások. Minden teszt paramétereket, amelyek némelyike határozza meg a tartalmazó munkafolyamat általános paramétereket saját csoportját határozza meg.

Kezelésével és figyelési teszteket kapcsolatos további információkért lásd: [figyelése és kezelése a VaaS portálon tesztek](azure-stack-vaas-monitor-test.md).

Teszt paraméterekkel kapcsolatos további információkért lásd: [munkafolyamat általános paramétereit az Azure Stack érvényesítési szolgáltatásként](azure-stack-vaas-parameters.md).

## <a name="agents"></a>Ügynökök

Egy VaaS ügynök meghajtók szoftvertesztek végrehajtása. Ügynökök VaaS vizsgálat futtatása két típusa:

- A **felhőalapú ügynök**. Ez az az alapértelmezett ügynök VaaS érhető el. Semmilyen beállítást nem szükséges, de ehhez a környezethez kötött a kapcsolat, és Azure Stack-végpontokat internetes feloldhatónak kell lennie. Néhány teszt nem kompatibilisek a felhő-ügynök.
- A **helyi ügynök**. Ez lehetővé teszi az érvényesítési futtatásához forgatókönyvekben, ahol a környezethez kötött a kapcsolat nem található. Néhány teszt végrehajtása a helyi ügynökön keresztül van szükség.

A helyi ügynökök nem adott Azure Stack vagy VaaS megoldással vannak társítva. Az ajánlott eljárás, futtassa az Azure Stack-környezeten kívül.

Egy helyi ügynök hozzáadására vonatkozó utasításokért lásd: [üzembe helyezése a helyi ügynök](azure-stack-vaas-local-agent.md).

## <a name="next-steps"></a>További lépések

- [Ajánlott eljárások a szolgáltatás érvényesítése](azure-stack-vaas-best-practice.md)