---
title: Ajánlat létrehozása Azure Stack hub-ban
description: Megtudhatja, hogyan hozhat létre ajánlatot a felhasználók számára Azure Stack hub-ban.
author: bryanla
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 8371b23da292400087def86fc6a18bbee90a0217
ms.sourcegitcommit: 53b0dde60a6435936a5e0cb9e931245f262d637a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91107061"
---
# <a name="create-an-offer-in-azure-stack-hub"></a>Ajánlat létrehozása Azure Stack hub-ban

[Az ajánlatok](azure-stack-overview.md) egy vagy több csomagból álló csomagcsoportok, amelyeket a szolgáltatók nyújtanak a felhasználóknak, amelyeket azok megvásárolhatnak vagy rájuk előfizethetnek. Ez a cikk ismerteti egy olyan ajánlat létrehozásának módját, amely tartalmazza a [létrehozott csomagot](azure-stack-create-plan.md). Ez az ajánlat lehetőséget ad az előfizetők számára virtuális gépek (VM-ek) beállítására.

::: moniker range=">=azs-1902"
## <a name="create-an-offer-1902-and-later"></a>Ajánlat létrehozása (1902 és újabb)

1. Jelentkezzen be az Azure Stack hub felügyeleti portálra, `https://adminportal.local.azurestack.external` és válassza az **+ erőforrás létrehozása**, majd a **+ csomagok**, majd az **ajánlat**lehetőséget.

   ![Képernyőkép, amely bemutatja, hogyan hozhat létre ajánlatot Azure Stack hub-ban.](media/azure-stack-create-offer/offers.png)

