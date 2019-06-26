---
title: Különbségek és szempontok a felügyelt lemezek és lemezképek az Azure Stack felügyelt |} A Microsoft Docs
description: Különbségek és szempontok ismerje meg a felügyelt lemezek és az Azure Stack felügyelt képek használata során.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 2e7737033b0f94473f4b794e452c9239f275c8f8
ms.sourcegitcommit: d1fdecdfa843dfc0629bfc226f1baf14f3ea621d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2019
ms.locfileid: "67387730"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Az Azure Stack felügyelt lemezek: különbségek és szempontok

Ez a cikk összefoglalja, ismert [Azure Stack felügyelt lemezek](azure-stack-manage-vm-disks.md) és [managed disks Azure](/azure/virtual-machines/windows/managed-disks-overview). Azure Stack és az Azure magas szintű különbségeit kapcsolatos további információkért tekintse meg a [szempontok kulcs](azure-stack-considerations.md) cikk.

A felügyelt lemezek leegyszerűsítik a Lemezkezelés IaaS virtuális gépekhez, mivel kezeli az [tárfiókok](../operator/azure-stack-manage-storage-accounts.md) társított Virtuálisgép-lemezek.

> [!NOTE]  
> Az Azure Stacken Managed disks szolgáltatás a 1808 frissítésével rendszertől érhető el. 1811 frissítésével kezdődően ez alapértelmezés szerint engedélyezve van az Azure Stack portálon használó virtuális gépek létrehozásakor.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Hasznos tanácsok: felügyelt lemez különbségek

| Funkció | Azure (globális) | Azure Stack |
| --- | --- | --- |
|Inaktív adatok titkosítása |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE)     |A BitLocker 128 bites AES-titkosítás      |
|Image          | Felügyelt egyéni rendszerkép |Támogatott|
|A biztonsági mentés | Az Azure Backup szolgáltatás |Még nem támogatott |
|Vész-helyreállítási lehetőségeket | Azure Site Recovery |Még nem támogatott|
|Lemeztípusok     |Prémium szintű SSD, Standard SSD és Standard HDD |Prémium szintű SSD, Standard HDD |
|Prémium szintű lemezek  |Teljes mértékben támogatott. |Bővítheti, de nincs teljesítményszint vagy garancia  |
|Prémium szintű lemez iops-érték  |Függ a lemez mérete  |2300 iops-érték lemezenként |
|Prémium szintű lemezek átviteli sebesség |Függ a lemez mérete |Lemezenként 145 MB/s |
|Lemezméret  |Azure Premium Disk: P4 (32 GiB) való P80 (32 TiB)<br>Azure Standard SSD Disk: E10 (128 GiB) való E80 (32 TiB)<br>Az Azure standard szintű HDD lemez: S4 (32 GiB) való S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GiB<br>M20: 512 GiB<br>M30: 1024 GiB |
|Lemezek pillanatkép-másolás|Pillanatkép készítése az Azure felügyelt támogatott futó virtuális Géphez csatolt lemezek|Még nem támogatott |
|Lemezek teljesítményét elemző |Összesített mérőszámok és a lemezenkénti metrikák támogatott |Még nem támogatott |
|Migrálás      |Adja meg az eszköz meglévő nem felügyelt Azure Resource Manager virtuális gépek nélkül hozza létre újra a virtuális gép áttelepítése  |Még nem támogatott |

> [!NOTE]  
> Felügyelt lemezek IOPs és átviteli sebesség az Azure Stackben helyett egy kiépített számot, amely negatív hatással a hardver- és az Azure Stackben futó számítási feladatokat lehet maximális szám.

## <a name="metrics"></a>Mérőszámok

A storage-mérőszámok különbségek is vannak:

- Az Azure Stack a tranzakciós adatokat a storage-mérőszámok nem tesznek különbséget a belső vagy külső hálózati sávszélességet.
- A storage-mérőszámok az Azure Stack tranzakciós adatokat nem tartalmazza a virtuális gép hozzá a csatlakoztatott lemezeket.

## <a name="api-versions"></a>API-verziók

Az Azure Stack felügyelt lemezeket támogatja a következő API-verziók:

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>Konvertálás felügyelt lemezekké

> [!NOTE]  
> Az Azure PowerShell-parancsmagot **ConvertTo-AzureRmVMManagedDisk** nem felügyelt lemez átalakítása az Azure Stack felügyelt lemez nem használható. Az Azure Stack még nem támogatja ezt a parancsmagot.

