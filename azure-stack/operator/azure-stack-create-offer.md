---
title: Hozzon létre egy ajánlatot az Azure Stackben |} A Microsoft Docs
description: Felhő-rendszergazdák megtudhatja, hogyan hozhat létre egy ajánlatot, a felhasználók számára az Azure Stackben.
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
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 087c3b95f96600f6ff0cf14ab0fb8618046b86d7
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64302528"
---
# <a name="create-an-offer-in-azure-stack"></a>Ajánlat létrehozása az Azure Stackben

[Kínál](azure-stack-overview.md) csoportjai, egy vagy több olyan csomag, amelyet szolgáltatók felhasználóknak, amelyet a felhasználók is vásárolhat, illetve fizethetnek elő. Ez a cikk bemutatja, hogyan hozhat létre, amely tartalmazza az ajánlatot a [létrehozott csomagot](azure-stack-create-plan.md). Ez az ajánlat virtuális gépeken (VM) beállítása lehetővé teszi az előfizetők számára.

## <a name="create-an-offer-1902-and-later"></a>Hozzon létre egy ajánlatot (1902 és újabb verziók)

1. Jelentkezzen be a [Azure Stack rendszergazdai portál](https://adminportal.local.azurestack.external) válassza **+ erőforrás létrehozása**, majd **ajánlatok és csomagok**, majd **ajánlat**.

   ![Ajánlat létrehozása](media/azure-stack-create-offer/offers.png)

2. Lapokra osztott felhasználói felület jelenik meg, amely lehetővé teszi, hogy határozza meg az ajánlat nevét, és adja hozzá a meglévő, vagy hozzon létre új alapcsomagok és kiegészítő csomagok. Ennél is fontosabb áttekintheti a hoz létre, mielőtt hozna létre, az ajánlat részleteit.

   Az a **alapjai** lap **új ajánlat**, adjon meg egy **megjelenített neve** és a egy **erőforrásnév**, majd a **erőforrás Csoport**válassza **új létrehozása** vagy **meglévő**. A megjelenítendő név az ajánlat rövid nevét. Erre a rövid névre az ajánlat, amely megjelenik a felhasználók számára, amikor előfizetnek az ajánlatra, a felhasználói portálon csak információt. Használható, amely segít a felhasználóknak megérteni, hogy mit tartalmaz az ajánlat az intuitív név. Csak a rendszergazda láthatja az erőforrás neve. Ezt a nevet használják a rendszergazdák az ajánlattal mint Azure Resource Manager-erőforrással végzett műveletekhez. Ezen a lapon választhatja győződjön meg arról, ez az ajánlat nyilvános, vagy legyen privát, amely az alapértelmezett érték. Is [módosítsa az ajánlat nyilvános vagy privát állapotát](#change-the-state-of-an-offer) újabb, illetve.

   ![Új ajánlat](media/azure-stack-create-offer/new-offer.png)
  
3. Válassza ki a **Alapcsomagok** fülre. Válassza ki a kívánt foglalni az ajánlatba csomagokkal.

   ![Adatforgalmi díjcsomag kiválasztása](media/azure-stack-create-offer/select-plan.png)

4. Ezen a ponton létrehozhat egy kiegészítő csomag az alapszintű csomag módosítására, de ez nem kötelező. Létre fogunk hozni egy kiegészítő csomag a következő cikkben [Azure Stack kiegészítő csomagok](create-add-on-plan.md).

5. Válassza ki a **felülvizsgálat + létrehozása** fülre. Tekintse át az ajánlat-összefoglaló arról, hogy minden érték helyes-e. A felület lehetővé teszi, hogy a kvóták bontsa ki a kiválasztott csomagja, egyenként, a csomagban lévő minden egyes kvóta részleteinek megtekintéséhez, és lépjen vissza a szükséges módosításokat.

6. Válassza ki **létrehozás** az ajánlat létrehozásához.

   ![Áttekintés és létrehozás](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>Ajánlat állapotának módosítása

Miután létrehozta az ajánlatot, módosíthatja a állapotában. Ajánlatok kell tenni **nyilvános** a felhasználók számára a teljes képet kaphat, amikor előfizetnek. Ajánlatok a következők lehetnek:

- **Nyilvános**: Látható a felhasználók számára.
- **Privát**: Csak a rendszergazdák elhelyezhetik látható. Ez a beállítás akkor hasznos, a csomag vagy ajánlat tervezése közben, vagy ha a felhő rendszergazdája szeretné [hozzon létre minden egyes felhasználók számára előfizetést](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
- **Szerelni**: Lezárva az új előfizetők számára. A felhő rendszergazdájához, leszerelheti ajánlatok megelőzni a jövőbeli előfizetések, de a jelenlegi előfizetők változatlanul hagyja.

  > [!TIP]  
  > Az ajánlat módosítások nem láthatók azonnal a felhasználó számára. A módosítások megtekintéséhez jelentkezzen ki, majd jelentkezzen be újra a felhasználói portálra, tekintse meg az új ajánlat felhasználók előfordulhat, hogy kell.

Az ajánlat állapotának két módja van:

1. A **összes erőforrás**, válassza ki az ajánlat nevét. Az a **áttekintése** az ajánlatra, kattintson képernyő **Állapotváltozáshoz**. Válassza ki a használni kívánt állapot (például **nyilvános**).

   ![Válassza ki az állapot](media/azure-stack-create-offer/change-state.png)

2. A **összes erőforrás**, válassza ki az ajánlat nevét. Válassza ki **ajánlat beállításai**. Válassza ki a használni kívánt állapot (például **nyilvános**), majd **mentése**.

   ![Válassza ki a kisegítő lehetőségek állapota](media/azure-stack-create-offer/offer-settings.png)

## <a name="create-an-offer-1901-and-earlier"></a>Hozzon létre egy ajánlatot (1901 és korábbi verziók)

1. Jelentkezzen be a [Azure Stack rendszergazdai portál](https://adminportal.local.azurestack.external) válassza **+ erőforrás létrehozása**, majd **bérlői ajánlatok és csomagok**, majd **ajánlat**.

   ![Ajánlat létrehozása](media/azure-stack-create-offer/image01.png)
  
2. Alatt **új ajánlat**, adjon meg egy **megjelenített neve** és a egy **erőforrásnév**, majd a **erőforráscsoport**, jelölje be **létrehozása új** vagy **meglévő**. A megjelenítendő név az ajánlat rövid nevét. Erre a rövid névre, csak az ajánlat, amely a felhasználó számára látható előfizetés egy ajánlatra vonatkozó információk. Használható, amely segít a felhasználóknak megérteni, hogy mit tartalmaz az ajánlat az intuitív név. Csak a rendszergazda láthatja az erőforrás neve. Ezt a nevet használják a rendszergazdák az ajánlattal mint Azure Resource Manager-erőforrással végzett műveletekhez.

   ![Új ajánlat](media/azure-stack-create-offer/image01a.png)
  
3. Válassza ki **Alapcsomagok** megnyitásához a **megtervezése**. Válassza ki a szeretne foglalni az ajánlatba, és válassza a csomagokat **kiválasztása**. Válassza a ajánlat létrehozása **létrehozás**.

   ![Adatforgalmi díjcsomag kiválasztása](media/azure-stack-create-offer/image02.png)
  
4. Miután létrehozta az ajánlatot, módosíthatja a állapotában. Ajánlatok kell tenni **nyilvános** a felhasználók számára a teljes képet kaphat, amikor előfizetnek. Ajánlatok a következők lehetnek:

   - **Nyilvános**: Látható a felhasználók számára.
   - **Privát**: Csak a rendszergazdák elhelyezhetik látható. Ez a beállítás akkor hasznos, a csomag vagy ajánlat tervezése közben, vagy ha a felhő rendszergazdája szeretné [hozzon létre minden egyes felhasználók számára előfizetést](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Szerelni**: Lezárva az új előfizetők számára. A felhő rendszergazdájához, leszerelheti ajánlatok megelőzni a jövőbeli előfizetések, de a jelenlegi előfizetők változatlanul hagyja.

   > [!TIP]  
   > Az ajánlat módosítások nem láthatók azonnal a felhasználó számára. A módosítások megtekintéséhez jelentkezzen ki, majd jelentkezzen be újra a felhasználói portálra, tekintse meg az új ajánlat felhasználók előfordulhat, hogy kell.

   Az ajánlat áttekintése képernyőn válassza ki a **kisegítő lehetőségek állapota**. Válassza ki a használni kívánt állapot (például **nyilvános**), majd válassza ki **mentése**.

     ![Válassza ki az állapot](media/azure-stack-create-offer/change-stage-1807.png)

     Alternatív megoldásként válassza **Állapotváltozáshoz** majd állapotba.

    ![Válassza ki a kisegítő lehetőségek állapota](media/azure-stack-create-offer/change-stage-select-1807.png)

> [!NOTE]
> PowerShell használatával alapértelmezett ajánlatok, tervek és kvóták létrehozása. További információkért lásd: [Azure Stack PowerShell-modul 1.4.0-s](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).

## <a name="next-steps"></a>További lépések

- [Előfizetések létrehozása](azure-stack-subscribe-plan-provision-vm.md)
- [Virtuális gép kiépítése](../user/azure-stack-create-vm-template.md)
