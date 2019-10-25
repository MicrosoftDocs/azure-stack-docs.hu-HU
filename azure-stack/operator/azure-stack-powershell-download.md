---
title: Azure Stack Tools letöltése a GitHubról | Microsoft Docs
description: Megtudhatja, hogyan töltheti le a Azure Stack használatához szükséges eszközöket.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 09/18/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/18/2019
ms.openlocfilehash: 89a31f6d7894ed9af710263298fef03604d1351b
ms.sourcegitcommit: acebda8a42ac8ecdeba490fc1738e9041479dab0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72813987"
---
# <a name="download-azure-stack-tools-from-github"></a>Azure Stack Tools letöltése a GitHubról

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A **AzureStack-Tools** egy [GitHub-tárház](https://github.com/Azure/AzureStack-Tools) , amely PowerShell-modulokat tartalmaz az erőforrások Azure stack történő kezeléséhez és üzembe helyezéséhez. Ha VPN-kapcsolat létesítését tervezi, letöltheti ezeket a PowerShell-modulokat a Azure Stack Development Kitba (ASDK) vagy egy Windows-alapú külső ügyfélre. Az eszközök beszerzéséhez klónozott a GitHub-tárházat, vagy töltse le a **AzureStack-Tools** mappát a következő parancsfájl futtatásával:

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

## <a name="functionality-provided-by-the-modules"></a>A modulok által biztosított funkciók

A **AzureStack-Tools** adattár PowerShell-modulokkal rendelkezik, amelyek támogatják a Azure stack következő funkcióit:  

| Funkció | Leírás | Kik használhatják ezt a modult? |
| --- | --- | --- |
| [Felhőbeli képességek](../user/azure-stack-validate-templates.md) | Ez a modul a felhő Felhőbeli képességeinek beszerzésére használható. Például olyan Felhőbeli funkciókat érhet el, mint az API-verzió és a Azure Resource Manager erőforrás. A virtuálisgép-bővítmények Azure Stack és az Azure-felhőkhez is beszerezhetők. | Felhőalapú operátorok és felhasználók |
| [Resource Manager-házirend a Azure Stack](../user/azure-stack-policy-module.md) | Ezzel a modullal konfigurálhat egy Azure-előfizetést vagy egy Azure-erőforráscsoportot ugyanazzal a verziószámozással és szolgáltatással, mint Azure Stack. | Felhőalapú operátorok és felhasználók |
| [Regisztrálás az Azure-ban](azure-stack-registration.md ) | Ezzel a modullal regisztrálhat ASDK-példányát az Azure-ban. A regisztráció után letöltheti az Azure Marketplace-elemeket Azure Stack. | Felhőbeli operátorok |
| [Azure Stack üzemelő példány](../asdk/asdk-install.md) | Ezzel a modullal előkészítheti a Azure Stack gazdagépet a Azure Stack virtuális merevlemez (VHD) lemezkép használatával történő üzembe helyezéshez és újbóli üzembe helyezéshez. | Felhőbeli operátorok|
| [Csatlakozás a Azure Stackhoz](azure-stack-powershell-install.md) | Ezzel a modullal konfigurálhatja a VPN-kapcsolatot Azure Stackhoz. | Felhőalapú operátorok és felhasználók |
| [Sablon-érvényesítő](../user/azure-stack-validate-templates.md) | Ezzel a modullal ellenőrizheti, hogy van-e telepítve meglévő vagy új sablon Azure Stack. | Felhőalapú operátorok és felhasználók|

## <a name="next-steps"></a>Következő lépések

- [Ismerkedés a PowerShell-lel Azure stackon](../user/azure-stack-powershell-overview.md).
- [Konfigurálja a Azure stack felhasználó PowerShell-környezetét](../user/azure-stack-powershell-configure-user.md).
- [Csatlakozás Azure stack Development Kit VPN-kapcsolaton keresztül](../asdk/asdk-connect.md).
