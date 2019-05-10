---
title: A MySQL erőforrás-szolgáltató az Azure Stacken karbantartása |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a MySQL erőforrás-szolgáltató szolgáltatás az Azure Stacken.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 24e1e315a9b52693cb2b9ff26bf424ee8e9d4f3f
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172316"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>MySQL erőforrás-szolgáltatói karbantartási műveletek

A MySQL erőforrás-szolgáltató zárolt virtuális gépen fut. Ahhoz, hogy a karbantartási műveleteket, frissíteni szeretné a virtuális gép biztonsági. Ehhez használja a legalacsonyabb jogosultsági szint elvének PowerShell csak Enough Administration (JEA) végpont DBAdapterMaintenance használhatja. Az erőforrás-szolgáltató telepítési csomag tartalmaz egy parancsfájlt, ehhez a művelethez.

## <a name="update-the-virtual-machine-operating-system"></a>A virtuális gép operációs rendszerének frissítése

Mivel az erőforrás-szolgáltatót futtat egy *felhasználói* virtuális gép, alkalmaznia kell a szükséges javítások és frissítések már kiadásakor. A javítási és frissítési ciklus részeként a Windows update csomagok biztosított használatával frissítések alkalmazása a virtuális géphez.

Frissítse a szolgáltatót virtuális gépet a következő módszerek egyikével:

- Telepítse a legújabb szolgáltató erőforráscsomagja jelenleg javított Windows Server 2016 Core rendszerkép használatával.
- Windows Update csomag telepítése, a telepítés során, vagy frissítse az erőforrás-szolgáltató.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>A virtuális géphez a Windows Defender-definíciók frissítése

A Defender-definíciók frissítéséhez kövesse az alábbi lépéseket:

1. A Windows Defender-definíciók frissítése a letöltési [Windows Defender definíciós](https://www.microsoft.com/en-us/wdsi/definitions).

    A definíciók oldalon görgessen "Manuálisan töltse le és telepítse a definíciókat". Töltse le a "Windows Defender víruskereső Windows 10 és Windows 8.1" 64 bites fájlrendszer.

    Másik megoldásként használhatja [a közvetlen hivatkozás](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) letöltése és futtatása fpam-fe.exe fájl.

2. Nyisson meg egy PowerShell-munkamenetet, a MySQL erőforrás szolgáltató adapter virtuális gép karbantartási végpontra.

3. Az erőforrás-szolgáltató adapter a virtuális gép a karbantartás végpont munkamenet használatával másolja a definíciók frissítési fájlt.

4. A karbantartási PowerShell-munkamenetben futtassa a _Update-DBAdapterWindowsDefenderDefinitions_ parancsot.

5. Miután telepítette a definíciókat, azt javasoljuk, hogy a definíciók frissítési fájl használatával törölje a _Remove-ItemOnUserDrive)_ parancsot.

**PowerShell-példaszkript definíciók frissítéséhez.**

Szerkesztheti, és futtassa a következő szkriptet a Defender-definíciók frissítéséhez. Cserélje le a szkriptben lévő értékek a környezetből.

```powershell
# Set credentials for the local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.  
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

## <a name="secrets-rotation"></a>Titkos kódok rotációja

*Ezek az utasítások csak az Azure Stack integrált rendszerek vonatkoznak.*

Ha az SQL- és MySQL erőforrás-szolgáltatók használata az Azure Stack integrált rendszerek, az Azure Stack – operátor felelős a következő erőforrás szolgáltató infrastruktúra titkos győződjön meg arról, hogy azok ne járjanak le az elforgatást:

- Külső SSL-tanúsítvány [üzembe helyezés során](azure-stack-pki-certs.md).
- A resource provider virtuális gép helyi rendszergazdai fiók jelszavának üzembe helyezés során.
- Erőforrás-szolgáltató (dbadapterdiag) diagnosztikai felhasználó jelszava.

### <a name="powershell-examples-for-rotating-secrets"></a>A titkos kulcsok rotált PowerShell-példák

**Módosítsa a titkos kulcsait egy időben.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds

```

