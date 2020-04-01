---
title: Szolgáltatási ajánlat létrehozása felhasználók számára
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan hozhat létre szolgáltatási ajánlatokat Azure Stack hub-ban ajánlatok, csomagok és szolgáltatások használatával.
author: BryanLa
ms.author: bryanla
ms.topic: tutorial
ms.date: 10/16/2019
ms.reviewer: shriramnat
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 772871cffa3d5394d1d8b939fa22ae91bc90d4ac
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423905"
---
# <a name="create-a-service-offering-for-users-in-azure-stack-hub"></a>Szolgáltatási ajánlat létrehozása Azure Stack hub felhasználói számára

Ez az oktatóanyag egy operátort mutat be az ajánlat létrehozásához. Az ajánlat lehetővé teszi, hogy a szolgáltatások elérhetők legyenek a felhasználók számára az előfizetések alapján. Az ajánlatra való előfizetést követően a felhasználó az ajánlat által meghatározott szolgáltatásokon belül hozhat létre és helyezhet üzembe erőforrásokat.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzon létre egy ajánlatot.
> * Hozzon létre egy csomagot.
> * Szolgáltatások és kvóták kiosztása egy csomaghoz.
> * Rendeljen egy csomagot az ajánlathoz.

## <a name="overview"></a>Áttekintés

Egy ajánlat egy vagy több csomagból áll. A csomag az egyes szolgáltatások megfelelő erőforrás-szolgáltatójának és kvótájának megadásával jogosult egy vagy több szolgáltatás elérésére. A csomagok alapcsomagként vehetők fel az ajánlatba, vagy kiegészítő csomagként is kiterjeszthetők az ajánlatra. További információ: [szolgáltatás, csomag, ajánlat, előfizetés áttekintése](service-plan-offer-subscription-overview.md).

![Előfizetések, ajánlatok és csomagok Azure Stack hub-ban](media/azure-stack-key-features/image4.png)

### <a name="resource-providers"></a>Erőforrás-szolgáltatók

Az erőforrás-szolgáltató támogatja az erőforrások létrehozását, üzembe helyezését és felügyeletét szolgáltatásként. Gyakori példa a Microsoft. számítási erőforrás-szolgáltató, amely lehetővé teszi a virtuális gépek (VM-EK) létrehozását és üzembe helyezését. Az Azure erőforrás-kezelési modell áttekintését [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) tekintheti meg.

Azure Stack központban az erőforrás-szolgáltatók két általános kategóriája létezik: az erőforrások központi szolgáltatásként való üzembe helyezése, valamint az érték-hozzáadási szolgáltatásként üzembe helyezett szolgáltatások.

### <a name="foundational-services"></a>Alapszolgáltatások

>[!NOTE]
> Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre ajánlatokat az alapszolgáltatások alapján. 

Az alapszolgáltatásokat a következő erőforrás-szolgáltatók támogatják, amelyek natív módon érhetők el Azure Stack hub minden telepítésekor:

| Erőforrás-szolgáltató | Példa erőforrások |
| ----------------- | ------------------|
| Microsoft.Compute | Virtuális gépek, lemezek, virtuálisgép-méretezési csoportok |
| Microsoft.KeyVault | Kulcstartók, titkok |
| Microsoft.Network | Virtuális hálózatok, nyilvános IP-címek, terheléselosztó |
| Microsoft.Storage | Storage-fiókok, blobok, várólisták, táblák |

### <a name="value-add-services"></a>Érték – szolgáltatások hozzáadása

>[!NOTE]
> Ha értéket szeretne hozzáadni a szolgáltatáshoz, először telepítenie kell a megfelelő erőforrás-szolgáltatót Azure Stack hub piactéren. A telepítést követően az erőforrásai ugyanúgy elérhetők a felhasználók számára, mint az alapszolgáltatások. Tekintse meg a TARTALOMJEGYZÉKben az olyan erőforrás **-** szolgáltatók aktuális készletét, amelyek támogatják az értéknövelt szolgáltatás ajánlatait.

