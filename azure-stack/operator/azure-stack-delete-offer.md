---
title: Kvóták, csomagok, ajánlatok és előfizetések törlése
description: Útmutató Azure Stack hub-kvóták,-csomagok,-ajánlatok és-előfizetések törléséhez.
author: bryanla
ms.topic: article
ms.date: 08/13/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 04/25/2019
ms.openlocfilehash: 3c8c8b424d6ce555b19f4a77dbb8275b731f488e
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836133"
---
# <a name="delete-quotas-plans-offers-and-subscriptions"></a>Kvóták, csomagok, ajánlatok és előfizetések törlése

Ez a cikk a már nem szükséges kvóták, csomagok, ajánlatok és előfizetések törlését ismerteti. Általános alapelv, hogy csak a jelenleg használatban lévőket lehet törölni. Az ajánlat törlése például csak akkor lehetséges, ha nincsenek az ajánlathoz tartozó előfizetések.

Az előfizetések kivételt képeznek az általános alapelv alól: törölheti az erőforrásokat tartalmazó előfizetéseket, és az erőforrásokat az előfizetéssel együtt törli a rendszer.

Ha törölni szeretné a kvótát, akkor a kvótát használó csomagokon és ajánlatokon át kell dolgoznia. Az ajánlatokkal kezdődően győződjön meg arról, hogy nincs előfizetése, törölje az egyes ajánlatokat, majd törölje a kvótát használó csomagokat, és így tovább.

## <a name="delete-a-subscription"></a>Előfizetés törlése

Ha törölni szeretne egy előfizetést, válassza a **minden szolgáltatás**, majd a **felhasználói előfizetések**lehetőséget a rendszeren lévő összes előfizetés listájának megjelenítéséhez. Ha egy ajánlaton dolgozik, akkor is kiválaszthatja az **előfizetéseket** .

A listából törölheti az előfizetéseket, vagy a PowerShell használatával írhat olyan parancsfájlt, amely törli az összes előfizetést. Ezek a parancsok az [előfizetések – törlési referenciában](/rest/api/azurestack/subscriptions/delete)vannak dokumentálva.

> [!CAUTION]
> Az előfizetés törlésével a benne lévő összes adatforrást és erőforrást is törli.

## <a name="delete-an-offer"></a>Ajánlat törlése

Ajánlat törléséhez a felügyeleti portálon lépjen a **minden szolgáltatás**, majd a **ajánlatok**elemre. Válassza ki a törölni kívánt ajánlatot, majd válassza a **Törlés**lehetőséget.

![Előfizetések törlése az Azure Stack hub felügyeleti portálján](media/azure-stack-delete-offer/delsub1.png)

Csak akkor törölhet ajánlatot, ha nincsenek használatban előfizetések. Ha előfizetések az ajánlat alapján léteznek, a **Törlés** lehetőség nem érhető el. Ebben az esetben tekintse meg az [előfizetés törlése](#delete-a-subscription) szakaszt.

## <a name="delete-a-plan"></a>Csomag törlése

Egy csomag törléséhez a felügyeleti portálon lépjen a **minden szolgáltatás**, majd a **csomagok**elemre. Válassza ki a törölni kívánt csomagot, majd válassza a **Törlés**lehetőséget.

![Csomagok törlése a Azure Stack hub felügyeleti portálján](media/azure-stack-delete-offer/delsub2.png)

Csak akkor törölheti a tervet, ha nem használ fel ajánlatokat vagy előfizetéseket. Ha van olyan ajánlat, amely a csomagot használja, törölje a csomagot, és engedélyezze, hogy a művelet meghiúsuljon, és hibaüzenet jelenik meg. A **fölérendelt ajánlatok** lehetőség kiválasztásával megjelenítheti a csomagot használó ajánlatok listáját. További információ az ajánlatok törléséről: [ajánlat törlése](#delete-an-offer).

Előfordulhat, hogy a csomagok közvetlenül egy előfizetéshez lettek hozzáadva kiegészítő csomagként, még akkor is, ha nem részei az ajánlatnak. Ebben az esetben a terv törlése előtt el kell távolítani azokat az előfizetéseket, amelyek azokat használják.

Emellett a csomag nem távolítható el az előfizetésből, ha az adott erőforráshoz tartozó egyetlen forrás az adott előfizetéshez. Ha például az A csomag hozzá lett adva az 1. előfizetéshez, és ez az egyetlen olyan csomag, amely hálózati kvótát biztosít az előfizetéshez, nem távolítható el az előfizetésből. Ezért nem törölhető.

## <a name="edit-and-delete-a-quota"></a>Kvóta szerkesztése és törlése

A meglévő kvótákat a felügyeleti portálon tekintheti meg és szerkesztheti: válassza a **régió felügyelete**lehetőséget, majd válassza ki a megfelelő erőforrás-szolgáltatót, majd válassza a **kvóták**lehetőséget. Bizonyos erőforrás-szolgáltatók kvótái is törölhetők.

![Kvóták törlése Azure Stack hub felügyeleti portálján](media/azure-stack-delete-offer/delsub3.png)

A REST API-kkal néhány kvótát is törölhet:

- [Compute](/rest/api/azurestack/quotas%20(compute)/delete)
- [Hálózat](/rest/api/azurestack/quotas%20(network)/delete)

> [!NOTE]
> A kvóta nem törölhető, ha vannak olyan aktuális csomagok, amelyek használják azt. Először törölnie kell a kvótára hivatkozó csomagot.

## <a name="next-steps"></a>További lépések

- [Előfizetések létrehozása](azure-stack-subscribe-plan-provision-vm.md)
- [Virtuális gép kiépítése](../user/azure-stack-create-vm-template.md)
