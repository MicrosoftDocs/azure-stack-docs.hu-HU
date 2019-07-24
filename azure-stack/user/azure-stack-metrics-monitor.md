---
title: Megfigyelési adatok felhasználása Azure Stackból | Microsoft Docs
description: További információ a Azure Stack figyelési adatainak felhasználására vonatkozó beállításokról.
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
ms.date: 07/23/2019
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 64935022f8ace33bd4350ab4ef5421636ff3991e
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418505"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>A monitorozási adatok felhasználása Azure Stack

*Vonatkozik: Integrált rendszerek Azure Stack*

A figyelési adatgyűjtés egyetlen helyen található a Azure Monitor folyamattal, akárcsak Azure Monitor a globális Azure-ban. A globális Azure-ban található összes figyelési érték azonban nem érhető el Azure Stackban. Ebben a cikkben összefoglaljuk, hogyan használhatók a monitorozási információk a Azure Stackban.
 
## <a name="options-for-data-consumption"></a>Adatfelhasználási beállítások

| Adattípus | Category | Támogatott szolgáltatások | Hozzáférési módszerek |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor platform szintű mérőszámok | Mérőszámok | [Támogatott metrikák a Azure Monitor on Azure Stack](azure-stack-metrics-supported.md) | REST API |
| A vendég operációsrendszer-metrikák (például a Perfek száma) kiszámítása | Mérőszámok | Windows és Linux rendszerű virtuális gépek | Storage-tábla vagy-blob:<br>Windows vagy Linux Azure Diagnostics <br>Event hub:<br>Windows Azure Diagnostics |
| Tárolási metrikák | Mérőszámok | Azure Storage | Storage-tábla:<br>Storage Analytics |
| Tevékenységnapló | Events | Minden Azure-szolgáltatás | REST API:<br>Azure Monitor Event API |
| Vendég operációs rendszer naplóinak számítása (például IIS, ETW, syslogs) | Events | Windows és Linux rendszerű virtuális gépek | Storage-tábla vagy-blob:<br>Windows vagy Linux Azure Diagnostics <br>Event hub:<br>Windows Azure Diagnostics |
| Tárolási naplók | Events | Azure Storage | Storage-tábla:<br>Storage Analytics |

## <a name="next-steps"></a>További lépések

Tudjon meg többet [Azure monitorozása az Azure Stacken](azure-stack-metrics-azure-data.md).
