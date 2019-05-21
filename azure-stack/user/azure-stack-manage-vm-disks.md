---
title: Az Azure Stackben Virtuálisgép-lemezek kezelése |} A Microsoft Docs
description: Virtuális gépek lemezeinek létrehozása az Azure Stackben.
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
ms.date: 05/20/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 10900095a79da2639212b5fa58becbafb7bee0eb
ms.sourcegitcommit: d2012e765c3fa5bccb4756d190349e890f9f48bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65941197"
---
# <a name="create-virtual-machine-disk-storage-in-azure-stack"></a>Lemezes tárolás virtuális gép létrehozása az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ez a cikk ismerteti, hogyan hozhat létre a virtuális gép lemezes tárolás az Azure Stack-portál használatával, vagy a PowerShell használatával.

## <a name="overview"></a>Áttekintés

Azure Stack verziójától kezdve 1808, virtual machines szolgáltatásban, az operációs rendszer (OS) és a egy adatlemezt is támogatja a felügyelt lemezek és a nem felügyelt lemezek használata. A következőnél régebbi verziók 1808 csak a nem felügyelt lemezek támogatottak.

[A Managed disks](/azure/virtual-machines/windows/managed-disks-overview) az Azure IaaS virtuális gépek lemezfelügyelet egyszerűsítése a virtuális gépek lemezeihez társított storage-fiókok kezelésével. Csak meg kell adnia a kívánt lemez méretét, és az Azure létrehozza és felügyeli a lemezt Ön helyett.

Nem felügyelt lemezekre van szükség, hogy a lemezek tárolására tárfiók létrehozása. A lemezeket hoz létre Virtuálisgép-lemezeket nevezik, és a tárfiókban lévő tárolók vannak tárolva.

### <a name="best-practice-guidelines"></a>Gyakorlati előírásai

Javíthatja a teljesítményt, és az általános költségek csökkentése, ajánlott minden egyes Virtuálisgép-lemez helyez egy külön tárolót. Egy tároló, operációsrendszer-lemezt vagy egy adatlemez, de nem mindkettőt egyszerre rendelkezzen. Azonban akkor is helyezheti lemezt mindkét típusú ugyanabban a tárolóban.

Egy vagy több adatlemezt ad hozzá egy virtuális Gépet, ha további tárolók használatával, egy helyen tárolja ezeket a lemezeket. Az operációsrendszer-lemez további virtuális gépeket is kell saját tárolókban.

Amikor virtuális gépeket hoz létre, felhasználhatja ugyanazt a tárfiókot minden egyes új virtuális géphez. Csak a tárolók létrehozása egyedinek kell lennie.

### <a name="adding-new-disks"></a>Új lemezek hozzáadását

Az alábbi táblázat foglalja össze a lemezek hozzáadása a portál használatával, és a PowerShell használatával:

| Módszer | Beállítások
|-|-|
|Felhasználói portál|-Új adatlemezek hozzáadása egy meglévő virtuális Gépet. Azure Stack új lemezeket hoznak létre. </br> </br>– A meglévő lemezt (.vhd) fájl hozzáadása a korábban létrehozott virtuális géphez. Ehhez kell előkészíteni a .vhd és majd feltöltjük a fájlt az Azure Stackhez. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | – Új virtuális gép létrehozása az operációsrendszer-lemezt, és a egy időben adjon hozzá egy vagy több adatlemezt a virtuális Gépre. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Lemezek hozzáadása egy virtuális Géphez a portál használatával

Alapértelmezés szerint a legtöbb Piactéri elemek, a virtuális gép létrehozása a portál használatakor csak az operációsrendszer-lemez jön létre.

Miután létrehozott egy virtuális Gépet, a portálon is használhatja:

* Hozzon létre egy új adatlemezt, és csatlakoztassa a virtuális Gépet.
* Töltse fel a már meglévő adatlemez, és csatlakoztassa a virtuális Gépet.

Minden nem felügyelt lemez hozzáadása egy külön tárolóban kell elhelyezni.

>[!NOTE]  
>Lemezek létrehozása és felügyelete az Azure-ban az úgynevezett [felügyelt lemezek](/azure/virtual-machines/windows/managed-disks-overview).

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>A portál segítségével létrehozhat, és a egy új adatlemez csatolása

