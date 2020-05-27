---
title: Azure Stack HCI-fürtök kezelése a PowerShell-lel
description: Ismerje meg, hogyan kezelheti a fürtöket Azure Stack HCI-ben a PowerShell használatával
author: v-dasis
ms.topic: article
ms.date: 05/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 922a6188c1529d3461288ac28ff542db6fa9a527
ms.sourcegitcommit: f2203f070c17e19ba1a41d681662bac3ab50ed12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83855684"
---
# <a name="manage-azure-stack-hci-clusters-using-powershell"></a>Azure Stack HCI-fürtök kezelése a PowerShell-lel

> A Windows Server 2019-re vonatkozik

A Windows PowerShell segítségével kezelheti az erőforrásokat, és konfigurálhatja a Azure Stack HCI-fürtök szolgáltatásait.

A fürtöket általában egy Windows 10 rendszert futtató távoli számítógépről kezelik, nem a fürtben lévő gazdagépen. Ezt a távoli számítógépet felügyeleti számítógépnek nevezzük.

> [!NOTE]
> Ha felügyeleti számítógépről futtat PowerShell-parancsokat, a vagy a `-Name` `-Cluster` paramétert adja meg a kezelt fürt nevével. Emellett meg kell adnia a teljes tartománynevet (FQDN) is, ha a paramétert használja a `-ComputerName` kiszolgálói csomóponthoz.

