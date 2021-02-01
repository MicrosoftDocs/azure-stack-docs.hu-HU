---
title: Linux rendszerű virtuális gép létrehozása a PowerShell-lel Azure Stack hub-ban
description: Hozzon létre egy linuxos virtuális gépet Azure Stack hub PowerShell használatával.
author: mattbriggs
ms.topic: quickstart
ms.date: 11/22/2020
ms.author: mabrigg
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: 6e2f6728b2c27c7571ac34bdf01499c1300c0675
ms.sourcegitcommit: e56b0eaf92c633d5d782bfdf17ce521fa88a7256
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99227378"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-powershell-in-azure-stack-hub"></a>Rövid útmutató: Linux Server rendszerű virtuális gép létrehozása a PowerShell használatával Azure Stack hub-ban

A Azure Stack hub PowerShell használatával létrehozhat egy Ubuntu Server 16,04 LTS virtuális gépet (VM). Ebben a cikkben egy virtuális gépet hoz létre és használ. A cikk azt is bemutatja, hogyan végezheti el a következőket:

* Kapcsolódjon a virtuális géphez egy távoli ügyféllel.
* Telepítsen egy NGINX-webkiszolgálót, és tekintse meg az alapértelmezett kezdőlapot.
* Törölje a használaton kívüli erőforrásokat.

## <a name="prerequisites"></a>Előfeltételek

* Linux-rendszerkép a Azure Stack hub piactéren. A Azure Stack hub piactér alapértelmezés szerint nem rendelkezik Linux-rendszerképpel. Az Azure Stack hub operátorral adja meg a szükséges Ubuntu Server 16,04 LTS-rendszerképet. Az operátor a [Marketplace-elemek Azure-ból Azure stack hubhoz való letöltésének](../operator/azure-stack-download-azure-marketplace-item.md)utasításait is használhatja.

* Azure Stack hub-nak az Azure CLI egy adott verziójára van szüksége az erőforrások létrehozásához és kezeléséhez. 
  * Ha nem rendelkezik Azure Stack hubhoz konfigurált PowerShell-lel, tekintse meg a [PowerShell telepítése Azure stack hub](../operator/powershell-install-az-module.md)-hoz című témakört. 
  * Azure Stack hub PowerShell beállítása után csatlakozni fog az Azure Stack hub-környezethez. Útmutatásért lásd: [kapcsolódás Azure stack hubhoz a PowerShell-lel felhasználóként](azure-stack-powershell-configure-user.md).

