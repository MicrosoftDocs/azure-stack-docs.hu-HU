---
title: A portál használatával az Azure Stackben sablonok üzembe helyezése |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure Stack portálon sablonok üzembe helyezése.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 6e848af9049c7f105e6a31fdef8dc03308bd276a
ms.sourcegitcommit: a78c0d143eadcab65a601746b9ea24be28091ad2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212440"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Az Azure Stack portálon sablonok üzembe helyezése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A portál segítségével az Azure Stack üzembe helyezése Azure Resource Manager-sablonok.

## <a name="to-deploy-a-template"></a>A sablon üzembe helyezése

1. Jelentkezzen be a portálra, válassza ki **+ erőforrás létrehozása**, majd válassza ki **egyéni**.

   ![Létrehozás](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Válassza a **Template deployment** lehetőséget.

   ![Sablon üzembe helyezése](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Válassza ki **szerkesztési sablon**, majd illessze be a sablon JSON-kód a kód ablakba. Kattintson a **Mentés** gombra.

   ![Sablon szerkesztése](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Válassza ki **paraméterek szerkesztése**, adja meg, hogy látható-e, és válassza a paraméterek értékeit **OK**.

   ![Paraméterek szerkesztése](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Válassza ki **előfizetés**. Válassza ki az előfizetést szeretné használni, és válassza ki **OK**.

   ![Előfizetés](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Válassza ki **erőforráscsoport**. Válasszon ki egy meglévő erőforráscsoportot vagy hozzon létre egy újat, és válassza **OK**.

   ![Erőforráscsoport](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Kattintson a **Létrehozás** gombra. Egy új csempét az irányítópulton a sablon központi telepítéséhez előrehaladását követi nyomon.

   ![Sablon létrehozása](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>További lépések

Sablonok telepítésével kapcsolatos további tudnivalókért tekintse meg a következő cikket:

- [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
