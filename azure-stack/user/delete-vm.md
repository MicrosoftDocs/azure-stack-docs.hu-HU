---
title: Virtuális gép törlése Azure Stack hub függőségeivel
description: Virtuális gép (VM) törlése Azure Stack hub függőségeivel
author: mattbriggs
ms.topic: how-to
ms.date: 11/22/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: f9e32351dbc73b42e51c485c8e2eb39d4226ea27
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518211"
---
# <a name="how-to-delete-a-vm-virtual-machine-with-dependencies-on-azure-stack-hub"></a>Virtuális gép (VM) törlése Azure Stack hub függőségeivel

Ebből a cikkből megtudhatja, hogyan távolíthat el egy virtuális gépet és annak függőségeit Azure Stack központban.

Ha eltávolít egy virtuális gépet Azure Stack hubhoz, akkor az összetevő függőségei, azaz adatlemezek, virtuális hálózati adapterek és diagnosztikai tárolók maradnak az erőforráscsoporthoz. Ezeket az elemeket az operációsrendszer-lemezzel együtt nem törli a rendszer.

## <a name="delete-a-vm-overview"></a>Virtuális gép törlése – áttekintés

Új virtuális gép létrehozásakor általában hozzon létre egy új erőforráscsoportot, és helyezze el az adott erőforráscsoport összes függőségét. Ha törölni szeretné a virtuális gépet és annak összes függőségét, törölheti az erőforráscsoportot. A Azure Resource Manager fogja kezelni a függőségeket a sikeres törléséhez. Előfordulhat, hogy az erőforráscsoport nem törölhető a virtuális gép eltávolításához. Előfordulhat például, hogy a virtuális gép olyan erőforrásokat tartalmaz, amelyek nem a megőrizni kívánt virtuális gép függőségei.

## <a name="delete-a-vm-with-dependencies"></a>Függőségekkel rendelkező virtuális gép törlése

### <a name="with-the-portal"></a>[A Portallal](#tab/portal)

Abban az esetben, ha nem tudja törölni az erőforráscsoportot, vagy a függőségek nem ugyanabban az erőforráscsoporthoz vannak, vagy más erőforrások is vannak, kövesse az alábbi lépéseket:

1. Nyissa meg a Azure Stack felhasználói portált.

2. Válassza a **Virtuális gépek** lehetőséget. Keresse meg a virtuális gépet, majd válassza ki a gépet a virtuális gép paneljének megnyitásához.  
![Függőségekkel rendelkező virtuális gép törlése](./media/delete-vm/azure-stack-hub-delete-vm-portal.png)  

3. Jegyezze fel a virtuális gépet és a virtuális gép függőségeit tartalmazó erőforráscsoportot.

4. Válassza a **hálózatkezelés** lehetőséget, és jegyezze fel a hálózati adaptert.

5. Válassza a **lemezek** lehetőséget, és jegyezze fel az operációsrendszer-lemezt és az adatlemezeket.

6. Térjen vissza a **virtuális gép** panelre, és válassza a **Törlés** lehetőséget.

7. `yes`A törlés megerősítéséhez és a **Törlés** kiválasztásához írja be a következőt:.

7. Válassza az **erőforráscsoportok** lehetőséget, majd válassza ki az erőforráscsoportot.

8. Törölje a függőségeket manuálisan kiválasztva, majd válassza a **Törlés** lehetőséget.
    1. `yes`A törlés megerősítéséhez és a **Törlés** kiválasztásához írja be a következőt:.
    2. Várjon, amíg az erőforrás teljesen törölve lesz.
    3. Ezután törölheti a következő függőséget.

### <a name="with-powershell"></a>[A PowerShell-lel](#tab/ps-az)

Abban az esetben, ha nem tudja törölni az erőforráscsoportot, vagy a függőségek nem ugyanabban az erőforráscsoporthoz vannak, vagy más erőforrások is vannak, kövesse az alábbi lépéseket.

