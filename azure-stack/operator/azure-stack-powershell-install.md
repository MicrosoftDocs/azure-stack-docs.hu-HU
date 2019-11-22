---
title: A PowerShell telepítése Azure Stackhoz | Microsoft Docs
description: Ismerje meg, hogyan telepítheti a PowerShellt Azure Stackhoz.
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
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 813cfb72a2fad2b22dfce5baff8680b30d2c599d
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74298816"
---
# <a name="install-powershell-for-azure-stack"></a>A PowerShell telepítése az Azure Stackhez

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Azure PowerShell olyan parancsmagokat biztosít, amelyek a Azure Resource Manager modellt használják a Azure Stack-erőforrások kezeléséhez.

A felhővel való együttműködéshez Azure Stack kompatibilis PowerShell-modulokat kell telepítenie. Azure Stack a globális Azure-ban használt újabb **AzureAZ** modul helyett a **AzureRM** modult használja. Az Azure Stack erőforrás-szolgáltatóhoz tartozó kompatibilis végpontok megadásához *API-profilokat* is kell használnia.

Az API-profilok lehetőséget biztosítanak az Azure és a Azure Stack közötti verziók közötti különbségek kezelésére. Az API-verzió profilja Azure Resource Manager PowerShell-modulok meghatározott API-verziókkal. Minden egyes felhőalapú platformon támogatott API-verzió-profilok szerepelnek. A Azure Stack például egy adott profil verzióját (például **2019-03-01-Hybrid**) támogatja. Profil telepítésekor a rendszer telepíti a megadott profilhoz tartozó Azure Resource Manager PowerShell-modulokat.

Azure Stack kompatibilis PowerShell-modulokat az internethez csatlakoztatott, részben csatlakoztatott vagy leválasztott forgatókönyvekhez is telepíthet. Ez a cikk részletesen ismerteti ezeket a forgatókönyveket.

## <a name="1-verify-your-prerequisites"></a>1. Ellenőrizze az előfeltételeket

A Azure Stack és a PowerShell használatának megkezdése előtt a következő előfeltételeket kell megadnia:

- **PowerShell 5,0-es verzió** <br>
A verziójának vizsgálatához futtassa a **$PSVersionTable. PSVersion** parancsot, és hasonlítsa **össze a** főverziót. Ha nem rendelkezik a PowerShell 5,0-rel, kövesse a [Windows PowerShell telepítése](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)című témakört.

  > [!Note]
  > A PowerShell 5,0 használatához Windows rendszerű gép szükséges.

- **Futtassa a PowerShellt egy rendszergazda jogú parancssorban**.

