---
title: Azure Stack hub megfigyelési adatainak felhasználása | Microsoft Docs
description: További információ a Azure Stack hub megfigyelési adatainak felhasználására vonatkozó lehetőségekről.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: e7b7b26afa4cb9a367ea48ef50308568a61db320
ms.sourcegitcommit: 7dd685fddf2f5d7a0c0a20fb8830ca5a061ed031
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76259749"
---
# <a name="consume-monitoring-data-from-azure-stack-hub"></a>Azure Stack hub megfigyelési adatainak felhasználása

A figyelési adatgyűjtés egyetlen helyen található a Azure Monitor folyamattal, akárcsak Azure Monitor a globális Azure-ban. A globális Azure-ban található összes figyelési érték azonban nem érhető el Azure Stack központban. Ebben a cikkben összefoglalást adunk a Azure Stack hub monitorozási adatainak felhasználásának különböző módjairól.
 
## <a name="options-for-data-consumption"></a>Adatfelhasználási beállítások

| Data type | Kategória | Támogatott szolgáltatások | Hozzáférési módszerek |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor platform szintű mérőszámok | Metrikák | [Támogatott metrikák a Azure Stack hub Azure Monitor](azure-stack-metrics-supported.md) | REST API |
| A vendég operációsrendszer-metrikák (például a Perfek száma) kiszámítása | Metrikák | Windows és Linux rendszerű virtuális gépek | Storage-tábla vagy-blob:<br>Windows vagy Linux Azure Diagnostics <br>Event hub:<br>Windows Azure Diagnostics |
| Tárolási metrikák | Metrikák | Azure Storage | Storage-tábla:<br>Storage Analytics |
| Tevékenységnapló | Események | Minden Azure-szolgáltatás | REST API:<br>Azure Monitor Event API |
| Vendég operációs rendszer naplóinak számítása (például IIS, ETW, syslogs) | Események | Windows és Linux rendszerű virtuális gépek | Storage-tábla vagy-blob:<br>Windows vagy Linux Azure Diagnostics <br>Event hub:<br>Windows Azure Diagnostics |
| Tárolási naplók | Események | Azure Storage | Storage-tábla:<br>Storage Analytics |

## <a name="next-steps"></a>Következő lépések

További információ az [Azure monitor Azure stack hub](azure-stack-metrics-azure-data.md)-ról.