* A (z) *id_rsa. pub* nevű nyilvános Secure Shell-(SSH-) kulcs a Windows felhasználói profil *. ssh* könyvtárába lett mentve. Az SSH-kulcsok létrehozásával kapcsolatos részletes információkért lásd: [SSH nyilvános kulcs használata](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amely Azure Stack hub-erőforrások üzembe helyezésére és kezelésére szolgál. Erőforráscsoport létrehozásához futtassa a következő kódrészletet: 

> [!NOTE]
> A következő példákban az összes változóhoz rendeltünk értékeket. Azonban a saját értékeit is hozzárendelheti.

```powershell  
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Tárerőforrások létrehozása

Hozzon létre egy Storage-fiókot, amelyet a rendszer a rendszerindítási diagnosztika kimenetének tárolására fog használni.

### <a name="az-modules"></a>[Az modulok](#tab/az1)

```powershell  
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

```
### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm1)

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

Set-AzureRMCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

```

---



## <a name="create-networking-resources"></a>Hálózati erőforrások létrehozása

Hozzon létre egy virtuális hálózatot, egy alhálózatot és egy nyilvános IP-címet. Ezek az erőforrások a virtuális géphez való hálózati kapcsolat biztosítására szolgálnak.

### <a name="az-modules"></a>[Az modulok](#tab/az2)

```powershell
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"

```
### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm2)
 


```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRMVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRMVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRMPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"

```
---
### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Hálózati biztonsági csoport és hálózati biztonsági csoportszabály létrehozása

A hálózati biztonsági csoport bejövő és kimenő szabályok használatával teszi biztonságossá a virtuális gépet. Hozzon létre egy bejövő szabályt az 3389-as porthoz a bejövő Távoli asztal kapcsolatok engedélyezéséhez, valamint egy bejövő szabályt a 80-es porthoz a bejövő webes forgalom engedélyezéséhez.

### <a name="az-modules"></a>[Az modulok](#tab/az3)


```powershell
# Create variables to store the network security group and rules names.
$nsgName = "myNetworkSecurityGroup"
$nsgRuleSSHName = "myNetworkSecurityGroupRuleSSH"
$nsgRuleWebName = "myNetworkSecurityGroupRuleWeb"


# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzNetworkSecurityRuleConfig -Name $nsgRuleSSHName -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name $nsgRuleWebName -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $location `
-Name $nsgName -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```
### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm3)


```powershell
# Create variables to store the network security group and rules names.
$nsgName = "myNetworkSecurityGroup"
$nsgRuleSSHName = "myNetworkSecurityGroupRuleSSH"
$nsgRuleWebName = "myNetworkSecurityGroupRuleWeb"


# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRMNetworkSecurityRuleConfig -Name $nsgRuleSSHName -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRMNetworkSecurityRuleConfig -Name $nsgRuleWebName -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRMNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $location `
-Name $nsgName -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

---


### <a name="create-a-network-card-for-the-vm"></a>Hálózati kártya létrehozása a virtuális géphez

A hálózati kártya csatlakoztatja a virtuális gépet egy alhálózathoz, egy hálózati biztonsági csoporthoz és egy nyilvános IP-címhez.

### <a name="az-modules"></a>[Az modulok](#tab/az4)

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```
### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm4)

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRMNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

---



## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gép konfigurációját. Ez a konfiguráció tartalmazza a virtuális gép telepítésekor használandó beállításokat (például a felhasználói hitelesítő adatokat, a méretet és a virtuálisgép-lemezképet).

### <a name="az-modules"></a>[Az modulok](#tab/az5)

```powershell
# Define a credential object
$UserName='demouser'
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the VM configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

# Set the operating system disk properties on a VM
$VirtualMachine = Set-AzVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzVMNetworkInterface -Id $nic.Id

# Configure SSH keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"

# Add the SSH key to the VM
Add-AzVMSshPublicKey -VM $VirtualMachine `
 -KeyData $sshPublicKey `
 -Path "/home/azureuser/.ssh/authorized_keys"

# Create the VM
New-AzVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```
### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm5)

```powershell
# Define a credential object
$UserName='demouser'
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the VM configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRMVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRMVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRMVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

# Set the operating system disk properties on a VM
$VirtualMachine = Set-AzureRMVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzureRMVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzureRMVMNetworkInterface -Id $nic.Id

# Configure SSH keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"

# Add the SSH key to the VM
Add-AzureRMVMSshPublicKey -VM $VirtualMachine `
 -KeyData $sshPublicKey `
 -Path "/home/azureuser/.ssh/authorized_keys"

# Create the VM
New-AzureRMVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

---



## <a name="vm-quick-create-full-script"></a>VM gyors létrehozás: teljes parancsfájl

> [!NOTE]
> Ez a lépés lényegében az előző kód összeolvadt, de a jelszóval nem rendelkező SSH-kulcs helyett a hitelesítéshez.

### <a name="az-modules"></a>[Az modulok](#tab/az6)

```powershell
## Create a resource group

<#
A resource group is a logical container where you can deploy and manage Azure Stack Hub resources. From your development kit or the Azure Stack Hub integrated system, run the following code block to create a resource group. Though we've assigned values for all the variables in this article, you can use these values or assign new ones.
#>

# Edit your variables, if required

# Create variables to store the location and resource group names
$location = "local"
$ResourceGroupName = "myResourceGroup"

# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create variables to store the network security group and rules names
$nsgName = "myNetworkSecurityGroup"
$nsgRuleSSHName = "myNetworkSecurityGroupRuleSSH"
$nsgRuleWebName = "myNetworkSecurityGroupRuleWeb"

# Create variable for VM password
$VMPassword = 'Password123!'

# End of variables - no need to edit anything past that point to deploy a single VM

# Create a resource group
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $location

## Create storage resources

# Create a storage account, and then create a storage container for the Ubuntu Server 16.04 LTS image

# Create a new storage account
$StorageAccount = New-AzStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the VM image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob


## Create networking resources

# Create a virtual network, a subnet, and a public IP address, resources that are used provide network connectivity to the VM

# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"


### Create a network security group and a network security group rule

<#
The network security group secures the VM by using inbound and outbound rules. Create an inbound rule for port 3389 to allow incoming Remote Desktop connections and an inbound rule for port 80 to allow incoming web traffic.
#>

# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzNetworkSecurityRuleConfig -Name $nsgRuleSSHName -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name $nsgRuleWebName -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $location `
-Name $nsgName -SecurityRules $nsgRuleSSH,$nsgRuleWeb

### Create a network card for the VM

# The network card connects the VM to a subnet, network security group, and public IP address.

# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

## Create a VM
<#
Create a VM configuration. This configuration includes the settings used when deploying the VM. For example: user credentials, size, and the VM image.
#>

# Define a credential object
$UserName='demouser'
$securePassword = ConvertTo-SecureString $VMPassword -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the VM configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzVMSourceImage `
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

# Set the operating system disk properties on a VM
$VirtualMachine = Set-AzVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzVMNetworkInterface -Id $nic.Id

# Create the VM
New-AzVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm6)

```powershell
## Create a resource group

<#
A resource group is a logical container where you can deploy and manage Azure Stack Hub resources. From your development kit or the Azure Stack Hub integrated system, run the following code block to create a resource group. Though we've assigned values for all the variables in this article, you can use these values or assign new ones.
#>

# Edit your variables, if required

# Create variables to store the location and resource group names
$location = "local"
$ResourceGroupName = "myResourceGroup"

# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create variables to store the network security group and rules names
$nsgName = "myNetworkSecurityGroup"
$nsgRuleSSHName = "myNetworkSecurityGroupRuleSSH"
$nsgRuleWebName = "myNetworkSecurityGroupRuleWeb"

# Create variable for VM password
$VMPassword = 'Password123!'

# End of variables - no need to edit anything past that point to deploy a single VM

# Create a resource group
New-AzureRMResourceGroup `
  -Name $ResourceGroupName `
  -Location $location

## Create storage resources

# Create a storage account, and then create a storage container for the Ubuntu Server 16.04 LTS image

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRMCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the VM image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob


## Create networking resources

# Create a virtual network, a subnet, and a public IP address, resources that are used provide network connectivity to the VM

# Create a subnet configuration
$subnetConfig = New-AzureRMVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRMVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRMPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"


### Create a network security group and a network security group rule

<#
The network security group secures the VM by using inbound and outbound rules. Create an inbound rule for port 3389 to allow incoming Remote Desktop connections and an inbound rule for port 80 to allow incoming web traffic.
#>

# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRMNetworkSecurityRuleConfig -Name $nsgRuleSSHName -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRMNetworkSecurityRuleConfig -Name $nsgRuleWebName -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRMNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $location `
-Name $nsgName -SecurityRules $nsgRuleSSH,$nsgRuleWeb

### Create a network card for the VM

# The network card connects the VM to a subnet, network security group, and public IP address.

# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRMNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

## Create a VM
<#
Create a VM configuration. This configuration includes the settings used when deploying the VM. For example: user credentials, size, and the VM image.
#>

# Define a credential object
$UserName='demouser'
$securePassword = ConvertTo-SecureString $VMPassword -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the VM configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRMVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRMVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRMVMSourceImage `
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

# Set the operating system disk properties on a VM
$VirtualMachine = Set-AzureRMVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRMVMNetworkInterface -Id $nic.Id

# Create the VM
New-AzureRMVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

---

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Miután telepítette a virtuális gépet, konfigurálja az SSH-kapcsolatokat. A virtuális gép nyilvános IP-címének lekéréséhez használja a [Get-AzPublicIpAddress](/powershell/module/Az.network/get-Azpublicipaddress) parancsot:

### <a name="az-modules"></a>[Az modulok](#tab/az7)

```powershell
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```
### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm7)

```powershell
Get-AzureRMPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

---



Az SSH-val telepített SSH-val a következő parancs használatával csatlakozhat a virtuális géphez. Ha Windows rendszeren dolgozik, a [Putty](https://www.putty.org/) segítségével hozhatja létre a kapcsolódást.

```
ssh <Public IP Address>
```

Amikor a rendszer kéri, jelentkezzen be **azureuser**-ként. Ha az SSH-kulcsok létrehozásakor jelszót használt, meg kell adnia a jelszót.

## <a name="install-the-nginx-web-server"></a>Az NGINX-webkiszolgáló telepítése

A csomag erőforrásainak frissítéséhez és a legújabb NGINX-csomag telepítéséhez futtassa a következő parancsfájlt:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Az NGINX kezdőlapjának megtekintése

Ha telepítette az NGINX-webkiszolgálót, és a 80-es port meg van nyitva a virtuális gépen, a virtuális gép nyilvános IP-címe segítségével érheti el a webkiszolgálót. Nyisson meg egy webböngészőt, és nyissa meg a következőt: ```http://<public IP address>``` .

![Az NGINX webkiszolgáló kezdőlapja](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A [Remove-AzResourceGroup](/powershell/module/Az.resources/remove-Azresourcegroup) parancs használatával törölheti azokat az erőforrásokat, amelyekre nincs szüksége többé. Az erőforráscsoport és az összes erőforrás törléséhez futtassa a következő parancsot:

### <a name="az-modules"></a>[Az modulok](#tab/az8)

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```
### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm8)

```powershell
Remove-AzureRMResourceGroup -Name myResourceGroup
```
---

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy alapszintű Linux Server-alapú virtuális gépet telepített. Az Azure Stack hub virtuális gépekkel kapcsolatos további tudnivalókért tekintse meg a [Azure stack hub-beli virtuális gépekkel kapcsolatos szempontokat](azure-stack-vm-considerations.md).