A következő parancsfájl segítségével a jelenleg üzembe helyezett virtuális gép konvertálás nem felügyeltről felügyelt lemezekre. Cserélje le a helyőrzőket a saját értékeit:

```powershell
$SubscriptionId = "SubId"

# The name of your resource group where your VM to be converted exists.
$ResourceGroupName ="MyResourceGroup"

# The name of the managed disk to be created.
$DiskName = "mngddisk"

# The size of the disks in GB. It should be greater than the VHD file size.
$DiskSize = "50"

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$VhdUri = "https://rgmgddisks347.blob.local.azurestack.external/vhds/unmngdvm20181109013817.vhd"

# The storage type for the managed disk: PremiumLRS or StandardLRS.
$AccountType = "StandardLRS"

# The Azure Stack location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM configuration (network config, VM name, location).
$Location = "local"
$VirtualMachineName = "unmngdvm"
$VirtualMachineSize = "Standard_D1"
$PIpName = "unmngdvm-ip"
$VirtualNetworkName = "unmngdrg-vnet"
$NicName = "unmngdvm"

# Set the context to the subscription ID in which the managed disk will be created.
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

# Delete old VM, but keep the OS disk.
Remove-AzureRmVm -Name $VirtualMachineName -ResourceGroupName $ResourceGroupName

# Create the managed disk configuration.
$DiskConfig = New-AzureRmDiskConfig -AccountType $AccountType -Location $Location -DiskSizeGB $DiskSize -SourceUri $VhdUri -CreateOption Import

# Create managed disk.
New-AzureRmDisk -DiskName $DiskName -Disk $DiskConfig -ResourceGroupName $resourceGroupName
$Disk = Get-AzureRmDisk -DiskName $DiskName -ResourceGroupName $ResourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $VirtualMachineName -VMSize $VirtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to "-Windows" if the OS disk has the Windows OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $Disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM.
$PublicIp = Get-AzureRmPublicIpAddress -Name $PIpName -ResourceGroupName $ResourceGroupName

# Get the virtual network where the virtual machine will be hosted.
$VNet = Get-AzureRmVirtualNetwork -Name $VirtualNetworkName -ResourceGroupName $ResourceGroupName

# Create NIC in the first subnet of the virtual network.
$Nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Nic.Id

# Create the virtual machine with managed disk.
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $ResourceGroupName -Location $Location
```

## <a name="managed-images"></a>Felügyelt képek

Az Azure Stack támogatja *lemezképek kezelt*, amelyek lehetővé teszik, hogy hozzon létre egy felügyelt rendszerképet objektumot egy általánosított virtuális gépen (mindkettő nem felügyelt és felügyelt), amely csak hozhat létre felügyelt lemez a virtuális gépek a jövőben. Felügyelt lemezképek a következő két esetben engedélyezése:

- Általánosítva van a nem felügyelt virtuális gépek és a jövőben felügyelt lemezeket használni szeretne.
- Felügyelt általánosított virtuális gép rendelkezik, és szeretne létrehozni a több, hasonló felügyelt virtuális gépeket.

### <a name="step-1-generalize-the-vm"></a>1\. lépés: A virtuális gép általánosítása

A Windows, kövesse a [a Sysprep használata Windows virtuális gép Általánosításához](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) szakaszban. Linux esetén hajtsa végre az 1. lépés [Itt](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm).

> [!NOTE]
> Ellenőrizze, hogy a virtuális gép általánosítása. Vezet, amely nem lett megfelelően általánosítva egy rendszerképből egy virtuális gép létrehozása egy **VMProvisioningTimeout** hiba.

### <a name="step-2-create-the-managed-image"></a>2\. lépés: A felügyelt rendszerkép létrehozása

A portal, PowerShell vagy parancssori felület használatával a felügyelt rendszerkép létrehozása. Kövesse a [létrehozhat egy felügyelt rendszerképet](/azure/virtual-machines/windows/capture-image-resource).

### <a name="step-3-choose-the-use-case"></a>3\. lépés: Válassza ki a használati eset

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>1\. eset: Nem felügyelt virtuális gépek migrálása felügyelt lemezekre

Győződjön meg arról, hogy a virtuális gép megfelelően általánosításához a lépés végrehajtása előtt. Általánossá, után már nem használhatja ezt a virtuális Gépet. Vezet, amely nem lett megfelelően általánosítva egy rendszerképből egy virtuális gép létrehozása egy **VMProvisioningTimeout** hiba.

