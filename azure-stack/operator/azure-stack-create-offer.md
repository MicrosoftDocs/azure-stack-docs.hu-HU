---
title: Ajánlat létrehozása a Azure Stackban | Microsoft Docs
description: Felhőalapú rendszergazdaként megtudhatja, hogyan hozhat létre ajánlatot a felhasználók számára Azure Stackban.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 1492c779144eac235e5c32e767e966b3a6cfb9fd
ms.sourcegitcommit: 8ddd70ba5ce05c591d3fa62597981859af107c06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70936142"
---
# <a name="create-an-offer-in-azure-stack"></a>Ajánlat létrehozása az Azure Stackben

[Az ajánlatok](azure-stack-overview.md) egy vagy több csomagból álló csomagcsoportok, amelyeket a szolgáltatók nyújtanak a felhasználóknak, amelyeket azok megvásárolhatnak vagy rájuk előfizethetnek. Ez a cikk ismerteti egy olyan ajánlat létrehozásának módját, amely tartalmazza a [létrehozott csomagot](azure-stack-create-plan.md). Ez az ajánlat lehetőséget ad az előfizetők számára virtuális gépek (VM-ek) beállítására.

::: moniker range=">=azs-1902"
## <a name="create-an-offer-1902-and-later"></a>Ajánlat létrehozása (1902 és újabb)

