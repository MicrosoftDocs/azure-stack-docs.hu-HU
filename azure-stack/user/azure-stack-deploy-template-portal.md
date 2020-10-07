---
title: Sablon üzembe helyezése a portál használatával Azure Stack hub-ban
description: Megtudhatja, hogyan helyezhet üzembe egy sablont a Azure Stack hub Portal használatával.
author: mattbriggs
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/05/2020
ms.openlocfilehash: b428c99f7c8b847e575e40af2915a4da60f2312f
ms.sourcegitcommit: 868887e4b13b1572f15004a9db2c334e60d8add2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778027"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>Sablon üzembe helyezése a portál használatával Azure Stack hub-ban

Az Azure Stack hub felhasználói portál használatával Azure Resource Manager sablonokat telepíthet Azure Stack hubhoz.

## <a name="to-deploy-a-template"></a>Sablon üzembe helyezése

1. Jelentkezzen be az Azure stack hub felhasználói portálra, válassza az **+ erőforrás létrehozása**  >  **Egyéni**  >  **template Deployment**lehetőséget.

   ![Erőforrás létrehozása Azure Stack hub-portálon](media/azure-stack-deploy-template-portal/template-deploy1.png)

2. A Type (típus) lehetőség kiválasztásával **megkezdheti a szűrést** a GitHub-gyorsindítási sablon kiválasztásához, vagy **a szerkesztőben válassza a saját sablon létrehozása**lehetőséget.

   ![Sablon üzembe helyezése Azure Stack hub portálon](media/azure-stack-deploy-template-portal/template-deploy2.png)

    [**AzureStack –**](https://github.com/Azure/AzureStack-QuickStart-Templates) rövid útmutató – a sablonokat a Azure stack hub Közösség tagja hozza létre, és nem a Microsoft. A sablon tulajdonosa, nem a Microsoft, hanem a licencszerződés keretében licencet kapott Önnek. A Microsoft nem felelős ezekre a sablonokra, és nem biztonsági, kompatibilitási vagy teljesítménybeli képernyő. A közösségi sablonok bármely Microsoft-támogatási program vagy szolgáltatás esetében nem támogatottak, és a rendelkezésére álló feltételek semmilyen garanciát nem *jelentenek* .

3. Ha **a szerkesztőben a saját sablon létrehozása**lehetőséget választotta, illessze be a JSON-sablon kódját a kód ablakába.

   ![Sablon szerkesztése Azure Stack hub-portálon](media/azure-stack-deploy-template-portal/template-deploy3.png)

    - Válassza ki a gyors üzembe helyezési **sablont** a közösségi sablon betöltéséhez a szerkesztőben.

    - Válassza a **fájl betöltése** lehetőséget Azure Resource Manager sablon betöltéséhez a helyi gépről a szerkesztőbe.

    - Válassza a **Letöltés** lehetőséget, hogy a Azure Resource Manager sablont a helyi gépre mentse.

    Ha végzett a sablon módosításával, válassza a **Mentés**lehetőséget.

4. Válassza az **előfizetés**lehetőséget. Válassza ki a használni kívánt előfizetést. Válassza az **erőforráscsoport**lehetőséget. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat, majd kattintson **az OK gombra**. Majd válassza a **felülvizsgálat + létrehozás**elemet.

   ![Paraméterek szerkesztése Azure Stack hub-portálon](media/azure-stack-deploy-template-portal/template-deploy4.png)

5. Kattintson a **Létrehozás** gombra.

   ![Előfizetés kiválasztása Azure Stack hub-portálon](media/azure-stack-deploy-template-portal/template-deploy5.png)

6. Az irányítópulton egy új csempén nyomon követheti a sablon üzembe helyezésének előrehaladását.

   ![Erőforráscsoport kiválasztása Azure Stack hub-portálon](media/azure-stack-deploy-template-portal/template-deploy6.png)

   Az alkalmazás összes erőforrását egyetlen, koordinált műveletben használhatja Azure Resource Manager sablonokkal. Az erőforráscsoportok erőforrásainak módosítására is lehetősége van a sablonok újbóli üzembe helyezésére. További információ a sablonok Azure Stack hub használatával történő használatáról: [Azure Resource Manager-sablonok használata az Azure stack hub-ban](azure-stack-arm-templates.md).

## <a name="next-steps"></a>Következő lépések

A sablonok telepítésével kapcsolatos további tudnivalókért tekintse meg a következő cikket:

- [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
