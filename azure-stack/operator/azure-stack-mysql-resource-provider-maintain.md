---
title: A MySQL erőforrás-szolgáltató karbantartása a Azure Stackon | Microsoft Docs
description: Ismerje meg, hogyan tarthatja karban a MySQL erőforrás-szolgáltató szolgáltatást Azure Stackon.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 6667fd3db21cd6138e756c16eb8e68b8ecd1b3e9
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829417"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>MySQL erőforrás-szolgáltató karbantartási műveletei

A MySQL erőforrás-szolgáltató zárolt virtuális gépen fut. A karbantartási műveletek engedélyezéséhez frissítenie kell a virtuális gép biztonságát. Ha ezt a legalacsonyabb jogosultsági szint elve alapján szeretné elvégezni, akkor a PowerShell elég adminisztrációs (JEA) végpont DBAdapterMaintenance használhatja. Az erőforrás-szolgáltató telepítési csomagja tartalmaz egy parancsfájlt ehhez a művelethez.

## <a name="update-the-virtual-machine-operating-system"></a>A virtuális gép operációs rendszerének frissítése

Mivel az erőforrás-szolgáltató egy *felhasználói* virtuális gépen fut, a kiadáskor a szükséges javításokat és frissítéseket kell alkalmaznia. A javítási és frissítési ciklus részeként biztosított Windows Update-csomagokat használhatja a virtuális gép frissítéseinek alkalmazásához.

Frissítse a szolgáltatói virtuális gépet az alábbi módszerek egyikével:

- Telepítse a legújabb erőforrás-szolgáltatói csomagot egy jelenleg javított Windows Server 2016 Core rendszerképpel.
- Telepítsen egy Windows Update csomagot a telepítése során, vagy frissítsen az erőforrás-szolgáltatóra.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>A virtuális gép Windows Defender-definícióinak frissítése

A Defender-definíciók frissítéséhez kövesse az alábbi lépéseket:

