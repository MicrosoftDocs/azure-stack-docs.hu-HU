---
title: Virtuális gép áthelyezése az Azure-ból Azure Stack hubhoz
description: Megtudhatja, hogyan helyezhet át egy virtuális gépet az Azure-ból Azure Stack hub-ra Azure Stack hub-ra.
author: mattbriggs
ms.topic: how-to
ms.date: 9/8/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: cefc127efcdac2d1610803ef90b54c50e7280e97
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95516936"
---
# <a name="move-a-vm-from-azure-to-azure-stack-hub"></a>Virtuális gép áthelyezése az Azure-ból Azure Stack hubhoz

Feltölthet egy virtuális merevlemezt (VHD-t) az Azure-ban létrehozott virtuális gépről (VM) az Azure Stack hub-példányba.

## <a name="prepare-and-download-your-vhd-from-azure"></a>A virtuális merevlemez előkészítése és letöltése az Azure-ból

Keresse meg a virtuális merevlemez előkészítésekor a saját igényeinek megfelelő szakaszt.

#### <a name="windows---specialized"></a>[Windows – specializált](#tab/win-spec)

- A virtuális merevlemez előkészítéséhez kövesse a [Windows rendszerű virtuális gép létrehozása speciális lemezről a PowerShell használatával](/azure/virtual-machines/windows/create-vm-specialized#prepare-the-vm) című cikkben ismertetett lépéseket.
- Virtuálisgép-bővítmények üzembe helyezéséhez ellenőrizze, hogy elérhető-e a VM Agent. msi.  
  További információ és lépések: az [Azure Virtual Machine Agent áttekintése](/azure/virtual-machines/extensions/agent-windows). A virtuális gép áthelyezése előtt győződjön meg róla, hogy a bővítmény telepítve van a virtuális gépen. Ha a virtuális gép ügynöke nem található a VHD-ben, a bővítmény telepítése sikertelen lesz. Nincs szükség az operációs rendszer profiljának beállítására a kiépítés során vagy a beállításakor `$vm.OSProfile.AllowExtensionOperations = $true` .

#### <a name="windows---generalized"></a>[Windows – általánosított](#tab/win-gen)

::: moniker range="<=azs-1910"
- Kövesse a [Windows VHD letöltése az Azure-ból](/azure/virtual-machines/windows/download-vhd) című témakör utasításait a virtuális merevlemez megfelelő általánosításához és letöltéséhez, mielőtt áthelyezi azt Azure stack hubhoz.
- Amikor kiépíti a virtuális gépet az Azure-ban, használja a PowerShellt. Készítse elő a `-ProvisionVMAgent` jelölést a jelző nélkül.
- Az Azure-beli virtuális gép általánosítása előtt távolítsa el az összes virtuálisgép-bővítményt a virtuális gép parancsmagjának használatával. A következő lépésekkel megkeresheti, hogy mely virtuálisgép-bővítményeket telepíti a rendszer `Windows (C:) > WindowsAzure > Logs > Plugins` .

Használja az az PowerShell-modult:

```powershell  
Remove-AzVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```

Használja a AzureRM PowerShell-modult:

```powershell  
Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```
::: moniker-end
::: moniker range=">=azs-2002"

Kövesse a [Windows VHD letöltése az Azure-ból](/azure/virtual-machines/windows/download-vhd) című témakör utasításait a virtuális merevlemez megfelelő általánosításához és letöltéséhez, mielőtt áthelyezi azt Azure stack hubhoz.
::: moniker-end

#### <a name="linux---specialized"></a>[Linux – specializált](#tab/lin-spec)

- A Linux rendszerű virtuális gép letöltése előtt kövesse az útmutató a [linuxos virtuális gép létrehozása egyéni lemezről az Azure CLI-vel](/azure/virtual-machines/linux/upload-vhd#prepare-the-vm) című cikk "virtuális gép előkészítése" című szakaszának útmutatását.
- Kövesse az Azure-beli [linuxos virtuális merevlemez letöltése](/azure//virtual-machines/windows/download-vhd) a virtuális merevlemez előkészítéséhez és letöltéséhez című cikkben ismertetett lépéseket.
- Egy speciális VHD esetében ügyeljen arra, hogy a használatával "csatolás" szemantikat használjon `-CreateOption Attach` . A cikkből megtudhatja, hogyan [hozhat létre virtuális gépet egy meglévő felügyelt operációsrendszer-lemezzel a PowerShell (Windows) használatával](/azure/virtual-machines/scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks).

#### <a name="linux---generalized"></a>[Linux – általánosított](#tab/lin-gen)

1. A **waagent** szolgáltatás leállítása:

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   Az Azure Stack hub-ban működő Azure Linux-ügynök verzióit [itt dokumentáljuk](../operator/azure-stack-linux.md#azure-linux-agent). Győződjön meg arról, hogy a Sysprep futtatásához használt rendszerkép rendelkezik az Azure Linux-ügynök olyan verziójával, amely kompatibilis az Azure Stack hubhoz.

2. Állítsa le a virtuális gép felszabadítását.

3. Töltse le a VHD-t.

   1. A VHD-fájl letöltéséhez készítsen egy közös hozzáférésű aláírás (SAS) URL-címet. Az URL-cím létrehozásakor a rendszer lejárati időt rendel az URL-címhez.

   1. A virtuális gép paneljének menüjében válassza a **lemezek** lehetőséget.

   1. Válassza ki a virtuális gép operációsrendszer-lemezét, majd válassza a **lemez exportálása** lehetőséget.

   1. Állítsa be az URL-cím lejárati idejét 36000-ra.

   1. Válassza az **URL-cím előállítása** lehetőséget.

   1. Az URL-cím előállítása.

   1. A létrehozott URL-cím alatt válassza **a VHD-fájl letöltése** lehetőséget.

   1. Előfordulhat, hogy a letöltés indításához a böngésző **Mentés** elemét kell választania. A VHD-fájl alapértelmezett neve **ABCD**.

   1. Most már áthelyezheti ezt a VHD-t Azure Stack hubhoz.

> [!IMPORTANT]  
> Az [Azure-beli virtuális merevlemezek feltöltéséhez és egy új virtuális gép](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script) létrehozásához a virtuális merevlemezt egy Azure stack hub felhasználói Storage-fiókba, és hozzon létre egy virtuális gépet, amely a cikk mintájának parancsfájljában található. Győződjön meg arról `$urlOfUploadedImageVhd` , hogy az Azure stack hub Storage-fiók + tároló URL-címét adja meg. Általánosított VHD esetén ügyeljen arra, hogy a `FromImage` beállításnál használja az értéket `-CreateOption FromImage` .

---

## <a name="verify-your-vhd"></a>A VHD ellenőrzése

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]

## <a name="upload-to-a-storage-account"></a>Feltöltés egy Storage-fiókba

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-vm"></a>A virtuális gép létrehozása

Az egyéni lemezképek két formában jelennek meg: **specializált** és **általánosított**.

### <a name="specialized"></a>[Specializált](#tab/create-vm-spec)

[!INCLUDE [Create the disk in Azure Stack Hub](../includes/user-compute-create-disk.md)]

### <a name="generalized"></a>[Általánosított](#tab/create-vm-gen)

[!INCLUDE [Create the image in Azure Stack Hub](../includes/user-compute-create-image.md)]
---
## <a name="next-steps"></a>Következő lépések

[Virtuális gép áthelyezése Azure Stack hub-ra – áttekintés](vm-move-overview.md)
