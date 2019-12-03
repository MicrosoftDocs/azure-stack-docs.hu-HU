---
title: Virtuálisgép-lemezes tároló létrehozása a Azure Stackban | Microsoft Docs
description: Lemezeket hozhat létre Azure Stack virtuális gépekhez.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: a9c0016ef75040263acfe8400e9e04dce9eb744c
ms.sourcegitcommit: 7817d61fa34ac4f6410ce6f8ac11d292e1ad807c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74690233"
---
# <a name="create-vm-disk-storage-in-azure-stack"></a>VIRTUÁLIS gépek lemezes tárolásának létrehozása a Azure Stackban

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ez a cikk azt ismerteti, hogyan hozható létre virtuális gép (VM) lemezes tárolás a Azure Stack portál vagy a PowerShell használatával.

## <a name="overview"></a>Áttekintés

Az 1808-es verziótól kezdve a Azure Stack támogatja a felügyelt lemezek és a nem felügyelt lemezek használatát a virtuális gépeken, mint az operációs rendszer (OS) és az adatlemezek. Az 1808-es verzió előtt csak a nem felügyelt lemezek támogatottak.

A [felügyelt lemezek](/azure/virtual-machines/windows/managed-disks-overview) leegyszerűsítik az Azure IaaS virtuális gépek lemezes kezelését a virtuálisgép-lemezekhez társított Storage-fiókok kezelésével. Csak meg kell adnia a kívánt lemez méretét, és az Azure létrehozza és felügyeli a lemezt Ön helyett.

A nem felügyelt lemezekhez szükség van egy Storage-fiók létrehozására a lemezek tárolásához. A létrehozott lemezeket virtuálisgép-lemeznek nevezzük, és tárolókban tárolódnak a Storage-fiókban.

### <a name="best-practice-guidelines"></a>Ajánlott eljárási irányelvek

Ajánlott a virtuális gép Managed Disks használata a könnyebb kezelhetőség és a kapacitás elosztása érdekében. A felügyelt lemez használata előtt nem kell előkészítenie a Storage-fiókot és-tárolókat.  Több Managed Disks létrehozásakor a lemezek több kötetre lesznek elosztva, amelyek a kötetek kapacitásának kiegyensúlyozására szolgálnak.  

A nem felügyelt lemezek esetében a teljesítmény javítása és a teljes költségek csökkentése érdekében javasoljuk, hogy mindegyik virtuálisgép-lemezt külön tárolóban helyezze el. Akár ugyanazon a tárolóban is elhelyezheti az operációsrendszer-lemezeket és az adatlemezeket, az ajánlott eljárás az, hogy az egyik tároló egy operációsrendszer-lemezt vagy egy adatlemezt is tároljon, de egyszerre nem. 

Ha egy vagy több adatlemezt ad hozzá egy virtuális géphez, a lemezeket tároló helyként további tárolókat is használhat. A további virtuális gépek operációsrendszer-lemezének a saját tárolókban is szerepelnie kell.

Virtuális gépek létrehozásakor ugyanazt a Storage-fiókot használhatja minden egyes új virtuális géphez. Csak a létrehozott tárolóknak egyedieknek kell lenniük.

### <a name="adding-new-disks"></a>Új lemezek hozzáadása

A következő táblázat összefoglalja, hogyan adhat hozzá lemezeket a portál használatával és a PowerShell használatával:

| Módszer | Beállítások
|-|-|
|Felhasználói portál|-Új adatlemezek hozzáadása egy meglévő virtuális géphez. Az új lemezeket a Azure Stack hozza létre. </br> </br> -Meglévő lemez (. vhd) fájl hozzáadása egy korábban létrehozott virtuális géphez. Ehhez elő kell készítenie a. vhd fájlt, majd fel kell töltenie a Azure Stackba. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | – Hozzon létre egy új virtuális gépet operációsrendszer-lemezzel, és egy időben adjon hozzá egy vagy több adatlemezt a virtuális géphez. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Lemezek hozzáadása egy virtuális géphez a portál használatával

Alapértelmezés szerint, amikor a portál használatával hoz létre egy virtuális gépet a legtöbb Piactéri elemhez, csak az operációsrendszer-lemez jön létre.

Miután létrehozta a virtuális gépet, a portálon a következőket végezheti el:

