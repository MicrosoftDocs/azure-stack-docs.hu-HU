---
title: Sablon üzembe helyezése a portál használatával Azure Stack | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe egy sablont a Azure Stack portál használatával.
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
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: bbbbbc5548397f82752a43c7a1aaca7b62151b75
ms.sourcegitcommit: e8aa26b078a9bab09c8fafd888a96785cc7abb4d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71708995"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack"></a>Sablon üzembe helyezése a portál használatával Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A portálon Azure Resource Manager sablonokat telepíthet Azure Stack.

## <a name="to-deploy-a-template"></a>Sablon üzembe helyezése

1. Jelentkezzen be a portálra, válassza az **+ erőforrás létrehozása**lehetőséget, majd válassza az **Egyéni**lehetőséget.

   ![Erőforrás létrehozása Azure Stack portálon](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Válassza a **Template deployment** lehetőséget.

   ![Sablon üzembe helyezése Azure Stack portálon](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Válassza a **Sablon szerkesztése**lehetőséget, majd illessze be a JSON-sablon kódját a kód ablakába. Kattintson a **Mentés** gombra.

   ![Sablon szerkesztése Azure Stack portálon](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Válassza a **Paraméterek szerkesztése**lehetőséget, adja meg a megjelenített paraméterek értékeit, majd kattintson **az OK gombra**.

   ![Paraméterek szerkesztése Azure Stack portálon](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Válassza az **előfizetés**lehetőséget. Válassza ki a használni kívánt előfizetést, majd kattintson **az OK gombra**.

   ![Előfizetés kiválasztása Azure Stack portálon](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Válassza az **erőforráscsoport**lehetőséget. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat, majd kattintson **az OK gombra**.

   ![Válassza ki az erőforráscsoportot a Azure Stack portálon](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Kattintson a **Létrehozás** gombra. Az irányítópulton egy új csempén nyomon követheti a sablon üzembe helyezésének előrehaladását.

   ![Sablon létrehozása Azure Stack portálon](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>További lépések

A sablonok telepítésével kapcsolatos további tudnivalókért tekintse meg a következő cikket:

- [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
