---
title: Az érvényesítési szolgáltatásként Azure Stack portálon az első teszt ütemtervének megadásához | Microsoft Docs
description: Az érvényesítési szolgáltatásként Azure Stack portálon az első teszt beütemezhet.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 8843b6791876b4e0362d7aee3aa738453a8c3b88
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955930"
---
# <a name="scheduling-a-test"></a>Teszt ütemezése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Egy tesztet ütemezhet a Azure Stack-megoldáshoz tartozó érvényesítési szolgáltatásként (). Az alapszolgáltatási megoldás egy adott hardveres anyagjegyzék (AJ) Azure Stack megoldást jelöl. Beütemezhet egy tesztet annak ellenőrzéséhez, hogy a hardver futtatható-e Azure Stack.

A megoldás ellenőrzéséhez hozzon létre egy teszt munkafolyamatot. Az alapszolgáltatási folyamat egy, az egy-egy, az Olyan tesztelési csomagok készletét jelöli, amelyek egy Azure Stack központi telepítés funkcióit gyakorolják a hardveren. Adja hozzá a megoldás környezeti paramétereit, és válasszon ki egy vagy több, a megoldáson futtatandó tesztet.

Míg a test pass munkafolyamat használható az összes olyan teszt futtatására, amelyet az adatbázis-ellenőrzés is tartalmaz, beleértve az ellenőrzési munkafolyamatokból származó teszteket, a test pass-munkafolyamat eredményei nem tekintendők *hivatalosnak*. További információ a hivatalos ellenőrzési munkafolyamatokról: [munkafolyamatok](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzése előtt végezze el a következő elemeket:

- [Az érvényesítés beállítása szolgáltatási erőforrásként](azure-stack-vaas-set-up-resources.md)
- [A helyi ügynök üzembe helyezése](azure-stack-vaas-local-agent.md) (kötelező)
- [Érvényesítés szolgáltatási kulcs fogalmai](azure-stack-vaas-key-concepts.md) (kötelező)

## <a name="start-a-workflow"></a>Munkafolyamat elindítása

![Bejelentkezés az Varga portálra](media/vaas_portalsignin.png)

Jelentkezzen be a portálra, válasszon ki vagy hozza létre a megoldást, majd válassza ki a megoldást.

1. Jelentkezzen be az [Varga portálra](https://azurestackvalidation.com).
2. Írja be egy meglévő megoldás nevét, vagy válassza az **új** megoldás lehetőséget új megoldás létrehozásához. Útmutatásért lásd: [megoldás létrehozása az alaprendszer-portálon](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-vaas-portal).
3. Válassza a **Start** lehetőséget a **teszt** átadása csempén.

## <a name="specify-parameters"></a>Paraméterek megadása

![Helyettesítő szöveg](media/vaas_test_pass_parameters.png)

Adja meg azokat a paramétereket, amelyek a munkafolyamaton belüli összes tesztre érvényesek.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Válassza a **tovább** lehetőséget az ütemezni kívánt tesztek kiválasztásához.

## <a name="select-tests-to-run"></a>Válassza ki a futtatandó teszteket

A kiválasztott tesztek a munkafolyamat létrehozása után lesznek ütemezve.

1. Válassza ki a munkafolyamatban futtatni kívánt teszt (eke) t.

    Ha szeretné felülbírálni az egyes tesztek általános paramétereit (azaz az előző szakaszban megadott paramétereket), válassza az új értékek megadása melletti **Szerkesztés** hivatkozást.

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
