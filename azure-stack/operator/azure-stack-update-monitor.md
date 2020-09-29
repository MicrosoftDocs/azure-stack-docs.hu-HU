---
title: Frissítések figyelése a PowerShell-lel Azure Stack hub-ban
description: Megtudhatja, hogyan figyelheti a frissítéseket a PowerShell-lel Azure Stack hub-ban.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 315cf773c63e3154ce92472c15d18b5e4bbbe019
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487549"
---
# <a name="monitor-updates-with-powershell-in-azure-stack-hub"></a>Frissítések figyelése a PowerShell-lel Azure Stack hub-ban

A frissítések figyeléséhez és kezeléséhez használhatja a Azure Stack hub felügyeleti végpontokat. Elérhetők a PowerShell-lel. Az Azure Stack hub PowerShell-lel való beállításával kapcsolatos utasításokért lásd: a [PowerShell telepítése Azure stack hubhoz](azure-stack-powershell-install.md).

A frissítések kezeléséhez a következő PowerShell-parancsmagokat használhatja:

| Parancsmag | Leírás |
|------------------------------------------------------|-------------|
| [Get-AzsUpdate](/powershell/module/azs.update.admin/get-azsupdate?view=azurestackps-1.8.0) | Az elérhető frissítések listájának beolvasása. |
| [Get-AzsUpdateLocation](/powershell/module/azs.update.admin/get-azsupdatelocation?view=azurestackps-1.8.0)| A frissítési helyszínek listájának beolvasása. |
| [Get-AzsUpdateRun](/powershell/module/azs.update.admin/get-azsupdaterun?view=azurestackps-1.8.0) | A frissítési futtatások listájának beolvasása.  |
| [Install-AzsUpdate](/powershell/module/azs.update.admin/install-azsupdate?view=azurestackps-1.8.0) | Egy adott frissítés alkalmazása egy frissítési helyen. |
| [Folytatás – AzsUpdateRun](/powershell/module/azs.update.admin/resume-azsupdaterun?view=azurestackps-1.8.0) | Egy korábban elindított frissítési futtatást folytat, amely sikertelen volt. |

## <a name="get-a-list-of-update-runs"></a>A frissítési futtatások listájának lekérése

A frissítési futtatások listájának lekéréséhez futtassa a következő parancsot:

```powershell
Get-AzsUpdateRun -UpdateName Microsoft1.0.180302.1
```

## <a name="resume-a-failed-update-operation"></a>Sikertelen frissítési művelet folytatása

Ha a frissítés sikertelen, akkor folytathatja a frissítés futtatását, ha a következő parancs futtatásával kilépett:

```powershell
Get-AzsUpdateRun -Name 5173e9f4-3040-494f-b7a7-738a6331d55c -UpdateName Microsoft1.0.180305.1 | Resume-AzsUpdateRun
```

## <a name="troubleshoot"></a>Hibaelhárítás

További információ a frissítések hibaelhárításáról: [Azure stack hibaelhárítás](azure-stack-troubleshooting.md).

## <a name="next-steps"></a>Következő lépések

- [Frissítések kezelése Azure Stack központban](./azure-stack-updates.md)
