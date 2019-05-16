---
title: Az Azure Stack PowerShell használatával Linux rendszerű virtuális gép létrehozása |} A Microsoft Docs
description: Hozzon létre egy Linux virtuális gép az Azure Stack PowerShell használatával.
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
ms.date: 03/11/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 55f1395d66262b268b9107f196528270c1546bba
ms.sourcegitcommit: 87d93cdcdb6efb06e894f56c2f09cad594e1a8b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65712285"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-using-powershell-in-azure-stack"></a>Gyors útmutató: Az Azure Stack PowerShell használatával Linux rendszerű kiszolgáló virtuális gép létrehozása

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ubuntu Server 16.04 LTS virtuális gépként az Azure Stack PowerShell használatával hozhat létre. Kövesse a cikkben hozhat létre és használhat egy virtuális gépet.  Ez a cikk is biztosít a lépéseket:

* Csatlakozzon a virtuális géphez a távoli ügyfélhez.
* Az NGINX-webkiszolgálót, és az alapértelmezett kezdőlap megtekintéséhez.
* A fel nem használt erőforrások törlése.

## <a name="prerequisites"></a>Előfeltételek

* **Egy Linuxos rendszerképet az Azure Stack piactéren**

   Az Azure Stack piactéren alapértelmezés szerint nem tartalmaz a Linux-rendszerképen. Adja meg az Azure Stack-operátorokról lekérése a **Ubuntu Server 16.04 LTS** rendszerképre van szüksége. Az operátor leírt lépéseket követve használhat a [Piactéri termékek letöltése az Azure-ból az Azure Stackhez](../operator/azure-stack-download-azure-marketplace-item.md) cikk.

* Az Azure Stack egy adott verzióját az Azure PowerShell-lel az erőforrások létrehozásához és kezeléséhez szükséges. Ha nincs konfigurálva az Azure Stack PowerShell, kövesse a lépéseket [telepítése](../operator/azure-stack-powershell-install.md) PowerShell.

* Az Azure Stack PowerShell használatával állítsa be szüksége lesz az Azure Stack környezettel való kapcsolódáshoz. További útmutatást lásd: [csatlakozhat az Azure Stack PowerShell felhasználói](azure-stack-powershell-configure-user.md).

