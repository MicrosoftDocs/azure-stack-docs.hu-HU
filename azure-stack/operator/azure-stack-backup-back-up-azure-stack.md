---
title: Azure Stack hub biztonsági mentése
description: Ismerje meg, hogyan végezheti el az igény szerinti biztonsági mentést Azure Stack hub-on.
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2019
ms.openlocfilehash: 10a7c60a18faa5528fb9c1917c62994b43bee51a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77701037"
---
# <a name="back-up-azure-stack-hub"></a>Azure Stack hub biztonsági mentése

Ez a cikk bemutatja, hogyan végezheti el az igény szerinti biztonsági mentést Azure Stack hub-on. A PowerShell-környezet konfigurálásával kapcsolatos útmutatásért lásd: a [PowerShell telepítése Azure stack hubhoz](azure-stack-powershell-install.md). Azure Stack hubhoz való bejelentkezéshez lásd: [a felügyeleti portál használata Azure stack hub-ban](azure-stack-manage-portals.md).

## <a name="start-azure-stack-hub-backup"></a>Azure Stack hub biztonsági másolatának elindítása

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Új biztonsági mentés indítása a feladatok előrehaladásának nyomon követése nélkül
A Start-AzSBackup használatával azonnal elindíthat egy új biztonsági mentést, és nem végezheti el a feladatok nyomon követését.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-hub-backup-with-job-progress-tracking"></a>Azure Stack hub biztonsági mentésének elindítása a feladatok előrehaladásának nyomon követésével
A Start-AzSBackup használatával indítson el egy új biztonsági mentést a **-AsJob** paraméterrel, és mentse változóként a biztonsági mentési feladatok előrehaladásának nyomon követéséhez.

> [!NOTE]
> A biztonsági mentési feladatot a portálon a művelet befejeződése előtt 10-15 percen belül sikeresen befejezve jelenik meg.
>
> A tényleges állapot jobban megfigyelhető az alábbi kód használatával.

> [!IMPORTANT]
> A kezdeti 1 ezredmásodperces késleltetés be van vezetve, mert a kód túl gyorsan regisztrálja a feladatot, és a **PSBeginTime** nélkül jön vissza, és a feladatokhoz tartozó **állapot** nélkül működik.

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

## <a name="confirm-backup-has-completed"></a>A biztonsági mentés jóváhagyása befejeződött

### <a name="confirm-backup-has-completed-using-powershell"></a>A biztonsági mentés megerősítésének befejezése a PowerShell használatával
Az alábbi PowerShell-parancsokkal ellenőrizheti, hogy a biztonsági mentés sikeresen befejeződött-e:

```powershell
   Get-AzsBackup
```

Az eredménynek a következő kimenethez hasonlóan kell kinéznie:

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

### <a name="confirm-backup-has-completed-in-the-administrator-portal"></a>A biztonsági mentés megerősítése befejeződött a felügyeleti portálon
A következő lépésekkel ellenőrizheti, hogy a biztonsági mentés sikeresen befejeződött-e a Azure Stack hub felügyeleti portálján:

1. Nyissa meg az [Azure stack hub felügyeleti portált](azure-stack-manage-portals.md).
2. Válassza a **minden szolgáltatás**lehetőséget, majd az **adminisztráció** kategóriában válassza > **infrastruktúra biztonsági mentése**lehetőséget. Válassza a **konfiguráció** lehetőséget az **infrastruktúra biztonsági mentése** panelen.
3. Keresse meg a biztonsági mentés **nevét** és **dátumát** a **rendelkezésre álló biztonsági másolatok** listájában.
4. Ellenőrizze, hogy az **állapot** **sikeres**-e.

## <a name="next-steps"></a>További lépések

További információ az [adatvesztési eseményről történő helyreállítás](azure-stack-backup-recover-data.md)munkafolyamatáról.
