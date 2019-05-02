---
title: Docker használata az Azure Stack PowerShell futtatásához |} A Microsoft Docs
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
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 0eacd2c5a058bca68e86f2d34df8d3cc91987c1c
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985541"
---
# <a name="use-docker-to-run-powershell"></a>Futtassa a Powershellt a Docker használatával

A Docker használatával, amelyeken futtatni szeretné a PowerShell verzióját a különböző felületek használatához szükséges Windows-alapú tárolót hoz létre. Windows-alapú tárolók docker kell használnia.

## <a name="docker-prerequisites"></a>Docker-Előfeltételek

1. Telepítés [Docker](https://docs.docker.com/install/).
2. Nyisson meg egy parancssor, Powershell vagy a Bash és a típusa:

    ```bash
        Docker -version
    ```

3. Docker, Windows 10-es igénylő Windows-tárolók használatával futtatni szeretné. Amikor futtatja a Docker, Windows-tárolók váltani.

4. Docker Azure Stack azonos tartományhoz csatlakoztatott számítógépről kell futtatni. Ha a ASDK használ, telepítenie kell [a távoli gépen a VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="service-principals-for-using-powershell"></a>Az egyszerű szolgáltatások a PowerShell használatával

Egy egyszerű szolgáltatást kell a PowerShell használatával hozzáférés-erőforrást az Azure Stack az Azure AD-bérlőhöz. Felhasználó szerepköralapú hozzáférés-vezérlési engedélyek delegálhat.

1. Állítsa be az egyszerű, a cikk utasításait követve [alkalmazások hozzáférést biztosíthat az Azure Stack-erőforrások egyszerű szolgáltatások létrehozásával](azure-stack-create-service-principals.md).
2. Jegyezze fel az Alkalmazásazonosítót, a titkos kulcsot, és a bérlő azonosítója.

## <a name="docker---azure-stack-api-profiles-module"></a>Docker - modul az Azure Stack API profilok

A docker-fájl megnyitása a Microsoft lemezkép a microsoft/windowsservercore, amelyen telepítve van a Windows PowerShell 5.1. A fájlt, majd betölti a NuGet, az Azure Stack PowerShell-modulok, és az eszközök tölt le az Azure Stack-eszközök.

1. Ez a tárház zip letöltése, vagy klónozza a tárházat:  
[https://github.com/mattbriggs/azure-stack-powershell](https://github.com/mattbriggs/azure-stack-powershell)

2. Nyissa meg a tárház mappát a terminálról.

3. Nyissa meg a parancssori felület a tárházat, és írja be:

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. A kép állították össze, egy interaktív tároló elindításához. Típus:

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. A rendszerhéj a parancsmagok készen áll.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Csatlakozás az Azure Stack, az egyszerű szolgáltatás használatával. Most már használ egy PowerShell-parancssort a Docker. 

    ```Powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell a fiók objektumot ad vissza:

    ```PowerShell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Tesztelje a kapcsolatot hoz létre egy erőforráscsoportot az Azure Stackben.

    ```PowerShell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>További lépések

-  Olvassa el az áttekintést [az Azure Stack PowerShell az Azure Stacken](azure-stack-powershell-overview.md).
- További információ [PowerShell API-profilok](azure-stack-version-profiles.md) az Azure Stackben.
- [Az Azure Stack Powershell telepítésének](../operator/azure-stack-powershell-install.md).
- Olvassa el létrehozása [Azure Resource Manager-sablonok](azure-stack-develop-templates.md) felhőalapú konzisztencia.