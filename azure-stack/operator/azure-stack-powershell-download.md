---
title: Azure Stack hub-eszközök letöltése a GitHubról
description: Megtudhatja, hogyan töltheti le az Azure Stack hub használatához szükséges eszközöket.
author: mattbriggs
ms.topic: article
ms.date: 4/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 4/22/2020
ms.openlocfilehash: c85982690578cbfbed65c83fb2c83792ce6b4fb2
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111179"
---
# <a name="download-azure-stack-hub-tools-from-github"></a>Azure Stack hub-eszközök letöltése a GitHubról

A **AzureStack-Tools** egy [GitHub-tárház](https://github.com/Azure/AzureStack-Tools) , amely PowerShell-modulokat üzemeltet az erőforrások Azure stack hubhoz való felügyeletéhez és üzembe helyezéséhez. Ha VPN-kapcsolat létesítését tervezi, letöltheti ezeket a PowerShell-modulokat a Azure Stack Development Kitba (ASDK) vagy egy Windows-alapú külső ügyfélre. 

## <a name="get-tools-for-azure-stack-hub-azurerm-module"></a>Eszközök beszerzése Azure Stack hub AzureRM modulhoz

Ezen eszközök beszerzéséhez klónozott a GitHub-tárházat a `master` fiókirodából, vagy töltse le a **AzureStack-Tools** mappát a következő parancsfájl futtatásával:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```
Az Azure Stack hub AzureRM moduljának használatával kapcsolatos további információkért lásd: a [PowerShell-AzureRM modul telepítése Azure stack hubhoz](azure-stack-powershell-install.md)

## <a name="get-tools-for-azure-stack-hub-az-preview-module"></a>Eszközök beszerzése az Azure Stack hub az (előzetes verzió) modulhoz

Ezen eszközök beszerzéséhez klónozott a GitHub-tárházat a `az` fiókirodából, vagy töltse le a **AzureStack-Tools** mappát a következő parancsfájl futtatásával:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/az.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

További információ az az modul Azure Stack hub-hoz való használatáról: a [PowerShell telepítése az előnézet modul for Azure stack hub](powershell-install-az-module.md).

## <a name="functionality-provided-by-the-modules"></a>A modulok által biztosított funkciók

A **AzureStack-Tools** adattár PowerShell-modulokkal rendelkezik, amelyek támogatják az Azure stack hub következő funkcióit:  

| Funkció | Description | Kik használhatják ezt a modult? |
| --- | --- | --- |
| [Felhőalapú képességek](../user/azure-stack-validate-templates.md) | Ez a modul a felhő Felhőbeli képességeinek beszerzésére használható. Például olyan Felhőbeli funkciókat érhet el, mint az API-verzió és a Azure Resource Manager erőforrás. Azure Stack hub és az Azure-felhők virtuálisgép-bővítményeit is lekérheti. | Felhőalapú operátorok és felhasználók |
| [Resource Manager-szabályzat Azure Stack hubhoz](../user/azure-stack-policy-module.md) | Ezzel a modullal konfigurálhat egy Azure-előfizetést vagy egy Azure-erőforráscsoportot ugyanazzal a verziószámozással és szolgáltatással, mint Azure Stack hub. | Felhőalapú operátorok és felhasználók |
| [Regisztrálás az Azure-ban](azure-stack-registration.md ) | Ezzel a modullal regisztrálhat ASDK-példányát az Azure-ban. A regisztráció után letöltheti az Azure Marketplace-elemeket Azure Stack központban. | Felhőbeli operátorok |
| [Azure Stack hub üzembe helyezése](../asdk/asdk-install.md) | Ezzel a modullal előkészítheti az Azure Stack hub-gazdaszámítógépet az Azure Stack hub virtuális merevlemez (VHD) lemezképének használatával történő üzembe helyezéséhez és újbóli üzembe helyezéséhez. | Felhőbeli operátorok|
| [Csatlakozás Azure Stack hubhoz](azure-stack-powershell-install.md) | Ezzel a modullal konfigurálhatja a VPN-kapcsolatot Azure Stack hubhoz. | Felhőalapú operátorok és felhasználók |
| [Sablon-érvényesítő](../user/azure-stack-validate-templates.md) | Ezzel a modullal ellenőrizheti, hogy van-e telepítve meglévő vagy új sablon Azure Stack hubhoz. | Felhőalapú operátorok és felhasználók|

## <a name="next-steps"></a>Következő lépések

- [Ismerkedjen meg Azure stack hub PowerShell](../user/azure-stack-powershell-overview.md)-lel.
- [Konfigurálja a Azure stack hub felhasználói PowerShell-környezetét](../user/azure-stack-powershell-configure-user.md).
- [Csatlakozás Azure stack Development Kit VPN-kapcsolaton keresztül](../asdk/asdk-connect.md).
