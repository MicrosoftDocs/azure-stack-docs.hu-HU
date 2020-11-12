---
title: Azure Stack hub felügyelt lemezek eltérései és szempontjai
description: Ismerje meg a felügyelt lemezek és a felügyelt lemezképek Azure Stack hub-ban való használatakor felmerülő különbségeket és szempontokat.
author: sethmanheim
ms.topic: article
ms.date: 08/27/2020
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 077ff172b1a8c1d68f823f521a17729d47a03eac
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546616"
---
# <a name="azure-stack-hub-managed-disks-differences-and-considerations"></a>Azure Stack hub által felügyelt lemezek: különbségek és szempontok

Ez a cikk a [ *felügyelt lemezek* Azure stack hub](azure-stack-manage-vm-disks.md) és [Az Azure-beli felügyelt lemezek](/azure/virtual-machines/windows/managed-disks-overview)közötti különbségeit összegzi. Az Azure Stack hub és az Azure közötti magas szintű különbségek megismeréséhez tekintse meg a [legfontosabb szempontokat](azure-stack-considerations.md) ismertető cikket.

A felügyelt lemezek egyszerűbbé teszik a IaaS virtuális gépek (VM-EK) lemezes kezelését a virtuálisgép-lemezekhez társított [Storage-fiókok](../operator/azure-stack-manage-storage-accounts.md) kezelésével.

A felügyelt lemezek alapértelmezés szerint engedélyezve vannak a virtuális gépek Azure Stack hub portál használatával történő létrehozásakor.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Cheat Sheet: felügyelt lemezes különbségek

| Jellemző | Azure (globális) | Azure Stack Hub |
| --- | --- | --- |
|Inaktív adatok titkosítása |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE).     |BitLocker 128 bites AES-titkosítás      |
|Kép          | Felügyelt egyéni rendszerkép |Támogatott|
|Biztonsági mentési beállítások | Azure Backup szolgáltatás |Még nem támogatott |
|Vész-helyreállítási lehetőségek | Azure Site Recovery |Még nem támogatott|
|Lemez típusa     |Prémium SSD, standard SSD és standard HDD. |Prémium SSD, standard HDD |
|Prémium szintű lemezek  |Teljes mértékben támogatott. |Kiépíthető, de nincs teljesítménybeli korlát vagy garancia  |
|Prémium szintű lemezek IOPs  |A lemez méretétől függ.  |2300 IOPs/lemez |
|Prémium szintű lemezek átviteli sebessége |A lemez méretétől függ. |145 MB/másodperc/lemez |
|Lemezméret  |Azure Premium Disk: P4 (32 GiB) – P80 (32 TiB)<br>Azure standard SSD lemez: E10 (128 GiB) – E80 (32 TiB)<br>Azure standard HDD lemez: S4 (32 GiB) – S80 (32 TiB) |M4:32 GiB<br>M6:64 GiB<br>M10:128 GiB<br>M15:256 GiB<br>M20:512 GiB<br>M30:1023 GiB |
|Lemezek pillanatképének másolása|A futó virtuális gépekhez csatolt Snapshot Azure Managed Disks.|Még nem támogatott |
|Lemezek teljesítményének analitikai |Összesített mérőszámok és lemezes mérőszámok támogatottak. |Még nem támogatott |
|Áttelepítés      |Adja meg az eszközt a meglévő, nem felügyelt Azure Resource Manager virtuális gépekről való áttelepítéshez anélkül, hogy újra létre kellene hoznia a virtuális gépet.  |Még nem támogatott |

> [!NOTE]  
> A felügyelt lemezek IOPs és átviteli sebessége Azure Stack hub-ban nem kiosztott szám, hanem a Azure Stack hub-ban futó hardverek és munkaterhelések is befolyásolhatják.

## <a name="metrics"></a>Mérőszámok

A tárolási metrikákkal kapcsolatban is különbségek vannak:

- Azure Stack hub esetében a tárolási mérőszámokban lévő tranzakciós adatok nem különböztetik meg a belső vagy külső hálózati sávszélességet.
- Azure Stack hub tranzakciós adatai a tárolási mérőszámokban nem tartalmazzák a csatlakoztatott lemezekhez tartozó virtuális gépekhez való hozzáférést.

## <a name="api-versions"></a>API-verziók

Azure Stack hub Managed Disks a következő API-verziókat támogatja:

- 2017-03-30
- 2017-12-01 (csak felügyelt lemezképek, nincsenek lemezek, Pillanatképek nélkül)

## <a name="convert-to-managed-disks"></a>Átalakítás felügyelt lemezekre