Az értéknövelt szolgáltatásokat a Azure Stack hub telepítése után telepített erőforrás-szolgáltatók támogatják. Példák erre vonatkozóan:

| Erőforrás-szolgáltató | Példa erőforrások |
| ----------------- | ------------------------- |
| Microsoft.Web | App Service Function apps, Web Apps, API apps | 
| Microsoft. MySqlAdapter | MySQL-üzemeltetési kiszolgáló, MySQL-adatbázis | 
| Microsoft. SqlAdapter | SQL Server üzemeltetési kiszolgáló, SQL Server adatbázis |
| Microsoft.EventHub | Event Hubs |

::: moniker range=">=azs-1902"

## <a name="create-an-offer"></a>Ajánlat létrehozása

Az ajánlat létrehozási folyamata során létre kell hoznia egy ajánlatot és egy csomagot is. Ezt a csomagot az ajánlat alapcsomagja használja. A terv létrehozása során meg kell adnia a csomagban elérhetővé tett szolgáltatásokat és a hozzájuk tartozó kvótákat.

1. Jelentkezzen be a felügyeleti portálra egy Felhőbeli rendszergazdai fiókkal.

    - Az integrált rendszerek esetében az URL-cím az operátor régiója és a külső tartománynév alapján változhat. Az URL-cím a `https://adminportal.<region>.<FQDN>`formátumot használja.
    - Ha a Azure Stack Development Kit használja, az URL-cím `https://adminportal.local.azurestack.external`.

    Ezután válassza **az + erőforrás létrehozása** > **ajánlatok + csomagok** > **ajánlat**lehetőséget.

    ![Új ajánlat az Azure Stack hub felügyeleti portálján](media/tutorial-offer-services/1-create-resource-offer.png)

1. Az **új ajánlat létrehozása** az **alapok** lapon adja meg a **megjelenítendő nevet**, az **erőforrás nevét**, majd válasszon ki egy meglévőt, vagy hozzon létre egy új **erőforráscsoportot**. A megjelenítendő név az ajánlat rövid neve. Csak a Felhőbeli operátor láthatja az erőforrás nevét, amely a rendszergazdák által az ajánlattal Azure Resource Manager erőforrásként használt név.

   ![Megjelenítendő név a Azure Stack hub felügyeleti portálján](media/tutorial-offer-services/2-create-new-offer.png)

1. Válassza ki az **alapcsomagok** lapot, majd válassza az **új terv létrehozása** lehetőséget új terv létrehozásához. A terv alapcsomagként is hozzá lesz adva az ajánlathoz.

   ![Csomag hozzáadása a Azure Stack hub felügyeleti portálján](media/tutorial-offer-services/3-create-new-offer-base-plans.png)

1. Az **alapbeállítások** lap **új tervében** adja meg a **megjelenítendő nevet** és az **erőforrás nevét**. A megjelenítendő név a terv felhasználóbarát neve, amelyet a felhasználók látnak. Csak a Felhőbeli operátor láthatja az erőforrás nevét, amely a Felhőbeli operátorok által a csomaggal Azure Resource Manager erőforrásként való együttműködéshez használt név. Az **erőforráscsoport** az ajánlathoz megadott értékre lesz állítva.

   ![A megjelenítendő név megtervezése Azure Stack hub felügyeleti portálján](media/tutorial-offer-services/4-create-new-plan-basics.png)

1. Válassza a **szolgáltatások** fület, és megtekintheti a telepített erőforrás-szolgáltatók által elérhető szolgáltatások listáját. Válassza a **Microsoft. számítás**, a **Microsoft. Network**és a **Microsoft. Storage**lehetőséget.

   ![Szolgáltatások megtervezése Azure Stack hub felügyeleti portálon](media/tutorial-offer-services/5-create-new-plan-services.png)

1. Válassza a **kvóták** fület, és megtekintheti a csomaghoz engedélyezett szolgáltatások listáját. Válassza az **új létrehozása** lehetőséget a **Microsoft. számítás**egyéni kvótájának megadásához. A kvóta **nevét** kötelező megadni; elfogadhatja vagy módosíthatja az egyes kvóták értékét. Ha elkészült, kattintson az **OK gombra** , majd ismételje meg ezeket a lépéseket a fennmaradó szolgáltatásokhoz.

   ![Számítási kvóta létrehozása Azure Stack hub felügyeleti portálján](media/tutorial-offer-services/6-create-new-plan-quotas.png)

