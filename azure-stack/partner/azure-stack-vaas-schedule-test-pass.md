---
title: Teszt beütemezett Azure Stack hub ellenőrzési portálján
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan ütemezhet egy tesztet az Azure Stack hub ellenőrzési portálján.
author: mattbriggs
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: ccaae2e6d7625687f7739cba4fa77eda3ab91520
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625458"
---
# <a name="schedule-a-test-in-azure-stack-validation-portal"></a>Teszt beütemezett Azure Stack ellenőrzési portálon

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Tesztet ütemezhet a Microsoft Azure Stack ellenőrzési portálon az Azure Stack hub-megoldáshoz. Az érvényesítési szolgáltatásként szolgáló megoldás egy adott hardveres anyagjegyzék (AJ) Azure Stack hub-megoldást jelöl. Beütemezhet egy tesztet annak ellenőrzéséhez, hogy a hardver képes-e Azure Stack hub futtatására.

A megoldás ellenőrzéséhez hozzon létre egy teszt munkafolyamatot. Az alapszolgáltatási folyamat egy, az egy-egy, az Olyan tesztelési csomagok készletét jelöli, amelyek egy Azure Stack hub üzemelő példányának funkcióit gyakorolják a hardveren. Adja hozzá a megoldás környezeti paramétereit, és válasszon ki egy vagy több, a megoldáson futtatandó tesztet.

Míg a test pass munkafolyamat használható az összes olyan teszt futtatására, amelyet az adatbázis-ellenőrzés is tartalmaz, beleértve az ellenőrzési munkafolyamatokból származó teszteket, a test pass-munkafolyamat eredményei nem minősülnek *hivatalosnak*. További információ a hivatalos ellenőrzési munkafolyamatokról: [munkafolyamatok](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzése előtt végezze el a következő feladatokat:

- [Az érvényesítés beállítása szolgáltatási erőforrásként](azure-stack-vaas-set-up-resources.md).
- [Telepítse a helyi ügynököt](azure-stack-vaas-local-agent.md) (kötelező).
- [Érvényesítés a szolgáltatás alapvető fogalmai](azure-stack-vaas-key-concepts.md) (kötelező).

## <a name="start-a-workflow"></a>Munkafolyamat elindítása

![Bejelentkezés az Varga portálra](media/vaas_portalsignin.png)

Jelentkezzen be a portálra, válasszon ki vagy hozzon létre egy megoldást, majd válassza ki a megoldást.

1. Jelentkezzen be az [Varga portálra](https://azurestackvalidation.com).
2. Írja be egy meglévő megoldás nevét, vagy válassza az **új** megoldás lehetőséget új megoldás létrehozásához. Útmutatásért lásd: [megoldás létrehozása az alaprendszer-portálon](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-azure-stack-hub-validation-portal).
3. Válassza a **Start** lehetőséget a **teszt** átadása csempén.

## <a name="specify-parameters"></a>Paraméterek megadása

![Paraméterek megadása az Varga portálon](media/vaas_test_pass_parameters.png)

Adja meg azokat a paramétereket, amelyek a munkafolyamaton belüli összes tesztre érvényesek.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Válassza a **tovább** lehetőséget az ütemezni kívánt tesztek kiválasztásához.

## <a name="select-tests-to-run"></a>Válassza ki a futtatandó teszteket

A kiválasztott tesztek a munkafolyamat létrehozása után lesznek ütemezve.

1. Válassza ki a munkafolyamatban futtatni kívánt teszt (eke) t.

    Ha szeretné felülbírálni az egyes tesztek általános paramétereit (az előző szakaszban megadott paramétereket), válassza az új értékek megadása melletti **Szerkesztés** hivatkozást.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

1. A munkafolyamat áttekintéséhez kattintson a **tovább** gombra.

## <a name="review-and-submit"></a>Áttekintés és küldés

Munkafolyamat-létrehozás befejezése

1. Tekintse át a megjelenített információkat.

    A szolgáltatás létrehozza a munkafolyamatot a megadott adatokkal, és a kiválasztott tesztek ütemezése megtörténik.

    Ha valami helytelennek tűnik, az **előző** gombokkal lépjen egy korábbi szakaszhoz.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>Következő lépések

- [Tesztek monitorozása és kezelése az alapkonfiguráció-portálon](azure-stack-vaas-monitor-test.md)
