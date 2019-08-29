---
title: Azure Stack biztonsági mentés ellenőrzése a ASDK használatával | Microsoft Docs
description: Megtudhatja, hogyan használhatja a ASDK az Azure Stack integrált rendszerek biztonsági mentésének ellenőrzésére.
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 07/31/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 07/31/2019
ms.openlocfilehash: 8905a376a165776acde2fb792df1e8f35279140e
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118760"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>A ASDK használata Azure Stack biztonsági mentés ellenőrzéséhez
Azure Stack üzembe helyezése és a felhasználói erőforrások kiépítése (például ajánlatok, csomagok, kvóták és előfizetések) után [engedélyeznie kell Azure stack-infrastruktúra biztonsági mentését](../operator/azure-stack-backup-enable-backup-console.md). Az infrastruktúra rendszeres biztonsági mentésének ütemezése és futtatása biztosítja, hogy az infrastruktúra-kezelési adatokat ne veszítse el a rendszer, ha katasztrofális hardveres vagy szolgáltatási hiba történik.

> [!TIP]
> Javasoljuk, hogy az eljárás megkezdése előtt [futtasson egy igény szerinti biztonsági mentést](../operator/azure-stack-backup-back-up-azure-stack.md) , és győződjön meg arról, hogy rendelkezik a legújabb infrastruktúra-adatmennyiség másolatával. Ügyeljen arra, hogy a biztonsági mentés sikeres befejeződése után rögzítse a biztonsági mentési azonosítót. Ez az azonosító a Felhőbeli helyreállítás során szükséges.

Azure Stack az infrastruktúra biztonsági mentései olyan fontos adatokat tartalmaznak a felhőről, amelyek visszaállíthatók a Azure Stack újratelepítése során. A ASDK használatával érvényesítheti ezeket a biztonsági másolatokat anélkül, hogy az hatással lenne az éles felhőre. 

A ASDK biztonsági másolatainak ellenőrzése a következő esetekben támogatott:

|Forgatókönyv|Cél|
|-----|-----|
|Az infrastruktúra biztonsági mentésének ellenőrzése egy integrált megoldásból.|Rövid élettartamú ellenőrzés, hogy a biztonsági mentésben szereplő értékek érvényesek-e.|
|Ismerje meg a teljes körű helyreállítási munkafolyamatot.|A ASDK használatával ellenőrizze a teljes biztonsági mentési és visszaállítási élményt.|
|     |     |

A következő forgatókönyv **nem** támogatott a biztonsági másolatok ASDK való ellenőrzésekor:

|Forgatókönyv|Cél|
|-----|-----|
|ASDK Build a biztonsági mentés és a visszaállítás létrehozásához.|A ASDK korábbi verziójából származó biztonsági mentési adatok visszaállítása újabb verzióra.|
|     |     |


## <a name="cloud-recovery-deployment"></a>Felhőbeli helyreállítás üzembe helyezése
Az integrált rendszerek központi telepítésének infrastruktúra-biztonsági mentései ellenőrizhetők a ASDK Felhőbeli helyreállítási telepítésének végrehajtásával. Ilyen típusú központi telepítés esetén a rendszer a ASDK telepítése után visszaállítja az adott szolgáltatási adatok biztonsági mentését a gazdagépre.

### <a name="prereqs"></a>A felhőalapú helyreállítás előfeltételei
A ASDK felhőalapú helyreállításának elindítása előtt ellenőrizze, hogy rendelkezik-e a következő információkkal:

**A felhasználói felület telepítőjének követelményei**

*A jelenlegi FELHASZNÁLÓIFELÜLET-telepítő csak a titkosítási kulcsot támogatja*