**A diagnosztikai felhasználói jelszó módosítása.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword  $passwd

```

**A virtuális gép helyi rendszergazdai fiók jelszavának módosítása.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**Módosítsa az SSL-tanúsítványának jelszava.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd

```

### <a name="secretrotationmysqlproviderps1-parameters"></a>SecretRotationMySQLProvider.ps1 parameters

|Paraméter|Leírás|
|-----|-----|
|AzCredential|Az Azure Stack szolgáltatás-rendszergazdai fiók hitelesítő adatait.|
|CloudAdminCredential|Az Azure Stack felhő rendszergazdai tartományifiók-hitelesítő adat.|
|PrivilegedEndpoint|Kiemelt jogosultságú végpont Get-AzureStackStampInformation eléréséhez.|
|DiagnosticsUserPassword|Diagnosztikai felhasználói fiók jelszavát.|
|VMLocalCredential|A helyi rendszergazdai fiók a MySQLAdapter virtuális gépen.|
|DefaultSSLCertificatePassword|Alapértelmezett SSL-tanúsítvány (* pfx) jelszavát.|
|DependencyFilesLocalPath|Függőségi fájl helyi elérési útja.|
|     |     |

### <a name="known-issues"></a>Ismert problémák

**A probléma leírása:**<br>
A titkos kódok rotációja naplókat automatikusan nem gyűjt, ha a titkos tanúsítványrotációs parancsfájlt sikertelen futtatásakor.

**Megkerülő megoldás:**<br>
A Get-AzsDBAdapterLogs parancsmag használatával minden az erőforrás szolgáltató naplók összegyűjtése, beleértve AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, C:\Logs mentett.

## <a name="collect-diagnostic-logs"></a>Diagnosztikai naplók összegyűjtése

Naplók gyűjtése a zárolt virtuális gépről, a PowerShell csak Enough Administration (JEA) végpont DBAdapterDiagnostics használhatja. Ez a végpont a következő parancsokat kínálja:

- **Get-AzsDBAdapterLog**. Ez a parancs létrehoz egy zip-csomagját, az erőforrás-szolgáltató diagnosztikai naplók, és menti a fájlt a munkamenet felhasználói meghajtón. Ez a parancs paraméterek nélkül futtathatja, és a naplók az elmúlt négy órában gyűjtött.

- **Remove-AzsDBAdapterLog**. Ez a parancs eltávolítja a meglévő log csomagokat a virtuális gép erőforrás-szolgáltató.

### <a name="endpoint-requirements-and-process"></a>Végpont követelményei és folyamata

Ha egy erőforrás-szolgáltató telepítése vagy frissítése, a dbadapterdiag felhasználói fiók jön létre. Ezt a fiókot fogja használni a diagnosztikai naplók gyűjtésére.

>[!NOTE]
>A dbadapterdiag jelszava megegyezik a helyi rendszergazda, a virtuális gépen, egy szolgáltató központi telepítés vagy frissítés során létrehozott használt jelszót.

Használatához a _DBAdapterDiagnostics_ parancsokat, és hozzon létre egy távoli PowerShell-munkamenetben az erőforrás-szolgáltató virtuális géphez, és futtassa a **Get-AzsDBAdapterLog** parancsot.

Az Erőforrásnapló-gyűjtés időtartamának beállítása használatával a **FromDate** és **ToDate** paramétereket. Ha nem adja meg az egyik vagy mindkét paramétert, a következő Alapértelmezések használhatók:

* FromDate négy órát jelent az aktuális időpont elé.
* ToDate az az aktuális idő.

**PowerShell-példaszkript naplók gyűjtésére szolgáló.**

A következő parancsprogram mutatja be a diagnosztikai naplók gyűjtésére a virtuális gép erőforrás-szolgáltató.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'
$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds
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
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession

```

## <a name="next-steps"></a>További lépések

[A MySQL erőforrás-szolgáltató eltávolítása](azure-stack-mysql-resource-provider-remove.md)
