---
title: A Docker használatával futtassa a Powershellt az Azure Stackben |} A Microsoft Docs
description: Az Azure Stack PowerShell futtatása a Docker használatával
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
ms.date: 07/09/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 07/09/2019
ms.openlocfilehash: 27f2b4c1817c28cf5d345f5aa9387a26cd18316b
ms.sourcegitcommit: d2df594e8346a875967e3cfb04c23562a1bd2e3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67725749"
---
# <a name="use-docker-to-run-powershell-in-azure-stack"></a>Az Azure Stack PowerShell futtatása a Docker használatával

Ez a cikk a Docker, amelyen szeretné, a PowerShell használata a különböző felületek szükséges verzióját futtató Windows-alapú tárolók létrehozásához használhatja. Docker Windows-alapú tárolók kell használnia.

## <a name="docker-prerequisites"></a>Docker-Előfeltételek

1. Telepítés [Docker](https://docs.docker.com/install/).

1. Parancssori program, például a Powershell vagy a Bash adja meg:

    ```bash
        Docker --version
    ```

1. Docker futtatása szükséges a Windows 10-es Windows-tárolók használatával kell. Amikor futtatja a Docker, Windows-tárolók váltani.

1. Futtassa a Docker Azure Stack azonos tartományhoz csatlakozó gépről. Ha az Azure Stack Development Kit (ASDK) használ, telepítenie kell [a távoli gépen a VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="set-up-a-service-principal-for-using-powershell"></a>Egyszerű szolgáltatás beállítása a PowerShell használatával

Erőforrások eléréséhez az Azure Stack PowerShell használatához szüksége egy egyszerű szolgáltatást az Azure Active Directory (Azure AD-) bérlőben. A felhasználói szerepkör alapú hozzáférés-vezérlés (RBAC) engedélyekkel delegálhat.

1. Az egyszerű szolgáltatás beállításához, kövesse a [alkalmazások hozzáférést biztosíthat az Azure Stack-erőforrások egyszerű szolgáltatások létrehozásával](azure-stack-create-service-principals.md).

2. Jegyezze fel az Alkalmazásazonosítót, a titkos kulcsot és a bérlő Azonosítóját későbbi felhasználásra.

## <a name="docker---azure-stack-api-profiles-module"></a>Docker – az Azure Stack API profilok modul

A docker-fájl megnyitása a Microsoft kép *microsoft/windowsservercore*, amely rendelkezik a Windows PowerShell 5.1 telepítve. A fájlt, majd betölti a NuGet és az Azure Stack PowerShell-modulok, és letölti az eszközök az Azure Stack elől.

1. [Töltse le az azure-verem – powershell-adattáron](https://github.com/mattbriggs/azure-stack-powershell) ZIP-fájlt, vagy a tárház klónozása.

2. Nyissa meg a tárház mappát a terminálról.

3. A tárházban nyissa meg egy parancssori felületet, és írja be a következő parancsot:

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. Amikor a lemezkép állították össze, először egy interaktív tárolóba írja be:

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. A rendszerhéj a parancsmagok készen áll.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Csatlakozás az Azure Stack-példány az egyszerű szolgáltatás használatával. Most már használ egy PowerShell-parancssort a Docker. 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell a fiók objektumot ad vissza:

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Tesztelje a kapcsolatot hoz létre egy erőforráscsoportot az Azure Stackben.

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>További lépések

-  Olvassa el az áttekintést [az Azure Stack PowerShell az Azure Stackben](azure-stack-powershell-overview.md).
- További információ [PowerShell API-profilok](azure-stack-version-profiles.md) az Azure Stackben.
- Telepítés [az Azure Stack Powershell](../operator/azure-stack-powershell-install.md).
- Olvassa el létrehozása [Azure Resource Manager-sablonok](azure-stack-develop-templates.md) felhőalapú konzisztencia.