1. Válassza a portál **minden szolgáltatás**, majd **virtuális gépek**.
   ![Példa: Virtuális gép irányítópult](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2. Válassza ki a korábban létrehozott virtuális gép.
   ![Példa: Válasszon egy virtuális Gépet az irányítópulton](media/azure-stack-manage-vm-disks/select-a-vm.png)

3. Válassza ki a virtuális gép **lemezek**, majd **Hozzáadás adatlemez**.
   ![Példa: Új lemez csatolása a virtuális géphez](media/azure-stack-manage-vm-disks/Attach-disks.png)

4. Az adatlemez:
   * Adja meg a **LUN**. A logikai egység érvényes számnak kell lennie.
   * Válassza ki **létrehozása lemez**.
   ![Példa: Új lemez csatolása a virtuális géphez](media/azure-stack-manage-vm-disks/add-a-data-disk-create-disk.png)

5. Az a **létrehozása felügyelt lemez** panelen:
   * Adja meg a **neve** a lemezen.
   * Válasszon egy meglévő, vagy hozzon létre egy új **erőforráscsoport**.
   * Válassza ki a **hely**. Alapértelmezés szerint a hely ugyanazt a tárolót, amely tárolja az operációsrendszer-lemez értéke.
   * Válassza ki a **fiók típusa**.
      ![Példa: Új lemez csatolása a virtuális géphez](media/azure-stack-manage-vm-disks/create-manage-disk.png)

      **Prémium szintű SSD**  
      Felügyelt lemezek prémium szintű (SSD) élvezik SSD-meghajtókat, és egységes, közel valós idejű teljesítményt. A legjobb ára és teljesítménye közötti egyensúlyt biztosítanak, és ideálisak a nagy I/O-igényes alkalmazások és a termelési számítási feladatokhoz.

      **Standard HDD**  
      A standard szintű lemezek (HDD) mágneses meghajtók élvezik és alkalmazások általában hol adatokhoz ritkán. Zónaredundáns lemezeket élvezik a zónaredundáns tárolás (ZRS), amely az adatokat több zónában replikálja, és elérhetők, még akkor is, ha egy zóna nem működik.

   * Válassza ki a **adatforrástípust**.

     Lemez létrehozása egy másik lemez pillanatképéből vagy egy tárfiókbeli blobból, vagy üres lemez létrehozása.

      **Snapshot**: Válassza ki a pillanatképet, ha rendelkezésre áll. A pillanatkép kell lennie a virtuális gép előfizetésben és helyen érhető el.

      **A tárolási blob**:
     * Adja hozzá az URI-ját a Storage-blobból, amely tartalmazza a lemezképet.  
     * Válassza ki **Tallózás** a Storage-fiókok panel megnyitásához. Útmutatásért lásd: [adatlemez hozzáadása egy tárfiókból](#add-a-data-disk-from-a-storage-account).
     * Válassza ki az operációs rendszer típusa, a lemezkép vagy **Windows**, **Linux**, vagy **nincs (adatlemez)**.

   * Válassza ki a **mérete (GiB)**.

     Standard szintű lemez díjai a lemezmérettel arányosan a lemez mérete. Prémium szintű lemez díjai és teljesítménye növelje a lemez mérete alapján. További információkért lásd: [Managed Disks díjszabását ismertető](https://go.microsoft.com/fwlink/?linkid=843142).

   * Kattintson a **Létrehozás** gombra. Az Azure Stack hoz létre, és érvényesíti a felügyelt lemez.

6. Után az Azure Stack a lemezt hoz létre, és csatlakoztatja a virtuális gép, az új lemez szerepel-e a virtuálisgép-lemez beállítások alatt **ADATLEMEZEK**.

   ![Példa: Lemez megtekintése](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>Adatlemez hozzáadása egy storage-fiókból

Tárfiókok az Azure Stack használatával kapcsolatos további információkért lásd: [storage Azure Stack bemutatása](azure-stack-storage-overview.md).

1. Válassza ki a **tárfiók** használatára.
2. Válassza ki a **tároló** kívánja helyezze az adatokat. Az a **tárolók** panelen létrehozhat egy új tárolót, ha azt szeretné. A saját tároló majd módosítsa a helyet az új lemez. Az egyes lemezek használatakor egy külön tárolót, terjesztése, javíthatja a teljesítményt az adatlemez elhelyezését.
3. Válasszon **kiválasztása** a mentéshez.

    ![Példa: Tároló kiválasztása](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Egy már meglévő adatlemez csatolása a virtuális géphez

1. [Készítse elő a .vhd fájlt](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) Virtuálisgép-adatlemez használhatók. A .vhd fájlt feltölteni egy storage-fiókot, amelyet a virtuális gép, amelyhez a .vhd fájlt csatolni szeretné.

    Tervezze meg használni egy másik tárolóba, amely tárolja a .vhd fájlt, mint a tároló, amely az operációsrendszer-lemez.
    ![Példa: A VHD-fájl feltöltése](media/azure-stack-manage-vm-disks/upload-vhd.png)

2. Miután feltöltötte a .vhd fájlt, készen áll a virtuális merevlemez csatlakoztatása egy virtuális Gépet. A bal oldali menüben válassza **virtuális gépek**.  
 ![Példa: Válasszon egy virtuális Gépet az irányítópulton](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3. Válassza ki a virtuális gépet a listából.

    ![Példa: Válasszon egy virtuális Gépet az irányítópulton](media/azure-stack-manage-vm-disks/select-a-vm.png)

4. Válassza ki a lapon a virtuális gép **lemezek**, majd **csatolása meglévő**.

    ![Példa: Meglévő lemez csatlakoztatása](media/azure-stack-manage-vm-disks/attach-disks2.png)

5. Az a **meglévő lemez csatolása** lapon jelölje be **VHD-fájl**. A **tárfiókok** lap megnyitásakor.

    ![Példa: Válassza ki a VHD-fájl](media/azure-stack-manage-vm-disks/select-vhd.png)

6. A **tárfiókok**, válassza ki a használni kívánt fiók, és válassza a korábban feltöltött .vhd fájlt tartalmazó tároló. Válassza ki a .vhd fájlt, és válassza **kiválasztása** a mentéshez.

    ![Példa: Tároló kiválasztása](media/azure-stack-manage-vm-disks/select-container2.png)

7. A **meglévő lemez csatolása**, a kiválasztott fájl részen **VHD-fájl**. Frissítés a **gazdagép gyorsítótárazási** beállítást a lemezen, és válassza ki **OK** az új lemez konfigurációját a virtuális gép mentéséhez.

    ![Példa: A VHD-fájl csatolása](media/azure-stack-manage-vm-disks/attach-vhd.png)

8. Után az Azure Stack a lemezt hoz létre, és csatlakoztatja a virtuális gép, az új lemez szerepel-e a virtuális gép lemezbeállításokat alatt **adatlemezek**.

    ![Példa: Befejeződött a lemez csatolása](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Több felügyelt lemez hozzáadása virtuális Géphez a PowerShell használatával

Virtuális gép létrehozása és a egy új adatlemez hozzáadása PowerShell használatával, vagy egy előzetesen meglévő lemez csatolása **.vhd** fájl adatlemezként.

A **Add-AzureRmVMDataDisk** parancsmag hozzáadja egy adatlemezt egy virtuális géphez. Hozzáadhat egy adatlemezt hoz létre virtuális gépet, vagy egy adatlemezt is hozzáadhat egy meglévő virtuális gépet. Adja meg a **VhdUri** elosztása a különböző tárolók lemezeket paramétert.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>Adatlemez hozzáadása egy új virtuális gép

Az alábbi példák három adatlemezekkel rendelkező virtuális gép létrehozása PowerShell-parancsok használatával, egy másik tárolóba helyezett minden egyes.

Az első parancs létrehoz egy virtuálisgép-objektumot, és tárolják a a `$VirtualMachine` változó. A parancs a virtuális gép nevét és méretét rendeli hozzá:

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
```

A következő három parancsok rendelje hozzá a három adatlemez, az útvonalak a `$DataDiskVhdUri01`, `$DataDiskVhdUri02`, és `$DataDiskVhdUri03` változókat. Adja meg egy másik elérési utat az URL-címben elosztása a különböző tárolók lemezeket:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Az utolsó három parancsok hozzáadhat adatlemezeket a tárolt virtuális gép `$VirtualMachine`. Minden egyes parancsot adja meg a nevét, helyét és a lemez további tulajdonságok. URI-ját az egyes lemezeken tárolja `$DataDiskVhdUri01`, `$DataDiskVhdUri02`, és `$DataDiskVhdUri03`:

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

A következő PowerShell-parancsok használatával az operációs rendszer lemez és hálózat konfigurációja hozzáadása a virtuális Géphez, és indítsa el az új virtuális Géphez:

```powershell
#set variables
$rgName = "myResourceGroup"
$location = "local"
#Set OS Disk
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

### <a name="add-data-disks-to-an-existing-virtual-machine"></a>Adatlemez hozzáadása egy meglévő virtuális gép

Az alábbi példák a PowerShell-parancsok használatával három adatlemezek hozzáadása egy meglévő virtuális Gépet. Az első parancs lekéri a virtuális gép nevű **VirtualMachine** használatával a **Get-AzureRmVM** parancsmagot. A parancs tárolja a virtuális gépet a `$VirtualMachine` változó:

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```

A következő három parancsok rendelje hozzá a három adatlemez, az útvonalak a `$DataDiskVhdUri01`, `$DataDiskVhdUri02`, és `$DataDiskVhdUri03` változókat. A másik elérési utat nevek a virtuális merevlemez URI-k azt jelzik, hogy a lemez elhelyezésre különböző tárolók:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

A következő három parancsokat az adatlemezek hozzáadása a tárolt virtuális gép a `$VirtualMachine` változó. Minden egyes parancsot adja meg a nevét, helyét és a lemez további tulajdonságok. URI-ját az egyes lemezeken tárolja `$DataDiskVhdUri01`, `$DataDiskVhdUri02`, és `$DataDiskVhdUri03`:

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

Az utolsó parancs frissíti a tárolt virtuális gép `$VirtualMachine` a `-ResourceGroupName`:

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>További lépések

Virtuális gépek az Azure Stack kapcsolatos további információkért lásd: [szempontok a virtuális gépek az Azure Stackben](azure-stack-vm-considerations.md).