|Előfeltétel|Leírás|
|-----|-----|
|Biztonsági másolat megosztásának elérési útja|A legújabb Azure Stack biztonsági mentés UNC-fájljának elérési útja, amelyet a rendszer Azure Stack infrastruktúra-információk helyreállítására fog használni. Ezt a helyi megosztást a rendszer a felhőalapú helyreállítás üzembe helyezési folyamata során hozza létre.|
|Visszaállítandó biztonsági mentési azonosító|A biztonsági mentési azonosító a "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" alfanumerikus formában, amely a Felhőbeli helyreállítás során visszaállítani kívánt biztonsági mentést azonosítja.|
|Időkiszolgáló IP-címe|Azure Stack központi telepítéshez érvényes időkiszolgálói IP-címet (például 132.163.97.2) kell megadni.|
|Külső tanúsítvány jelszava|Az Azure Stack által használt külső tanúsítvány jelszava. A HITELESÍTÉSSZOLGÁLTATÓ biztonsági mentése külső tanúsítványokat tartalmaz, amelyeket ezzel a jelszóval kell visszaállítani.|
|Titkosítási kulcs biztonsági mentése|Kötelező, ha a biztonsági mentési beállítások a-ben egy titkosítási kulccsal vannak konfigurálva, ami elavult. A telepítő a visszamenőleges kompatibilitási módban támogatja a titkosítási kulcsot legalább 3 kiadásban. Miután frissítette a biztonsági mentési beállításokat a tanúsítvány használatára, tekintse meg a következő táblázatot a szükséges információkkal kapcsolatban.|

|     |     | 

**PowerShell-telepítőre vonatkozó követelmények**

*A jelenlegi PowerShell-telepítő támogatja a titkosítási kulcs vagy a visszafejtési tanúsítvány használatát.*

|Előfeltétel|Leírás|
|-----|-----|
|Biztonsági másolat megosztásának elérési útja|A legújabb Azure Stack biztonsági mentés UNC-fájljának elérési útja, amelyet a rendszer Azure Stack infrastruktúra-információk helyreállítására fog használni. Ezt a helyi megosztást a rendszer a felhőalapú helyreállítás üzembe helyezési folyamata során hozza létre.|
|Visszaállítandó biztonsági mentési azonosító|A biztonsági mentési azonosító a "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" alfanumerikus formában, amely a Felhőbeli helyreállítás során visszaállítani kívánt biztonsági mentést azonosítja.|
|Időkiszolgáló IP-címe|Azure Stack központi telepítéshez érvényes időkiszolgálói IP-címet (például 132.163.97.2) kell megadni.|
|Külső tanúsítvány jelszava|Az Azure Stack által használt külső tanúsítvány jelszava. A HITELESÍTÉSSZOLGÁLTATÓ biztonsági mentése külső tanúsítványokat tartalmaz, amelyeket ezzel a jelszóval kell visszaállítani.|
|Visszafejtési tanúsítvány jelszava|Nem kötelező. Csak akkor szükséges, ha a biztonsági másolat tanúsítvány használatával van titkosítva. A jelszó a biztonsági másolatok visszafejtéséhez szükséges titkos kulcsot tartalmazó önaláírt tanúsítvány (. pfx).|
|Titkosítási kulcs biztonsági mentése|Nem kötelező. Kötelező, ha a biztonsági mentési beállítások még egy titkosítási kulccsal vannak konfigurálva. A telepítő a visszamenőleges kompatibilitási módban támogatja a titkosítási kulcsot legalább 3 kiadásban. Miután frissítette a biztonsági mentési beállításokat a tanúsítvány használatára, meg kell adnia a visszafejtési tanúsítvány jelszavát.|
|     |     | 

## <a name="prepare-the-host-computer"></a>A gazda számítógép előkészítése 
A normál ASDK-telepítéshez hasonlóan a ASDK gazdagép rendszerkörnyezetét is elő kell készíteni a telepítéshez. A ASDK-gazdaszámítógép előkészítése után a rendszer a CloudBuilder. vhdx VM merevlemezről indítja el a ASDK üzembe helyezésének megkezdéséhez.

