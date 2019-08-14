---
title: Kvóták, csomagok, ajánlatok és előfizetések törlése | Microsoft Docs
description: További információ a Azure Stack kvóták, csomagok, ajánlatok és előfizetések törléséről.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 04/25/2019
ms.openlocfilehash: fca26723c062dd9f5155030fb11c8e1d695bf5f1
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991808"
---
# <a name="delete-quotas-plans-offers-and-subscriptions"></a>Kvóták, csomagok, ajánlatok és előfizetések törlése

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ez a cikk a már nem szükséges kvóták, csomagok, ajánlatok és előfizetések törlését ismerteti. Általános alapelv, hogy csak a nem használt elemek törölhetők. Az ajánlat törlése például csak akkor lehetséges, ha nincsenek az ajánlathoz tartozó előfizetések.

Az előfizetések kivételt képeznek az általános alapelv alól: törölheti az erőforrásokat tartalmazó előfizetéseket. a rendszer törli az erőforrásokat az előfizetéssel együtt.

Ezért, ha törölni szeretné a kvótát, minden olyan csomagon és ajánlaton át kell dolgoznia, amely ezt a kvótát használja: az ajánlatokkal kezdődően győződjön meg arról, hogy nincs előfizetése, törölje az egyes ajánlatokat, majd törölje a kvótát használó csomagokat, és így tovább.

## <a name="delete-a-subscription"></a>Előfizetés törlése

Ha törölni szeretne egy előfizetést, válassza a **minden szolgáltatás**, majd a **felhasználói**előfizetések lehetőséget a rendszeren lévő összes előfizetés listájának megjelenítéséhez. Ha egy ajánlaton dolgozik, itt is kiválaszthatja az előfizetéseket.

A listából törölheti az előfizetéseket, vagy a PowerShell használatával írhat egy olyan parancsfájlt, amely törli az összes előfizetést, az előfizetések [– törlési hivatkozás](/rest/api/azurestack/subscriptions/delete)dokumentációjában ismertetett parancsokkal.

> [!CAUTION]
> Az előfizetés törlésével a benne lévő összes adatforrást és erőforrást is törli.

## <a name="delete-an-offer"></a>Ajánlat törlése

Ajánlat törléséhez a felügyeleti portálon lépjen a **minden szolgáltatás**, majd a **ajánlatok**elemre. Válassza ki a törölni kívánt ajánlatot, majd válassza a **Törlés**lehetőséget.

![delsub1](media/azure-stack-delete-offer/delsub1.png)

Csak akkor törölhet ajánlatot, ha nincsenek használatban előfizetések. Ha előfizetések az ajánlat alapján léteznek, a **Törlés** lehetőség nem érhető el. Ebben az esetben tekintse meg az [előfizetés törlése](#delete-a-subscription) szakaszt.

## <a name="delete-a-plan"></a>Csomag törlése

Egy csomag törléséhez a felügyeleti portálon lépjen a **minden szolgáltatás**, majd a **csomagok**elemre. Válassza ki a törölni kívánt csomagot, majd válassza a **Törlés**lehetőséget.

![delsub2](media/azure-stack-delete-offer/delsub2.png)

Csak akkor törölheti a tervet, ha nem használ fel ajánlatokat vagy előfizetéseket. Ha van olyan ajánlat, amely a csomagot használja, törölje a csomagot, és engedélyezze, hogy a művelet meghiúsuljon, és hibaüzenetet kap. A **fölérendelt ajánlatok** lehetőség kiválasztásával megjelenítheti a csomagot használó ajánlatok listáját. További információ az ajánlatok törléséről: [ajánlat törlése](#delete-an-offer).

Lehetséges, hogy a csomagok közvetlenül egy előfizetéshez lettek hozzáadva kiegészítő csomagként, még akkor is, ha nem részei az ajánlatnak. Ebben az esetben a terv törlése előtt el kell távolítani azokat az előfizetéseket, amelyek azokat használják.

Emellett a csomag nem távolítható el az előfizetésből, ha az adott erőforráshoz tartozó egyetlen forrás az adott előfizetéshez. Ha például az A csomag hozzá lett adva az 1. előfizetéshez, és az egyetlen olyan csomag, amely hálózati kvótát biztosít az előfizetéshez, akkor nem távolítható el az előfizetésből. Ezért nem törölhető.

## <a name="edit-and-delete-a-quota"></a>Kvóta szerkesztése és törlése

A meglévő kvótákat a felügyeleti portálon tekintheti meg és szerkesztheti: válassza a **régió felügyelete**lehetőséget, majd válassza ki a megfelelőerőforrás-szolgáltatót, és kattintson a kvóták elemre. Bizonyos erőforrás-szolgáltatók kvótái is törölhetők.

![delsub3](media/azure-stack-delete-offer/delsub3.png)

Emellett a REST API-kkal is törölhet néhány kvótát:

- [Számítás](/rest/api/azurestack/quotas%20(compute)/delete)
- [Hálózat](/rest/api/azurestack/quotas%20(network)/delete)
- [Storage](/rest/api/azurestack/storagequotas/delete)

> [!NOTE]
> A kvóta nem törölhető, ha vannak olyan aktuális csomagok, amelyek használják azt. Először törölnie kell a kvótára hivatkozó csomagot.

## <a name="next-steps"></a>További lépések

- [Előfizetések létrehozása](azure-stack-subscribe-plan-provision-vm.md)
- [Virtuális gép kiépítése](../user/azure-stack-create-vm-template.md)