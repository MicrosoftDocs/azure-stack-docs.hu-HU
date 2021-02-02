---
title: Azure Stack hub megfigyelési adatainak felhasználása
description: További információ a Azure Stack hub megfigyelési adatainak felhasználására vonatkozó lehetőségekről.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: b4398423fa50c0c9ff90cd9d9c73a48760791a8a
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247625"
---
# <a name="consume-monitoring-data-from-azure-stack-hub"></a>Azure Stack hub megfigyelési adatainak felhasználása

A figyelési adatgyűjtés egyetlen helyen található a Azure Monitor folyamattal, akárcsak Azure Monitor a globális Azure-ban. A globális Azure-ban található összes figyelési érték azonban nem érhető el Azure Stack központban. Ebben a cikkben összefoglalást adunk a Azure Stack hub monitorozási adatainak felhasználásának különböző módjairól.
 
## <a name="options-for-data-consumption"></a>Adatfelhasználási beállítások

| Adattípus | Kategória | Támogatott szolgáltatások | Hozzáférési módszerek |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor platform szintű mérőszámok | Mérőszámok | [Támogatott metrikák a Azure Stack hub Azure Monitor](azure-stack-metrics-supported.md) | REST API |
| A vendég operációsrendszer-metrikák (például a Perfek száma) kiszámítása | Mérőszámok | Windows és Linux rendszerű virtuális gépek | Storage-tábla vagy-blob:<br>Windows vagy Linux Azure Diagnostics <br>Event hub:<br>Windows Azure Diagnostics |
| Tárolási metrikák | Mérőszámok | Azure Storage | Storage-tábla:<br>Storage Analytics |
| Tevékenységnapló | Események | Minden Azure-szolgáltatás | REST API:<br>Azure Monitor Event API |
| Vendég operációs rendszer naplóinak számítása (például IIS, ETW, syslogs) | Események | Windows és Linux rendszerű virtuális gépek | Storage-tábla vagy-blob:<br>Windows vagy Linux Azure Diagnostics <br>Event hub:<br>Windows Azure Diagnostics |
| Tárolási naplók | Események | Azure Storage | Storage-tábla:<br>Storage Analytics |

## <a name="next-steps"></a>Következő lépések

További információ az [Azure monitor Azure stack hub](azure-stack-metrics-azure-data.md)-ról.
