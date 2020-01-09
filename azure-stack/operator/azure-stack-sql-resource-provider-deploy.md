---
title: SQL Server erőforrás-szolgáltató üzembe helyezése
titleSuffix: Azure Stack
description: Megtudhatja, hogyan helyezheti üzembe a SQL Server erőforrás-szolgáltatót a Azure Stackon.
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
ms.lastreviewed: 03/18/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: d486eaef122e1a66cca3725743d7772107e55107
ms.sourcegitcommit: 4cd33bcb1bb761a424afd51f511b093543786d76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2019
ms.locfileid: "75325127"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>A SQL Server erőforrás-szolgáltató üzembe helyezése Azure Stack

Az SQL-adatbázisok Azure Stack szolgáltatásként való elérhetővé tétele a Azure Stack SQL Server erőforrás-szolgáltató használatával. Az SQL erőforrás-szolgáltató szolgáltatásként fut egy Windows Server 2016 Server Core virtuális gépen (VM).

> [!IMPORTANT]
> Csak az erőforrás-szolgáltató támogatott az SQL vagy a MySQL-t futtató kiszolgálókon lévő elemek létrehozásához. Az erőforrás-szolgáltató által nem létrehozott gazdagép-kiszolgálón létrehozott elemek nem egyező állapotba kerülhetnek.

## <a name="prerequisites"></a>Előfeltételek

A Azure Stack SQL-erőforrás-szolgáltató üzembe helyezése előtt több előfeltételnek kell megvalósulnia. A követelmények teljesítéséhez hajtsa végre az alábbi lépéseket egy olyan számítógépen, amely hozzáfér a privilegizált végponti virtuális géphez:

- Ha még nem tette meg, [regisztráljon Azure stack](azure-stack-registration.md) az Azure-ban, hogy letöltse az Azure Marketplace-elemeket.

- Telepítse az Azure-t és Azure Stack PowerShell-modulokat azon a rendszeren, amelyen a telepítést futtatni fogja. A rendszernek Windows 10 vagy Windows Server 2016 lemezképnek kell lennie a .NET-futtatókörnyezet legújabb verziójával. Lásd: [a PowerShell telepítése Azure Stackhoz](./azure-stack-powershell-install.md).

- A **Windows server 2016 Datacenter-Server Core** rendszerkép letöltésével adja hozzá a szükséges Windows Server Core virtuális gépet Azure stack Marketplace-hez.