* Egy nyilvános SSH-kulcsot a név id_rsa.pub mentése a Windows felhasználói profilja .ssh könyvtárában. Az SSH-kulcsok létrehozásával kapcsolatos részletes információkért lásd: [nyilvános SSH-kulcs használatával](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Egy erőforráscsoport olyan logikai tároló, ahol üzembe helyezése és kezelése az Azure Stack-erőforrások. A fejlesztői készlet vagy az Azure Stackkel integrált rendszereknél futtassa a következő kódblokk egy erőforráscsoport létrehozásához. 

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

Hozzon létre egy tárfiókot, és hozzon létre egy storage-tárolót az Ubuntu Server 16.04 LTS-rendszerképhez.

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

A hálózati biztonsági csoport bejövő és kimenő szabályok használatával teszi biztonságossá a virtuális gépet. Hozzon létre egy bejövő szabályt a 3389-es bejövő távoli asztali kapcsolatok engedélyezése és a egy bejövő szabály a bejövő webes forgalom engedélyezéséhez a 80-as porton.

```powershell
# Create variables to store the network security group and rules names.
$nsgName = "myNetworkSecurityGroup"
$nsgRuleSSHName = "myNetworkSecurityGroupRuleSSH"
$nsgRuleWebName = "myNetworkSecurityGroupRuleWeb"


# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleSSHName -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleWebName -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $location `
-Name $nsgName -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Hálózati kártya létrehozása a virtuális géphez

A hálózati kártya csatlakoztatja a virtuális gépet egy alhálózathoz, egy hálózati biztonsági csoporthoz és egy nyilvános IP-címhez.

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

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuálisgép-konfigurációt. Ez a konfiguráció a virtuális gép üzembe helyezése során használt beállításait tartalmazza. Például: felhasználói hitelesítő adatokat, mérete és a virtuálisgép-lemezkép.

```powershell
# Define a credential object.
$UserName='demouser'
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzureRmVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzureRmVMNetworkInterface -Id $nic.Id


# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"

# Adds the SSH Key to the virtual machine
Add-AzureRmVMSshPublicKey -VM $VirtualMachine `
 -KeyData $sshPublicKey `
 -Path "/home/azureuser/.ssh/authorized_keys"

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="quick-create-virtual-machine---full-script"></a>Gyors létrehozás virtuális gép – teljes szkript

> [!NOTE]
> Ez lényegében a fenti kódrészlettel egyesített együtt, de a jelszót az SSH-kulcsot a hitelesítéshez helyett.

```powershell
## Create a resource group

<#
A resource group is a logical container where you can deploy and manage Azure Stack resources. From your development kit or the Azure Stack integrated system, run the following code block to create a resource group. Values are assigned for all the variables in this document, you can use these values or assign new values.
#>

# Edit your variables here if required

# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create variables to store the network security group and rules names.
$nsgName = "myNetworkSecurityGroup"
$nsgRuleSSHName = "myNetworkSecurityGroupRuleSSH"
$nsgRuleWebName = "myNetworkSecurityGroupRuleWeb"

# Create variable for virtual machine password
$VMPassword = 'Password123!'

# End of Variables - no need to edit anything past that point to deploy a single VM

# Create Resource Group
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location

## Create storage resources

# Create a storage account and then create a storage container for the Ubuntu Server 16.04 LTS image.

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the virtual machine image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob


## Create networking resources

# Create a virtual network, subnet, and a public IP address. These resources are used to provide network connectivity to the virtual machine.

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


### Create a network security group and a network security group rule

<#
The network security group secures the virtual machine by using inbound and outbound rules. Create an inbound rule for port 3389 to allow incoming Remote Desktop connections and an inbound rule for port 80 to allow incoming web traffic.
#>

# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleSSHName -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleWebName -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $location `
-Name $nsgName -SecurityRules $nsgRuleSSH,$nsgRuleWeb

### Create a network card for the virtual machine

# The network card connects the virtual machine to a subnet, network security group, and public IP address.

# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

## Create a virtual machine
<#
Create a virtual machine configuration. This configuration includes the settings used when deploying the virtual machine. For example: user credentials, size, and the virtual machine image.
#>

# Define a credential object.
$UserName='demouser'
$securePassword = ConvertTo-SecureString $VMPassword -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

A virtuális gép üzembe helyezését követően konfigurálja a virtuális gép SSH-kapcsolatot. Használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) parancsot a virtuális gép nyilvános IP-címének lekéréséhez.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Az ssh-val telepített ügyfél rendszer a következő parancs segítségével csatlakozhat a virtuális géphez. Ha Windows rendszeren dolgozik, akkor használhatja [Putty](https://www.putty.org/) a kapcsolat létrehozásához.

```
ssh <Public IP Address>
```

Amikor a rendszer kéri, jelentkezzen be, **azureuser**. Ha egy hozzáférési kódot az SSH-kulcsok létrehozásakor használt, kell meg a jelszót.

## <a name="install-the-nginx-web-server"></a>Az NGINX-webkiszolgáló telepítése

Csomag erőforrásokat, és telepítse a legújabb NGINX-csomagot, futtassa a következő szkriptet:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Az NGINX kezdőlapjának megtekintése

Az NGINX telepítve van, és nyissa meg a virtuális gépen a 80-as porton a webkiszolgáló, a virtuális gép nyilvános IP-cím használatával is elérheti. Nyisson meg egy webböngészőt, és váltson ```http://<public IP address>```.

![Az NGINX web server kezdőlap](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforrásokat, amelyeket többé nem kell törölni. Használhatja a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal eltávolítható az ezekhez az erőforrásokhoz. Az erőforráscsoport és az ahhoz tartozó összes erőforrás törléséhez futtassa a következő parancsot:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban üzembe helyezett alapszintű Linux-kiszolgáló virtuális gép. További információ az Azure Stack virtuális gépekről, lépjen a [szempontok a virtuális gépek az Azure Stackben](azure-stack-vm-considerations.md).