> [!NOTE]  
> A Azure PowerShell **-parancsmag ConvertTo-AzVMManagedDisk** nem használható nem felügyelt lemez Azure stack hub-beli felügyelt lemezre történő átalakítására. Azure Stack hub jelenleg nem támogatja ezt a parancsmagot.

A következő szkripttel a jelenleg kiépített virtuális gépek nem felügyelt lemezekre konvertálhatók. Cserélje le a helyőrzőket a saját értékeire:

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

# The Azure Stack Hub location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM configuration (network config, VM name, location).
$Location = "local"
$VirtualMachineName = "unmngdvm"
$VirtualMachineSize = "Standard_D1"
$PIpName = "unmngdvm-ip"
$VirtualNetworkName = "unmngdrg-vnet"
$NicName = "unmngdvm"

# Set the context to the subscription ID in which the managed disk will be created.
Select-AzSubscription -SubscriptionId $SubscriptionId

# Delete old VM, but keep the OS disk.
Remove-AzVm -Name $VirtualMachineName -ResourceGroupName $ResourceGroupName

# Create the managed disk configuration.
$DiskConfig = New-AzDiskConfig -AccountType $AccountType -Location $Location -DiskSizeGB $DiskSize -SourceUri $VhdUri -CreateOption Import

# Create managed disk.
New-AzDisk -DiskName $DiskName -Disk $DiskConfig -ResourceGroupName $resourceGroupName
$Disk = Get-AzDisk -DiskName $DiskName -ResourceGroupName $ResourceGroupName
$VirtualMachine = New-AzVMConfig -VMName $VirtualMachineName -VMSize $VirtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to "-Windows" if the OS disk has the Windows OS.
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -ManagedDiskId $Disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM.
$PublicIp = Get-AzPublicIpAddress -Name $PIpName -ResourceGroupName $ResourceGroupName

# Get the virtual network where the virtual machine will be hosted.
$VNet = Get-AzVirtualNetwork -Name $VirtualNetworkName -ResourceGroupName $ResourceGroupName

# Create NIC in the first subnet of the virtual network.
$Nic = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName

$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Nic.Id

# Create the virtual machine with managed disk.
New-AzVM -VM $VirtualMachine -ResourceGroupName $ResourceGroupName -Location $Location
```

## <a name="managed-images"></a>Felügyelt lemezképek

Azure Stack hub támogatja a *felügyelt* lemezképeket, amelyek lehetővé teszik, hogy felügyelt lemezkép-objektumokat hozzon létre egy ÁLTALÁNOSÍTOTT virtuális gépen (nem felügyelt és felügyelt), amelyek csak felügyelt lemezes virtuális gépeket hozhatnak létre. A felügyelt lemezképek a következő két forgatókönyvet teszik lehetővé:

- Általánosított nem felügyelt virtuális gépeket használ, és szeretné használni a felügyelt lemezeket.
- Rendelkezik egy általánosított felügyelt virtuális géppel, és több, hasonló felügyelt virtuális gépet szeretne létrehozni.

### <a name="step-1-generalize-the-vm"></a>1. lépés: a virtuális gép általánosítása

Windows esetén kövesse a [Windows rendszerű virtuális gép általánosítása a Sysprep használatával](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) című szakaszt. Linux esetén kövesse [az 1.](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm)lépést.

> [!NOTE]
> Győződjön meg arról, hogy általánosítja a virtuális gépet. A nem megfelelően általánosított rendszerképből létrehozott virtuális gépek **VMProvisioningTimeout** hibát okozhatnak.

### <a name="step-2-create-the-managed-image"></a>2. lépés: a felügyelt rendszerkép létrehozása

A felügyelt rendszerkép létrehozásához használhatja a portált, a PowerShellt vagy a CLI-t is. Kövesse a [felügyelt rendszerkép létrehozása](/azure/virtual-machines/windows/capture-image-resource)című témakör lépéseit.

### <a name="step-3-choose-the-use-case"></a>3. lépés: a használati eset kiválasztása

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>1. eset: nem felügyelt virtuális gépek áttelepítése felügyelt lemezekre

A lépés végrehajtása előtt győződjön meg arról, hogy megfelelően általánosítja a virtuális gépet. Az általánosítás után már nem használhatja ezt a virtuális gépet. A nem megfelelően általánosított rendszerképből létrehozott virtuális gépek **VMProvisioningTimeout** hibához vezethetnek.

Kövesse a [rendszerkép létrehozása olyan virtuális gépről, amely Storage-fiókot használ](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-vm-that-uses-a-storage-account) egy felügyelt rendszerkép létrehozásához egy általános VHD-ből egy Storage-fiókban. A jövőben ezt a rendszerképet használhatja a felügyelt virtuális gépek létrehozásához.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>2. eset: felügyelt virtuális gép létrehozása felügyelt képből a PowerShell használatával

Miután létrehozta a lemezképet egy meglévő felügyelt lemezről származó virtuális gépről a [lemezkép létrehozása felügyelt lemezről a PowerShell használatával](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-managed-disk-using-powershell)című parancsfájl használatával, a következő példában szereplő parancsfájl használatával hozzon létre egy hasonló linuxos virtuális gépet egy meglévő rendszerkép-objektumból.

Azure Stack hub PowerShell-moduljának 1.7.0 vagy újabb verziója: kövesse a [virtuális gép létrehozása felügyelt rendszerképből](/azure/virtual-machines/windows/create-vm-generalized-managed)című témakör utasításait.

Azure Stack hub PowerShell-modul 1.6.0 vagy korábbi:

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
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Create a subnet configuration
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name "MySubnet" -AddressPrefix "192.168.1.0/24"

# Create a virtual network
$VNet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyVNet" -AddressPrefix "192.168.0.0/16" -Subnet $SubnetConfig

# Create a public IP address and specify a DNS name
$PIp = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$NsgRuleSSH = New-AzNetworkSecurityRuleConfig -Name "MyNetworkSecurityGroupRuleSSH"  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 22 -Access Allow

# Create a network security group
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyNetworkSecurityGroup" -SecurityRules $NsgRuleSSH

# Create a virtual network card and associate with public IP address and NSG
$Nic = New-AzNetworkInterface -Name "MyNic" -ResourceGroupName $ResourceGroupName -Location $Location `
  -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PIp.Id -NetworkSecurityGroupId $Nsg.Id

