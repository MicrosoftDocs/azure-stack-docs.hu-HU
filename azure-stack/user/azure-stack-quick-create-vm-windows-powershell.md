---
title: A Windows Server rendszerű virtuális gép létrehozása az Azure Stack PowerShell használatával |} A Microsoft Docs
description: A Windows Server rendszerű virtuális gép létrehozása a PowerShell-lel az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 1b0f367540012b86da322329f0536b3c484c39b4
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269564"
---
# <a name="quickstart-create-a-windows-server-vm-by-using-powershell-in-azure-stack"></a>Gyors útmutató: A Windows Server rendszerű virtuális gép létrehozása az Azure Stack PowerShell használatával

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Létrehozhat egy Windows Server 2016 virtuális gép (VM) az Azure Stack PowerShell használatával. Kövesse a cikkben hozhat létre és használhat egy virtuális Gépet. Ez a cikk is biztosít a lépéseket:

* Csatlakozzon a virtuális géphez a távoli ügyfélhez.
* Az IIS-webkiszolgáló telepítéséhez, és az alapértelmezett kezdőlap megtekintéséhez.
* Az erőforrások törlése.

> [!NOTE]
>  Ha egy VPN-kapcsolaton keresztül kapcsolódik, ez a cikk az Azure Stack fejlesztői készletet, vagy a külső ügyfél Windows-alapú leírt lépések futtatásával.

## <a name="prerequisites-for-windows-server-vm"></a>Windows Server virtuális gép előfeltételei

* Győződjön meg arról, hogy az Azure Stack-operátorokról e hozzáadni a **Windows Server 2016** lemezképet az Azure Stack piactéren.

* Az Azure Stack egy adott verzióját az Azure PowerShell-lel az erőforrások létrehozásához és kezeléséhez szükséges. Ha nincs konfigurálva az Azure Stack PowerShell, kövesse a lépéseket [telepítése](../operator/azure-stack-powershell-install.md) PowerShell.

* Az Azure Stack PowerShell használatával állítsa be szüksége lesz az Azure Stack környezettel való kapcsolódáshoz. További útmutatást lásd: [csatlakozhat az Azure Stack PowerShell felhasználói](azure-stack-powershell-configure-user.md).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Egy erőforráscsoport olyan logikai tároló, amely az Azure Stack erőforrások üzembe helyezése és felügyelt. A fejlesztői készlet vagy az Azure Stackkel integrált rendszereknél futtassa a következő kódblokk egy erőforráscsoport létrehozásához. 

> [!NOTE]
> A Kódminták minden változóját értékek vannak hozzárendelve. Megadhat azonban új értéket, ha szeretné.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Tároló-erőforrások létrehozása

Hozzon létre egy tárfiókot és a egy storage-tároló, a Windows Server 2016-ban tárolja.

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

```

## <a name="create-networking-resources"></a>Hálózati erőforrások létrehozása

Hozzon létre egy virtuális hálózatot, egy alhálózatot és egy nyilvános IP-címet. Ezeket az erőforrásokat a virtuális gép hálózati csatlakoztatásának biztosítására szolgálnak.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Hálózati biztonsági csoport és hálózati biztonsági csoportszabály létrehozása

A hálózati biztonsági csoport bejövő és kimenő szabályok használatával teszi biztonságossá a virtuális Gépet. Hozzunk létre egy bejövő szabályt a 3389-es bejövő távoli asztali kapcsolatok engedélyezéséhez és a egy bejövő szabály a bejövő webes forgalom engedélyezéséhez a 80-as porton.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-vm"></a>Hozzon létre egy hálózati kártyát a virtuális gép

A hálózati kártyát a virtuális gép csatlakozik egy alhálózat, a hálózati biztonsági csoport és a nyilvános IP-címet.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy Virtuálisgép-konfigurációt. Ez a konfiguráció a virtuális gép üzembe helyezése során használt beállításokat tartalmaz. Például: hitelesítő adatok, mérete és a Virtuálisgép-lemezkép.

```powershell
# Define a credential object to store the username and password for the VM
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the VM configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

# Sets the operating system disk properties on a VM.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzureRmVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzureRmVMNetworkInterface -Id $nic.Id


# Create the VM.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Az előző lépésben létrehozott virtuális gép távolról, a nyilvános IP-címének kell. A következő parancsot a virtuális gép nyilvános IP-címének lekéréséhez:

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```

A következő paranccsal hozzon létre egy távoli asztali munkamenetet a virtuális géppel. Cserélje le az IP-címet a virtuális gép *publicIPAddress* címére. Amikor a rendszer kéri, adja meg a felhasználónevet és jelszót a virtuális gép létrehozásakor használt.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>IIS telepítése a PowerShell használatával

Most, hogy már bejelentkezett az Azure virtuális géphez, egyetlen sor PowerShell segítségével az IIS telepítése és engedélyezése a helyi tűzfalszabály átengedje a webforgalmat. Nyisson meg egy PowerShell-parancssort, és futtassa a következő parancsot:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Az IIS kezdőlapjának megtekintése

A telepített IIS-t, és nyissa meg a virtuális Gépen a 80-as porton bármely böngészőben használhatja az alapértelmezett IIS kezdőlapjának megtekintése. Használja a *publicIpAddress* az előző szakaszban, és látogasson el az alapértelmezett oldalt a dokumentált.

![Alapértelmezett IIS-webhely](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-vm"></a>A virtuális gép törlése

Ha már nincs rá szükség, a következő paranccsal eltávolítható az erőforráscsoport, amely tartalmazza a virtuális gép és a kapcsolódó erőforrásokkal:

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy egyszerű Windows virtuális Gépet helyezte. Azure Stack virtuális gépekkel kapcsolatos további információkért folytassa [Azure Stack-beli Virtuálisgép-funkciók](azure-stack-vm-considerations.md).
