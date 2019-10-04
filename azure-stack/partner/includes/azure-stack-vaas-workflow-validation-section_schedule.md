---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: c3a85e866acc615d2d33219232f7890b32df50f5
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71938802"
---
Az érvényesítési munkafolyamatokban a teszt **ütemezése** a munkafolyamat-létrehozás során megadott munkafolyamat-szintű általános paramétereket használja (lásd: a [Azure stack érvényesítéséhez használt munkafolyamat általános paraméterei szolgáltatásként](../azure-stack-vaas-parameters.md)). Ha a teszt paramétereinek bármelyik értéke érvénytelenvé válik, a [munkafolyamat-paraméterek módosítása](../azure-stack-vaas-monitor-test.md#change-workflow-parameters)elem utasításai szerint újra meg kell adnia őket.

> [!NOTE]
> Egy meglévő példányon egy érvényesítési teszt ütemezése egy új példányt hoz létre a régi példány helyett a portálon. A régi példány naplói megmaradnak, de nem érhetők el a portálról.  
Ha egy teszt sikeresen befejeződött, az **ütemezett** művelet le lesz tiltva.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. Válassza ki az **Ütemezés** lehetőséget a helyi menüben a teszt példány ütemezésének megadásához.

1. Tekintse át a tesztelési paramétereket, majd válassza a **Submit (Küldés** ) lehetőséget a teszt végrehajtásához.