---
title: Azure Stack hub-operátor hozzáférési munkaállomása
description: Megtudhatja, hogyan tölthet le és konfigurálhat egy Azure Stack hub-kezelői hozzáférési munkaállomást.
author: mattbriggs
ms.topic: article
ms.date: 03/05/2021
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 03/05/2021
ms.openlocfilehash: 957b5860853b12040bfc13c4380290ad27e53a42
ms.sourcegitcommit: 7ee28fad5b8ba628b1a7dc3d82cabfc36aa62f0d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2021
ms.locfileid: "102250289"
---
# <a name="azure-stack-hub-operator-access-workstation"></a>Azure Stack hub-operátor hozzáférési munkaállomása

A kezelői hozzáférési munkaállomás (OAW) egy virtuális gép (VM) üzembe helyezésére használatos egy Azure Stack hub--hardveres életciklus-gazdagépen (HLH) vagy bármely más, Microsoft Hyper-V futtató gépen. Szükség van hálózati kapcsolatra a Azure Stack hub-végpontokkal, amelyeket kezelő vagy felhasználói forgatókönyvekhez kell használni.

A OAW VM egy opcionális virtuális gép, amely nem szükséges Azure Stack hub működéséhez. A cél az, hogy a legújabb eszközöket biztosítson a kezelőknek vagy a felhasználóknak, amikor az Azure Stack hub-t használják.

## <a name="oaw-scenarios"></a>OAW-forgatókönyvek

Az alábbi táblázatok felsorolják a OAW általános forgatókönyveit. A OAW való kapcsolódáshoz használjon Távoli asztal.

