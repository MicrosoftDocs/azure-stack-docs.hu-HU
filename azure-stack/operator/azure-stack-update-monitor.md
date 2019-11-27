---
title: Frissítések figyelése Azure Stack a PowerShell használatával | Microsoft Docs
description: Tudnivalók a Azure Stack frissítéseinek figyeléséről a PowerShell használatával
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
ms.openlocfilehash: a724320bd066e6d46e33e6bdc9c80687cfb97736
ms.sourcegitcommit: 55ec59f831a98c42a4e9ff0dd954bf10adb98ff1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74540283"
---
# <a name="monitor-updates-in-azure-stack-using-powershell"></a>Frissítések figyelése Azure Stack a PowerShell használatával

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

A frissítések figyeléséhez és kezeléséhez használhatja a Azure Stack felügyeleti végpontokat. Elérhetők a PowerShell-lel. A Azure Stack PowerShell-lel való beállításával kapcsolatos utasításokért lásd: a [PowerShell telepítése Azure Stackhoz](azure-stack-powershell-install.md).

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

-   [Frissítések kezelése Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)