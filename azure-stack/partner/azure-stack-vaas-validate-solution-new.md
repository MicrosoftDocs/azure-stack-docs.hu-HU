---
title: Megoldás ellenőrzése Azure Stack központban
description: Ismerje meg, hogyan érvényesítheti az új Azure Stack hub-megoldást a megoldás-ellenőrzési munkafolyamattal.
author: mattbriggs
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 0218e1adc239e0f609e79a75e2fd62e364739fe9
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868256"
---
# <a name="validate-a-solution-in-azure-stack-hub"></a>Megoldás ellenőrzése Azure Stack központban

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ismerje meg, hogyan ellenőrizheti a **megoldás-ellenőrzési** munkafolyamatot az új Azure stack hub-megoldások ellenőrzéséhez.

Az Azure Stack hub-megoldás olyan hardveres anyagjegyzék (AJ), amely a Microsoft és a partner között közösen egyetértett a Windows Server-embléma minősítési követelményeinek teljesítése után. A megoldást újra hitelesíteni kell, ha a hardveres anyagjegyzék módosult. Ha további kérdése van a megoldások újraellenőrzésével kapcsolatban, lépjen kapcsolatba a csapatával a következő címen: [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) .

A megoldás érvényesítéséhez futtassa kétszer a megoldás-ellenőrzési munkafolyamatot. Egyszer futtassa egyszer a *minimálisan* támogatott konfigurációhoz. Futtassa még egyszer a *maximálisan* támogatott konfigurációhoz. A Microsoft ellenőrzi a megoldást, ha mindkét konfiguráció átadja az összes tesztet.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Megoldás-ellenőrzési munkafolyamat létrehozása

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

2. Válassza az **Indítás** lehetőséget a **megoldás érvényessége** csempén.

    ![Megoldás érvényességi munkafolyamatának csempe](media/tile_validation-solution.png)

3. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

4. Válassza ki a **megoldás konfigurációját**.
    - **Minimum**: a megoldás a minimálisan támogatott számú csomóponttal van konfigurálva.
    - **Maximum**: a megoldás a maximálisan támogatott számú csomóponttal van konfigurálva.
5. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Megoldás-ellenőrzési információk](media/workflow_validation-solution_info.png)

6. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Munkafolyamat létrehozása után a környezeti paraméterek nem módosíthatók.

7. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
8. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    A rendszer átirányítja a tesztek összegzése lapra.

## <a name="run-solution-validation-tests"></a>Megoldás-ellenőrzési tesztek futtatása

A **megoldás-ellenőrzési tesztek összegzése** lapon megjelenik az érvényesítés befejezéséhez szükséges tesztek listája.

Az érvényesítési munkafolyamatokban a teszt **ütemezése** a munkafolyamat-létrehozás során megadott munkafolyamat-szintű általános paramétereket használja (lásd: a [Azure stack hub-hitelesítéshez használt munkafolyamat általános paraméterei szolgáltatásként](azure-stack-vaas-parameters.md)). Ha a teszt paramétereinek bármelyik értéke érvénytelenvé válik, a [munkafolyamat-paraméterek módosítása](azure-stack-vaas-monitor-test.md#change-workflow-parameters)elem utasításai szerint újra meg kell adnia őket.

> [!NOTE]
> Egy meglévő példányon egy érvényesítési teszt ütemezése egy új példányt hoz létre a régi példány helyett a portálon. A régi példány naplói megmaradnak, de nem érhetők el a portálról.<br>
Ha egy teszt sikeresen befejeződött, az **ütemezett** művelet le lesz tiltva.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

2. Válassza ki a következő teszteket:
    - Felhőalapú szimulációs motor
    - Lemez-azonosítási teszt

3. Válassza ki az **Ütemezés** lehetőséget a helyi menüben a teszt példány ütemezésének megadásához.

4. Tekintse át a tesztelési paramétereket, majd válassza a **Submit (Küldés** ) lehetőséget a teszt végrehajtásához.

![Megoldás-ellenőrzési teszt ütemezett ellenőrzése](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>További lépések

- [Tesztek monitorozása és kezelése az alapkonfiguráció-portálon](azure-stack-vaas-monitor-test.md)