1. Válassza a **felülvizsgálat + létrehozás** lapot. Ekkor meg kell jelennie egy zöld "sikeres ellenőrzés" szalagcímnek, amely azt jelzi, hogy az új alapcsomag készen áll a létrehozásra. Kattintson a **Létrehozás** gombra. Egy értesítést is látnia kell, amely jelzi, hogy a terv létrejött.

   ![Új csomag létrehozása a Azure Stack hub felügyeleti portálján](media/tutorial-offer-services/7-create-new-plan-review-create.png)

1. Miután visszatért az **új ajánlat létrehozása** lap **alaptervek** lapjára, megfigyelheti, hogy a terv létrejött. Győződjön meg arról, hogy az új terv be van jelölve az ajánlatban alapcsomagként való felvételre, majd válassza a **felülvizsgálat + létrehozás**lehetőséget.

   ![Alapcsomag hozzáadása az Azure Stack hub felügyeleti portálján](media/tutorial-offer-services/8-create-new-offer-base-plans-done.png)

1. A **felülvizsgálat + létrehozás** lapon megjelenik egy zöld "érvényesítési átadott" szalagcím a felső oldalon. Tekintse át az "alapszintű" és az "alapcsomagok" információt, és ha elkészült, válassza a **Létrehozás** lehetőséget.

   ![Új ajánlat létrehozása Azure Stack hub felügyeleti portálján](media/tutorial-offer-services/9-create-new-offer-review-create.png)

1. Először az "üzembe helyezés folyamatban" oldal jelenik meg, amelyet az ajánlat üzembe helyezése után "az üzembe helyezés befejeződött" című oldalon láthat. Válassza ki az ajánlat nevét az **erőforrás** oszlopban.

   ![Az ajánlat üzembe helyezése befejeződött az Azure Stack hub felügyeleti portálján](media/tutorial-offer-services/10-offer-deployment-complete.png)

1. Figyelje meg, hogy az ajánlata továbbra is magánjellegű, ami megakadályozza, hogy a felhasználók előiratkozzon. Változtassa meg nyilvánosra az **Állapot módosítása**lehetőség kiválasztásával, majd válassza a **nyilvános**lehetőséget.

    ![Nyilvános állapot az Azure Stack hub felügyeleti portálján](media/tutorial-offer-services/11-offer-change-state.png)

::: moniker-end

::: moniker range="<=azs-1901"

## <a name="create-an-offer-1901-and-earlier"></a>Ajánlat létrehozása (1901 és korábbi verziók)

Az ajánlat létrehozási folyamata során létre kell hoznia egy ajánlatot és egy csomagot is. Ezt a csomagot az ajánlat alapcsomagja használja. A terv létrehozása során meg kell adnia a csomagban elérhetővé tett szolgáltatásokat és a hozzájuk tartozó kvótákat.

1. Jelentkezzen be a felügyeleti portálra egy Felhőbeli rendszergazdai fiókkal.

    - Az integrált rendszerek esetében az URL-cím az operátor régiója és a külső tartománynév alapján változik, a `https://adminportal.<region>.<FQDN>`formátum használatával.
    - Ha a Azure Stack Development Kit használja, az URL-cím <https://adminportal.local.azurestack.external>.

    Ezután válassza **az + erőforrás létrehozása** > **ajánlatok + csomagok** > **ajánlat**lehetőséget.

    ![Új ajánlat az Azure Stack hub felügyeleti portálján](media/tutorial-offer-services/image01.png)

1. Az **új ajánlat**mezőben adja meg a **megjelenítendő nevet** és az **erőforrás nevét**, majd válasszon ki egy új vagy egy meglévő **erőforráscsoportot**. A megjelenítendő név az ajánlat rövid neve. Csak a Felhőbeli operátor láthatja az erőforrás nevét, amely a rendszergazdák által az ajánlattal Azure Resource Manager erőforrásként használt név.

   ![Megjelenítendő név a Azure Stack hub felügyeleti portálján](media/tutorial-offer-services/image02.png)