Kapcsolódjon a Azure Stack hub-környezethez, majd frissítse a következő változókat a virtuális gép nevével és erőforráscsoporthoz. A PowerShell-munkamenethez Azure Stack hubhoz való csatlakozással kapcsolatos utasításokért lásd: [csatlakozás Azure stack hub-hoz a PowerShell felhasználóként](azure-stack-powershell-configure-user.md).

```powershell
$machineName = 'VM_TO_DELETE'
$resGroupName = 'RESOURCE_GROUP'
$machine = Get-AzVM -Name $machineName -ResourceGroupName $resGroupName
```

Kérje le a virtuális gép információit és a függőségek nevét. Ugyanebben a munkamenetben futtassa a következő parancsmagokat:

```powershell
 $azResParams = @{
 'ResourceName' = $machineName
 'ResourceType' = 'Microsoft.Compute/virtualMachines'
     'ResourceGroupName' = $resGroupName
 }
 $vmRes = Get-AzResource @azResParams
 $vmId = $vmRes.Properties.VmId
```

Törölje a rendszerindítási diagnosztikai tárolót. Ha a gép neve rövidebb, mint 9 karakter, akkor a változó létrehozásakor módosítania kell az indexet a karakterlánc hosszára az alkarakterláncban `$diagContainer` . 

Ugyanebben a munkamenetben futtassa a következő parancsmagokat:

```powershell
$container = [regex]::match($machine.DiagnosticsProfile.bootDiagnostics.storageUri, '^http[s]?://(.+?)\.').groups[1].value
$diagContainer = ('bootdiagnostics-{0}-{1}' -f $machine.Name.ToLower().Substring(0, 9), $vmId)
$containerRg = (Get-AzStorageAccount | where { $_.StorageAccountName -eq $container }).ResourceGroupName
$storeParams = @{
    'ResourceGroupName' = $containerRg
    'Name' = $container }
Get-AzStorageAccount @storeParams | Get-AzureStorageContainer | where { $_.Name-eq $diagContainer } | Remove-AzureStorageContainer -Force
```

Távolítsa el a virtuális hálózati adaptert.

```powershell
$machine | Remove-AzNetworkInterface -Force
```

Törölje az operációs rendszer lemezét.

```powershell
$osVhdUri = $machine.StorageProfile.OSDisk.Vhd.Uri
$osDiskConName = $osVhdUri.Split('/')[-2]
$osDiskStorageAcct = Get-AzStorageAccount | where { $_.StorageAccountName -eq $osVhdUri.Split('/')[2].Split('.')[0] }
$osDiskStorageAcct | Remove-AzureStorageBlob -Container $osDiskConName -Blob $osVhdUri.Split('/')[-1]
```

Távolítsa el a virtuális géphez csatolt adatlemezeket.

```powershell
if ($machine.DataDiskNames.Count -gt 0)
 {
    Write-Verbose -Message 'Deleting disks...'
        foreach ($uri in $machine.StorageProfile.DataDisks.Vhd.Uri )
        {
            $dataDiskStorageAcct = Get-AzStorageAccount -Name $uri.Split('/')[2].Split('.')[0]
             $dataDiskStorageAcct | Remove-AzureStorageBlob -Container $uri.Split('/')[-2] -Blob $uri.Split('/')[-1] -ea Ignore
        }
 }
```

Végezetül törölje a virtuális gépet. A parancsmag futtatása eltarthat egy ideig. A virtuális GÉPHEZ csatolt összetevők naplózásához tekintse át a virtuálisgép-objektumot a PowerShellben. Az objektum áttekintéséhez egyszerűen tekintse meg a virtuálisgép-objektumot tartalmazó változót. Gépelje be: `$machine`.

A virtuális gép törléséhez ugyanebben a munkamenetben futtassa a következő parancsmagokat:

```powershell
$machine | Remove-AzVM -Force
```
### <a name="with-powershell"></a>[A PowerShell-lel](#tab/ps-azureRM)

Abban az esetben, ha nem tudja törölni az erőforráscsoportot, vagy a függőségek nem ugyanabban az erőforráscsoporthoz vannak, vagy más erőforrások is vannak, kövesse az alábbi lépéseket.

