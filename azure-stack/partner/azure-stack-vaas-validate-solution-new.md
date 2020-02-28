---
title: Új Azure Stack hub-megoldás érvényesítése
description: Ismerje meg, hogyan érvényesítheti az új Azure Stack hub-megoldást szolgáltatásként történő érvényesítéssel.
author: mattbriggs
ms.topic: quickstart
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6a743a27b836ad4e82d75f8a4c6b156340c354ad
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704556"
---
# <a name="validate-a-new-azure-stack-hub-solution"></a>Új Azure Stack hub-megoldás érvényesítése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Megtudhatja, hogyan használhatja a **megoldás-ellenőrzési** munkafolyamatot az új Azure stack hub-megoldások hitelesítéséhez.

Az Azure Stack hub-megoldás olyan hardveres anyagjegyzék (AJ), amely a Microsoft és a partner között közösen egyetértett a Windows Server-embléma minősítési követelményeinek teljesítése után. A megoldást újra hitelesíteni kell, ha a hardveres anyagjegyzék módosult. Ha további kérdése van a megoldások újbóli hitelesítésével kapcsolatban, vegye fel a kapcsolatot a csapattal [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com)címen.

A megoldás hitelesítéséhez futtassa kétszer a megoldás-ellenőrzési munkafolyamatot. Egyszer futtassa egyszer a *minimálisan* támogatott konfigurációhoz. Futtassa még egyszer a *maximálisan* támogatott konfigurációhoz. A Microsoft tanúsítja a megoldást, ha mindkét konfiguráció átadja az összes tesztet.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Megoldás-ellenőrzési munkafolyamat létrehozása

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Válassza az **Indítás** lehetőséget a **megoldás érvényessége** csempén.

    ![Megoldás érvényességi munkafolyamatának csempe](media/tile_validation-solution.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Válassza ki a **megoldás konfigurációját**.
    - **Minimum**: a megoldás a minimálisan támogatott számú csomóponttal van konfigurálva.
    - **Maximum**: a megoldás a maximálisan támogatott számú csomóponttal van konfigurálva.
6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Megoldás-ellenőrzési információk](media/workflow_validation-solution_info.png)

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Munkafolyamat létrehozása után a környezeti paraméterek nem módosíthatók.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    A rendszer átirányítja a tesztek összegzése lapra.

## <a name="run-solution-validation-tests"></a>Megoldás-ellenőrzési tesztek futtatása

A **megoldás-ellenőrzési tesztek összegzése** lapon megjelenik az érvényesítés végrehajtásához szükséges tesztek listája.

Az érvényesítési munkafolyamatokban a teszt **ütemezése** a munkafolyamat-létrehozás során megadott munkafolyamat-szintű általános paramétereket használja (lásd: a [Azure stack hub-hitelesítéshez használt munkafolyamat általános paraméterei szolgáltatásként](azure-stack-vaas-parameters.md)). Ha a teszt paramétereinek bármelyik értéke érvénytelenvé válik, a [munkafolyamat-paraméterek módosítása](azure-stack-vaas-monitor-test.md#change-workflow-parameters)elem utasításai szerint újra meg kell adnia őket.

> [!NOTE]
> Egy meglévő példányon egy érvényesítési teszt ütemezése egy új példányt hoz létre a régi példány helyett a portálon. A régi példány naplói megmaradnak, de nem érhetők el a portálról.  
Ha egy teszt sikeresen befejeződött, az **ütemezett** művelet le lesz tiltva.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

2. Válassza ki a következő teszteket:
    - Felhőalapú szimulációs motor
    - Számítási SDK operatív csomagja
    - Lemez-azonosítási teszt
    - Kulcstartó bővítmény SDK operatív csomagja
    - Kulcstartó SDK operatív csomag
    - Hálózati SDK operatív csomag
    - Storage-fiók SDK operatív csomagja

3. Válassza ki az **Ütemezés** lehetőséget a helyi menüben a teszt példány ütemezésének megadásához.

4. Tekintse át a tesztelési paramétereket, majd válassza a **Submit (Küldés** ) lehetőséget a teszt végrehajtásához.

![Megoldás-ellenőrzési teszt ütemezett ellenőrzése](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Következő lépések

- [Tesztek monitorozása és kezelése az alapkonfiguráció-portálon](azure-stack-vaas-monitor-test.md)