$Image = Get-AzImage -ResourceGroupName $ImageRG -ImageName $ImageName

# Create a virtual machine configuration
$VmConfig = New-AzureRmVMConfig -VMName $VirtualMachineName -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $VirtualMachineName -Credential $Cred | `
Set-AzureRmVMSourceImage -Id $Image.Id | `
Set-AzureRmVMOSDisk -VM $VmConfig -CreateOption FromImage -Linux | `
Add-AzureRmVMNetworkInterface -Id $Nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VmConfig
```

A portál használatával a virtuális gépet felügyelt rendszerképből is létrehozhatja. További információkért tekintse meg az Azure Managed rendszerkép című cikket, amely egy [általánosított virtuális gép felügyelt rendszerképét hozza létre az Azure-ban](/azure/virtual-machines/windows/capture-image-resource) , és [hozzon létre egy virtuális gépet egy felügyelt rendszerképből](/azure/virtual-machines/windows/create-vm-generalized-managed).

## <a name="configuration"></a>Konfiguráció

Az 1808-es vagy újabb frissítés alkalmazása után a következő konfigurációs módosításokat kell elvégeznie a felügyelt lemezek használata előtt:

- Ha előfizetést hozott létre az 1808-es frissítés előtt, az alábbi lépéseket követve frissítheti az előfizetést. Ellenkező esetben előfordulhat, hogy a virtuális gépek üzembe helyezése ebben az előfizetésben sikertelen lehet, ha a "belső hiba a lemezmeghajtóban" hibaüzenet jelenik meg.
   1. Az Azure Stack hub felhasználói portálon lépjen az **előfizetések** elemre, és keresse meg az előfizetést. Kattintson az **erőforrás-szolgáltatók** elemre, majd a **Microsoft. számítás** elemre, majd az **újbóli regisztrálás** elemre.
   2. Ugyanebben az előfizetésben lépjen a **Access Control (iam)** elemre, és ellenőrizze, hogy a **Azure stack hub által felügyelt lemez szerepel-** e a listáján.
- Ha több-bérlős környezetet használ, kérje meg a Felhőbeli operátort (amely a saját szervezetében vagy a szolgáltatónál lehet), hogy az [ebben a cikkben](../operator/azure-stack-enable-multitenancy.md#register-azure-stack-hub-with-the-guest-directory)leírt lépéseket követve konfigurálja újra az egyes vendég címtárakat. Ellenkező esetben előfordulhat, hogy a virtuális gépeknek az adott vendég címtárhoz társított előfizetésben való telepítése sikertelen lehet, ha a "belső hiba a Lemezkezelésben" hibaüzenet jelenik meg.

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg [Azure stack hub virtuális gépekkel](azure-stack-compute-overview.md).
