---
title: Figyelési a tárolt adatokat használják az Azure Stackben |} A Microsoft Docs
description: Az Azure Stack monitorozási adatok felhasználásához lehetőségek ismertetése.
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
ms.date: 04/15/2019
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 0b04a7834218d07706e6314a9c41e0370ff6f6fd
ms.sourcegitcommit: b36d078e699c7924624b79641dbe9021af9606ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67816190"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Hogyan lehet az Azure Stack származó megfigyelési adatokat

*Vonatkozik: Az Azure Stack integrált rendszerek*

Figyelési adatok az Azure Monitor folyamattal, csakúgy, mint az Azure Monitor globális Azure-ban egyetlen helyen található meg. De nem az összes globális Azure-ban található a monitorozási adatok nem érhető el az Azure Stackben. Ez a cikk a különböző módon fogyasztanak monitorozási adatok az Azure Stackben összegzését biztosítunk.
 
## <a name="options-for-data-consumption"></a>Adathasználat lehetőségei

| Adattípus | Category | Támogatott szolgáltatások | Hozzáférési módok |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Az Azure platform-szintű metrikák figyelése | Mérőszámok | [A támogatott mérőszámok az Azure monitorral, az Azure Stackben](azure-stack-metrics-supported.md) | REST API |
| Vendég operációs rendszer metrikákat (például teljesítményszám) számítási | Mérőszámok | Windows és Linux rendszerű virtuális gépek | \- Vagy blob Storage-táblába:<br>Windows vagy Linux rendszerű Azure-diagnosztika <br>Event Hub:<br>Windows Azure Diagnostics |
| Storage-mérőszámok | Mérőszámok | Azure Storage | Storage-táblához:<br>Storage Analytics |
| Tevékenységnapló | Events | Minden Azure-szolgáltatás | REST API:<br>Az Azure Monitor Event API |
| Vendég operációs rendszer naplóit (például az IIS, az ETW, rendszerbejegyzéseket) számítási | Events | Windows és Linux rendszerű virtuális gépek | \- Vagy blob Storage-táblába:<br>Windows vagy Linux rendszerű Azure-diagnosztika <br>Event Hub:<br>Windows Azure Diagnostics |
| Storage-naplók | Events | Azure Storage | Storage-táblához:<br>Storage Analytics |

## <a name="next-steps"></a>További lépések

Tudjon meg többet [Azure monitorozása az Azure Stacken](azure-stack-metrics-azure-data.md).
