---
title: A PowerShell telepítése az Azure Stack hub modulhoz
description: Ismerje meg, hogyan telepítheti a PowerShellt Azure Stack hubhoz.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: c3e18ca850bb96791dbaa5d546864a11eb39dc62
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520484"
---
# <a name="install-powershell-az-module-for-azure-stack-hub"></a>A PowerShell telepítése az Azure Stack hub modulhoz

Ez a cikk azt ismerteti, hogyan telepítheti az Azure PowerShell az modulokat a PowerShellGet használatával. Az útmutatás Windows, macOS és Linux platformon használható.

Ha Azure Stack hub PowerShell-AzureRM modulját szeretné telepíteni, tekintse meg [a PowerShell-AzureRM modul telepítése Azure stack hubhoz](azure-stack-powershell-install.md)című témakört.

> [!IMPORTANT]
>  A PowerShell az modul jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Stack hub erőforrás-szolgáltatói számára a kompatibilis végpontok megadásához is *API-profilokat* kell használnia.
Az API-profilok lehetőséget biztosítanak az Azure és a Azure Stack hub közötti verziók közötti különbségek kezelésére. Az API-verzió profilja Azure Resource Manager PowerShell-modulok meghatározott API-verziókkal. Minden egyes felhőalapú platformon támogatott API-verzió-profilok szerepelnek. Az Azure Stack hub például egy adott profil verzióját támogatja, például **2019-03-01-Hybrid**. Profil telepítésekor a rendszer telepíti a megadott profilhoz tartozó Azure Resource Manager PowerShell-modulokat.

Az Azure Stack hub-kompatibilis PowerShell az a modulok az internethez csatlakoztatott, részben csatlakoztatott vagy leválasztott forgatókönyvekben telepíthetők. Ez a cikk részletesen ismerteti ezeket a forgatókönyveket.

## <a name="1-verify-your-prerequisites"></a>1. Ellenőrizze az előfeltételeket

