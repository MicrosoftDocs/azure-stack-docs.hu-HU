---
title: Fizikai lemez cseréje
titleSuffix: Azure Stack
description: Megtudhatja, hogyan cserélhet le egy fizikai lemezt a Azure Stackban.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 12/02/2019
ms.openlocfilehash: 0700ca4caefbec2245f2303720a675aece6c21e6
ms.sourcegitcommit: fd7d43738f275f36dacfa0786697e7c44d405abb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74694823"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Fizikai lemez cseréje Azure Stack

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ez a cikk a fizikai lemezek Azure Stackban való cseréjének általános folyamatát ismerteti. Ha egy fizikai lemez meghibásodik, minél hamarabb cserélje ki.

> [!Note]  
> A fizikai adatmeghajtó cseréje nem igényli, hogy a méretezési egység csomópontja előre **ne** kerüljön karbantartási módba (Drain). Emellett a fizikai meghajtó cseréje után a méretezési egység csomópontot nem kell kijavítani a Azure Stack hub felügyeleti portál használatával. A következő cikk további információt tartalmaz, ha javításra van szükség egy [Azure stack skálázási egység csomópontján található hardver-összetevő cseréjéhez](azure-stack-replace-component.md).

Ezt az eljárást használhatja integrált rendszerekhez, valamint olyan Azure Stack Development Kit (ASDK) üzemelő példányokhoz, amelyek gyors lecserélhető lemezekkel rendelkeznek.

A lemez cseréjének tényleges lépései a számítógépgyártó (OEM) hardverforgalmazójától függően eltérőek lehetnek. Az Ön rendszerére vonatkozó részletes lépésekért tekintse meg a forgalmazó helyszínen cserélhető egységekkel (FRU-k) kapcsolatos dokumentációját.

## <a name="review-disk-alert-information"></a>Lemez riasztási adatainak ellenőrzése
Ha egy lemez meghibásodik, egy riasztást kap, miszerint megszakadt a kapcsolat egy fizikai lemezzel.

![Riasztás a Azure Stack felügyeleti eszköz fizikai lemezének elvesztése miatti kapcsolatról](media/azure-stack-replace-disk/DiskAlert.png)

Ha megnyitja a riasztást, a riasztás leírása tartalmazza a méretezési egység csomópontot és a lecserélni kívánt lemez pontos fizikai tárolóhelyének helyét. Azure Stack további segítséget nyújt a hibás lemez azonosításához a LED kijelző képességeinek használatával.

## <a name="replace-the-physical-disk"></a>A fizikai lemez cseréje

Tekintse meg a számítógépgyártó hardverforgalmazójának a lemez cseréjére vonatkozó, helyszínen cserélhető egységekkel kapcsolatos útmutatóját.

> [!note]
> Lecseréli a lemezeket egyszerre egy méretezési egység csomópontjaira. Várjon, amíg a virtuális lemez javítási feladatai befejeződik, mielőtt továbblép a következő skálázási egység csomópontra.

A nem támogatott lemezek integrált rendszeren való használatának megakadályozása érdekében a rendszer blokkolja a gyártó által nem támogatott lemezeket. Ha nem támogatott lemezt próbál használni, egy új riasztás jelzi, hogy egy lemez nem támogatott modell vagy belső vezérlőprogram miatt lett karanténba helyezve.

A lemez cseréje után Azure Stack automatikusan felfedi az új lemezt, és elindítja a virtuális lemez javítási folyamatát.

## <a name="check-the-status-of-virtual-disk-repair-using-azure-stack-powershell"></a>A virtuális lemezek javításának állapota a Azure Stack PowerShell használatával

A lemez cseréje után nyomon követheti a virtuális lemez állapotának állapotát, és javíthatja a feladatok előrehaladását Azure Stack PowerShell használatával.

1. Győződjön meg arról, hogy telepítve van Azure Stack PowerShell. További információ: [a PowerShell telepítése Azure Stackhoz](azure-stack-powershell-install.md).
2. Kapcsolódjon Azure Stack a PowerShell-lel operátorként. További információ: [kapcsolódás Azure Stackhoz a PowerShell-lel kezelőként](azure-stack-powershell-configure-admin.md).
3. Futtassa a következő parancsmagokat a virtuális lemez állapotának és javítási állapotának ellenőrzéséhez:

    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name
    Get-AzsVolume -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Select-Object VolumeLabel, OperationalStatus, RepairStatus
    ```

    ![Azure Stack kötetek állapota a PowerShellben](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

4. Azure Stack rendszerállapotának ellenőrzése. Útmutatásért lásd: [Azure stack rendszer állapotának ellenőrzése](azure-stack-diagnostic-test.md).
5. Igény szerint a következő parancs futtatásával ellenőrizheti a lecserélt fizikai lemez állapotát.

```powershell  
$scaleunit=Get-AzsScaleUnit
$StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name

Get-AzsDrive -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Sort-Object StorageNode,MediaType,PhysicalLocation | Format-Table Storagenode, Healthstatus, PhysicalLocation, Model, MediaType,  CapacityGB, CanPool, CannotPoolReason
```

![Azure Stack fizikai lemezek cseréje a PowerShell-lel](media/azure-stack-replace-disk/check-replaced-physical-disks-azure-stack.png)

## <a name="check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint"></a>A virtuális lemez javításának állapota a privilegizált végpont használatával ellenőrizhető

A lemez cseréje után nyomon követheti a virtuális lemez állapotának állapotát, és javíthatja a feladatok előrehaladását a Kiemelt végpont használatával. Kövesse az alábbi lépéseket bármely olyan számítógépről, amely hálózati kapcsolattal rendelkezik a Kiemelt végponthoz.

1. Nyisson meg egy Windows PowerShell-munkamenetet, és kapcsolódjon a privilegizált végponthoz.
    ```powershell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```
  
2. Futtassa a következő parancsot a virtuális lemez állapotának megtekintéséhez:
    ```powershell
        Get-VirtualDisk -CimSession s-cluster
    ```

   ![A Get-VirtualDisk parancs PowerShell-kimenete](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Futtassa a következő parancsot a tárolási feladatok aktuális állapotának megtekintéséhez:
    ```powershell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ```
      ![A Get-StorageJob parancs PowerShell-kimenete](media/azure-stack-replace-disk/GetStorageJobOutput.png)

4. Ellenőrizze a Azure Stack rendszerállapotot. Útmutatásért lásd: [Azure stack rendszer állapotának ellenőrzése](azure-stack-diagnostic-test.md).

## <a name="troubleshoot-virtual-disk-repair-using-the-privileged-endpoint"></a>Virtuális lemezek javításának hibáinak megoldása a privilegizált végpont használatával

Ha a virtuális lemez javítási feladata megakad, futtassa a következő parancsot a feladatok újraindításához:
  ```powershell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```
