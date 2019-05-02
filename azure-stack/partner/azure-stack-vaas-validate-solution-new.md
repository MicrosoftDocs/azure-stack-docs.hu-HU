---
title: Egy új Azure Stack megoldás ellenőrzése |} A Microsoft Docs
description: Ismerje meg, hogyan érvényesítheti egy új megoldást az Azure Stack érvényesítési szolgáltatásként.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: fb7b98a5307606f7335e76ada42d583b975730f5
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64299591"
---
# <a name="validate-a-new-azure-stack-solution"></a>Egy új Azure Stack megoldás ellenőrzése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ismerje meg, hogyan használható a **megoldás érvényesítési** hitelesíttetni az új Azure Stack megoldásokat a munkafolyamatot.

Egy Azure Stack megoldás, a hardver anyagjegyzék (AJ), amelyek közösen egyeztetett a Microsoft és a partnerek között a Windows Server embléma minősítési követelményeinek teljesítése után. A megoldás kell kell recertified, ha a hardver AJ változás történt. Segítségével újból hitelesítheti a megoldásokkal kapcsolatos további kérdéseikkel, forduljon a csapathoz a következő címen [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

A megoldás tanúsításához kétszer futtatni a megoldás szűrőérvényesítési munkafolyamat. Futtassa egyszer a *minimálisan* konfiguráció támogatott. A második alkalommal futtassa a *standardként* konfiguráció támogatott. A Microsoft igazolja a megoldás, ha mindkét adja át az összes teszt.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>A megoldás Szűrőérvényesítési munkafolyamat létrehozása

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Válassza ki **Start** a a **megoldás ellenőrzések** csempére.

    ![Megoldás ellenőrzések munkafolyamat csempe](media/tile_validation-solution.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Válassza ki a **megoldáskonfiguráció**.
    - **Minimális**: a megoldás úgy van konfigurálva, a csomópontok minimális támogatott számát.
    - **Maximális**: a megoldás úgy van konfigurálva, a csomópontok támogatott maximális számát.
6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Megoldás érvényesítésre vonatkozó információk](media/workflow_validation-solution_info.png)

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Környezeti paraméter nem lehet módosítani egy munkafolyamat létrehozása után.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    A tesztek összefoglaló oldalra irányítja.

## <a name="run-solution-validation-tests"></a>Megoldás Érvényesítési tesztek futtatásához

Az a **megoldás Érvényesítési tesztek összefoglalása** lapon látni fogja a szükséges érvényesítési teszteket listáját.

Az érvényesítés munkafolyamatokban **ütemezés** egy tesztet a munkafolyamat-szintű gyakori paramétereket, a munkafolyamat létrehozása során megadott használja (lásd: [az Azure Stack érvényesítésiszolgáltatásáltalánosmunkafolyamat-paraméterek](azure-stack-vaas-parameters.md)). Ha bármelyik teszt paraméterértékek érvénytelenné válik, meg kell resupply őket a utasításai [munkafolyamat-paraméterek módosítása](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Teszt ütemezés egy meglévő példányát keresztül fog létrehozni a régi példány helyett egy új példányt a portálon. A régi példány naplók megőrzi a rendszer, de nem érhetők el a portálon.  
Egy teszt sikeres végrehajtása után a **ütemezés** művelet le lesz tiltva.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

2. Válassza ki a következő ellenőrzés:
    - Felhőbeli szimuláció motor
    - COMPUTE SDK üzemeltetési csomag
    - Lemez azonosítása teszt
    - KeyVault Extension SDK Operational Suite
    - KeyVault SDK Operational Suite
    - Hálózati SDK üzemeltetési csomag
    - Tárolási fiók SDK üzemeltetési csomag

3. Válassza ki **ütemezés** nyissa meg a tevékenységütemezést a test-példány ehhez a helyi menüből.

4. Tekintse át a teszt paramétereket, majd **küldés** ütemezni a teszt végrehajtásához.

![Ütemezés megoldás ellenőrző teszt](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>További lépések

- [Figyelheti és kezelheti a VaaS portálon tesztek](azure-stack-vaas-monitor-test.md)