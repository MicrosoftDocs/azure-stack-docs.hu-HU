---
title: SQL erőforrás-szolgáltató karbantartási műveletei
titleSuffix: Azure Stack Hub
description: Ismerje meg az SQL erőforrás-szolgáltató karbantartási műveleteit Azure Stack hub-on.
author: bryanla
ms.topic: article
ms.date: 9/22/2020
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: 3472cf330efb250f20eb66a5df50239a66293307
ms.sourcegitcommit: 6efe456173ce77d52789144709195b6291d0d707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97950705"
---
# <a name="sql-resource-provider-maintenance-operations"></a>SQL erőforrás-szolgáltató karbantartási műveletei

Az SQL erőforrás-szolgáltató zárolt virtuális gépen (VM) fut. A karbantartási műveletek engedélyezéséhez frissítenie kell a virtuális gép biztonságát. Ehhez a legalacsonyabb jogosultsági szintű rendszerbiztonsági tag használatával hajtsa végre a [PowerShell elég adminisztrációs (JEA)](/powershell/scripting/learn/remoting/jea/overview) végpont *DBAdapterMaintenance*. Az erőforrás-szolgáltató telepítési csomagja tartalmaz egy parancsfájlt ehhez a művelethez.

## <a name="patching-and-updating"></a>Javítás és frissítés

Az SQL-erőforrás-szolgáltató nincs kiszolgálva Azure Stack hub részeként, mert ez egy kiegészítő összetevő. A Microsoft szükség szerint frissítéseket biztosít az SQL erőforrás-szolgáltatónak. Amikor megjelent egy frissített SQL-adapter, a rendszer parancsfájlt biztosít a frissítés alkalmazásához. Ez a szkript létrehoz egy új erőforrás-szolgáltató virtuális gépet, áttelepíti a régi szolgáltató virtuális gép állapotát az új virtuális gépre. További információ: [az SQL-erőforrás szolgáltatójának frissítése](azure-stack-sql-resource-provider-update.md).

### <a name="provider-vm"></a>Szolgáltató virtuális gép

Mivel az erőforrás-szolgáltató egy *felhasználói* virtuális gépen fut, alkalmaznia kell a szükséges javításokat és frissítéseket a kiadáskor. A javítási és frissítési ciklus részeként megadott Windows Update-csomagok segítségével alkalmazza a virtuális gépek frissítéseit.

## <a name="updating-sql-credentials"></a>SQL-hitelesítő adatok frissítése

Ön felelős a sysadmin fiókok létrehozásához és karbantartásához az SQL-kiszolgálókon. Az erőforrás-szolgáltatónak olyan fiókkal kell rendelkeznie, amely rendelkezik ezekkel a jogosultságokkal a felhasználók adatbázisainak kezeléséhez, de nincs szükség a felhasználói adatforrásokhoz való hozzáférésre. Ha frissítenie kell a sysadmin (rendszergazda) jelszavait az SQL-kiszolgálókon, az erőforrás-szolgáltató rendszergazdai felületén módosíthatja a tárolt jelszavakat. Ezeket a jelszavakat a Azure Stack hub-példány egy Key Vault tárolja.

A beállítások módosításához válassza a **Tallózás** &gt; **felügyeleti erőforrások** &gt; **SQL üzemeltetési kiszolgálók** &gt; **SQL-bejelentkezések** lehetőséget, és válasszon egy felhasználónevet. A változást az SQL-példányon kell végrehajtani (és szükség esetén a replikákat is). A **Beállítások** területen válassza a **jelszó** lehetőséget.

![SQL-rendszergazdai jelszó frissítése](./media/azure-stack-sql-rp-deploy/sql-rp-update-password.png)

## <a name="secrets-rotation"></a>Titkos kulcsok rotálása

*Ezek az utasítások csak Azure Stack hub integrált rendszerekre vonatkoznak.*

Ha az SQL-és a MySQL-erőforrás-szolgáltatót Azure Stack hub integrált rendszerekkel használja, az Azure Stack hub-operátor felelős a következő erőforrás-szolgáltatói infrastruktúra titkainak elforgatása érdekében, hogy azok ne járjanak le:

- Az [üzembe helyezés során megadott](azure-stack-pki-certs.md)külső SSL-tanúsítvány.
- Az erőforrás-szolgáltató virtuális gép helyi rendszergazdai fiókjának jelszava az üzembe helyezés során.
- Erőforrás-szolgáltató diagnosztikai felhasználói (dbadapterdiag) jelszava.
- (verzió: >= 1.1.47.0) Key Vault az üzembe helyezés során generált tanúsítvány.

### <a name="powershell-examples-for-rotating-secrets"></a>PowerShell-példák a titkok elforgatására

**Módosítsa az összes titkot egy időben.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds `
    -KeyVaultPfxPassword $keyvaultCertPasswd
```

