---
title: MySQL erőforrás-szolgáltató üzembe helyezése Azure Stack központban
description: Megtudhatja, hogyan helyezheti üzembe a MySQL erőforrás-szolgáltatói adaptert és a MySQL-adatbázisokat Azure Stack hub szolgáltatásként.
author: bryanla
ms.topic: article
ms.date: 9/22/2020
ms.author: bryanla
ms.reviewer: caoyang
ms.lastreviewed: 9/22/2020
ms.openlocfilehash: 22377e80f52b2a8e3a7827ded6400b17cebdce9c
ms.sourcegitcommit: af4374755cb4875a7cbed405b821f5703fa1c8cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95812732"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack-hub"></a>A MySQL erőforrás-szolgáltató üzembe helyezése Azure Stack központban

A MySQL-kiszolgáló erőforrás-szolgáltató használatával Azure Stack hub-szolgáltatásként elérhetővé teheti a MySQL-adatbázisokat. A MySQL erőforrás-szolgáltató szolgáltatásként fut egy Windows Server 2016 Server Core virtuális gépen (az adapter verziója <= 1.1.47.0>), vagy egy speciális kiegészítő RP Windows Server (az adapter verziójához >= 1.1.93.0).

> [!IMPORTANT]
> Csak az erőforrás-szolgáltató támogatott az SQL vagy a MySQL-t futtató kiszolgálókon lévő elemek létrehozásához. Az erőforrás-szolgáltató által nem létrehozott gazdagép-kiszolgálón létrehozott elemek nem egyező állapotba kerülhetnek.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack hub MySQL erőforrás-szolgáltató üzembe helyezése előtt több előfeltételt is meg kell hoznia. A követelmények teljesítése érdekében hajtsa végre a jelen cikkben ismertetett lépéseket egy olyan számítógépen, amely hozzáfér az emelt szintű végpont virtuális géphez.

* Ha még nem tette meg, [regisztráljon Azure stack hubot](./azure-stack-registration.md) az Azure-ban, hogy letöltse az Azure Marketplace-elemeket.

* Adja hozzá a szükséges Windows Server-alapú virtuális gépet Azure Stack hub Marketplace-hez.
  * A MySQL RP <= 1.1.47.0 esetében töltse le a **Windows server 2016 Datacenter – Server Core** rendszerképet.
  * A MySQL RP >= 1.1.93.0 esetében töltse le a **Microsoft AzureStack Add-On RP Windows Server csak belső** rendszerképet. Ez a Windows Server-verzió a Azure Stack Add-On RP-infrastruktúrára specializálódott, és nem látható a bérlői piactéren.

