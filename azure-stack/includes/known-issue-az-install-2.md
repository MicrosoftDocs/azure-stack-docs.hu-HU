---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/9/2020
ms.reviewer: sijuman
ms.lastreviewed: 12/9/2020
ms.openlocfilehash: e456f17c75b129a3f45c64b9b55e75d21ecb2973
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935149"
---
### <a name="when-installing-az-module-falsely-throws-admin-rights-required-error"></a>Ha az az modul telepítésekor hamisan dobja a rendszergazdai jogosultságokat, hiba történt

- Alkalmazható: a probléma a 2002-es és újabb verziókra vonatkozik
- Ok: Ha a modult egy emelt szintű parancssorból telepíti, a rendszer hibát jelez. A hiba a következőt mondja: `Administrator rights required` .
- Szervizelés: zárd be a munkamenetet, és indítson el egy új, emelt szintű PowerShell-munkamenetet. Győződjön meg arról, hogy nincs létező az. A munkamenetben betöltött fiókok modul.
- Előfordulás: gyakori