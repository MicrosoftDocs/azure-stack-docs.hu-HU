---
title: Azure Stack biztonsági mentésének engedélyezése a PowerShell-MDC
description: Megtudhatja, hogyan engedélyezheti a Infrastructure Backup szolgáltatást a PowerShell-lel, hogy a rendszer meghibásodás esetén visszaállítsa Azure Stack. Moduláris adatközpontokhoz (MDC).
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
ms.date: 10/27/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/27/2020
ms.openlocfilehash: f3d28fa9f5610f91ab8b35ad017c836745f5117d
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910874"
---
# <a name="configure-backup-for-azure-stack-with-powershell---modular-data-center-mdc"></a>A Azure Stack biztonsági mentésének konfigurálása PowerShell-moduláris adatközponttal (MDC)

*A következőkre vonatkozik: moduláris adatközpont, Azure Stack hub robusztus*

A Infrastructure Backup szolgáltatással konfigurálhatja az infrastruktúra biztonsági mentését egy külső tárolási helyre a PowerShell használatával. Az infrastruktúra biztonsági mentéseit a támogatás a csökkentett teljesítményű szolgáltatások kijavításához használhatja.

## <a name="prepare-powershell-environment"></a>PowerShell-környezet előkészítése

A PowerShell-környezet konfigurálásával kapcsolatos útmutatásért lásd: a [PowerShell telepítése Azure Stackhoz](../../operator/powershell-install-az-module.md). A Azure Stackba való bejelentkezéshez lásd: [az operátori környezet konfigurálása és a Azure Stackba való bejelentkezés](../../operator/azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>A biztonsági mentés engedélyezéséhez adja meg a biztonsági mentési megosztást, a hitelesítő adatokat és a titkosítási kulcsot

Ugyanebben a PowerShell-munkamenetben szerkessze a következő PowerShell-szkriptet a környezetéhez tartozó változók hozzáadásával. Futtassa a frissített parancsfájlt a biztonsági mentési megosztás, a hitelesítő adatok és a titkosítási kulcs megadásához a Infrastructure Backup szolgáltatáshoz.

|Változó  |Leírás  |
|---------|---------|
|$username     | Írja be a **felhasználónevet** a megosztott meghajtó helyének a tartomány és a Felhasználónév használatával, megfelelő hozzáféréssel az olvasási és írási fájlokhoz. Például: contoso \\ backupshareuser.        |
|$password     | Adja meg a felhasználó **jelszavát** .        |
|$path     | Adja meg a **biztonsági mentési tár helyének** elérési útját. Egy különálló eszközön tárolt fájlmegosztás elérési útjához univerzális elnevezési konvenció (UNC) karakterláncot kell használnia. Az UNC-karakterlánc megadja az erőforrások, például a megosztott fájlok vagy eszközök helyét. A biztonsági mentési adatmennyiség biztosításához az eszköznek külön helyen kell lennie.        |
|$backupfrequencyinhours     | Az órák gyakorisága határozza meg, hogy milyen gyakran jönnek létre a biztonsági másolatok. Az alapértelmezett érték 12. A Scheduler legalább 4 és legfeljebb 12 értéket támogat.        |
|$backupretentionperiodindays     | A megőrzési időtartam napokban határozza meg, hogy a biztonsági másolatok hány napja maradnak meg a külső helyen. Az alapértelmezett érték 7. A Scheduler legalább 2, de legfeljebb 14 értéket támogat. A megőrzési időtartamnál régebbi biztonsági mentések automatikusan törlődnek a külső helyről.        |
|$encryptioncertpath     | Az üzembe helyezés során megadott tanúsítvány. A külső tároló helyének konfigurálásakor nem kell újat megadnia. A titkosítási tanúsítvány elérési útja megadja a fájl elérési útját. Az adattitkosításhoz használt nyilvános kulccsal rendelkező CER-fájl.        |
|$isbackupschedulerenabled     | Engedélyezi vagy letiltja az automatikus biztonsági mentést. Az automatikus biztonsági mentések alapértelmezés szerint engedélyezve vannak a megosztások és a hitelesítő adatok megadása után.        |

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása

```powershell
# Example username
$username = "domain\backupadmin"

# Example share path
$sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

$password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

# Set the backup settings with the name, password, share, and CER certificate file.
Set-AzsBackupConfiguration -BackupShare path -Username $username -Password $password
```

## <a name="confirm-backup-settings"></a>Biztonsági mentési beállítások megerősítése

Ugyanebben a PowerShell-munkamenetben futtassa a következő parancsokat:

```powershell
Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
```

Az eredménynek a következő példában látható kimenethez hasonlóan kell kinéznie:

```shell
Path : \\serverIP\AzsBackupStore\contoso.com\seattle
UserName : domain\backupadmin
```

## <a name="update-backup-settings"></a>Biztonsági mentési beállítások frissítése

Ugyanebben a PowerShell-munkamenetben frissítheti a megőrzési időtartam és a biztonsági mentések gyakoriságának alapértelmezett értékeit is:

```powershell
# Set the backup frequency and retention period values.
$frequencyInHours = 10
$retentionPeriodInDays = 5

Set-AzsBackupConfiguration -BackupFrequencyInHours $backupfrequencyInHours -BackupRetentionPeriodInDays $backupretentionPeriodInDays

Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
```

## <a name="update-encryption-certificate"></a>Titkosítási tanúsítvány frissítése

Ugyanebben a PowerShell-munkamenetben frissítheti a biztonsági másolatok titkosításához használt titkosítási tanúsítványt. Csak az új biztonsági másolatok fogják használni az új titkosítási tanúsítványt. Az összes meglévő biztonsági mentés titkosítva marad az előző tanúsítvánnyal. Győződjön meg arról, hogy legalább egy hónapig megőrzi az előző tanúsítvány másolatát, hogy meggyőződjön arról, hogy a régi tanúsítvánnyal titkosított korábbi biztonsági másolatok törlődtek:

```powershell
#Set the new encryption certificate by providing local path to CER file.
Set-AzsBackupConfiguration -EncryptionCertPath "c:\temp\cert.cer"
```

## <a name="enable-or-disable-automatic-backups"></a>Automatikus biztonsági mentések engedélyezése vagy letiltása

Ugyanebben a PowerShell-munkamenetben engedélyezheti vagy letilthatja az automatikus biztonsági mentéseket. Az automatikus biztonsági mentések alapértelmezés szerint engedélyezve vannak.

```powershell
#Disable automatic backups.
Set-AzsBackupConfiguration - Isbackupschedulerenabled $false

#Enable automatic backups.
Set-AzsBackupConfiguration - Isbackupschedulerenabled $true
```

## <a name="next-steps"></a>Következő lépések

A biztonsági mentés futtatásának ellenőrzéséről a [biztonsági mentés befejezése a felügyeleti portálon](../../operator/azure-stack-backup-back-up-azure-stack.md)című cikkből tájékozódhat.
