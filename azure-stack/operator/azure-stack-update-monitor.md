---
title: Frissítések figyelése Azure Stack központban a PowerShell használatával
description: Útmutató a Azure Stack hub frissítéseinek figyeléséhez a PowerShell használatával
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: c611d901c62765462d5bccaee6894f5f7c0b0b18
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367284"
---
# <a name="monitor-updates-in-azure-stack-hub-using-powershell"></a>Frissítések figyelése Azure Stack központban a PowerShell használatával

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
