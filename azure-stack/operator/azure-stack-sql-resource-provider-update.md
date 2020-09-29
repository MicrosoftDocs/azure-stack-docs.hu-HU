---
title: Az Azure Stack hub SQL erőforrás-szolgáltatójának frissítése
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan frissítheti az Azure Stack hub SQL erőforrás-szolgáltatóját.
author: bryanla
ms.topic: article
ms.date: 8/19/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 1c6a7e39131dc9d422a68161b3022ac1acc28f7e
ms.sourcegitcommit: b80d529ff47b15b8b612d8a787340c7b0f68165b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89472874"
---
# <a name="update-the-sql-resource-provider"></a>Az SQL típusú erőforrás-szolgáltató frissítése

> [!IMPORTANT]
> Az erőforrás-szolgáltató frissítése előtt tekintse át a kibocsátási megjegyzéseket, és ismerkedjen meg az új funkciókkal, javításokkal és az üzembe helyezést befolyásoló ismert problémákkal. A kibocsátási megjegyzések az erőforrás-szolgáltatóhoz szükséges minimális Azure Stack hub-verziót is meghatározzák.

Az új SQL-erőforrás-szolgáltató akkor szabadítható fel, ha Azure Stack hub új buildre frissül. Bár a meglévő erőforrás-szolgáltató továbbra is működik, javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb buildre.

 |Támogatott Azure Stack hub-verzió|Az SQL RP verziója|
  |-----|-----|
  |2005, 2002, 1910|[Az SQL RP verziója 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|
  |1908|[Az SQL RP verziója 1.1.33.0](https://aka.ms/azurestacksqlrp11330)| 
  |     |     |

Az SQL Resource Provider 1.1.33.0 kiadásával kezdődően a frissítések összegző jellegűek, és nem kell azokat a kiadási sorrendben telepíteni, amíg a 1.1.24.0 vagy újabb verzióról indul. Ha például az SQL-erőforrás-szolgáltató 1.1.24.0 verzióját futtatja, akkor a 1.1.33.0 vagy újabb verzióra is frissíthet, anélkül, hogy először telepítenie kell a 1.1.30.0 verziót. Az elérhető erőforrás-szolgáltatói verziók, valamint az Azure Stack hub támogatott verziójának áttekintéséhez tekintse meg a verziók listáját az [erőforrás-szolgáltató előfeltételeinek telepítése](./azure-stack-sql-resource-provider-deploy.md#prerequisites)című részben.

Az erőforrás-szolgáltató frissítéséhez használja a *UpdateSQLProvider.ps1* parancsfájlt. Helyi rendszergazdai jogokkal rendelkező szolgáltatásfiók használata, amely az előfizetés **tulajdonosa** . Ez a szkript az új SQL-erőforrás-szolgáltató letöltését tartalmazza. A frissítési folyamat hasonló az [erőforrás-szolgáltató üzembe helyezéséhez](./azure-stack-sql-resource-provider-deploy.md)használt folyamathoz. A Frissítési parancsfájl ugyanazokat az argumentumokat használja, mint a DeploySqlProvider.ps1 szkript, és meg kell adnia a tanúsítvány adatait.

## <a name="update-script-processes"></a>Parancsfájl-folyamatok frissítése

A *UpdateSQLProvider.ps1* szkript létrehoz egy új virtuális GÉPET (VM) a legújabb erőforrás-szolgáltatói kóddal.

> [!NOTE]
> Javasoljuk, hogy töltse le a legújabb Windows Server 2016 Core rendszerképet a piactér-felügyeletből. Ha frissítést kell telepítenie, **egyetlen** msu-csomagot is elhelyezhet a helyi függőségi útvonalon. A szkript sikertelen lesz, ha több MSU-fájl is van ezen a helyen.

Miután a *UpdateSQLProvider.ps1* szkript létrehoz egy új virtuális gépet, a parancsfájl áttelepíti a következő beállításokat a régi szolgáltató virtuális gépről:

* adatbázis-információk
* üzemeltetési kiszolgáló adatai
* szükséges DNS-rekord

## <a name="update-script-parameters"></a>Parancsfájl paramétereinek frissítése

A következő paramétereket adhatja meg a parancssorból a **UpdateSQLProvider.ps1** PowerShell-parancsfájl futtatásakor. Ha nem, vagy ha valamelyik paraméter ellenőrzése sikertelen, a rendszer felszólítja a szükséges paraméterek megadására.

| Paraméter neve | Leírás | Megjegyzés vagy alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A rendszerjogosultságú végpont eléréséhez szükséges felhőalapú rendszergazda hitelesítő adatai. | _Kötelező_ |
| **AzCredential** | Az Azure Stack hub szolgáltatás rendszergazdai fiókjának hitelesítő adatai. Használja ugyanazokat a hitelesítő adatokat, amelyeket az Azure Stack hub üzembe helyezéséhez használt. | _Kötelező_ |
| **VMLocalCredential** | Az SQL Resource Provider virtuális gép helyi rendszergazdai fiókjának hitelesítő adatai. | _Kötelező_ |
| **PrivilegedEndpoint** | Az emelt szintű végpont IP-címe vagy DNS-neve. |  _Kötelező_ |
| **AzureEnvironment** | Az Azure Stack hub üzembe helyezéséhez használt szolgáltatás-rendszergazdai fiók Azure-környezete. Csak az Azure AD-telepítésekhez szükséges. A támogatott környezeti nevek: **AzureCloud**, **AzureUSGovernment**, illetve kínai Azure ad-t, **AzureChinaCloud**-t használnak. | AzureCloud |
| **DependencyFilesLocalPath** | A Certificate. pfx fájlt is ebbe a könyvtárba kell helyeznie. | _Egyetlen csomópont esetében nem kötelező, de a többcsomópontos használatra kötelező_ |
| **DefaultSSLCertificatePassword** | A. pfx-tanúsítvány jelszava. | _Kötelező_ |
| **MaxRetryCount** | Az egyes műveletek újrapróbálkozási időpontjának száma, ha hiba történt.| 2 |
| **RetryDuration** |Az újrapróbálkozások közötti időtúllépési időköz (másodpercben). | 120 |
| **Eltávolítása** | Eltávolítja az erőforrás-szolgáltatót és az összes kapcsolódó erőforrást. | No |
| **DebugMode** | Megakadályozza a hibák automatikus törlését. | No |

## <a name="update-script-powershell-example"></a>Parancsfájl PowerShell-példa frissítése
> [!NOTE]
> Ez a frissítési folyamat csak Azure Stack hub integrált rendszerekre vonatkozik.

Ha az SQL erőforrás-szolgáltató verzióját 1.1.33.0 vagy korábbi verzióra frissíti, telepítenie kell a AzureRm. BootStrapper és a Azure Stack hub-modulok adott verzióját a PowerShell-ben. Ha az SQL Resource Provider verzió 1.1.47.0 frissíti, az üzembe helyezési parancsfájl automatikusan letölti és telepíti a szükséges PowerShell-modulokat az elérési út C:\Program Files\SqlMySqlPsh.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.8.2
```

> [!NOTE]
> A leválasztott forgatókönyvben le kell töltenie a szükséges PowerShell-modulokat, és manuálisan kell regisztrálnia az adattárat előfeltételként. További információt az [SQL erőforrás-szolgáltató üzembe helyezése című](azure-stack-sql-resource-provider-deploy.md) témakörben találhat.

Az alábbi példa egy emelt szintű PowerShell-konzolról futtatható *UpdateSQLProvider.ps1* szkript használatát szemlélteti. Ügyeljen rá, hogy szükség szerint módosítsa a változó információit és jelszavát:  

```powershell
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

Az erőforrás-szolgáltató frissítési parancsfájljának befejeződése után zárd be a jelenlegi PowerShell-munkamenetet.

## <a name="next-steps"></a>Következő lépések

[Az SQL-erőforrás szolgáltatójának karbantartása](azure-stack-sql-resource-provider-maintain.md)