2. Megjelenik egy Többlapos felhasználói felület, amely lehetővé teszi az ajánlat nevének megadását. Hozzáadhat meglévőket is, vagy létrehozhat új alapcsomagokat és kiegészítő csomagokat. A legfontosabb, hogy a létrehozás előtt tekintse át az Ön által létrehozott ajánlat részleteit.

   Az **alapvető beállítások** lapon adja meg a **megjelenítendő nevet** és az **erőforrás nevét**, majd az **erőforráscsoport**területen válassza az **új létrehozása** vagy a **meglévő használata**lehetőséget. A megjelenítendő név az ajánlat felhasználóbarát neve. Ez a felhasználóbarát név az egyetlen olyan ajánlat, amelyet a felhasználók a felhasználói portál ajánlatára való előfizetéskor látnak. Használjon olyan intuitív nevet, amely segít a felhasználóknak megérteni, hogy mit tartalmaz az ajánlat. Csak a rendszergazda láthatja az erőforrás nevét. Ezt a nevet használják a rendszergazdák az ajánlattal mint Azure Resource Manager-erőforrással végzett műveletekhez. Ezen a lapon azt is megteheti, hogy ezt az ajánlatot nyilvánosként vagy privátként szeretné megtartani. Az alapértelmezett beállítás a privát. [Az ajánlat nyilvános vagy privát állapotát bármikor módosíthatja](#change-the-state-of-an-offer) .

   ![Képernyőfelvétel: a Azure Stack hub új ajánlatával kapcsolatos alapvető információk hozzáadása.](media/azure-stack-create-offer/new-offer.png)
  
3. Válassza az **alapcsomagok** fület, vagy kattintson a **Tovább gombra: alapcsomagok >** gombra. Válassza ki az ajánlatba felvenni kívánt csomag (oka) t.

   ![Képernyőkép, amely bemutatja, hogyan választhatja ki a Azure Stack hub-ajánlatban szerepeltetni kívánt csomagot.](media/azure-stack-create-offer/select-plan.png)

4. Ezen a ponton létrehozhat egy kiegészítő csomagot az alapcsomag módosításához, de ez nem kötelező. Lehetőség van arra, hogy kiegészítő csomagot hozzon létre a következő cikkben, [Azure stack hub-bővítményi terveket](create-add-on-plan.md).

5. Válassza a **felülvizsgálat + létrehozás** lapot. Tekintse át az ajánlat összegzését, és győződjön meg arról, hogy az összes érték helyes. Az interfész lehetővé teszi, hogy a kiválasztott csomagok kvótái kibontásával egy adott csomag egyes kvótáinak részleteit megtekinthesse. Visszatérhet a szükséges módosítások elvégzéséhez is.

6. Az ajánlat létrehozásához válassza a **Létrehozás** lehetőséget.

   ![Ajánlat áttekintése és létrehozása Azure Stack központban](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>Ajánlat állapotának módosítása

Az ajánlat létrehozása után megváltoztathatja az állapotát. A felhasználóknak **közzé** kell tenniük a teljes nézetet, ha előfizetnek. Az ajánlatok a következőket tehetik:

- **Nyilvános**: látható a felhasználók számára.
- **Magánjellegű**: csak a Felhőbeli rendszergazdák láthatják. Ez a beállítás akkor hasznos, ha a csomag vagy ajánlat tervezetét tervezi, vagy ha a felhő rendszergazdája szeretné [létrehozni az egyes előfizetéseket a felhasználók](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)számára.
- **Leszerelve:** Új előfizetők már nem választhatják. A felhő rendszergazdája leszerelheti az ajánlatokat a jövőbeli előfizetések megelőzésére, de a jelenlegi előfizetőket nem érinti.

  > [!TIP]  
  > Az ajánlat módosításai nem láthatók azonnal a felhasználó számára. A módosítások megtekintéséhez előfordulhat, hogy a felhasználóknak ki kell jelentkezniük, majd újra be kell jelentkezniük a felhasználói portálra az új ajánlat megtekintéséhez.

Az ajánlatok állapota kétféleképpen módosítható:

1. Az **összes erőforrás**területen válassza ki az ajánlat nevét. Az ajánlat **Áttekintés** képernyőjén válassza az **Állapot módosítása**lehetőséget. Válassza ki a használni kívánt állapotot (például **nyilvános**).

   ![A Azure Stack hub-ajánlat állapotának módosítását bemutató képernyőkép.](media/azure-stack-create-offer/change-state.png)

2. Válassza az **ajánlati beállítások**lehetőséget. Válassza ki a használni kívánt állapotot (például **nyilvános**), majd válassza a **Mentés**lehetőséget.

   ![Azure Stack hub-ajánlat beállításai](media/azure-stack-create-offer/offer-settings.png)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-offer-1901-and-earlier"></a>Ajánlat létrehozása (1901 és korábbi verziók)

1. Jelentkezzen be az Azure Stack hub felügyeleti portálra, `https://adminportal.local.azurestack.external` és válassza az **+ erőforrás létrehozása**, majd a **bérlői ajánlatok + csomagok**lehetőséget, majd az **ajánlatot**.

   ![Képernyőkép, amely bemutatja, hogyan hozhat létre ajánlatot.](media/azure-stack-create-offer/image01.png)
  
2. Az **új ajánlat**területen adja meg a **megjelenítendő nevet** és **az erőforrás nevét**, majd az **erőforráscsoport**területen válassza az **új létrehozása** vagy a **meglévő használata**lehetőséget. A megjelenítendő név az ajánlat felhasználóbarát neve. Ez a felhasználóbarát név az egyetlen olyan ajánlat, amelyet a felhasználók az ajánlatra való előfizetéskor látnak. Használjon olyan intuitív nevet, amely segít a felhasználóknak megérteni, hogy mit tartalmaz az ajánlat. Csak a rendszergazda láthatja az erőforrás nevét. Ezt a nevet használják a rendszergazdák az ajánlattal mint Azure Resource Manager-erőforrással végzett műveletekhez.

   ![Képernyőkép, amely bemutatja, hogyan hozhat létre egy erőforrást az új ajánlathoz Azure Stack hub-ban.](media/azure-stack-create-offer/image01a.png)
  
3. A **terv**megnyitásához válassza az **alapcsomagok** lehetőséget. Válassza ki az ajánlatba felvenni kívánt terveket, majd válassza a **kiválasztás**lehetőséget. Az ajánlat létrehozásához válassza a **Létrehozás**lehetőséget.

   ![Képernyőkép, amely bemutatja, hogyan választható ki a Azure Stack hub-ajánlatban szerepeltetni kívánt csomag.](media/azure-stack-create-offer/image02.png)
  
4. Az ajánlat létrehozása után megváltoztathatja az állapotát. A felhasználóknak **közzé** kell tenniük a teljes nézetet, ha előfizetnek. Az ajánlatok a következőket tehetik:

   - **Nyilvános**: látható a felhasználók számára.
   - **Magánjellegű**: csak a Felhőbeli rendszergazdák láthatják. Ez a beállítás akkor hasznos, ha a csomag vagy ajánlat tervezetét tervezi, vagy ha a felhő rendszergazdája szeretné [létrehozni az egyes előfizetéseket a felhasználók](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)számára.
   - **Leszerelve:** Új előfizetők már nem választhatják. A felhő rendszergazdája leszerelheti az ajánlatokat a jövőbeli előfizetések megelőzésére, de a jelenlegi előfizetőket nem érinti.

   > [!TIP]  
   > Az ajánlat módosításai nem láthatók azonnal a felhasználó számára. A módosítások megtekintéséhez előfordulhat, hogy a felhasználóknak ki kell jelentkezniük, majd újra be kell jelentkezniük a felhasználói portálra az új ajánlat megtekintéséhez.

   Az ajánlat áttekintés képernyőjén válassza a **kisegítő lehetőségek állapota**lehetőséget. Válassza ki a használni kívánt állapotot (például **nyilvános**), majd válassza a **Mentés**lehetőséget.

     ![A Azure Stack hub-ajánlat állapotának módosítását bemutató képernyőkép.](media/azure-stack-create-offer/change-stage-1807.png)

     Alternatív megoldásként válassza az **Állapot módosítása** lehetőséget, majd válasszon egy állapotot.

    ![Válassza ki az Azure Stack hub-ajánlat kisegítő lehetőségek állapotát](media/azure-stack-create-offer/change-stage-select-1807.png)

> [!NOTE]
> Az alapértelmezett ajánlatokat, terveket és kvótákat a PowerShell használatával is létrehozhatja. További információ: [Azure stack hub PowerShell-modul 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).
::: moniker-end

## <a name="next-steps"></a>Következő lépések

- Ha szeretné megtudni, hogyan módosíthat egy ajánlatot, és hogyan adhat hozzá felhasználókat egy kiegészítő csomaggal, folytassa a [kiegészítő csomag létrehozásával](create-add-on-plan.md) (opcionális).
- Ellenkező esetben ugorjon az [ajánlatra való előfizetésre](azure-stack-subscribe-plan-provision-vm.md)