A fürtök PowerShell használatával történő kezelésével kapcsolatos teljes dokumentációért tekintse meg a [FailoverCluster-referenciát](https://docs.microsoft.com/powershell/module/failoverclusters/?view=win10-ps).

## <a name="using-windows-powershell"></a>A Windows PowerShell használata

A Windows PowerShell a jelen cikk összes feladatának elvégzésére szolgál. Javasoljuk, hogy a kényelmes használat érdekében rögzítse az alkalmazást a tálcán.

Ha az alábbi parancsmagok nem érhetők el a PowerShell-munkamenetben, előfordulhat, hogy hozzá kell adnia a `Failover Cluster` Windows PowerShell szolgáltatáshoz tartozó modult a következő PowerShell-cmd használatával: `Add-WindowsFeature RSAT-Clustering-PowerShell` .

> [!NOTE]
> A Windows 10 2018-es és újabb verziójának frissítése után az RSAT "igény szerinti szolgáltatások"-készletként szerepel a Windows 10 rendszertől kezdve. Egyszerűen lépjen a **beállítások > alkalmazások > alkalmazások & funkciók > választható funkciók > Hozzáadás a szolgáltatás > RSAT: feladatátvételi fürtszolgáltatás eszközei**, és válassza a **telepítés**lehetőséget. A telepítési folyamat megjelenítéséhez kattintson a Vissza gombra az állapot megtekintéséhez a "választható szolgáltatások kezelése" lapon. A telepített szolgáltatás a Windows 10 verziófrissítése alatt is megmarad.

## <a name="view-cluster-settings-and-resources"></a>A fürt beállításainak és erőforrásainak megtekintése

Információk beolvasása egy Cluster1 nevű fürtről:

```powershell
Get-Cluster -Name Cluster1
```
Információk beolvasása egy vagy több csomópontról vagy kiszolgálóról a Cluster1-ben:

```powershell
Get-ClusterNode -Cluster Cluster1
```

Ha szeretné megtekinteni, hogy mely Windows-szolgáltatások vannak telepítve a fürtcsomóponton, használja a `Get-WindowsFeature` parancsmagot. Például:

```powershell
Get-WindowsFeature -ComputerName Server1
```

A hálózati adapterek és tulajdonságaik, például a név, az IPv4-címek és a VLAN-azonosító megjelenítéséhez:

```powershell
Get-NetAdapter -CimSession Server1 | Where Status -Eq "Up" | Sort InterfaceAlias | Format-Table Name, InterfaceDescription, Status, LinkSpeed, VLANID, MacAddress
```

A Hyper-V virtuális kapcsolók és a fizikai hálózati adapterek összevonása:

```powershell
Get-VMSwitch -ComputerName Server1
```

A gazdagép virtuális hálózati adapterek megjelenítéséhez:

```powershell
Get-VMNetworkAdapter -ComputerName Server1
```

Annak megállapítása, hogy engedélyezve van-e a Közvetlen tárolóhelyek:

```powershell
Get-CimSession -ComputerName Server1 | Get-ClusterStorageSpacesDirect
```

## <a name="start-or-stop-a-cluster"></a>Fürt elindítása vagy leállítása

A `Start-Cluster` és a `Stop-Cluster` parancsmag használatával adjon hozzá vagy távolítson el egy kiszolgálói csomópontot a fürthöz. További példákat és használati információkat a [Start-cluster](https://docs.microsoft.com/powershell/module/failoverclusters/start-cluster?view=win10-ps) és a [stop-cluster](https://docs.microsoft.com/powershell/module/failoverclusters/stop-cluster?view=win10-ps) dokumentációban talál.

A Fürtszolgáltatás elindítása azon fürt összes kiszolgáló-csomópontján, amelyen még nem indult el:

```powershell
Start-Cluster -Name Cluster1
```

Ez a példa leállítja a Fürtszolgáltatást a Cluster1 nevű fürt összes csomópontján, amely leállítja a fürtben konfigurált összes szolgáltatást és alkalmazást:

```powershell
Stop-Cluster -Name Cluster1
```

## <a name="add-or-remove-a-server"></a>Kiszolgáló hozzáadása vagy eltávolítása

A `Add-ClusterNode` és a `Remove-ClusterNode` parancsmag használatával adjon hozzá vagy távolítson el egy kiszolgálói csomópontot a fürthöz. További példák és használati információk: [Add-fürtcsomópont](https://docs.microsoft.com/powershell/module/failoverclusters/add-clusternode?view=win10-ps) and [Remove-fürtcsomópont](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusternode?view=win10-ps) Reference dokumentáció.

Ez a példa egy Csomópont4 nevű kiszolgálót helyez el egy Cluster1 nevű fürthöz. Először ellenőrizze, hogy a kiszolgáló be van-e kapcsolva és csatlakoztatva van-e a fürt hálózathoz.

```powershell
Add-ClusterNode -Cluster Cluster1 -Name Node4
```

Ez a példa eltávolítja a csomópont4 nevű csomópontot a fürt Cluster1:

```powershell
Remove-ClusterNode -Cluster Cluster1 -Name Node4
```

## <a name="set-quorum-options"></a>Kvórum beállításainak megadása

A `Set-ClusterQuorum` parancsmag használatával állítsa be a fürt Kvórumának beállításait. További példák és használati információk: [set-ClusterQuorum](https://docs.microsoft.com/powershell/module/failoverclusters/set-clusterquorum?view=win10-ps) Reference dokumentáció.

Ez a példa a kvórum konfigurációját úgy módosítja, hogy Felhőbeli tanúsító használjon a fürt Cluster1:

```powershell
Set-ClusterQuorum -Cluster Cluster1 -CloudWitness
```

Ez a példa a kvórum konfigurációját a fürt Cluster1 lévő csomópont-és fájlmegosztás többségére módosítja, a \\ tanúsító fájlmegosztás fileserver\fsw a következő helyen:.

```powershell
Set-ClusterQuorum -Cluster Cluster1 -NodeAndFileShareMajority \\fileserver\fsw
```

## <a name="enable-storage-spaces-direct"></a>A Közvetlen tárolóhelyek engedélyezése

A `Enable-ClusterStorageSpacesDirect` parancsmag használatával engedélyezze közvetlen tárolóhelyek a fürtön. További példák és használati információk: [enable-ClusterStorageSpacesDirect](https://docs.microsoft.com/powershell/module/failoverclusters/enable-clusterstoragespacesdirect?view=win10-ps) Reference dokumentáció.

Ez a példa engedélyezi a Közvetlen tárolóhelyekt a Kiszolgáló1-ben:

```powershell
Enable-ClusterStorageSpacesDirect -CimSession Cluster1
```

## <a name="configure-a-hyper-v-host"></a>Hyper-V-gazdagép konfigurálása

A `Set-VMHost` parancsmaggal különböző Hyper-V-gazdagép-beállításokat konfigurálhat, például a VHD-t és a virtuális gépek elérési útját, az élő áttelepítést, a tárterület-áttelepítést, a hitelesítést, a Numa-átfedést További példák és használati információk: [enable-ClusterStorageSpacesDirect](https://docs.microsoft.com/powershell/module/hyper-v/Set-VMHost?view=win10-ps) Reference dokumentáció.

Ez a példa a virtuális merevlemezek és virtuális gépek új alapértelmezett helyét adja meg a gazdagépen a Kiszolgáló1 kiszolgálón:

```powershell
Set-VMHost -ComputerName Server1 -VirtualHardDiskPath "C:\Hyper-V\Virtual Hard Disks" -VirtualMachinePath "C:\Hyper-V\Configuration Files"
```

Ez a példa úgy konfigurálja a gazdagép-kiszolgáló Kiszolgáló1-t, hogy 10 egyidejű élő áttelepítést és tárterület-áttelepítést engedélyezzen:

```powershell
Set-VMHost -ComputerName Server1 -MaximumVirtualMachineMigrations 10 -MaximumStorageMigrations 10
```

Ez a példa úgy konfigurálja a gazdagép-kiszolgáló Kiszolgáló1-t, hogy Kerberos használatával hitelesítse a bejövő élő áttelepítéseket:

```powershell
Set-VMHost -ComputerName Server1 -VirtualMachineMigrationAuthenticationType Kerberos
```

## <a name="validate-a-cluster"></a>Fürt ellenőrzése

Az `Test-Cluster` ellenőrző tesztek futtatásához használja a parancsmagot egy fürtön. További példákat és használati információkat a [test-cluster](https://docs.microsoft.com/powershell/module/failoverclusters/test-cluster?view=win10-ps) dokumentációjában talál.

Ez a példa a Cluster1 nevű fürtön futtatja a fürt összes vonatkozó ellenőrző tesztjét:

```powershell
Test-Cluster -Cluster Cluster1
```

Ez a példa felsorolja az összes teszt és kategória nevét a fürt érvényesítése során. Adja meg ezeket a teszt neveket *figyelmen kívül hagyva* *, vagy adjon* meg paramétereket az adott tesztek futtatásához:


```powershell
Test-Cluster -Cluster Cluster1 -List
Category                                DisplayName                             Description
--------                                -----------                             -----------
Cluster Configuration                   List Cluster Core Groups                List information about the available...
Cluster Configuration                   List Cluster Network Information        List cluster-specific network settin...
Cluster Configuration                   List Cluster Resources                  List the resources that are configur...
Cluster Configuration                   List Cluster Volumes                    List information for the volumes in ...
Cluster Configuration                   List Clustered Roles                    List information about clustered roles.
Cluster Configuration                   Validate Quorum Configuration           Validate that the current quorum con...
Cluster Configuration                   Validate Resource Status                Validate that cluster resources are ...
Cluster Configuration                   Validate Service Principal Name         Validate that a Service Principal Na...
Cluster Configuration                   Validate Volume Consistency             If any volumes are flagged as incons...
Hyper-V Configuration                   List Information About Servers Runni... List Hyper-V related information on ...
Hyper-V Configuration                   Validate Compatibility of Virtual Fi... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Memory Resource Poo... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Network Resource Po... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Processor Resource ... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Role Installed         Validate that all the nodes have the...
Hyper-V Configuration                   Validate Hyper-V Storage Resource Po... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Matching Processor Manufact... Validate that all specified nodes sh...
Hyper-V Configuration                   List Hyper-V Virtual Machine Informa... List Hyper-V virtual machine informa...
Hyper-V Configuration                   Validate Hyper-V Integration Service... Validate that the Hyper-V integratio...
Hyper-V Configuration                   Validate Hyper-V Virtual Machine Net... Validate that all virtual machines o...
Hyper-V Configuration                   Validate Hyper-V Virtual Machine Sto... Validate that all virtual machines o...
Inventory                               List Fibre Channel Host Bus Adapters    List Fibre Channel host bus adapters...
Inventory                               List iSCSI Host Bus Adapters            List iSCSI host bus adapters on each...
Inventory                               List SAS Host Bus Adapters              List Serial Attached SCSI (SAS) host...
Inventory                               List BIOS Information                   List BIOS information from each node...
Inventory                               List Environment Variables              List environment variables set on ea...
Inventory                               List Memory Information                 List memory information for each node.
Inventory                               List Operating System Information       List information about the operating...
Inventory                               List Plug and Play Devices              List Plug and Play devices on each n...
Inventory                               List Running Processes                  List the running processes on each n...
Inventory                               List Services Information               List information about the services ...
Inventory                               List Software Updates                   List software updates that have been...
Inventory                               List System Drivers                     List the system drivers on each node.
Inventory                               List System Information                 List system information such as comp...
Inventory                               List Unsigned Drivers                   List the unsigned drivers on each node.
Network                                 List Network Binding Order              List the order in which networks are...
Network                                 Validate Cluster Network Configuration  Validate the cluster networks that w...
Network                                 Validate IP Configuration               Validate that IP addresses are uniqu...
Network                                 Validate Multiple Subnet Properties     For clusters using multiple subnets,...
Network                                 Validate Network Communication          Validate that servers can communicat...
Network                                 Validate Windows Firewall Configuration Validate that the Windows Firewall i...
Storage                                 List Disks                              List all disks visible to one or mor...
Storage                                 List Potential Cluster Disks            List disks that will be validated fo...
Storage                                 Validate CSV Network Bindings           Validate that network bindings requi...
Storage                                 Validate CSV Settings                   Validate that settings and configura...
Storage                                 Validate Disk Access Latency            Validate acceptable latency for disk...
Storage                                 Validate Disk Arbitration               Validate that a node that owns a dis...
Storage                                 Validate Disk Failover                  Validate that a disk can fail over s...
Storage                                 Validate File System                    Validate that the file system on dis...
Storage                                 Validate Microsoft MPIO-based disks     Validate that disks that use Microso...
Storage                                 Validate Multiple Arbitration           Validate that in a multiple-node arb...
Storage                                 Validate SCSI device Vital Product D... Validate uniqueness of inquiry data ...
Storage                                 Validate SCSI-3 Persistent Reservation  Validate that storage supports the S...
Storage                                 Validate Simultaneous Failover          Validate that disks can fail over si...
System Configuration                    Validate Active Directory Configuration Validate that all the nodes have the...
System Configuration                    Validate All Drivers Signed             Validate that tested servers contain...
System Configuration                    Validate Cluster Service and Driver ... Validate startup settings used by se...
System Configuration                    Validate Memory Dump Settings           Validate that none of the nodes curr...
System Configuration                    Validate Operating System Edition       Validate that all tested servers are...
System Configuration                    Validate Operating System Installati... Validate that the operating systems ...
System Configuration                    Validate Operating System Version       Validate that the operating systems ...
System Configuration                    Validate Required Services              Validate that services required for ...
System Configuration                    Validate Same Processor Architecture    Validate that all servers run as 64-...
System Configuration                    Validate Service Pack Levels            Validate that all servers with same ...
System Configuration                    Validate Software Update Levels         Validate that all tested servers hav...
System Configuration                    Validate System Drive Variable          Validate that all nodes have the sam...
```

## <a name="remove-cluster-and-resources"></a>Fürt és erőforrások eltávolítása

A `Remove-ClusterResource` parancsmag használatával távolítsa el a fürt egy vagy több erőforrását. További példák és használati információk: [Remove-ClusterResource](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusterresource?view=win10-ps) Reference dokumentáció.

> [!NOTE]
> A fürt eltávolításához átmenetileg engedélyeznie kell a hitelesítő adatok biztonsági szolgáltatójának (CredSSP) hitelesítését. További információ: [enable-WSManCredSSP](https://docs.microsoft.com/powershell/module/microsoft.wsman.management/enable-wsmancredssp?view=powershell-7).

A következő példa eltávolítja a fürt erőforrásait név alapján a fürt Cluster1:

```powershell
Remove-ClusterResource -Cluster Cluster1 -Name "Cluster Disk 4"
```

Ez a példa teljesen eltávolítja a fürt Cluster1 a `Remove-Cluster` parancsmag használatával:

```powershell
Remove-Cluster -Cluster Cluster1
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan kezelheti a fürtöket a Windows felügyeleti központ használatával. Lásd: [fürtök kezelése Azure stack HCI-ben a Windows felügyeleti központban](cluster.md).