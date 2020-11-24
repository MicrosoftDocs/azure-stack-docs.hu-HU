---
title: A MySQL erőforrás-szolgáltató frissítése Azure Stack központban
description: Ismerje meg, hogyan frissítheti az Azure Stack hub MySQL erőforrás-szolgáltatót az Azure Stack hub-ban.
author: bryanla
ms.topic: article
ms.date: 9/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: 61149b7eb89908b641fd995b10b22e3d05a5fb8d
ms.sourcegitcommit: af4374755cb4875a7cbed405b821f5703fa1c8cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95812700"
---
# <a name="update-the-mysql-resource-provider-in-azure-stack-hub"></a>A MySQL erőforrás-szolgáltató frissítése Azure Stack központban

> [!IMPORTANT]
> Az erőforrás-szolgáltató frissítése előtt tekintse át a kibocsátási megjegyzéseket, és ismerkedjen meg az új funkciókkal, javításokkal és az üzembe helyezést befolyásoló ismert problémákkal. A kibocsátási megjegyzések az erőforrás-szolgáltatóhoz szükséges minimális Azure Stack hub-verziót is meghatározzák.

Előfordulhat, hogy a Azure Stack hub-buildek frissítésekor új MySQL erőforrás-szolgáltatói adapter jelenik meg. Míg a meglévő adapter továbbra is működik, javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb buildre.

  |Támogatott Azure Stack hub-verzió|MySQL RP-verzió|Az a Windows Server, amelyre az RP szolgáltatás fut
  |-----|-----|-----|
  |2008, 2005|[MySQL RP-verzió 1.1.93.0](https://aka.ms/azshmysqlrp11930)|Microsoft AzureStack-bővítmény csak belső Windows Server-kiszolgálón
  |2005, 2002, 1910|[MySQL RP-verzió 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|Windows Server 2016 Datacenter – Server Core|
  |1908|[MySQL RP-verzió 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|Windows Server 2016 Datacenter – Server Core|
  |     |     |     |

A MySQL erőforrás-szolgáltató frissítése kumulatív. Régi verzióról történő frissítés esetén közvetlenül a legújabb verzióra frissíthet. 

Az erőforrás-szolgáltató frissítéséhez használja a **UpdateMySQLProvider.ps1** szkriptet. Helyi rendszergazdai jogokkal rendelkező szolgáltatásfiók használata, amely az előfizetés **tulajdonosa** . A Frissítési parancsfájl tartalmazza az erőforrás-szolgáltató letöltését. 

A frissítési folyamat hasonló az [erőforrás-szolgáltató üzembe helyezéséhez](./azure-stack-mysql-resource-provider-deploy.md)használt folyamathoz. A Frissítési parancsfájl ugyanazokat az argumentumokat használja, mint a DeployMySqlProvider.ps1 szkript, és meg kell adnia a tanúsítvány adatait.

## <a name="update-script-processes"></a>Parancsfájl-folyamatok frissítése

A **UpdateMySQLProvider.ps1** szkript létrehoz egy új virtuális GÉPET (VM) a legújabb operációsrendszer-lemezképpel, telepíti a legújabb erőforrás-szolgáltatói kódot, és áttelepíti a beállításokat a régi erőforrás-szolgáltatóról az új erőforrás-szolgáltatóra.

>[!NOTE]
>Javasoljuk, hogy töltse le a legújabb Windows Server 2016 Core rendszerképet vagy a Microsoft AzureStack bővítmény RP Windows Server-lemezképét a piactér-felügyeletből. Ha frissítést kell telepítenie, **egyetlen** msu-csomagot is elhelyezhet a helyi függőségi útvonalon. A szkript sikertelen lesz, ha több MSU-fájl is van ezen a helyen.

Miután a *UpdateMySQLProvider.ps1* szkript létrehoz egy új virtuális gépet, a parancsfájl áttelepíti a következő beállításokat a régi erőforrás-szolgáltató virtuális gépről:

* adatbázis-információk
* üzemeltetési kiszolgáló adatai
* szükséges DNS-rekord

## <a name="update-script-parameters"></a>Parancsfájl paramétereinek frissítése 
A **UpdateMySQLProvider.ps1** PowerShell-parancsfájl futtatásakor adja meg a következő paramétereket a parancssorból. Ha nem, vagy ha valamelyik paraméter ellenőrzése sikertelen, a rendszer felszólítja a szükséges paraméterek megadására.

| Paraméter neve | Leírás | Megjegyzés vagy alapértelmezett érték | 
| --- | --- | --- | 
| **CloudAdminCredential** | A rendszerjogosultságú végpont eléréséhez szükséges felhőalapú rendszergazda hitelesítő adatai. | _Kötelező_ | 
| **AzCredential** | Az Azure Stack hub szolgáltatás rendszergazdai fiókjának hitelesítő adatai. Használja ugyanazokat a hitelesítő adatokat, amelyeket az Azure Stack hub üzembe helyezéséhez használt. A szkript sikertelen lesz, ha a AzCredential használt fiók többtényezős hitelesítést (MFA) igényel. | _Kötelező_ | 
| **VMLocalCredential** |Az SQL Resource Provider virtuális gép helyi rendszergazdai fiókjának hitelesítő adatai. | _Kötelező_ | 
| **PrivilegedEndpoint** | Az emelt szintű végpont IP-címe vagy DNS-neve. |  _Kötelező_ | 
| **AzureEnvironment** | Az Azure Stack hub üzembe helyezéséhez használt szolgáltatás-rendszergazdai fiók Azure-környezete. Csak az Azure AD-telepítésekhez szükséges. A támogatott környezeti nevek: **AzureCloud**, **AzureUSGovernment**, illetve kínai Azure ad-t, **AzureChinaCloud**-t használnak. | AzureCloud |
| **DependencyFilesLocalPath** | A Certificate. pfx fájlt is ebbe a könyvtárba kell helyezni. | Nem _kötelező_ (több csomópont esetében _kötelező_ ) | 
| **DefaultSSLCertificatePassword** | A. pfx-tanúsítvány jelszava. | _Kötelező_ | 
| **MaxRetryCount** | Az egyes műveletek újrapróbálkozási időpontjának száma, ha hiba történt.| 2 | 
| **RetryDuration** | Az újrapróbálkozások közötti időtúllépési időköz (másodpercben). | 120 | 
| **Eltávolítás** | Távolítsa el az erőforrás-szolgáltatót és az összes kapcsolódó erőforrást (lásd a következő megjegyzéseket). | No | 
| **DebugMode** | Megakadályozza a hibák automatikus törlését. | No | 
| **AcceptLicense** | Kihagyja a kérést, hogy elfogadja a GPL-licencet.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Parancsfájl frissítése – példa

Ha a MySQL erőforrás-szolgáltató verzióját 1.1.33.0 vagy korábbi verzióra frissíti, telepítenie kell a AzureRm. BootStrapper és a Azure Stack hub-modulok adott verzióját a PowerShell-ben. 

Ha a MySQL erőforrás-szolgáltatót a 1.1.47.0 vagy újabb verzióra frissíti, akkor kihagyhatja ezt a lépést. Az üzembe helyezési parancsfájl automatikusan letölti és telepíti a szükséges PowerShell-modulokat a következő elérési úthoz: C:\Program Files\SqlMySqlPsh. 

>[!NOTE]
>Ha a "C:\Program Files\SqlMySqlPsh" mappa már létezik a letöltött PowerShell-modullal, akkor a Frissítési parancsfájl futtatása előtt ajánlott törölni ezt a mappát. Ezzel gondoskodhat arról, hogy a PowerShell-modul megfelelő verziója le legyen töltve és használatban legyen.

```powershell 
# Run the following scripts when updating to version 1.1.33.0 only.
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> A leválasztott forgatókönyvben le kell töltenie a szükséges PowerShell-modulokat, és manuálisan kell regisztrálnia az adattárat előfeltételként. További információt a [MySQL erőforrás-szolgáltató üzembe helyezése című](azure-stack-mysql-resource-provider-deploy.md) témakörben találhat.

Az alábbi példa azt a *UpdateMySQLProvider.ps1* szkriptet mutatja be, amelyet egy emelt szintű PowerShell-konzolról futtathat. Ügyeljen rá, hogy szükség szerint módosítsa a változó információit és jelszavát:

```powershell 
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 

# For version 1.1.47.0 or later, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath 

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
.$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
-VMLocalCredential $vmLocalAdminCreds `
-CloudAdminCredential $cloudAdminCreds `
-PrivilegedEndpoint $privilegedEndpoint `
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass `
-DependencyFilesLocalPath $tempDir\cert `
-AcceptLicense
```  

Az erőforrás-szolgáltató frissítési parancsfájljának befejeződése után zárd be a jelenlegi PowerShell-munkamenetet.

## <a name="next-steps"></a>További lépések
[A MySQL erőforrás-szolgáltató fenntartása](azure-stack-mysql-resource-provider-maintain.md)