- Töltse le az SQL erőforrás-szolgáltató bináris fájlját, majd futtassa az önálló kivonót a tartalom ideiglenes könyvtárba való kibontásához. Az erőforrás-szolgáltató minimálisan megfelelő Azure Stack buildtel rendelkezik.

  |Minimális Azure Stack-verzió|Az SQL RP verziója|
  |-----|-----|
  |1910-es verzió (1.1910.0.58)|[Az SQL RP verziója 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|
  |1808-es verzió (1.1808.0.97)|[Az SQL RP verziója 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
  |1808-es verzió (1.1808.0.97)|[Az SQL RP verziója 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|  
  |1804-es verzió (1.0.180513.1)|[Az SQL RP verziója 1.1.24.0](https://aka.ms/azurestacksqlrp11240)  
  |     |     |

> [!IMPORTANT]
> Az SQL Resource Provider 1.1.47.0 verziójának telepítése előtt a Azure Stack rendszer frissítése a 1910-es vagy újabb verzióra. A korábbi, nem támogatott Azure Stack verziók SQL Resource Provider verziójának 1.1.47.0 nem működik.

- Győződjön meg arról, hogy a Datacenter-integráció előfeltételei teljesülnek:

    |Előfeltétel|Leírások|
    |-----|-----|
    |A feltételes DNS-továbbítás helyesen van beállítva.|[Azure Stack Datacenter-integráció – DNS](azure-stack-integrate-dns.md)|
    |Az erőforrás-szolgáltatók bejövő portjai nyitva vannak.|[Azure Stack Datacenter-integráció – bejövő portok és protokollok](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |A PKI-tanúsítvány tárgya és SAN beállítása helyesen van beállítva.|[Azure Stack központi telepítés kötelező PKI-előfeltételei](azure-stack-pki-certs.md#mandatory-certificates)<br>[Azure Stack üzembe helyezési Péter tanúsítványának előfeltételei](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Tanúsítványok

_Csak az integrált rendszerek telepítéséhez_. Meg kell adnia az SQL Péter PKI-tanúsítványát, amely a [Azure stack üzembe helyezési PKI követelményeinek](./azure-stack-pki-certs.md#optional-paas-certificates)nem kötelező kihagyása című részben olvasható. Helyezze a. pfx-fájlt a **DependencyFilesLocalPath** paraméter által megadott helyre. Ne adjon meg tanúsítványt a ASDK rendszerekhez.

## <a name="deploy-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató üzembe helyezése

Miután telepítette az összes előfeltételt, futtassa az **DeploySqlProvider. ps1** parancsfájlt az SQL-erőforrás-szolgáltató üzembe helyezéséhez. A DeploySqlProvider. ps1 parancsfájlt a rendszer az SQL-erőforrás-szolgáltató bináris fájljának részeként kibontja, amelyet a Azure Stack saját verziójához töltött le.

 > [!IMPORTANT]
 > Az erőforrás-szolgáltató üzembe helyezése előtt tekintse át a kibocsátási megjegyzéseket, és ismerkedjen meg az új funkciókkal, javításokkal és az üzembe helyezést befolyásoló ismert problémákkal.

Az SQL-erőforrás-szolgáltató üzembe helyezéséhez nyisson meg egy **új** emelt szintű PowerShell-ablakot (ne PowerShell ISE), és váltson arra a könyvtárra, ahová kicsomagolta az SQL Resource Provider bináris fájljait. A már betöltött PowerShell-modulok által okozott lehetséges problémák elkerülése érdekében javasoljuk, hogy használjon egy új PowerShell-ablakot.

Futtassa a DeploySqlProvider. ps1 parancsfájlt, amely a következő feladatokat hajtja végre:

- Feltölti a tanúsítványokat és egyéb összetevőket egy Storage-fiókba Azure Stackon.
- Közzéteszi a katalógus-csomagokat, így az SQL-adatbázisok üzembe helyezhetők a gyűjtemény használatával.
- Közzétesz egy gyűjtemény-csomagot az üzemeltetési kiszolgálók telepítéséhez.
- Üzembe helyez egy virtuális gépet a letöltött Windows Server 2016 Core lemezkép használatával, majd telepíti az SQL-erőforrás-szolgáltatót.
- Egy helyi DNS-rekordot regisztrál, amely az erőforrás-szolgáltató virtuális géphez van társítva.
- Regisztrálja az erőforrás-szolgáltatót a kezelő fiók helyi Azure Resource Manager.

> [!NOTE]
> Az SQL erőforrás-szolgáltató központi telepítésének indításakor létrejön a **System. local. sqladapter** erőforráscsoport. A szükséges központi telepítések elvégzése akár 75 percet is igénybe vehet.

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider. ps1 paraméterek

A következő paramétereket adhatja meg a parancssorból. Ha nem, vagy ha valamelyik paraméter ellenőrzése sikertelen, a rendszer felszólítja a szükséges paraméterek megadására.

| Paraméter neve | Leírás | Megjegyzés vagy alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A rendszerjogosultságú végpont eléréséhez szükséges felhőalapú rendszergazda hitelesítő adatai. | _Kötelező_ |
| **AzCredential** | A Azure Stack szolgáltatás rendszergazdai fiókjának hitelesítő adatai. Használja ugyanazokat a hitelesítő adatokat, amelyeket a Azure Stack telepítéséhez használt. | _Kötelező_ |
| **VMLocalCredential** | Az SQL Resource Provider virtuális gép helyi rendszergazdai fiókjának hitelesítő adatai. | _Kötelező_ |
| **PrivilegedEndpoint** | Az emelt szintű végpont IP-címe vagy DNS-neve. |  _Kötelező_ |
| **AzureEnvironment** | A Azure Stack telepítéséhez használt szolgáltatás-rendszergazdai fiók Azure-környezete. Csak az Azure AD-telepítésekhez szükséges. A támogatott környezeti nevek a következők: **AzureCloud**, **AzureUSGovernment**, vagy kínai Azure Active Directory, **AzureChinaCloud**használatával. | AzureCloud |
| **DependencyFilesLocalPath** | Csak az integrált rendszerek esetében a tanúsítvány. pfx fájlját ebbe a könyvtárba kell helyezni. Itt egy Windows Update MSU-csomagot is másolhat. | Nem _kötelező_ (az integrált rendszerek esetében_kötelező_ ) |
| **DefaultSSLCertificatePassword** | A. pfx-tanúsítvány jelszava. | _Kötelező_ |
| **Maxretrycount csak** | Az egyes műveletek újrapróbálkozási időpontjának száma, ha hiba történt.| 2 |
| **RetryDuration** | Az újrapróbálkozások közötti időtúllépési időköz (másodpercben). | 120 |
| **Eltávolítás** | Eltávolítja az erőforrás-szolgáltatót és az összes kapcsolódó erőforrást (lásd a következő megjegyzéseket). | Nem |
| **DebugMode** | Megakadályozza a hibák automatikus törlését. | Nem |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Az SQL-erőforrás-szolgáltató üzembe helyezése egyéni parancsfájl használatával

Ha az SQL-erőforrás-szolgáltató 1.1.33.0 vagy korábbi verzióját telepíti, telepítenie kell a AzureRm. BootStrapper és a Azure Stack-modulok adott verzióját a PowerShellben. Ha az SQL Resource Provider 1.1.47.0 verzióját telepíti, ez a lépés kihagyható.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell
Install-Module -Name AzureRm.BootStrapper -RequiredVersion 0.5.0 -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

Az erőforrás-szolgáltató üzembe helyezése során felmerülő manuális konfiguráció elkerülése érdekében testreszabhatja az alábbi parancsfájlt.  

Szükség szerint módosítsa az alapértelmezett fiók adatait és a jelszavakat a Azure Stack központi telepítéshez.

```powershell
# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS VMs
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local admin account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory folder where you extracted the installation files. Don't provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Az erőforrás-szolgáltató telepítési parancsfájljának befejeződése után frissítse a böngészőt, és győződjön meg arról, hogy a legújabb frissítések láthatók.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>A központi telepítés ellenőrzése a Azure Stack portál használatával

Az alábbi lépéseket követve ellenőrizheti, hogy az SQL-erőforrás szolgáltatója sikeresen telepítve van-e.

1. Jelentkezzen be a felügyeleti portálra szolgáltatás-rendszergazdaként.
2. Válassza az **Erőforráscsoportok** lehetőséget.
3. Válassza ki a **System.\<helyet\>. sqladapter** erőforráscsoportot.
4. Az erőforráscsoport-áttekintés összefoglaló lapján nem lehetnek sikertelen központi telepítések.

    ![Az SQL-erőforrás-szolgáltató üzembe helyezésének ellenőrzése a Azure Stack felügyeleti portálon](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

5. Végül a felügyeleti portálon válassza a **Virtual Machines (virtuális gépek** ) lehetőséget annak ellenőrzéséhez, hogy az SQL Resource Provider virtuális gép sikeresen létrejött-e, és fut-e.

## <a name="next-steps"></a>Következő lépések

[Üzemeltetési kiszolgálók hozzáadása](azure-stack-sql-resource-provider-hosting-servers.md)
