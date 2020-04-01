---
title: Event Hubs-fürt létrehozása a portál használatával
description: Megtudhatja, hogyan hozhat létre Event Hubs fürtöt a Azure Stack hub felhasználói portál használatával.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: quickstart
ms.date: 01/22/2020
ms.reviewer: bryanla
ms.lastreviewed: 01/22/2020
ms.openlocfilehash: 6af9ef6f562c9b31d7310b7f35d7b6b0533472ea
ms.sourcegitcommit: b824c7b9af9ba415ca4fe8d15673b521362f0abb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479244"
---
# <a name="quickstart-create-an-event-hubs-cluster-using-the-azure-stack-hub-portal"></a>Rövid útmutató: Event Hubs-fürt létrehozása az Azure Stack hub portál használatával

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Event Hubs-fürtöt az Azure Stack hub felhasználói portál használatával. 

Event Hubs fürtök egybérlős üzemelő példányokat biztosítanak a legigényesebb folyamatos átviteli igényekhez. Egy Event Hubs fürt másodpercenként több millió eseményt tud befogadni a garantált kapacitással és a másodpercenkénti késéssel. Event Hubs-fürtök minden népszerű szolgáltatást tartalmaz, és az Azure Event Hubs Edition által kínált funkciókkal zárt paritást biztosít.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzése előtt győződjön meg arról, hogy a Event Hubs szolgáltatás elérhető az előfizetésében. Ha nem, akkor a rendszergazdával együttműködve [telepítse a Event Hubs Azure stack hub erőforrás-szolgáltatón](../operator/event-hubs-rp-overview.md). A telepítési lépések kiterjednek az Event Hubs szolgáltatást tartalmazó ajánlat létrehozására is. 

Ha egy ajánlat elérhetővé válik, a rendszergazda létrehozhatja vagy frissítheti az előfizetését, hogy tartalmazza a Event Hubs. Másik lehetőségként [előfizethet az új ajánlatra, és létrehozhatja saját előfizetését](azure-stack-subscribe-services.md)is.

## <a name="overview"></a>Áttekintés

Event Hubs-fürtöket a kapacitási egységek (ke) megadásával hozhatja létre. A CU a processzor, a tárterület és a memória erőforrásainak előre lefoglalt mennyisége. Event Hubs a fürtöket a CPU/HR számlázza. A fürt által használt magok (CPU-EK) száma akkor jelenik meg, ha a fürt létrehozása során a ke (fürt mérete) érték van kiválasztva. A fürterőforrás-használattal kapcsolatos részletesebb információkért lásd: a [Event Hubs kapacitásának megtervezése Azure stack hub-on](../operator/event-hubs-rp-capacity-planning.md). 

Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Azure Stack hub felhasználói portált a következőre:
- Hozzon létre egy 1 CU Event Hubs fürtöt.
- Hozzon létre egy névteret a fürtben.
- Hozzon létre egy Event hubot a névtérben.
- Event Hubs-fürt törlése

## <a name="create-an-event-hubs-cluster"></a>Event Hubs-fürt létrehozása

Az Event Hubs-fürtök egyedi hatókörű tárolót biztosítanak, amelyben egy vagy több névteret hozhat létre. Event Hubs-fürt létrehozásához hajtsa végre a következő lépéseket: 

