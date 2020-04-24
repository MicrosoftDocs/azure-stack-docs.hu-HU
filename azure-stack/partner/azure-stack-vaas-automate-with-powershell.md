---
title: Azure Stack érvényesítésének automatizálása a PowerShell használatával
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan automatizálhatja Azure Stack érvényesítését a PowerShell használatával.
author: mattbriggs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a3b7919f920dc173a3378328bbfff427b9820d48
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661433"
---
# <a name="automate-azure-stack-hub-validation-with-powershell"></a>Azure Stack hub ellenőrzésének automatizálása a PowerShell-lel

A szolgáltatásként történő érvényesítés lehetővé teszi a tesztek automatizálását a **RunVaaSAutomation. ps1** parancsfájl használatával.

Ez a szkript a következőket tudja használni:

> [!div class="checklist"]
> * Telepítse az előfeltételeket.
> * Telepítse és indítsa el a helyi ügynököt.
> * A tesztelési fázis, a megoldás-ellenőrzés és a csomag-ellenőrzési munkafolyamatok esetében az Varga teszteket indítsa el.
> * Vizsgálati eredmények jelentése

Az alábbi hivatkozások azt ismertetik, hogy miként futtathatók a tesztek az az Varga-portálon keresztül. A szkript használata előtt meg kell ismernie a szükséges paramétereket és azok értékeit:

* SolutionValidation-munkafolyamat: [új Azure stack hub-megoldás érvényesítése](azure-stack-vaas-validate-solution-new.md)
* PackageValidation munkafolyamat: [OEM-csomagok ellenőrzése](azure-stack-vaas-validate-oem-package.md)
* TestPass munkafolyamat: [teszt ütemezése](azure-stack-vaas-schedule-test-pass.md)

## <a name="download-the-automation-scripts"></a>Az Automation-parancsfájlok letöltése

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort.

2. Az Automation-szkript letöltéséhez futtassa a következő szkriptet:

```powershell
# Review and update the $RootFolder parameter
$RootFolder = "c:\VaaS"

if (-not(Test-Path($RootFolder))) {
    mkdir $RootFolder
}
Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "$RootFolder\RunVaaSAutomation.zip"
Expand-Archive -Path "$RootFolder\RunVaaSAutomation.zip" -DestinationPath "$RootFolder\RunVaaSAutomation" -Force
Set-Location "$RootFolder\RunVaaSAutomation"
```

## <a name="launch-the-solution-validation-workflow"></a>A megoldás-ellenőrzési munkafolyamat elindítása

Ha szeretné megtudni, hogyan futtathatja a megoldás-ellenőrzési munkafolyamatot az alapmodul-portálon, lásd: [OEM-csomagok ellenőrzése](azure-stack-vaas-validate-oem-package.md).

Futtassa a következő parancsfájlt a megfelelő paraméterek értékeivel:

```powershell
# Review and update the following parameters
$VaaSAccountUserName = ""
$VaaSAccountPassword = ""
$VaaSAccountTenantId = ""
$ServiceAdminUserName = ""
$ServiceAdminPassword = ""
$TenantAdminUserName  = ""
$TenantAdminPassword  = ""
$CloudAdminUserName   = ""
$CloudAdminPassword   = ""
$SolutionName   = ''
$ProjectName    = ''
$DiagnosticsStorageConnection=''
$VaaSProject_SolutionValidation_Configuration='Min' # enter 'Min' or 'Max'

# No need to modify the following lines
Import-Module .\VaaSAutomation.psm1 -verbose -force
$stampInfo = Get-StampInfo -cloudAdminUserName $CloudAdminUserName -cloudAdminPassword $CloudAdminPassword -retryCount 3 -retryPeriod 30 -outputFolder "."
$AgentName = "$((Get-WmiObject win32_computersystem).DNSHostName).$((Get-WmiObject win32_computersystem).Domain)".ToLower()
$VaaSAccountCredentail = New-Object System.Management.Automation.PSCredential ($VaaSAccountUserName, (ConvertTo-SecureString $VaaSAccountPassword -AsPlainText -Force))

$testParameters = @{}
$projectParameters = @{
    "Configuration" = $VaaSProject_SolutionValidation_Configuration;
}
$scriptParameters = @{
    'VaaSAccountCredentail' = $VaaSAccountCredentail;
    'VaaSAccountTenantId' = $VaaSAccountTenantId;
    'VaaSSolutionName' = $SolutionName;
    'VaaSProjectType' = 'SolutionValidation';
    'VaaSProjectName' = $ProjectName;
    'VaaSProjectParameterHashTable'= $projectParameters;
    'VaaSTestFilter' = 'Test';
    'VaaSTestFilterValue' = '';
    'VaaSTestParameterHashTable'= $testParameters;
    'VaaSOnPremAgentName'= $AgentName;
    'MaxScriptWaitTimeInHours'=48;
    'ServiceAdminUserName' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUserName' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUserName' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DiagnosticsStorageConnection' = $DiagnosticsStorageConnection;
}
& .\RunVaaSAutomation.ps1 @scriptParameters
```

