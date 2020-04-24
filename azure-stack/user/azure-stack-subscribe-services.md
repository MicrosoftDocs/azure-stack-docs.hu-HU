---
title: Előfizetés létrehozása Azure Stack hub ajánlatával
description: Ismerje meg, hogyan hozhat létre új előfizetést Azure Stack hub ajánlatával, majd tesztelje az ajánlatot egy teszt virtuális géppel.
author: bryanla
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 244d5e214e2d1d42e5697a5aafd51c17d97465f6
ms.sourcegitcommit: bdd4d529bd3e115a9f76eece62b1613448d5d020
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "77704896"
---
# <a name="tutorial-create-and-test-a-subscription-in-azure-stack-hub"></a>Oktatóanyag: előfizetés létrehozása és tesztelése Azure Stack hub-ban

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy ajánlatot tartalmazó előfizetést, majd hogyan tesztelheti azt. A teszthez jelentkezzen be az Azure Stack hub felhasználói portálra felhőalapú rendszergazdaként, iratkozzon fel az ajánlatra, majd hozzon létre egy virtuális gépet (VM).

> [!TIP]
> A fejlettebb értékelési élmény érdekében [létrehozhat egy adott felhasználóhoz tartozó előfizetést](../operator/azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) , majd a felhasználói portálon is bejelentkezhet.

Ez az oktatóanyag bemutatja, hogyan fizethet elő Azure Stack hub-ajánlatra.

Ismertetett témák:

> [!div class="checklist"]
> * Előfizetés egy ajánlatra 
> * Az ajánlat tesztelése

## <a name="subscribe-to-an-offer"></a>Előfizetés egy ajánlatra

Ha felhasználóként szeretne előfizetni az ajánlatra, jelentkezzen be az Azure Stack hub felhasználói portálra, és tekintse meg az Azure Stack hub-operátor által kínált elérhető szolgáltatásokat.

1. Jelentkezzen be a felhasználói portálra, és válassza az **előfizetés beszerzése**lehetőséget.

   ![Előfizetés beszerzése](media/azure-stack-subscribe-services/get-subscription.png)

2. A **Megjelenítendő név** mezőben adja meg az előfizetés nevét. Ezután válassza az **ajánlat** lehetőséget, hogy az **ajánlat választása** szakaszban válassza ki az elérhető ajánlatok egyikét. Ezután válassza a **Létrehozás**lehetőséget.

   ![Ajánlat létrehozása](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Frissítse a felhasználói portált az előfizetés használatának megkezdéséhez.

3. A létrehozott előfizetés megtekintéséhez válassza a **minden szolgáltatás**lehetőséget. Ezután az **általános** kategóriában válassza az **előfizetések**lehetőséget, majd válassza ki az új előfizetést. Az ajánlatra való előfizetést követően frissítse a portált, és ellenőrizze, hogy az új szolgáltatások részét képezték-e az új előfizetés részeként. Ebben a példában a **virtuális gépek** lettek hozzáadva.

   ![Előfizetés megtekintése](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>Az ajánlat tesztelése

Ha bejelentkezett a felhasználói portálra, tesztelje az ajánlatot úgy, hogy kiépít egy virtuális gépet az új előfizetési funkciók használatával.

> [!NOTE]
> Ehhez a teszthez először hozzá kell adni egy Windows Server 2016 Datacenter virtuális gépet a Azure Stack hub piactérhez.

1. Jelentkezzen be a felhasználói portálra.

2. A felhasználói portálon válassza a **Virtual Machines**, majd a **Hozzáadás**, majd a **Windows Server 2016 Datacenter**elemet, majd válassza a **Létrehozás**lehetőséget.

3. Az **alapismeretek** szakaszban adja meg a **nevet**, a **felhasználónevet**és a **jelszót**, válasszon ki egy **előfizetést**, hozzon létre egy **erőforráscsoportot** (vagy válasszon ki egy meglévőt), majd kattintson **az OK gombra**.

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