A ASDK-gazdaszámítógépen töltsön le egy új cloudbuilder. vhdx, amely a biztonsági másolatban szereplő Azure Stack azonos verziójának felel meg, és kövesse az [ASDK-gazdaszámítógép előkészítésének](asdk-prepare-host.md)utasításait.

Miután a gazda-kiszolgáló újraindult a cloudbuilder. vhdx fájlból, létre kell hoznia egy fájlmegosztást, és át kell másolnia a biztonsági mentési adatait a-ba. A fájlmegosztás elérhetőnek kell lennie a telepítőt futtató fiók számára. Rendszergazdai példák a PowerShell-parancsokra: 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

Ezután másolja a legújabb Azure Stack biztonságimásolat-fájljait az újonnan létrehozott megosztásra. A megosztáson belüli mappa struktúrájának a következőket `\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`kell tennie:.

Végül másolja a visszafejtési tanúsítványt (. pfx) a tanúsítvány könyvtárába, és nevezze `C:\CloudDeployment\Setup\Certificates\` át a `BackupDecryptionCert.pfx`fájlt a következőre:.

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>A ASDK üzembe helyezése Felhőbeli helyreállítási módban

> [!IMPORTANT]
> 1. Az aktuális telepítő felhasználói felülete csak a titkosítási kulcsot támogatja. Csak olyan rendszerekből származó biztonsági mentéseket lehet érvényesíteni, amelyek továbbra is használják a titkosítási kulcsot. Ha a biztonsági mentést egy integrált rendszeren vagy tanúsítványon alapuló ASDK titkosították, akkor a PowerShell-telepítőt (**InstallAzureStackPOC. ps1**) kell használnia. 
> 2. A PowerShell-telepítő (**InstallAzureStackPOC. ps1**) támogatja a titkosítási kulcs vagy a tanúsítvány használatát.
> 3. A ASDK telepítése pontosan egy hálózati adaptert (NIC) támogat a hálózatkezeléshez. Ha több hálózati adapterrel rendelkezik, győződjön meg arról, hogy csak egy van engedélyezve (és minden más le van tiltva) a telepítési parancsfájl futtatása előtt.

### <a name="use-the-installer-ui-to-deploy-the-asdk-in-recovery-mode"></a>A ASDK helyreállítási módban való üzembe helyezéséhez használja a telepítő felhasználói felületét.
Az ebben a szakaszban ismertetett lépések bemutatják, hogyan helyezheti üzembe a ASDK a **asdk-Installer. ps1** PowerShell-parancsfájl letöltésével és futtatásával elérhető grafikus felhasználói felület használatával.

> [!NOTE]
> A ASDK telepítő felhasználói felülete egy, a WCF és a PowerShell-alapú, nyílt forráskódú parancsfájl.

> [!IMPORTANT]
> Az aktuális telepítő felhasználói felülete csak a titkosítási kulcsot támogatja.

1. Miután a gazdaszámítógép sikeresen beindult a CloudBuilder. vhdx lemezképbe, jelentkezzen be a ASDK-gazdaszámítógép ASDK-telepítésre [való előkészítésekor](asdk-prepare-host.md) megadott rendszergazdai hitelesítő adatokkal. Ezeknek a hitelesítő adatoknak meg kell egyezniük a ASDK gazdagép helyi rendszergazdai hitelesítő adataival.
2. Nyisson meg egy rendszergazda jogú PowerShell-konzolt, és futtassa a  **&lt;meghajtóbetűjel > \AzureStack_Installer\asdk-Installer.ps1** PowerShell-parancsfájlt. Előfordulhat, hogy a parancsfájl egy másik meghajtón van, mint a C:\ az CloudBuilder. vhdx képen. Kattintson a **helyreállítás**gombra.

    ![ASDK-telepítő parancsfájl](media/asdk-validate-backup/1.PNG) 

3. Adja meg az Azure AD-címtár adatait (nem kötelező) és a helyi rendszergazdai jelszót a ASDK gazdagép számára az Identity Provider és a hitelesítő adatok lapon. Kattintson a **Tovább** gombra.

    ![ASDK-identitás és hitelesítő adatok lap](media/asdk-validate-backup/2.PNG) 

4. Válassza ki a ASDK futtató számítógép által használandó hálózati adaptert, és kattintson a **tovább**gombra. A ASDK telepítése során az összes többi hálózati adapter le lesz tiltva. 

    ![ASDK hálózati adapter felülete](media/asdk-validate-backup/3.PNG) 

5. A hálózati konfiguráció lapon adja meg az érvényes időkiszolgáló és a DNS-továbbító IP-címeit. Kattintson a **Tovább** gombra.

    ![ASDK hálózati konfiguráció lapja](media/asdk-validate-backup/4.PNG) 

6. A hálózati kártya tulajdonságainak ellenőrzése után kattintson a **tovább**gombra. 

    ![ASDK hálózati kártya beállításainak ellenőrzése](media/asdk-validate-backup/5.PNG) 

7. Adja meg a biztonsági mentési beállítások lapon, valamint a megosztás eléréséhez használni kívánt felhasználónevet és jelszót a korábban ismertetett [Előfeltételek szakaszban](#prereqs) . Kattintson a **tovább**gombra: 

   ![ASDK biztonsági mentési beállítások lapja](media/asdk-validate-backup/6.PNG) 

8. Tekintse át a ASDK telepítéséhez használni kívánt telepítési parancsfájlt az összefoglalás lapon. A telepítés megkezdéséhez kattintson a **telepítés** gombra. 

    ![ASDK összegzése lap](media/asdk-validate-backup/7.PNG) 


### <a name="use-powershell-to-deploy-the-asdk-in-recovery-mode"></a>A ASDK telepítése helyreállítási módban a PowerShell használatával

Módosítsa az alábbi PowerShell-parancsokat a környezetéhez, és futtassa őket a ASDK Felhőbeli helyreállítási módban való üzembe helyezéséhez:

**A InstallAzureStackPOC. ps1 parancsfájl használatával indítsa el a Cloud Recoveryt a titkosítási kulccsal.**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$key = Read-Host -AsSecureString -Prompt "Your backup encryption key"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupEncryptionKeyBase64 $key `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

