---
title: Sablon üzembe helyezése a PowerShell használatával Azure Stack hub-ban
description: Sablon üzembe helyezése a PowerShell használatával Azure Stack hub-ban.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: a5bd582cd93a95f662a8acc2094e6a62a7ecdf50
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546905"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack-hub"></a>Sablon üzembe helyezése a PowerShell használatával Azure Stack hub-ban

A PowerShell használatával Azure Resource Manager sablonokat telepíthet Azure Stack hubhoz. Ez a cikk bemutatja, hogyan helyezhet üzembe egy sablont a PowerShell használatával.

## <a name="run-az-powershell-cmdlets"></a>Futtatás az PowerShell parancsmagok

Ez a példa **az az** PowerShell-parancsmagokat és a githubon tárolt sablont használja. A sablon egy Windows Server 2012 R2 Datacenter rendszerű virtuális gépet hoz létre.

>[!NOTE]
> A példa kipróbálása előtt ellenőrizze, hogy konfigurálta-e a [PowerShellt](azure-stack-powershell-configure-user.md) egy Azure stack hub-felhasználóhoz.

1. Keresse meg a [AzureStack-Gyorsindítás-sablonok](https://aka.ms/AzureStackGitHub) tárházat, és keresse meg a **101-Simple-Windows-VM** sablont. Mentse a sablont erre a helyre: `C:\templates\azuredeploy-101-simple-windows-vm.json` .
2. Nyisson meg egy emelt szintű PowerShell-parancssort.
3. Cserélje le a `username` és a `password` parancsot a következő parancsfájlba a felhasználónevével és jelszavával, majd futtassa a parancsfájlt:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "yourregion" # local for the ASDK

    # Create resource group for template deployment
    New-AzResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > Minden alkalommal, amikor futtatja a szkriptet, növelje a `$myNum` paraméter értékét, hogy megakadályozza a telepítés felülírását.

4. Nyissa meg az Azure Stack hub portált, válassza a **Tallózás** lehetőséget, majd válassza a  **virtuális gépek** lehetőséget az új virtuális gép ( **myDeployment001** ) megkereséséhez.

## <a name="cancel-a-running-template-deployment"></a>Futó sablon központi telepítésének megszakítása

Egy futó sablon központi telepítésének megszakításához használja a `Stop-AzResourceGroupDeployment` PowerShell-parancsmagot.

## <a name="next-steps"></a>Következő lépések

- [Sablon üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
