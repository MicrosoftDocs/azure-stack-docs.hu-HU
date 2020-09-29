---
title: SQL Server erőforrás-szolgáltató üzembe helyezése
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan helyezheti üzembe a SQL Server erőforrás-szolgáltatót Azure Stack hub-on.
author: bryanla
ms.topic: article
ms.date: 10/02/2019
ms.lastreviewed: 03/18/2019
ms.author: bryanla
ms.reviewer: xiao
ms.openlocfilehash: adc2288d8886c5b952f26da4798fccd731738733
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946402"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack-hub"></a>A SQL Server erőforrás-szolgáltató üzembe helyezése Azure Stack központban

Az Azure Stack hub SQL Server erőforrás-szolgáltató segítségével elérhetővé teheti az SQL-adatbázisokat Azure Stack hub-szolgáltatásként. Az SQL erőforrás-szolgáltató szolgáltatásként fut egy Windows Server 2016 Server Core virtuális gépen (VM).

> [!IMPORTANT]
> Csak az erőforrás-szolgáltató támogatott az SQL vagy a MySQL-t futtató kiszolgálókon lévő elemek létrehozásához. Az erőforrás-szolgáltató által nem létrehozott gazdagép-kiszolgálón létrehozott elemek nem egyező állapotba kerülhetnek.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack hub SQL erőforrás-szolgáltató üzembe helyezése előtt több előfeltételnek kell teljesülnie. A követelmények teljesítéséhez hajtsa végre az alábbi lépéseket egy olyan számítógépen, amely hozzáfér a privilegizált végponti virtuális géphez:

- Ha még nem tette meg, [regisztráljon Azure stack hubot](azure-stack-registration.md) az Azure-ban, hogy letöltse az Azure Marketplace-elemeket.

- A **Windows server 2016 Datacenter-Server Core** rendszerkép letöltésével adja hozzá a szükséges Windows Server Core virtuális gépet Azure stack hub Marketplace-hez.

