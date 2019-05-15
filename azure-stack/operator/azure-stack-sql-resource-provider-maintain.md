---
title: Az SQL erőforrás-szolgáltató az Azure Stacken karbantartása |} A Microsoft Docs
description: Ismerje meg, hogyan kell karbantartani az SQL erőforrás-szolgáltató szolgáltatás az Azure Stacken.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 77b341baca92e53448065325118088ff01a59052
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617811"
---
# <a name="sql-resource-provider-maintenance-operations"></a>SQL erőforrás-szolgáltatói karbantartási műveletek

Az erőforrás-szolgáltató SQL zárolt virtuális gépen fut. Ahhoz, hogy a karbantartási műveleteket, frissíteni szeretné a virtuális gép biztonsági. Ehhez használja a legalacsonyabb jogosultsági egyszerű használható [PowerShell csak Enough Administration (JEA)](https://docs.microsoft.com/powershell/jea/overview) végpont *DBAdapterMaintenance*. Az erőforrás-szolgáltató telepítési csomag tartalmaz egy parancsfájlt, ehhez a művelethez.

## <a name="patching-and-updating"></a>Javításait és frissítését

Az erőforrás-szolgáltató az SQL Azure Stack részeként nem szervizelt, bővítménye, mert. A Microsoft szükség szerint az SQL erőforrás-szolgáltató frissítéseit tartalmazza. Amikor egy frissített SQL adapter megjelenik, a frissítés alkalmazásához megadunk egy szkriptet. Ez a szkript létrehoz egy új erőforrás-szolgáltató virtuális Gépet, az új virtuális gép-ba való migrálás a régi szolgáltató virtuális gép állapotát. További információkért lásd: [frissíteni az erőforrás-szolgáltató SQL](azure-stack-sql-resource-provider-update.md).

### <a name="provider-virtual-machine"></a>Szolgáltató virtuális gép

Mivel az erőforrás-szolgáltatót futtat egy *felhasználói* virtuális gép, alkalmaznia kell a szükséges javítások és frissítések már kiadásakor. A javítási és frissítési ciklus részeként a Windows update csomagok biztosított használatával frissítések alkalmazása a virtuális géphez.

## <a name="updating-sql-credentials"></a>SQL-hitelesítő adatok frissítése

Ön felelős létrehozása és fenntartása a rendszergazdai fiókok, az SQL Server-kiszolgálókon. Az erőforrás-szolgáltató ezen adatbázisok, felhasználók kezelése engedéllyel rendelkező fiók szükséges, de nincs szüksége a felhasználók adatokhoz való hozzáférést. Ha frissítenie az SQL Server-kiszolgálókon a sysadmin (rendszergazda) jelszavát, használhatja az erőforrás-szolgáltató rendszergazdai felület tárolt jelszó módosítására. Ezek a jelszavak vannak tárolva egy Key Vaultot az Azure Stack-példányon.

A beállítások módosításához válassza **Tallózás** &gt; **felügyeleti erőforrások** &gt; **SQL üzemeltető kiszolgálók** &gt; **SQL-Bejelentkezésekben** , és válassza ki a felhasználót. A módosítani kell az SQL-példányon első (és esetleges replikákat, ha szükséges.) A **beállítások**válassza **jelszó**.

![A rendszergazdai jelszó frissítése](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Titkos kódok rotációja

*Ezek az utasítások csak az Azure Stack integrált rendszerek vonatkoznak.*

Ha az SQL- és MySQL erőforrás-szolgáltatók használata az Azure Stack integrált rendszerek, az Azure Stack – operátor felelős a következő erőforrás szolgáltató infrastruktúra titkos győződjön meg arról, hogy azok ne járjanak le az elforgatást:

- Külső SSL-tanúsítvány [üzembe helyezés során](azure-stack-pki-certs.md).
- A resource provider virtuális gép helyi rendszergazdai fiók jelszavának üzembe helyezés során.
- Erőforrás-szolgáltató (dbadapterdiag) diagnosztikai felhasználó jelszava.

### <a name="powershell-examples-for-rotating-secrets"></a>A titkos kulcsok rotált PowerShell-példák

**Módosítsa a titkos kulcsait egy időben.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**A diagnosztikai felhasználói jelszó módosítása.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword  $passwd
```

**A virtuális gép helyi rendszergazdai fiók jelszavának módosítása.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Módosítsa az SSL-tanúsítványának jelszava.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>SecretRotationSQLProvider.ps1 parameters

|Paraméter|Leírás|
|-----|-----|
|AzCredential|Az Azure Stack szolgáltatás-rendszergazdai fiók hitelesítő adatait.|
|CloudAdminCredential|Az Azure Stack felhő rendszergazdai tartományifiók-hitelesítő adat.|
|PrivilegedEndpoint|Kiemelt jogosultságú végpont Get-AzureStackStampInformation eléréséhez.|
|DiagnosticsUserPassword|Diagnosztikai felhasználói fiók jelszavát.|
|VMLocalCredential|Helyi rendszergazdai fiók a MySQLAdapter virtuális gépen.|
|DefaultSSLCertificatePassword|Alapértelmezett SSL-tanúsítvány (* pfx) jelszavát.|
|DependencyFilesLocalPath|Függőségi fájl helyi elérési útja.|
|     |     |

### <a name="known-issues"></a>Ismert problémák

**A probléma**: Titkos kódok rotációja naplókat.<br>
A titkos kódok rotációja naplókat automatikusan nem gyűjt, ha a titkos Elforgatás egyéni parancsfájl futtatásakor sikertelen.

**Megkerülő megoldás**:<br>
Használja a Get-AzsDBAdapterLogs parancsmagot minden erőforrás szolgáltató naplókat, beleértve a AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, C:\Logs mentett.

## <a name="update-the-virtual-machine-operating-system"></a>A virtuális gép operációs rendszerének frissítése

Az alábbi módszerek valamelyikével a virtuális gép operációs rendszerének frissítése.

- Telepítse a legújabb szolgáltató erőforráscsomagja jelenleg javított Windows Server 2016 Core rendszerkép használatával.
- Windows Update csomag telepítése, a telepítés során, vagy frissítse, az erőforrás-szolgáltató.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>A virtuális géphez a Windows Defender-definíciók frissítése

A Windows Defender-definíciók frissítése:

1. A Windows Defender-definíciók frissítése a letöltési [Windows Defender definíciós](https://www.microsoft.com/en-us/wdsi/definitions).

   A definíciók frissítése a lapot, görgessen le a "Manuálisan töltse le és telepítse a definíciókat". Töltse le a "Windows Defender víruskereső Windows 10 és Windows 8.1" 64 bites fájlrendszer.

   Másik megoldásként használhatja [a közvetlen hivatkozás](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) letöltése és futtatása fpam-fe.exe fájl.

2. Hozzon létre egy PowerShell-munkamenetet a SQL resource provider adapter virtuális gép karbantartási végpontra.

3. A definíciók frissítési fájlt másolja a virtuális gép a karbantartás végpont munkamenet használatával.

4. A karbantartási PowerShell-munkamenetben futtassa a *Update-DBAdapterWindowsDefenderDefinitions* parancsot.

5. Miután telepítette a definíciókat, azt javasoljuk, hogy a definíciók frissítési fájl használatával törölje a *Remove-ItemOnUserDrive* parancsot.

**PowerShell-példaszkript definíciók frissítéséhez.**

Szerkesztheti, és futtassa a következő szkriptet a Defender-definíciók frissítéséhez. Cserélje le a szkriptben lévő értékek a környezetből.

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="collect-diagnostic-logs"></a>Diagnosztikai naplók összegyűjtése

Naplók gyűjtése a zárolt virtuális gépről, használhatja a PowerShell csak Enough Administration (JEA) végpont *DBAdapterDiagnostics*. Ez a végpont a következő parancsokat kínálja:

- **Get-AzsDBAdapterLog**. Ez a parancs létrehoz egy zip-csomagját, az erőforrás-szolgáltató diagnosztikai naplók, és menti a fájlt a munkamenet felhasználói meghajtón. Ez a parancs paraméterek nélkül futtathatja, és a naplók az elmúlt négy órában gyűjtött.
- **Remove-AzsDBAdapterLog**. Ez a parancs eltávolítja a meglévő log csomagokat a virtuális gép erőforrás-szolgáltató.

### <a name="endpoint-requirements-and-process"></a>Végpont követelményei és folyamata

Ha egy erőforrás-szolgáltató van telepítve, vagy frissíteni, a **dbadapterdiag** felhasználói fiók jön létre. Ezt a fiókot fogja használni a diagnosztikai naplók gyűjtésére.

>[!NOTE]
>A dbadapterdiag jelszava megegyezik a helyi rendszergazda, a virtuális gépen, egy szolgáltató központi telepítés vagy frissítés során létrehozott használt jelszót.

Használatához a *DBAdapterDiagnostics* parancsokat, és hozzon létre egy távoli PowerShell-munkamenetben az erőforrás-szolgáltató virtuális géphez, és futtassa a **Get-AzsDBAdapterLog** parancsot.

Az Erőforrásnapló-gyűjtés időtartamának beállítása használatával a **FromDate** és **ToDate** paramétereket. Ha nem adja meg az egyik vagy mindkét paramétert, a következő Alapértelmezések használhatók:

- FromDate négy órát jelent az aktuális időpont elé.
- ToDate az az aktuális idő.

**PowerShell-példaszkript naplók gyűjtésére szolgáló.**

A következő parancsprogram mutatja be a diagnosztikai naplók gyűjtésére a virtuális gép erőforrás-szolgáltató.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session.
$session | Remove-PSSession
```

## <a name="next-steps"></a>További lépések

[SQL Server kiszolgálót a kiszolgálók hozzáadása](azure-stack-sql-resource-provider-hosting-servers.md)
