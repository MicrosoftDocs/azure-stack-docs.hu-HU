---
title: A Docker használata a PowerShell futtatásához Azure Stack hub-ban | Microsoft Docs
description: A Docker használata a PowerShell futtatásához Azure Stack központban
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
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 07/09/2019
ms.openlocfilehash: e55fd18babea30d0b004c1219d8ce4842f4750bd
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75819471"
---
# <a name="use-docker-to-run-powershell-in-azure-stack-hub"></a>A Docker használata a PowerShell futtatásához Azure Stack központban

Ebben a cikkben a Docker használatával hozhat létre Windows-alapú tárolókat, amelyeken futtathatja a PowerShell azon verzióját, amely a különböző felületeken való munkához szükséges. A Docker-ben Windows-alapú tárolókat kell használnia.

## <a name="docker-prerequisites"></a>A Docker előfeltételei

1. A [Docker](https://docs.docker.com/install/)telepítése.

1. Egy parancssori programban, például a PowerShellben vagy a bash-ben írja be a következőket:

    ```bash
        Docker --version
    ```

1. A Docker futtatásához Windows 10 rendszerű Windows-tárolókat kell használnia. A Docker futtatásakor váltson a Windows-tárolók elemre.

1. Futtassa a Docker-t egy olyan gépről, amely ugyanahhoz a tartományhoz csatlakozik, mint Azure Stack hub. Ha a Azure Stack Development Kit (ASDK) használja, telepítenie kell [a VPN-t a távoli gépen](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn).

## <a name="set-up-a-service-principal-for-using-powershell"></a>Egyszerű szolgáltatásnév beállítása a PowerShell használatához

Ahhoz, hogy a PowerShell használatával hozzáférhessen a Azure Stack hub erőforrásaihoz, szüksége lesz egy egyszerű szolgáltatásnév használatára a Azure Active Directory-(Azure AD-) bérlőben. Engedélyeket delegálhat a felhasználói szerepköralapú hozzáférés-vezérléssel (RBAC).

1. Az egyszerű szolgáltatás beállításához kövesse az [alkalmazások Azure stack hub-erőforrásokhoz való hozzáférésének biztosítása az egyszerű szolgáltatások létrehozásával](azure-stack-create-service-principals.md)című témakör utasításait.

2. Jegyezze fel az alkalmazás AZONOSÍTÓját, a titkos kulcsot és a bérlő AZONOSÍTÓját későbbi használatra.

## <a name="docker---azure-stack-hub-api-profiles-module"></a>Docker-Azure Stack hub API-profilok modul

A Docker megnyitja a Microsoft */Windowsservercore Microsoft-* rendszerképét, amelyen telepítve van a Windows PowerShell 5,1. A fájl ezután betölti a NuGet és a Azure Stack hub PowerShell-modulokat, és letölti az eszközöket Azure Stack Hub-eszközökről.

1. [Töltse le az Azure-stack-PowerShell-tárházat](https://github.com/mattbriggs/azure-stack-powershell) zip-fájlként, vagy az adattár klónozásával.

2. Nyissa meg a tárház mappát a terminálon.

3. Nyisson meg egy parancssori felületet a tárházban, majd írja be a következő parancsot:

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. A rendszerkép létrehozása után indítson el egy interaktív tárolót az alábbiak beírásával:

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. A rendszerhéj készen áll a parancsmagokra.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Kapcsolódjon a Azure Stack hub-példányhoz az egyszerű szolgáltatásnév használatával. Most egy PowerShell-parancssort használ a Docker-ben. 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   A PowerShell visszaadja a fiók objektumát:

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. A kapcsolat teszteléséhez hozzon létre egy erőforráscsoportot az Azure Stack hub-ban.

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>Következő lépések

-  Tekintse át az [Azure stack hub PowerShell Azure stack hub-](azure-stack-powershell-overview.md)beli áttekintését.
- További információ a [PowerShell API-profiljairól](azure-stack-version-profiles.md) Azure stack hub-ban.
- Telepítse [Azure stack hub PowerShellt](../operator/azure-stack-powershell-install.md).
- További információ a Felhőbeli konzisztencia [Azure Resource Manager-sablonok](azure-stack-develop-templates.md) létrehozásáról.