**A diagnosztikai felhasználó jelszavának módosítása.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword  $passwd
```

**Módosítsa a virtuális gép helyi rendszergazdai fiókjának jelszavát.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Módosítsa az SSL-tanúsítvány jelszavát.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

**Módosítsa a Key Vault tanúsítvány jelszavát.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -KeyVaultPfxPassword $keyvaultCertPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>SecretRotationSQLProvider.ps1 paraméterek

|Paraméter|Leírás|Megjegyzés|
|-----|-----|-----|
|AzureEnvironment|Az Azure Stack hub üzembe helyezéséhez használt szolgáltatás-rendszergazdai fiók Azure-környezete. Csak az Azure AD-telepítésekhez szükséges. A támogatott környezeti nevek a következők: **AzureCloud**, **AzureUSGovernment**, vagy kínai Azure Active Directory, **AzureChinaCloud** használatával.|Választható|
|AzCredential|Azure Stack hub szolgáltatás rendszergazdai fiókjának hitelesítő adatai. A szkript sikertelen lesz, ha a AzCredential használt fiók többtényezős hitelesítést (MFA) igényel.|Kötelező|
|CloudAdminCredential|Azure Stack hub felhőalapú rendszergazdai tartományi fiókjának hitelesítő adatai.|Kötelező|
|PrivilegedEndpoint|A rendszerjogosultságú végpont a Get-AzureStackStampInformation eléréséhez.|Kötelező|
|DiagnosticsUserPassword|A diagnosztikai felhasználói fiók jelszava.|Választható|
|VMLocalCredential|Helyi rendszergazdai fiók a MySQLAdapter virtuális gépen.|Választható|
|DefaultSSLCertificatePassword|Az alapértelmezett SSL-tanúsítvány (*. pfx) jelszava.|Választható|
|DependencyFilesLocalPath|Függőségi fájlok helyi elérési útja|Választható|
|KeyVaultPfxPassword|Az adatbázis-adapter Key Vault tanúsítványának létrehozásához használt jelszó.|Választható|
|     |     |     |

### <a name="known-issues"></a>Ismert problémák

**Probléma**:<br>
Titkok rotációs naplói. A titkok elforgatásának naplói nem lesznek automatikusan begyűjtve, ha a titkos kód egyéni parancsfájl futtatása sikertelen.

**Áthidaló megoldás**:<br>
Az Get-AzsDBAdapterLogs parancsmaggal gyűjtheti össze az összes erőforrás-szolgáltatói naplót, beleértve a AzureStack.DatabaseAdapter.SecretRotation.ps1_ *. log, a C:\Logs. mentett adatokat.

## <a name="update-the-vm-operating-system"></a>A virtuális gép operációs rendszerének frissítése

A virtuális gép operációs rendszerének frissítéséhez használja az alábbi módszerek egyikét.

- Telepítse a legújabb erőforrás-szolgáltatói csomagot egy aktuálisan javított virtuálisgép-rendszerkép használatával.
- Telepítsen egy Windows Update csomagot az erőforrás-szolgáltató telepítése vagy frissítése során.

## <a name="update-the-vm-windows-defender-definitions"></a>A virtuális gép Windows Defender-definícióinak frissítése

A Windows Defender-definíciók frissítése:

1. Töltse le a Windows Defender-definíciók frissítését a [Windows Defender biztonsági intelligencia frissítéseiről](https://www.microsoft.com/wdsi/definitions).

   A definíciók frissítése lapon görgessen le a "frissítés manuális letöltése" gombra. Töltse le a "Windows Defender Antivirus for Windows 10 és a Windows 8,1" 64 bites fájlt.

   [Ezt a közvetlen hivatkozást](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) is használhatja a fpam-fe.exe fájl letöltéséhez/futtatásához.

2. Hozzon létre egy PowerShell-munkamenetet az SQL Resource Provider-adapter virtuális gép karbantartási végpontján.

3. Másolja a definíciók frissítési fájlját a virtuális gépre a karbantartási végpont-munkamenet használatával.

4. A karbantartási PowerShell-munkamenetben futtassa az *Update-DBAdapterWindowsDefenderDefinitions* parancsot.

5. A definíciók telepítése után javasoljuk, hogy törölje a definíciók frissítési fájlját a *Remove-ItemOnUserDrive* parancs használatával.

**Példa a PowerShell-parancsfájlra a definíciók frissítéséhez**

A Defender-definíciók frissítéséhez szerkesztheti és futtathatja a következő szkriptet. A parancsfájlban szereplő értékeket cserélje le a környezet értékeire.

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/wdsi/definitions.
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy the defender update file to the adapter VM.
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

## <a name="collect-diagnostic-logs"></a>Diagnosztikai naplók gyűjtése

::: moniker range=">= azs-2008"

Azure Stack hub több módon is gyűjthet, menthet és küldhet diagnosztikai naplókat Microsoft ügyfélszolgálataba. A 1.1.93 verziótól kezdődően az SQL erőforrás-szolgáltató támogatja a naplók Azure Stack hub-környezetből való gyűjtésének szabványos módját. További információ: [diagnosztikai naplók gyűjteménye](diagnostic-log-collection.md).

::: moniker-end

A 1.1.93 verziótól kezdődően az SQL erőforrás-szolgáltató támogatja a naplók Azure Stack hub-környezetből való gyűjtésének szabványos módját. Ha régebbi verziót használ, javasoljuk, hogy frissítse az SQL-erőforrás-szolgáltatót a legújabb verzióra.

A zárolt virtuális gépről származó naplók gyűjtéséhez használja a PowerShell elég adminisztrációs (JEA) végpont *DBAdapterDiagnostics*. Ez a végpont a következő parancsokat tartalmazza:

- **Get-AzsDBAdapterLog**. Ez a parancs létrehoz egy ZIP-csomagot az erőforrás-szolgáltató diagnosztikai naplóiból, és menti a fájlt a munkamenet felhasználói meghajtóján. Ezt a parancsot paraméterek nélkül is futtathatja, és a rendszer az utolsó négy órányi naplót gyűjti.
- **Remove-AzsDBAdapterLog**. Ez a parancs eltávolítja a meglévő napló csomagokat az erőforrás-szolgáltató virtuális gépen.

### <a name="endpoint-requirements-and-process"></a>Végponti követelmények és folyamat

Egy erőforrás-szolgáltató telepítésekor vagy frissítésekor a rendszer létrehozza a **dbadapterdiag** felhasználói fiókot. Ezt a fiókot fogja használni a diagnosztikai naplók összegyűjtéséhez.

>[!NOTE]
>A dbadapterdiag fiók jelszava megegyezik a szolgáltató üzembe helyezése vagy frissítése során létrehozott virtuális gépen a helyi rendszergazda számára használt jelszóval.

A *DBAdapterDiagnostics* parancsok használatához hozzon létre egy távoli PowerShell-munkamenetet az erőforrás-szolgáltató virtuális géphez, és futtassa a **Get-AzsDBAdapterLog** parancsot.

A naplók időtartományát a **FromDate** és a **ToDate** paraméterek használatával állíthatja be. Ha nem ad meg egyet vagy mindkét paramétert, a rendszer a következő alapértelmezett értékeket használja:

- A FromDate az aktuális idő előtt négy órával korábbi.
- A ToDate az aktuális idő.

**Példa PowerShell-parancsfájlra a naplók gyűjtéséhez**

Az alábbi szkript bemutatja, hogyan gyűjthet diagnosztikai naplókat az erőforrás-szolgáltató virtuális gépről.

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

# Clean up the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession
```
## <a name="configure-azure-diagnostics-extension-for-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató Azure Diagnostics-bővítményének konfigurálása
A Azure Diagnostics bővítmény alapértelmezés szerint telepítve van az SQL Resource Provider adapter virtuális gépén. A következő lépések bemutatják, hogyan szabhatja testre a bővítményt az SQL erőforrás-szolgáltató operatív eseménynaplóinak és az IIS-naplóknak a hibaelhárítás és a naplózás céljából történő összegyűjtéséhez.

