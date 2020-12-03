---
title: PowerShell Azure Stack hub-ban
description: Azure Stack központban található PowerShell számos modult és kontextust tartalmaz.
author: mattbriggs
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 3e00be01c56b9689e29681e01919feb0f432d7b9
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96524656"
---
# <a name="get-started-with-powershell-in-azure-stack-hub"></a>Bevezetés a PowerShell Azure Stack Hubon való használatába

A PowerShell úgy van kialakítva, hogy az erőforrásokat a parancssorból kezelhesse és felügyelje. A PowerShellt akkor használhatja, ha olyan automatizált eszközöket szeretne létrehozni, amelyek a Azure Resource Manager modellt használják. A PowerShell-modul olyan PowerShell-függvények készlete lehet, amelyek egy adott terület összes aspektusának kezelésére vannak csoportosítva. Azure Stack hub használatához a PowerShell-parancsmagok különböző készleteit kell megzsonglőrködni.

Ez a cikk segítséget nyújt az Azure Stack hub-ban használt PowerShell-modulok különböző eléréséhez. Ha a PowerShellt Azure Stack hub-ban használja, a négy API-készlet bármelyikét használhatja, ahogy az a következő táblázatban látható:

| API | PowerShell-dokumentáció | REST-referencia |
| --- | --- | --- |
| Globális Azure Resource Manager | [Azure PowerShell-modulok](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [REST API böngésző](/rest/api/) |
| Azure Stack hub Resource Manager | [API-verziók profiljainak kezelése Azure Stack hub-ban](azure-stack-version-profiles.md) | [API-verziók profiljainak kezelése Azure Stack hub-ban](azure-stack-version-profiles.md) |
| Azure Stack hub rendszergazdai végpontok | [Azure Stack hub felügyeleti modul](/powershell/azure/azure-stack/overview) | [REST API Browser-Azure Stack hub](/rest/api/?term=Azure Azure Stack Admin) |
| Azure Stack hub privilegizált végpontja | [A rendszerjogosultságú végpont használata Azure Stack központban](../operator/azure-stack-privileged-endpoint.md) | |

Minden csatoló a globális Azure-ban vagy Azure Stack hub-ban található erőforrás-szolgáltatót. Az erőforrás-szolgáltatók lehetővé teszik az Azure képességeit. Az Azure számítási erőforrás-szolgáltatója például programozott hozzáférést biztosít a virtuális gépek létrehozásához és kezeléséhez, valamint az azokhoz kapcsolódó erőforrásokhoz.

Az erőforrás-szolgáltatók mindkét funkciót és vezérlőt biztosítják az erőforrás kezeléséhez és konfigurálásához. Azure Resource Manager használatával programozott módon érheti el az erőforrás-szolgáltatókat. Az illesztőfelület pedig felületet biztosít a PowerShell, az Azure CLI és a saját REST-ügyfelek számára.

## <a name="where-to-find-azure-stack-hub-powershell"></a>Hol található Azure Stack hub PowerShell

A következő blokk diagram a PowerShell-modulok készletei közötti kapcsolatokat mutatja. A gépről betöltheti a PowerShell-modulokat, és felügyelheti a globális Azure-és Azure Stack hubot is.

![Azure Stack hub PowerShell](media/azure-stack-powershell-overview/azure-stack-powershell.svg)

### <a name="global-azure"></a>Globális Azure

Azure PowerShell olyan parancsmagokat tartalmaz, amelyek a Azure Resource Manager aktuális verzióját használják az Azure-erőforrásokkal való munkavégzéshez. Azure PowerShell a .NET Standard verziót használja, ami azt jelenti, hogy a PowerShell-verziókat Windows, macOS és Linux rendszeren is használhatja. Az Azure PowerShell az Azure Cloud Shellben is elérhető. További információért lásd [az Azure PowerShell használatának első lépéseit](/powershell/azure/get-started-azureps).

### <a name="azure-stack-hub-resource-manager"></a>Azure Stack hub Resource Manager

Azure Stack hub PowerShell olyan parancsmagokat biztosít, amelyek a Azure Resource Manager korábbi verzióit használják. Ezek a parancsmagok kompatibilisek az Azure Stack hub erőforrás-szolgáltatókkal. Azure Stack hub minden erőforrás-szolgáltatója a globális Azure-ban található szolgáltató egy régebbi verzióját használja. Az Azure Stack hub által támogatott összes szolgáltató verziójának koordinálásához használhatja az API-profilokat. További információ: [az API-verziók profiljainak kezelése Azure stack központban](azure-stack-version-profiles.md).

### <a name="azure-stack-hub-administrator"></a>Azure Stack hub rendszergazdája

Azure Stack hub erőforrás-szolgáltatók készletét teszi elérhetővé a Felhőbeli operátor számára, így az operátor Azure Stack hub telepítését és karbantartását is lehetővé teszi. A globális Azure-ban ez az interakció a felhasználótól származik, és az Azure részeként kezeli a színfalak mögött. Azure Stack hub; a vállalatok azonban támogatni tudják a privát felhőket. A feladatok végrehajtásához az operátor az Azure Stack hub felügyeleti API-kkal kommunikál. További információ: a [PowerShell telepítése Azure stack hubhoz](../operator/powershell-install-az-module.md).

### <a name="azure-stack-hub-privileged-endpoint"></a>Azure Stack hub privilegizált végpontja

Azure Stack hub operátori tevékenységeihez, például a telepítés teszteléséhez és a naplók eléréséhez a kezelők kommunikálhatnak a privilegizált végponttal (PEP). A PEP egy előre konfigurált távoli PowerShell-konzol, amely lehetővé teszi, hogy a kezelők számára megfelelő hozzáférést biztosítson bizonyos feladatokhoz. A végpont a PowerShell elég felügyeletét (JEA) használja a parancsmagok korlátozott készletének elérhetővé tétele érdekében. További információkat [a kiemelt végpont Azure Stack Hubbeli használatát ismertető részben](../operator/azure-stack-privileged-endpoint.md) talál.

### <a name="azure-stack-hub-tools"></a>Azure Stack hub-eszközök

Azure Stack hub parancsfájlokat és további parancsmagokat tesz elérhetővé egy GitHub-tárházban, a *AzureStack-eszközökben*. A AzureStack-Tools PowerShell-modulokat üzemeltet az erőforrások Azure Stack hubhoz való felügyeletéhez és üzembe helyezéséhez. Ha VPN-kapcsolat létesítését tervezi, letöltheti ezeket a PowerShell-modulokat a Azure Stack Development Kitra vagy egy Windows-alapú külső ügyfélre. További információért lépjen a [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) oldalra.

## <a name="work-with-powershell-in-azure-stack-hub"></a>A PowerShell használata Azure Stack hub-ban

A PowerShell programozási módszert biztosít a Azure Resource Manager való interakcióhoz. Dolgozhat interaktív parancssorral, vagy ha automatizálja a feladatokat, írhat parancsfájlokat.

Ha sok időt tölt a Azure Stack hub PowerShell használatával, akkor a modulok telepítését és újratelepítését is megtalálhatja. Ha egyszerre dolgozik a globális Azure-ban, ez a rutin kihívást jelenthet, mivel a célnak megfelelően el kell távolítania és újra kell telepítenie a modulokat.

A Docker-tárolók segítségével elkülönítheti a PowerShell egyes verzióit a helyi gépen. Ha Docker-tárolókat szeretne használni, hogy a PowerShell-modul PowerShell-modul készletére váltson, a PowerShell [futtatásához használja a Docker használata](azure-stack-powershell-user-docker.md)című témakört.


## <a name="next-steps"></a>További lépések

- További információ a [PowerShell API-profiljairól](azure-stack-version-profiles.md) Azure stack hub-ban.
- Telepítse [Azure stack hub PowerShellt](../operator/powershell-install-az-module.md).
- További információ a Felhőbeli konzisztencia [Azure Resource Manager-sablonok](azure-stack-develop-templates.md) létrehozásáról.