1. Jelentkezzen be az Azure Stack hub felhasználói portálra.
2. Válassza a **minden szolgáltatás** a bal oldali navigációs ablaktáblán, írja be a "Event Hubs fürtök" kifejezést a keresősáv mezőbe, és válassza ki a **Event Hubs fürtök** elemet az eredmények listából.
3. A Event Hubs- **fürtök** lapon válassza a **+ Hozzáadás** lehetőséget a felső menüben. A jobb oldalon megnyílik a **Event Hubs-fürt létrehozása** panel.
4. A **Event Hubs-fürt létrehozása** lap **alapok** lapján:  
   - **Fürt neve**: adjon meg egy nevet. A rendszer azonnal ellenőrzi, hogy a név elérhető-e. Ha elérhető, egy pipa jelenik meg a mező jobb oldalán. 
   - **Előfizetés**: válassza ki azt az előfizetést, amelyben létre szeretné hozni a fürtöt. 
   - **Erőforráscsoport**: hozza létre vagy válassza ki azt az erőforráscsoportot, amelyben létre szeretné hozni a fürtöt. 
   - A folytatáshoz kattintson a **következő: címkék >**  gombra a lap alján. Előfordulhat, hogy várnia kell, amíg a rendszer teljes mértékben kiépíti az erőforrásokat. 

   [![Event hub-fürt létrehozása – alapismeretek](media/event-hubs-quickstart-cluster-portal/1-create-cluster-basics.png)](media/event-hubs-quickstart-cluster-portal/1-create-cluster-basics.png#lightbox)

5. A **címkék** lapon: 
   - Igény szerint megadhatja az erőforrás-kódelemek nevét/érték párokat.  
   - A folytatáshoz válassza a **következőt: felülvizsgálat + > létrehozása** gombra. 

   [![Event hub-fürt létrehozása – Címkék](media/event-hubs-quickstart-cluster-portal/1-create-cluster-tags.png)](media/event-hubs-quickstart-cluster-portal/1-create-cluster-tags.png#lightbox)

6. A **felülvizsgálat + létrehozás** lapon az oldal tetején található "Érvényesítés sikeres" szalagcímet is látnia kell. A részletek áttekintése után válassza a **Létrehozás** lehetőséget, amikor készen áll a fürt létrehozására. 

   [![Event hub-fürt létrehozása – áttekintés + létrehozás](media/event-hubs-quickstart-cluster-portal/1-create-cluster-review.png)](media/event-hubs-quickstart-cluster-portal/1-create-cluster-review.png#lightbox)

   >[!NOTE]
   > Egy Event Hubs fürt üzembe helyezése több percet is igénybe vehet, általában legalább 45 percet.

7. Az üzembe helyezés során megjelenik az üzemelő **példány** állapota lap. Az üzembe helyezés befejeződése után az oldal a **központi telepítésre**változik. Mielőtt továbblépne a következő szakaszra, válassza az **Ugrás az erőforráshoz** gombot az új fürt megtekintéséhez.

   [![Event hub-fürt létrehozása – az üzembe helyezés befejeződött](media/event-hubs-quickstart-cluster-portal/1-deployment-complete.png)](media/event-hubs-quickstart-cluster-portal/1-deployment-complete.png#lightbox)


## <a name="create-a-namespace"></a>Névtér létrehozása

Most hozzon létre egy névteret az új fürtön belül:

1. **Event Hubs fürt** **Áttekintés** lapján válassza a felső menüben a **+ névtér** lehetőséget. 

   [![Event Hubs-fürtök – fürt kiválasztása](media/event-hubs-quickstart-cluster-portal/2-view-cluster.png)](media/event-hubs-quickstart-cluster-portal/2-view-cluster.png#lightbox)

2. A **névtér létrehozása a fürt** panelen:

   - **Név**: adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e. Ha elérhető, egy pipa jelenik meg a mező jobb oldalán. 
   - **Tulajdonság/érték lista**: a névtér örökli a következő tulajdonságokat: 
     - Előfizetés azonosítója 
     - Erőforráscsoport 
     - Hely 
     - Fürt neve 

   - Válassza a **létrehozás** elemet a névtér létrehozásához:

   [![Event Hubs-fürtök – névtér létrehozása](media/event-hubs-quickstart-cluster-portal/2-view-cluster-create-namespace.png)](media/event-hubs-quickstart-cluster-portal/2-view-cluster-create-namespace.png#lightbox)

3. Jegyezze fel az új névteret a **fürt névterei**alatt. Mielőtt továbblépne a következő szakaszra, válassza ki az új névtérre mutató hivatkozást. 

   [![Event Hubs-fürtök – névtér megtekintése](media/event-hubs-quickstart-cluster-portal/2-view-cluster-with-namespace.png)](media/event-hubs-quickstart-cluster-portal/2-view-cluster-with-namespace.png#lightbox)

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

1. **Event Hubs névtér** **Áttekintés** lapján válassza a **+ Event hub** lehetőséget a felső menüben.  

   [![Event Hubs – névtér áttekintése](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview.png)](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview.png#lightbox)

2. Az **Event hub létrehozása** panelen:
   - **Név**: adja meg az Event hub nevét. A név csak betűket, számokat, pontokat, kötőjeleket és aláhúzást tartalmazhat. A névnek betűvel vagy számmal kell kezdődnie és végződnie. A rendszer azonnal ellenőrzi, hogy a név elérhető-e. Ha elérhető, egy pipa jelenik meg a mező jobb oldalán.
   - Az Event hub létrehozásához válassza az  **létrehozása** lehetőséget.

   [![Event Hubs – a névtér áttekintése Event hub létrehozása](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview-create-event-hub.png)](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview-create-event-hub.png#lightbox)

## <a name="delete-an-event-hubs-cluster"></a>Event Hubs-fürt törlése

A fürt törlése:

1. A bal oldali navigációs panelen válassza a **minden szolgáltatás** elemet. A keresőmezőbe írja be a "Event Hubs fürtök" kifejezést, majd válassza ki a **Event Hubs fürtök** elemet az eredmények listából.
2. Az **Event hub-fürtök** lapon keresse meg és válassza ki a korábban létrehozott fürtöt.

   [![Event Hubs fürtök – fürt törlése](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-clusters.png)](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-clusters.png#lightbox)

3. **Event Hubs fürt** **Áttekintés** lapján:
   - A felső menüben válassza a **törlés** elemet.  
   - Ekkor megnyílik a **fürt törlése** panel a jobb oldalon, és törlési megerősítő üzenet jelenik meg. 
   - Adja meg a fürt nevét, és válassza a  **törlése** elemet a fürt törléséhez. 

   [![Event Hubs fürtök – fürt törlése](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-delete.png)](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-delete.png#lightbox)

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre Event Hubs-fürtöt a portál használatával. Most folytassa a "küldési és fogadási események" rövid útmutatókat, az elsővel kezdve:  

> [!div class="nextstepaction"]
> [Események küldése vagy fogadása Event Hubs](/azure/event-hubs/get-started-dotnet-standard-send-v2)
