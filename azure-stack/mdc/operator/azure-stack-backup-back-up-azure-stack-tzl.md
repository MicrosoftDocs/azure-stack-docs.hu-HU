---
title: Azure Stack biztonsági mentése – MDC | Microsoft Docs
description: Megtudhatja, hogyan teheti igény szerinti biztonsági mentést Azure Stack egy moduláris adatközponthoz (MDC).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: f64b656b6a0a0d5310b6d6e2fbb8ff26d5206ad1
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910891"
---
# <a name="back-up-azure-stack---modular-data-center-mdc"></a>Biztonsági mentés Azure Stack – moduláris adatközpont (MDC)

*A következőkre vonatkozik: moduláris adatközpont, Azure Stack hub robusztus*

Ez a cikk bemutatja, hogyan végezheti el a Azure Stack igény szerinti biztonsági mentését.

## <a name="start-backup"></a>Biztonsági mentés indítása

A biztonsági mentések ütemezése automatikusan megtörténik. Igény szerinti biztonsági mentés indítása csak akkor szükséges, ha riasztást küld manuálisan a biztonsági mentés elindításához. Az igény szerinti biztonsági mentés elindításához válassza a **biztonsági mentés most** lehetőséget. Az igény szerinti biztonsági mentés nem módosítja a következő ütemezett biztonsági mentés idejét. A feladat befejezése után az **alapvető** beállítások panelen ellenőrizheti a beállításokat:

![Biztonsági mentési beállítások](media/azure-stack-backup-back-up-azure-stack-tzl/on-demand-backup.png)

## <a name="start-azure-stack-backup"></a>Azure Stack biztonsági mentés indítása

A **Start-AzsBackup PowerShell-** parancsmagot a Azure stack felügyeleti számítógépén is futtathatja.

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Új biztonsági mentés indítása a feladatok előrehaladásának nyomon követése nélkül

A **Start-AzSBackup** használatával azonnal elindíthat egy új biztonsági mentést, és nem végezheti el a feladatok nyomon követését.

```powershell
Start-AzsBackup -Force
```

## <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Azure Stack biztonsági mentés indítása a feladatok előrehaladásának nyomon követésével

A **Start-AzSBackup** használatával indítson el egy új biztonsági mentést a **-AsJob** paraméterrel, és mentse változóként a biztonsági mentési feladatok előrehaladásának nyomon követéséhez.

> [!NOTE]
> A biztonsági mentési feladatot a portálon a művelet befejeződése előtt 10-15 percen belül sikeresen befejezve jelenik meg.
>
> A tényleges állapot a következő kód használatával jobban megfigyelhető.

A kezdeti 1 ezredmásodperces késleltetés be van vezetve, mert a kód túl gyors a feladatok megfelelő regisztrálásához. A kód nem jelent **PSBeginTime** , és nem a feladatok **állapotát** .

```powershell
$BackupJob = Start-AzsBackup -Force -AsJob
While (!$BackupJob.PSBeginTime) {
    Start-Sleep -Milliseconds 1
}
Write-Host "Start time: $($BackupJob.PSBeginTime)"
While ($BackupJob.State -eq "Running") {
    Write-Host "Job is currently: $($BackupJob.State) - Duration: $((New-TimeSpan -Start ($BackupJob.PSBeginTime) -End (Get-Date)).ToString().Split(".")[0])"
    Start-Sleep -Seconds 30
}

If ($BackupJob.State -eq "Completed") {
    Get-AzsBackup | Where-Object {$_.BackupId -eq $BackupJob.Output.BackupId}
    $Duration = $BackupJob.Output.TimeTakenToCreate
    $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Mohs>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
    If ($Duration -match $Pattern) {
        If (!$Matches.ContainsKey("Hours")) {
            $Hours = ""
        } 
        Else {
            $Hours = ($Matches.Hours).ToString + 'h '
        }
        $Minutes = ($Matches.Minutes)
        $Seconds = [math]::round(($Matches.Seconds))
        $Runtime = '{0}{1:00}m {2:00}s' -f $Hours, $Minutes, $Seconds
    }
    Write-Host "BackupJob: $($BackupJob.Output.BackupId) - Completed with Status: $($BackupJob.Output.Status) - It took: $($Runtime) to run" -ForegroundColor Green
}
ElseIf ($BackupJob.State -ne "Completed") {
    $BackupJob
    $BackupJob.Output
}
```

## <a name="confirm-backup-has-completed"></a>A biztonsági mentés jóváhagyása befejeződött

### <a name="confirm-backup-has-completed-using-powershell"></a>A biztonsági mentés megerősítésének befejezése a PowerShell használatával

Az alábbi PowerShell-parancsokkal ellenőrizheti, hogy a biztonsági mentés sikeresen befejeződött-e:

```powershell
Get-AzsBackup
```

Az eredménynek a következő kimenethez hasonlóan kell kinéznie:

```shell
BackupDataVersion : 1.0.1
BackupId          : <backup ID>
RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
Status            : Succeeded
CreatedDateTime   : 7/6/2018 6:46:24 AM
TimeTakenToCreate : PT20M32.364138S
DeploymentID      : <deployment ID>
StampVersion      : 1.1807.0.41
OemVersion        : 
Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
Name              : local/<local name>
Type              : Microsoft.Backup.Admin/backupLocations/backups
Location          : local
Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administrator-portal"></a>A biztonsági mentés megerősítése befejeződött a felügyeleti portálon

A Azure Stack felügyeleti portál segítségével ellenőrizze, hogy a biztonsági mentés sikeresen befejeződött-e a következő lépésekkel:

1. Nyissa meg a [Azure stack felügyeleti portált](../../operator/azure-stack-manage-portals.md).

2. Válassza a **minden szolgáltatás** lehetőséget, majd az **Adminisztráció** kategóriában válassza az **infrastruktúra biztonsági mentése** lehetőséget. Válassza a **konfiguráció** lehetőséget az **infrastruktúra biztonsági mentése** panelen.

3. Keresse meg a biztonsági mentés **nevét** és **dátumát** a **rendelkezésre álló biztonsági másolatok** listájában.

4. Ellenőrizze, hogy az **állapot** **sikeres**-e.

## <a name="next-steps"></a>Következő lépések

További információ az [infrastruktúra biztonsági mentésével kapcsolatos ajánlott eljárásokról](azure-stack-backup-best-practices-tzl.md).