**A InstallAzureStackPOC. ps1 parancsfájl használatával indítsa el a visszafejtési tanúsítvánnyal rendelkező Cloud Recoveryt.**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$decryptioncertpassword  = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupDecryptionCertPassword $decryptioncertpassword `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="complete-cloud-recovery"></a>A felhőalapú helyreállítás befejezése 
A sikeres felhőalapú helyreállítás után a **Restore-AzureStack** parancsmaggal kell végrehajtania a visszaállítást. 

A Azure Stack operátorként való bejelentkezés után [telepítse Azure stack PowerShellt](asdk-post-deploy.md#install-azure-stack-powershell) , és futtassa a következő parancsokat a biztonsági másolatból való visszaállításhoz használandó tanúsítvány és jelszó megadásához:

**Helyreállítási mód tanúsítványfájl-fájllal**

> [!NOTE]
> Azure Stack üzemelő példány biztonsági okokból nem őrzi meg a visszafejtési tanúsítványt. A visszafejtési tanúsítványt és a hozzá tartozó jelszót újra meg kell adnia.

```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>" `
 -DecryptionCertPath "<path to decryption certificate with file name (.pfx)>" `
 -DecryptionCertPassword $decryptioncertpassword
```

**Helyreállítási mód titkosítási kulccsal**
```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>"
```

Várjon 60 percet a parancsmag meghívása után, hogy megkezdje a biztonsági mentési adatmennyiség ellenőrzését a felhőalapú helyreállított ASDK.

## <a name="next-steps"></a>További lépések
[Azure Stack regisztrálása](asdk-register.md)

