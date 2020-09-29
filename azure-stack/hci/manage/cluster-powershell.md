---
title: Azure Stack HCI-fürtök kezelése a PowerShell-lel
description: Ismerje meg, hogyan kezelheti a fürtöket Azure Stack HCI-ben a PowerShell használatával
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 88f6ace707d14466de481f60133875968b8edce0
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947435"
---
# <a name="manage-azure-stack-hci-clusters-using-powershell"></a>Azure Stack HCI-fürtök kezelése a PowerShell-lel

> A következőre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

A Windows PowerShell segítségével kezelheti az erőforrásokat, és konfigurálhatja a Azure Stack HCI-fürtök szolgáltatásait.

A fürtöket nem a fürtben lévő gazdagépen, hanem távoli számítógépről kezelheti. Ezt a távoli számítógépet felügyeleti számítógépnek nevezzük.

> [!NOTE]
> Ha felügyeleti számítógépről futtat PowerShell-parancsokat, a vagy a `-Name` `-Cluster` paramétert adja meg a kezelt fürt nevével. Emellett meg kell adnia a teljes tartománynevet (FQDN) is, ha a paramétert használja a `-ComputerName` kiszolgálói csomóponthoz.

A fürtök PowerShell használatával történő kezelésével kapcsolatos teljes dokumentációért tekintse meg a [FailoverCluster-referenciát](/powershell/module/failoverclusters/?view=win10-ps).

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

A `Start-Cluster` és a `Stop-Cluster` parancsmag használatával adjon hozzá vagy távolítson el egy kiszolgálói csomópontot a fürthöz. További példákat és használati információkat a [Start-cluster](/powershell/module/failoverclusters/start-cluster?view=win10-ps) és a [stop-cluster](/powershell/module/failoverclusters/stop-cluster?view=win10-ps) dokumentációban talál.

A Fürtszolgáltatás elindítása azon fürt összes kiszolgáló-csomópontján, amelyen még nem indult el:

```powershell
Start-Cluster -Name Cluster1
```

Ez a példa leállítja a Fürtszolgáltatást a Cluster1 nevű fürt összes csomópontján, amely leállítja a fürtben konfigurált összes szolgáltatást és alkalmazást:

```powershell
Stop-Cluster -Name Cluster1
```

## <a name="add-or-remove-a-server"></a>Kiszolgáló hozzáadása vagy eltávolítása

A `Add-ClusterNode` és a `Remove-ClusterNode` parancsmag használatával adjon hozzá vagy távolítson el egy kiszolgálói csomópontot a fürthöz. További példák és használati információk: [Add-fürtcsomópont](/powershell/module/failoverclusters/add-clusternode?view=win10-ps) and [Remove-fürtcsomópont](/powershell/module/failoverclusters/remove-clusternode?view=win10-ps) Reference dokumentáció.

Ez a példa egy Csomópont4 nevű kiszolgálót helyez el egy Cluster1 nevű fürthöz. Győződjön meg arról, hogy a kiszolgáló fut, és először csatlakozik a fürthöz.

```powershell
Add-ClusterNode -Cluster Cluster1 -Name Node4
```

Ez a példa eltávolítja a csomópont4 nevű csomópontot a fürt Cluster1:

```powershell
Remove-ClusterNode -Cluster Cluster1 -Name Node4
```

## <a name="setup-the-cluster-witness"></a>A tanúsító fürt beállítása

A `Set-ClusterQuorum` parancsmag használatával állítsa be a kvórum tanúsító beállításait a fürthöz. További példák és használati információk: [set-ClusterQuorum](/powershell/module/failoverclusters/set-clusterquorum?view=win10-ps) Reference dokumentáció.

Ez a példa a kvórum konfigurációját úgy módosítja, hogy Felhőbeli tanúsító használjon a fürt Cluster1:

```powershell
Set-ClusterQuorum -Cluster Cluster1 -CloudWitness
```

Ez a példa a kvórum konfigurációját a fürt Cluster1 lévő csomópont-és fájlmegosztás többségére módosítja, a \\ tanúsító fájlmegosztás fileserver\fsw a következő helyen:.

```powershell
Set-ClusterQuorum -Cluster Cluster1 -NodeAndFileShareMajority \\fileserver\fsw
```

## <a name="enable-storage-spaces-direct"></a>A Közvetlen tárolóhelyek engedélyezése

A `Enable-ClusterStorageSpacesDirect` parancsmag használatával engedélyezze közvetlen tárolóhelyek a fürtön. További példák és használati információk: [enable-ClusterStorageSpacesDirect](/powershell/module/failoverclusters/enable-clusterstoragespacesdirect?view=win10-ps) Reference dokumentáció.

Ez a példa engedélyezi a Közvetlen tárolóhelyekt a Kiszolgáló1-ben:

```powershell
Enable-ClusterStorageSpacesDirect -CimSession Cluster1
```

## <a name="configure-a-hyper-v-host"></a>Hyper-V-gazdagép konfigurálása

A `Set-VMHost` parancsmaggal különböző Hyper-V-gazdagép-beállításokat konfigurálhat, például a VHD-t és a virtuális gépek elérési útját, az élő áttelepítést, a tárterület-áttelepítést, a hitelesítést, a Numa-átfedést További példák és használati információk: [enable-ClusterStorageSpacesDirect](/powershell/module/hyper-v/set-vmhost?view=win10-ps) Reference dokumentáció.

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

## <a name="remove-cluster-and-resources"></a>Fürt és erőforrások eltávolítása

A `Remove-ClusterResource` parancsmag használatával távolítsa el a fürt egy vagy több erőforrását. További példák és használati információk: [Remove-ClusterResource](/powershell/module/failoverclusters/remove-clusterresource?view=win10-ps) Reference dokumentáció.

> [!NOTE]
> A fürt eltávolításához átmenetileg engedélyeznie kell a hitelesítő adatok biztonsági szolgáltatójának (CredSSP) hitelesítését. További információ: [enable-WSManCredSSP](/powershell/module/microsoft.wsman.management/enable-wsmancredssp?view=powershell-7).

A következő példa eltávolítja a fürt erőforrásait név alapján a fürt Cluster1:

```powershell
Remove-ClusterResource -Cluster Cluster1 -Name "Cluster Disk 4"
```

Ez a példa teljesen eltávolítja a fürt Cluster1 a `Remove-Cluster` parancsmag használatával:

```powershell
Remove-Cluster -Cluster Cluster1
```

## <a name="next-steps"></a>Következő lépések

- A módosítások végrehajtása után a fürtöt később kell érvényesíteni. További információ: [Azure stack HCI-fürt ellenőrzése](../deploy/validate.md) .
- Ismerje meg, hogyan kezelheti a fürtöket a Windows felügyeleti központ használatával. Lásd: [fürtök kezelése Azure stack HCI-ben a Windows felügyeleti központban](cluster.md).
