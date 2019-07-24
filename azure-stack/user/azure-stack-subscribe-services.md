---
title: Előfizetés létrehozása a Azure Stack ajánlatával | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre új előfizetést Azure Stack ajánlatával, majd tesztelje az ajánlatot egy teszt virtuális géppel.
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
ms.date: 06/04/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 5b72ccee255aadd5d8f42aefea9e397ba310812c
ms.sourcegitcommit: 72d45bb935db0db172d4d7c37d8e48e79e25af64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68376806"
---
# <a name="tutorial-create-and-test-a-subscription-in-azure-stack"></a>Oktatóanyag: Előfizetés létrehozása és tesztelése Azure Stack

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy ajánlatot tartalmazó előfizetést, majd hogyan tesztelheti azt. A teszthez jelentkezzen be a Azure Stack felhasználói portálra felhőalapú rendszergazdaként, iratkozzon fel az ajánlatra, majd hozzon létre egy virtuális gépet (VM).

> [!TIP]
> A fejlettebb értékelési élmény érdekében [létrehozhat egy adott felhasználóhoz tartozó](../operator/azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) előfizetést, majd a felhasználói portálon is bejelentkezhet.

Ez az oktatóanyag bemutatja, hogyan fizethet elő Azure Stack ajánlatra.

Ismertetett témák:

> [!div class="checklist"]
> * Előfizetés egy ajánlatra 
> * Az ajánlat tesztelése

## <a name="subscribe-to-an-offer"></a>Előfizetés egy ajánlatra

Ha felhasználóként szeretne előfizetni az ajánlatra, jelentkezzen be a Azure Stack felhasználói portálra, és tekintse meg az Azure Stack operátor által kínált elérhető szolgáltatásokat.

1. Jelentkezzen be a felhasználói portálra, és válassza az **előfizetés**beszerzése lehetőséget.

   ![Előfizetés beszerzése](media/azure-stack-subscribe-services/get-subscription.png)

2. A **Megjelenítendő név** mezőben adja meg az előfizetés nevét. Ezután válassza az **ajánlat** lehetőséget, hogy az **ajánlat választása** szakaszban válassza ki az elérhető ajánlatok egyikét. Ezután kattintson a **Létrehozás** elemre.

   ![Ajánlat létrehozása](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Frissítse a felhasználói portált az előfizetés használatának megkezdéséhez.

3. A létrehozott előfizetés megtekintéséhez válassza a **minden szolgáltatás**lehetőséget. Ezután az **általános** kategóriában válassza az előfizetések lehetőséget, majd válassza ki az új előfizetést. Az ajánlatra való előfizetést követően frissítse a portált, és ellenőrizze, hogy az új szolgáltatások részét képezték-e az új előfizetés részeként. Ebben a példában a **virtuális gépek** lettek hozzáadva.

   ![Előfizetés megtekintése](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>Az ajánlat tesztelése

Ha bejelentkezett a felhasználói portálra, tesztelje az ajánlatot az új előfizetési lehetőségekkel (VM) való kiépítés használatával.

> [!NOTE]
> Ehhez a teszthez először hozzá kell adni egy Windows Server 2016 Datacenter rendszerű virtuális gépet a Azure Stack Marketplace-hez.

1. Jelentkezzen be a felhasználói portálra.

2. A felhasználói portálon válassza a **Virtual Machines**, majd a **Hozzáadás**, majd a **Windows Server 2016 Datacenter**elemet, majd válassza a **Létrehozás**lehetőséget.

3. Az **alapismeretek** szakaszban adja meg a **nevet**, a **felhasználónevet**és a **jelszót**, válasszon ki egy előfizetést, hozzon létre egy **erőforráscsoportot** (vagy válasszon ki egy meglévőt), majd kattintson **az OK gombra**.

4. A **méret választása** szakaszban válassza az **a1 standard**lehetőséget, majd kattintson a **kiválasztás**elemre.  

5. A **Beállítások** panelen fogadja el az alapértelmezett értékeket, majd kattintson az **OK gombra**.

6. Az **Összefoglalás** szakaszban kattintson az **OK** gombra a virtuális gép létrehozásához.  

7. Az új virtuális gép megtekintéséhez válassza a **virtuális gépek**lehetőséget, majd keresse meg az új virtuális gépet, és válassza ki a nevét.

    ![Minden erőforrás](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> A virtuális gép üzembe helyezése néhány percet vesz igénybe.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Virtuális gép létrehozása közösségi sablonból](azure-stack-create-vm-template.md)