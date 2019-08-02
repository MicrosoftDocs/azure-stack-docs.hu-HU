---
title: Sablon üzembe helyezése a PowerShell használatával Azure Stackban | Microsoft Docs
description: Telepítsen egy sablont a PowerShell használatával Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 6824e6bfd0b6c824783c82041fb1a51ba8f5213f
ms.sourcegitcommit: 2063332b4d7f98ee944dd1f443847eea70eb5614
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68303119"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack"></a>Sablon üzembe helyezése a PowerShell használatával Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A PowerShell használatával Azure Resource Manager sablonokat telepíthet Azure Stack. Ez a cikk bemutatja, hogyan helyezhet üzembe egy sablont a PowerShell használatával.

## <a name="run-azurerm-powershell-cmdlets"></a>AzureRM PowerShell-parancsmagok futtatása

Ez a példa **AzureRM** PowerShell-parancsmagokat és egy githubon tárolt sablont használ. A sablon egy Windows Server 2012 R2 Datacenter rendszerű virtuális gépet hoz létre.

>[!NOTE]
> A példa kipróbálása előtt győződjön meg arról, hogy egy [](azure-stack-powershell-configure-user.md) Azure stack felhasználóhoz konfigurálta a PowerShellt.

1. Keresse meg a [AzureStackGitHub](https://aka.ms/AzureStackGitHub) -tárházat, és keresse meg a **101-Simple-Windows-VM** sablont. Mentse a sablont erre a helyre: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Nyisson meg egy rendszergazda jogú PowerShell-parancssort.
3. Cserélje `username` le `password` a és a parancsot a következő parancsfájlba a felhasználónevével és jelszavával, majd futtassa a szkriptet:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "local"

    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
        -NewStorageAccountName mystorage$myNum `
        -DnsNameForPublicIP mydns$myNum `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
        -VmName myVM$myNum `
        -WindowsOSVersion 2012-R2-Datacenter
    ```

    >[!IMPORTANT]
    > Minden alkalommal, amikor futtatja a szkriptet, növelje a `$myNum` paraméter értékét, hogy megakadályozza a telepítés felülírását.

4. Nyissa meg a Azure Stack portált, válassza a **Tallózás**lehetőséget, majd válassza a **virtuális gépek** lehetőséget az új virtuális gép (**myDeployment001**) megkereséséhez.

## <a name="next-steps"></a>További lépések

- [Sablon üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