- Töltse le az SQL Resource Provider bináris verziója támogatott verzióját az alábbi verzió-hozzárendelési táblázat szerint. Futtassa az önálló kivonót a letöltött tartalmak ideiglenes könyvtárba való kinyeréséhez. 

  |Támogatott Azure Stack hub-verzió|Az SQL RP verziója|
  |-----|-----|
  |2005, 2002, 1910|[Az SQL RP verziója 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|
  |1908|[Az SQL RP verziója 1.1.33.0](https://aka.ms/azurestacksqlrp11330)| 
  |     |     |

- Győződjön meg arról, hogy a Datacenter-integráció előfeltételei teljesülnek:

    |Előfeltétel|Referencia|
    |-----|-----|
    |A feltételes DNS-továbbítás helyesen van beállítva.|[Azure Stack hub Datacenter-integráció – DNS](azure-stack-integrate-dns.md)|
    |Az erőforrás-szolgáltatók bejövő portjai nyitva vannak.|[Azure Stack hub Datacenter-integráció – bejövő portok és protokollok](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |A PKI-tanúsítvány tárgya és SAN beállítása helyesen van beállítva.|[Azure Stack hub üzembe helyezése kötelező PKI-előfeltételek](azure-stack-pki-certs.md)<br>[A Azure Stack hub üzembe helyezése – az összes tanúsítvány előfeltételei](azure-stack-pki-certs.md)|
    |     |     |

Leválasztott forgatókönyv esetén végezze el a következő lépéseket a szükséges PowerShell-modulok letöltéséhez és az adattár manuális regisztrálásához.

1. Jelentkezzen be egy internetkapcsolattal rendelkező számítógépre, és használja a következő parancsfájlokat a PowerShell-modulok letöltéséhez.

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop

# path to save the packages, c:\temp\azs1.6.0 as an example here
$Path = "c:\temp\azs1.6.0"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.2
```

2. Ezután másolja a letöltött csomagokat egy USB-eszközre.

3. Jelentkezzen be a kapcsolat nélküli munkaállomásra, és másolja a csomagokat az USB-eszközről a munkaállomás egyik helyére.

4. Helyi tárházként regisztrálja ezt a helyet.

```powershell
# requires -Version 5
# requires -RunAsAdministrator
# requires -Module PowerShellGet
# requires -Module PackageManagement

$SourceLocation = "C:\temp\azs1.6.0"
$RepoName = "azs1.6.0"

Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

New-Item -Path $env:ProgramFiles -name "SqlMySqlPsh" -ItemType "Directory"
```

### <a name="certificates"></a>Tanúsítványok

_Csak az integrált rendszerek telepítéséhez_. Meg kell adnia az SQL Péter PKI-tanúsítványát, amely az [Azure stack hub központi telepítésének PKI-követelményeinek](./azure-stack-pki-certs.md)opcionális, nem kötelezően megadandó tanúsítványok című szakaszában található. Helyezze a. pfx-fájlt a **DependencyFilesLocalPath** paraméter által megadott helyre. Ne adjon meg tanúsítványt a ASDK rendszerekhez.

## <a name="deploy-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató üzembe helyezése

Miután telepítette az összes előfeltételt, futtassa a **DeploySqlProvider.ps1** parancsfájlt egy olyan számítógépről, amely hozzáférhet mind a Azure stack hub rendszergazdai Azure Resource Management-végponthoz, mind a privilegizált végponthoz az SQL-erőforrás-szolgáltató üzembe helyezéséhez. A DeploySqlProvider.ps1 szkriptet az SQL-erőforrás-szolgáltató bináris fájljának részeként Kinyeri a rendszer, amelyet az Azure Stack hub adott verziójához töltött le.

 > [!IMPORTANT]
 > Az erőforrás-szolgáltató üzembe helyezése előtt tekintse át a kibocsátási megjegyzéseket, és ismerkedjen meg az új funkciókkal, javításokkal és az üzembe helyezést befolyásoló ismert problémákkal.

Az SQL-erőforrás-szolgáltató üzembe helyezéséhez nyisson meg egy **új** emelt szintű PowerShell-ablakot (ne PowerShell ISE), és váltson arra a könyvtárra, ahová kicsomagolta az SQL Resource Provider bináris fájljait. 

> [!IMPORTANT]
> A már betöltött PowerShell-modulok által okozott lehetséges problémák elkerülése érdekében javasoljuk, hogy használjon egy új PowerShell-ablakot. Vagy a Clear-azurermcontext használatával törölheti is a gyorsítótárat a Frissítési parancsfájl futtatása előtt.

Futtassa a DeploySqlProvider.ps1 parancsfájlt, amely a következő feladatokat hajtja végre:

- Feltölti a tanúsítványokat és egyéb összetevőket egy Azure Stack hub Storage-fiókjába.
- Közzéteszi a katalógus-csomagokat, így az SQL-adatbázisok üzembe helyezhetők a gyűjtemény használatával.
- Közzétesz egy gyűjtemény-csomagot az üzemeltetési kiszolgálók telepítéséhez.
- Üzembe helyez egy virtuális gépet a letöltött Windows Server 2016 Core lemezkép használatával, majd telepíti az SQL-erőforrás-szolgáltatót.
- Egy helyi DNS-rekordot regisztrál, amely az erőforrás-szolgáltató virtuális géphez van társítva.
- Regisztrálja az erőforrás-szolgáltatót a kezelő fiók helyi Azure Resource Manager.

> [!NOTE]
> Az SQL erőforrás-szolgáltató központi telepítésének indításakor létrejön a **System. local. sqladapter** erőforráscsoport. A szükséges központi telepítések elvégzése akár 75 percet is igénybe vehet. Ne helyezzen más erőforrásokat a **System. local. sqladapter** erőforráscsoporthoz.

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 paraméterek

A következő paramétereket adhatja meg a parancssorból. Ha nem, vagy ha valamelyik paraméter ellenőrzése sikertelen, a rendszer felszólítja a szükséges paraméterek megadására.

| Paraméter neve | Leírás | Megjegyzés vagy alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A rendszerjogosultságú végpont eléréséhez szükséges felhőalapú rendszergazda hitelesítő adatai. | _Kötelező_ |
| **AzCredential** | Az Azure Stack hub szolgáltatás rendszergazdai fiókjának hitelesítő adatai. Használja ugyanazokat a hitelesítő adatokat, amelyeket az Azure Stack hub üzembe helyezéséhez használt. A szkript sikertelen lesz, ha a AzCredential használt fiók többtényezős hitelesítést (MFA) igényel.| _Kötelező_ |
| **VMLocalCredential** | Az SQL Resource Provider virtuális gép helyi rendszergazdai fiókjának hitelesítő adatai. | _Kötelező_ |
| **PrivilegedEndpoint** | Az emelt szintű végpont IP-címe vagy DNS-neve. |  _Kötelező_ |
| **AzureEnvironment** | Az Azure Stack hub üzembe helyezéséhez használt szolgáltatás-rendszergazdai fiók Azure-környezete. Csak az Azure AD-telepítésekhez szükséges. A támogatott környezeti nevek a következők: **AzureCloud**, **AzureUSGovernment**, vagy kínai Azure Active Directory, **AzureChinaCloud**használatával. | AzureCloud |
| **DependencyFilesLocalPath** | Csak az integrált rendszerek esetében a tanúsítvány. pfx fájlját ebbe a könyvtárba kell helyezni. Itt egy Windows Update MSU-csomagot is másolhat. | Nem _kötelező_ (az integrált rendszerek esetében_kötelező_ ) |
| **DefaultSSLCertificatePassword** | A. pfx-tanúsítvány jelszava. | _Kötelező_ |
| **MaxRetryCount** | Az egyes műveletek újrapróbálkozási időpontjának száma, ha hiba történt.| 2 |
| **RetryDuration** | Az újrapróbálkozások közötti időtúllépési időköz (másodpercben). | 120 |
| **Eltávolítása** | Eltávolítja az erőforrás-szolgáltatót és az összes kapcsolódó erőforrást (lásd a következő megjegyzéseket). | No |
| **DebugMode** | Megakadályozza a hibák automatikus törlését. | No |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Az SQL-erőforrás-szolgáltató üzembe helyezése egyéni parancsfájl használatával

Ha az SQL-erőforrás-szolgáltató 1.1.33.0 vagy korábbi verzióját telepíti, telepítenie kell a AzureRm. BootStrapper és a Azure Stack hub-modulok adott verzióját a PowerShell-ben. Ha az SQL Resource Provider 1.1.47.0 verzióját telepíti, a telepítési parancsfájl automatikusan letölti és telepíti a szükséges PowerShell-modulokat a C:\Program Files\SqlMySqlPsh.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell
Install-Module -Name AzureRm.BootStrapper -RequiredVersion 0.5.0 -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> A leválasztott forgatókönyvben le kell töltenie a szükséges PowerShell-modulokat, és manuálisan kell regisztrálnia az adattárat előfeltételként.

Az erőforrás-szolgáltató üzembe helyezése során felmerülő manuális konfiguráció elkerülése érdekében testreszabhatja az alábbi parancsfájlt. Az Azure Stack hub telepítéséhez szükség szerint módosítsa az alapértelmezett fiók adatait és a jelszavakat.

```powershell
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS VMs
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local admin account.
$vmLocalAdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath 

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

Az erőforrás-szolgáltató telepítési parancsfájljának befejeződése után frissítse a böngészőt, és győződjön meg arról, hogy a legújabb frissítések láthatók, és hogy az aktuális PowerShell-munkamenetet lezárta.

## <a name="verify-the-deployment-using-the-azure-stack-hub-portal"></a>A központi telepítés ellenőrzése a Azure Stack hub portál használatával

1. Jelentkezzen be a felügyeleti portálra szolgáltatás-rendszergazdaként.
2. Válassza az **Erőforráscsoportok** lehetőséget.
3. Válassza ki a **System. \<location\> . sqladapter** erőforráscsoport.
4. Az erőforráscsoport-áttekintés összefoglaló lapján nem lehetnek sikertelen központi telepítések.
5. Végül a felügyeleti portálon válassza a **Virtual Machines (virtuális gépek** ) lehetőséget annak ellenőrzéséhez, hogy az SQL Resource Provider virtuális gép sikeresen létrejött-e, és fut-e.

## <a name="next-steps"></a>Következő lépések

[Üzemeltetési kiszolgálók hozzáadása](azure-stack-sql-resource-provider-hosting-servers.md)
