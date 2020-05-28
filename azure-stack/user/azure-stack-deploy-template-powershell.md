---
title: Sablon üzembe helyezése a PowerShell használatával Azure Stack hub-ban
description: Sablon üzembe helyezése a PowerShell használatával Azure Stack hub-ban.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: 7c8c27374b7ba114dc09297c2f0112d3f0f9579d
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84112161"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack-hub"></a>Sablon üzembe helyezése a PowerShell használatával Azure Stack hub-ban

A PowerShell használatával Azure Resource Manager sablonokat telepíthet Azure Stack hubhoz. Ez a cikk bemutatja, hogyan helyezhet üzembe egy sablont a PowerShell használatával.

## <a name="run-azurerm-powershell-cmdlets"></a>AzureRM PowerShell-parancsmagok futtatása

Ez a példa **AzureRM** PowerShell-parancsmagokat és egy githubon tárolt sablont használ. A sablon egy Windows Server 2012 R2 Datacenter rendszerű virtuális gépet hoz létre.

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

4. Nyissa meg az Azure Stack hub portált, válassza a **Tallózás**lehetőséget, majd válassza a **virtuális gépek** lehetőséget az új virtuális gép (**myDeployment001**) megkereséséhez.

## <a name="cancel-a-running-template-deployment"></a>Futó sablon központi telepítésének megszakítása

Egy futó sablon központi telepítésének megszakításához használja a `Stop-AzureRmResourceGroupDeployment` PowerShell-parancsmagot.

## <a name="next-steps"></a>Következő lépések

- [Sablon üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
