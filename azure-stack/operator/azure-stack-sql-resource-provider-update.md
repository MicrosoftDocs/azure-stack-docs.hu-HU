---
title: Az Azure Stack SQL Resource Provider frissítése | Microsoft Docs
description: Ismerje meg, hogyan frissítheti a Azure Stack SQL erőforrás-szolgáltatót.
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
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 26ce99f87f1b0e1e379bad6276c88a8e6772c035
ms.sourcegitcommit: 284f5316677c9a7f4c300177d0e2a905df8cb478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465350"
---
# <a name="update-the-sql-resource-provider"></a>Az SQL-erőforrás szolgáltatójának frissítése

*A következőkre vonatkozik: Azure Stack integrált rendszerek.*

Az új SQL-erőforrás-szolgáltató akkor szabadítható fel, ha a Azure Stack új buildre frissül. Bár a meglévő erőforrás-szolgáltató továbbra is működik, javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb buildre. 

Az SQL Resource Provider 1.1.33.0 kiadásával kezdődően a frissítések összegző jellegűek, és nem kell azokat a kiadásuk sorrendjében telepíteni; a 1.1.24.0 vagy újabb verziótól kezdődően. Ha például az SQL-erőforrás-szolgáltató 1.1.24.0 verzióját futtatja, akkor a 1.1.33.0 vagy újabb verzióra is frissíthet, anélkül, hogy először telepítenie kell a 1.1.30.0-verziót. Az elérhető erőforrás-szolgáltatói verziók, valamint a támogatott Azure Stack verziójának áttekintéséhez tekintse meg a verziók listáját az [erőforrás-szolgáltatói előfeltételek üzembe helyezése](./azure-stack-sql-resource-provider-deploy.md#prerequisites)című témakörben.

Az erőforrás-szolgáltató frissítéséhez használja a *UpdateSQLProvider. ps1* parancsfájlt. Ez a szkript az új SQL-erőforrás-szolgáltató letöltését tartalmazza. A frissítési folyamat hasonló az [erőforrás-szolgáltató üzembe helyezéséhez](./azure-stack-sql-resource-provider-deploy.md)használt folyamathoz. A Frissítési parancsfájl ugyanazokat az argumentumokat használja, mint a DeploySqlProvider. ps1 parancsfájl, és meg kell adnia a tanúsítvány adatait.

 > [!IMPORTANT]
 > Az erőforrás-szolgáltató frissítése előtt tekintse át a kibocsátási megjegyzéseket, és ismerkedjen meg az új funkciókkal, javításokkal és az üzembe helyezést befolyásoló ismert problémákkal.

## <a name="update-script-processes"></a>Parancsfájl-folyamatok frissítése

A *UpdateSQLProvider. ps1* szkript létrehoz egy új virtuális GÉPET (VM) a legújabb erőforrás-szolgáltatói kóddal.

> [!NOTE]
> Javasoljuk, hogy töltse le a legújabb Windows Server 2016 Core rendszerképet a piactér-felügyeletből. Ha frissítést kell telepítenie, **egyetlen** msu-csomagot is elhelyezhet a helyi függőségi útvonalon. A szkript sikertelen lesz, ha több MSU-fájl is van ezen a helyen.

Miután a *UpdateSQLProvider. ps1* parancsfájl létrehoz egy új virtuális gépet, a parancsfájl áttelepíti a következő beállításokat a régi szolgáltató virtuális gépről:

* Adatbázis-információk
* üzemeltetési kiszolgáló adatai
* szükséges DNS-rekord

## <a name="update-script-parameters"></a>Parancsfájl paramétereinek frissítése

A **UpdateSQLProvider. ps1** PowerShell-parancsfájl futtatásakor a parancssorban megadhatja a következő paramétereket. Ha nem, vagy ha valamelyik paraméter ellenőrzése sikertelen, a rendszer felszólítja a szükséges paraméterek megadására.

| Paraméter neve | Leírás | Megjegyzés vagy alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A rendszerjogosultságú végpont eléréséhez szükséges hitelesítő adatok a felhő rendszergazdájához. | _Szükséges_ |
| **AzCredential** | A Azure Stack szolgáltatás rendszergazdai fiókjának hitelesítő adatai. Használja ugyanazokat a hitelesítő adatokat, amelyeket a Azure Stack telepítéséhez használt. | _Szükséges_ |
| **VMLocalCredential** | Az SQL Resource Provider virtuális gép helyi rendszergazdai fiókjának hitelesítő adatai. | _Szükséges_ |
| **PrivilegedEndpoint** | Az emelt szintű végpont IP-címe vagy DNS-neve. |  _Szükséges_ |
| **AzureEnvironment** | Az Azure Stack telepítéséhez használt szolgáltatás-rendszergazdai fiók Azure-környezete. Csak az Azure AD-telepítésekhez szükséges. A támogatott környezeti nevek: **AzureCloud**, **AzureUSGovernment**, illetve kínai Azure ad-t, **AzureChinaCloud**-t használnak. | AzureCloud |
| **DependencyFilesLocalPath** | A Certificate. pfx fájlt is ebbe a könyvtárba kell helyeznie. | _Egyetlen csomópont esetében nem kötelező, de a többcsomópontos használatra kötelező_ |
| **DefaultSSLCertificatePassword** | A. pfx-tanúsítvány jelszava. | _Szükséges_ |
| **Maxretrycount csak** | Az egyes műveletek újrapróbálkozási időpontjának száma, ha hiba történt.| 2 |
| **RetryDuration** |Az újrapróbálkozások közötti időtúllépési időköz (másodpercben). | 120 |
| **Eltávolítás** | Eltávolítja az erőforrás-szolgáltatót és az összes kapcsolódó erőforrást. | Nem |
| **DebugMode** | Megakadályozza a hibák automatikus törlését. | Nem |

## <a name="update-script-powershell-example"></a>Parancsfájl PowerShell-példa frissítése
> [!NOTE]
> Ez a frissítési folyamat csak Azure Stack integrált rendszerekre vonatkozik.

Ha az SQL-erőforrás-szolgáltató verzióját 1.1.33.0 vagy korábbi verzióra frissíti, telepítenie kell a AzureRm. BootStrapper és a Azure Stack-modulok adott verzióját a PowerShellben. Ha frissíti az SQL Resource Provider 1.1.47.0 verzióját, ez a lépés kihagyható.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

A következő példa egy emelt szintű PowerShell-konzolról futtatható *UpdateSQLProvider. ps1* parancsfájl használatára mutat be példát. Ügyeljen rá, hogy szükség szerint módosítsa a változó információit és jelszavát:  

```powershell
# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
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

## <a name="next-steps"></a>További lépések

[Az SQL-erőforrás szolgáltatójának karbantartása](azure-stack-sql-resource-provider-maintain.md)
