---
title: Event Hubs kezelése Azure Stack hub-on
description: Megtudhatja, hogyan kezelheti a Event Hubs erőforrás-szolgáltatót az Azure Stack hub-on.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 1d83ef1d43dbe974ead7a5d80bb42939a311af7a
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255792"
---
# <a name="how-to-manage-event-hubs-on-azure-stack-hub"></a>Event Hubs kezelése Azure Stack hub-on

A Event Hubs felügyeleti felület lehetővé teszi a szolgáltatás vezérlését, valamint az állapot és a riasztások megjelenítését. 

## <a name="overview"></a>Áttekintés

A következő lépésekkel érheti el a Event Hubs felügyeleti lapját:

1. Jelentkezzen be az Azure Stack Hub felügyeleti portálra.
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán.
3. Keressen rá a "Event Hubs" kifejezésre, és válassza ki a szolgáltatást. Ha nem találja a Event Hubs szolgáltatást, először [telepítenie](event-hubs-rp-install.md)kell az erőforrás-szolgáltatót.
4. Megjelenik a Event Hubs-kezelés áttekintő lapja. A bal oldali panelen négy szakaszt talál:
   - **Áttekintés**: általános nézet és hozzáférés az adott felügyeleti területekhez.
   - **Riasztások**: megjeleníti a Event Hubs összes kritikus és figyelmeztető riasztását. A részletekért tekintse meg a [riasztások szakaszt](#alerts) .
   - **Kvóták**: lehetővé teszi kvóták létrehozását, frissítését és törlését. A részletekért tekintse meg a [kvóták szakaszt](#quotas) .
   - **Event Hubs fürtök**: az összes konfigurált fürt listáját jeleníti meg. A részletekért tekintse meg a [Event Hubs-fürtök](#event-hubs-clusters) szakaszt.

   [![Event hubok kezelése](media/event-hubs-rp-manage/1-manage-event-hubs.png)](media/event-hubs-rp-manage/1-manage-event-hubs.png#lightbox)

## <a name="quotas"></a>Kvóták

A **kvóták** kiválasztása a főoldalon megjeleníti a használatban lévő kvóták listáját, beleértve a kvótákat meghatározó társított csomagokat is. 
 
[![Event hub-kvóták kezelése](media/event-hubs-rp-manage/3-quotas.png)](media/event-hubs-rp-manage/3-quotas.png#lightbox)

További információ a Event Hubshoz megadott kvóták típusairól: [kvóta típusai](azure-stack-quota-types.md#event-hubs-quota-types)

## <a name="alerts"></a>Riasztások

A Event Hubs erőforrás-szolgáltató a következő riasztásokat támogatja:
   
| Kategória | Riasztás | Típus | Feltétel |
|----------|-------|------|-----------|
| Teljesítmény | | | |
| | EventHub-CpuUsage | Figyelmeztetés | Event Hubs fürt átlagos CPU-használata az elmúlt 6 órában nagyobb, mint 50%. |
| | EventHub-MemoryUsage | Figyelmeztetés | A (z)% adatlemez átlaga (E:) Event Hubs-fürt használata az elmúlt 6 órában nagyobb, mint 50%. |
| | EventHub-DiskUsage | Figyelmeztetés | A Event Hubs-fürt utolsó 6 órában mért szabad lemezterületének átlaga 50%-nál kisebb. |
| Használat/kvóta | | | |
| | EventHub-QuotaExceeded | Figyelmeztetés | A kvóta túllépte az elmúlt hat órában történt hibát. |
| | EventHub-NamespaceCreditUsage | Figyelmeztetés | Az elmúlt hat órában a névtérbeli kreditek használatának összege nagyobb, mint 10000,0. |
| Csökkentett teljesítményű szolgáltatás | | | |
| | EventHub-InternalServerError | Figyelmeztetés | Belső kiszolgálóhiba történt az elmúlt hat órában. |
| | EventHub-ServerBusy | Figyelmeztetés | A kiszolgáló foglalt hiba történt az elmúlt hat órában. |
| Ügyfél | | | |
| | EventHub-ClientError | Figyelmeztetés | Ügyfél-hiba történt az elmúlt hat órában. |
| Erőforrás | | | |
| | EventHub-PendingDeletingResources | Figyelmeztetés | Az elmúlt hat órában az erőforrások függőben lévő törlésének összege nagyobb, mint 100. |
| | EventHub-ProvisioningQueueLength | Figyelmeztetés | Az utolsó hat órában a kiépítési várólista átlagos hosszának átlaga nagyobb, mint 30. |

A **riasztások** kiválasztása a főoldalon a kiállított riasztások listáját jeleníti meg:

[![Event hubok kezelése – riasztások összegzése](media/event-hubs-rp-manage/2-alerts-summary.png)](media/event-hubs-rp-manage/2-alerts-summary.png#lightbox)

Ha kijelöl egy riasztást a listából, a jobb oldalon a **riasztás részletei** panel jelenik meg:

[![Event hubok kezelése – riasztások részletei](media/event-hubs-rp-manage/2-alerts-detail.png)](media/event-hubs-rp-manage/2-alerts-detail.png#lightbox)

További információ a Azure Stack hub figyelési képességeiről, beleértve a riasztásokat is: az [állapot és a riasztások figyelése](azure-stack-monitor-health.md). A naplók gyűjtésével kapcsolatos részletekért lásd: [Azure stack diagnosztikai naplók áttekintése](./diagnostic-log-collection.md).

## <a name="event-hubs-clusters"></a>Fürtök Event Hubs

**Event Hubs fürtök** kiválasztása a főlapon az elérhető felhasználói fürtök listáját jeleníti meg. A lista a következőket tartalmazza minden egyes fürthöz:

- Magas szintű konfigurációs információk.
- Szolgáltatás állapota.
- Biztonsági mentés állapota.

[![Event hubok kezelése – felhasználói erőforrások](media/event-hubs-rp-manage/4-user-resources.png)](media/event-hubs-rp-manage/4-user-resources.png#lightbox)

Az **állapot** vagy a **biztonsági mentés** területen található hivatkozásra kattintva részletes információkat jeleníthet meg Event Hubs állapotáról és a biztonsági mentés állapotáról. A **név** alatti hivatkozás a fürt további részleteit jeleníti meg, beleértve a következőket:
- Állapot-és konfigurációs információk.
- A fürt szolgáltatási korlátainak listája.

[![Event hubok kezelése – felhasználói fürtök](media/event-hubs-rp-manage/4-user-clusters.png)](media/event-hubs-rp-manage/4-user-clusters.png#lightbox)

A szolgáltatási korlátok a Event Hubs működési határait meghatározó konfigurációs paraméterek. Az elérhető szolgáltatási korlátozások hasonlóak az Azure dedikált Event Hubs számára kínált szolgáltatásokhoz. A **konfigurációs érték** alatti hivatkozás (ok) kiválasztásával módosíthatja a hozzárendelt értéket.

> [!IMPORTANT]
> A szolgáltatási korlátok módosítása előtt időt kell fordítania a teljes hatás elemzésére. A szolgáltatási korlát módosítása hatással lehet a megoldás viselkedésére, amely az eseményeket használja és állítja elő. A módosítások a Azure Stack kapacitásának erőforrás-felhasználását is befolyásolhatják.

## <a name="next-steps"></a>További lépések

További információ a következőről:

- A Event Hubshoz megadott kvóta-típusok a [kvóták típusainál](azure-stack-quota-types.md#event-hubs-quota-types)tekinthetők meg.
- Azure Stack hub figyelési képességei, beleértve a riasztásokat, lásd: [állapotok és riasztások figyelése](azure-stack-monitor-health.md). 
- Azure Stack hub-naplófájlok gyűjteménye: [Azure stack diagnosztikai naplók áttekintése](./diagnostic-log-collection.md).