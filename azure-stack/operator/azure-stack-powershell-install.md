---
title: PowerShell AzureRM-modul telepítése Azure Stack hubhoz
description: 'Ismerje meg, hogyan telepítheti a PowerShellt Azure Stack hubhoz. Lásd: a PowerShell AzureRM modul és a szükséges API-profilok telepítése.'
author: mattbriggs
ms.topic: article
ms.date: 08/04/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: bbf1a5d296ddbef554a4401e66eab4226ae38dd3
ms.sourcegitcommit: a1e2003fb9c6dacdc76f97614ff5a26a5b197b49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91623166"
---
# <a name="install-powershell-azurerm-module-for-azure-stack-hub"></a>PowerShell AzureRM-modul telepítése Azure Stack hubhoz

Azure PowerShell AzureRM olyan parancsmagokat biztosít, amelyek a Azure Resource Manager modellt használják az Azure Stack hub-erőforrások kezeléséhez.

Az Azure Stack hub erőforrás-szolgáltatói számára a kompatibilis végpontok megadásához is *API-profilokat* kell használnia.

Az API-profilok lehetőséget biztosítanak az Azure és a Azure Stack hub közötti verziók közötti különbségek kezelésére. Az API-verzió profilja Azure Resource Manager PowerShell-modulok meghatározott API-verziókkal. Minden egyes felhőalapú platformon támogatott API-verzió-profilok szerepelnek. Az Azure Stack hub például egy adott profil verzióját támogatja, például **2019-03-01-Hybrid**. Profil telepítésekor a rendszer telepíti a megadott profilhoz tartozó Azure Resource Manager PowerShell-modulokat.

Azure Stack hub-kompatibilis PowerShell-modulokat az internethez csatlakoztatott, részben csatlakoztatott vagy leválasztott helyzetekben is telepítheti. Ez a cikk részletesen ismerteti ezeket a forgatókönyveket.

Az Azure Stack hub AzureRM moduljait egy Docker-tárolóban is futtathatja. Útmutatásért lásd: [a Docker használata a PowerShell Azure stack hubhoz való futtatásához](../user/azure-stack-powershell-user-docker.md).

## <a name="1-verify-your-prerequisites"></a>1. Ellenőrizze az előfeltételeket

Az Azure Stack hub és a PowerShell-AzureRM modul használatának megkezdése előtt a következő előfeltételeket kell megadnia:

- **PowerShell 5,1-es verzió** <br>
A verziójának vizsgálatához futtassa a **$PSVersionTable. PSVersion** parancsot, és hasonlítsa **össze a** főverziót. Ha nem rendelkezik a PowerShell 5,1-rel, kövesse a [Windows PowerShell telepítése](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)című témakört.

  > [!Note]
  > A PowerShell 5,1 használatához Windows rendszerű gép szükséges.

- **Futtassa a PowerShellt egy rendszergazda jogú parancssorban**.

