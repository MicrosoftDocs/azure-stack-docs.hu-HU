---
title: A Azure Stack MySQL erőforrás-szolgáltató frissítése | Microsoft Docs
description: Ismerje meg, hogyan frissítheti a Azure Stack MySQL erőforrás-szolgáltatót.
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
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 6513138226bf60d37f7f2f541d8d00dcf8d87073
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68417436"
---
# <a name="update-the-mysql-resource-provider"></a>A MySQL erőforrás-szolgáltató frissítése 

*Vonatkozik: Azure Stack integrált rendszerek.*

A Azure Stack-buildek frissítésekor új MySQL erőforrás-szolgáltatói adaptert lehet felszabadítani. Míg a meglévő adapter továbbra is működik, javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb buildre. 

A MySQL erőforrás-szolgáltató 1.1.33.0 kiadásával kezdődően a frissítések kumulatív jellegűek, és nem kell azokat a kiadásuk sorrendjében telepíteni; a 1.1.24.0 vagy újabb verziótól kezdődően. Ha például a MySQL erőforrás-szolgáltató 1.1.24.0 verzióját futtatja, akkor a 1.1.33.0 vagy újabb verzióra is frissíthet, anélkül, hogy először telepítenie kell a 1.1.30.0-verziót. Az elérhető erőforrás-szolgáltatói verziók, valamint a támogatott Azure Stack verziójának áttekintéséhez tekintse meg a verziók listáját az [erőforrás-szolgáltatói előfeltételek üzembe helyezése](./azure-stack-mysql-resource-provider-deploy.md#prerequisites)című témakörben.

Az erőforrás-szolgáltató frissítéséhez használja a **UpdateMySQLProvider. ps1** parancsfájlt. A folyamat hasonló az erőforrás-szolgáltató telepítéséhez használt folyamathoz, a jelen cikk erőforrás-szolgáltató üzembe helyezése című szakaszában leírtak szerint. A parancsfájl az erőforrás-szolgáltató letöltését tartalmazza. 

 > [!IMPORTANT]
 > Az erőforrás-szolgáltató frissítése előtt tekintse át a kibocsátási megjegyzéseket, és ismerkedjen meg az új funkciókkal, javításokkal és az üzembe helyezést befolyásoló ismert problémákkal.

## <a name="update-script-processes"></a>Parancsfájl-folyamatok frissítése

A **UpdateMySQLProvider. ps1** szkript létrehoz egy új virtuális gépet a legújabb erőforrás-szolgáltatói kóddal, és áttelepíti a beállításokat a régi virtuális gépről az új virtuális gépre. Az áttelepíteni kívánt beállítások közé tartozik az adatbázis és az üzemeltetési kiszolgáló adatai, valamint a szükséges DNS-rekord. 

>[!NOTE]
>Javasoljuk, hogy töltse le a legújabb Windows Server 2016 Core rendszerképet a piactér-felügyeletből. Ha frissítést kell telepítenie, **egyetlen** msu-csomagot is elhelyezhet a helyi függőségi útvonalon. A szkript sikertelen lesz, ha több MSU-fájl is van ezen a helyen.

A parancsfájlnak ugyanazokat az argumentumokat kell használnia, amelyek a DeployMySqlProvider. ps1 parancsfájlhoz vannak leírva. Itt is megadhatja a tanúsítványt.  


## <a name="update-script-parameters"></a>Parancsfájl paramétereinek frissítése 
A **UpdateMySQLProvider. ps1** PowerShell-parancsfájl futtatásakor a parancssorban megadhatja a következő paramétereket. Ha nem, vagy ha valamelyik paraméter ellenőrzése sikertelen, a rendszer felszólítja a szükséges paraméterek megadására. 

| Paraméter neve | Leírás | Megjegyzés vagy alapértelmezett érték | 
| --- | --- | --- | 
| **CloudAdminCredential** | A rendszerjogosultságú végpont eléréséhez szükséges hitelesítő adatok a felhő rendszergazdájához. | _Kötelező_ | 
| **AzCredential** | A Azure Stack szolgáltatás rendszergazdai fiókjának hitelesítő adatai. Ugyanazokat a hitelesítő adatokat használja, mint amelyeket a Azure Stack telepítéséhez használt. | _Kötelező_ | 
| **VMLocalCredential** |Az SQL Resource Provider virtuális gép helyi rendszergazdai fiókjának hitelesítő adatai. | _Kötelező_ | 
| **PrivilegedEndpoint** | Az emelt szintű végpont IP-címe vagy DNS-neve. |  _Kötelező_ | 
| **AzureEnvironment** | Az Azure Stack telepítéséhez használt szolgáltatás-rendszergazdai fiók Azure-környezete. Csak az Azure AD-telepítésekhez szükséges. A támogatott környezeti nevek: **AzureCloud**, **AzureUSGovernment**, illetve kínai Azure ad-t, **AzureChinaCloud**-t használnak. | AzureCloud |
| **DependencyFilesLocalPath** | A Certificate. pfx fájlt is ebbe a könyvtárba kell helyezni. | Nem _kötelező_ (többcsomópontos használatra_kötelező_ ) | 
| **DefaultSSLCertificatePassword** | A. pfx-tanúsítvány jelszava. | _Kötelező_ | 
| **MaxRetryCount** | Az egyes műveletek újrapróbálkozásának száma, ha hiba történt.| 2 | 
| **RetryDuration** | Az újrapróbálkozások közötti időtúllépési időköz (másodpercben). | 120 | 
| **Eltávolítás** | Távolítsa el az erőforrás-szolgáltatót és az összes kapcsolódó erőforrást (lásd a következő megjegyzéseket). | Nem | 
| **DebugMode** | Megakadályozza a hibák automatikus törlését. | Nem | 
| **AcceptLicense** | Kihagyja a kérést, hogy elfogadja a GPL-licencet.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Parancsfájl frissítése – példa
A következő példa egy emelt szintű PowerShell-konzolról futtatható *UpdateMySQLProvider. ps1* parancsfájl használatára mutat be példát. Ügyeljen rá, hogy szükség szerint módosítsa a változó információit és jelszavát:  

> [!NOTE] 
> A frissítési folyamat csak az integrált rendszerekre vonatkozik. 

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
[A MySQL erőforrás-szolgáltató fenntartása](azure-stack-mysql-resource-provider-maintain.md)