- **PowerShell-galéria hozzáférés** <br>
  Hozzá kell férnie a [PowerShell-galériahoz](https://www.powershellgallery.com). A gyűjtemény a PowerShell-tartalmak központi tárháza. A **PowerShellGet** modul parancsmagokat tartalmaz a PowerShell-összetevők felfedéséhez, telepítéséhez, frissítéséhez és közzétételéhez. Ilyen összetevők például a modulok, a DSC-erőforrások, a szerepkör-képességek, valamint a PowerShell-galéria és más privát adattárakból származó parancsfájlok. Ha a PowerShellt leválasztott forgatókönyvben használja, le kell kérnie az erőforrásokat egy internetkapcsolattal rendelkező gépről, és a leválasztott gép számára elérhető helyen kell tárolnia azokat.

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. a PowerShell-galéria kisegítő lehetőségek ellenőrzése

Annak ellenőrzése, hogy a PSGallery regisztrálva van-e adattárként.

> [!Note]  
> Ehhez a lépéshez internet-hozzáférés szükséges.

Nyisson meg egy rendszergazda jogú PowerShell-parancssort, és futtassa a következő parancsmagokat:

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Ha a tárház nincs regisztrálva, nyisson meg egy emelt szintű PowerShell-munkamenetet, és futtassa a következő parancsot:

```powershell
Register-PSRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. távolítsa el a Azure Stack PowerShell-modulok meglévő verzióit

A szükséges verzió telepítése előtt győződjön meg arról, hogy eltávolította a korábban telepített Azure Stack AzureRM PowerShell-modulokat. Távolítsa el a modulokat a következő két módszer egyikének használatával:

1. A meglévő AzureRM PowerShell-modulok eltávolításához zárjunk be minden aktív PowerShell-munkamenetet, és futtassa a következő parancsmagokat:

    ```powershell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```

    Ha olyan hibát talál, mint például a "a modul már használatban van", akkor a modulokat használó PowerShell-munkameneteket, majd futtassa újra a fenti szkriptet.

2. Törölje az összes olyan mappát, amely `Azure` vagy `Azs.` a `C:\Program Files\WindowsPowerShell\Modules` és `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` mappákból. A mappák törlése eltávolítja a meglévő PowerShell-modulokat.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. csatlakoztatott: telepítse a PowerShellt Azure Stack internetkapcsolattal

A szükséges API-verzió profilja és Azure Stack PowerShell-modulok a futtatott Azure Stack verziójától függenek.

### <a name="install-azure-stack-powershell"></a>Az Azure Stack PowerShell telepítése

Futtassa a következő PowerShell-szkriptet a modulok fejlesztői munkaállomáson történő telepítéséhez:

- Azure Stack 1904 vagy újabb verzió esetén:

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.7.2
    ```
  
- Azure Stack 1903-es vagy korábbi verzió esetén a következő két modult kell telepítenie:

    ```powershell  
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.

    Install-Module -Name AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > - A Azure Stack modul 1.7.1 verziója a feltörési változás kiadása. Azure Stack 1.6.0 való áttelepítéshez tekintse meg az [áttelepítési útmutatót](https://aka.ms/azspshmigration171).
    > - A AzureRM modul 2.4.0 verziója a Remove-AzureRmStorageAccount parancsmag megszakítási változásával jár. Ez a parancsmag arra vár, `-Force` paramétert kell megadni a Storage-fiók megerősítés nélküli eltávolításához.
    > - Nem kell telepítenie a **AzureRM. BootStrapper** modult a 1901-es vagy újabb verzióhoz Azure stack tartozó modulok telepítéséhez.
    > - Ne telepítse a 2018-03-01-Hybrid profilt a fenti AzureRM-modulok Azure Stack 1901-es vagy újabb verzióra való használata mellett.

### <a name="confirm-the-installation-of-powershell"></a>A PowerShell telepítésének megerősítése

A következő parancs futtatásával erősítse meg a telepítést:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Ha a telepítés sikeres, a `AzureRM` és `AzureStack` modulok megjelennek a kimenetben.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. leválasztva: a PowerShell telepítése internetkapcsolat nélkül

Leválasztott forgatókönyv esetén először töltse le a PowerShell-modulokat egy internetkapcsolattal rendelkező gépre. Ezután a telepítéshez továbbítja azokat a Azure Stack Development Kitba (ASDK).

Jelentkezzen be egy internetkapcsolattal rendelkező számítógépre, és a következő parancsfájlok segítségével töltse le a Azure Resource Manager és Azure Stack csomagokat a Azure Stack verziójától függően.

A telepítésnek négy lépése van:

1. Telepítse a Azure Stack PowerShellt egy csatlakoztatott gépre.
2. Engedélyezze a további tárolási funkciókat.
3. A PowerShell-csomagok átvitele a kapcsolat nélküli munkaállomásra.
4. Adja meg manuálisan a NuGet-szolgáltatót a kapcsolat nélküli munkaállomáson.
5. Erősítse meg a PowerShell telepítését.

### <a name="install-azure-stack-powershell"></a>Az Azure Stack PowerShell telepítése

- Azure Stack 1904 vagy újabb.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.2
    ```

- Azure Stack 1903 vagy korábbi.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > A Azure Stack modul 1.7.1-verziója egy megszakítási változás. Azure Stack 1.6.0 való áttelepítéshez tekintse meg az [áttelepítési útmutatót](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).

    > [!NOTE]
    > Internetkapcsolat nélküli gépeken a következő parancsmag futtatását javasoljuk a telemetria-adatgyűjtés letiltásához. A parancsmagok teljesítménybeli romlása a telemetria-adatgyűjtés letiltása nélkül is felmerülhet. Ez csak az internetkapcsolat nélküli gépek esetében érvényes
    > ```powershell
    > Disable-AzureRmDataCollection
    > ```

### <a name="add-your-packages-to-your-workstation"></a>Csomagok hozzáadása a munkaállomáshoz

1. Másolja a letöltött csomagokat egy USB-eszközre.

2. Jelentkezzen be a kapcsolat nélküli munkaállomásra, és másolja a csomagokat az USB-eszközről a munkaállomás egyik helyére.

3. Adja meg manuálisan a NuGet-szolgáltatót a kapcsolat nélküli munkaállomáson. Útmutatásért lásd: [a NuGet-szolgáltató manuális betöltése olyan gépen, amely nem kapcsolódik az internethez](https://docs.microsoft.com/powershell/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet).

4. Regisztrálja ezt a helyet alapértelmezett tárházként, és telepítse a AzureRM és a `AzureStack` modult ebből a tárházból:

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

## <a name="next-steps"></a>További lépések

- [Azure Stack Tools letöltése a GitHubról](azure-stack-powershell-download.md)
- [A Azure Stack felhasználó PowerShell-környezetének konfigurálása](../user/azure-stack-powershell-configure-user.md)
- [A Azure Stack operátor PowerShell-környezetének konfigurálása](azure-stack-powershell-configure-admin.md)
- [API-verzióprofilok kezelése az Azure Stackben](../user/azure-stack-version-profiles.md)