- **PowerShell-galéria hozzáférés** <br>
  Hozzá kell férnie a [PowerShell-galériahoz](https://www.powershellgallery.com). A gyűjtemény a PowerShell-tartalmak központi tárháza. A **PowerShellGet** modul parancsmagokat tartalmaz a PowerShell-összetevők felfedéséhez, telepítéséhez, frissítéséhez és közzétételéhez. Ilyen összetevők például a modulok, a DSC-erőforrások, a szerepkör-képességek, valamint a PowerShell-galéria és más privát adattárakból származó parancsfájlok. Ha a PowerShellt leválasztott forgatókönyvben használja, le kell kérnie az erőforrásokat egy internetkapcsolattal rendelkező gépről, és a leválasztott gép számára elérhető helyen kell tárolnia azokat.

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. a PowerShell-galéria kisegítő lehetőségek ellenőrzése

Annak ellenőrzése, hogy a PSGallery regisztrálva van-e adattárként.

> [!Note]  
> Ehhez a lépéshez internet-hozzáférés szükséges.

Nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagokat:

```powershell
Install-module -Name PowerShellGet -Force
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Ha a tárház nincs regisztrálva, nyisson meg egy emelt szintű PowerShell-munkamenetet, és futtassa a következő parancsot:

```powershell
Register-PSRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules"></a>3. távolítsa el az Azure Stack hub PowerShell-modulok meglévő verzióit

A szükséges verzió telepítése előtt győződjön meg arról, hogy eltávolította a korábban telepített Azure Stack hub AzureRM PowerShell-modulokat. Távolítsa el a modulokat a következő két módszer egyikének használatával:

1. A meglévő AzureRM és az PowerShell-modulok eltávolításához zárjunk be minden aktív PowerShell-munkamenetet, és futtassa a következő parancsmagokat:

    ```powershell
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Az.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    ```

    Ha olyan hibát talál, mint például a "a modul már használatban van", akkor a modulokat használó PowerShell-munkameneteket, majd futtassa újra a fenti szkriptet.

2. Törölje az összes olyan mappát, amely a-val kezdődik `Azure` , `Az` vagy `Azs.` a `C:\Program Files\WindowsPowerShell\Modules` és a `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` mappákból. A mappák törlése eltávolítja a meglévő PowerShell-modulokat.

## <a name="4-connected-install-powershell-for-azure-stack-hub-with-internet-connectivity"></a>4. csatlakozás: a PowerShell telepítése Azure Stack hubhoz internetkapcsolattal

A szükséges API-verzió profil és Azure Stack hub PowerShell-modulok a futtatott Azure Stack hub verziójától függenek.

### <a name="install-azure-stack-hub-powershell"></a>Azure Stack hub PowerShell telepítése

Futtassa a következő PowerShell-szkriptet a modulok fejlesztői munkaállomáson történő telepítéséhez:

::: moniker range=">=azs-2002"
Azure Stack hub 2002-es vagy újabb verziója esetén:

Használhat felhasználói AzureRm-modulokat vagy az előzetes verziójú modulokat is. Az az modulok használatához Azure Stack hub 2002 és a legújabb gyorsjavítás szükséges.

Az az előzetes verziójú modulok használatához kövesse a [PowerShell telepítése az modulban](powershell-install-az-module.md)című témakör útmutatását.

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.8.2
```

::: moniker-end
::: moniker range="azs-1910"
Azure Stack hub 1910:

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.8.0
```

> [!Note]  
> - A Azure Stack hub-modul 1.8.0-es verziója egy feltörést jelentő változási kiadás. A részletekért tekintse meg a [kibocsátási megjegyzést](release-notes.md) .

::: moniker-end
::: moniker range="<=azs-1908"
Azure Stack hub 1908 vagy korábbi verzió esetén:

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.7.2
```

> [!Note]  
> Az Azure Stack hub modul 1.7.2 verziója a feltörési változás kiadása. Az Azure Stack hub 1.6.0 való áttelepítéshez tekintse meg az [áttelepítési útmutatót](https://aka.ms/azspshmigration171).

::: moniker-end

### <a name="confirm-the-installation-of-powershell"></a>A PowerShell telepítésének megerősítése

A következő parancs futtatásával erősítse meg a telepítést:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Ha a telepítés sikeres, a `AzureRm` és a `AzureStack` modulok megjelennek a kimenetben.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. leválasztva: a PowerShell telepítése internetkapcsolat nélkül

Leválasztott forgatókönyv esetén először töltse le a PowerShell-modulokat egy internetkapcsolattal rendelkező gépre. Ezután a telepítéshez továbbítja azokat a Azure Stack Development Kitba (ASDK).

Jelentkezzen be egy internetkapcsolattal rendelkező számítógépre, és a következő parancsfájlok segítségével töltse le a Azure Resource Manager és Azure Stack hub-csomagokat az Azure Stack hub verziójától függően.

A telepítés öt lépésből áll:

1. Telepítse Azure Stack hub PowerShellt egy csatlakoztatott gépre.
2. Engedélyezze a további tárolási funkciókat.
3. A PowerShell-csomagok átvitele a kapcsolat nélküli munkaállomásra.
4. Adja meg manuálisan a NuGet-szolgáltatót a kapcsolat nélküli munkaállomáson.
5. Erősítse meg a PowerShell telepítését.

### <a name="install-azure-stack-hub-powershell"></a>Azure Stack hub PowerShell telepítése

::: moniker range=">=azs-2002"
Azure Stack hub 2002 vagy újabb.

Használhatja a AzureRM vagy az az előnézet modulokat. Az az modulok esetében lásd a [PowerShell telepítése az modulban](powershell-install-az-module.md)című témakör útmutatását.

```powershell

Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.2
```
::: moniker-end

::: moniker range="azs-1910"
Azure Stack hub 1910.

```powershell
Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.0
```

> [!NOTE]  
> A Azure Stack hub-modul 1.8.0-es verziója egy feltörést jelentő változási kiadás. A részletekért tekintse meg a [kibocsátási megjegyzést](release-notes.md) .

::: moniker-end
::: moniker range="<=azs-1908"
Azure Stack hub 1908 vagy korábbi verzió esetén:

```powershell
Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.2
```

> [!NOTE]  
> Az Azure Stack hub-modul 1.7.1 verziója egy megszakítási változás. Azure Stack hub-1.6.0 való áttelepítéshez tekintse meg az [áttelepítési útmutatót](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).

::: moniker-end

> [!NOTE]  
> Internetkapcsolat nélküli gépeken a következő parancsmag futtatását javasoljuk a telemetria-adatgyűjtés letiltásához. A parancsmagok teljesítménybeli romlása a telemetria-adatgyűjtés letiltása nélkül is felmerülhet. Ez csak az internetkapcsolat nélküli gépek esetében érvényes
> ```powershell
> Disable-AzureRmDataCollection
> ```

### <a name="add-your-packages-to-your-workstation"></a>Csomagok hozzáadása a munkaállomáshoz

1. Másolja a letöltött csomagokat egy USB-eszközre.

2. Jelentkezzen be a kapcsolat nélküli munkaállomásra, és másolja a csomagokat az USB-eszközről a munkaállomás egyik helyére.

3. Adja meg manuálisan a NuGet-szolgáltatót a kapcsolat nélküli munkaállomáson. Útmutatásért lásd: [a NuGet-szolgáltató manuális betöltése olyan gépen, amely nem kapcsolódik az internethez](/powershell/scripting/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet).

4. Regisztrálja ezt a helyet alapértelmezett tárházként, és telepítse a AzureRM és a `AzureStack` modulokat ebből a tárházból:

   ```powershell
   # requires -Version 5
   # requires -RunAsAdministrator
   # requires -Module PowerShellGet
   # requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

### <a name="confirm-the-installation-of-powershell"></a>A PowerShell telepítésének megerősítése

A következő parancs futtatásával erősítse meg a telepítést:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. a PowerShell konfigurálása proxykiszolgáló használatára

Olyan helyzetekben, amelyekhez proxykiszolgáló szükséges az internethez való hozzáféréshez, először konfigurálja a PowerShellt egy meglévő proxykiszolgáló használatára:

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort.
2. Futtassa az alábbi parancsot:

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="known-issue"></a>Ismert probléma

###  <a name="method-get_serializationsettings-error"></a>Metódus get_SerializationSettings hiba 

- Ok: a PowerShell az Module és a PowerShell AzureRM modulok nem kompatibilisek.

    A következő hiba azt jelzi, hogy a AzureRM modulok és az az modulok betöltődik ugyanabban a munkamenetben: 

    ```powershell  
    >  Method 'get_SerializationSettings' in type 'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly 'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' does 
    not have an implementation.
    ```

- Szervizelés: távolítsa el az ütköző modulokat. 

  Ha a AzureRM-modulokat szeretné használni, távolítsa el az az modulokat. Vagy távolítsa el a AzureRM, ha az az modulokat szeretné használni. Zárjuk be a PowerShell-munkamenetet, és távolítsa el az az vagy a AzureRM modult. 
  
  [Az Azure stack hub PowerShell-modulok meglévő verzióinak eltávolításához](#3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules)útmutatást talál.

## <a name="next-steps"></a>További lépések

- [Azure Stack hub-eszközök letöltése a GitHubról](azure-stack-powershell-download.md)
- [A Azure Stack hub felhasználói PowerShell-környezetének konfigurálása](../user/azure-stack-powershell-configure-user.md)
- [Az Azure Stack hub-kezelő PowerShell-környezetének konfigurálása](azure-stack-powershell-configure-admin.md)
- [API-verziók profiljainak kezelése Azure Stack hub-ban](../user/azure-stack-version-profiles.md)
