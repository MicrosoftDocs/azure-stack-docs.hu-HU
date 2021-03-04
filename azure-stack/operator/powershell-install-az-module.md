---
title: A PowerShell telepítése az Azure Stack hub modulhoz
description: Ismerje meg, hogyan telepítheti a PowerShellt Azure Stack hubhoz.
author: mattbriggs
ms.topic: article
ms.date: 02/18/2021
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 02/18/2021
ms.openlocfilehash: 031a1695f8ba11db5a8787ef1b38c40763614b88
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840864"
---
# <a name="install-powershell-az-module-for-azure-stack-hub"></a>A PowerShell telepítése az Azure Stack hub modulhoz

Ez a cikk azt ismerteti, hogyan telepítheti az Azure PowerShell az és a kompatibilis Azure Stack hub rendszergazdai moduljait a PowerShellGet használatával. Az az modulokat Windows, macOS és Linux platformokra lehet telepíteni.

A Docker-tárolóban futtathatja az az Azure Stack hub-hoz készült modulokat is. Útmutatásért lásd: [a Docker használata a PowerShell Azure stack hubhoz való futtatásához](../user/azure-stack-powershell-user-docker.md).

Ha Azure Stack hub PowerShell Resource modules (AzureRM) modulját szeretné telepíteni, tekintse meg [a PowerShell-AzureRM modul telepítése Azure stack hub](azure-stack-powershell-install.md)-hoz című témakört.

> [!IMPORTANT]
> Valószínűleg nem lesznek új Azure Resource Module-modulok. Az Azure Resource Module modulok csak a kritikus javítások támogatását támogatják. A továbbiakban csak az az releases for Azure Stack hub lesz.

Az API- *profilok* segítségével megadhatja az Azure stack hub erőforrás-szolgáltatóhoz tartozó kompatibilis végpontokat.

Az API-profilok lehetőséget biztosítanak az Azure és a Azure Stack hub közötti verziók közötti különbségek kezelésére. Az API-verzió profilja Azure Resource Manager PowerShell-modulok meghatározott API-verziókkal. Minden egyes felhőalapú platformon támogatott API-verzió-profilok szerepelnek. Az Azure Stack hub például egy adott profil verzióját támogatja, például **2019-03-01-Hybrid**. Profil telepítésekor a rendszer telepíti a megadott profilhoz tartozó Azure Resource Manager PowerShell-modulokat.

Az Azure Stack hub-kompatibilis PowerShell az a modulok az internethez csatlakoztatott, részben csatlakoztatott vagy leválasztott forgatókönyvekben telepíthetők. Ez a cikk részletesen ismerteti ezeket a forgatókönyveket.

## <a name="1-verify-your-prerequisites"></a>1. Ellenőrizze az előfeltételeket

Az az modulokat a 2002-es vagy újabb frissítéssel rendelkező Azure Stack hub támogatja, valamint a jelenleg telepített gyorsjavítások. További információért tekintse meg az [Azure stack hub kibocsátási megjegyzéseit](release-notes.md) .

