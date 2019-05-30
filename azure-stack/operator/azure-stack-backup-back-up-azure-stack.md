---
title: Azure Stack biztonsági mentése |} A Microsoft Docs
description: Hajtsa végre az Azure Stacken egy igény szerinti biztonsági mentést a biztonsági mentési helyen.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: a572275ff81918d1b8f739a99fbe7a57784d0651
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269017"
---
# <a name="back-up-azure-stack"></a>Készítsen biztonsági másolatot az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Hajtsa végre egy igény szerinti biztonsági mentést az Azure Stacken. A PowerShell-környezet konfigurálása, lásd: [Azure Stack PowerShell telepítése](azure-stack-powershell-install.md). Jelentkezzen be az Azure Stack, lásd: [a felügyeleti portál használatával az Azure Stackben](azure-stack-manage-portals.md).

## <a name="start-azure-stack-backup"></a>Azure Stack biztonsági mentés indítása

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Egy új biztonsági mentés indítása nélkül a feladat előrehaladásának nyomon követése
Használja a Start-AzSBackup azonnal elindul egy új biztonsági másolatot a nem a feladat előrehaladásának nyomon követése.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>A feladat előrehaladásának nyomon követése az Azure Stack biztonsági mentés indítása
Az új biztonsági mentést elindítani a Start-AzSBackup használatával a **- AsJob** paramétert, és mentse egy változót, nyomon követheti a biztonsági mentési feladat.

> [!NOTE]
> Biztonsági mentési feladat sikeres fog megjelenni a portálon végezhető körülbelül 10 – 15 percet, mielőtt befejezi a feladatot.
>
> A tényleges állapota, ezért jobban meg az alábbi kód használatával.

> [!IMPORTANT]
> A kezdeti 1 ezredmásodperces késleltetési vezetett be, mert a kód túl rövid a feladat megfelelően regisztrálja, és vissza nem tartalmaz **PSBeginTime** és viszont nem **állapot** a feladat.

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
        $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Months>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
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

## <a name="confirm-backup-has-completed"></a>Erősítse meg a biztonsági mentés befejeződött

### <a name="confirm-backup-has-completed-using-powershell"></a>Erősítse meg a biztonsági mentés befejeződött, PowerShell-lel
A következő PowerShell-parancsok segítségével győződjön meg arról, hogy a biztonsági mentés sikeresen befejeződött:

```powershell
   Get-AzsBackup
```

Az eredmény a következő kimenet hasonlóan kell kinéznie:

```powershell
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

### <a name="confirm-backup-has-completed-in-the-administration-portal"></a>Erősítse meg a biztonsági mentés befejeződött, a felügyeleti portálon
Az Azure Stack felügyeleti portál használatával győződjön meg arról, hogy a biztonsági mentés sikeresen befejeződött a következő lépésekkel:

1. Nyissa meg a [Azure Stack felügyeleti portálon](azure-stack-manage-portals.md).
2. Válassza ki **minden szolgáltatás**, majd a **felügyeleti** kategória kiválasztása > **infrastruktúra biztonsági mentését**. Válasszon **konfigurációs** a a **infrastruktúra biztonsági mentését** panelen.
3. Keresse meg a **neve** és **dátuma** a biztonsági mentési **elérhető biztonsági másolatok** listája.
4. Ellenőrizze a **állapot** van **sikeres**.

## <a name="next-steps"></a>További lépések

További információ a munkafolyamat [adatvesztési esemény utáni](azure-stack-backup-recover-data.md).
