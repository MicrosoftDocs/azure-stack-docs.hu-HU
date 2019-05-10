---
title: Az Azure Stack PowerShell telepítése |} A Microsoft Docs
description: Ismerje meg az Azure Stack PowerShell telepítése.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/09/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: a1923c06d31ff32e1c7e5d50e3b70330d16d25c5
ms.sourcegitcommit: c755c7eac0f871960f9290591421cf5990b9e734
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506135"
---
# <a name="install-powershell-for-azure-stack"></a>Az Azure Stack PowerShell telepítése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Dolgozunk a felhőben, telepítenie kell az Azure Stack-kompatibilis PowerShell-modulok. Keresztül szolgáltatás engedélyezve van a kompatibilitási *API profilok*.

API-profilok kezeléséhez az Azure és az Azure Stack közötti tartalmaznak. Egy API-verzióprofil egy Azure Resource Manager PowerShell-modulok az adott API-verziókat. Minden felhőalapú platform a támogatott API-verzióprofilok készletével rendelkezik. Például az Azure Stack egy adott profil verziót támogatja például **2018-03-01-hibrid**. Amikor telepít egy profilt, az Azure Resource Manager PowerShell-modulok, amelyek megfelelnek a megadott profil vannak telepítve.

Azure Stack kompatibilis PowerShell-modulok az internethez csatlakoztatott, részben csatlakoztatva vagy leválasztott forgatókönyvek is telepítheti. Ez a cikk végigvezeti a részletes utasításokat a forgatókönyvekben.

## <a name="1-verify-your-prerequisites"></a>1. Az Előfeltételek ellenőrzése

Mielőtt elkezdené, az Azure Stack és a PowerShell, a következő előfeltételeknek kell rendelkeznie:

