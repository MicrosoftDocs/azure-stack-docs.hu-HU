---
title: Szolgáltatástelepítési konfigurációk az Azure Stack Development Kit (ASDK) a közzététele |} A Microsoft Docs
description: Az Azure Stack Development Kit (ASDK) telepítése után győződjön meg arról is, ajánlott konfigurációs változásait ismerteti.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/10/2018
ms.openlocfilehash: dd16d08e2e262b9aa31a1d59cb8ed59868608fbb
ms.sourcegitcommit: 9f5157ce6b938d190ef9df5a2df4342266ca5545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66174345"
---
# <a name="post-asdk-installation-configuration-tasks"></a>ASDK telepítés utáni konfigurációs feladatok

Miután [telepítése az Azure Stack Development Kit (ASDK)](asdk-install.md), meg kell néhány ajánlott telepítés utáni konfigurációs módosítások közben a ASDK gazdagépen AzureStack\AzureStackAdmin néven jelentkezett be.

## <a name="install-azure-stack-powershell"></a>Az Azure Stack PowerShell telepítése

Az Azure Stack kompatibilis az Azure PowerShell-modulok használata az Azure Stack van szükség.

Azure Stack PowerShell-parancsokat a PowerShell-galériából keresztül telepíti. A PSGallery tárház regisztrálása nyisson meg egy rendszergazda jogú PowerShell-munkamenetet, és futtassa a következő parancsot:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

API-verzióprofilok segítségével adja meg az Azure Stack-kompatibilis AzureRM-modulok.  API-verzióprofilok kezelése az Azure és az Azure Stack közötti terveztek. Egy API-verzióprofil egy adott API-verziók az AzureRM PowerShell-modulok. A **AzureRM.BootStrapper** modul, amely a PowerShell-galériából érhető el az API-verzióprofilok működéséhez szükséges PowerShell-parancsmagokat kínál.

Telepítheti a legújabb Azure Stack PowerShell-modul vagy a ASDK gazdagépnek internetkapcsolat nélkül:

> [!IMPORTANT]
> A szükséges verzió telepítése előtt győződjön meg arról, hogy [távolítsa el minden meglévő Azure PowerShell-modulok](../operator/azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **Internetkapcsolattal rendelkező** ASDK állomásról. Futtassa a következő PowerShell-parancsfájlt a development kit telepítésen ezeket a modulokat telepíteni:

  - A buildek 1904-es vagy újabb:

    ```powershell  
      # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
      Install-Module -Name AzureRM.BootStrapper

      # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
      Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
      Install-Module -Name AzureStack -RequiredVersion 1.7.2
    ```

  - Az Azure Stack 1903 vagy korábbi, csak az alábbi két modul telepítése:

    ```powershell
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Install-Module -Name AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > Az Azure Stack modul 1.7.1 verziószáma használhatatlanná tévő változást. Migrálhat az Azure Stack 1.6.0-s tekintse meg a [áttelepítési útmutató](https://aka.ms/azspshmigration171).

  - Az Azure Stack 1811:

    ``` PowerShell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.6.0.
    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

  Ha a telepítés sikeres, a kimenetben az AzureRM- és AzureStack modulok jelennek meg.

- **Internetkapcsolat nélküli** ASDK állomásról. A leválasztott forgatókönyvekben, először le kell töltenie a PowerShell-modulok a gépre, amely rendelkezik internetkapcsolattal, a következő PowerShell-parancsokkal:

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Ezután másolja a letöltött csomagokat ASDK számítógépre, és regisztrálja a hely alapértelmezett tárházaként, és a tárházból az AzureRM- és az AzureStack modulok telepítéséhez:

    ```powershell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

    Install-Module AzureRM -Repository $RepoName

    Install-Module AzureStack -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Az Azure Stack-eszközök letöltése

[1.2.9-es-eszközök](https://github.com/Azure/AzureStack-Tools) van egy GitHub-adattár, amely futtatja a PowerShell-modulok kezelésére és üzembe erőforrásokat az Azure Stack. Ezek az eszközök klónozza a GitHub-adattárát, vagy töltse le az AzureStack-eszközök mappát a következő szkript futtatásával:

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

## <a name="validate-the-asdk-installation"></a>A ASDK a telepítés ellenőrzése

Győződjön meg arról, hogy a ASDK központi telepítés sikeres volt-e, használhatja a Test-AzureStack parancsmag az alábbi lépéseket:

1. Jelentkezzen AzureStack\AzureStackAdmin ASDK a gazdagépen.
2. Nyissa meg a Powershellt rendszergazdaként (nem PowerShell ISE-ben).
3. Futtassa: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Futtassa: `Test-AzureStack`

A tesztek végrehajtásához néhány percet igénybe vehet. Ha a telepítés sikeres volt, a következőhöz hasonló eredményt fog:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Hiba lépett fel, ha a hibaelhárítási lépésekkel segítséget kaphat.

## <a name="reset-the-password-expiration-policy"></a>Jelszó-elévülési szabályzatának alaphelyzetbe állítása

Ahhoz, hogy, hogy a jelszó a development kit gazdagép le nem jár, a kiértékelési időszak vége előtt, kövesse az alábbi lépéseket a ASDK üzembe helyezése után.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Jelszó-elévülési szabályzatának módosítása a Powershell

Futtassa a parancsot egy rendszergazda jogú Powershell-konzolt:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>A manuális jelszó-elévülési szabályzatának módosítása

1. Nyissa meg a fejlesztői csomag gazdagép **Csoportházirend kezelése** (GPMC. Az MMC), és keresse meg **Csoportházirend kezelése** - **erdő: azurestack.local** - **tartományok**  -  **azurestack.local**.
2. Kattintson a jobb gombbal **alapértelmezett tartományi házirend** kattintson **szerkesztése**.
3. Lépjen a a Csoportházirendkezelés-szerkesztő, **számítógép konfigurációja** - **házirendek** - **Windows beállítások**  -  **Biztonsági beállítások** - **Fiókházirend** - **jelszóházirend**.
4. A jobb oldali ablaktáblában kattintson duplán a **jelszó maximális kora**.
5. Az a **jelszó maximális kora tulajdonságok** párbeszédpanelen módosítsa a **a jelszó lejár** értéket a következőre **180**, és kattintson a **OK**.

![Csoportházirend kezelése konzol](media/asdk-post-deploy/gpmc.png)

## <a name="enable-multi-tenancy"></a>A több-bérlős üzemmód engedélyezése

Az Azure AD-vel telepítések esetén kell [több bérlős üzemmód engedélyezése](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy) a ASDK telepítésekor.

> [!NOTE]
> Ha eltérő Azure Stack regisztrálásához használt rendszergazda vagy a felhasználói fiók segítségével jelentkezzen be az Azure Stack Portalra, a tartománynév regisztrálása az Azure Stack segítségével kell hozzáfűzi a portál URL-címét. Például, ha a fabrikam.onmicrosoft.com Azure Stack regisztrálva van, és a bejelentkezett felhasználói fiók van admin@contoso.com, a használatával jelentkezzen be a felhasználói portál URL-cím: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>További lépések

[A ASDK regisztrálása az Azure-ral](asdk-register.md)