## <a name="launch-package-validation-workflow"></a>Csomag-ellenőrzési munkafolyamat elindítása

Ha szeretné megtudni, hogyan futtathatja a csomag-ellenőrzési munkafolyamatot a következő helyen: [OEM-csomagok ellenőrzése](azure-stack-vaas-validate-oem-package.md).

Futtassa a következő parancsfájlt a megfelelő paraméterek értékeivel:

```powershell
# Review and update the following parameters
$VaaSAccountUserName = ""
$VaaSAccountPassword = ""
$VaaSAccountTenantId = ""
$ServiceAdminUserName = ""
$ServiceAdminPassword = ""
$TenantAdminUserName  = ""
$TenantAdminPassword  = ""
$CloudAdminUserName   = ""
$CloudAdminPassword   = ""
$SolutionName   = ''
$ProjectName    = ''
$DiagnosticsStorageConnection=''
$VaaSProject_PackageValidation_PackageBlobUri=''
$VaaSProject_PackageValidation_RequireDigitalSignature = "false" # enter 'true' or 'false' string
$VaaSProject_PackageValidation_LocalPathtoAzureStackUpdatePkgs = ""
$VaaSProject_PackageValidation_LocalPathtoOEMUpdatePkgs = ""

# No need to modify the following lines
Import-Module .\VaaSAutomation.psm1 -verbose -force
$stampInfo = Get-StampInfo -cloudAdminUserName $CloudAdminUserName -cloudAdminPassword $CloudAdminPassword -retryCount 3 -retryPeriod 30 -outputFolder "."
$AgentName = "$((Get-WmiObject win32_computersystem).DNSHostName).$((Get-WmiObject win32_computersystem).Domain)".ToLower()
$VaaSAccountCredentail = New-Object System.Management.Automation.PSCredential ($VaaSAccountUserName, (ConvertTo-SecureString $VaaSAccountPassword -AsPlainText -Force))

$testParameters = @{
    "RequireDigitalSignature" = $VaaSProject_PackageValidation_RequireDigitalSignature;
    "LocalPathtoAzureStackUpdatePkgs" = $VaaSProject_PackageValidation_LocalPathtoAzureStackUpdatePkgs;
    "LocalPathtoOEMUpdatePkgs" = $VaaSProject_PackageValidation_LocalPathtoOEMUpdatePkgs;
}

$projectParameters = @{
    "PackageBlobUri" = $VaaSProject_PackageValidation_PackageBlobUri;
}
$scriptParameters = @{
    'VaaSAccountCredentail' = $VaaSAccountCredentail;
    'VaaSAccountTenantId' = $VaaSAccountTenantId;
    'VaaSSolutionName' = $SolutionName;
    'VaaSProjectType' = 'PackageValidation';
    'VaaSProjectName' = $ProjectName;
    'VaaSProjectParameterHashTable' = $projectParameters;
    'VaaSTestFilter' = 'Test';
    'VaaSTestFilterValue' = '';
    'VaaSTestParameterHashTable'= $testParameters;
    'VaaSOnPremAgentName'= $AgentName;
    'MaxScriptWaitTimeInHours'=96;
    'ServiceAdminUserName' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUserName' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUserName' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DiagnosticsStorageConnection' = $DiagnosticsStorageConnection;
}
& .\RunVaaSAutomation.ps1 @scriptParameters
```

## <a name="launch-the-test-pass-workflow"></a>A test pass munkafolyamat elindítása

Ha meg szeretné tudni, hogyan futtathatja a test pass-munkafolyamatot az Varga porton keresztül, olvassa el a [teszt ütemezése](azure-stack-vaas-schedule-test-pass.md)című témakört

Futtassa a következő parancsfájlt a megfelelő paraméterek értékeivel:

