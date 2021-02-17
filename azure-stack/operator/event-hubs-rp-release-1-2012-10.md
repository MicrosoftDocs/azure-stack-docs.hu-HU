---
title: Event Hubs Azure Stack hub 1.2012.1.0 kibocsátási megjegyzései
description: Ismerkedjen meg a Azure Stack hub Event Hubs 1.2012.1.0-kiadásával, beleértve a hibajavításokat és szolgáltatásokat, valamint a frissítés telepítését.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: article
ms.date: 02/15/2021
ms.reviewer: kalkeea
ms.lastreviewed: 02/15/2021
ms.openlocfilehash: ef28447b87c0647b65c996112c5739bf7c6caccf
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100563501"
---
# <a name="event-hubs-on-azure-stack-hub-1201210-release-notes"></a>Event Hubs Azure Stack hub 1.2012.1.0 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések a Event Hubs Azure Stack hub-verzió 1.2012.1.0 és bármely ismert problémával kapcsolatos javításait és javításait ismertetik. 

[!INCLUDE [Azure Stack Hub update reminder](../includes/event-hubs-hub-update-banner.md)]

## <a name="updates-in-this-release"></a>Frissítések ebben a kiadásban

Ez a kiadás a következő frissítéseket tartalmazza:

- Azure Portal SDK-fejlesztők számára mostantól támogatott a portál 5.0.303.7361 verziója.
- Event Hubs-fürtök belső naplózásának fejlesztése.

## <a name="issues-fixed-in-this-release"></a>Ebben a kiadásban rögzített problémák

Ez a kiadás a következő javításokat tartalmazza:

- Event Hubs-fürtök frissítési sorrendjének javítása a frissítési probléma megoldásához.
- Event Hubs fürtökhöz tartozó állapot-és biztonsági mentési állapot ellenőrzése nem futott, ha a fürtök "verziófrissítés" vagy "frissítés sikertelen" állapotban voltak. A probléma megoldódott ebben a kiadásban.
- Kijavítva egy hiba, ami a használati rekordokat nem megfelelő mennyiség tárolására okozza. A magok helyett kapacitási egységeket (CU) adtak ki. Korábban egy 1CU-fürt 1 mag-t mutat az óránkénti használat során. A felhasználók mostantól a megfelelő mennyiségű 10 magot láthatják egy 1 CU-fürthöz az óránkénti használat során.

## <a name="known-issues"></a>Ismert problémák 

Ebben a kiadásban nincsenek ismert problémák.

## <a name="next-steps"></a>Következő lépések

- További információkért [tekintse meg a Event Hubs Azure stack hub operátor áttekintése](event-hubs-rp-overview.md)című témakört.

