---
title: MySQL-adatbázisok használata az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan telepíthet MySQL-adatbázisok szolgáltatásként az Azure Stack és az első lépéseket a MySQL-kiszolgáló erőforrás-szolgáltató adapter üzembe helyezéséhez.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 9b2fb5f6ae1fef29801f7decc6adfbd7e68abd0c
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64294069"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Az Azure Stackben a MySQL erőforrás-szolgáltató üzembe helyezése

A MySQL Server erőforrás-szolgáltatóval Azure Stack-szolgáltatásként tehet közzé MySQL-adatbázisokat. A MySQL erőforrás-szolgáltató szolgáltatásként fut, a Windows Server 2016 Server Core virtuális gépeken (VM).

> [!IMPORTANT]
> Konfigurációelemek létrehozása a kiszolgálókon, a fogadó SQL vagy MySQL csak az erőforrás-szolgáltató támogatott. A gazdagép-kiszolgálón létrehozott elemek nem az erőforrás-szolgáltató által létrehozott egy nem megfelelő állapot eredményezhet.

## <a name="prerequisites"></a>Előfeltételek

Nincsenek számos előfeltételt kell lennie a helyen, az Azure Stack MySQL erőforrás-szolgáltató telepítése előtt. Mindezen követelmények teljesítése érdekében hajtsa végre az olyan számítógépre, amelyen a kiemelt jogosultságú végpont a virtuális gép érhető el ez a cikk lépéseit.

* Ha ezt még nem tette meg, [regisztrálása az Azure Stack](./azure-stack-registration.md) az Azure-ral, letöltheti az Azure marketplace-elemek.
* Telepítenie kell az Azure és az Azure Stack PowerShell-modulokat a rendszer, amelyen futtatni fogja a telepítést. A rendszer a .NET-modul legújabb verzióját a Windows 10-es vagy Windows Server 2016 képnek kell lennie. Lásd: [PowerShell telepítése az Azure Stackhez](./azure-stack-powershell-install.md).
* Az Azure Stack piactéren úgy, hogy letölti a szükséges Windows Server core virtuális gép hozzáadása a **Windows Server 2016 Datacenter - Server Core** kép.

* Töltse le a MySQL erőforrás-szolgáltató bináris, és futtassa a mappába, csomagolja ki a tartalmát egy ideiglenes könyvtárba.

  >[!NOTE]
  >A rendszer nem rendelkezik Internet-hozzáférés a MySQL-szolgáltató üzembe helyezése, másolja át a [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) fájlt egy helyi elérési útra. Adja meg az elérési út neve használatával a **DependencyFilesLocalPath** paraméter.

