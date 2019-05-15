---
title: Az Azure Stack MySQL erőforrás-szolgáltató frissítése |} A Microsoft Docs
description: Ismerje meg, hogyan frissítheti az Azure Stack MySQL erőforrás-szolgáltató.
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
ms.date: 03/29/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: a81b1f0dae233adc29437bc88a82e4560fc36f10
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617897"
---
# <a name="update-the-mysql-resource-provider"></a>A MySQL erőforrás-szolgáltató frissítése 

*Vonatkozik: Az Azure Stackkel integrált rendszerek.*

Egy új MySQL erőforrás-szolgáltató adapter előfordulhat, hogy szabadul fel, ha az Azure Stack-buildek frissülnek. A meglévő adapter továbbra is működik, javasoljuk, hogy frissítse a legújabb buildre minél hamarabb. 

A MySQL erőforrás-szolgáltató 1.1.33.0. verzió kiadását kezdve frissítések összesítettek, és nem kell telepíteni a sorrendben, ahol azok jelentek meg; mindaddig, amíg Ön kezdődően 1.1.24.0 verzió vagy újabb verziója. Például ha a MySQL erőforrás-szolgáltató 1.1.24.0 verzióját futtatja, majd frissítheti 1.1.33.0 verzióra, vagy később anélkül, hogy először a 1.1.30.0 verzió telepítése. Tekintse át az elérhető erőforrás-szolgáltató verziói és az Azure Stack támogatottak a verzióját, a verziók listáját [üzembe helyezése az erőforrás-szolgáltatóra vonatkozó Előfeltételek](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Frissíteni az erőforrás-szolgáltatót használ, a **UpdateMySQLProvider.ps1** parancsfájlt. A folyamat hasonlít a folyamat egy erőforrás-szolgáltató telepíthető, az üzembe helyezés Ez a cikk resource provider szakaszában leírtak szerint. A parancsfájl az erőforrás-szolgáltató a letöltés részét képezi. 

 > [!IMPORTANT]
 > Az erőforrás-szolgáltató a frissítés előtt tekintse át a kibocsátási megjegyzéseket, új funkciókat, javításokat és olyan ismert problémákat, amelyek hatással lehetnek a központi telepítés megismeréséhez.

## <a name="update-script-processes"></a>Frissítési parancsfájl folyamatok

A **UpdateMySQLProvider.ps1** parancsfájl egy új virtuális Gépet hoz létre az erőforrás-szolgáltató legújabb kódját, és a beállításokat áttelepíti a régi virtuális gépről az új virtuális gépre. A beállításokat, amelyeket át közé tartozik az adatbázis és az üzemeltetési kiszolgáló adatait, és a szükséges DNS-bejegyzést. 

>[!NOTE]
>Azt javasoljuk, hogy le kell tölteni a legújabb Windows Server 2016 Core lemezképet a Marketplace-kezelés. Ha szeretne telepíteni egy frissítést, elhelyezhet egy **egyetlen** MSU-csomagot a helyi függőségi útvonalát. A parancsfájl futtatása sikertelen lesz, ha egynél több MSU-fájlt ezen a helyen.

A szkript a DeployMySqlProvider.ps1 parancsfájl ismertetett argumentumokkal használatát igényli. A tanúsítvány itt is biztosítanak.  


## <a name="update-script-parameters"></a>Parancsprogram paramétereinek frissítése 
Megadhatja az alábbi paramétereket a parancssorból való futtatásakor a **UpdateMySQLProvider.ps1** PowerShell-parancsfájlt. Ha nem, vagy ha minden paraméter ellenőrzése sikertelen, kéri, hogy adja meg a szükséges paramétereket. 

| Paraméter neve | Leírás | Megjegyzés vagy az alapértelmezett érték | 
| --- | --- | --- | 
| **CloudAdminCredential** | A felhő rendszergazdájához, a kiemelt végponthoz eléréséhez szükséges hitelesítő adatait. | _Kötelező_ | 
| **AzCredential** | Az Azure Stack szolgáltatás-rendszergazdai fiók hitelesítő adatait. Használja ugyanazokat a hitelesítő adatokat az Azure Stack üzembe helyezéséhez használt. | _Kötelező_ | 
| **VMLocalCredential** |Az SQL-erőforrás-szolgáltató virtuális gép helyi rendszergazdai fiókjának hitelesítő adatait. | _Kötelező_ | 
| **PrivilegedEndpoint** | Az IP-cím vagy a kiemelt végponthoz DNS-nevét. |  _Kötelező_ | 
| **AzureEnvironment** | Az Azure-környezethez az Azure Stack üzembe helyezéséhez használt szolgáltatás-rendszergazdai fiókot. Kizárólag az Azure AD központi telepítések esetén szükséges. Támogatott környezeti nevek **AzureCloud**, **AzureUSGovernment**, vagy a China Azure AD-ben való használatakor **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | A tanúsítvány .pfx fájlját a könyvtárban kell elhelyezni. | _Nem kötelező_ (_kötelező_ több csomópont) | 
| **DefaultSSLCertificatePassword** | A .pfx tanúsítvány jelszava. | _Kötelező_ | 
| **MaxRetryCount** | Többször ismételje meg minden művelet, ha sikertelen egy kívánt száma.| 2 | 
| **RetryDuration** | Az időkorlát között eltelő időt másodpercben mérve. | 120 | 
| **Eltávolítás** | Távolítsa el az erőforrás-szolgáltató és az összes társított erőforrást (lásd az alábbi megjegyzések). | Nem | 
| **DebugMode** | Megakadályozza, hogy hiba esetén az automatikus tisztítás. | Nem | 
| **AcceptLicense** | A rendszer kéri, fogadja el a GPL-licenc kihagyja.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Példaszkript frissítése
Az alábbiakban egy példát a a *UpdateMySQLProvider.ps1* parancsfájl, amely futtatható az emelt szintű PowerShell-konzolon. Ügyeljen arra, hogy szükség szerint változtassa meg a változó adatainak és jelszavak:  

> [!NOTE] 
> A frissítési folyamat csak integrált rendszerek vonatkozik. 

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM. 
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access. 
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate. 
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
 
# Change directory to the folder where you extracted the installation files. 
# Then adjust the endpoints. 
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
```  

## <a name="next-steps"></a>További lépések
[MySQL típusú erőforrás-szolgáltató kezelése](azure-stack-mysql-resource-provider-maintain.md)