1. Jelentkezzen be a [Azure stack felügyeleti portálra](https://adminportal.local.azurestack.external) , és válassza a **+ erőforrás létrehozása**, majd a **+ csomagok**, majd az **ajánlat**lehetőséget.

   ![Ajánlat létrehozása](media/azure-stack-create-offer/offers.png)

2. Megjelenik egy Többlapos felhasználói felület, amely lehetővé teszi az ajánlat nevének megadását, illetve meglévő vagy új alapcsomagok és kiegészítő csomagok létrehozását. A legfontosabb, hogy a létrehozás előtt áttekintheti a létrehozott ajánlat részleteit.

   Az **alapvető beállítások** lapon adja meg a **megjelenítendő nevet** és az **erőforrás nevét**, majd az **erőforráscsoport**területen válassza az **új létrehozása** vagy a **meglévő használata**lehetőséget. A megjelenítendő név az ajánlat felhasználóbarát neve. Ez a felhasználóbarát név az egyetlen olyan ajánlat, amelyet a felhasználók a felhasználói portál ajánlatára való előfizetéskor látnak. Használjon olyan intuitív nevet, amely segít a felhasználóknak megérteni, hogy mit tartalmaz az ajánlat. Csak a rendszergazda láthatja az erőforrás nevét. Ezt a nevet használják a rendszergazdák az ajánlattal mint Azure Resource Manager-erőforrással végzett műveletekhez. Ezen a lapon azt is megteheti, hogy ezt az ajánlatot nyilvánosként vagy privátként szeretné megőrizni, ami az alapértelmezett. [Az ajánlat nyilvános vagy privát állapotát később is megváltoztathatja](#change-the-state-of-an-offer) .

   ![Új ajánlat](media/azure-stack-create-offer/new-offer.png)
  
3. Válassza ki az **alapcsomagok** lapot, **vagy kattintson a Tovább gombra: Alapcsomagok** > gomb. Válassza ki az ajánlatba felvenni kívánt csomag (oka) t.

   ![Csomag kiválasztása](media/azure-stack-create-offer/select-plan.png)

4. Ezen a ponton létrehozhat egy kiegészítő csomagot az alapcsomag módosításához, de ez nem kötelező. A következő cikkben egy kiegészítő csomagot fogunk létrehozni, [Azure stack kiegészítő](create-add-on-plan.md)csomagokat.

5. Válassza a **felülvizsgálat + létrehozás** lapot. Tekintse át az ajánlat összegzését, és győződjön meg arról, hogy az összes érték helyes. Az interfész lehetővé teszi, hogy a kiválasztott csomagokban lévő kvótákat egyenként kibontva megtekintse a csomagok egyes kvótáinak részleteit, és térjen vissza a szükséges módosítások elvégzéséhez.

6. Az ajánlat létrehozásához válassza a **Létrehozás** lehetőséget.

   ![Ellenőrzés és létrehozás](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>Ajánlat állapotának módosítása

Az ajánlat létrehozása után megváltoztathatja az állapotát. A felhasználóknak **közzé** kell tenniük a teljes nézetet, ha előfizetnek. Az ajánlatok a következőket tehetik:

- **Nyilvános**: A felhasználók számára látható.
- **Magánjellegű**: Csak a Felhőbeli rendszergazdák számára látható. Ez a beállítás akkor hasznos, ha a csomag vagy ajánlat tervezetét tervezi, vagy ha a felhő rendszergazdája szeretné [létrehozni az egyes előfizetéseket a felhasználók](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)számára.
- **Leszerelt**: Lezárva az új előfizetőknek. A felhő rendszergazdája leszerelheti az ajánlatokat a jövőbeli előfizetések megelőzésére, de a jelenlegi előfizetőket nem érinti.

  > [!TIP]  
  > Az ajánlat módosításai nem láthatók azonnal a felhasználó számára. A módosítások megtekintéséhez előfordulhat, hogy a felhasználóknak ki kell jelentkezniük, majd újra be kell jelentkezniük a felhasználói portálra az új ajánlat megtekintéséhez.

Az ajánlatok állapota kétféleképpen módosítható:

1. Az **összes erőforrás**területen válassza ki az ajánlat nevét. Az ajánlat **Áttekintés** képernyőjén válassza az **Állapot módosítása**lehetőséget. Válassza ki a használni kívánt állapotot (például **nyilvános**).

   ![Válassza ki az állapotot](media/azure-stack-create-offer/change-state.png)

2. Válassza az **ajánlati beállítások**lehetőséget. Válassza ki a használni kívánt állapotot (például **nyilvános**), majd válassza a **Mentés**lehetőséget.

   ![Kisegítő lehetőségek kiválasztása](media/azure-stack-create-offer/offer-settings.png)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-offer-1901-and-earlier"></a>Ajánlat létrehozása (1901 és korábbi verziók)

1. Jelentkezzen be a [Azure stack felügyeleti portálra](https://adminportal.local.azurestack.external) , és válassza a **+ erőforrás létrehozása**, majd a **bérlői ajánlatok + csomagok**lehetőséget, majd az **ajánlat**lehetőséget.

   ![Ajánlat létrehozása](media/azure-stack-create-offer/image01.png)
  
2. Az **új ajánlat**területen adja meg a **megjelenítendő nevet** és **az erőforrás nevét**, majd az **erőforráscsoport**területen válassza az **új létrehozása** vagy a **meglévő használata**lehetőséget. A megjelenítendő név az ajánlat felhasználóbarát neve. Ez a felhasználóbarát név az egyetlen olyan ajánlat, amelyet a felhasználók az ajánlatra való előfizetéskor látnak. Használjon olyan intuitív nevet, amely segít a felhasználóknak megérteni, hogy mit tartalmaz az ajánlat. Csak a rendszergazda láthatja az erőforrás nevét. Ezt a nevet használják a rendszergazdák az ajánlattal mint Azure Resource Manager-erőforrással végzett műveletekhez.

   ![Új ajánlat](media/azure-stack-create-offer/image01a.png)
  
3. A **terv**megnyitásához válassza az alapcsomagok lehetőséget. Válassza ki az ajánlatba felvenni kívánt terveket, majd válassza a **kiválasztás**lehetőséget. Az ajánlat létrehozásához válassza a **Létrehozás**lehetőséget.

   ![Csomag kiválasztása](media/azure-stack-create-offer/image02.png)
  
4. Az ajánlat létrehozása után megváltoztathatja az állapotát. A felhasználóknak **közzé** kell tenniük a teljes nézetet, ha előfizetnek. Az ajánlatok a következőket tehetik:

   - **Nyilvános**: A felhasználók számára látható.
   - **Magánjellegű**: Csak a Felhőbeli rendszergazdák számára látható. Ez a beállítás akkor hasznos, ha a csomag vagy ajánlat tervezetét tervezi, vagy ha a felhő rendszergazdája szeretné [létrehozni az egyes előfizetéseket a felhasználók](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)számára.
   - **Leszerelt**: Lezárva az új előfizetőknek. A felhő rendszergazdája leszerelheti az ajánlatokat a jövőbeli előfizetések megelőzésére, de a jelenlegi előfizetőket nem érinti.

   > [!TIP]  
   > Az ajánlat módosításai nem láthatók azonnal a felhasználó számára. A módosítások megtekintéséhez előfordulhat, hogy a felhasználóknak ki kell jelentkezniük, majd újra be kell jelentkezniük a felhasználói portálra az új ajánlat megtekintéséhez.

   Az ajánlat áttekintés képernyőjén válassza a **kisegítő lehetőségek állapota**lehetőséget. Válassza ki a használni kívánt állapotot (például **nyilvános**), majd válassza a **Mentés**lehetőséget.

     ![Válassza ki az állapotot](media/azure-stack-create-offer/change-stage-1807.png)

     Alternatív megoldásként válassza az **Állapot módosítása** lehetőséget, majd válasszon egy állapotot.

    ![Kisegítő lehetőségek kiválasztása](media/azure-stack-create-offer/change-stage-select-1807.png)

> [!NOTE]
> Az alapértelmezett ajánlatokat, terveket és kvótákat a PowerShell használatával is létrehozhatja. További információ: [Azure stack PowerShell-modul 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).
::: moniker-end

## <a name="next-steps"></a>További lépések

- [Előfizetések létrehozása](azure-stack-subscribe-plan-provision-vm.md)
- [Virtuális gép kiépítése](../user/azure-stack-create-vm-template.md)