Kapcsolódjon a Azure Stack hub-környezethez, majd frissítse a következő változókat a virtuális gép nevével és erőforráscsoporthoz. A PowerShell-munkamenethez Azure Stack hubhoz való csatlakozással kapcsolatos utasításokért lásd: [csatlakozás Azure stack hub-hoz a PowerShell felhasználóként](azure-stack-powershell-configure-user.md).

```powershell
$machineName = 'VM_TO_DELETE'
$resGroupName = 'RESOURCE_GROUP'
$machine = Get-AzureRmVM -Name $machineName -ResourceGroupName $resGroupName
```

Kérje le a virtuális gép információit és a függőségek nevét. Ugyanebben a munkamenetben futtassa a következő parancsmagokat:

```powershell
 $azResParams = @{
 'ResourceName' = $machineName
 'ResourceType' = 'Microsoft.Compute/virtualMachines'
     'ResourceGroupName' = $resGroupName
 }
 $vmRes = Get-AzureRmResource @azResParams
 $vmId = $vmRes.Properties.VmId
```

Törölje a rendszerindítási diagnosztikai tárolót. Ha a gép neve rövidebb, mint 9 karakter, akkor a változó létrehozásakor módosítania kell az indexet a karakterlánc hosszára az alkarakterláncban `$diagContainer` . 

Ugyanebben a munkamenetben futtassa a következő parancsmagokat:

```powershell
$container = [regex]::match($machine.DiagnosticsProfile.bootDiagnostics.storageUri, '^http[s]?://(.+?)\.').groups[1].value
$diagContainer = ('bootdiagnostics-{0}-{1}' -f $machine.Name.ToLower().Substring(0, 9), $vmId)
$containerRg = (Get-AzureRmStorageAccount | where { $_.StorageAccountName -eq $container }).ResourceGroupName
$storeParams = @{
    'ResourceGroupName' = $containerRg
    'Name' = $container }
Get-AzureRmStorageAccount @storeParams | Get-AzureStorageContainer | where { $_.Name-eq $diagContainer } | Remove-AzureStorageContainer -Force
```

Távolítsa el a virtuális hálózati adaptert.

```powershell
$machine | Remove-AzureRmNetworkInterface -Force
```

Törölje az operációs rendszer lemezét.

```powershell
$osVhdUri = $machine.StorageProfile.OSDisk.Vhd.Uri
$osDiskConName = $osVhdUri.Split('/')[-2]
$osDiskStorageAcct = Get-AzureRmStorageAccount | where { $_.StorageAccountName -eq $osVhdUri.Split('/')[2].Split('.')[0] }
$osDiskStorageAcct | Remove-AzureStorageBlob -Container $osDiskConName -Blob $osVhdUri.Split('/')[-1]
```

Távolítsa el a virtuális géphez csatolt adatlemezeket.

```powershell
if ($machine.DataDiskNames.Count -gt 0)
 {
    Write-Verbose -Message 'Deleting disks...'
        foreach ($uri in $machine.StorageProfile.DataDisks.Vhd.Uri )
        {
            $dataDiskStorageAcct = Get-AzureRmStorageAccount -Name $uri.Split('/')[2].Split('.')[0]
             $dataDiskStorageAcct | Remove-AzureStorageBlob -Container $uri.Split('/')[-2] -Blob $uri.Split('/')[-1] -ea Ignore
        }
 }
```

Végezetül törölje a virtuális gépet. A parancsmag futtatása eltarthat egy ideig. A virtuális GÉPHEZ csatolt összetevők naplózásához tekintse át a virtuálisgép-objektumot a PowerShellben. Az objektum áttekintéséhez egyszerűen tekintse meg a virtuálisgép-objektumot tartalmazó változót. Gépelje be: `$machine`.

A virtuális gép törléséhez ugyanebben a munkamenetben futtassa a következő parancsmagokat:

```powershell
$machine | Remove-AzureRmVM -Force
```
---
## <a name="next-steps"></a>Következő lépések

[Azure Stack hub VM-funkciók](azure-stack-vm-considerations.md)