1. Jelentkezzen be az Azure Stack Hub felügyeleti portálra.

2. A bal oldali ablaktáblán válassza a **virtuális gépek** lehetőséget, keresse meg az SQL Resource Provider adapter virtuális gépet, és válassza ki a virtuális gépet.

3. A virtuális gép **diagnosztikai beállításainál** lépjen a **naplók** lapra, és az **Egyéni** elemre kattintva testreszabhatja az eseménynaplók gyűjtését.
![A diagnosztikai beállítások keresése](media/azure-stack-sql-resource-provider-maintain/sqlrp-diagnostics-settings.png)

4. Adja hozzá a **Microsoft-AzureStack-DatabaseAdapter/ \* Operational!* _ az SQL erőforrás-szolgáltató operatív eseménynaplóinak összegyűjtéséhez.
![Eseménynaplók hozzáadása](media/azure-stack-sql-resource-provider-maintain/sqlrp-event-logs.png)

5. Az IIS-naplók gyűjtésének engedélyezéséhez jelölje be az _ *IIS-naplók** és a **Sikertelen kérelmek naplófájljait**.
![IIS-naplók hozzáadása](media/azure-stack-sql-resource-provider-maintain/sqlrp-iis-logs.png)

6. Végül válassza a **Mentés** lehetőséget a diagnosztika összes beállításának mentéséhez.

Miután az eseménynaplók és az IIS-naplók gyűjteménye konfigurálva van az SQL-erőforrás-szolgáltatóhoz, a naplók a **sqladapterdiagaccount** nevű rendszertároló fiókban találhatók.

Ha többet szeretne megtudni a Azure Diagnostics bővítménnyel kapcsolatban, tekintse meg a [Mi az Azure Diagnostics Extension](/azure/azure-monitor/platform/diagnostics-extension-overview)című témakört.

## <a name="next-steps"></a>További lépések

[SQL Server üzemeltetési kiszolgálók hozzáadása](azure-stack-sql-resource-provider-hosting-servers.md)
