---
title: Kvóták, csomagok, ajánlatok és előfizetések törlése |} A Microsoft Docs
description: Ismerje meg az Azure Stack kvóták, csomagok, ajánlatok és előfizetések törlése.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: sethm
ms.reviewer: ''
ms.lastreviewed: 04/25/2019
ms.openlocfilehash: 5a15d55a053fbacd3a947bd61d7186d93be77dbf
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64987716"
---
# <a name="delete-quotas-plans-offers-and-subscriptions"></a>Kvóták, csomagok, ajánlatok és előfizetések törlése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ez a cikk ismerteti, hogyan lehet törölni a kvóták, csomagok, ajánlatok és előfizetések, amelyekre már nincs szüksége. Általános szabály törölheti csak mi nem lesz használatban. Például egy ajánlat törlése csak akkor lehetséges, ha nem tartoznak előfizetések, amelyek az ajánlat tartoznak.

Előfizetések a kivételt az általános szabály: törölheti; erőforrást tartalmazó előfizetés és az erőforrásokat pedig töröljük a-előfizetésével együtt.

Ezért, ha törölni szeretné a kvótát, kell dolgozik a csomagok és ajánlatok, amelyek adott kvóta keresztül visszairányító: kezdve az ajánlatok, győződjön meg arról, akkor nem rendelkezik előfizetéssel, törölje az egyes ajánlatok, majd törölje a terveket, a kvóta növeléséhez használja, és így tovább.

## <a name="delete-a-subscription"></a>Előfizetés törlése

Töröl egy előfizetést, válasszon **minden szolgáltatás**, majd **felhasználói előfizetések**, hogy a rendszer az összes előfizetések listájának megjelenítéséhez. Ha éppen dolgozik egy ajánlatot, Ön is megadhat **előfizetések** onnan.

Előfizetések törölheti a listából, vagy használhatja a Powershellt írni egy parancsfájlt, amely törli az összes előfizetés, részletes ismertetését lásd: a parancsokkal a [előfizetés - törlési referencia](/rest/api/azurestack/subscriptions/delete).

> [!CAUTION]
> Is egy előfizetés törlése töröl minden adatot és a benne található erőforrást.

## <a name="delete-an-offer"></a>Ajánlat törlése

Törölje az ajánlat, a felügyeleti portálon lépjen a **minden szolgáltatás**, majd **kínál**. Válassza ki a kívánt törölje, majd válassza ki az ajánlat **törlése**.

![delsub1](media/azure-stack-delete-offer/delsub1.png)

Nem tartoznak előfizetések használatával, amikor egy ajánlatot csak törölheti. Ha az előfizetések létezik az ajánlat alapján a **törlése** lehetőség szürkén jelenik meg. Ebben az esetben tekintse meg a [töröl egy előfizetést](#delete-a-subscription) szakaszban.

## <a name="delete-a-plan"></a>A csomag törlése

Törli a csomagot a felügyeleti portálon, lépjen a **minden szolgáltatás**, majd **csomagok**. Válassza ki a csomagot, törölje, majd válassza ki a kívánt **törlése**.

![delsub2](media/azure-stack-delete-offer/delsub2.png)

Csomagot csak törölni, ha nem ajánlatok vagy előfizetést használja. Ha az ajánlatok, amelyek a csomagot használja, a terv törlése, engedélyezze a sikertelen lesz, és egy hibaüzenetet fog kapni. Választhat **ajánlatok szülő** ajánlatok, amelyek a csomagot használja egy listájának megjelenítéséhez. Ajánlatok törlésével kapcsolatos további információkért lásd: [ajánlat törlése](#delete-an-offer).

Csomagok előfordulhat, hogy bővült közvetlenül egy előfizetésben találhatók kiegészítő csomagok, még akkor is, ha azok nem részei az ajánlatot. Ebben az esetben, el kell távolítani az előfizetést, a terv törlése előtt használja őket.

Is egy csomagot nem lehet eltávolítani egy előfizetésből Ha az adott előfizetéshez tartozó, egy adott erőforrás csak forrását. Például ha egy csomag az 1. előfizetéshez meg van adva, és a kezeléséről az előfizetés hálózati kvótát csak terv, azt nem lehet eltávolítani az előfizetésből. Ezért azt nem lehet törölni.

## <a name="edit-and-delete-a-quota"></a>Szerkeszthet és törölhet egy kvótát

Megtekintheti és szerkesztheti a felügyeleti portál használatával meglévő kvóták: válasszon **régiók kezelése**, majd válassza ki a megfelelő erőforrás-szolgáltatót, majd kattintson a **kvóták**. Kvóták bizonyos erőforrás-szolgáltatók is törölheti.

![delsub3](media/azure-stack-delete-offer/delsub3.png)

Azt is megteheti néhány kvóták REST API-k használatával törölheti:

- [Számítás](/rest/api/azurestack/quotas%20(compute)/delete)
- [Hálózat](/rest/api/azurestack/quotas%20(network)/delete)
- [Tárolás](/rest/api/azurestack/storagequotas/delete)

> [!NOTE]
> Kvóta nem törölhető, ha azokat a jelenlegi csomagokat, amelyek használják azt. Először törölnie kell a sémát, amely hivatkozik a kvótát.

## <a name="next-steps"></a>További lépések

- [Előfizetések létrehozása](azure-stack-subscribe-plan-provision-vm.md)
- [Virtuális gép kiépítése](../user/azure-stack-create-vm-template.md)