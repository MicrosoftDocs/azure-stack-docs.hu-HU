---
title: Azure Stack hub-eszközök letöltése a GitHubról | Microsoft Docs
description: Megtudhatja, hogyan töltheti le az Azure Stack hub használatához szükséges eszközöket.
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
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 4f60d3dc3ae22dd1265b9bd2b0ce86145720042c
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534532"
---
# <a name="download-azure-stack-hub-tools-from-github"></a>Azure Stack hub-eszközök letöltése a GitHubról

A **AzureStack-Tools** egy [GitHub-tárház](https://github.com/Azure/AzureStack-Tools) , amely PowerShell-modulokat üzemeltet az erőforrások Azure stack hubhoz való felügyeletéhez és üzembe helyezéséhez. Ha VPN-kapcsolat létesítését tervezi, letöltheti ezeket a PowerShell-modulokat a Azure Stack Development Kitba (ASDK) vagy egy Windows-alapú külső ügyfélre. Az eszközök beszerzéséhez klónozott a GitHub-tárházat, vagy töltse le a **AzureStack-Tools** mappát a következő parancsfájl futtatásával:

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

A **AzureStack-Tools** adattár PowerShell-modulokkal rendelkezik, amelyek támogatják az Azure stack hub következő funkcióit:  

| Funkció | Leírás | Kik használhatják ezt a modult? |
| --- | --- | --- |
| [Felhőbeli képességek](../user/azure-stack-validate-templates.md) | Ez a modul a felhő Felhőbeli képességeinek beszerzésére használható. Például olyan Felhőbeli funkciókat érhet el, mint az API-verzió és a Azure Resource Manager erőforrás. Azure Stack hub és az Azure-felhők virtuálisgép-bővítményeit is lekérheti. | Felhőalapú operátorok és felhasználók |
| [Resource Manager-szabályzat Azure Stack hubhoz](../user/azure-stack-policy-module.md) | Ezzel a modullal konfigurálhat egy Azure-előfizetést vagy egy Azure-erőforráscsoportot ugyanazzal a verziószámozással és szolgáltatással, mint Azure Stack hub. | Felhőalapú operátorok és felhasználók |
| [Regisztrálás az Azure-ban](azure-stack-registration.md ) | Ezzel a modullal regisztrálhat ASDK-példányát az Azure-ban. A regisztráció után letöltheti az Azure Marketplace-elemeket Azure Stack központban. | Felhőbeli operátorok |
| [Azure Stack hub üzembe helyezése](../asdk/asdk-install.md) | Ezzel a modullal előkészítheti az Azure Stack hub-gazdaszámítógépet az Azure Stack hub virtuális merevlemez (VHD) lemezképének használatával történő üzembe helyezéséhez és újbóli üzembe helyezéséhez. | Felhőbeli operátorok|
| [Csatlakozás Azure Stack hubhoz](azure-stack-powershell-install.md) | Ezzel a modullal konfigurálhatja a VPN-kapcsolatot Azure Stack hubhoz. | Felhőalapú operátorok és felhasználók |
| [Sablon-érvényesítő](../user/azure-stack-validate-templates.md) | Ezzel a modullal ellenőrizheti, hogy van-e telepítve meglévő vagy új sablon Azure Stack hubhoz. | Felhőalapú operátorok és felhasználók|

## <a name="next-steps"></a>Következő lépések

- [Ismerkedjen meg Azure stack hub PowerShell](../user/azure-stack-powershell-overview.md)-lel.
- [Konfigurálja a Azure stack hub felhasználói PowerShell-környezetét](../user/azure-stack-powershell-configure-user.md).
- [Csatlakozás Azure stack Development Kit VPN-kapcsolaton keresztül](../asdk/asdk-connect.md).