1. Töltse le a Windows Defender-definíciók frissítését a [Windows Defender-definícióból](https://www.microsoft.com/en-us/wdsi/definitions).

    A definíciók lapon görgessen le a "a definíciók manuális letöltése és telepítése" elemre. Töltse le a "Windows Defender Antivirus for Windows 10 és a Windows 8,1" 64 bites fájlt.

    Másik lehetőségként használja [ezt a közvetlen hivatkozást](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) a fpam-fe. exe fájl letöltéséhez/futtatásához.

2. Nyisson meg egy PowerShell-munkamenetet a MySQL erőforrás-szolgáltatói adapter virtuális gépe karbantartási végpontján.

3. Másolja a definíciók frissítési fájlját az erőforrás-szolgáltatói adapter virtuális gépre a karbantartási végpont-munkamenet használatával.

4. A karbantartási PowerShell-munkamenetben futtassa az _Update-DBAdapterWindowsDefenderDefinitions_ parancsot.

5. A definíciók telepítése után javasoljuk, hogy törölje a definíciók frissítési fájlját a _Remove-ItemOnUserDrive_ parancs használatával.

**Példa a PowerShell-parancsfájlra a definíciók frissítéséhez.**

A Defender-definíciók frissítéséhez szerkesztheti és futtathatja a következő szkriptet. A parancsfájlban szereplő értékeket cserélje le a környezet értékeire.

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

## <a name="secrets-rotation"></a>Titkok rotációja

*Ezek az utasítások csak Azure Stack integrált rendszerekre vonatkoznak.*

Ha Azure Stack integrált rendszerrel rendelkező SQL-és MySQL-erőforrás-szolgáltatót használ, a Azure Stack operátor feladata a következő erőforrás-szolgáltatói infrastruktúra-titkok elforgatása annak biztosítása érdekében, hogy ne járjanak le:

- Az [üzembe helyezés során megadott](azure-stack-pki-certs.md)külső SSL-tanúsítvány.
- Az erőforrás-szolgáltató virtuális gép helyi rendszergazdai fiókjának jelszava az üzembe helyezés során.
- Erőforrás-szolgáltató diagnosztikai felhasználói (dbadapterdiag) jelszava.

### <a name="powershell-examples-for-rotating-secrets"></a>PowerShell-példák a titkok elforgatására

**Módosítsa az összes titkot egy időben.**

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

**A diagnosztikai felhasználó jelszavának módosítása.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword  $passwd

```

**Módosítsa a virtuális gép helyi rendszergazdai fiókjának jelszavát.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**Módosítsa az SSL-tanúsítvány jelszavát.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd

```

### <a name="secretrotationmysqlproviderps1-parameters"></a>SecretRotationMySQLProvider. ps1 paraméterek

|Paraméter|Leírás|
|-----|-----|
|AzCredential|Azure Stack a szolgáltatás-rendszergazdai fiók hitelesítő adatait.|
|CloudAdminCredential|Azure Stack a Felhőbeli rendszergazdai tartományi fiók hitelesítő adatait.|
|PrivilegedEndpoint|A rendszerjogosultságú végpont a Get-AzureStackStampInformation eléréséhez.|
|DiagnosticsUserPassword|A diagnosztikai felhasználói fiók jelszava.|
|VMLocalCredential|A helyi rendszergazdai fiók a MySQLAdapter virtuális gépen.|
|DefaultSSLCertificatePassword|Az alapértelmezett SSL-tanúsítvány (* pfx) jelszava.|
|DependencyFilesLocalPath|Függőségi fájlok helyi elérési útja|
|     |     |

### <a name="known-issues"></a>Ismert problémák

**Probléma:**<br>
A titkok rotációs naplója nem kerül automatikusan begyűjtésre, ha a titkos elforgatási parancsfájl futtatása meghiúsul.

**Workaround**<br>
A Get-AzsDBAdapterLogs parancsmaggal gyűjtheti össze az összes erőforrás-szolgáltatói naplót, beleértve a AzureStack. DatabaseAdapter. SecretRotation. ps1 _*. log és a C:\Logs. mentett adatokat.

## <a name="collect-diagnostic-logs"></a>Diagnosztikai naplók gyűjtése

A zárolt virtuális gépről származó naplók összegyűjtéséhez használhatja a PowerShell elég adminisztrációs (JEA) végponti DBAdapterDiagnostics. Ez a végpont a következő parancsokat tartalmazza:

- **Get-AzsDBAdapterLog**. Ez a parancs létrehoz egy ZIP-csomagot az erőforrás-szolgáltató diagnosztikai naplóiból, és menti a fájlt a munkamenet felhasználói meghajtóján. Ezt a parancsot paraméterek nélkül is futtathatja, és a rendszer az utolsó négy órányi naplót gyűjti.

- **Remove-AzsDBAdapterLog**. Ez a parancs eltávolítja a meglévő napló csomagokat az erőforrás-szolgáltató virtuális gépen.

### <a name="endpoint-requirements-and-process"></a>Végponti követelmények és folyamat

Egy erőforrás-szolgáltató telepítésekor vagy frissítésekor a rendszer létrehozza a dbadapterdiag felhasználói fiókot. Ezt a fiókot fogja használni a diagnosztikai naplók összegyűjtéséhez.

>[!NOTE]
>A dbadapterdiag fiók jelszava megegyezik a szolgáltató üzembe helyezése vagy frissítése során létrehozott virtuális gép helyi rendszergazdájához használt jelszóval.

A _DBAdapterDiagnostics_ parancsok használatához hozzon létre egy távoli PowerShell-munkamenetet az erőforrás-szolgáltató virtuális géphez, és futtassa a **Get-AzsDBAdapterLog** parancsot.

A naplók időtartományát a **FromDate** és a **ToDate** paraméterek használatával állíthatja be. Ha nem ad meg egyet vagy mindkét paramétert, a rendszer a következő alapértelmezett értékeket használja:

* A FromDate az aktuális idő előtt négy órával korábbi.
* A ToDate az aktuális idő.

**Példa PowerShell-parancsfájlra a naplók összegyűjtéséhez.**

Az alábbi szkript bemutatja, hogyan gyűjthet diagnosztikai naplókat az erőforrás-szolgáltató virtuális gépről.

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
