---
title: Azure Stack hub megfigyelési adatainak felhasználása
description: További információ a Azure Stack hub megfigyelési adatainak felhasználására vonatkozó lehetőségekről.
author: mattbriggs
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 9bbe39f63a4b59446f5c8d6444381afc9ea89cc8
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703927"
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
