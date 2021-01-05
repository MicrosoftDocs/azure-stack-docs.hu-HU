---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: 15afed256db592def52f06f8507bcb266d2ad9d3
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874785"
---
Az érvényesítési munkafolyamatokban a teszt **ütemezése** a munkafolyamat-létrehozás során megadott munkafolyamat-szintű általános paramétereket használja (lásd: a [Azure stack hub-hitelesítéshez használt munkafolyamat általános paraméterei szolgáltatásként](../azure-stack-vaas-parameters.md)). Ha a teszt paramétereinek bármelyik értéke érvénytelenvé válik, a [munkafolyamat-paraméterek módosítása](../azure-stack-vaas-monitor-test.md#change-workflow-parameters)elem utasításai szerint újra meg kell adnia őket.

> [!NOTE]
> Egy meglévő példányon egy érvényesítési teszt ütemezése egy új példányt hoz létre a régi példány helyett a portálon. A régi példány naplói megmaradnak, de nem érhetők el a portálról. Ha egy teszt sikeresen befejeződött, az **ütemezett** művelet le lesz tiltva.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. Válassza ki az **Ütemezés** lehetőséget a helyi menüben a teszt példány ütemezésének megadásához.

1. Tekintse át a tesztelési paramétereket, majd válassza a **Submit (Küldés** ) lehetőséget a teszt végrehajtásához.