Az Azure PowerShell Windows rendszeren a PowerShell 5.1 vagy újabb, más platformon a PowerShell Core 6.x vagy újabb verziójával működik. Telepítenie kell a [PowerShell Core legújabb verzióját](/powershell/scripting/install/installing-powershell#powershell-core) az operációs rendszer számára. A Azure PowerShell nem rendelkezik további követelményekkel a PowerShell Core-on való futtatáskor.

A PowerShell verziójának megtekintéséhez futtassa az alábbi parancsot:

```powershell  
$PSVersionTable.PSVersion
```

### <a name="prerequisites-for-windows"></a>A Windows előfeltételei
A Azure PowerShell használata a PowerShell 5,1-ben Windows rendszeren:

1. Frissítsen a [Windows PowerShell 5.1-re](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell), ha szükséges. Ha Windows 10 rendszert használ, már telepítve van a PowerShell 5.1.
2. Telepítse a [.NET-keretrendszer 4.7.2-es vagy újabb verzióját](/dotnet/framework/install).
3. Győződjön meg arról, hogy a PowerShellGet legújabb verziója van telepítve. Futtassa az `Update-Module PowerShellGet -Force` parancsot.

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. a PowerShell-galéria kisegítő lehetőségek ellenőrzése

Alapértelmezés szerint a PowerShell-galéria nincs konfigurálva a PowerShellGet megbízható adattáraként. A PSGallery első használatakor a következő üzenet jelenik meg:

```Output
Untrusted repository

You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the `Set-PSRepository` cmdlet.

Are you sure you want to install the modules from 'PSGallery'?
[Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "N"):
```

A telepítés folytatásához válassza a `Yes` vagy a `Yes to All` lehetőséget.

Az Az modul az Azure PowerShell-parancsmagok összesített modulja. A modul telepítésével letölti az összes elérhető Azure Resource Manager-modult, és használatra elérhetővé teszi a parancsmagjaikat.

Először [telepítse a PowerShell Core 6. x vagy újabb verzióját.](/powershell/scripting/install/installing-powershell-core-on-windows)

Ezután a PowerShell Core-munkamenetből telepítse az az modult az aktuális felhasználóhoz. Ez az ajánlott telepítési hatókör.

```powershell  
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Ha a modult egy rendszer összes felhasználójára telepíti, emelt szintű jogosultságok szükségesek. Indítsa el a PowerShell-munkamenetet a **Futtatás rendszergazdaként** a Windows rendszerben, vagy használja a `sudo` parancsot MacOS vagy Linux rendszeren:

```powershell  
Install-Module -Name Az -AllowClobber -Scope AllUsers
```

## <a name="3-connected-install-with-internet-connectivity"></a>3. csatlakozás: telepítés internetkapcsolattal

Az Azure Stack az Module Azure Stack hub 2002-as vagy újabb verziójával fog működni. Emellett a Azure Stack az modul a PowerShell 5,1-es vagy újabb verzióját fogja működni Windows-gépen, vagy a PowerShell 6. x-es vagy újabb verzióját egy Linux-vagy macOS-platformon. A PowerShellGet-parancsmagok használata az előnyben részesített telepítési módszer. Ez a módszer ugyanúgy működik a támogatott platformokon. 

Futtassa a következő parancsot egy PowerShell-munkamenetből:

```powershell  
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.BootStrapper
Use-AzProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 2.0.0-preview -AllowPrerelease
```

> [!Note]  
> Azure Stack hub-modul 2.0.0-verziójának feltörési változása. A részletekért tekintse meg az [áttelepítés a AzureRM-ről Azure PowerShell az az Azure stack hub-ban](migrate-azurerm-az.md) című témakört.

> [!WARNING]  
> Egyszerre nem lehet egyszerre telepíteni a PowerShell 5,1-AzureRM és az az-modulokat. Ha az AzureRM-nek továbbra is elérhetőnek kell maradnia a rendszeren, telepítse a PowerShell Core 6.x vagy újabb verziójához készült Az modult. Ehhez [telepítse a PowerShell Core 6.x vagy újabb](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows) verzióját, majd kövesse az itt ismertetett utasításokat a PowerShell Core-terminálban.


## <a name="5-disconnected-install-without-internet-connection"></a>5. leválasztva: telepítés internetkapcsolat nélkül

Néhány környezetben nem lehet csatlakozni a PowerShell-galériához. Ezekben a helyzetekben offline telepítést végezhet az alábbi módszerek valamelyikével:

* Töltse le a modulokat a hálózat egy másik helyére, és használja azt telepítési forrásként.
  Ez lehetővé teszi a PowerShell-modulok gyorsítótárazását egyetlen kiszolgálón vagy fájlmegosztást, hogy a PowerShellGet-mel legyenek telepítve a leválasztott rendszerekhez. Megtudhatja, hogyan állíthat be helyi adattárat, és hogyan telepíthet leválasztott rendszerekre [helyi PowerShellGet-adattárakkal](https://docs.microsoft.com/powershell/scripting/gallery/how-to/working-with-local-psrepositories).
* [Letöltheti az Azure PowerShell MSI-t](https://docs.microsoft.com/powershell/azure/install-az-ps-msi) a hálózathoz csatlakoztatott egyik gépre, majd a telepítőt olyan rendszerekre másolhatja, amelyek nem férnek hozzá a PowerShell-galériához. Ne feledje, hogy az MSI-telepítő csak a PowerShell 5.1-hez, Windows rendszeren használható.
* A modult a [Save-Module](https://docs.microsoft.com/powershell/module/PowershellGet/Save-Module) paranccsal egy fájlmegosztásra mentheti, vagy egy másik forrásra mentheti, és más gépekre másolhatja:

  ```powershell  
  Save-Module -Name Az -Path '\\server\share\PowerShell\modules' -Force
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

A parancsmagokat és a kód mintáit a AzureRM alapján is használhatja. Azonban módosítania kell a modulok és a parancsmagok nevét. A modul neve megváltozott, így a `AzureRM` és az Azure `Az`válik, és ugyanaz a parancsmagok. Például a `AzureRM.Compute` modul átnevezve lett `Az.Compute`re.` New-AzureRMVM` ` New-AzVM` lett, a `Get-AzureStorageBlob` pedig `Get-AzStorageBlob`.

A AzurRM-szkriptek az az-ba való áthelyezésével és a Azure Stack hub AZ AZ modulban történő módosításával kapcsolatos részletesebb megbeszélések és útmutatás: [áttelepítés az AzureRM-ből Azure PowerShell az-ba](migrate-azurerm-az.md).

## <a name="next-steps"></a>Következő lépések

- [Azure Stack hub-eszközök letöltése a GitHubról](azure-stack-powershell-download.md)
- [A Azure Stack hub felhasználói PowerShell-környezetének konfigurálása](../user/azure-stack-powershell-configure-user.md)
- [Az Azure Stack hub-kezelő PowerShell-környezetének konfigurálása](azure-stack-powershell-configure-admin.md)
- [API-verziók profiljainak kezelése Azure Stack hub-ban](../user/azure-stack-version-profiles.md)

