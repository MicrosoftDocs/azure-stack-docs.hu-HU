---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/9/2020
ms.reviewer: sijuman
ms.lastreviewed: 12/9/2020
ms.openlocfilehash: 003f6801209d5d5ab1c9c4bd1df0fa47578004ee
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935150"
---
### <a name="error-thrown-when-installing-the-az-modules"></a>Hiba történt az az modulok telepítésekor

- Alkalmazható: a probléma a 2002-es és újabb verziókra vonatkozik
- Ok: a modul telepítésekor hiba történik. A hibaüzenet kezdete: `Register-PacakgeSource : A parameter cannot be found that matches parameter name. 'PackageManagementProvider'.` vagy a hibaüzenet a következő szöveget tartalmazhatja: `PackageManagement\Install-Package : Cannot convert value "2.0.1-preview" to type "System.Version". Error: "Input string was not in a correct format."`
- Szervizelés: futtassa a következő parancsmagot ugyanabban a munkamenetben:  
    `Install-Module PowershellGet -MinimumumVersion 2.3.0 -Force`  
Zárjuk be a munkamenetet, és indítson el egy új emelt szintű PowerShell-munkamenetet.
- Előfordulás: gyakori