| **Forgatókönyv**                                                                                                                                          | **Leírás**                                                                                                                                                                                                                                                                                                         |
|-------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Hozzáférés az adminisztrációs portálhoz](https://docs.microsoft.com/azure-stack/operator/azure-stack-manage-portals?view=azs-2008)                    | Rendszergazdai műveletek végrehajtása.                                                                                                                                                                                                                                                                                       |
| [A PEP elérése](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint?view=azs-2008)                                     | Naplók gyűjtése és feltöltése:<br>-[Hozzon létre egy SMB-megosztást](https://docs.microsoft.com/azure-stack/operator/operator-access-workstation?view=azs-2008#transfer-files-between-the-hlh-and-oaw) a HLH az Azure stack hub-ból való fájlátvitel céljából.<br>– Azure Storage Explorer használatával feltöltheti az SMB-megosztásba mentett naplókat. |
| [Azure Stack hub regisztrálása](https://docs.microsoft.com/azure-stack/operator/azure-stack-registration?view=azs-2008#renew-or-change-registration) | Az újbóli regisztráláshoz szerezze be a korábbi regisztrációs nevet és erőforráscsoportot a felügyeleti portálról.                                                                                                                                                                                                                   |
| [Piactéri hírszolgáltatás](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-2008)            | [Hozzon létre egy SMB-megosztást](https://docs.microsoft.com/azure-stack/operator/operator-access-workstation?view=azs-2008#transfer-files-between-the-hlh-and-oaw) a HLH a letöltött rendszerkép vagy bővítmény tárolásához.                                                                                                     |
| [Virtual Machines létrehozása](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-create-vm-windows-cli?view=azs-2008)                    | Virtuális gépek létrehozása a parancssori felület használatával.                                                                                                                                                                                                                                                                                       |
| [Az AKS kezelése](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-scale?view=azs-2008)                                 | AK-fürtök kezelése, például méretezés vagy frissítés.                                                                                                                                                                                                                                                                        |

## <a name="pre-installed-software"></a>Előre telepített szoftverek

A következő táblázat felsorolja a OAW virtuális gépen előre telepített szoftvereket.

| **Szoftver neve**                                                                                              | **Hely**                                                         |
|----------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| [Microsoft Edge vállalatoknak](https://www.microsoft.com/edge/business/)                                        | [SystemDrive] \\ Program Files (x86) \\ Microsoft \\ Edge- \\ alkalmazás     |
| [Az modulok](https://docs.microsoft.com/azure-stack/operator/powershell-install-az-module?view=azs-2008) | [SystemDrive] \\ ProgramFiles \\ WindowsPowerShell \\ modulok              |
| [PowerShell 7](https://devblogs.microsoft.com/powershell/announcing-PowerShell-7-0/)                           | [SystemDrive] \\ Program Files \\ PowerShell \\ 7                          |
| [Azure parancssori felület (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)        | [SystemDrive] \\ Program Files (x86) \\ Microsoft SDK-k \\ Azure \\ CLI2      |
| [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)                     | [SystemDrive] \\ Program Files (x86) \\ Microsoft Azure Storage Explorer |
| [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)                         | [SystemDrive] \\ VMSoftware \\ azcopy_windows_amd64_10.3.4               |
| [AzureStack – eszközök](https://github.com/Azure/AzureStack-Tools/tree/az)                                          | [SystemDrive] \\ VMSoftware \\ AzureStack – eszközök                          |

## <a name="download-files"></a>Fájlok letöltése

A OAW virtuális gép létrehozásához szükséges fájlok [letöltéséhez töltse le](https://aka.ms/OAWDownload)a következőt:. A letöltés előtt tekintse át a [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/privacystatement) és [jogi feltételeit](https://docs.microsoft.com/legal/azure-stack-hub/azure-stack-operator-access-workstation-legal-terms) .

A megoldás állapot nélküli jellegéből adódóan nincsenek frissítések a OAW virtuális géphez. Minden egyes mérföldkő esetében megjelenik a VM-lemezképfájl új verziója. A legújabb verzió használatával hozzon létre egy új OAW virtuális gépet. A képfájl a Windows Server 2019 legújabb verziójára épül. A telepítés után a Windows Update használatával frissítéseket alkalmazhat, beleértve a kritikus frissítéseket is.

Érvényesítse a letöltött OAW.zip fájl kivonatát, és győződjön meg arról, hogy az nem módosult, mielőtt a OAW virtuális gép létrehozásához használta volna. Futtassa a következő PowerShell-szkriptet. Ha a visszatérési érték a `True` , a letöltött OAW.zip is használhatja:

> [!NOTE]  
> A letöltés kibontása után oldja fel a parancsfájlok zárolását.

```powershell
param( 
    [Parameter(Mandatory=$True)] 
    [ValidateNotNullOrEmpty()] 
    [ValidateScript({Test-Path $_ -PathType Leaf})] 
    [string] 
    $DownloadedOAWZipFilePath 
) 
$expectedHash = '2B268EFB113A3BEDA008FCF382A5EF2F2D4E5DCC7FD0D12DB061E37F9671D3A7' 
$actualHash = (Get-FileHash -Path $DownloadedOAWZipFilePath).Hash 
Write-Host "Expected hash: $expectedHash" 
if ($expectedHash -eq $actualHash) 
{ 
    Write-Host 'SUCCESS: OAW.zip file hash matches.' 
} 
else 
{ 
    Write-Error 'ERROR: OAW.zip file hash does not match! It isn't safe to use it, please download it again.' 
    Write-Error "Actual hash: $actualHash" 
} 
```

## <a name="check-hlh-version"></a>HLH verziójának keresése

> [!NOTE]  
> Ez a lépés fontos annak megállapításához, hogy a OAW olyan HLH telepíti-e, amelyet Microsoft-rendszerkép vagy OEM-rendszerkép használatával telepítettek. Ha általános Microsoft Hyper-V telepíti a OAW, kihagyhatja ezt a lépést.

1.  Jelentkezzen be a HLH a hitelesítő adataival.

2.  Nyissa meg a PowerShell ISE-t, és futtassa a következő parancsfájlt:
    ```powershell  
    C:\Version\Get-Version.ps1
    ```

    Például:

    ![Képernyőkép a PowerShell-parancsmagról a OAW virtuális gép verziójának vizsgálatához.](media/operator-access-workstation/check-operator-access-workstation-vm-version.png)

> [!NOTE]  
> Lehet, hogy ez a PowerShell-parancsmag nem található egy OEM-rendszerkép használatával telepített HLH.

## <a name="create-the-oaw-vm-using-a-script"></a>A OAW virtuális gép létrehozása parancsfájl használatával

A következő szkript előkészíti a virtuális gépet az operátori hozzáférési munkaállomás (OAW) számára, amely az Microsoft Azure Stack hub elérésére szolgál.

1.  Jelentkezzen be a HLH a hitelesítő adataival.

2.  Töltse le OAW.zip és bontsa ki a fájlokat.

3.  Nyisson meg egy rendszergazda jogú PowerShell-munkamenetet.

4.  Navigáljon a OAW.zip fájl kibontott tartalmához.

5.  Futtassa az New-OAW.ps1 szkriptet.

### <a name="example-deploy-on-hlh-using-a-microsoft-image"></a>Példa: üzembe helyezés a HLH Microsoft-rendszerkép használatával

```powershell  
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString 
New-OAW.ps1 -LocalAdministratorPassword $securePassword 
```


### <a name="example-deploy-on-hlh-using-an-oem-image"></a>Példa: üzembe helyezés a HLH OEM-rendszerkép használatával

```powershell  
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString 
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -AzureStackCertificatePath 'F:\certroot.cer' `
   -DeploymentDataFilePath 'F:\DeploymentData.json' `
   -AzSStampInfoFilePath 'F:\AzureStackStampInformation.json'
```

Ha a fájl DeploymentData.jsa OAW virtuális gép elnevezési előtagját tartalmazza, akkor a paraméterhez a rendszer ezt az értéket fogja használni `VirtualMachineName` . Ellenkező esetben az alapértelmezett név `AzSOAW` vagy a felhasználó által megadott név.

> [!NOTE]  
> A paramétert csak akkor kell `AzureStackCertificatePath` használni, ha Azure stack hub vállalati hitelesítésszolgáltatótól kiállított tanúsítványok használatával lett telepítve.

### <a name="example-deploy-on-microsoft-hyper-v"></a>Példa: üzembe helyezés Microsoft Hyper-V

A Microsoft Hyper-Vt futtató gépnek négy maggal és négy GB szabad memóriával kell rendelkeznie.

```powershell  
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString 
New-OAW.ps1 -LocalAdministratorPassword $securePassword ` 
-AzureStackCertificatePath 'F:\certroot.cer' ` 
-SkipNetworkConfiguration ` 
-VirtualSwitchName Example  
```

> [!NOTE]  
> A paramétert csak akkor kell `AzureStackCertificatePath` használni, ha Azure stack hub vállalati hitelesítésszolgáltatótól kiállított tanúsítványok használatával lett telepítve. A OAW virtuális gép hálózati konfiguráció nélkül lesz üzembe helyezve. Beállíthat statikus IP-címet, vagy lekérhet egy IP-címet DHCP-n keresztül.

## <a name="user-account-policy"></a>Felhasználói fiók házirendje

A OAW virtuális gépre a következő felhasználói fiók házirendje van alkalmazva:
 - Beépített rendszergazda Felhasználónév: AdminUser
 - MinimumPasswordLength = 14
 - A PasswordComplexity engedélyezve van
 - MinimumPasswordAge = 1 (nap)
 - MaximumPasswordAge = 42 (nap)
 - NewGuestName = GUser (alapértelmezés szerint letiltva)

## <a name="new-oaw-cmdlet-parameters"></a>New-OAW parancsmag paraméterei

A New-OAW két paraméter-készlet érhető el. A választható paraméterek szögletes zárójelben jelennek meg.

```powershell  
New-OAW  
-LocalAdministratorPassword <Security.SecureString> ` 
[-AzureStackCertificatePath <String>] ` 
[-AzSStampInfoFilePath <String>] ` 
[-CertificatePassword <Security.SecureString>] ` 
[-ERCSVMIP <String[]>] ` 
[-DNS <String[]>] ` 
[-DeploymentDataFilePath <String>] ` 
[-SkipNetworkConfiguration] ` 
[-ImageFilePath <String>] ` 
[-VirtualMachineName <String>] ` 
[-VirtualMachineMemory <int64>] ` 
[-VirtualProcessorCount <int>] ` 
[-VirtualMachineDiffDiskPath <String>] ` 
[-PhysicalAdapterMACAddress <String>] ` 
[-VirtualSwitchName <String>] ` 
[-ReCreate] ` 
[-AsJob] ` 
[-Passthru] ` 
[-WhatIf] ` 
[-Confirm] ` 
[<CommonParameters>] 

```

```powershell  
New-OAW 
-LocalAdministratorPassword <Security.SecureString> ` 
-IPAddress <String> ` 
-SubnetMask <String> ` 
-DefaultGateway <String> ` 
-DNS <String[]> ` 
[-AzureStackCertificatePath <String>] ` 
[-AzSStampInfoFilePath <String>] ` 
[-CertificatePassword <Security.SecureString>] ` 
[-ERCSVMIP <String[]>] ` 
[-ImageFilePath <String>] ` 
[-VirtualMachineName <String>] ` 
[-VirtualMachineMemory <int64>] ` 
[-VirtualProcessorCount <int>] ` 
[-VirtualMachineDiffDiskPath <String>] ` 
[-PhysicalAdapterMACAddress <String>] ` 
[-VirtualSwitchName <String>] ` 
[-ReCreate] ` 
[-AsJob] ` 
[-Passthru] ` 
[-WhatIf] ` 
[-Confirm] ` 
[<CommonParameters>] 

```

A következő táblázat felsorolja az egyes paraméterek definícióját.

| **Paraméter**              | **Kötelező vagy nem kötelező** | **Leírás**                                                                                                                                                                                                                                                                                                                                     |
|----------------------------|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| LocalAdministratorPassword | Kötelező              | A virtuális gép helyi rendszergazdai fiókjának AdminUser tartozó jelszó.                                                                                                                                                                                                                                                                           |
| IPAddress                  | Kötelező              | A virtuális gépen a TCP/IP konfigurálására szolgáló statikus IPv4-cím.                                                                                                                                                                                                                                                                                 |
| SubnetMask                 | Kötelező              | Az IPv4-alhálózati maszk a TCP/IP konfigurálásához a virtuális gépen.                                                                                                                                                                                                                                                                                    |
| DefaultGateway             | Kötelező              | Az alapértelmezett átjáró IPv4-címe, amely a TCP/IP-t konfigurálja a virtuális gépen.                                                                                                                                                                                                                                                                     |
| DNS                        | Kötelező              | DNS-kiszolgáló (k) a TCP/IP konfigurálásához a virtuális gépen.                                                                                                                                                                                                                                                                                           |
| ImageFilePath              | Választható              | A Microsoft által biztosított OAW. vhdx elérési útja. Az alapértelmezett érték a **OAW. vhdx** a szkript azonos mappájába.                                                                                                                                                                                                                                  |
| VirtualMachineName         | Választható              | A virtuális géphez hozzárendelni kívánt név. Ha az elnevezési előtag megtalálható a DeploymentData.jsfájljában, akkor a rendszer az alapértelmezett nevet használja. Ellenkező esetben a rendszer az alapértelmezett nevet használja az **AzSOAW**. Egy másik nevet is megadhat az alapértelmezett érték felülírásához.                                                                         |
| VirtualMachineMemory       | Választható              | A virtuális géphez hozzárendelni kívánt memória. Az alapértelmezett érték **4 GB**.                                                                                                                                                                                                                                                                             |
| VirtualProcessorCount      | Választható              | A virtuális géphez hozzárendelni kívánt virtuális processzorok száma. Az alapértelmezett érték **8**.                                                                                                                                                                                                                                                         |
| VirtualMachineDiffDiskPath | Választható              | Az ideiglenes diff lemez fájljainak tárolási útvonala, miközben a felügyeleti virtuális gép aktív volt. Az alapértelmezett érték a parancsfájl ugyanazon **DiffDisks** tartozó alkönyvtár.                                                                                                                                                                                |
| AzureStackCertificatePath  | Választható              | Az Azure Stack hub-hozzáféréshez a virtuális gépre importálandó tanúsítványok elérési útja.                                                                                                                                                                                                                                                              |
| AzSStampInfoFilePath       | Választható              | AzureStackStampInformation.jselérési útja a fájlon, ahol a parancsfájl lekérheti a ERCS virtuális gép IP-címeit.                                                                                                                                                                                                                                                  |
| CertificatePassword        | Választható              | Az Azure Stack hub-hozzáféréshez a virtuális gépre importálandó tanúsítvány jelszava.                                                                                                                                                                                                                                                           |
| ERCSVMIP                   | Választható              | A virtuális gép megbízható ERCS felvenni kívánt Azure Stack hub IP-címe. A **-SkipNetworkConfiguration** beállítása nem lép érvénybe.                                                                                                                                                                                                |
| SkipNetworkConfiguration   | Választható              | Kihagyja a virtuális gép hálózati konfigurációját, hogy a felhasználó később konfigurálható legyen.                                                                                                                                                                                                                                                                    |
| DeploymentDataFilePath     | Választható              | DeploymentData.jselérési útja. A **-SkipNetworkConfiguration** beállítása nem lép érvénybe.                                                                                                                                                                                                                                                             |
| PhysicalAdapterMACAddress  | Választható              | A gazdagép hálózati adapterének MAC-címe, amely a virtuális gép kapcsolódásához lesz használva.<br>– Ha csak egy fizikai hálózati adapter van, akkor ez a paraméter nem szükséges, és az egyetlen hálózati adapter lesz használatban.<br>– Ha több fizikai hálózati adapter van, akkor ez a paraméter szükséges annak megadásához, hogy melyiket kell használni. |
| VirtualSwitchName          | Választható              | A virtuális gép Hyper-V-beli konfigurálásához szükséges virtuális kapcsoló neve.<br>– Ha a megadott nevű VMSwitch van, akkor az ilyen VMSwitch lesz kiválasztva.<br>– Ha nincs VMSwitch a megadott névvel, a rendszer létrehoz egy VMSwitch a megadott névvel.                                                            |
| Re-Create                   | Választható              | A virtuális gép eltávolítása és újbóli létrehozása, ha már létezik ilyen nevű virtuális gép.                                                                                                                                                                                                                                       |

## <a name="check-the-oaw-vm-version"></a>A OAW VM-verziójának keresése

1.  Jelentkezzen be a OAW virtuális gépre a hitelesítő adataival.

2.  Nyissa meg a PowerShell ISE-t, és futtassa a következő parancsfájlt:

    ```powershell  
    C:\\Version\\Get-Version.ps1
    ```

    Például:
    
    ![Képernyőkép a PowerShell-parancsmagról a hardver életciklus-gazdagép verziójának vizsgálatához.](media/operator-access-workstation/check-operator-access-workstation-vm-version.png)

## <a name="transfer-files-between-the-hlh-and-oaw"></a>Fájlok átvitele a HLH és a OAW között

Ha fájlokat kell átvinnie a HLH és a OAW között, hozzon létre egy SMB-megosztást a [New-SmbShare](https://docs.microsoft.com/powershell/module/smbshare/new-smbshare?view=win10-ps) parancsmag használatával.
A New-SmbShare egy fájlrendszerbeli mappát tesz elérhetővé a távoli ügyfelek számára kiszolgálói üzenetblokk (SMB) megosztásként. Például:

A parancsmag által létrehozott megosztás törléséhez használja a [Remove-SmbShare](https://docs.microsoft.com/powershell/module/smbshare/remove-smbshare?view=win10-ps) parancsmagot.

## <a name="remove-the-oaw-vm"></a>A OAW virtuális gép eltávolítása

A következő szkript eltávolítja a OAW virtuális gépet, amely az Azure Stack hub felügyeleti és diagnosztikai célú elérésére szolgál. Ez a szkript eltávolítja a virtuális géphez tartozó lemez-és Guardian-fájlokat is.

1. Jelentkezzen be a HLH a hitelesítő adataival.

2.  Nyisson meg egy rendszergazda jogú PowerShell-munkamenetet.

3.  Navigáljon a telepített OAW.zip fájl kibontott tartalmához.

4.  Távolítsa el a virtuális gépet a Remove-OAW.ps1 parancsfájl futtatásával:

    ```powershell  
    Remove-OAW.ps1 -VirtualMachineName \<name\>
    ```
    Ahol az az \<name\> eltávolítandó virtuális gép neve. Alapértelmezés szerint a név **AzSOAW**.

    Például:

    ```powershell  
    Remove-OAW.ps1 -VirtualMachineName AzSOAW
    ```

## <a name="next-steps"></a>Következő lépések

[Azure Stack felügyeleti feladatok](azure-stack-manage-basics.md)