* Az erőforrás-szolgáltató rendelkezik egy minimális megfelelő Azure Stack hozhat létre.

  |Azure Stack minimális verziója|MySQL-RP-verzió|
  |-----|-----|
  |Verzió 1808 (1.1808.0.97)|[MySQL-RP 1.1.33.0 verzió](https://aka.ms/azurestackmysqlrp11330)|  
  |Verzió 1808 (1.1808.0.97)|[MySQL-RP 1.1.30.0 verzió](https://aka.ms/azurestackmysqlrp11300)|
  |Verzió 1804 (1.0.180513.1)|[MySQL-RP 1.1.24.0 verzió](https://aka.ms/azurestackmysqlrp11240)
  |     |     |

* Ellenőrizze, hogy adatközpont integrációja Előfeltételek teljesülését:

    |Előfeltétel|Leírások|
    |-----|-----|
    |Feltételes DNS-továbbítás megfelelően van beállítva.|[Az Azure Stack adatközpont integrációja - DNS](azure-stack-integrate-dns.md)|
    |Erőforrás-szolgáltatók bejövő portok nyitva.|[Az Azure Stack adatközpont integrációja – végpontok közzététele](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |PKI-tanúsítvány tulajdonosának és SAN helyesen van beállítva.|[Az Azure Stack üzembehelyezési kötelező nyilvános kulcsokra épülő infrastruktúra Előfeltételek](azure-stack-pki-certs.md#mandatory-certificates)[Azure Stack üzembehelyezési PaaS tanúsítvány Előfeltételek](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Tanúsítványok

_Csak az integrált rendszerek telepítés_. Meg kell adnia az SQL PaaS PKI-tanúsítványt a választható PaaS tanúsítványok szakaszában leírt [Azure Stack üzembe helyezési nyilvános kulcsokra épülő infrastruktúra követelményei](./azure-stack-pki-certs.md#optional-paas-certificates). A megadott helyen helyezze el a .pfx-fájlt a **DependencyFilesLocalPath** paraméter. ASDK rendszerek nem biztosítanak egy tanúsítványt.

## <a name="deploy-the-resource-provider"></a>Az erőforrás-szolgáltató üzembe helyezése

Minden előfeltétel telepítése után futtathatja a **DeployMySqlProvider.ps1** üzembe helyezi a MySQL erőforrás-szolgáltató. A DeployMySqlProvider.ps1 parancsfájlt a MySQL resource provider telepítőfájljainak letöltött az Azure Stack verziójának részeként ki kell olvasni.

 > [!IMPORTANT]
 > Az erőforrás-szolgáltató üzembe helyezése előtt tekintse át a kibocsátási megjegyzéseket, új funkciókat, javításokat és olyan ismert problémákat, amelyek hatással lehetnek a központi telepítés megismeréséhez.

A MySQL erőforrás-szolgáltató üzembe helyezése, nyisson meg egy új emelt szintű PowerShell-ablakot (nem a PowerShell ISE), és váltson arra a könyvtárra, amelyben kibontotta a MySQL erőforrás szolgáltató bináris fájlokat. Azt javasoljuk, egy új PowerShell-ablakot a már betöltött PowerShell-modulok által okozott problémák elkerülése érdekében.

Futtassa a **DeployMySqlProvider.ps1** parancsfájl, amely a következő feladatokat hajtja végre:

* A tanúsítványokat és más összetevőket tölt fel egy storage-fiókba az Azure Stacken.
* Katalóguscsomagok tesz közzé, úgy, hogy a MySQL-adatbázisok, a katalógus használatával telepítheti.
* Közzétesz egy gyűjteménycsomag üzemeltetési kiszolgáló üzembe helyezéséhez.
* Üzembe helyez egy virtuális Gépet a Windows Server 2016 core rendszerképet letöltötte, és ezután telepíti a MySQL erőforrás-szolgáltató használatával.
* Regisztrálja a helyi DNS-rekordot, amely a virtuális gép erőforrás-szolgáltató van leképezve.
* Az erőforrás-szolgáltató regisztrálása a helyi Azure Resource Managerrel az operátor fiók.

> [!NOTE]
> A MySQL erőforrás-szolgáltató telepítési indításakor, a **system.local.mysqladapter** erőforráscsoportot kell létrehozni. Ez az erőforráscsoport szükséges központi telepítések befejezéséhez akár 75 perc is eltarthat.

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 paraméterek

Ezeket a paramétereket a parancssorból is megadhat. Ha nem, vagy ha minden paraméter ellenőrzése sikertelen, kéri, hogy adja meg a szükséges paramétereket.

| Paraméter neve | Leírás | Megjegyzés vagy az alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A felhő rendszergazdájához, a kiemelt végponthoz eléréséhez szükséges hitelesítő adatait. | _Kötelező_ |
| **AzCredential** | Az Azure Stack szolgáltatás-rendszergazdai fiók hitelesítő adatait. Használja az Azure Stack üzembe helyezéséhez használt hitelesítő adatokkal. | _Kötelező_ |
| **VMLocalCredential** | A MySQL erőforrás-szolgáltató virtuális Gépet, a helyi rendszergazdai fiók hitelesítő adatait. | _Kötelező_ |
| **PrivilegedEndpoint** | Az IP-cím vagy a kiemelt végponthoz DNS-nevét. |  _Kötelező_ |
| **AzureEnvironment** | A szolgáltatás-rendszergazdai fiókot használja az Azure Stack üzembe helyezése az Azure környezetben. Kizárólag az Azure AD központi telepítések esetén szükséges. Támogatott környezeti nevek **AzureCloud**, **AzureUSGovernment**, vagy a China Azure AD-ben való használatakor **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | A csak integrált rendszerek a tanúsítvány .pfx fájlját ebben a könyvtárban kell elhelyezni. Kapcsolat nélküli környezetben, töltse le a [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) ebben a könyvtárban. Szükség esetén egy Windows Update MSU csomag itt másolhatja. | _Nem kötelező_ (_kötelező_ integrált rendszerek vagy kapcsolat nélküli környezetben) |
| **DefaultSSLCertificatePassword** | A .pfx tanúsítvány jelszava. | _Kötelező_ |
| **MaxRetryCount** | Többször ismételje meg minden művelet, ha sikertelen egy kívánt száma.| 2 |
| **RetryDuration** | Az időkorlát között eltelő időt másodpercben mérve. | 120 |
| **Eltávolítás** | Eltávolítja az erőforrás-szolgáltató és az összes társított erőforrást (lásd az alábbi megjegyzések). | Nem |
| **DebugMode** | Megakadályozza, hogy hiba esetén az automatikus tisztítás. | Nem |
| **AcceptLicense** | A rendszer kéri, fogadja el a GPL-licenc kihagyja.  <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>A MySQL erőforrás-szolgáltató egy egyéni parancsfájl használata központi telepítése

Minden manuális konfigurációs megszüntetésére, az erőforrás-szolgáltató üzembe helyezésekor, testre szabhatja az alábbi parancsfájlt. Az Azure Stack üzembe helyezéshez szükség szerint változtassa meg az alapértelmezett fiók adatait és a jelszavakat.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"  

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Clear the existing login information from the Azure PowerShell context.
Clear-AzureRMContext -Scope CurrentUser -Force
Clear-AzureRMContext -Scope Process -Force

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
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

Amikor befejezi az erőforrás-szolgáltató telepítési parancsfájlt, frissítse a böngészőben győződjön meg arról, láthatja, hogy a legújabb frissítéseket.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>A telepítés ellenőrzése az Azure Stack-portál használatával

1. Jelentkezzen be a felügyeleti portálon a szolgáltatás-rendszergazdaként.
2. Válassza ki **erőforráscsoportok**
3. Válassza ki a **system.\< hely\>.mysqladapter** erőforráscsoportot.
4. Az összefoglalás lapon erőforráscsoport áttekintése nincs sikertelen üzembe helyezés kell lennie.
5. Végül válassza **virtuális gépek** a felügyeleti portálon, győződjön meg arról, hogy a MySQL erőforrás-szolgáltató virtuális gép sikeresen létrehozott és fut-e.

## <a name="next-steps"></a>További lépések

[Üzemeltetési kiszolgáló hozzáadása](azure-stack-mysql-resource-provider-hosting-servers.md)