Kövesse a [rendszerkép létrehozása a tárfiókban lévő virtuális merevlemezből](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-vhd-in-a-storage-account) , létrehozhat egy felügyelt rendszerképet, a storage-fiókban általános virtuális merevlemezből. Ez a rendszerkép módosítástól felügyelt virtuális gépek létrehozásához is használhatja.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>2\. eset: Felügyelt virtuális gép létrehozása felügyelt rendszerképből Powershell használatával

Egy meglévő kép felügyelt létrehozását követően a szkript a virtuális gép lemez [rendszerkép létrehozása a PowerShell használatával egy felügyelt lemezről](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-managed-disk-using-powershell), az alábbi példa parancsfájl hasonló Linux virtuális Gépet hoz létre egy meglévő kép objektum.

Azure Stack PowerShell-modul 1.7.0-ás vagy később: kövesse a [virtuális gép létrehozása felügyelt rendszerképből](/azure/virtual-machines/windows/create-vm-generalized-managed).

Azure Stack PowerShell-modul 1.6.0-s vagy korábban:

```powershell
# Variables for common values
$ResourceGroupName = "MyResourceGroup"
$Location = "local"
$VirtualMachineName = "MyVM"
$ImageRG = "managedlinuxrg"
$ImageName = "simplelinuxvmm-image-2019122"

# Create credential object
$Cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a subnet configuration
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name "MySubnet" -AddressPrefix "192.168.1.0/24"

# Create a virtual network
$VNet = New-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyVNet" -AddressPrefix "192.168.0.0/16" -Subnet $SubnetConfig

# Create a public IP address and specify a DNS name
$PIp = New-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "MyNetworkSecurityGroupRuleRDP"  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyNetworkSecurityGroup" -SecurityRules $NsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$Nic = New-AzureRmNetworkInterface -Name "MyNic" -ResourceGroupName $ResourceGroupName -Location $Location `
  -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PIp.Id -NetworkSecurityGroupId $Nsg.Id

$Image = Get-AzureRmImage -ResourceGroupName $ImageRG -ImageName $ImageName

# Create a virtual machine configuration
$VmConfig = New-AzureRmVMConfig -VMName $VirtualMachineName -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $VirtualMachineName -Credential $Cred | `
Set-AzureRmVMSourceImage -Id $Image.Id | `
Add-AzureRmVMNetworkInterface -Id $Nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VmConfig
```

A portál használatával egy virtuális gép létrehozása felügyelt rendszerképből. További információkért tekintse meg az Azure által felügyelt rendszerkép cikkek [általánosított virtuális gép felügyelt rendszerképének létrehozása az Azure-ban](/azure/virtual-machines/windows/capture-image-resource) és [virtuális gép létrehozása felügyelt rendszerképből](/azure/virtual-machines/windows/create-vm-generalized-managed).

## <a name="configuration"></a>Konfiguráció

Frissítse a 1808 alkalmazása után, vagy újabb, felügyelt lemezek használata előtt a következő konfigurációt kell végrehajtania:

- Ha egy előfizetést a 1808 korábban lett létrehozva, kövesse az alábbi lépések végrehajtásával frissítheti az előfizetést. Ellenkező esetben az előfizetésben található virtuális gépek üzembe helyezéséhez meghiúsulhat egy hibaüzenettel "Belső hiba történt a Lemezkezelőben."
   1. Az Azure Stack felhasználói portálon lépjen a **előfizetések** , és keresse meg az előfizetés. Kattintson a **erőforrás-szolgáltatók**, majd kattintson az **Microsoft.Compute**, és kattintson a **újraregisztrálni**.
   2. Lépjen az azonos előfizetéshez tartozó **hozzáférés-vezérlés (IAM)** , és ellenőrizze, hogy **Azure Stack – felügyelt lemez** szerepel a listán.
- Ha egy több-bérlős környezetet használ, kérje meg a felhő üzemeltetője (arról, hogy a saját cég vagy a szolgáltató), konfigurálja újra a Vendég címtárak a lépések mindegyike [Ez a cikk](../operator/azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). Ellenkező esetben az előfizetéshez tartozó Vendég könyvtárban a virtuális gépek üzembe helyezéséhez meghiúsulhat, egy hibaüzenet, **belső hiba történt a Lemezkezelőben**.

## <a name="next-steps"></a>További lépések

- [Azure Stack virtual machines bemutatása](azure-stack-compute-overview.md)