```powershell
# Review and update the following parameters
$VaaSAccountUserName = ""
$VaaSAccountPassword = ""
$VaaSAccountTenantId = ""
$ServiceAdminUserName = ""
$ServiceAdminPassword = ""
$TenantAdminUserName  = ""
$TenantAdminPassword  = ""
$CloudAdminUserName   = ""
$CloudAdminPassword   = ""
$SolutionName   = ''
$ProjectName    = ''
$DiagnosticsStorageConnection=''

# No need to modify the following lines
# The following code is an example of running the "Cloud Simulation Engine" test
Import-Module .\VaaSAutomation.psm1 -verbose -force
$stampInfo = Get-StampInfo -cloudAdminUserName $CloudAdminUserName -cloudAdminPassword $CloudAdminPassword -retryCount 3 -retryPeriod 30 -outputFolder "."
$AgentName = "$((Get-WmiObject win32_computersystem).DNSHostName).$((Get-WmiObject win32_computersystem).Domain)".ToLower()
$VaaSAccountCredentail = New-Object System.Management.Automation.PSCredential ($VaaSAccountUserName, (ConvertTo-SecureString $VaaSAccountPassword -AsPlainText -Force))

$VaaSTestFilter='Test'
$VaaSTestFilterValue='cloudsimulation'
$testParameters = @{
    'ServiceAdminUser' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUser' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUser' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DomainFQDN' = "";
    'DomainAdminUser' = "";
    'DomainAdminPassword' = "";
    'TenantId' = $stampInfo.AadTenantId;
    'ExternalFqdn' = $stampInfo.ExternalDomainFQDN;
    'NumberOfNodes' = "$($stampInfo.NumberOfNodes)";
    'Region' = $stampInfo.RegionName;
    'RunDurationInHours' = 24;
    'EnableSuBR' = "false";
    'Faults' = "";
    'Resources' = "";
    'FaultControllerSettings' = "default";
    'DiagnosticsStorageConnection' = $diagnosticsStorageConnection;
    'DiagnosticsContainerName' = "$(New-Guid).ToString().ToLower()";
    'MaxFiddlerCaptureSizeInMB' = "0";
    'PackageHashCode' = "";
}

$projectParameters = @{}

$scriptParameters = @{
    'VaaSAccountCredentail' = $VaaSAccountCredentail;
    'VaaSAccountTenantId' = $VaaSAccountTenantId
    'VaaSSolutionName' = $SolutionName;
    'VaaSProjectType' = 'TestPass';
    'VaaSProjectName' = $ProjectName;
    'VaaSProjectParameterHashTable'= $projectParameters;
    'VaaSTestFilter'= $VaaSTestFilter;
    'VaaSTestFilterValue' = $VaaSTestFilterValue;
    'VaaSTestParameterHashTable'= $testParameters;
    'VaaSOnPremAgentName' = $AgentName;
    'MaxScriptWaitTimeInHours' = 24;
    'ServiceAdminUserName' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUserName' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUserName' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DiagnosticsStorageConnection' = $DiagnosticsStorageConnection;
}
& .\RunVaaSAutomation.ps1 @scriptParameters
```

## <a name="parameter-table"></a>Paraméter táblázata

További információ: [gyakori munkafolyamat-paraméterek](azure-stack-vaas-parameters.md).

| Paraméter | Leírás |
| --- | --- |
| VaaSAccountUserName | Az Ön által használt, az alaprendszerbeli felhasználói név |
| VaaSAccountPassword | Az Ön által használt Varga-jelszó az alaprendszer-portálhoz |
| VaaSAccountTenantId | Az Ön által használt Varga-bérlő GUID-azonosítója. |
| ServiceAdminUserName | Az Azure Stack hub szolgáltatás rendszergazdai fiókja.  |
| ServiceAdminPassword | Az Azure Stack hub szolgáltatás jelszava.  |
| TenantAdminUserName | Az elsődleges bérlő rendszergazdája.  |
| TenantAdminPassword | Az elsődleges bérlő jelszava.  |
| CloudAdminUserName | A Felhőbeli rendszergazda felhasználóneve.  |
| CloudAdminPassword | A felhő rendszergazdájának jelszava.  |
| SolutionName | Az alapszolgáltatási megoldás neve. |
| Projektnév | Az az Varga-munkafolyamat neve. |
| DiagnosticsStorageConnection | Egy Azure Storage-fiókhoz tartozó SAS URL-cím, amelybe a rendszer a diagnosztikai naplókat másolja a tesztek végrehajtása során. A SAS URL-cím létrehozásával kapcsolatos utasításokért lásd: [a diagnosztikai kapcsolatok karakterláncának létrehozása](azure-stack-vaas-parameters.md). |

## <a name="review-the-results"></a>Az eredmények áttekintése

A rendszer a naplókat és a jelentéseket a jelenlegi munkamappa szerint menti. 

További lehetőségekért lásd: [tesztek figyelése és kezelése az alapvizsgálati portálon](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Stack hub PowerShell-ről, tekintse át a legújabb modulokat.

- [Azure Stack hub-modul](/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
