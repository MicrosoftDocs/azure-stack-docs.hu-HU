---
title: Sablonok üzembe helyezése a Visual Studióval Azure Stack hub-ban
description: Megtudhatja, hogyan helyezhet üzembe sablonokat a Visual Studióval Azure Stack hub-ban.
author: mattbriggs
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 894e04e8e7b54d9e87d51af93c98f9abfd074aee
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525353"
---
# <a name="deploy-templates-in-azure-stack-hub-using-visual-studio"></a>Sablonok üzembe helyezése Azure Stack hub-ban a Visual Studióval

A Visual Studióval Azure Resource Manager sablonokat telepíthet Azure Stack hubhoz.

## <a name="to-deploy-a-template"></a>Sablon üzembe helyezése

1. [Telepítse és kapcsolódjon](azure-stack-install-visual-studio.md) Azure stack hubhoz a Visual Studióval.
2. Nyissa meg a Visual Studiót.
3. Válassza a **fájl** lehetőséget, majd válassza az **új** lehetőséget. Az **új projekt** területen válassza az **Azure-erőforráscsoport** lehetőséget.
4. Adja meg az új projekt **nevét** , majd kattintson **az OK gombra**.
5. Az **Azure-sablon kiválasztása lapon válassza ki** **Azure stack hub** gyors üzembe helyezési pontját a legördülő listából.
6. Válassza a **101-Create-Storage-Account** lehetőséget, majd kattintson **az OK gombra**.
7. Az új projektben bontsa ki a **sablonok** csomópontot **megoldáskezelő** az elérhető sablonok megtekintéséhez.
8. A **megoldáskezelő** területen válassza ki a projekt nevét, majd kattintson a **telepítés** elemre. Válassza az **új központi telepítés** lehetőséget.
9. Az **üzembe helyezés az erőforráscsoporthoz** területen válassza az **előfizetés** legördülő listát az Microsoft Azure stack hub-előfizetés kiválasztásához.
10. Az **erőforráscsoport** listából válasszon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.
11. Az **erőforráscsoport helye** listából válassza ki a helyet, majd válassza a **telepítés** lehetőséget.
12. A **Paraméterek szerkesztése** területen adja meg a paraméterek (amelyek sablon alapján változnak) értékeit, majd válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>További lépések

* [Sablonok üzembe helyezése a parancssorral](azure-stack-deploy-template-command-line.md)
* [Sablonok fejlesztése Azure Stack hubhoz](azure-stack-develop-templates.md)
