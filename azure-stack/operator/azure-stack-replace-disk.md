---
title: Fizikai lemez cseréje Azure Stackban | Microsoft Docs
description: A Azure Stack lévő fizikai lemezek cseréjének folyamatát ismerteti.
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
ms.date: 06/04/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 5d0c3bdff2684c90f118e26ac62b8219802fa25b
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70008646"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Fizikai lemez cseréje Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ez a cikk a fizikai lemezek Azure Stackban való cseréjének általános folyamatát ismerteti. Ha egy fizikai lemez meghibásodik, minél hamarabb cserélje ki.

Ezt az eljárást használhatja az integrált rendszerekhez, valamint a gyors lecserélhető lemezekkel rendelkező fejlesztési csomagokhoz.

A lemez cseréjének tényleges lépései a számítógépgyártó (OEM) hardverforgalmazójától függően eltérőek lehetnek. Az Ön rendszerére vonatkozó részletes lépésekért tekintse meg a forgalmazó helyszínen cserélhető egységekkel (FRU-k) kapcsolatos dokumentációját.

## <a name="review-disk-alert-information"></a>Lemez riasztási adatainak ellenőrzése
Ha egy lemez meghibásodik, egy riasztást kap, miszerint megszakadt a kapcsolat egy fizikai lemezzel.

![A fizikai lemezről elveszett kapcsolódást bemutató riasztás](media/azure-stack-replace-disk/DiskAlert.png)

Ha megnyitja a riasztást, a riasztás leírása tartalmazza a méretezési egység csomópontot és a lecserélni kívánt lemez pontos fizikai tárolóhelyének helyét. Azure Stack további segítséget nyújt a hibás lemez azonosításához a LED kijelző képességeinek használatával.

## <a name="replace-the-disk"></a>A lemez cseréje

Tekintse meg a számítógépgyártó hardverforgalmazójának a lemez cseréjére vonatkozó, helyszínen cserélhető egységekkel kapcsolatos útmutatóját.

> [!note]
> Lecseréli a lemezeket egyszerre egy méretezési egység csomópontjaira. Várjon, amíg a virtuális lemez javítási feladatai befejeződik, mielőtt továbblép a következő skálázási egység csomópontjára

Annak megakadályozásához, hogy egy nem támogatott lemez ne legyen egy integrált rendszeren, a rendszer blokkolja a gyártó által nem támogatott lemezeket. Ha nem támogatott lemezt próbál használni, egy új riasztás jelzi, hogy egy lemez nem támogatott modell vagy belső vezérlőprogram miatt lett karanténba helyezve.

A lemez cseréje után Azure Stack automatikusan felfedi az új lemezt, és elindítja a virtuális lemez javítási folyamatát.

## <a name="check-the-status-of-virtual-disk-repair-using-azure-stack-powershell"></a>A virtuális lemezek javításának állapota a Azure Stack PowerShell használatával

A lemez cseréje után nyomon követheti a virtuális lemez állapotának állapotát, és javíthatja a feladatok előrehaladását Azure Stack PowerShell használatával.

1. Győződjön meg arról, hogy telepítve van Azure Stack PowerShell. További információ: [a PowerShell telepítése Azure Stackhoz](azure-stack-powershell-install.md).
2. Kapcsolódjon Azure Stack a PowerShell-lel operátorként. További információ: [kapcsolódás Azure Stackhoz a PowerShell-](azure-stack-powershell-configure-admin.md)lel kezelőként.
3. Futtassa a következő parancsmagokat a virtuális lemez állapotának és javítási állapotának ellenőrzéséhez:
    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name
    Get-AzsVolume -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Select-Object VolumeLabel, OperationalStatus, RepairStatus
    ```

    ![Azure Stack kötetek állapota](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

4. Azure Stack rendszerállapotának ellenőrzése. Útmutatásért lásd: [Azure stack rendszer állapotának ellenőrzése](azure-stack-diagnostic-test.md).
5. Igény szerint a következő parancs futtatásával ellenőrizheti a lecserélt fizikai lemez állapotát.

```powershell  
$scaleunit=Get-AzsScaleUnit
$StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name

Get-AzsDrive -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Sort-Object StorageNode,MediaType,PhysicalLocation | Format-Table Storagenode, Healthstatus, PhysicalLocation, Model, MediaType,  CapacityGB, CanPool, CannotPoolReason
```

![Fizikai lemezek cseréje Azure Stack](media/azure-stack-replace-disk/check-replaced-physical-disks-azure-stack.png)

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
