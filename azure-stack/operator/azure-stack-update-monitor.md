---
title: Frissítések figyelése Azure Stack központban a PowerShell használatával | Microsoft Docs
description: Útmutató a Azure Stack hub frissítéseinek figyeléséhez a PowerShell használatával
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 91d5d0e7c63be8a192113d26818380983c668aa7
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75813572"
---
# <a name="monitor-updates-in-azure-stack-hub-using-powershell"></a>Frissítések figyelése Azure Stack központban a PowerShell használatával

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek*

A frissítések figyeléséhez és kezeléséhez használhatja a Azure Stack hub felügyeleti végpontokat. Elérhetők a PowerShell-lel. Az Azure Stack hub PowerShell-lel való beállításával kapcsolatos utasításokért lásd: a [PowerShell telepítése Azure stack hubhoz](azure-stack-powershell-install.md).

A frissítések kezeléséhez a következő PowerShell-parancsmagot használhatja:

| Parancsmag | Leírás |
|------------------------------------------------------|-------------|
| [Get-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdate?view=azurestackps-1.8.0) | Az elérhető frissítések listájának beolvasása. |
| [Get-AzsUpdateLocation](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateLocation?view=azurestackps-1.8.0)| A frissítési helyszínek listájának beolvasása. |
| [Get-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateRun?view=azurestackps-1.8.0) | A frissítési futtatások listájának beolvasása.  |
| [Install-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Install-AzsUpdate?view=azurestackps-1.8.0) | Egy adott frissítés alkalmazása egy frissítési helyen. |
| [Folytatás – AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Resume-AzsUpdateRun?view=azurestackps-1.8.0) | Egy korábban elindított frissítési futtatást folytat, amely sikertelen volt. |

## <a name="get-a-list-of-update-runs"></a>A frissítési futtatások listájának lekérése

A frissítési futtatások listájának lekérése:

```powershell
Get-AzsUpdateRun -UpdateName Microsoft1.0.180302.1
```

## <a name="resume-a-failed-update-operation"></a>Sikertelen frissítési művelet folytatása

Ha a frissítés sikertelen, akkor folytathatja a frissítés futtatását, ha a következő parancs futtatásával kilépett:

```powershell
Get-AzsUpdateRun -Name 5173e9f4-3040-494f-b7a7-738a6331d55c -UpdateName Microsoft1.0.180305.1 | Resume-AzsUpdateRun
```

## <a name="next-steps"></a>Következő lépések

-   [Frissítések kezelése Azure Stack központban](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)
