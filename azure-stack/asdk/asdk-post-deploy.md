---
title: Üzembe helyezési konfigurációk a Azure Stack Development Kit (ASDK) számára | Microsoft Docs
description: A Azure Stack Development Kit telepítésének (ASDK) telepítése után javasolt konfigurációs módosításokat ismerteti.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 07/31/2019
ms.openlocfilehash: cbf9872fa75013fdb3e933c102b813924d396a83
ms.sourcegitcommit: bf4d265a3522cbfdd9dd295a0f4ad0daf2ed5eca
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68692099"
---
# <a name="post-asdk-installation-configuration-tasks"></a>ASDK-telepítési konfigurációs feladatok közzététele

[A Azure stack Development Kit (ASDK) telepítése](asdk-install.md)után néhány ajánlott telepítés utáni konfigurációs módosítást kell végrehajtania, miközben a ASDK-gazdaszámítógép AzureStack\AzureStackAdmin bejelentkezett.

## <a name="install-azure-stack-powershell"></a>Az Azure Stack PowerShell telepítése

Azure Stack kompatibilis Azure PowerShell modulok szükségesek a Azure Stack használatához.

A Azure Stack PowerShell-parancsai a PowerShell-galériaon keresztül települnek. A PSGallery adattár regisztrálásához nyisson meg egy rendszergazda jogú PowerShell-munkamenetet, és futtassa a következő parancsot:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Az API-verziók profiljaival Azure Stack kompatibilis AzureRM-modulokat is megadhat.  Az API-verziók profiljai lehetővé teszik az Azure és a Azure Stack közötti verziók közötti különbségek kezelését. Az API-verziók egy adott API-verzióval rendelkező AzureRM PowerShell-modulok készlete. A PowerShell-galériaon keresztül elérhető **AzureRM. BootStrapper** modul PowerShell-parancsmagokat biztosít, amelyek az API-verzió profiljainak használatához szükségesek.

A legújabb Azure Stack PowerShell-modult internetkapcsolattal rendelkező vagy anélkül is telepítheti a ASDK gazdagéphez:

> [!IMPORTANT]
> A szükséges verzió telepítése előtt győződjön meg arról, hogy [eltávolította a meglévő Azure PowerShell modulokat](../operator/azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **Internetkapcsolatot** a ASDK gazdagép számítógépén. Futtassa a következő PowerShell-szkriptet a modulok telepítéséhez a fejlesztői csomag telepítéséhez:


  ```powershell  
  Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
  Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose

  # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
  Install-Module -Name AzureRM.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
  Install-Module -Name AzureStack -RequiredVersion 1.7.2
  ```

  Ha a telepítés sikeres, a AzureRM és a AzureStack modulok megjelennek a kimenetben.

- **Nincs internetkapcsolat** a ASDK gazdagép számítógépén. Leválasztott forgatókönyv esetén először le kell töltenie a PowerShell-modulokat egy internetkapcsolattal rendelkező gépre az alábbi PowerShell-parancsok használatával:

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

A ASDK-telepítés sikerességének biztosításához a következő lépéseket követve használhatja a test-AzureStack parancsmagot:

1. Jelentkezzen be AzureStack\AzureStackAdmin-ként a ASDK-gazdaszámítógépen.
2. Nyissa meg a PowerShellt rendszergazdaként (nem PowerShell ISE).
3. Futtassa: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Futtassa: `Test-AzureStack`

A tesztek végrehajtása eltarthat néhány percig. Ha a telepítés sikeres volt, a kimenet az alábbihoz hasonló módon fog kinézni:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Ha hiba történt, kövesse a hibaelhárítási lépéseket a Súgó beszerzéséhez.

## <a name="enable-multi-tenancy"></a>A több-bérlős üzemmód engedélyezése

Az Azure AD-t használó központi telepítések esetén [engedélyeznie kell a több-bérlős](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy) telepítést a ASDK-telepítéshez.

> [!NOTE]
> Ha a rendszergazda vagy a felhasználói fiókok a Azure Stack regisztrálásához használt tartománytól eltérő tartományban vannak, akkor a rendszer egy Azure Stack portálra jelentkezik be, a Azure Stack regisztrálásához használt tartománynevet hozzá kell fűzni a portál URL-címéhez. Ha például Azure stack regisztrálva van a fabrikam.onmicrosoft.com-ben, és a felhasználói fiókja be admin@contoso.comvan jelentkezve, a felhasználói portálra való bejelentkezéshez használt URL-cím https://portal.local.azurestack.external/fabrikam.onmicrosoft.com a következő lesz:.

## <a name="next-steps"></a>További lépések

[A ASDK regisztrálása az Azure-ban](asdk-register.md)
