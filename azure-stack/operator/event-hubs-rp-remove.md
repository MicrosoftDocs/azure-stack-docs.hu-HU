---
title: Event Hubs eltávolítása Azure Stack hub-on
description: Ismerje meg, hogyan távolíthatja el a Event Hubs erőforrás-szolgáltatót az Azure Stack hub-on.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 2a1525f4b91998479d368714aa3a88df6ecfcef9
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423959"
---
# <a name="how-to-remove-event-hubs-on-azure-stack-hub"></a>Event Hubs eltávolítása Azure Stack hub-on

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

> [!WARNING]
> Az Event Hubs eltávolítása eltávolítja az erőforrás-szolgáltatót, valamint az összes felhasználó által létrehozott Event Hubs-fürtöt, névteret és Event hub-erőforrást. Emellett eltávolítja a hozzájuk tartozó esemény-adataikat is.  
> A Event Hubs eltávolításának eldöntése előtt folytassa a rendkívüli óvatossággal. Event Hubs eltávolítása **nem** törli a Event Hubs telepítéséhez használt csomagokat. Ennek érdekében tekintse meg [Event Hubs csomagok törlését](#delete-event-hubs-packages)ismertető témakört.

## <a name="uninstall-event-hubs"></a>Event Hubs eltávolítása

A lépések ezen szakasza eltávolítja az összes Event Hubs erőforrást, beleértve a fürtöket, a névtereket, az Event hubokat és az erőforrás-szolgáltatót is.

A Event Hubs és a felhasználók által létrehozott összes kapcsolódó erőforrás eltávolításához hajtsa végre a következő lépéseket:

1. Jelentkezzen be az Azure Stack hub felügyeleti portálján.
2. Válassza a **piactér-kezelés** lehetőséget a bal oldalon.
3. Válassza ki az **Erőforrás-szolgáltatók** elemet.
4. Válassza a **Event Hubs** elemet az erőforrás-szolgáltatók listájából. Érdemes lehet szűrni a listát a megadott keresési szövegmezőben a "Event Hubs" érték beírásával.

   [az Event hubok 1 ![eltávolítása](media/event-hubs-rp-remove/1-uninstall.png)](media/event-hubs-rp-remove/1-uninstall.png#lightbox)

5. A lap tetején található beállítások közül válassza az **Eltávolítás** lehetőséget.

   [az Event hubok 2 eltávolítása ![](media/event-hubs-rp-remove/2-uninstall.png)](media/event-hubs-rp-remove/2-uninstall.png#lightbox)

6. Adja meg az erőforrás-szolgáltató nevét, majd válassza az **Eltávolítás**lehetőséget. Ez a művelet megerősíti az eltávolításának szándékát:
   - A Event Hubs erőforrás-szolgáltató
   - Minden felhasználó által létrehozott fürt, névtér, Event hub és Event-érték.

   [az Event hubok 3 ![eltávolítása](media/event-hubs-rp-remove/3-uninstall.png)](media/event-hubs-rp-remove/3-uninstall.png#lightbox)

   [az Event hub 4 eltávolítása ![](media/event-hubs-rp-remove/4-uninstall.png)](media/event-hubs-rp-remove/4-uninstall.png#lightbox)

   > [!IMPORTANT]
   > A Event Hubs újbóli telepítése előtt legalább 10 perccel a Event Hubs eltávolítása után várnia kell. Ennek oka az lehet, hogy a törlési tevékenységek továbbra is futnak, ami ütközik bármely új telepítéssel.

## <a name="delete-event-hubs-packages"></a>Event Hubs csomagok törlése

Akkor használja ezt a beállítást, ha a Event Hubs eltávolítása után el szeretné távolítani a Event Hubs telepítéséhez használt csomagokat is. 

## <a name="next-steps"></a>Következő lépések

Az újratelepítéshez térjen vissza a [Event Hubs erőforrás-szolgáltató telepítése](event-hubs-rp-install.md) című cikkre.