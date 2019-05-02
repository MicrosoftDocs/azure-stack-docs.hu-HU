---
title: Ebben az oktatóanyagban megismerheti, hogyan fizethet elő az Azure Stack-ajánlat |} A Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozzon létre egy új előfizetést az Azure Stack-szolgáltatások és a egy tesztcélú virtuális gép létrehozása az ajánlat teszteléséhez.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 83ece8d508e9fe6fa37d6711d5fb1c43fc2fd7b5
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64302814"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Oktatóanyag: létrehozása és tesztelése egy előfizetést

Ez az oktatóanyag bemutatja, hogyan hozzon létre egy ajánlatot tartalmazó előfizetést, és tesztelje azt. A teszthez jelentkezzen be az Azure Stack felhasználói portál a felhő-rendszergazdák előfizetnek az ajánlatra, és hozzon létre egy virtuális gépet.

> [!TIP]
> További speciális értékelési élményt, is [hozzon létre egy előfizetést, egy adott felhasználó](../operator/azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) majd jelentkezzen be a felhasználó a felhasználói portálon.

Ez az oktatóanyag bemutatja, hogyan fizethet elő az Azure Stack-ajánlat.

Amiről tanulni fog:

> [!div class="checklist"]
> * Előfizetés egy ajánlatra 
> * Az ajánlat tesztelése

## <a name="subscribe-to-an-offer"></a>Előfizetés egy ajánlatra

Az előfizetés egy ajánlatra felhasználóként, bejelentkezik az Azure Stack felhasználói portálra a szolgáltatásokat, amelyek az Azure Stack operátorait szerint felajánlották felderítéséhez.

1. Jelentkezzen be a Portalon, és válassza a felhasználó **előfizetés beszerzése**.

   ![Előfizetés beszerzése](media/azure-stack-subscribe-services/get-subscription.png)

2. A **Megjelenítendő név** mezőben adja meg az előfizetés nevét. Válassza ki **ajánlat** az elérhető ajánlatok a közül a **válasszon ajánlatot** szakaszban. Ezután kattintson a **Létrehozás** elemre.

   ![Ajánlat létrehozása](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Most frissítenie kell a felhasználói portálra, az előfizetés használatának megkezdéséhez.

3. A létrehozott előfizetés megtekintéséhez jelölje ki **minden szolgáltatás**. Ezt követően a **általános** kategória kiválasztása **előfizetések**, majd válassza ki az új előfizetés. Miután előfizetett egy ajánlatra, frissítse a portált tekintse meg, ha új lett az új előfizetés része. Ebben a példában **virtuális gépek** hozzá lett adva.

   ![Előfizetési nézet](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>Az ajánlat tesztelése

Miközben be van jelentkezve a felhasználói portálra, az ajánlat letesztelheti az új előfizetési lehetőségek használatával virtuális gép üzembe helyezésének.

> [!NOTE]
> Ez a vizsgálat szükséges, hogy egy Windows Server 2016 Datacenter virtuális Gépet az Azure Stack piactéren először bővült.

1. Jelentkezzen be a felhasználói portálra.

2. Válassza ki a felhasználói portálon **virtuális gépek**, majd **Hozzáadás**, majd **Windows Server 2016 Datacenter**, és kattintson a **létrehozás**.

3. Az a **alapjai** területén írja be egy **neve**, **felhasználónév**, és **jelszó**, válassza ki egy **előfizetés**, Hozzon létre egy **erőforráscsoport** (vagy válasszon ki egy meglévőt), majd válassza ki **OK**.

4. Az a **méret kiválasztása** szakaszban jelölje be **A1 Standard**, és kattintson a **kiválasztása**.  

5. Az a **beállítások** panelen elfogadhatja az alapértelmezett beállításokat, majd válassza ki **OK**.

6. Az a **összefoglalás** területén kattintson **OK** a virtuális gép létrehozásához.  

7. Az új virtuális gépet, jelölje ki a **virtuális gépek**, majd keresse meg az új virtuális gép, és kattintson a nevére.

    ![Minden erőforrás](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> A virtuális gépek üzembe helyezése néhány percet vesz igénybe.

## <a name="next-steps"></a>További lépések

Ismerte meg ebben az oktatóanyagban:

> [!div class="checklist"]
> * Előfizetés egy ajánlatra 
> * Az ajánlat tesztelése

> [!div class="nextstepaction"]
> [Virtuális gép létrehozása egy közösségi sablon](azure-stack-create-vm-template.md)