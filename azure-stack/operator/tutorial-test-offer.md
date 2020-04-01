---
title: Azure Stack hub szolgáltatási ajánlat tesztelése.
description: Megtudhatja, hogyan tesztelheti a szolgáltatási ajánlatokat az előfizetés létrehozásával és az erőforrások üzembe helyezésével.
author: BryanLa
ms.author: bryanla
ms.topic: tutorial
ms.date: 10/13/2019
ms.reviewer: shriramnat
ms.lastreviewed: 10/06/2019
ms.openlocfilehash: 2aae33b324be4828b514ffbd019992aae989f50b
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423847"
---
# <a name="tutorial-test-a-service-offering"></a>Oktatóanyag: szolgáltatási ajánlat tesztelése

Az előző oktatóanyagban létrehozott egy ajánlatot a felhasználók számára. Ebből az oktatóanyagból megtudhatja, hogyan tesztelheti az ajánlatot, ha a segítségével előfizetést hoz létre. Ezután létrehozhatja és üzembe helyezheti az erőforrásokat az előfizetés által jogosult alapszolgáltatásokhoz.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Előfizetés létrehozása
> * Erőforrások létrehozása és üzembe helyezése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt végre kell hajtania a következő előfeltételeket:

- Fejezze be a [szolgáltatás nyújtása a felhasználók számára](tutorial-offer-services.md) oktatóanyagot. Ez a témakör bemutatja, hogyan hozhatja létre az oktatóanyag által használt ajánlatot.

- Az ebben az oktatóanyagban előfizetett ajánlat lehetővé teszi a virtuális gép (VM) erőforrásának üzembe helyezését. Ha tesztelni szeretné a virtuális gépek üzembe helyezését, először a Azure Stack hub piactéren kell elérhetővé tenni egy virtuálisgép-rendszerképet az Azure Marketplace-ről való letöltéssel. Útmutatásért lásd: [Marketplace-elemek letöltése az Azure-ból a Azure stack hub-](azure-stack-download-azure-marketplace-item.md) ba. 

## <a name="subscribe-to-the-offer"></a>Előfizetés az ajánlatra

1. Bejelentkezés a felhasználói portálra felhasználói fiókkal 

   - Az integrált rendszerek esetében az URL-cím az operátor régiója és a külső tartománynév alapján változik, a https://portal.&ltformátumot használva. *régió*&gt;.&lt;*FQDN*&gt;.
   - Ha a Azure Stack Development Kit használja, a portál címe https://portal.local.azurestack.external.

1. Válassza az **előfizetés beszerzése** csempét.

   ![Előfizetés beszerzése](media/tutorial-test-offer/1-get-subscription.png)

1. Az **előfizetés beszerzése**területen adja meg az új előfizetés nevét a **megjelenítendő név** mezőben. Válassza az **ajánlat**lehetőséget, majd válassza ki az előző oktatóanyagban létrehozott ajánlatot az **ajánlat kiválasztása** listából. Kattintson a **Létrehozás** gombra.

   ![Ajánlat létrehozása](media/tutorial-test-offer/2-create-subscription.png)

1. Az előfizetés megtekintéséhez válassza a **minden szolgáltatás**lehetőséget, majd az **általános** kategóriában válassza az **előfizetések**lehetőséget. Válassza ki az új előfizetést, és tekintse meg a hozzá társított ajánlatot, valamint annak tulajdonságait.

   >[!NOTE]
   >Ha előfizetett egy ajánlatra, előfordulhat, hogy frissítenie kell a portált, hogy megtekintse, mely szolgáltatások tartoznak az új előfizetés részévé.

::: moniker range=">=azs-1902"
## <a name="deploy-a-storage-account-resource"></a>Storage-fiók erőforrásának üzembe helyezése

A felhasználói portálon kiépít egy Storage-fiókot az előző szakaszban létrehozott előfizetéssel.

1. Jelentkezzen be a felhasználói portálra egy felhasználói fiókkal.

1. Válassza az **+ erőforrás létrehozása** > **adattároló** > **Storage-fiók – blob, fájl, tábla, üzenetsor**lehetőséget.

1. A **Storage-fiók létrehozása**területen adja meg a következő információkat:
  
   - Adjon meg egy **nevet**
   - Válassza ki az új **előfizetést**
   - Válasszon ki egy **erőforráscsoportot** (vagy hozzon létre egyet) 
   - Kattintson a **Létrehozás** gombra a tárfiók létrehozásához.

1. Az üzembe helyezés megkezdése után térjen vissza az irányítópultra. Az új Storage-fiók megtekintéséhez válassza az **összes erőforrás**lehetőséget. Keresse meg a Storage-fiókot, és válassza ki a nevét a keresési eredmények közül. Innen kezelheti a Storage-fiókot és annak tartalmát.

## <a name="deploy-a-virtual-machine-resource"></a>Virtuálisgép-erőforrás üzembe helyezése

A felhasználói portálon egy virtuális gépet kell kiépíteni az előző szakaszban létrehozott előfizetéssel.

