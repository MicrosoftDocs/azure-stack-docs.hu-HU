---
title: Sablon üzembe helyezése a PowerShell használatával Azure Stackban | Microsoft Docs
description: Telepítsen egy sablont a PowerShell használatával Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: af4ac82e03b96b4fc3f6d728cbebf5a6fa9d6388
ms.sourcegitcommit: e8aa26b078a9bab09c8fafd888a96785cc7abb4d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71708967"
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
3. Cserélje le a `username` és a `password` értéket a következő parancsfájlban a felhasználónevével és jelszavával, majd futtassa a szkriptet:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "yourregion" # local for the ASDK

    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > Minden alkalommal, amikor futtatja a szkriptet, növelje a `$myNum` paraméter értékét, hogy megakadályozza a telepítés felülírását.

4. Nyissa meg a Azure Stack portált, válassza a **Tallózás**lehetőséget, majd válassza a **virtuális gépek** lehetőséget az új virtuális gép (**myDeployment001**) megkereséséhez.

## <a name="cancel-a-running-template-deployment"></a>Futó sablon központi telepítésének megszakítása

Egy futó sablon központi telepítésének megszakításához használja a `Stop-AzureRmResourceGroupDeployment` PowerShell-parancsmagot.

## <a name="next-steps"></a>További lépések

- [Sablon üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
