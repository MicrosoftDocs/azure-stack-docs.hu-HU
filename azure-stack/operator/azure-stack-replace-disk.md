---
title: Cserélje le egy fizikai lemezt az Azure Stackben |} A Microsoft Docs
description: Az Azure Stack fizikai lemez cseréje folyamatának ismertetése.
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
ms.openlocfilehash: bfe18e0aa59f81f614ae00057b2c1f287b1257da
ms.sourcegitcommit: cf9440cd2c76cc6a45b89aeead7b02a681c4628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2019
ms.locfileid: "66469292"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Az Azure Stack fizikai lemez cseréje

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Ez a cikk ismerteti az Azure Stack fizikai lemez cseréje általános folyamata. Ha a fizikai lemez meghibásodik, cserélje le a lehető legrövidebb időn belül.

Ezt az eljárást követve integrált rendszerek, valamint development kit központi telepítésekhez, amelyek gyakran használt adatok rétegére – cserélhető lemezekkel rendelkeznek.

Tényleges lemezcsere lépései eltérőek lesznek a számítógépgyártó (OEM) hardver szállítójával alapján. Konkrétan a rendszer a részletes lépéseket a gyártója által biztosított mező telepen cserélhető egység (FRU) dokumentációjában talál.

## <a name="review-disk-alert-information"></a>Lemez riasztási információk áttekintése
Ha egy lemez meghibásodik, kap egy riasztást, mely arra kéri, hogy kapcsolat megszakadt-e egy fizikai lemezt.

![Riasztás megjelenítése megszakad a fizikai lemez](media/azure-stack-replace-disk/DiskAlert.png)

Megnyitja a riasztás, ha a riasztás leírása tartalmazza a skálázási egység csomópont és a lemezre, le kell cserélnie fizikai tárolóhely pontos helyét. Az Azure Stack további segít azonosítani a meghibásodott lemezt LED kijelző képességek segítségével.

## <a name="replace-the-disk"></a>Cserélje le a lemezt

Utasítások a OEM hardver szállítójával FRU tényleges lemezt kell cserélni.

> [!note]
> Cserélje le a lemezeket egy skálázási egység csomópont egyszerre. Várjon, amíg a virtuális lemez helyreállítási feladat befejeződését, mielőtt továbblép a következő skálázási egység csomópont

Letilthatja az egy nem támogatott lemez egy integrált rendszer, a rendszer blokkolja a lemezek, a szállító által nem támogatott. Meg nem támogatott lemez használata, ha egy új riasztás kiderül, hogy, hogy az egy lemezt egy nem támogatott modell vagy a belső vezérlőprogram miatt karanténba-e.

A lemezt cserél ki, az Azure Stack automatikusan felderíti az új lemezt, és elindítja a Virtuálislemez-javítási folyamatot.

## <a name="check-the-status-of-virtual-disk-repair-using-azure-stack-powershell"></a>Azure Stack PowerShell-lel Virtuálislemez-javítási állapotának ellenőrzése

Követően a lemezt cserél ki, az Azure Stack PowerShell használatával figyelheti a virtuális lemez állapotának állapota és a javítási feladat előrehaladását.

1. Ellenőrizze, hogy van-e az Azure Stack PowerShell telepítve van. További információkért lásd: [Azure Stack PowerShell telepítése](azure-stack-powershell-install.md).
2. Csatlakozhat az Azure Stack PowerShell-kezelőként. További információkért lásd: [csatlakozhat az Azure Stack a PowerShell-lel kezelőként](azure-stack-powershell-configure-admin.md).
3. A virtuális lemez állapotának ellenőrzése és javítása állapota a következő parancsmagok futtatásához:
    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name
    Get-AzsVolume -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Select-Object VolumeLabel, OperationalStatus, RepairStatus
    ```

    ![Az Azure Stack-kötetek health](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

4. Azure Stack rendszerállapotának ellenőrzése. Útmutatásért lásd: [rendszerállapotának ellenőrzése az Azure Stack](azure-stack-diagnostic-test.md).
5. Igény szerint futtathatja meg a következő parancsot a lecserélt fizikai lemez állapotának ellenőrzéséhez.

```powershell  
$scaleunit=Get-AzsScaleUnit
$StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name

Get-AzsDrive -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Format-Table Storagenode, Healthstatus, PhysicalLocation, Model, MediaType,  CapacityGB, CanPool, CannotPoolReason
```

![Az Azure Stack fizikai lemezek helyett](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

## <a name="check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint"></a>Virtuális lemezek javítási használatával a privilegizált végpont állapotának ellenőrzése
 
Követően a lemezt cserél ki, a virtuális lemez állapot figyelése, és javítsa ki a feladat állapotát a rendszerjogosultságú végpont használatával. Kövesse az alábbi lépéseket bármely olyan számítógépről, amelyen a kiemelt végponthoz való hálózati kapcsolatot.

1. Nyisson meg egy Windows PowerShell-munkamenetet, és a kiemelt végponthoz kapcsolódjon.
    ```powershell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ``` 
  
2. Futtassa a virtuális lemez állapotának megtekintéséhez a következő parancsot:
    ```powershell
        Get-VirtualDisk -CimSession s-cluster
    ```
   ![PowerShell Get-VirtualDisk parancs kimenete](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Futtassa a storage-feladat aktuális állapotának megtekintéséhez a következő parancsot:
    ```powershell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ```
      ![PowerShell Get-StorageJob parancs kimenete](media/azure-stack-replace-disk/GetStorageJobOutput.png)

4. Az Azure Stack rendszer állapotának ellenőrzése. Útmutatásért lásd: [rendszerállapotának ellenőrzése az Azure Stack](azure-stack-diagnostic-test.md).


## <a name="troubleshoot-virtual-disk-repair-using-the-privileged-endpoint"></a>Virtuális lemezek javítási használatával a privilegizált végpont hibaelhárítása

Ha a virtuális lemezek javítása feladat jelenik meg elakadt, indítsa újra a feladatot, a következő parancsot:
  ```powershell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ``` 