1. Jelentkezzen be a felhasználói portálra egy felhasználói fiókkal.

1. Válassza az **+ erőforrás létrehozása** > **számítási** > **\<rendszerkép-név\>** lehetőséget, ahol a "rendszerkép-Name" az előfeltételek között letöltött virtuális gép neve.
1. A **virtuális gép létrehozása** / **alapjai**területen adja meg a következő információkat:
  
   - Adja meg a virtuális gép **nevét** .
   - Adja meg a rendszergazdai fiók **felhasználónevét** .
   - Linux rendszerű virtuális gépek esetén válassza a "jelszó" lehetőséget a **hitelesítési típushoz**.
   - A rendszergazdai fiókhoz adja meg a **jelszót** és a **jelszó megerősítését**.
   - Válassza ki az új **előfizetést**.
   - Válasszon ki egy **erőforráscsoportot** (vagy hozzon létre egyet). 
   - Kattintson **az OK** gombra az adatok érvényesítéséhez és a folytatáshoz.

1. A **méret kiválasztása**lapon szükség szerint szűrje a listát, válasszon ki egy VIRTUÁLISGÉP-SKU-t, majd válassza a **kiválasztás**lehetőséget.  
1. A **Beállítások**területen adja meg a **nyilvános bejövő portok kiválasztása**területen megnyitni kívánt portot (ka) t, majd kattintson **az OK gombra**.
   > [!NOTE]
   > Az "RDP (3389)" elem kiválasztásával például távolról csatlakozhat a virtuális géphez a futása közben.
1. Az **Összefoglalás**területen tekintse át a beállításokat, majd kattintson az **OK** gombra a virtuális gép létrehozásához.  
1. Az üzembe helyezés megkezdése után térjen vissza az irányítópultra. Az új virtuális gép megjelenítéséhez válassza az **összes erőforrás**lehetőséget. Keresse meg a virtuális gépet, és válassza ki a nevét a keresési eredmények közül. Innen elérheti és kezelheti a virtuális gépet.
   > [!NOTE]
   > A virtuális gép teljes üzembe helyezése és elindítása több percet is igénybe vehet. Ha a virtuális gép használatra kész, az [állapot](/azure/virtual-machines/windows/states-lifecycle) a "Running" értékre változik.

::: moniker-end

::: moniker range="<=azs-1901"
## <a name="deploy-a-virtual-machine-resource-1901-and-earlier"></a>Virtuálisgép-erőforrás üzembe helyezése (1901-es és korábbi verziók)

A felhasználói portálon egy virtuális gépet kell kiépíteni az új előfizetéssel.

1. Jelentkezzen be a felhasználói portálra egy felhasználói fiókkal.

1. Az irányítópulton válassza az **+ erőforrás létrehozása** > **számítás** > **Windows Server 2016 Datacenter eval**lehetőséget, majd válassza a **Létrehozás**lehetőséget.

1. Az **alapismeretek**területen adja meg a következő információkat:
  
   - Adjon meg egy **nevet**
   - Adja meg a **felhasználónevet**
   - Adja meg a **jelszót**
   - Válassza ki az új **előfizetést**
   - Hozzon létre egy **erőforráscsoportot** (vagy válasszon ki egy meglévőt) 
   - Az adatok mentéséhez kattintson **az OK gombra** .

1. A **méret kiválasztása**területen válassza az **a1 standard**lehetőséget, majd **válassza a elemet**.  
1. A **Beállítások**területen válassza a **virtuális hálózat**lehetőséget.

1. A **virtuális hálózat kiválasztása**területen válassza az **új létrehozása**lehetőséget.

1. A **virtuális hálózat létrehozása**lapon fogadja el az összes alapértelmezett beállítást, majd kattintson **az OK gombra**.

1. A hálózati konfiguráció mentéséhez kattintson **az OK gombra** a **Beállítások** területen.

1. Az **Összefoglalás**területen kattintson az **OK** gombra a virtuális gép létrehozásához.  

1. Az új virtuális gép megjelenítéséhez válassza az **összes erőforrás**lehetőséget. Keresse meg a virtuális gépet, és válassza ki a nevét a keresési eredmények közül.
::: moniker-end

## <a name="next-steps"></a>Következő lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Előfizetés létrehozása
> * Erőforrások létrehozása és üzembe helyezése 

A következő lépés az erőforrás-szolgáltatók üzembe helyezése érték-hozzáadási szolgáltatásokhoz című témakörben olvasható. Lehetővé teszik, hogy még több szolgáltatást nyújtson a csomagok felhasználói számára:

- [SQL-ajánlat Azure Stack hub-on](azure-stack-sql-resource-provider.md)
- [MySQL-ajánlat Azure Stack hub-on](azure-stack-mysql-resource-provider.md)
- [Ajánlat App Service Azure Stack hub-on](azure-stack-app-service-overview.md)
- [Ajánlat Event Hubs Azure Stack hub-on](event-hubs-rp-overview.md)