* Töltse le a MySQL erőforrás-szolgáltató bináris verziója támogatott verzióját az alábbi verzió-hozzárendelési táblázat szerint. Futtassa az önálló kivonót a letöltött tartalmak ideiglenes könyvtárba való kinyeréséhez. 

  |Támogatott Azure Stack hub-verzió|MySQL RP-verzió|Az a Windows Server, amelyre az RP szolgáltatás fut
  |-----|-----|-----|
  |2008, 2005|[MySQL RP-verzió 1.1.93.0](https://aka.ms/azshmysqlrp11930)|Microsoft AzureStack-bővítmény csak belső Windows Server-kiszolgálón
  |2005, 2002, 1910|[MySQL RP-verzió 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|Windows Server 2016 Datacenter – Server Core|
  |1908|[MySQL RP-verzió 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|Windows Server 2016 Datacenter – Server Core|
  |     |     |     |

>[!NOTE]
>Ha a MySQL-szolgáltatót olyan rendszeren szeretné telepíteni, amely nem rendelkezik internet-hozzáféréssel, másolja a [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) fájlt egy helyi elérési útra. Adja meg az elérési út nevét a **DependencyFilesLocalPath** paraméter használatával.


* Győződjön meg arról, hogy a Datacenter-integráció előfeltételei teljesülnek:

    |Előfeltétel|Referencia|
    |-----|-----|
    |A feltételes DNS-továbbítás helyesen van beállítva.|[Azure Stack hub Datacenter-integráció – DNS](azure-stack-integrate-dns.md)|
    |Az erőforrás-szolgáltatók bejövő portjai nyitva vannak.|[Azure Stack hub Datacenter-integráció – végpontok közzététele](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |A PKI-tanúsítvány tárgya és SAN beállítása helyesen van beállítva.|[Azure stack hub üzembe helyezése kötelező PKI-előfeltételek](azure-stack-pki-certs.md)[Azure stack hub üzembe helyezése – a tanúsítvány előfeltételei](azure-stack-pki-certs.md)|
    |     |     |

Leválasztott forgatókönyv esetén végezze el a következő lépéseket a szükséges PowerShell-modulok letöltéséhez és az adattár manuális regisztrálásához.

1. Jelentkezzen be egy internetkapcsolattal rendelkező számítógépre, és használja a következő parancsfájlokat a PowerShell-modulok letöltéséhez.

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop

# path to save the packages, c:\temp\azs1.6.0 as an example here
$Path = "c:\temp\azs1.6.0"
```

2. A telepített erőforrás-szolgáltató verziójától függően futtassa az egyik parancsfájlt.

```powershell
# for resource provider version >= 1.1.93.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.2
```
```powershell
# for resource provider version <= 1.1.47.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
```

3. Ezután másolja a letöltött csomagokat egy USB-eszközre.

4. Jelentkezzen be a kapcsolat nélküli munkaállomásra, és másolja a csomagokat az USB-eszközről a munkaállomás egyik helyére.

5. Helyi tárházként regisztrálja ezt a helyet.

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

## <a name="deploy-the-resource-provider"></a>Az erőforrás-szolgáltató üzembe helyezése

Miután telepítette az összes előfeltételt, futtathatja a **DeployMySqlProvider.ps1** parancsfájlt egy olyan számítógépről, amely a Azure stack hub rendszergazdai Azure Resource Management végpontját és a privilegizált végpontot is elérheti a MySQL erőforrás-szolgáltató üzembe helyezéséhez. A DeployMySqlProvider.ps1 szkript a MySQL erőforrás-szolgáltató telepítési fájljainak részeként lett kibontva, amelyeket a Azure Stack hub-verziójában töltött le.

 > [!IMPORTANT]
 > Az erőforrás-szolgáltató üzembe helyezése előtt tekintse át a kibocsátási megjegyzéseket, és ismerkedjen meg az új funkciókkal, javításokkal és az üzembe helyezést befolyásoló ismert problémákkal.

A MySQL erőforrás-szolgáltató üzembe helyezéséhez nyisson meg egy **új** emelt szintű PowerShell-ablakot (ne PowerShell ISE), és váltson arra a könyvtárra, ahová kicsomagolta a MySQL erőforrás-szolgáltató bináris fájljait. 

> [!IMPORTANT]
> Javasoljuk, hogy a **Clear-AzureRmContext-scope CurrentUser** és a **Clear-AzureRmContext-scope folyamat** használatával törölje a gyorsítótárat a Frissítési parancsfájl futtatása előtt.

Futtassa a **DeployMySqlProvider.ps1** parancsfájlt, amely a következő feladatokat hajtja végre:

* Feltölti a tanúsítványokat és egyéb összetevőket egy Azure Stack hub Storage-fiókjába.
* Közzéteszi a katalógus-csomagokat, így a gyűjtemény használatával MySQL-adatbázisokat telepíthet.
* Közzétesz egy gyűjtemény-csomagot az üzemeltetési kiszolgálók telepítéséhez.
* Üzembe helyez egy virtuális gépet a letöltött Windows Server 2016 Core rendszerkép vagy Microsoft AzureStack add-on RP Windows Server-rendszerkép használatával, majd telepíti a MySQL erőforrás-szolgáltatót.
* Egy helyi DNS-rekordot regisztrál, amely az erőforrás-szolgáltató virtuális géphez van társítva.
* Regisztrálja az erőforrás-szolgáltatót a kezelő fiók helyi Azure Resource Manager.

> [!NOTE]
> Amikor elindul a MySQL erőforrás-szolgáltató üzembe helyezése, létrejön a **System. local. mysqladapter** erőforráscsoport. Akár 75 percet is igénybe vehet, hogy elvégezze az ehhez az erőforráscsoporthoz szükséges központi telepítéseket. Ne helyezzen más erőforrásokat a **System. local. mysqladapter** erőforráscsoporthoz.

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 paraméterek

Ezeket a paramétereket megadhatja a parancssorból. Ha nem, vagy ha valamelyik paraméter ellenőrzése sikertelen, a rendszer felszólítja a szükséges paraméterek megadására.

| Paraméter neve | Leírás | Megjegyzés vagy alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A rendszerjogosultságú végpont eléréséhez szükséges hitelesítő adatok a felhő rendszergazdájához. | _Kötelező_ |
| **AzCredential** | Az Azure Stack hub szolgáltatás rendszergazdai fiókjának hitelesítő adatai. Használja ugyanazokat a hitelesítő adatokat, amelyeket az Azure Stack hub üzembe helyezéséhez használt. A szkript sikertelen lesz, ha a AzCredential használt fiók többtényezős hitelesítést (MFA) igényel. | _Kötelező_ |
| **VMLocalCredential** | A MySQL erőforrás-szolgáltató virtuális gép helyi rendszergazdai fiókjának hitelesítő adatai. | _Kötelező_ |
| **PrivilegedEndpoint** | Az emelt szintű végpont IP-címe vagy DNS-neve. |  _Kötelező_ |
| **AzureEnvironment** | Az Azure Stack hub üzembe helyezéséhez használt szolgáltatás-rendszergazdai fiók Azure-környezete. Csak az Azure AD-telepítésekhez szükséges. A támogatott környezeti nevek: **AzureCloud**, **AzureUSGovernment**, illetve kínai Azure ad-t, **AzureChinaCloud**-t használnak. | AzureCloud |
| **DependencyFilesLocalPath** | Csak az integrált rendszerek esetében a tanúsítvány. pfx fájlját ebbe a könyvtárba kell helyezni. A leválasztott környezetek esetében töltse le [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) erre a könyvtárba. Itt egy Windows Update MSU-csomagot is másolhat. | Nem _kötelező_ (az integrált rendszerekhez és a leválasztott környezetekhez _kötelező_ ) |
| **DefaultSSLCertificatePassword** | A. pfx-tanúsítvány jelszava. | _Kötelező_ |
| **MaxRetryCount** | Az egyes műveletek újrapróbálkozási időpontjának száma, ha hiba történt.| 2 |
| **RetryDuration** | Az újrapróbálkozások közötti időtúllépési időköz (másodpercben). | 120 |
| **Eltávolítás** | Eltávolítja az erőforrás-szolgáltatót és az összes kapcsolódó erőforrást (lásd a következő megjegyzéseket). | No |
| **DebugMode** | Megakadályozza a hibák automatikus törlését. | No |
| **AcceptLicense** | Kihagyja a kérést, hogy elfogadja a GPL-licencet.  <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>A MySQL erőforrás-szolgáltató üzembe helyezése egyéni parancsfájl használatával

Ha a MySQL erőforrás-szolgáltató 1.1.33.0 vagy korábbi verzióit telepíti, telepítenie kell a AzureRm. BootStrapper és a Azure Stack hub-modulok adott verzióját a PowerShell-ben. Ha a MySQL erőforrás-szolgáltató 1.1.47.0 vagy újabb verzióját telepíti, akkor az üzembehelyezési parancsfájl automatikusan letölti és telepíti a szükséges PowerShell-modulokat a C:\Program Files\SqlMySqlPsh.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> A leválasztott forgatókönyvben le kell töltenie a szükséges PowerShell-modulokat, és manuálisan kell regisztrálnia az adattárat előfeltételként.

Az erőforrás-szolgáltató üzembe helyezése során felmerülő manuális konfiguráció elkerülése érdekében testreszabhatja az alábbi parancsfájlt. Az Azure Stack hub telepítéséhez szükség szerint módosítsa az alapértelmezett fiók adatait és a jelszavakat.

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
$AdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local admin account
$vmLocalAdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force

# For version 1.1.47.0 or later, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh,
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath

# Change to the directory folder where you extracted the installation files. Don't provide a certificate on ASDK!
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Az erőforrás-szolgáltató telepítési parancsfájljának befejeződése után frissítse a böngészőt, és győződjön meg arról, hogy a legújabb frissítések láthatók, és hogy az aktuális PowerShell-munkamenetet lezárta.

## <a name="verify-the-deployment-by-using-the-azure-stack-hub-portal"></a>A központi telepítés ellenőrzése a Azure Stack hub portál használatával

1. Jelentkezzen be a felügyeleti portálra szolgáltatás-rendszergazdaként.
2. Válassza az **Erőforráscsoportok** lehetőséget.
3. Válassza ki a **System. \<location\> . mysqladapter** erőforráscsoport.
4. Az erőforráscsoport-áttekintés összefoglaló lapján nem lehetnek sikertelen központi telepítések.
5. Végül válassza a **virtuális gépek** lehetőséget a felügyeleti portálon annak ellenőrzéséhez, hogy a MySQL erőforrás-szolgáltató virtuális gépe sikeresen létrejött-e, és fut-e.

## <a name="next-steps"></a>További lépések

[Üzemeltetési kiszolgálók hozzáadása](azure-stack-mysql-resource-provider-hosting-servers.md)
