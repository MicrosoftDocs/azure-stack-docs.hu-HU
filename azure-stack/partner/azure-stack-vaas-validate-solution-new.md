---
title: Új Azure Stack megoldás ellenőrzése | Microsoft Docs
description: Ismerje meg, hogyan érvényesítheti az új Azure Stack megoldást szolgáltatásként történő érvényesítéssel.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a29b003dca06ed1b886229f031e90c50fbc8dccb
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955792"
---
# <a name="validate-a-new-azure-stack-solution"></a>Új Azure Stack megoldás érvényesítése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Megtudhatja, hogyan használhatja a **megoldás-ellenőrzési** munkafolyamatot az új Azure stack megoldások hitelesítéséhez.

Az Azure Stack-megoldás olyan hardveres anyagjegyzék (AJ), amely a Microsoft és a partner között közösen megállapodott a Windows Server embléma minősítési követelményeinek teljesítése után. A megoldást újra hitelesíteni kell, ha a hardveres anyagjegyzék módosult. Ha további kérdése van a megoldások újbóli hitelesítésével kapcsolatban, vegye fel a kapcsolatot a csapattal [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com)címen.

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

Az érvényesítési munkafolyamatokban a teszt **ütemezése** a munkafolyamat-létrehozás során megadott munkafolyamat-szintű általános paramétereket használja (lásd: a [Azure stack érvényesítéséhez használt munkafolyamat általános paraméterei szolgáltatásként](azure-stack-vaas-parameters.md)). Ha a teszt paramétereinek bármelyik értéke érvénytelenvé válik, a [munkafolyamat-paraméterek módosítása](azure-stack-vaas-monitor-test.md#change-workflow-parameters)elem utasításai szerint újra meg kell adnia őket.

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