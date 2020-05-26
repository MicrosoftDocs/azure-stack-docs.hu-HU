---
title: Virtuális gépek kezelése Azure Stack HCI-ben a Windows PowerShell használatával
description: Virtuális gépek kezelése Azure Stack HCI-ben a Windows PowerShell használatával
author: v-dasis
ms.topic: article
ms.date: 05/20/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 471dc57505832ef58424a74d08f1534a66ea285e
ms.sourcegitcommit: d69eacbf48c06309b00d17c82ebe0ce2bc6552df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83794195"
---
# <a name="manage-vms-on-azure-stack-hci-using-windows-powershell"></a>Virtuális gépek kezelése Azure Stack HCI-ben a Windows PowerShell használatával

> A Windows Server 2019-re vonatkozik

A Windows PowerShell használatával a virtuális gépek (VM-EK) Azure Stack HCI-re hozhatók létre és kezelhetők.

A virtuális gépeket általában egy Windows 10-es rendszert futtató távoli számítógépről kell kezelni, nem pedig a fürtben lévő gazdagépen. Ezt a távoli számítógépet felügyeleti számítógépnek nevezzük.

> [!NOTE]
> Ha felügyeleti számítógépről futtat PowerShell-parancsokat, a-számítógépnév paramétert is adja meg a kezelt gazdagép nevével. A NetBIOS-nevek, az IP-címek és a teljes tartománynevek engedélyezve vannak.

