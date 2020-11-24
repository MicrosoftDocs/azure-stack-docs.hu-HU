---
title: A ASDK üzembe helyezés utáni konfigurációi
description: A Azure Stack Development Kit (ASDK) telepítése utáni ajánlott konfigurációs módosítások ismertetése.
author: justinha
ms.topic: article
ms.date: 11/14/2020
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/14/2020
ms.openlocfilehash: efb7d803d8f0c29269bae3e147fc48eec3e29eb3
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517310"
---
# <a name="post-deployment-configurations-for-asdk"></a>A ASDK üzembe helyezés utáni konfigurációi

Miután [telepítette a Azure stack Development Kit (ASDK)](asdk-install.md), néhány ajánlott utólagos telepítési konfigurációt kell végrehajtania, miközben a ASDK-gazdaszámítógép AzureStack\AzureStackAdmin bejelentkezett.

## <a name="install-azure-stack-powershell"></a>Az Azure Stack PowerShell telepítése

Azure Stack kompatibilis Azure PowerShell modulok szükségesek a Azure Stack használatához.

A Azure Stack PowerShell-parancsai a PowerShell-galériaon keresztül települnek. A PSGallery adattár regisztrálásához nyisson meg egy rendszergazda jogú PowerShell-munkamenetet, és futtassa a következő parancsot:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Az API-verziók profiljaival megadásával Azure Stack kompatibilis az modulok.  Az API-verziók profiljai lehetővé teszik az Azure és a Azure Stack közötti verziók közötti különbségek kezelését. Az API-verzió profil az az PowerShell-modulok meghatározott API-verziókkal. Az az **. BootStrapper** modul, amely a PowerShell-Galéria keresztül érhető el, PowerShell-parancsmagokat biztosít, amelyek szükségesek az API-verzió profiljainak használatához.

A legújabb Azure Stack PowerShell-modult internetkapcsolattal rendelkező vagy anélkül is telepítheti a ASDK gazdagéphez:

> [!IMPORTANT]
> A szükséges verzió telepítése előtt győződjön meg arról, hogy [eltávolította a meglévő Azure PowerShell modulokat](../operator/powershell-install-az-module.md#3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules).

- **Internetkapcsolat** a ASDK gazdagépen: futtassa a következő PowerShell-szkriptet a modulok ASDK-telepítésre történő telepítéséhez:

### <a name="az-modules"></a>[Az modulok](#tab/az1)

  ```powershell  
  Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
  Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose

  # Install the Az.BootStrapper module. Select Yes when prompted to install NuGet
  Install-Module -Name Az.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzProfile -Profile 2019-03-01-hybrid -Force
  Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease
  ```

Ha a telepítés sikeres, az az és a AzureStack modulok megjelennek a kimenetben.

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm1)

  ```powershell  
  Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
  Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose

  # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
  Install-Module -Name AzureRM.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
  Install-Module -Name AzureStack -RequiredVersion 1.8.2
  ```

Ha a telepítés sikeres, a AureRM és a AzureStack modulok megjelennek a kimenetben.

---

- **Nincs internetkapcsolat** a ASDK gazdagép számítógépén: a leválasztott forgatókönyvekben először le kell töltenie a PowerShell-modulokat egy internetkapcsolattal rendelkező gépre az alábbi PowerShell-parancsok használatával:

### <a name="az-modules"></a>[Az modulok](#tab/az2)

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Az -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Ezután másolja a letöltött csomagokat a ASDK számítógépre, és regisztrálja a helyet az alapértelmezett tárházként, és telepítse az az és a AzureStack modult a tárházból:

  ```powershell  
  $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
  $RepoName = "MyNuGetSource"

  Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

  Install-Module Az -Repository $RepoName

  Install-Module AzureStack -Repository $RepoName
  ```

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm2)

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Ezután másolja a letöltött csomagokat a ASDK számítógépre, és regisztrálja a helyet az alapértelmezett tárházként, és telepítse a AzureRM és a AzureStack modult ebből a tárházból:

  ```powershell  
  $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
  $RepoName = "MyNuGetSource"

  Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

  Install-Module AzureRM -Repository $RepoName

  Install-Module AzureStack -Repository $RepoName
  ```

---

## <a name="download-the-azure-stack-tools"></a>A Azure Stack-eszközök letöltése

A [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) egy GitHub-tárház, amely PowerShell-modulokat tartalmaz az erőforrások Azure stack történő kezeléséhez és üzembe helyezéséhez. Az eszközöket az az PowerShell-modulok vagy a AzureRM-modulok használatával használhatja.

### <a name="az-modules"></a>[Az modulok](#tab/az3)

Ezen eszközök beszerzéséhez klónozott a GitHub-tárházat a `az` fiókirodából, vagy töltse le a **AzureStack-Tools** mappát a következő parancsfájl futtatásával:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/az.zip `
  -OutFile az.zip

# Expand the downloaded files.
expand-archive az.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-az

```
### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm3)

Ezen eszközök beszerzéséhez klónozott a GitHub-tárházat a `master` fiókirodából, vagy töltse le a **AzureStack-Tools** mappát a következő parancsfájl egy emelt szintű PowerShell-parancssorból történő futtatásával:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```
Az Azure Stack hub AzureRM moduljának használatával kapcsolatos további információkért lásd: a [PowerShell-AzureRM modul telepítése Azure stack hubhoz](../operator/azure-stack-powershell-install.md)

---

## <a name="validate-the-asdk-installation"></a>A ASDK telepítésének ellenőrzése

A ASDK-telepítés sikerességének ellenőrzéséhez használja a Test-AzureStack parancsmagot a következő lépések végrehajtásával:

1. Jelentkezzen be AzureStack\AzureStackAdmin-ként a ASDK-gazdaszámítógépen.
2. Nyissa meg a PowerShellt rendszergazdaként (nem PowerShell ISE).
3. Futtassa `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Futtassa `Test-AzureStack`

A tesztek végrehajtása eltarthat néhány percig. Ha a telepítés sikeres volt, a kimenet az alábbihoz hasonló módon fog kinézni:

![Tesztelés Azure Stack – sikeres telepítés](media/asdk-post-deploy/test-azurestack.png)

Ha hiba történt, kövesse a hibaelhárítási lépéseket a Súgó beszerzéséhez.

## <a name="enable-multi-tenancy"></a>A több-bérlős üzemmód engedélyezése

Az Azure AD-t használó központi telepítések esetén [engedélyeznie kell a több-bérlős](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy) telepítést a ASDK-telepítéshez.

> [!NOTE]
> Ha az Azure Stack regisztrálásához használt tartománytól eltérő rendszergazdai vagy felhasználói fiókok vannak használatban egy Azure Stack portálra való bejelentkezéshez, a Azure Stack regisztrálásához használt tartománynevet a portál URL-címéhez kell csatolni. Ha például Azure Stack regisztrálva van a fabrikam.onmicrosoft.com-ben, és a felhasználói fiókja be van jelentkezve admin@contoso.com , a felhasználói portálra való bejelentkezéshez használt URL-cím a következő lesz: https \: //Portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Következő lépések

[A ASDK regisztrálása az Azure-ban](asdk-register.md)
