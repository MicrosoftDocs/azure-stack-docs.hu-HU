---
title: Sablonok üzembe helyezése a Visual Studióval Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe sablonokat a Visual Studióval Azure Stackban.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: c735e2a2d58e31cdd6b9e94c461a101b2b79b914
ms.sourcegitcommit: bbf3edbfc07603d2c23de44240933c07976ea550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71714687"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Sablonok üzembe helyezése Azure Stack a Visual Studióval

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Visual Studióval Azure Resource Manager sablonokat telepíthet Azure Stack.

## <a name="to-deploy-a-template"></a>Sablon üzembe helyezése

1. [Telepítse és kapcsolódjon](azure-stack-install-visual-studio.md) Azure stack a Visual Studióval.
2. Nyissa meg a Visual Studiót.
3. Válassza a **fájl**lehetőséget, majd válassza az **új**lehetőséget. Az **új projekt**területen válassza az **Azure-erőforráscsoport**lehetőséget.
4. Adja meg az új projekt **nevét** , majd kattintson **az OK gombra**.
5. Az **Azure-sablon kiválasztása lapon**válassza a legördülő lista **Azure stack** rövid útmutató elemét.
6. Válassza a **101-Create-Storage-Account**lehetőséget, majd kattintson **az OK gombra**.
7. Az új projektben bontsa ki a **sablonok** csomópontot **megoldáskezelő** az elérhető sablonok megtekintéséhez.
8. A **megoldáskezelő**területen válassza ki a projekt nevét, majd kattintson a **telepítés**elemre. Válassza az **új központi telepítés**lehetőséget.
9. Az **üzembe helyezés az erőforráscsoporthoz**területen válassza az **előfizetés** legördülő listát a Microsoft Azure stack-előfizetés kiválasztásához.
10. Az **erőforráscsoport** listából válasszon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.
11. Az **erőforráscsoport helye** listából válassza ki a helyet, majd válassza a **telepítés**lehetőséget.
12. A **Paraméterek szerkesztése**területen adja meg a paraméterek (amelyek sablon alapján változnak) értékeit, majd válassza a **Mentés**lehetőséget.

## <a name="next-steps"></a>További lépések

* [Sablonok üzembe helyezése a parancssorral](azure-stack-deploy-template-command-line.md)
* [Sablonok fejlesztése az Azure Stackhez](azure-stack-develop-templates.md)