- **5.0-s verzió PowerShell** a verzió ellenőrzéséhez futtassa **$PSVersionTable.PSVersion** és hasonlítsa össze a **fő** verzió. Ha nem rendelkezik a PowerShell 5.0, kövesse a [Windows PowerShell telepítése](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

  > [!Note]
  > PowerShell 5.0 szükséges egy Windows-gépen.

- **Egy rendszergazda jogú parancssorban futtassa a Powershellt**.

- **PowerShell-galériából hozzáférés** hozzá kell férnie a [PowerShell-galériából](https://www.powershellgallery.com). A katalógus a PowerShell-tartalmak központi adattára. A **PowerShellGet** modul felderítése, telepítése, frissítése és PowerShell-összetevőket, például a modulok, DSC-erőforrások, szerepkörrel képességeket és szkripteket a PowerShell-galériában és egyéb titkos közzétételéhez szükséges parancsmagokat tartalmazza tárházak. PowerShell használatakor a leválasztott forgatókönyvek esetében kell kérnie a erőforrások kapcsolat rendelkező számítógépről az interneten és tárolja őket egy helyen érhető el kapcsolat nélküli számítógépre.

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. A PowerShell-galériából kisegítő ellenőrzése

Ha egy adattár, regisztrálva van a PSGallery ellenőrzése.

> [!Note]  
> Ezt a lépést Internet-hozzáférést igényel.

Nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagokat:

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Ha a tárház nincs regisztrálva, nyisson meg egy rendszergazda jogú PowerShell-munkamenetet, és futtassa a következő parancsot:

```powershell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Az Azure Stack PowerShell-modulok meglévő verzióinak eltávolítása

A szükséges verzió telepítése előtt győződjön meg arról, hogy távolítsa el minden korábban telepített Azure Stack AzureRM PowerShell-modulok. Az alábbi két módszer egyikével eltávolíthatja őket:

1. Távolítsa el a meglévő AzureRM PowerShell-modulok, zárja be az összes aktív PowerShell-munkamenetet, és futtassa a következő parancsmagokat:

    ```powershell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```

    Ha hibát észlel például "a modul már használatban van", zárja be a PowerShell-munkamenetekben, amelyek a modulokat használ, és futtassa újra a fenti szkript.

2. Indítsa el az összes mappa törlése `Azure` vagy `Azs.` a a `C:\Program Files\WindowsPowerShell\Modules` és `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` mappákat. Ezek a mappák törlése eltávolítja a bármely meglévő PowerShell-modulok.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Csatlakoztatva: Az Azure Stack PowerShell telepítése az internetkapcsolattal rendelkező

Azure Stack 1901 vagy újabb verziójára AzureRM 2.4.0 verziót használja. Emellett az AzureRM-modulok, telepítse az Azure Stack-specifikus PowerShell-modulok. Az API-verzióprofil és van szüksége az Azure Stack PowerShell-modulok függ az Azure Stack verzióját a rendszer futó.

Telepítési három lépésből áll:

1. Azure Stack verziójától függően az Azure Stack PowerShell telepítése
2. További tárolási szolgáltatások engedélyezése
3. Erősítse meg a PowerShell telepítése

### <a name="install-azure-stack-powershell"></a>Az Azure Stack PowerShell telepítése

Futtassa a következő PowerShell-parancsfájl ezeket a modulokat a fejlesztői munkaállomáson telepítendő:

- A buildek 1904-es vagy újabb:

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper
    
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Get-AzureRMProfile -Update
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.7.2
    ```
  
- Az Azure Stack 1903 vagy korábbi, csak az alábbi két modul telepítése:

    ```powershell  
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.

    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > - Az Azure Stack modul 1.7.1 verziója egy használhatatlanná tévő változást tartalmazó kiadás. Migrálhat az Azure Stack 1.6.0-s tekintse meg a [áttelepítési útmutató](https://aka.ms/azspshmigration171).
    > - A Remove-AzureRmStorageAccount parancsmag használhatatlanná tévő változást az AzureRm modul verziója 2.4.0 tartalmaz. Ennek a parancsmagnak - Force paraméterrel adható meg a tárfiók megerősítés nélküli eltávolítása.
    > - Nem kell telepíteni **AzureRM.Bootstrapper** a az Azure stack 1901 vagy újabb verziói a modulok telepítéséhez.
    > - Ne telepítse a 2018-03-01-hibrid profil a fenti AzureRM-modulok használata az Azure Stack 1901 vagy újabb verziójára mellett.

- Az Azure Stack verzió 1811, telepítse a profilt használó **AzureRM.Bootstrapper**, a verziók szerepelnek a parancsmagok mellett:

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

### <a name="enable-additional-storage-features"></a>További tárolási szolgáltatások engedélyezése

Szeretné, hogy a további tárolási funkciók (csatlakoztatott szakaszban említett), használja a letöltési és a következő csomagok telepítéséhez.

```powershell
# Install the Azure.Storage module version 4.5.0
Install-Module -Name Azure.Storage -RequiredVersion 4.5.0 -Force -AllowClobber

# Install the AzureRm.Storage module version 5.0.4
Install-Module -Name AzureRM.Storage -RequiredVersion 5.0.4 -Force -AllowClobber

# Remove incompatible storage module installed by AzureRM.Storage
Uninstall-Module Azure.Storage -RequiredVersion 4.6.1 -Force

# Load the modules explicitly specifying the versions
Import-Module -Name Azure.Storage -RequiredVersion 4.5.0
Import-Module -Name AzureRM.Storage -RequiredVersion 5.0.4
```

### <a name="confirm-the-installation-of-powershell"></a>Erősítse meg a PowerShell telepítése

Erősítse meg a telepítést a következő parancs futtatásával:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Ha a telepítés sikeres, a kimenetben az AzureRM- és AzureStack modulok jelennek meg.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Megszakadt: Telepítse a PowerShell internetkapcsolat nélkül

Leválasztott forgatókönyvekben először töltse le a PowerShell-modulok olyan gépre, amely rendelkezik internetkapcsolattal, és utána az Azure Stack Development Kit telepítéséhez.

Jelentkezzen be a számítógépre az internetkapcsolattal rendelkező, és az Azure Resource Manager és az Azure Stack-csomagok letöltése, Azure Stack verziójától függően az alábbi parancsfájlok használatával.

Telepítés négy lépésből áll:

1. Csatlakoztatott gépek az Azure Stack PowerShell telepítése
2. További tárolási szolgáltatások engedélyezése
3. A PowerShell-csomagok a kapcsolat nélküli munkaállomáson átviteli
4. Erősítse meg a PowerShell telepítése

### <a name="install-azure-stack-powershell"></a>Az Azure Stack PowerShell telepítése

- Az Azure Stack 1901 vagy újabb verziója.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > Az Azure Stack modul 1.7.1 verziószáma használhatatlanná tévő változást. 1.2.9-es migrálhat 1.6.0-s tekintse meg a [áttelepítési útmutató](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).

  - Az Azure Stack 1811 vagy valamely korábbi rendszerrel.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

  - Az Azure Stack 1809 vagy valamely korábbi rendszerrel.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ```

    > [!NOTE]
    > Internetkapcsolat nélküli gépekre javasoljuk, hogy végrehajtása a következő parancsmagot a telemetriai adatok gyűjtésének letiltása. A parancsmagok a teljesítmény csökkenése nélkül a telemetriai adatok gyűjtésének letiltása tapasztalhat. Ez a tulajdonság csak azoknál a gépeknél internetkapcsolat nélküli vonatkozik
    > ```powershell
    > Disable-AzureRmDataCollection
    > ```

### <a name="enable-additional-storage-features"></a>További tárolási szolgáltatások engedélyezése

Szeretné, hogy a további tárolási funkciók (csatlakoztatott szakaszban említett), használja a letöltési és a következő csomagok telepítéséhez.

```powershell
$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azure.Storage -Path $Path -Force -RequiredVersion 4.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRm.Storage -Path $Path -Force -RequiredVersion 5.0.4
```

### <a name="add-your-packages-to-your-workstation"></a>A csomagok hozzáadása a munkaállomáson

1. Másolja a letöltött csomagokat egy USB-eszközt.

2. Jelentkezzen be a kapcsolat nélküli munkaállomáson, és másolja a csomagokat az USB-eszközt egy olyan helyre a munkaállomáson.

3. Most már ezen a helyen az alapértelmezett tárház regisztrálja, és a tárházból az AzureRM- és az AzureStack modulok telepítéséhez:

   ```powershell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

### <a name="confirm-the-installation-of-powershell"></a>Erősítse meg a PowerShell telepítése

Erősítse meg a telepítést a következő parancs futtatásával:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Proxykiszolgáló használata a PowerShell konfigurálása

Az Internet eléréséhez proxykiszolgáló használatát igénylő forgatókönyvekben először konfigurálnia kell a PowerShell használata a meglévő proxykiszolgáló:

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssorban.
2. Futtassa az alábbi parancsot:

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>További lépések

- [Azure Stack-eszközök letöltése a githubról](azure-stack-powershell-download.md)
- [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](../user/azure-stack-powershell-configure-user.md)
- [Az Azure Stack-operátorokról PowerShell-környezet konfigurálása](azure-stack-powershell-configure-admin.md)
- [Az Azure Stackben API-verzióprofilok kezelése](../user/azure-stack-version-profiles.md)
