---
title: Biztonsági mentés engedélyezése a PowerShell-lel az Azure Stack |} A Microsoft Docs
description: Engedélyezze az infrastruktúra Backup szolgáltatás a Windows PowerShell-lel, úgy, hogy az Azure Stack állíthatók, ha hiba történik.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 08950940510d874b407448bab37c6a43f6965c7f
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268948"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>A PowerShell-lel az Azure Stack biztonsági mentés engedélyezése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Engedélyezze az infrastruktúra Backup szolgáltatás a Windows PowerShell-lel így rendszeres biztonsági mentést hajtsa végre:
 - Belső identity service és a legfelső szintű tanúsítvány
 - Felhasználói csomagok, ajánlatok, előfizetések
 - Számítási, tárolási és hálózati felhasználó kvóták
 - Felhasználói Key vault titkos kódok
 - RBAC-szerepkörök felhasználói és szabályzatok
 - Storage-fiókok felhasználó

A PowerShell-parancsmagok, biztonsági mentés engedélyezése, indítsa el a biztonsági mentési és biztonsági mentési információ az operátor felügyeleti végponton keresztül érheti el.

## <a name="prepare-powershell-environment"></a>PowerShell-környezet előkészítése

A PowerShell-környezet konfigurálása, lásd: [Azure Stack PowerShell telepítése](azure-stack-powershell-install.md). Jelentkezzen be az Azure Stack, lásd: [konfigurálja az operátor környezetet, és jelentkezzen be az Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Adja meg a biztonsági mentési megosztást, a hitelesítő adatok és a titkosítási kulcs biztonsági mentésének engedélyezése

Ugyanazon PowerShell-munkamenetben szerkessze a következő PowerShell-parancsfájlt a környezetnek a változók hozzáadásával. A frissített parancsprogrammal adja meg a biztonsági mentési megosztást, a hitelesítő adatok és a titkosítási kulcsot a biztonsági mentési infrastruktúra-szolgáltatáshoz.

| Változó        | Leírás   |
|---              |---                                        |
| $username       | Írja be a **felhasználónév** fájlok olvasása és írása a megfelelő szintű hozzáféréssel rendelkező megosztott meghajtóhelyét tartomány és felhasználónév használata. Például: `Contoso\backupshareuser`. |
| $password       | Írja be a **jelszó** a felhasználó számára. |
| $sharepath      | Írja be a elérési útját a **biztonsági mentési tárhelyet**. Egy univerzális elnevezési konvenció (UNC) karakterlánc egy különálló eszköz található fájlmegosztás elérési útját kell használnia. Karakterláncnak UNC helyét adja meg az erőforrások, például megosztott fájlokhoz vagy eszközökön. A biztonsági mentési adatok rendelkezésre állásának biztosításához, hogy az eszköz egy külön helyen kell lennie. |
| $frequencyInHours | A gyakorisága (óra) határozza meg, hogy milyen gyakran jönnek létre biztonsági mentéseket. Az alapértelmezett érték: 12. A Scheduler támogatja a legfeljebb 12 és a egy legalább 4.|
| $retentionPeriodInDays | A megőrzési időszak napban határozza meg, hány napig a biztonsági mentések megmaradnak a külső helyen. Az alapértelmezett értéke a 7. A Scheduler támogatja a legfeljebb 14 és a egy legalább 2. A megőrzési időszak régebbi biztonsági másolatok beolvasása automatikusan törli a külső helyről.|
| $encryptioncertpath | 1901 és nem vonatkozik.  A paraméter az Azure Stack-modul 1.7-es verziója és más alkalmazásokhoz érhető el. A titkosítási tanúsítvány elérési útja fájl elérési útját adja meg a. CER-fájl az adatok titkosításához használt nyilvános kulccsal. |
| $encryptionkey | Alkalmazott 1811 hozhat létre vagy régebbi. A paraméter az Azure Stack-modul 1.6 vagy régebbi verzió érhető el. Az adatok titkosításához használt titkosítási kulcs. Használja a [New-AzsEncryptionKeyBase64](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64) parancsmag segítségével hozzon létre egy új kulcsot. |
|     |     |

### <a name="enable-backup-on-1901-and-beyond-using-certificate"></a>Biztonsági mentés engedélyezése 1901 és túli tanúsítvány használatával
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
### <a name="enable-backup-on-1811-or-earlier-using-certificate"></a>1811 vagy a korábban használt tanúsítvány biztonsági mentés engedélyezése
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

   
##  <a name="confirm-backup-settings"></a>Biztonsági mentési beállításainak megerősítése

Az ugyanazon PowerShell-munkamenetben futtassa a következő parancsokat:

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
   ```

Az eredmény az alábbi példa kimenetében hasonlóan kell kinéznie:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Biztonsági mentés beállításainak frissítése
Ugyanebben a PowerShell munkamenetben frissítheti a megőrzési időtartam és a biztonsági mentések gyakoriságát az alapértelmezett értékeket. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

Az eredmény az alábbi példa kimenetében hasonlóan kell kinéznie:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

### <a name="azure-stack-powershell"></a>Azure Stack PowerShell 
A PowerShell-parancsmag használatával konfigurálja az infrastruktúra biztonsági mentést a Set-AzsBackupConfiguration. A korábbi kiadásokban a parancsmag a Set-AzsBackupShare volt. Ez a parancsmag meg kell adnia egy tanúsítványt. Ha infrastruktúra biztonsági mentését a titkosítási kulcs van konfigurálva, nem a titkosítási kulcs módosítása vagy megtekintése a tulajdonság. Szüksége lesz a felügyeleti PowerShell 1.6-os verzióját használja. 

Infrastruktúra biztonsági mentését 1901 frissítése előtt volt beállítva, ha a rendszergazda PowerShell 1.6-os verziójának segítségével állítsa be, és a titkosítási kulcs megtekintéséhez. 1.6-os verziójára kell frissítenie a titkosítási kulcs tanúsítványfájl nem teszi lehetővé.
Tekintse meg [telepítse az Azure Stack PowerShell](azure-stack-powershell-install.md) megfelelő verzióját a modul telepítéséről további információt. 


## <a name="next-steps"></a>További lépések

Arról, hogyan futtathat egy biztonsági mentési, lásd: [biztonsági mentése az Azure Stackben](azure-stack-backup-back-up-azure-stack.md)

Ismerje meg, annak ellenőrzéséhez, hogy futtatta-e a biztonsági mentés, lásd: [megerősítése a biztonsági mentés a felügyeleti portál](azure-stack-backup-back-up-azure-stack.md)
