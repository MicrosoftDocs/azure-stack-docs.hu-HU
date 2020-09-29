---
title: Azure Stack hub biztonsági mentésének engedélyezése a PowerShell-lel
description: Megtudhatja, hogyan engedélyezheti a Infrastructure Backup szolgáltatást a PowerShell-lel, hogy az Azure Stack hub visszaállítható, ha hiba történt.
author: justinha
ms.topic: article
ms.date: 04/25/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 01f02fcf624db0a8ce47084ec5aa331e0831014b
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86489283"
---
# <a name="enable-backup-for-azure-stack-hub-with-powershell"></a>Azure Stack hub biztonsági mentésének engedélyezése a PowerShell-lel

Engedélyezze a Infrastructure Backup szolgáltatást a Windows PowerShell használatával a következő időszakos biztonsági mentések elvégzéséhez:
 - Belső identitás szolgáltatás és főtanúsítvány.
 - Felhasználói csomagok, ajánlatok, előfizetések.
 - Számítási, tárolási és hálózati felhasználói kvóták.
 - A felhasználó Key Vault titkot.
 - A felhasználók RBAC szerepkörei és házirendjei.
 - Felhasználói Storage-fiókok.

A PowerShell-parancsmagok segítségével engedélyezheti a biztonsági mentést, megkezdheti a biztonsági mentést, és lekérheti a biztonsági mentési információkat az operátor felügyeleti végpontján keresztül.

## <a name="prepare-powershell-environment"></a>PowerShell-környezet előkészítése

A PowerShell-környezet konfigurálásával kapcsolatos útmutatásért lásd: a [PowerShell telepítése Azure stack hubhoz](azure-stack-powershell-install.md). Azure Stack központba való bejelentkezéshez lásd: [az operátori környezet konfigurálása és bejelentkezés Azure stack hubhoz](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>A biztonsági mentés engedélyezéséhez adja meg a biztonsági mentési megosztást, a hitelesítő adatokat és a titkosítási kulcsot

Ugyanebben a PowerShell-munkamenetben szerkessze a következő PowerShell-szkriptet a környezetéhez tartozó változók hozzáadásával. Futtassa a frissített parancsfájlt a biztonsági mentési megosztás, a hitelesítő adatok és a titkosítási kulcs megadásához a Infrastructure Backup szolgáltatáshoz.

| Változó        | Leírás   |
|---              |---                                        |
| `$username`       | Írja be a **felhasználónevet** a megosztott meghajtó helyének a tartomány és a Felhasználónév használatával, megfelelő hozzáféréssel az olvasási és írási fájlokhoz. Például: `Contoso\backupshareuser`. |
| `$password`       | Adja meg a felhasználó **jelszavát** . |
| `$sharepath`      | Adja meg a **biztonsági mentési tár helyének**elérési útját. Egy különálló eszközön tárolt fájlmegosztás elérési útjához univerzális elnevezési konvenció (UNC) karakterláncot kell használnia. Az UNC-karakterlánc megadja az erőforrások, például a megosztott fájlok vagy eszközök helyét. A biztonsági mentési adatmennyiség biztosításához az eszköznek külön helyen kell lennie. |
| `$frequencyInHours` | Az órák gyakorisága határozza meg, hogy milyen gyakran jönnek létre a biztonsági másolatok. Az alapértelmezett érték 12. A Scheduler legfeljebb 12 és legalább 4 értéket támogat.|
| `$retentionPeriodInDays` | A megőrzési időtartam napokban határozza meg, hogy a biztonsági másolatok hány napja maradnak meg a külső helyen. Az alapértelmezett érték 7. A Scheduler legfeljebb 14 és minimum 2 értéket támogat. A megőrzési időtartamnál régebbi biztonsági mentések automatikusan törlődnek a külső helyről.|
| `$encryptioncertpath` | A 1901-es és újabb verzióira vonatkozik. A paraméter a Azure Stack hub modul 1,7-es és újabb verzióiban érhető el. A titkosítási tanúsítvány elérési útja megadja a fájl elérési útját. Az adattitkosításhoz használt nyilvános kulccsal rendelkező CER-fájl. |
| `$encryptionkey` | A Build 1811-es vagy korábbi verziójára vonatkozik. A paraméter Azure Stack hub-modul 1,6-es vagy korábbi verziójában érhető el. A titkosítási kulcs az adattitkosításhoz használatos. Új kulcs létrehozásához használja a [New-AzsEncryptionKeyBase64](/powershell/module/azs.backup.admin/new-azsencryptionkeybase64) parancsmagot. |
|     |     |

### <a name="enable-backup-on-1901-and-later-using-certificate"></a>Biztonsági mentés engedélyezése a 1901-es és újabb verziókban a tanúsítvány használatával
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certificate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
### <a name="enable-backup-on-1811-or-earlier-using-certificate"></a>Biztonsági mentés engedélyezése a 1811-es vagy korábbi verzióban tanúsítvány használatával
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $key = New-AzsEncryptionKeyBase64
    $Securekey = ConvertTo-SecureString -String ($key) -AsPlainText -Force

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $Securekey
```

   
##  <a name="confirm-backup-settings"></a>Biztonsági mentési beállítások megerősítése

Ugyanebben a PowerShell-munkamenetben futtassa a következő parancsokat:

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
   ```

Az eredménynek a következő példában látható kimenethez hasonlóan kell kinéznie:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Biztonsági mentési beállítások frissítése
Ugyanebben a PowerShell-munkamenetben frissítheti a megőrzési időszak alapértelmezett értékeit és a biztonsági mentések gyakoriságát. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

Az eredménynek a következő példában látható kimenethez hasonlóan kell kinéznie:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

### <a name="azure-stack-hub-powershell"></a>Azure Stack hub PowerShell 
Az infrastruktúra biztonsági mentésének konfigurálására szolgáló PowerShell-parancsmag a set-AzsBackupConfiguration. A korábbi kiadásokban a parancsmag beállítása – AzsBackupShare. Ehhez a parancsmaghoz tanúsítványt kell biztosítani. Ha az infrastruktúra biztonsági mentése titkosítási kulccsal van konfigurálva, nem frissítheti a titkosítási kulcsot, vagy megtekintheti a tulajdonságot. A felügyeleti PowerShell 1,6-es verzióját kell használnia.

Ha az infrastruktúra biztonsági mentése a 1901-es frissítés előtt lett konfigurálva, a titkosítási kulcs beállításához és megtekintéséhez használja a felügyeleti PowerShell 1,6-os verzióját. Az 1,6-es verzió nem teszi lehetővé, hogy a titkosítási kulcsról tanúsítványfájl-fájlra frissítsen.
A modul helyes verziójának telepítésével kapcsolatos további információkért tekintse meg az [Azure stack hub PowerShell telepítése](azure-stack-powershell-install.md) című témakört.


## <a name="next-steps"></a>Következő lépések

További információ a biztonsági másolatok futtatásáról: [Azure stack hub biztonsági mentése](azure-stack-backup-back-up-azure-stack.md).

További információ a biztonsági mentés futtatásáról: a [biztonsági mentés megerősítése befejeződött a felügyeleti portálon](azure-stack-backup-back-up-azure-stack.md).
