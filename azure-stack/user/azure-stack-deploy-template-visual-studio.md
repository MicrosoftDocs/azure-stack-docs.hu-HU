---
title: Sablonok üzembe helyezése a Visual Studióval Azure Stack hub-ban
description: Megtudhatja, hogyan helyezhet üzembe sablonokat a Visual Studióval Azure Stack hub-ban.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: f8e1dea368e5c03620cfe7985b3056cc6318c1f7
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884956"
---
# <a name="deploy-templates-in-azure-stack-hub-using-visual-studio"></a>Sablonok üzembe helyezése Azure Stack hub-ban a Visual Studióval

A Visual Studióval Azure Resource Manager sablonokat telepíthet Azure Stack hubhoz.

## <a name="to-deploy-a-template"></a>Sablon üzembe helyezése

1. [Telepítse és kapcsolódjon](azure-stack-install-visual-studio.md) Azure stack hubhoz a Visual Studióval.
2. Nyissa meg a Visual Studiót.
3. Válassza a **fájl**lehetőséget, majd válassza az **új**lehetőséget. Az **új projekt**területen válassza az **Azure-erőforráscsoport**lehetőséget.
4. Adja meg az új projekt **nevét** , majd kattintson **az OK gombra**.
5. Az **Azure-sablon kiválasztása lapon válassza ki** **Azure stack hub** gyors üzembe helyezési pontját a legördülő listából.
6. Válassza a **101-Create-Storage-Account**lehetőséget, majd kattintson **az OK gombra**.
7. Az új projektben bontsa ki a **sablonok** csomópontot **megoldáskezelő** az elérhető sablonok megtekintéséhez.
8. A **megoldáskezelő**területen válassza ki a projekt nevét, majd kattintson a **telepítés**elemre. Válassza az **új központi telepítés**lehetőséget.
9. Az **üzembe helyezés az erőforráscsoporthoz**területen válassza az **előfizetés** legördülő listát az Microsoft Azure stack hub-előfizetés kiválasztásához.
10. Az **erőforráscsoport** listából válasszon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.
11. Az **erőforráscsoport helye** listából válassza ki a helyet, majd válassza a **telepítés**lehetőséget.
12. A **Paraméterek szerkesztése**területen adja meg a paraméterek (amelyek sablon alapján változnak) értékeit, majd válassza a **Mentés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

* [Sablonok üzembe helyezése a parancssorral](azure-stack-deploy-template-command-line.md)
* [Sablonok fejlesztése Azure Stack hubhoz](azure-stack-develop-templates.md)