* Hozzon létre egy új adatlemezt, és csatolja a virtuális géphez.
* Töltsön fel egy meglévő adatlemezt, és csatolja a virtuális géphez.

Minden hozzáadott nem felügyelt lemezt külön tárolóba kell helyezni.

>[!NOTE]  
>Az Azure által létrehozott és kezelt lemezeket [felügyelt lemeznek](/azure/virtual-machines/windows/managed-disks-overview)nevezzük.

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>Új adatlemez létrehozása és csatolása a portál használatával

1. A portálon válassza a **minden szolgáltatás**, majd a **virtuális gépek**lehetőséget.
   ![példa: virtuális gép irányítópultja](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2. Válasszon ki egy korábban létrehozott virtuális gépet.
   ![példa: válasszon egy virtuális gépet az irányítópulton](media/azure-stack-manage-vm-disks/select-a-vm.png)

3. A virtuális gép esetében válassza a **lemezek**elemet, majd **adja hozzá az adatlemezt**.
   ![példa: új lemez csatolása a virtuális géphez](media/azure-stack-manage-vm-disks/Attach-disks.png)

4. Adatlemez esetén:
   * Adja meg a **LUN**-t. A logikai egységnek érvényes számnak kell lennie.
   * Válassza a **lemez létrehozása**lehetőséget.
   ![példa: új lemez csatolása a virtuális géphez](media/azure-stack-manage-vm-disks/add-a-data-disk-create-disk.png)

5. A **felügyelt lemez létrehozása** panelen:
   * Adja meg a lemez **nevét** .
   * Válasszon ki egy meglévő **erőforráscsoportot** , vagy hozzon létre egy újat.
   * Válassza ki a **helyet**. Alapértelmezés szerint a hely ugyanarra a tárolóra van beállítva, amely az operációsrendszer-lemezt tárolja.
   * Válassza ki a **fiók típusát**.
      ![példa: új lemez csatolása a virtuális géphez](media/azure-stack-manage-vm-disks/create-manage-disk.png)

      **prémium SSD**  
      A prémium szintű lemezeket (SSD-ket) stabil állapotú meghajtók végzik, és konzisztens, kis késleltetésű teljesítményt nyújtanak. A legjobb egyensúlyt biztosítják az árak és a teljesítmény között, és ideálisak az I/O-igényes alkalmazások és a termelési feladatok számára.

      **standard HDD**  
      A standard szintű lemezek (HDD-ket) a mágneses meghajtókon alapulnak, és olyan alkalmazások számára ajánlottak, amelyekben az adatelérés nem ritkán történik. A Zone-redundáns lemezeket a zóna-redundáns tárolók (ZRS-EK) biztosítják, amelyek több zónában replikálják az adatait, így biztosítva, hogy az adott zóna leállása esetén is elérhetők legyenek az adatai.

   * Válassza ki a **forrás típusát**.

     Lemez létrehozása pillanatképből egy másik lemezről, egy blob egy Storage-fiókban vagy egy üres lemez létrehozása.

      **Pillanatkép**: válasszon ki egy pillanatképet, ha elérhető. A pillanatképnek elérhetőnek kell lennie a virtuális gép előfizetésében és helyén.

      **Tárolási blob**:
     * Adja hozzá a lemezképet tartalmazó Storage-blob URI-JÁT.  
     * Kattintson a **Tallózás** gombra a Storage-fiókok panel megnyitásához. Útmutatásért lásd: [adatlemez hozzáadása egy Storage-fiókból](#add-a-data-disk-from-a-storage-account).
     * Válassza ki a lemezkép operációsrendszer-típusát: **Windows**, **Linux**vagy **none (adatlemez)** .

   * Válassza ki a **méretet (GIB)** .

     A standard szintű lemez költségei a lemez méretétől függően növekednek. A prémium szintű lemezes költségek és a teljesítmény növekedése a lemez mérete alapján. További információ: [Managed Disks díjszabása](https://go.microsoft.com/fwlink/?linkid=843142).

   * Kattintson a **Létrehozás** gombra. Azure Stack létrehozza és ellenőrzi a felügyelt lemezt.

6. Miután Azure Stack létrehozza a lemezt, és csatolja a virtuális géphez, az új lemez megjelenik a virtuálisgép-lemez beállításai között az **ADATlemezek**területen.

   ![Példa: lemez megtekintése](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>Adatlemez hozzáadása egy Storage-fiókból

További információ a Azure Stack lévő Storage-fiókok használatáról: a [Azure stack Storage bemutatása](azure-stack-storage-overview.md).

1. Válassza ki a használni kívánt **Storage-fiókot** .
2. Válassza ki azt a **tárolót** , ahová az adatlemezt helyezni kívánja. A **tárolók** panelen új tárolót hozhat létre, ha szeretné. Ezután módosíthatja az új lemez helyét a saját tárolóján. Ha az egyes lemezekhez külön tárolót használ, a teljesítményt javító adatlemez elhelyezését terjeszti.
3. Válassza **a kijelölés lehetőséget** a kijelölés mentéséhez.

    ![Példa: válasszon egy tárolót](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Meglévő adatlemez csatolása egy virtuális géphez

1. [Készítse elő a](/azure/virtual-machines/windows/classic/createupload-vhd) virtuális gép adatlemezként való használatára szolgáló. vhd-fájlt. Töltse fel a. vhd fájlt egy olyan Storage-fiókba, amelyet azzal a virtuális géppel használ, amelyhez csatlakoztatni kívánja a. vhd-fájlt.

    - Tervezze meg egy másik tároló használatát, hogy a. vhd fájlt tárolja, mint az operációsrendszer-lemezt tartalmazó tároló.  
    - A virtuális merevlemezek Azure-ba való feltöltése előtt kövesse az Azure-ba való [feltöltéshez szükséges Windows VHD vagy VHDX előkészítését](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ismertető témakört.
    - A Migrálás megkezdése előtt tekintse át [a Managed Disks áttelepítésének tervét](https://docs.microsoft.com/azure/virtual-machines/windows/on-prem-to-azure#plan-for-the-migration-to-managed-disks) [Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).
    
    ![Példa: VHD-fájl feltöltése](media/azure-stack-manage-vm-disks/upload-vhd.png)



2. A. vhd-fájl feltöltése után készen áll a virtuális merevlemez csatlakoztatására egy virtuális géphez. A bal oldali menüben válassza a **virtuális gépek**lehetőséget.  
 ![példa: válasszon egy virtuális gépet az irányítópulton](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3. Válassza ki a virtuális gépet a listából.

    ![Példa: válasszon egy virtuális gépet az irányítópulton](media/azure-stack-manage-vm-disks/select-a-vm.png)

4. A virtuális gép lapján válassza a **lemezek**, majd a **meglévő csatolása**lehetőséget.

    ![Példa: meglévő lemez csatolása](media/azure-stack-manage-vm-disks/attach-disks2.png)

5. A **meglévő lemez csatolása** lapon válassza a **vhd-fájl**lehetőséget. Megnyílik a **Storage-fiókok** lap.

    ![Példa: válasszon egy VHD-fájlt](media/azure-stack-manage-vm-disks/select-vhd.png)

6. A **Storage-fiókok**területen válassza ki a használni kívánt fiókot, majd válasszon egy olyan tárolót, amely a korábban feltöltött. vhd fájlt tárolja. Válassza ki a. vhd fájlt, **majd válassza a** kijelölés lehetőséget a kijelölés mentéséhez.

    ![Példa: válasszon egy tárolót](media/azure-stack-manage-vm-disks/select-container2.png)

7. A **meglévő lemez csatolása**területen a kiválasztott fájl a VHD- **fájl**területen jelenik meg. Frissítse a lemez **gazdagép-gyorsítótárazási** beállítását, majd kattintson az **OK gombra** a virtuális gép új lemez-konfigurációjának mentéséhez.

    ![Példa: a VHD-fájl csatolása](media/azure-stack-manage-vm-disks/attach-vhd.png)

8. Miután Azure Stack létrehozza a lemezt, és csatolja a virtuális géphez, az új lemez megjelenik a virtuális gép lemez-beállításai között az **adatlemezek**területen.

    ![Példa: a lemez csatlakoztatásának befejezése](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Több nem felügyelt lemez hozzáadása egy virtuális géphez a PowerShell használatával

A PowerShell használatával kiépítheti a virtuális gépet, és hozzáadhat egy új adatlemezt, vagy egy már meglévő. vhd fájlt is csatolhat adatlemezként.

Az **Add-AzureRmVMDataDisk** parancsmag egy adatlemezt ad hozzá egy virtuális géphez. Létrehozhat egy adatlemezt a virtuális gép létrehozásakor, vagy hozzáadhat egy adatlemezt egy meglévő virtuális géphez. A **VhdUri** paraméter megadásával terjesztheti a lemezeket a különböző tárolókra.

### <a name="add-data-disks-to-a-new-vm"></a>Adatlemezek hozzáadása egy új virtuális géphez

Az alábbi példák PowerShell-parancsokkal hoznak létre egy három adatlemezzel rendelkező virtuális gépet, amelyek mindegyike egy másik tárolóba kerül.

Az első parancs létrehoz egy VM-objektumot, majd a `$VirtualMachine` változóban tárolja. A parancs egy nevet és egy méretet rendel a virtuális géphez:

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
```

A következő három parancs három adatlemez elérési útját rendeli hozzá a `$DataDiskVhdUri01`hoz, `$DataDiskVhdUri02`hoz és `$DataDiskVhdUri03` változóhoz. Adjon meg másik elérési utat az URL-címben a lemezek különböző tárolókra való terjesztéséhez:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Az utolsó három parancs adatlemezeket ad hozzá a `$VirtualMachine`ban tárolt virtuális géphez. Az egyes parancsok a lemez nevét, helyét és további tulajdonságait határozzák meg. Az egyes lemezek URI-JÁT `$DataDiskVhdUri01`, `$DataDiskVhdUri02`és `$DataDiskVhdUri03`tárolja:

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                -VhdUri $DataDiskVhdUri01 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                -VhdUri $DataDiskVhdUri02 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                -VhdUri $DataDiskVhdUri03 -CreateOption Empty
```

A következő PowerShell-parancsok használatával adja hozzá az operációsrendszer-lemezt és a hálózati konfigurációt a virtuális géphez, majd indítsa el az új virtuális gépet:

```powershell
# Set variables
$rgName = "myResourceGroup"
$location = "local"
# Set OS Disk
$osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
$osDiskName = "osDisk"

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
    -CreateOption FromImage -Windows

# Create a subnet configuration
$subnetName = "mySubNet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

# Create a vnet configuration
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
    -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

# Create a public IP
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
    -AllocationMethod Dynamic

# Create a network security group configuration
$nsgName = "myNsg"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule

# Create a NIC configuration
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
-Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

#Create the new VM
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
```

### <a name="add-data-disks-to-an-existing-vm"></a>Adatlemezek hozzáadása meglévő virtuális géphez

Az alábbi példák a PowerShell-parancsok használatával három adatlemezt vesznek fel egy meglévő virtuális géphez. Az első parancs lekéri a **VirtualMachine** nevű virtuális gépet a **Get-AzureRmVM** parancsmag használatával. A parancs a `$VirtualMachine` változóban tárolja a virtuális gépet:

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```

A következő három parancs három adatlemez elérési útját rendeli hozzá a `$DataDiskVhdUri01`hoz, `$DataDiskVhdUri02`hoz és `$DataDiskVhdUri03` változóhoz. A VHD URI-k különböző elérési útjai különböző tárolókat jeleznek a lemez elhelyezéséhez:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

A következő három parancs hozzáadja az adatlemezeket a `$VirtualMachine` változóban tárolt virtuális géphez. Az egyes parancsok a lemez nevét, helyét és további tulajdonságait határozzák meg. Az egyes lemezek URI-JÁT `$DataDiskVhdUri01`, `$DataDiskVhdUri02`és `$DataDiskVhdUri03`tárolja:

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                      -VhdUri $DataDiskVhdUri01 -LUN 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                      -VhdUri $DataDiskVhdUri02 -LUN 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                      -VhdUri $DataDiskVhdUri03 -LUN 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

Az utolsó parancs frissíti a `$VirtualMachine`ban tárolt virtuális gép állapotát `-ResourceGroupName`:

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>Következő lépések

Azure Stack virtuális gépekkel kapcsolatos további tudnivalókért tekintse meg a [Azure Stack Virtual Machinesével kapcsolatos szempontokat](azure-stack-vm-considerations.md).
