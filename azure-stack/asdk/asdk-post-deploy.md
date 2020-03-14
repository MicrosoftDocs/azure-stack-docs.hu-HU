---
title: A ASDK üzembe helyezés utáni konfigurációi
description: A Azure Stack Development Kit (ASDK) telepítése utáni ajánlott konfigurációs módosítások ismertetése.
author: justinha
ms.topic: article
ms.date: 07/31/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 07/31/2019
ms.openlocfilehash: 8c0cd14dbeda4e2b7844aefaf10f4a8cf685d60d
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79294439"
---
# <a name="post-deployment-configurations-for-asdk"></a>A ASDK üzembe helyezés utáni konfigurációi

Miután [telepítette a Azure stack Development Kit (ASDK)](asdk-install.md), néhány ajánlott utólagos telepítési konfigurációt kell végrehajtania, miközben a ASDK-gazdaszámítógép AzureStack\AzureStackAdmin bejelentkezett.

## <a name="install-azure-stack-powershell"></a>Az Azure Stack PowerShell telepítése

Azure Stack kompatibilis Azure PowerShell modulok szükségesek a Azure Stack használatához.

A Azure Stack PowerShell-parancsai a PowerShell-galériaon keresztül települnek. A PSGallery adattár regisztrálásához nyisson meg egy rendszergazda jogú PowerShell-munkamenetet, és futtassa a következő parancsot:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Az API-verziók profiljaival Azure Stack kompatibilis AzureRM-modulokat lehet megadni.  Az API-verziók profiljai lehetővé teszik az Azure és a Azure Stack közötti verziók közötti különbségek kezelését. Az API-verziók egy adott API-verzióval rendelkező AzureRM PowerShell-modulok készlete. A PowerShell-galériaon keresztül elérhető **AzureRM. BootStrapper** modul PowerShell-parancsmagokat biztosít, amelyek az API-verzió profiljainak használatához szükségesek.

A legújabb Azure Stack PowerShell-modult internetkapcsolattal rendelkező vagy anélkül is telepítheti a ASDK gazdagéphez:

> [!IMPORTANT]
> A szükséges verzió telepítése előtt győződjön meg arról, hogy [eltávolította a meglévő Azure PowerShell modulokat](../operator/azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules).

- **Internetkapcsolat** a ASDK gazdagépen: futtassa a következő PowerShell-szkriptet a modulok ASDK-telepítésre történő telepítéséhez:


  ```powershell  
  Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
  Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose

  # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
  Install-Module -Name AzureRM.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
  Install-Module -Name AzureStack -RequiredVersion 1.8.0
  ```

  Ha a telepítés sikeres, a AzureRM és a AzureStack modulok megjelennek a kimenetben.

- **Nincs internetkapcsolat** a ASDK gazdagép számítógépén: a leválasztott forgatókönyvekben először le kell töltenie a PowerShell-modulokat egy internetkapcsolattal rendelkező gépre az alábbi PowerShell-parancsok használatával:

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

## <a name="download-the-azure-stack-tools"></a>A Azure Stack-eszközök letöltése

A [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) egy GitHub-tárház, amely PowerShell-modulokat tartalmaz az erőforrások Azure stack történő kezeléséhez és üzembe helyezéséhez. Az eszközök beszerzéséhez klónozott a GitHub-tárházat, vagy töltse le a AzureStack-Tools mappát a következő parancsfájl futtatásával:

  ```powershell
  # Change directory to the root directory.
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  Invoke-WebRequest `
    -Uri https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  Expand-Archive -Path master.zip -DestinationPath . -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>A ASDK telepítésének ellenőrzése

A ASDK üzembe helyezése sikerességének biztosításához használja a test-AzureStack parancsmagot a következő lépésekkel:

1. Jelentkezzen be AzureStack\AzureStackAdmin-ként a ASDK-gazdaszámítógépen.
2. Nyissa meg a PowerShellt rendszergazdaként (nem PowerShell ISE).
3. Futtatás: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Futtatás: `Test-AzureStack`

A tesztek végrehajtása eltarthat néhány percig. Ha a telepítés sikeres volt, a kimenet az alábbihoz hasonló módon fog kinézni:

![Tesztelés Azure Stack – sikeres telepítés](media/asdk-post-deploy/test-azurestack.png)

Ha hiba történt, kövesse a hibaelhárítási lépéseket a Súgó beszerzéséhez.

## <a name="enable-multi-tenancy"></a>A több-bérlős üzemmód engedélyezése

Az Azure AD-t használó központi telepítések esetén [engedélyeznie kell a több-bérlős](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy) telepítést a ASDK-telepítéshez.

> [!NOTE]
> Ha az Azure Stack regisztrálásához használt tartománytól eltérő rendszergazdai vagy felhasználói fiókok vannak használatban egy Azure Stack portálra való bejelentkezéshez, a Azure Stack regisztrálásához használt tartománynevet a portál URL-címéhez kell csatolni. Ha például Azure Stack regisztrálva van a fabrikam.onmicrosoft.com-ben, és a felhasználói fiók bejelentkezve admin@contoso.com, a felhasználói portálra való bejelentkezéshez használt URL-cím a következő lesz: https\://Portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Következő lépések

[A ASDK regisztrálása az Azure-ban](asdk-register.md)
