---
title: Sablon üzembe helyezése a portál használatával Azure Stack hub-ban
description: Megtudhatja, hogyan helyezhet üzembe egy sablont a Azure Stack hub Portal használatával.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 8425b4f8492e57502ca93b0be802b63601f6ba34
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77704318"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>Sablon üzembe helyezése a portál használatával Azure Stack hub-ban

A portál használatával Azure Resource Manager sablonokat telepíthet Azure Stack hubhoz.

## <a name="to-deploy-a-template"></a>Sablon üzembe helyezése

1. Jelentkezzen be a portálra, válassza az **+ erőforrás létrehozása**lehetőséget, majd válassza az **Egyéni**lehetőséget.

   ![Erőforrás létrehozása Azure Stack hub-portálon](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Válassza a **Template deployment** lehetőséget.

   ![Sablon üzembe helyezése Azure Stack hub portálon](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Válassza a **Sablon szerkesztése**lehetőséget, majd illessze be a JSON-sablon kódját a kód ablakába. Kattintson a **Mentés** gombra.

   ![Sablon szerkesztése Azure Stack hub-portálon](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Válassza a **Paraméterek szerkesztése**lehetőséget, adja meg a megjelenített paraméterek értékeit, majd kattintson **az OK gombra**.

   ![Paraméterek szerkesztése Azure Stack hub-portálon](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Válasszon ki egy **Előfizetést**. Válassza ki a használni kívánt előfizetést, majd kattintson **az OK gombra**.

   ![Előfizetés kiválasztása Azure Stack hub-portálon](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Válassza az **erőforráscsoport**lehetőséget. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat, majd kattintson **az OK gombra**.

   ![Erőforráscsoport kiválasztása Azure Stack hub-portálon](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Kattintson a **Létrehozás** gombra. Az irányítópulton egy új csempén nyomon követheti a sablon üzembe helyezésének előrehaladását.

   ![Sablon létrehozása Azure Stack hub-portálon](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>További lépések

A sablonok telepítésével kapcsolatos további tudnivalókért tekintse meg a következő cikket:

- [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
