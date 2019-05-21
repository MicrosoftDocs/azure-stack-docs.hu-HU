---
title: Az Azure Resource Manager-sablonok használata az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure Resource Manager-sablonok az Azure Stackben erőforrások kiépítése.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 11/14/2018
ms.openlocfilehash: fb39928b50e6cce35d5c35c33a95fc2c6b82c160
ms.sourcegitcommit: d2012e765c3fa5bccb4756d190349e890f9f48bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65941130"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Az Azure Resource Manager-sablonok használata az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Resource Manager-sablonok segítségével telepítése és jogosultságok kiosztása összes erőforrását egyetlen, koordinált műveletben az alkalmazáshoz. Telepítse újra a sablonok az erőforrásokat egy erőforráscsoportba tartozó módosíthatja is.

Ezeket a sablonokat is üzembe helyezhetők a Microsoft Azure Stack portal, PowerShell, a parancssor és a Visual Studio.

A következő gyorsindítási sablonok érhetők el a [GitHub](https://aka.ms/azurestackgithub):


## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>A SharePoint Server (nem – magas rendelkezésre állású üzembe helyezés) üzembe helyezése

A PowerShell-lel [Desired State Configuration](https://docs.microsoft.com/powershell/dsc/overview/overview) (DSC) bővítmény [hozzon létre egy SharePoint Server 2013-farmban](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha) , amely tartalmazza az alábbi forrásanyagokat:

* Virtuális hálózat
* Három tárfiókot tartalmaz
* Két a külső terheléselosztók
* Egy virtuális gép (VM) egy új erdő egyetlen tartományt a tartományvezérlővel konfigurálva
* Egy virtuális Gépet egy önálló SQL Server 2014-kiszolgáló
* Egy virtuális Gépet egy egy gépen a SharePoint Server 2013-farm

## <a name="deploy-ad-non-high-availability-deployment"></a>(Nem nagy – rendelkezésre állás – telepítési) AD üzembe helyezése

Használja a PowerShell DSC bővítmény [hozzon létre egy AD domain controller-kiszolgáló](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha) , amely tartalmazza az alábbi forrásanyagokat:

* Virtuális hálózat
* Egy storage-fiók
* Egy külső terheléselosztó
* Egy virtuális gép (VM) egy új erdő egyetlen tartományt a tartományvezérlővel konfigurálva

## <a name="deploy-adsql-non-high-availability-deployment"></a>Deploy AD/SQL (non-high-availability-deployment)

Használja a PowerShell DSC bővítmény [hozzon létre egy SQL Server 2014 önálló kiszolgáló](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha) , amely tartalmazza az alábbi forrásanyagokat:

* Virtuális hálózat
* Két tárfiókot
* Egy külső terheléselosztó
* Egy virtuális gép (VM) egy új erdő egyetlen tartományt a tartományvezérlővel konfigurálva
* Egy virtuális Gépet egy önálló SQL Server 2014-kiszolgáló

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

A PowerShell DSC bővítmény segítségével egy meglévő virtuális gép helyi Configuration Manager (LCM) konfigurálása és a egy Azure Automation fiókhoz DSC lekéréses kiszolgálón való bejegyzéséhez.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Virtuális gép létrehozása felhasználói rendszerképből

[Virtuális gép létrehozása egyéni felhasználói rendszerképből](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-create-from-customimage). Ez a sablon is üzembe helyez egy virtuális hálózatot (a DNS-), a nyilvános IP-cím és a egy hálózati adapter.

## <a name="basic-virtual-machine"></a>Alapszintű virtuális gép

[Windows virtuális gép üzembe helyezése](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm) , amely egy virtuális hálózatot (a DNS-), a nyilvános IP-cím és a egy hálózati adaptert tartalmaz.

## <a name="cancel-a-running-template-deployment"></a>Mégse sablon üzemelő példányban

Mégse sablon üzemelő példányban, használja a [Stop-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment) PowerShell [parancsmag](https://docs.microsoft.com/powershell/developer/cmdlet/cmdlet-overview).

## <a name="next-steps"></a>További lépések

* [Sablonok üzembe helyezése a portállal](azure-stack-deploy-template-portal.md)
* [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
* [Sablonok üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
* [Az Azure Resource Manager áttekintése](/azure/azure-resource-manager/resource-group-overview)
