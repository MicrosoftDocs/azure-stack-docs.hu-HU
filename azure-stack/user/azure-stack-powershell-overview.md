---
title: PowerShell az Azure Stackben |} A Microsoft Docs
description: Az Azure Stack PowerShell számos modulok és környezeteket.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: b28a90ceca59e5cd072018b217d81d101c1b0853
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490047"
---
# <a name="get-started-with-powershell-on-azure-stack"></a>Az Azure Stack PowerShell használatának első lépései

PowerShell kezelésére és a parancssorból erőforrások felügyeletére szolgál. Használhatja a Powershellt, ha szeretne létrehozni, amely az Azure Resource Manager modellt használja, automatizált eszközökkel. Egy PowerShell-modul PowerShell-funkciók, amelyek egy adott terület minden szempontjának kezeléséhez adható meg. Azure Stack használatának kell juggle PowerShell-parancsmagok más-más részhalmazához.

Ez a cikk segít kiigazodni között saját magának, ezek más PowerShell-modulok az Azure Stacken használni. Nincsenek API-kat használhatja az Azure Stack PowerShell használata esetén négy részhalmazához.

| API | PowerShell-referencia | REST-referencia |
| --- | --- | --- |
| 1. Globális Azure Resource Manager | [Az Azure PowerShell-modulok](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [REST API-böngésző](https://docs.microsoft.com/rest/api/) |
| 2. Az Azure Stack Resource Manager | [API-verzióprofilok kezelése az Azure Stackben](azure-stack-version-profiles.md) | [API-verzióprofilok kezelése az Azure Stackben](azure-stack-version-profiles.md) |
| 3. Az Azure Stack rendszergazdai végpontok | [Azure Stack Admin Module](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [REST API-böngésző -, az Azure Stack](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| 4.  Az Azure Stack kiemelt végponthoz | [A kiemelt végpont használata az Azure Stackben](../operator/azure-stack-privileged-endpoint.md) | |

Mindegyik adapterhez kapcsolatba lép az erőforrás-szolgáltatók a globális Azure-ban. Erőforrás-szolgáltatók az Azure képességek engedélyezéséhez. Például az Azure számítási erőforrás-szolgáltató programozott hozzáférést biztosít a létrehozását és kezelését virtuális gépek és az azokat támogató erőforrásokhoz.

Erőforrás-szolgáltatók olyan funkciókat kínálnak, de is biztosít a vezérlők kezeléséhez és az erőforrás-konfigurálása. Programozott módon hozzáférhet az erőforrás-szolgáltatók az Azure Resource Manager használatával, és, a felületet biztosít a PowerShell, az Azure CLI és REST-ügyfelek a saját egy felületre.

## <a name="where-to-find-azure-stack-powershell"></a>Hol található az Azure Stack PowerShell-lel

A következő blokkban ábrán az egyes PowerShell-modulok a kapcsolat. A gép számára a PowerShell-modulokat betölteni és a globális Azure és az Azure Stack kezeléséhez.

![Az Azure Stack Powershell](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>Globális Azure

Az Azure PowerShell-parancsmagokat, amelyek az Azure Resource Manager-modell aktuális verzióját használják az Azure-erőforrások kezeléséhez biztosít. Az Azure PowerShell a .NET Standardet használja, így Windows, macOS és Linux rendszereken is elérhető. Az Azure PowerShell az Azure Cloud Shellben is elérhető. További információért lásd [az Azure PowerShell használatának első lépéseit](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-resource-manager"></a>Az Azure Stack Resource Manager

Az Azure Stack PowerShell-parancsmagot is, melyek korábbi verziók az Azure Resource Manager kompatibilis az erőforrás-szolgáltatók az Azure Stackben biztosít. Mindegyik erőforrás-szolgáltató az Azure Stackben a globális Azure-ban található szolgáltató régebbi verzióját használja. Könnyebben koordinálja az egyes Azure Stack által támogatott szolgáltató verziója, használhat API-t. Az Azure Stack PowerShell PowerShell 5.1 használja és csak a Windows. További információkért lásd: [kezelése API-verzióprofilok az Azure Stackben](azure-stack-version-profiles.md).

### <a name="azure-stack-administrator"></a>Azure Stack-rendszergazda

Az Azure Stack tesz elérhetővé, telepítése és kezelése az Azure Stack a felhőalapú szereplő erőforrás-szolgáltatók. Globális Azure-ban ennek az interakciónak emeli ki, a felhasználó elől, és a színfalak mögött az Azure részeként kezeli. Az Azure Stack vállalatok számára azonban lehetővé teszi a magánfelhő támogatásához. Annak érdekében, hogy ezeket a feladatokat az operátor kommunikál az Azure Stack rendszergazdai API-kat. További információkért lásd: [Azure Stack PowerShell telepítése](../operator/azure-stack-powershell-install.md).

### <a name="azure-stack-privileged-endpoint"></a>Az Azure Stack kiemelt végponthoz

Operátor tevékenységek az Azure Stacken, például a telepítés tesztelése és a naplók elérése operátorok a kiemelt végponthoz (EGP) kezelheti. Az EGP egy előre konfigurált távoli PowerShell-konzolt, amely csak meghatározott feladatok végezhetők el elég hozzáférést biztosít az operátorok. A végpont PowerShell JEA (Just Enough Administration) használatával teszi közzé a parancsmagok egy korlátozott készletét. További információkért lásd: [a rendszerjogosultságú végpont használata az Azure Stackben](../operator/azure-stack-privileged-endpoint.md).

### <a name="azurestack-tools"></a>AzureStack-Tools

Emellett az Azure Stack biztosít parancsfájlok és a egy GitHub-adattárból, az Azure Stack eszközök elérhető további parancsmagjait. 1.2.9-es-eszközök kezeléséhez, és üzembe erőforrásokat az Azure Stack PowerShell-modulok üzemelteti. Ha azt tervezi, VPN-kapcsolatot létesíteni, letöltheti ezek a PowerShell-modulok, az Azure Stack fejlesztői készletet, vagy egy Windows-alapú külső ügyfél. További információkért lásd: [AzureStack-eszközök](https://github.com/Azure/AzureStack-Tools).

## <a name="working-with-powershell-on-azure-stack"></a>PowerShell használata az Azure Stackben

PowerShell-programozott módon kezelheti az Azure Resource Manager biztosít. Egy interaktív parancssort dolgozni, vagy parancsfájlokat írhat, ha a feladatok automatizálásához-e.

Mennyi időt az Azure Stack PowerShell használata, ha saját maga telepítése és újratelepítése az modulok találja. Ha a globális Azure-ral egyszerre óta eltávolítása és újratelepítése függően a cél a modulok kell ez kihívást is jelenthet. A Docker-tárolók használatával elkülönítheti az egyes PowerShell-verzió a helyi gépen. [Futtassa a Powershellt használhatja a Dockert](azure-stack-powershell-user-docker.md) megvizsgálja a Docker-tárolók használatával, így válthat a PowerShell-modul set beállítása PowerShell-modult.


## <a name="next-steps"></a>További lépések

- További információ [PowerShell API-profilok](azure-stack-version-profiles.md) az Azure Stackben.
- [Az Azure Stack Powershell telepítése](../operator/azure-stack-powershell-install.md)
- Olvassa el létrehozása [Azure Resource Manager-sablonok](azure-stack-develop-templates.md) felhőalapú konzisztencia.