A Azure PowerShell az modulok a PowerShell 5,1-es vagy újabb verziójával működnek a Windowson, vagy a PowerShell Core 6. x-es és újabb verzióiban minden platformon. Telepítenie kell a [PowerShell Core legújabb verzióját](/powershell/scripting/install/installing-powershell#powershell-core) az operációs rendszer számára. A Azure PowerShell nem rendelkezik más követelményekkel a PowerShell Core-on való futtatáskor.

A PowerShell verziójának megtekintéséhez futtassa az alábbi parancsot:

```powershell  
$PSVersionTable.PSVersion
```

### <a name="prerequisites-for-windows"></a>A Windows előfeltételei
Az Azure PowerShell használata PowerShell 5.1-ben Windows rendszeren:

1. Frissítsen a [Windows PowerShell 5.1-re](/powershell/scripting/windows-powershell/install/installing-windows-powershell#upgrading-existing-windows-powershell), ha szükséges. Ha Windows 10 rendszert használ, már telepítve van a PowerShell 5.1.
2. Telepítse a [.NET-keretrendszer 4.7.2-es vagy újabb verzióját](/dotnet/framework/install).
3. Győződjön meg arról, hogy a PowerShellGet legújabb verziójával rendelkezik. Futtassa az `Install-Module PowerShellGet -MinimumVersion 2.2.3 -Force` parancsot. 

## <a name="2-prerequisites-for-linux-and-mac"></a>2. a Linux és a Mac előfeltételei
A PowerShell Core 6. x vagy újabb verziója szükséges. Útmutatásért kövesse az alábbi [hivatkozást](/powershell/scripting/install/installing-powershell-core-on-windows)

## <a name="3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules"></a>3. távolítsa el az Azure Stack hub PowerShell-modulok meglévő verzióit

A szükséges verzió telepítése előtt győződjön meg arról, hogy eltávolítja a korábban telepített Azure Stack hub Azure Resource Manager vagy az PowerShell modulokat. Távolítsa el a modulokat a következő két módszer egyikének használatával:

1. A meglévő Azure Resource Manager és az PowerShell-modulok eltávolításához zárjunk be minden aktív PowerShell-munkamenetet, és futtassa a következő parancsmagokat:

    ```powershell
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Az.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    ```
    Ha olyan hibát talál, mint például a "a modul már használatban van", akkor a modulokat használó PowerShell-munkameneteket, majd futtassa újra a fenti szkriptet.

2. Törölje az összes olyan mappát, amely a-val kezdődik `Azure` , `Az` vagy `Azs.` a `C:\Program Files\WindowsPowerShell\Modules` és a `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` mappákból. A mappák törlése eltávolítja a meglévő PowerShell-modulokat.

## <a name="4-connected-install-with-internet-connectivity"></a>4. csatlakoztatva: telepítés internetkapcsolattal

Az Azure Stack az Module Azure Stack hub 2002-as vagy újabb verziójával fog működni. Emellett a Azure Stack az modul a PowerShell 5,1-es vagy újabb verzióját fogja működni Windows-gépen, vagy a PowerShell 6. x-es vagy újabb verzióját egy Linux-vagy macOS-platformon. Az előnyben részesített telepítési módszer a PowerShellGet-parancsmagok használata. Ez a módszer ugyanúgy működik a támogatott platformokon.

1. Futtassa a következő parancsot egy PowerShell-munkamenetből, hogy frissítse a PowerShellGet-t legalább a 2.2.3-es verzióra.

    ```powershell  
    Install-Module PowerShellGet -MinimumVersion 2.2.3 -Force
    ```

2. Zárjuk be a PowerShell-munkamenetet, majd nyisson meg egy új PowerShell-munkamenetet, hogy a frissítés életbe lépjen.

3. Futtassa az alábbi parancsot egy PowerShell-munkamenetből:

    ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    
    Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
    Install-AzProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease
    ```

> [!Note]  
> Azure Stack hub-modul 2.0.0-verziójának feltörési változása. A részletekért tekintse meg az [áttelepítés a AzureRM-ről Azure PowerShell az az Azure stack hub-ban](migrate-azurerm-az.md) című témakört.

> [!WARNING]
> Egyszerre csak a Azure Resource Manager (AzureRM) és az az modulok vannak telepítve a PowerShell 5,1-hez. Ha meg kell őriznie Azure Resource Manager a rendszeren elérhetőnek kell lennie, telepítse az az modult a PowerShell Core 6. x vagy újabb verzióra. Ehhez [telepítse a PowerShell Core 6. x-es vagy újabb verzióját](/powershell/scripting/install/installing-powershell-core-on-windows) , majd kövesse ezeket az utasításokat egy PowerShell Core-terminálon.

## <a name="5-disconnected-install-without-internet-connection"></a>5. leválasztva: telepítés internetkapcsolat nélkül

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

Azure Resource Manager vagy az az modulokat is használhatja. Azure Resource Manager esetében tekintse meg a [PowerShell-AzureRM modul telepítése](powershell-install-az-module.md)című témakör útmutatását. A következő kód a megbízható online tárházból menti a modulokat https://www.powershellgallery.com/ .

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

Install-module -Name PowerShellGet -MinimumVersion 2.2.3 -Force
Import-Module -Name PackageManagement -ErrorAction Stop

$savedModulesPath = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Az -Path $savedModulesPath -Force -RequiredVersion 0.10.0-preview
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $savedModulesPath -Force -RequiredVersion 2.0.2-preview
```
::: moniker-end

> [!NOTE]  
> Internetkapcsolat nélküli gépeken a következő parancsmag futtatását javasoljuk a telemetria-adatgyűjtés letiltásához. A parancsmagok teljesítménybeli romlása a telemetria-adatgyűjtés letiltása nélkül is felmerülhet. Ez csak az internetkapcsolat nélküli gépek esetében érvényes
> ```powershell
> Disable-AzDataCollection
> ```

### <a name="add-your-packages-to-your-workstation"></a>Csomagok hozzáadása a munkaállomáshoz

1. Másolja a letöltött csomagokat egy USB-eszközre.

2. Jelentkezzen be a kapcsolat nélküli munkaállomásra, és másolja a csomagokat az USB-eszközről a munkaállomás egyik helyére.

3. Adja meg manuálisan a NuGet-szolgáltatót a kapcsolat nélküli munkaállomáson. Útmutatásért lásd: [a NuGet-szolgáltató manuális betöltése olyan gépen, amely nem kapcsolódik az internethez](/powershell/scripting/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet).

4. Regisztrálja ezt a helyet alapértelmezett tárházként, és telepítse a `AzureRM` és a `AzureStack` modulokat ebből a tárházból:

   ```powershell
   # requires -Version 5
   # requires -RunAsAdministrator
   # requires -Module PowerShellGet
   # requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

   Install-Module -Name AzureStack -Repository $RepoName -RequiredVersion 2.0.2-preview -AllowPrerelease -Scope AllUsers

   Install-Module -Name Az -Repository $RepoName -RequiredVersion 0.10.0-preview -AllowPrerelease -Scope AllUsers
   ```

### <a name="confirm-the-installation-of-powershell"></a>A PowerShell telepítésének megerősítése

A következő parancs futtatásával erősítse meg a telepítést:

```powershell
Get-Module -Name "Az*" -ListAvailable
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

## <a name="7-use-the-az-module"></a>7. használja az az modult

A parancsmagokat és a kód mintákat Azure Resource Manager alapján is használhatja. Azonban módosítania kell a modulok és a parancsmagok nevét. A modul neve megváltozott, így az `AzureRM` Azure és `Az` a parancsmagok is megegyeznek. Például a `AzureRM.Compute` modul át lett nevezve a következőre: `Az.Compute` .` New-AzureRMVM` ` New-AzVM` lett, a `Get-AzureStorageBlob` pedig `Get-AzStorageBlob`.

A AzurRM-szkriptek az az-ba való áthelyezésével és a Azure Stack hub az az modulban történő módosításával kapcsolatos részletesebb megbeszélések és útmutatás: [áttelepítés az AzureRM-ből Azure PowerShell az-ba](migrate-azurerm-az.md).

## <a name="known-issues"></a>Ismert problémák

[!Include[Known issue for install - one](../includes/known-issue-az-install-1.md)]

[!Include[Known issue for install - two](../includes/known-issue-az-install-2.md)]

## <a name="next-steps"></a>Következő lépések

- [Azure Stack hub-eszközök letöltése a GitHubról](azure-stack-powershell-download.md)
- [A Azure Stack hub felhasználói PowerShell-környezetének konfigurálása](../user/azure-stack-powershell-configure-user.md)
- [Az Azure Stack hub-kezelő PowerShell-környezetének konfigurálása](azure-stack-powershell-configure-admin.md)
- [API-verziók profiljainak kezelése Azure Stack hub-ban](../user/azure-stack-version-profiles.md)
