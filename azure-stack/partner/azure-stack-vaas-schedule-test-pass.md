---
title: Használja az érvényesítés szolgáltatását az Azure Stack portálon az első vizsgálat ütemezése |} A Microsoft Docs
description: Az Azure Stack portal szolgáltatásként az érvényesítés használatával az első vizsgálat ütemezése.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 3fb5c3deeddb6f3ee381ca45df76feebf3405b21
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64300504"
---
# <a name="scheduling-a-test"></a>A vizsgálat ütemezése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az Azure Stack megoldás egy (VaaS) portált, az érvényesítési egy teszt ütemezése. Egy VaaS megoldás az Azure Stack megoldás egy adott hardver anyagjegyzék (AJ) jelöli. Ellenőrizze, hogy a hardver futtathatja az Azure Stack egy tesztet is ütemezhető.

A megoldás ellenőrzéséhez hozzon létre tesztelni a munkafolyamat. VaaS munkafolyamat VaaS megoldás kontextusában működik. Azt jelöli, hogy több tesztelése az Azure Stack üzembe helyezését, a hardver funkcióit, amelyek egy készlet. Adja hozzá a megoldás környezeti paramétereket, és válassza ki egy vagy több tesztek futtatásához a megoldásra.

Míg a tesztelési fázisban munkafolyamat segítségével VaaS, beleértve a teszteket az érvényesítési munkafolyamataiból által biztosított összes teszt futtatása a Tesztmenetek munkafolyamat származó eredmények nem lesznek figyelembe véve *hivatalos*. Hivatalos érvényesítési munkafolyamatokkal kapcsolatos további információkért lásd: [munkafolyamatok](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt Ez a rövid útmutató követéséhez befejeződik a következő elemek:

- [A szolgáltatási erőforrások, az ellenőrzés beállítása](azure-stack-vaas-set-up-resources.md)
- [A helyi ügynök telepítésének](azure-stack-vaas-local-agent.md) (kötelező)
- [Egy szolgáltatás fő fogalmait, érvényesítési](azure-stack-vaas-key-concepts.md) (kötelező)

## <a name="start-a-workflow"></a>A munkafolyamat indításához

![Jelentkezzen be a VaaS portálra](media/vaas_portalsignin.png)

Jelentkezzen be a portálra, válassza ki vagy megoldás létrehozása, és válassza ki a megoldást.

1. Jelentkezzen be a [VaaS portál](https://azurestackvalidation.com).
2. Írja be egy meglévő megoldás nevét, vagy válasszon **új megoldás** egy új megoldás létrehozásához. Útmutatásért lásd: [megoldás létrehozása a VaaS portálon](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-vaas-portal).
3. Válassza ki **Start** a a **teszt pass** csempére.

## <a name="specify-parameters"></a>Adja meg a paramétereket

![Helyettesítő szöveg](media/vaas_test_pass_parameters.png)

Adja meg a paramétereket, amelyek az összes teszt a munkafolyamaton belül érvényesek.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Válassza ki **tovább** tesztek ütemezése kiválasztásához.

## <a name="select-tests-to-run"></a>Válassza ki a tesztek futtatása

A teszteket, válassza ki a munkafolyamat létrehozása után lesz ütemezve.

1. Válassza ki a teszt szeretné futtatni a munkafolyamatban.

    Ha szeretné felülbírálni a következő általános paramétereket (azaz a az előző szakaszban megadott paraméterek) minden olyan tesztelési, válassza ki a a **szerkesztése** meg új értéket a következő hivatkozást.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

1. Válassza ki **tovább** , tekintse át a munkafolyamatot.

## <a name="review-and-submit"></a>Felülvizsgálat és elküldés

Munkafolyamat létrehozásának befejezéséhez.

1. Tekintse át a megjelenített információkat.

    A szolgáltatás a munkafolyamatot hoz létre a megadott információkat, és a kijelölt tesztek lesz ütemezve.

    Ha semmit nem megfelelő, akkor a **előző** gombokra kattintva nyissa meg az előző szakaszban.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>További lépések

- [Figyelheti és kezelheti a VaaS portálon tesztek](azure-stack-vaas-monitor-test.md)
