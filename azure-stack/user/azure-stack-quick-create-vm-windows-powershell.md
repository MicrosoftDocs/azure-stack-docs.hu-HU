---
title: Windows Server rendszerű virtuális gép létrehozása a PowerShell használatával Azure Stackban | Microsoft Docs
description: Hozzon létre egy Windows Server rendszerű virtuális gépet a PowerShell használatával Azure Stack.
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
ms.date: 07/23/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 21dacbd9fc0bd7b646eb937e5460f6e7a89e3465
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68417401"
---
# <a name="quickstart-create-a-windows-server-vm-by-using-powershell-in-azure-stack"></a>Gyors útmutató: Windows Server rendszerű virtuális gép létrehozása a PowerShell használatával Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Azure Stack PowerShell használatával létrehozhat egy Windows Server 2016 rendszerű virtuális gépet (VM). A virtuális gép létrehozásához és használatához kövesse a cikk lépéseit. A cikk a következőket is ismerteti:

* Kapcsolódjon a virtuális géphez egy távoli ügyféllel.
* Telepítse az IIS-webkiszolgálót, és tekintse meg az alapértelmezett kezdőlapot.
* Távolítsa el az erőforrásokat.

> [!NOTE]
>  A jelen cikkben ismertetett lépéseket a Azure Stack Development Kit vagy egy Windows-alapú külső ügyfélről futtathatja, ha VPN-kapcsolaton keresztül csatlakozik.

## <a name="prerequisites-for-windows-server-vm"></a>A Windows Server rendszerű virtuális gép előfeltételei

* Győződjön meg arról, hogy a Azure Stack-kezelő felvette a **Windows Server 2016** rendszerképet a Azure stack Marketplace-re.

* Azure Stack az erőforrások létrehozásához és kezeléséhez a Azure PowerShell adott verzióját kell megadnia. Ha nincs konfigurálva a PowerShell a Azure Stackhoz, kövesse a PowerShell [telepítésének](../operator/azure-stack-powershell-install.md) lépéseit.

* A Azure Stack PowerShell beállítása után csatlakoznia kell a Azure Stack-környezethez. Útmutatásért lásd: [Kapcsolódás a Azure Stackhoz a PowerShell felhasználóként](azure-stack-powershell-configure-user.md).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amelybe Azure Stack erőforrások üzembe helyezése és kezelése történik. A fejlesztői készletből vagy a Azure Stack integrált rendszerből futtassa az alábbi kódrészletet egy erőforráscsoport létrehozásához. 

> [!NOTE]
> Az értékek a kódban szereplő összes változóhoz vannak rendelve. Szükség esetén azonban új értékeket is hozzárendelhet.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Tárolási erőforrások létrehozása

Hozzon létre egy Storage-fiókot és egy Storage-tárolót a Windows Server 2016-rendszerkép tárolásához.

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

Hozzon létre egy virtuális hálózatot, egy alhálózatot és egy nyilvános IP-címet. Ezek az erőforrások a virtuális géphez való hálózati kapcsolat biztosítására szolgálnak.

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

A hálózati biztonsági csoport bejövő és kimenő szabályok használatával teszi biztonságossá a virtuális gépet. Hozzon létre egy bejövő szabályt az 3389-as porthoz a bejövő Távoli asztal kapcsolatok engedélyezéséhez, valamint egy bejövő szabályt a 80-es porthoz a bejövő webes forgalom engedélyezéséhez.

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

### <a name="create-a-network-card-for-the-vm"></a>Hálózati kártya létrehozása a virtuális géphez

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

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gép konfigurációját. Ez a konfiguráció tartalmazza a virtuális gép telepítésekor használt beállításokat. Például: hitelesítő adatok, méret és a VM-rendszerkép.

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

Ahhoz, hogy az előző lépésben létrehozott virtuális gépre távolról is el lehessen végezni a távoli elérést, szüksége lesz a nyilvános IP-címére. Futtassa a következő parancsot a virtuális gép nyilvános IP-címének lekéréséhez:

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```

A következő parancs használatával hozzon létre egy Távoli asztal-munkamenetet a virtuális géppel. Cserélje le az IP-címet a virtuális gép *publicIPAddress* címére. Ha a rendszer kéri, adja meg a virtuális gép létrehozásakor használt felhasználónevet és jelszót.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>IIS telepítése a PowerShell használatával

Most, hogy bejelentkezett az Azure-beli virtuális gépre, egyetlen PowerShell-sor használatával telepítheti az IIS-t, és engedélyezheti a helyi tűzfalszabály számára a webes forgalmat. Nyisson meg egy PowerShell-parancssort, és futtassa a következő parancsot:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Az IIS kezdőlapjának megtekintése

Ha telepítette az IIS-t és a 80-as portot a virtuális gépen, akkor bármely böngésző használatával megtekintheti az alapértelmezett IIS-kezdőlapot. Használja az előző szakaszban leírt *publicIpAddress* az alapértelmezett lap megkereséséhez.

![Alapértelmezett IIS-webhely](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-vm"></a>A virtuális gép törlése

Ha már nincs rá szükség, használja a következő parancsot a virtuális gépet és a kapcsolódó erőforrásokat tartalmazó erőforráscsoport eltávolításához:

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy egyszerű Windowsos virtuális gépet telepített. Ha többet szeretne megtudni a Azure Stack virtuális gépekről, folytassa [Azure stack](azure-stack-vm-considerations.md)virtuálisgép-funkciókkal.