1. Válassza az **alapcsomagok**lehetőséget, majd a **terv** szakaszban válassza a **Hozzáadás** lehetőséget, ha új csomagot szeretne hozzáadni az ajánlathoz.

   ![Csomag hozzáadása a Azure Stack hub felügyeleti portálján](media/tutorial-offer-services/image03.png)

1. Az **új terv** szakaszban adja **meg a megjelenítendő név** és az **erőforrás nevét**. A megjelenítendő név a terv felhasználóbarát neve, amelyet a felhasználók látnak. Csak a Felhőbeli operátor láthatja az erőforrás nevét, amely a Felhőbeli operátorok által a csomaggal Azure Resource Manager erőforrásként való együttműködéshez használt név.

   ![A megjelenítendő név megtervezése Azure Stack hub felügyeleti portálján](media/tutorial-offer-services/image04.png)

1. Válassza a **szolgáltatások**lehetőséget. A szolgáltatások listájából válassza a **Microsoft. számítás**, a **Microsoft. Network**és a **Microsoft. Storage**lehetőséget. Válassza a **kiválasztás** lehetőséget, hogy hozzáadja ezeket a szolgáltatásokat a csomaghoz.

   ![Szolgáltatások megtervezése Azure Stack hub felügyeleti portálon](media/tutorial-offer-services/image05.png)

1. Válassza a **kvóták**lehetőséget, majd válassza ki azt az első szolgáltatást, amelyhez kvótát kíván létrehozni. IaaS-kvóta esetén használja a következő példát útmutatóként a számítási, hálózati és tárolási szolgáltatások kvótáinak konfigurálásához.

    - Először hozzon létre egy kvótát a számítási szolgáltatáshoz. A névtér listában válassza a **Microsoft. számítás** lehetőséget, majd válassza az **új kvóta létrehozása**lehetőséget.
    
      ![Új kvóta létrehozása](media/tutorial-offer-services/image06.png)

   - A **kvóta létrehozása**területen adja meg a kvóta nevét. Megváltoztathatja vagy elfogadhatja a megjelenő kvóta-értékeket. Ebben a példában fogadjuk el az alapértelmezett beállításokat, majd az **OK**gombot.
   
      ![Kvóta neve](media/tutorial-offer-services/image07.png)
       
    - Válassza a **Microsoft. számítás** elemet a névtér listából, majd válassza ki a létrehozott kvótát. Ez a lépés a kvótát a számítási szolgáltatáshoz csatolja.
    
      ![Kvóta kiválasztása](media/tutorial-offer-services/image08.png)
        
        Ismételje meg ezeket a lépéseket a hálózati és tárolási szolgáltatásokhoz. Ha elkészült, válassza az **OK** lehetőséget a **kvóták** területen az összes kvóta mentéséhez.

1. Az **új csomag**területen kattintson **az OK gombra**.

1. A **terv**területen válassza ki az új csomagot, majd **válassza a elemet**.

1. Az **új ajánlat**területen válassza a **Létrehozás**lehetőséget. Ekkor megjelenik egy értesítés az ajánlat létrehozásakor.

1. Az irányítópult menüben válassza az **ajánlatok** lehetőséget, majd válassza ki a létrehozott ajánlatot.

1. Válassza az **Állapot módosítása**, majd a **nyilvános**lehetőséget.

    ![Nyilvános állapot](media/tutorial-offer-services/image09.png)

::: moniker-end
 
## <a name="next-steps"></a>Következő lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Hozzon létre egy ajánlatot.
> * Hozzon létre egy csomagot.
> * Szolgáltatások és kvóták kiosztása egy csomaghoz.
> * Rendeljen egy csomagot az ajánlathoz.

Folytassa a következő oktatóanyaggal, amely a következőket ismerteti:
> [!div class="nextstepaction"]
> [Az oktatóanyagban kínált szolgáltatások tesztelése](tutorial-test-offer.md)
