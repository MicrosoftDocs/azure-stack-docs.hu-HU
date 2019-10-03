---
title: PowerShell a Azure Stackban | Microsoft Docs
description: Azure Stack PowerShell-ben számos modul és kontextus található.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 1c2727562fe287c5450a4977803a7c6fce14f27a
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824291"
---
# <a name="get-started-with-powershell-in-azure-stack"></a>Ismerkedés a PowerShell-lel Azure Stack

A PowerShell úgy van kialakítva, hogy az erőforrásokat a parancssorból kezelhesse és felügyelje. A PowerShellt akkor használhatja, ha olyan automatizált eszközöket szeretne létrehozni, amelyek a Azure Resource Manager modellt használják. A PowerShell-modul olyan PowerShell-függvények készlete lehet, amelyek egy adott terület összes aspektusának kezelésére vannak csoportosítva. A Azure Stack használatához a PowerShell-parancsmagok különböző készleteit kell megzsonglőrködni.

Ez a cikk segítséget nyújt a Azure Stack-ben használt PowerShell-modulok különböző eléréséhez. Ha Azure Stack PowerShellt használ, az alábbi táblázatban látható módon használhatja a négy API-készlet bármelyikét:

| API | PowerShell-referencia | REST-hivatkozás |
| --- | --- | --- |
| Globális Azure Resource Manager | [Azure PowerShell modulok](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [REST API böngésző](https://docs.microsoft.com/rest/api/) |
| Azure Stack Resource Manager | [Az Azure Stackben API-verzióprofilok kezelése](azure-stack-version-profiles.md) | [Az Azure Stackben API-verzióprofilok kezelése](azure-stack-version-profiles.md) |
| Azure Stack rendszergazdai végpontok | [Azure Stack felügyeleti modul](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [REST API böngésző – Azure Stack](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| Azure Stack Kiemelt végpont | [A rendszerjogosultságú végpont használata Azure Stack](../operator/azure-stack-privileged-endpoint.md) | |

Minden csatoló a globális Azure-ban vagy Azure Stack-ban található erőforrás-szolgáltatót. Az erőforrás-szolgáltatók lehetővé teszik az Azure képességeit. Az Azure számítási erőforrás-szolgáltatója például programozott hozzáférést biztosít a virtuális gépek létrehozásához és kezeléséhez, valamint az azokhoz kapcsolódó erőforrásokhoz.

Az erőforrás-szolgáltatók mindkét funkciót és vezérlőt biztosítják az erőforrás kezeléséhez és konfigurálásához. Azure Resource Manager használatával programozott módon érheti el az erőforrás-szolgáltatókat. Az illesztőfelület pedig felületet biztosít a PowerShell, az Azure CLI és a saját REST-ügyfelek számára.

## <a name="where-to-find-azure-stack-powershell"></a>Hol található Azure Stack PowerShell

A következő blokk diagram a PowerShell-modulok készletei közötti kapcsolatokat mutatja. A gépről betöltheti a PowerShell-modulokat, és felügyelheti a globális Azure-t és a Azure Stack is.

![Azure Stack PowerShell](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>Globális Azure

Azure PowerShell olyan parancsmagokat tartalmaz, amelyek a Azure Resource Manager aktuális verzióját használják az Azure-erőforrásokkal való munkavégzéshez. Azure PowerShell a .NET Standard verziót használja, ami azt jelenti, hogy a PowerShell-verziókat Windows, macOS és Linux rendszeren is használhatja. Az Azure PowerShell az Azure Cloud Shellben is elérhető. További információért lásd [az Azure PowerShell használatának első lépéseit](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manager

Azure Stack PowerShell olyan parancsmagokat biztosít, amelyek a Azure Resource Manager korábbi verzióit használják. Ezek a parancsmagok kompatibilisek a Azure Stack található erőforrás-szolgáltatókkal. A Azure Stack minden erőforrás-szolgáltatója a globális Azure-ban található szolgáltató egy régebbi verzióját használja. Az Azure Stack által támogatott összes szolgáltató verziójának koordinálásához használhatja az API-profilokat. Azure Stack PowerShell a PowerShell 5,1-et használja, és csak Windows rendszeren érhető el. További információ: [az API-verziók profiljainak kezelése Azure Stackban](azure-stack-version-profiles.md).

### <a name="azure-stack-administrator"></a>Azure Stack rendszergazda

A Azure Stack erőforrás-szolgáltatók készletét teszi elérhetővé a Felhőbeli operátor számára, hogy az operátor Azure Stack telepítsen és őrizzen meg. A globális Azure-ban ez az interakció a felhasználótól származik, és az Azure részeként kezeli a színfalak mögött. A Azure Stack azonban a vállalatok támogatják a privát felhőket is. A feladatok végrehajtásához az operátor a Azure Stack felügyeleti API-kkal kommunikál. További információ: [a PowerShell telepítése Azure Stackhoz](../operator/azure-stack-powershell-install.md).

### <a name="azure-stack-privileged-endpoint"></a>Azure Stack Kiemelt végpont

A Azure Stack kezelői tevékenységeit, például a telepítés tesztelését és a naplók elérését, a kezelők kezelhetik a privilegizált végpontot (PEP). A PEP egy előre konfigurált távoli PowerShell-konzol, amely lehetővé teszi, hogy a kezelők számára megfelelő hozzáférést biztosítson bizonyos feladatokhoz. A végpont a PowerShell elég felügyeletét (JEA) használja a parancsmagok korlátozott készletének elérhetővé tétele érdekében. További információ: [a privilegizált végpont használata Azure Stackban](../operator/azure-stack-privileged-endpoint.md).

### <a name="azure-stack-tools"></a>Eszközök Azure Stack

A Azure Stack parancsfájlokat és további parancsmagokat tesz elérhetővé egy GitHub-tárházban, a *AzureStack-eszközökben*. A AzureStack-Tools PowerShell-modulokat üzemeltet az erőforrások Azure Stack történő kezeléséhez és üzembe helyezéséhez. Ha VPN-kapcsolat létesítését tervezi, letöltheti ezeket a PowerShell-modulokat a Azure Stack Development Kitra vagy egy Windows-alapú külső ügyfélre. További információért lépjen a [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) oldalra.

## <a name="work-with-powershell-in-azure-stack"></a>A PowerShell használata Azure Stack

A PowerShell programozási módszert biztosít a Azure Resource Manager való interakcióhoz. Dolgozhat interaktív parancssorral, vagy ha automatizálja a feladatokat, írhat parancsfájlokat.

Ha sok időt tölt Azure Stack PowerShell-lel, a modulok telepítését és újratelepítését is megtalálhatja. Ha egyszerre dolgozik a globális Azure-ban, ez a rutin kihívást jelenthet, mivel a célnak megfelelően el kell távolítania és újra kell telepítenie a modulokat. 

A Docker-tárolók segítségével elkülönítheti a PowerShell egyes verzióit a helyi gépen. Ha Docker-tárolókat szeretne használni, hogy a PowerShell-modul PowerShell-modul készletére váltson, a PowerShell [futtatásához használja a Docker használata](azure-stack-powershell-user-docker.md)című témakört.


## <a name="next-steps"></a>További lépések

- További információ a [PowerShell API-profiljairól](azure-stack-version-profiles.md) Azure stack.
- Telepítse a [Azure stack PowerShellt](../operator/azure-stack-powershell-install.md).
- További információ a Felhőbeli konzisztencia [Azure Resource Manager-sablonok](azure-stack-develop-templates.md) létrehozásáról.