A virtuális gépek PowerShell használatával történő kezelésével kapcsolatos teljes dokumentációért lásd: [Hyper-V referenciák](https://docs.microsoft.com/powershell/module/hyper-v/?view=win10-ps).

## <a name="get-a-list-of-vms"></a>Virtuális gépek listájának lekérése

A következő példa a Kiszolgáló1-ben lévő összes virtuális gép listáját adja vissza.

```powershell
Get-VM -ComputerName Server1
```

Az alábbi példa a kiszolgálón futó virtuális gépek listáját adja vissza egy szűrő hozzáadásával a `Where-Object` paranccsal. További információ: [a WHERE-Object dokumentáció használata](<https://docs.microsoft.com/previous-versions/windows/it-pro/windows-powershell-1.0/ee177028(v=technet.10)>) .

```powershell
Get-VM -ComputerName Server1 | Where-Object -Property State -eq "Running"
```

A következő példa a kiszolgálón lévő összes leállított virtuális gép listáját adja vissza.

```powershell
Get-VM -ComputerName Server1 | Where-Object -Property State -eq "Off"
```

## <a name="start-and-stop-a-vm"></a>Virtuális gép indítása és leállítása

A `Start-VM` és a `Stop-VM` parancsok használatával indítsa el vagy állítsa le a virtuális gépet. Részletes információkat a [Start-VM](https://docs.microsoft.com/powershell/module/hyper-v/start-vm?view=win10-ps) és a [stop-VM](https://docs.microsoft.com/powershell/module/hyper-v/stop-vm?view=win10-ps) dokumentációjában talál.

Az alábbi példa bemutatja, hogyan indíthat el egy VM1 nevű virtuális gépet:

```powershell
Start-VM -Name VM1 -ComputerName Server1
```

A következő példa egy TestVM nevű virtuális gép leállítását mutatja be:

```powershell
Stop-VM -Name VM1 -ComputerName Server1
```

## <a name="move-a-vm"></a>Virtuális gép áthelyezése

A `Move-VM` parancsmag egy virtuális gépet egy másik kiszolgálóra helyez át. További információt a [Move-VM](https://docs.microsoft.com/powershell/module/hyper-v/move-vm?view=win10-ps) dokumentációjában talál.

 Az alábbi példa bemutatja, hogyan helyezhet át egy virtuális gépet a Kiszolgáló2, amikor a virtuális gépet egy SMB-megosztáson tárolja a Kiszolgáló1-ben:

```powershell
Move-VM -ComputerName Server1 -Name VM1 -DestinationHost Server2
 ```

Az alábbi példa bemutatja, hogyan helyezhet át egy virtuális gépet a Kiszolgáló1-ről a Kiszolgáló2-re, és hogyan helyezheti át a virtuális géphez társított összes fájlt a D:\ VM_name a távoli számítógépen:

```powershell
Move-VM -ComputerName Server1 -Name VM1 -DestinationHost Server2 -IncludeStorage -DestinationStoragePath D:\VM_name
```

## <a name="import-or-export-a-vm"></a>Virtuális gép importálása vagy exportálása

A `Import-VM` és a `Export-VM` parancsmag egy virtuális gép importálását és exportálását. Az alábbiakban néhány példát mutatunk be. További információ: [import-VM](https://docs.microsoft.com/powershell/module/hyper-v/import-vm?view=win10-ps) és [export-VM](https://docs.microsoft.com/powershell/module/hyper-v/export-vm?view=win10-ps) Reference dokumentáció.

Az alábbi példa bemutatja, hogyan importálhat egy virtuális gépet a konfigurációs fájljából. A virtuális gép helyben van regisztrálva, ezért a rendszer nem másolja a fájlokat:

```powershell
Import-VM -ComputerName Server1 -Name VM1 -Path 'C:\<vm export path>\2B91FEB3-F1E0-4FFF-B8BE-29CED892A95A.vmcx'
```

A következő példa egy virtuális gépet exportál a D meghajtó gyökerébe:

```powershell
Export-VM -ComputerName Server1 -Name VM1 -Path D:\
```

## <a name="rename-a-vm"></a>Virtuális gép átnevezése

A `Rename-VM` parancsmag használatával lehet átnevezni a virtuális gépeket. Részletes információkért tekintse meg az [Átnevezés – virtuális gép](https://docs.microsoft.com/powershell/module/hyper-v/rename-vm?view=win10-ps) referenciájának dokumentációját.

A következő példa átnevezi a VM1 a VM2, és megjeleníti az átnevezett virtuális gépet:

```powershell
Rename-VM -ComputerName Server1 -Name VM1 -NewName VM2
```

## <a name="create-a-vm-checkpoint"></a>Virtuális gép ellenőrzőpontjának létrehozása

A `Checkpoint-VM` parancsmag egy virtuális gép ellenőrzőpontjának létrehozására szolgál. Részletes információkért tekintse meg az [ellenőrzőpont – virtuális gép](https://docs.microsoft.com/powershell/module/hyper-v/checkpoint-vm?view=win10-ps) dokumentációját.

A következő példa létrehoz egy BeforeInstallingUpdates nevű ellenőrzőpontot a test nevű virtuális géphez.

```powershell
Checkpoint-VM -ComputerName Server1 -Name VM1 -SnapshotName BeforeInstallingUpdates
```

## <a name="create-a-vhd-for-a-vm"></a>Virtuális merevlemez létrehozása virtuális géphez

A `New-VHD` parancsmag használatával új virtuális merevlemez hozható létre egy virtuális géphez. További információ a használatáról: [új VHD-](https://docs.microsoft.com/powershell/module/hyper-v/new-vhd?view=win10-ps) dokumentáció.

A következő példa egy VHDX formátumú dinamikus virtuális merevlemezt hoz létre, amely 10 GB méretű. A fájlnévkiterjesztés határozza meg a formátumot, és az alapértelmezett dinamikus típust használja a rendszer, mert nincs megadva típus.

```powershell
New-VHD -ComputerName Server1 -Name VM1 -Path c:\Base.vhdx -SizeBytes 10GB
```

## <a name="add-a-network-adapter-to-a-vm"></a>Hálózati adapter hozzáadása egy virtuális géphez

A `Add-VMNetworkAdapter` parancsmag használatával egy virtuális hálózati adaptert adhat hozzá egy virtuális géphez. Az alábbiakban néhány példát mutatunk be. A használatáról részletes információt az [Add-VMNetworkAdapter](https://docs.microsoft.com/powershell/module/hyper-v/add-vmnetworkadapter?view=win10-ps) dokumentációjában talál.

Az alábbi példa egy Redmond NIC1 nevű virtuális hálózati adaptert hoz létre egy VM1 nevű virtuális géphez:

```powershell
Add-VMNetworkAdapter -ComputerName Server1 -VMName VM1 -Name "Redmond NIC1"
```

Ez a példa egy virtuális hálózati adaptert hoz létre egy VM1 nevű virtuális géphez, és csatlakoztatja azt egy hálózat nevű virtuális kapcsolóhoz:

```powershell
Add-VMNetworkAdapter -ComputerName Server1 -VMName VM1 -SwitchName Network
```

## <a name="create-a-virtual-switch-for-a-vm"></a>Virtuális kapcsoló létrehozása virtuális GÉPHEZ

A `New-VMSwitch` parancsmag a virtuálisgép-gazdagépek új virtuális kapcsolójának használatára szolgál. A használatával kapcsolatos részletes információkat a [New-VMSwitch](https://docs.microsoft.com/powershell/module/hyper-v/new-vmswitch?view=win10-ps) Reference dokumentációban talál.

Az alábbi példa egy "QoS kapcsoló" nevű új kapcsolót hoz létre, amely egy vezetékes Ethernet-kapcsolat 3 nevű hálózati adapterhez kötődik, és támogatja a súlyozási minimális sávszélességet.

```powershell
New-VMSwitch "QoS Switch" -NetAdapterName "Wired Ethernet Connection 3" -MinimumBandwidthMode Weight
```

## <a name="set-memory-for-a-vm"></a>Virtuális gép memóriájának beállítása

A `Set-VMMemory` parancsmag a virtuális gép memóriájának konfigurálására szolgál. A használatáról részletes információt a [set-VMMemory](https://docs.microsoft.com/powershell/module/hyper-v/set-vmmemory?view=win10-ps) dokumentációjában talál.

A következő példa egy VM1 nevű virtuális gépen engedélyezi a dinamikus memóriát, beállítja a minimális, indítási és maximális memóriát, a memória prioritását és a pufferét.

```powershell
Set-VMMemory -ComputerName Server1 -Name VM1 -DynamicMemoryEnabled $true -MinimumBytes 64MB -StartupBytes 256MB -MaximumBytes 2GB -Priority 80 -Buffer 25
```

## <a name="set-virtual-processors-for-a-vm"></a>Virtuális processzorok beállítása virtuális GÉPHEZ

A `Set-VMProcessor` parancsmag használatával konfigurálhatja a virtuális gép virtuális processzorait. A használatáról részletes információt a [set-VMProcessor](https://docs.microsoft.com/powershell/module/hyper-v/set-vmprocessor?view=win10-ps
) dokumentációjában talál.

A következő példa egy VM1 nevű virtuális GÉPET konfigurál két virtuális processzorral, 10%-os tartaléktal, 75%-os korláttal és 200 relatív súlyozással.

```powershell
Set-VMProcessor -ComputerName Server1 -Name VM1 -Count 2 -Reserve 10 -Maximum 75 -RelativeWeight 200
```

## <a name="create-a-vm"></a>Virtuális gép létrehozása  

A `New-VM` parancsmag egy új virtuális gép létrehozásához használatos. A részletes használatért tekintse meg a [New-VM](https://docs.microsoft.com/powershell/module/hyper-v/new-vm?view=win10-ps) Reference dokumentációját.

A következő beállítások adhatók meg, amikor új virtuális GÉPET hoz létre egy meglévő virtuális merevlemezzel, ahol:

- A **-Name** a létrehozandó virtuális gép nevét adja meg.

- **– A MemoryStartupBytes** a virtuális gép számára elérhető memória mennyisége.

- **– A BootDevice** az az eszköz, amelyet a virtuális gép a indításakor elindul.
 Ez általában egy virtuális merevlemez (VHD), egy. ISO fájl DVD-alapú rendszerindításhoz vagy hálózati adapter (NetworkAdapter) a hálózati rendszerindításhoz.

- **– A VHDPath** a használni kívánt virtuális gép lemezének elérési útja.

- **– Az elérési** út a virtuális gép konfigurációs fájljainak tárolási útvonala.

- **– A generáció** a virtuális gép generációja. Használja az 1. generációs virtuális merevlemezt és a 2. generációt a VHDX.

- **– A kapcsoló** annak a virtuális kapcsolónak a neve, amelyet a virtuális gépnek más virtuális gépekhez vagy hálózathoz való kapcsolódáshoz használni kíván. Szerezze be a virtuális kapcsoló nevét a [Get-VMSwitch](https://docs.microsoft.com/powershell/module/hyper-v/get-vmswitch?view=win10-ps)használatával. Például:  

Az VM1 nevű virtuális gép létrehozásához a következő teljes parancs szükséges:

 ```powershell
 New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes <Memory> -BootDevice <BootDevice> -VHDPath <VHDPath> -Path <Path> -Generation <Generation> -Switch <SwitchName>
 ```

A következő példa egy 2. generációs virtuális gépet hoz létre 4 GB memóriával. Elindul az aktuális könyvtár VMs\Win10.vhdx, és a ExternalSwitch nevű virtuális kapcsolót használja. A virtuális gép konfigurációs fájljai a VMData mappában vannak tárolva.  

``` powershell
New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes 4GB -BootDevice VHD -VHDPath .\VMs\Win10.vhdx -Path .\VMData -Generation 2 -Switch ExternalSwitch
```

A következő paraméterek használhatók a virtuális merevlemezek megadásához.

Ha új virtuális merevlemezzel szeretné létrehozni a virtuális gépet, cserélje le a- **VHDPath** paramétert a fenti példában a **-NewVHDPath** értékre, és adja hozzá a **-NewVHDSizeBytes** paramétert az itt látható módon:  

``` powershell  
New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes 4GB -BootDevice VHD -NewVHDPath .\VMs\Win10.vhdx -Path .\VMData -NewVHDSizeBytes 20GB -Generation 2 -Switch ExternalSwitch  
 ```  

Ha olyan virtuális gépet szeretne létrehozni, amely az operációs rendszer lemezképéhez tartozó új virtuális lemezzel rendelkezik, tekintse meg a [Windows 10 rendszerű Hyper-V virtuális gép létrehozása](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_create_vm)bemutatójának PowerShell-példáját.  

## <a name="next-steps"></a>További lépések  

A virtuális gépeket a Windows felügyeleti központban is létrehozhatja és kezelheti. További információ: [Windows felügyeleti központ](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview).