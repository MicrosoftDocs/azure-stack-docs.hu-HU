---
title: Azure Stack-eszközök letöltése a githubról |} A Microsoft Docs
description: Ismerje meg, hogyan töltse le az Azure Stack használatához szükséges eszközöket.
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
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 722fea2777071fa78c89372a21fe52022a56f264
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782256"
---
# <a name="download-azure-stack-tools-from-github"></a>Azure Stack-eszközök letöltése a githubról

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

**1.2.9-es-eszközök** van egy [GitHub-adattár](https://github.com/Azure/AzureStack-Tools) kezelésére és üzembe erőforrásokat az Azure Stack PowerShell-modulok helyadatbázist. Ha azt tervezi, VPN-kapcsolatot létesíteni, letöltheti ezek a PowerShell-modulok, az Azure Stack fejlesztői készletet, vagy egy Windows-alapú külső ügyfél. Ezek az eszközök beszerzése, klónozza a GitHub-adattárát, vagy töltse le a **AzureStack-eszközök** mappában a következő szkript futtatásával:

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

## <a name="functionality-provided-by-the-modules"></a>A modulok által biztosított funkciókat

A **AzureStack-eszközök** tárház, amely támogatja a következő funkciók az Azure Stack PowerShell-modulokat tartalmaz:  

| Funkció | Leírás | Ez a modul ki tudja használni? |
| --- | --- | --- |
| [A felhőalapú képességek](../user/azure-stack-validate-templates.md) | Ez a modul használatával a felhő felhő képességeit. Ha például a modul használatával kaphat a felhőalapú képességek, például az API-verzió és az Azure Resource Manager-erőforrásokat. Ez a modul használatával Azure Stack és az Azure-felhőket is beszerezheti a Virtuálisgép-bővítmények. | Felhő üzemeltetőinek és a felhasználók |
| [Az Azure Stack Resource Manager-házirend](../user/azure-stack-policy-module.md) | Ez a modul használatával Azure-előfizetésre vagy Azure-erőforrás konfigurálása az Azure Stack, azonos verziókezelés és szolgáltatás rendelkezésre. | Felhő üzemeltetőinek és a felhasználók |
| [Regisztráljon az Azure-ral](azure-stack-registration.md ) | Ez a modul segítségével a development kit példány regisztrálja az Azure-ral. A regisztrálás után a marketplace-elemek letöltése az Azure-ból, és használja őket az Azure Stackben. | Felhő üzemeltetői |
| [Az Azure Stack üzembe helyezése](../asdk/asdk-install.md) | Ez a modul üzembe helyezése és ismételt üzembe helyezése az Azure Stack virtuális merevlemez (VHD) lemezképet használja az Azure Stack gazdaszámítógép segítségével. | Felhő üzemeltetői|
| [Csatlakozás az Azure Stackhez](azure-stack-powershell-install.md) | Ez a modul segítségével konfigurálhatja a VPN-kapcsolat Azure stackhez. | Felhő üzemeltetőinek és a felhasználók |
| [Sablon-érvényesítő](../user/azure-stack-validate-templates.md) | Ez a modul segítségével ellenőrizheti, ha egy meglévő vagy új sablont is üzembe helyezhetők az Azure Stackhez. | Felhő üzemeltetőinek és a felhasználók|


## <a name="next-steps"></a>További lépések

- [Az Azure Stack PowerShell használatának első lépései](../user/azure-stack-powershell-overview.md)
- [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](../user/azure-stack-powershell-configure-user.md)   
- [Csatlakozás az Azure Stack Development Kit egy VPN-kapcsolaton keresztül](../asdk/asdk-connect.md)  
