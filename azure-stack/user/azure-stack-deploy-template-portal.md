---
title: Sablon üzembe helyezése a portál használatával Azure Stack hub-ban | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe egy sablont a Azure Stack hub Portal használatával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: df7034f60d7e0e4999c4d658b72f47088cb70c3e
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75816360"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>Sablon üzembe helyezése a portál használatával Azure Stack hub-ban

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek és Azure Stack Development Kit*

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

1. Válassza az **előfizetés**lehetőséget. Válassza ki a használni kívánt előfizetést, majd kattintson **az OK gombra**.

   ![Előfizetés kiválasztása Azure Stack hub-portálon](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Válassza az **erőforráscsoport**lehetőséget. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat, majd kattintson **az OK gombra**.

   ![Erőforráscsoport kiválasztása Azure Stack hub-portálon](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Kattintson a **Létrehozás** gombra. Az irányítópulton egy új csempén nyomon követheti a sablon üzembe helyezésének előrehaladását.

   ![Sablon létrehozása Azure Stack hub-portálon](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>Következő lépések

A sablonok telepítésével kapcsolatos további tudnivalókért tekintse meg a következő cikket:

- [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
