---
title: Azure Resource Manager-sablonok használata Azure Stack hub-ban | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre erőforrásokat a Azure Stack hub Azure Resource Manager sablonjaival.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 11/14/2018
ms.openlocfilehash: 130a4f31f608368df99ec3f98fc2e2763a0fbd1a
ms.sourcegitcommit: ce01b2cd114ca8ab5b70c6311b66c58ceb054469
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75923997"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack-hub"></a>Azure Resource Manager-sablonok használata Azure Stack hub-ban

Az alkalmazás összes erőforrását egyetlen, koordinált műveletben használhatja Azure Resource Manager sablonokkal. Az erőforráscsoportok erőforrásainak módosítására is lehetősége van a sablonok újbóli üzembe helyezésére.

Ezek a sablonok a Microsoft Azure Stack hub portál, a PowerShell, a parancssor és a Visual Studio használatával telepíthetők.

A GitHubon a következő rövid útmutató-sablonok [érhetők el](https://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>A SharePoint Server telepítése (nem magas rendelkezésre állású telepítés)

A következő erőforrásokat tartalmazó [SharePoint Server 2013-Farm létrehozásához](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha) használja a PowerShell [desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) bővítményét:

* Egy virtuális hálózatot
* Három Storage-fiók
* Két külső terheléselosztó
* Egy virtuális gép (VM) tartományvezérlőként van konfigurálva egy új erdőhöz egyetlen tartománnyal
* Egy virtuális gép SQL Server 2014 önálló kiszolgálóként van konfigurálva
* Egy virtuális gép, amely egy-számítógép SharePoint Server 2013-farmként van konfigurálva

## <a name="deploy-ad-non-high-availability-deployment"></a>AD üzembe helyezése (nem magas rendelkezésre állású központi telepítés)

A PowerShell DSC bővítmény használatával [hozzon létre egy ad tartományvezérlő kiszolgálót](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha) , amely a következő erőforrásokat tartalmazza:

* Egy virtuális hálózatot
* Egy Storage-fiók
* Egy külső Load Balancer
* Egy virtuális gép (VM) tartományvezérlőként van konfigurálva egy új erdőhöz egyetlen tartománnyal

## <a name="deploy-adsql-non-high-availability-deployment"></a>AD/SQL üzembe helyezése (nem magas rendelkezésre állású központi telepítés)

A PowerShell DSC bővítmény használatával [hozzon létre egy SQL Server 2014 önálló kiszolgálót](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha) , amely a következő erőforrásokat tartalmazza:

* Egy virtuális hálózatot
* Két Storage-fiók
* Egy külső Load Balancer
* Egy virtuális gép (VM) tartományvezérlőként van konfigurálva egy új erdőhöz egyetlen tartománnyal
* Egy virtuális gép SQL Server 2014 önálló kiszolgálóként van konfigurálva

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-bővítmény-Azure-Automation-pull-Server

A PowerShell DSC bővítmény használatával konfigurálhat egy meglévő virtuális gép helyi Configuration Manager (LCD ChipOnGlas), és regisztrálhatja azt egy Azure Automation-fiók DSC lekérési kiszolgálójára.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Virtuális gép létrehozása felhasználói rendszerképből

[Hozzon létre egy virtuális gépet egy egyéni felhasználói rendszerképből](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-create-from-customimage). Ez a sablon egy virtuális hálózatot (DNS-t), egy nyilvános IP-címet és egy hálózati adaptert is üzembe helyez.

## <a name="basic-virtual-machine"></a>Alapszintű virtuális gép

[Helyezzen üzembe egy olyan Windows rendszerű virtuális gépet](https://aka.ms/aa6zdzx) , amely tartalmaz egy virtuális hálózatot (DNS), egy nyilvános IP-címet és egy hálózati adaptert.

## <a name="cancel-a-running-template-deployment"></a>Futó sablon központi telepítésének megszakítása

Egy futó sablon központi telepítésének megszakításához használja a [stop-AzureRmResourceGroupDeployment PowerShell-](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment) [parancsmagot](/powershell/scripting/developer/cmdlet/cmdlet-overview).

## <a name="next-steps"></a>Következő lépések

* [Sablonok üzembe helyezése a portállal](azure-stack-deploy-template-portal.md)
* [Sablonok üzembe helyezése a PowerShell-lel](azure-stack-deploy-template-powershell.md)
* [Sablonok üzembe helyezése a Visual Studióval](azure-stack-deploy-template-visual-studio.md)
* [Az Azure Resource Manager áttekintése](/azure/azure-resource-manager/resource-group-overview)
