---
title: A MySQL erőforrás-szolgáltató frissítése Azure Stack hub-ban | Microsoft Docs
description: Ismerje meg, hogyan frissítheti az Azure Stack hub MySQL erőforrás-szolgáltatót az Azure Stack hub-ban.
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
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: a230a07c62f415c990b4cccf21cd5c842782d41b
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534634"
---
# <a name="update-the-mysql-resource-provider-in-azure-stack-hub"></a>A MySQL erőforrás-szolgáltató frissítése Azure Stack központban

Előfordulhat, hogy a Azure Stack hub-buildek frissítésekor új MySQL erőforrás-szolgáltatói adapter jelenik meg. Míg a meglévő adapter továbbra is működik, javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb buildre.

A MySQL erőforrás-szolgáltató 1.1.33.0 kiadásával kezdődően a frissítések összegző jellegűek, és nem kell azokat a kiadási sorrendben telepíteni, amíg a 1.1.24.0 vagy újabb verzióról indul. Ha például a MySQL erőforrás-szolgáltató 1.1.24.0 verzióját futtatja, akkor a 1.1.33.0 vagy újabb verzióra is frissíthet, anélkül, hogy először telepítenie kell a 1.1.30.0-verziót. Az elérhető erőforrás-szolgáltatói verziók, valamint az Azure Stack hub támogatott verziójának áttekintéséhez tekintse meg a verziók listáját az [erőforrás-szolgáltató előfeltételeinek telepítése](./azure-stack-mysql-resource-provider-deploy.md#prerequisites)című cikkben.

Az erőforrás-szolgáltató frissítéséhez használja a **UpdateMySQLProvider. ps1** parancsfájlt. A folyamat hasonló az erőforrás-szolgáltató telepítéséhez használt folyamathoz, a jelen cikk erőforrás-szolgáltató üzembe helyezése című szakaszában leírtak szerint. A parancsfájl az erőforrás-szolgáltató letöltését tartalmazza. 

 > [!IMPORTANT]
 > Az erőforrás-szolgáltató frissítése előtt tekintse át a kibocsátási megjegyzéseket, és ismerkedjen meg az új funkciókkal, javításokkal és az üzembe helyezést befolyásoló ismert problémákkal.

## <a name="update-script-processes"></a>Parancsfájl-folyamatok frissítése

A **UpdateMySQLProvider. ps1** szkript létrehoz egy új virtuális GÉPET (VM) a legújabb erőforrás-szolgáltatói kóddal, és áttelepíti a beállításokat a régi virtuális gépről az új virtuális gépre. Az áttelepíteni kívánt beállítások közé tartozik az adatbázis és az üzemeltetési kiszolgáló adatai és a szükséges DNS-rekord.

>[!NOTE]
>Javasoljuk, hogy töltse le a legújabb Windows Server 2016 Core rendszerképet a piactér-felügyeletből. Ha frissítést kell telepítenie, **egyetlen** msu-csomagot is elhelyezhet a helyi függőségi útvonalon. A szkript sikertelen lesz, ha több MSU-fájl is van ezen a helyen.

A parancsfájlnak ugyanazokat az argumentumokat kell használnia, amelyek a DeployMySqlProvider. ps1 parancsfájlhoz vannak leírva. Itt is megadhatja a tanúsítványt.  


## <a name="update-script-parameters"></a>Parancsfájl paramétereinek frissítése 
A **UpdateMySQLProvider. ps1** PowerShell-parancsfájl futtatásakor adja meg a következő paramétereket a parancssorból. Ha nem, vagy ha valamelyik paraméter ellenőrzése sikertelen, a rendszer felszólítja a szükséges paraméterek megadására.

| Paraméter neve | Leírás | Megjegyzés vagy alapértelmezett érték | 
| --- | --- | --- | 
| **CloudAdminCredential** | A rendszerjogosultságú végpont eléréséhez szükséges felhőalapú rendszergazda hitelesítő adatai. | _Kötelező_ | 
| **AzCredential** | Az Azure Stack hub szolgáltatás rendszergazdai fiókjának hitelesítő adatai. Ugyanazokat a hitelesítő adatokat használja, mint amelyeket az Azure Stack hub telepítéséhez használt. | _Kötelező_ | 
| **VMLocalCredential** |Az SQL Resource Provider virtuális gép helyi rendszergazdai fiókjának hitelesítő adatai. | _Kötelező_ | 
| **PrivilegedEndpoint** | Az emelt szintű végpont IP-címe vagy DNS-neve. |  _Kötelező_ | 
| **AzureEnvironment** | Az Azure Stack hub üzembe helyezéséhez használt szolgáltatás-rendszergazdai fiók Azure-környezete. Csak az Azure AD-telepítésekhez szükséges. A támogatott környezeti nevek: **AzureCloud**, **AzureUSGovernment**, illetve kínai Azure ad-t, **AzureChinaCloud**-t használnak. | AzureCloud |
| **DependencyFilesLocalPath** | A Certificate. pfx fájlt is ebbe a könyvtárba kell helyezni. | Nem _kötelező_ (több csomópont esetében_kötelező_ ) | 
| **DefaultSSLCertificatePassword** | A. pfx-tanúsítvány jelszava. | _Kötelező_ | 
| **Maxretrycount csak** | Az egyes műveletek újrapróbálkozási időpontjának száma, ha hiba történt.| 2 | 
| **RetryDuration** | Az újrapróbálkozások közötti időtúllépési időköz (másodpercben). | 120 | 
| **Eltávolítás** | Távolítsa el az erőforrás-szolgáltatót és az összes kapcsolódó erőforrást (lásd a következő megjegyzéseket). | Nem | 
| **DebugMode** | Megakadályozza a hibák automatikus törlését. | Nem | 
| **AcceptLicense** | Kihagyja a kérést, hogy elfogadja a GPL-licencet.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Parancsfájl frissítése – példa

> [!NOTE] 
> A frissítési folyamat csak az integrált rendszerekre vonatkozik.

Ha a MySQL erőforrás-szolgáltató verzióját 1.1.33.0 vagy korábbi verzióra frissíti, telepítenie kell a AzureRm. BootStrapper és a Azure Stack hub-modulok adott verzióját a PowerShell-ben. Ha a MySQL erőforrás-szolgáltatót a 1.1.47.0 verzióra frissíti, a telepítési parancsfájl automatikusan letölti és telepíti a szükséges PowerShell-modulokat a C:\Program Files\SqlMySqlPsh.

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> A leválasztott forgatókönyvben le kell töltenie a szükséges PowerShell-modulokat, és manuálisan kell regisztrálnia az adattárat előfeltételként. További információt a [MySQL erőforrás-szolgáltató üzembe helyezése című](azure-stack-mysql-resource-provider-deploy.md) témakörben találhat.

Az alábbi példa azt a *UpdateMySQLProvider. ps1* parancsfájlt mutatja, amelyet egy emelt szintű PowerShell-konzolról lehet futtatni. Ügyeljen rá, hogy szükség szerint módosítsa a változó információit és jelszavát:

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

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
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

## <a name="next-steps"></a>Következő lépések
[A MySQL erőforrás-szolgáltató fenntartása](azure-stack-mysql-resource-provider-maintain.md)
