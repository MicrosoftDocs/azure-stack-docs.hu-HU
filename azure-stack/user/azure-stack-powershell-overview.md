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
ms.openlocfilehash: 8f5d97969c96e8f3546d37ffed28b6332d80dc05
ms.sourcegitcommit: 2ee75ded704e8cfb900d9ac302d269c54a5dd9a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394427"
---
# <a name="get-started-with-powershell-in-azure-stack"></a>Az Azure Stack PowerShell használatának első lépései

PowerShell kezelésére és a parancssorból erőforrások felügyeletére szolgál. Használhatja a Powershellt, ha szeretne létrehozni, amely az Azure Resource Manager modellt használja, automatizált eszközökkel. Egy PowerShell-modul PowerShell-funkciók, amelyek egy adott terület minden szempontjának kezeléséhez adható meg. Azure Stack használatának különböző virtuálisgép-csoportokon PowerShell-parancsmagok juggle kell.

Ez a cikk segít az Elhelyezés önállóan a PowerShell-modulok az Azure Stack által használt különböző. Az Azure Stack PowerShell használatakor használhatja bármelyik négyféle az API-k, az alábbi táblázatban látható módon:

| API | PowerShell-referencia | REST-referencia |
| --- | --- | --- |
| Globális Azure Resource Manager | [Az Azure PowerShell-modulok](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [REST API-böngésző](https://docs.microsoft.com/rest/api/) |
| Az Azure Stack Resource Manager | [Az Azure Stackben API-verzióprofilok kezelése](azure-stack-version-profiles.md) | [Az Azure Stackben API-verzióprofilok kezelése](azure-stack-version-profiles.md) |
| Az Azure Stack rendszergazdai végpontok | [Az Azure Stack rendszergazdai modul](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [REST API-böngésző - Azure Stack](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| Az Azure Stack az emelt szintű végpont | [A kiemelt jogosultságú végpont használata az Azure Stackben](../operator/azure-stack-privileged-endpoint.md) | |

Mindegyik adapterhez kapcsolatba lép az erőforrás-szolgáltatók a globális Azure-ban. Erőforrás-szolgáltatók az Azure képességek engedélyezéséhez. Például az Azure Compute erőforrás-szolgáltató programozás alapú hozzáférést biztosít a létrehozását és kezelését virtuális gépek és az azokat támogató erőforrásokhoz.

Erőforrás-szolgáltatók adja meg a funkciók és a vezérlők kezeléséhez és az erőforrás-konfigurálása. Az erőforrás-szolgáltatókat is programozott módon érheti el, hogy az Azure Resource Managerrel. A felület, a surface biztosít a PowerShell, az Azure CLI és a saját REST-ügyfelek.

## <a name="where-to-find-azure-stack-powershell"></a>Hol található az Azure Stack PowerShell-lel

A következő blokkban ábrán a PowerShell-modulok a készletek közötti kapcsolatokat. A gép számára a PowerShell-modulokat betölteni és a globális Azure és az Azure Stack kezeléséhez.

![Az Azure Stack Powershell](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>Globális Azure

Az Azure PowerShell parancsmagok, amelyek a jelenlegi verzió az Azure Resource Manager használata az Azure-erőforrások használata egy készletét tartalmazza. Az Azure PowerShell a .NET Standard verziót, ami azt jelenti, hogy használhatja a Windows, macOS és Linux PowerShell-verziókat használja. Az Azure PowerShell az Azure Cloud Shellben is elérhető. További információért lásd [az Azure PowerShell használatának első lépéseit](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-resource-manager"></a>Az Azure Stack Resource Manager

Az Azure Stack PowerShell-parancsmagokat, amelyek használják a korábbi verziók az Azure Resource Manager biztosít. Ezek a parancsmagok kompatibilisek az erőforrás-szolgáltatók az Azure Stackben. Mindegyik erőforrás-szolgáltató az Azure Stackben a globális Azure-ban található szolgáltató régebbi verzióját használja. Könnyebben koordinálja az egyes Azure Stack által támogatott szolgáltató verziója, használhat API-t. Az Azure Stack PowerShell PowerShell 5.1 használja, és csak a Windows érhető el. További információkért lásd: [kezelése API-verzióprofilok az Azure Stackben](azure-stack-version-profiles.md).

### <a name="azure-stack-administrator"></a>Azure Stack-rendszergazda

Az Azure Stack tesz elérhetővé, a felhő üzemeltetője az erőforrás-szolgáltatók, hogy az operátor is telepítése és kezelése az Azure Stack. A globális Azure-ban ennek az interakciónak emeli ki, a felhasználó és a színfalak mögött az Azure részeként kezeli. Az Azure Stack azonban vállalatok számára is támogatja a privát felhő. Ezek a feladatok végrehajtásához az operátor az Azure Stack rendszergazdai API-k kommunikál. További információkért lásd: [Azure Stack PowerShell telepítése](../operator/azure-stack-powershell-install.md).

### <a name="azure-stack-privileged-endpoint"></a>Az Azure Stack az emelt szintű végpont

Operátor tevékenységek az Azure Stackben, például a telepítés tesztelése és a naplók elérése operátorok a kiemelt végponthoz (EGP) kezelheti. Az EGP egy előre konfigurált távoli PowerShell-konzolt, amely operátorok elegendő hozzáférést adott feladatok elvégzéséhez nyújt. A végpont PowerShell csak Enough Administration (JEA) használatával teszi közzé a parancsmagok egy korlátozott készletét. További információkért lásd: [az emelt szintű végpontot használja az Azure Stackben](../operator/azure-stack-privileged-endpoint.md).

### <a name="azure-stack-tools"></a>Azure Stack Tools

Az Azure Stack teszi elérhetővé parancsfájlok és további parancsmag egy GitHub-adattárában *AzureStack-eszközök*. 1.2.9-es-eszközök kezeléséhez, és üzembe erőforrásokat az Azure Stack PowerShell-modulok üzemelteti. Ha VPN-kapcsolatot létesíteni, letöltheti ezek a PowerShell-modulok, az Azure Stack fejlesztői készletet, vagy egy Windows-alapú külső ügyfél. További információkért látogasson el a [AzureStack-eszközök](https://github.com/Azure/AzureStack-Tools) lapot.

## <a name="work-with-powershell-in-azure-stack"></a>Az Azure Stack PowerShell-lel dolgozni

PowerShell-programozott módon kezelheti az Azure Resource Manager biztosít. Egy interaktív parancssort használhatja, vagy ha feladatok vagyunk automatizálása, szkripteket.

Mennyi időt az Azure Stack PowerShell használata, ha saját maga telepítése és újratelepítése az modulok találja. Dolgozunk a globális Azure-ral egyszerre, ha a rutin nehéz feladat lehet, mert eltávolítása és újratelepítése függően a cél a modulok kell. 

A Docker-tárolók használatával elkülönítheti az egyes PowerShell-verzió a helyi gépen. Tekintse meg, hogy válthat a PowerShell-modul set beállítása PowerShell-modul Docker-tárolók használatához [használja a Docker, futtassa a Powershellt](azure-stack-powershell-user-docker.md).


## <a name="next-steps"></a>További lépések

- További információ [PowerShell API-profilok](azure-stack-version-profiles.md) az Azure Stackben.
- Telepítés [az Azure Stack Powershell](../operator/azure-stack-powershell-install.md).
- Olvassa el létrehozása [Azure Resource Manager-sablonok](azure-stack-develop-templates.md) felhőalapú konzisztencia.
