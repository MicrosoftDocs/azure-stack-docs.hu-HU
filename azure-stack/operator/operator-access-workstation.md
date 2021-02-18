---
title: Azure Stack hub-operátor hozzáférési munkaállomása
description: Megtudhatja, hogyan tölthet le és konfigurálhat egy Azure Stack hub-kezelői hozzáférési munkaállomást.
author: ashika789
ms.topic: article
ms.date: 02/17/2021
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 02/17/2021
ms.openlocfilehash: 91ed23e40743de4236623322f9f830032d1793ac
ms.sourcegitcommit: dfce5d143318150d2cab563dc0ee328c777bf5f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636645"
---
# <a name="azure-stack-hub-operator-access-workstation"></a>Azure Stack hub-operátor hozzáférési munkaállomása 

Az operátori hozzáférési munkaállomás (OAW) egy, a 2005-es vagy újabb verziót futtató hardveres életciklusú gazdagépen (HLH) lévő Jump Box virtuális gép (VM) üzembe helyezésére szolgál, így egy Azure Stack hub-kezelő elérheti a jogosultsági szintű végpontot (PEP) és a felügyeleti portált a támogatási forgatókönyvekhez. 

A OAW virtuális gépet akkor kell létrehozni, ha egy operátor új feladatot hajt végre. Miután befejeződött a virtuális gép egy szükséges feladata, a virtuális gépet le kell állítani és el kell távolítani, mivel Azure Stack hub-nak nem kell mindig futtatnia.  

## <a name="oaw-scenarios"></a>OAW-forgatókönyvek

A következő táblázatok a OAW általános forgatókönyveit ismertetik, de ez nem kizárólagos. A OAW való kapcsolódáshoz ajánlott Távoli asztal használni. 

|Forgatókönyv                                                                                                                          |Leírás                 |
|----------------------------------------------------------------------------------------------------------------------------------|-----------------------------|
|[Hozzáférés az adminisztrációs portálhoz](./azure-stack-manage-portals.md)                     |Rendszergazdai műveletek végrehajtása                                                                           |
|[A PEP elérése](./azure-stack-privileged-endpoint.md)                                     |Naplók gyűjtése és feltöltése:<br>-[SMB-megosztás létrehozása](#transfer-files-between-the-hlh-and-oaw) a HLH az Azure stack hub-ból való fájlátvitel céljából<br>– Azure Storage Explorer használata az SMB-megosztásba mentett naplók feltöltéséhez |
|[Azure Stack hub regisztrálása](./azure-stack-registration.md#renew-or-change-registration) |Az újbóli regisztráláshoz szerezze be a korábbi regisztrációs nevet és erőforráscsoportot az adminisztrációs portálról                               |
|[Piactéri hírszolgáltatás](./azure-stack-download-azure-marketplace-item.md)            |[Hozzon létre egy SMB-megosztást](#transfer-files-between-the-hlh-and-oaw) a HLH a letöltött rendszerkép vagy bővítmény tárolásához                                                        |

## <a name="download-files"></a>Fájlok letöltése

A OAW virtuális gép létrehozásához szükséges fájlok [**letöltéséhez töltse le**](https://aka.ms/OAWDownload)a következőt:. A letöltés előtt olvassa el a [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/privacystatement) és [jogi feltételeit](/legal/azure-stack-hub/azure-stack-operator-access-workstation-legal-terms) .

A megoldás állapot nélküli jellegéből adódóan nincsenek frissítések a OAW virtuális géphez. Minden egyes mérföldkő esetében a rendszer a virtuálisgép-lemezképfájl új verzióját fogja felszabadítani. A legújabb verzió használatával hozzon létre egy új OAW virtuális gépet. A képfájl a Windows Server 2019 legújabb verziójára épül. A telepítés után a Windows Update használatával frissítéseket alkalmazhat, beleértve a kritikus frissítéseket is. 

Ellenőrizze a letöltött OAW.zip fájl kivonatát, és győződjön meg arról, hogy a OAW virtuális gép létrehozása előtt nem módosították. Futtassa a következő PowerShell-szkriptet. Ha a visszatérési érték TRUE (igaz), használhatja a letöltött OAW.zip:

```powershell
param(
    [Parameter(Mandatory=$True)]
    [ValidateNotNullOrEmpty()]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [string]
    $DownloadedOAWZipFilePath
)

$expectedHash = '6E412551C7921A6FA4E9D64812A4D3770EB1B1257E2BE55B8426E5EFBCB2C001'
$actualHash = (Get-FileHash -Path $DownloadedOAWZipFilePath).Hash

Write-Host "Expected hash: $expectedHash"

if ($expectedHash -eq $actualHash)
{
    Write-Host 'SUCCESS: OAW.zip file hash matches.'
}
else
{
    Write-Error 'ERROR: OAW.zip file hash does not match! It is not safe to use it, please download it again.'
    Write-Error "Actual hash: $actualHash"
}
```

## <a name="user-account-policy"></a>Felhasználói fiók házirendje 
A OAW virtuális gépre a következő felhasználói fiók házirendje van alkalmazva:

- Beépített rendszergazda Felhasználónév: AdminUser
- MinimumPasswordLength = 14
- A PasswordComplexity engedélyezve van
- MinimumPasswordAge = 1 (nap)
- MaximumPasswordAge = 42 (nap)
- NewGuestName = GUser (alapértelmezés szerint letiltva)

## <a name="pre-installed-software"></a>Előre telepített szoftverek
A következő táblázat felsorolja a OAW virtuális gépen előre telepített szoftvereket.

| Szoftver neve           | Hely                                                                                       |
|--------------------------|------------------------------------------------------------------------------------------------|
| [Microsoft Edge vállalatoknak](https://www.microsoft.com/edge/business/)                                            | \[SystemDrive \] \Program Files (x86) \Microsoft\Edge\Application                                                                                        |
| [Az modulok](./powershell-install-az-module.md)                         | \[SystemDrive \] \ProgramFiles\WindowsPowerShell\Modules                                         |  
| [PowerShell 7](https://devblogs.microsoft.com/powershell/announcing-PowerShell-7-0/)| \[SystemDrive \] \Program Files\PowerShell\7                                                                       |
| [Azure parancssori felület (CLI)](/cli/azure/?view=azure-cli-latest) | \[SystemDrive \] \Program Files (x86) \Microsoft SDKs\Azure\CLI2 |
| [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)   | \[SystemDrive \] \Program Files (x86) \Microsoft Azure Storage Explorer                                                                       |
| [AzCopy](/azure/storage/common/storage-use-azcopy-v10)                             | \[SystemDrive \] \vmsoftware\ azcopy_windows_amd64_10.3.4                                         |
| [AzureStack – eszközök](https://github.com/Azure/AzureStack-Tools/tree/az)                  | \[SystemDrive \] \VMSoftware\AzureStack-Tools                                                    |

## <a name="check-hlh-version"></a>HLH verziójának keresése

1. Jelentkezzen be a HLH a hitelesítő adataival.
1. Nyissa meg a PowerShell ISE-t, és futtassa a következő parancsfájlt:

   ```powershell
   'C:\Version\Get-Version.ps1'
   ```

   Például:

   ![Képernyőkép a PowerShell-parancsmagról a OAW virtuális gép verziójának vizsgálatához](./media/operator-access-workstation/check-hardware-lifecycle-host-version.png)

## <a name="create-the-oaw-vm-using-a-script"></a>A OAW virtuális gép létrehozása parancsfájl használatával

A következő szkript előkészíti a virtuális gépet a kezelői hozzáférési munkaállomásként (OAW), amely az Microsoft Azure Stack hub felügyeleti és diagnosztikai célú elérésére szolgál.

1. Jelentkezzen be a HLH a hitelesítő adataival.
1. Töltse le OAW.zip és bontsa ki a fájlokat.
1. Nyisson meg egy rendszergazda jogú PowerShell-munkamenetet.
1. Navigáljon a OAW.zip fájl kibontott tartalmához.
1. Futtassa az New-OAW.ps1 szkriptet. 

Ha például az Azure Stack hub 2005-es vagy újabb verzióját használó testreszabás nélkül szeretné létrehozni a OAW virtuális gépet a HLH, futtassa a New-OAW.ps1 parancsfájlt csak a **-LocalAdministratorPassword** paraméterrel:

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword  
```

A OAW virtuális gép létrehozása olyan gazdagépen, amelyen Azure Stack hub hálózati kapcsolatai vannak:

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -IPAddress '192.168.0.20' `
   -SubnetMask '255.255.255.0' `
   -DefaultGateway '192.168.0.1' `
   -DNS '192.168.0.10'
```

A ERCS virtuális gép IP-címének lekérése a AzureStackStampInformation.jsfájlból:

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -AzureStackCertificatePath 'F:\certroot.cer' `
   -DeploymentDataFilePath 'F:\DeploymentData.json' `
   -AzSStampInfoFilePath 'F:\AzureStackStampInformation.json'
```

A OAW virtuális gép létrehozása a HLH DeploymentData.jshasználatával:

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -DeploymentDataFilePath 'D:\AzureStack\DeploymentData.json'
```

Ha a fájl DeploymentData.jsa OAW virtuális gép elnevezési előtagját tartalmazza, akkor a rendszer ezt az értéket fogja használni a **VirtualMachineName** paraméterhez. Ellenkező esetben az alapértelmezett név a **AzSOAW** , vagy pedig a felhasználó által megadott név.

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

| Paraméter   | Kötelező vagy nem kötelező  | Description       |
|-------------|--------------------|-------------------|
| LocalAdministratorPassword | Kötelező | A virtuális gép helyi rendszergazdai fiókjának AdminUser tartozó jelszó. |
| IPAddress                  | Kötelező | A virtuális gépen a TCP/IP konfigurálására szolgáló statikus IPv4-cím.                                                |
| SubnetMask                 | Kötelező | Az IPv4-alhálózati maszk a TCP/IP konfigurálásához a virtuális gépen.                                                   |
| DefaultGateway             | Kötelező | Az alapértelmezett átjáró IPv4-címe, amely a TCP/IP-t konfigurálja a virtuális gépen.                                    |
| DNS                        | Kötelező | DNS-kiszolgáló (k) a TCP/IP konfigurálásához a virtuális gépen.                                                          |
| ImageFilePath              | Választható | A Microsoft által biztosított OAW. vhdx elérési útja. Az alapértelmezett érték a **OAW. vhdx** a szkript azonos mappájába. |
| VirtualMachineName         | Választható | A virtuális géphez hozzárendelni kívánt név. Ha az elnevezési előtag megtalálható a DeploymentData.jsfájljában, akkor a rendszer az alapértelmezett nevet használja. Ellenkező esetben a rendszer az alapértelmezett nevet használja az **AzSOAW** . Egy másik nevet is megadhat az alapértelmezett érték felülírásához. |
| VirtualMachineMemory       | Választható | A virtuális géphez hozzárendelni kívánt memória. Az alapértelmezett érték **4 GB**.                            |
| VirtualProcessorCount      | Választható | A virtuális géphez hozzárendelni kívánt virtuális processzorok száma. Az alapértelmezett érték **8**.        |
| VirtualMachineDiffDiskPath | Választható | Az ideiglenes diff lemez fájljainak tárolási útvonala, miközben a felügyeleti virtuális gép aktív volt. Az alapértelmezett érték a parancsfájl ugyanazon **DiffDisks** tartozó alkönyvtár. |
| AzureStackCertificatePath  | Választható | Az Azure Stack hub-hozzáféréshez a virtuális gépre importálandó tanúsítványok elérési útja. |
| AzSStampInfoFilePath       | Választható | AzureStackStampInformation.jselérési útja a fájlon, ahol a parancsfájl lekérheti a ERCS virtuális gép IP-címeit. |
| CertificatePassword        | Választható | Az Azure Stack hub-hozzáféréshez a virtuális gépre importálandó tanúsítvány jelszava. |
| ERCSVMIP                   | Választható | A virtuális gép megbízható ERCS felvenni kívánt Azure Stack hub IP-címe. A **-SkipNetworkConfiguration** beállítása nem lép érvénybe. |
SkipNetworkConfiguration     | Választható | Kihagyja a virtuális gép hálózati konfigurációját, hogy a felhasználó később konfigurálható legyen. |
| DeploymentDataFilePath     | Választható | DeploymentData.jselérési útja. A **-SkipNetworkConfiguration** beállítása nem lép érvénybe.            |
| PhysicalAdapterMACAddress  | Választható | A gazdagép hálózati adapterének MAC-címe, amely a virtuális gép kapcsolódásához lesz használva.<br>– Ha csak egy fizikai hálózati adapter van, akkor ez a paraméter nem szükséges, és az egyetlen hálózati adapter lesz használatban.<br>– Ha több fizikai hálózati adapter van, akkor ez a paraméter szükséges annak megadásához, hogy melyiket kell használni.<br> |
| VirtualSwitchName          | Választható | A virtuális gép Hyper-V-beli konfigurálásához szükséges virtuális kapcsoló neve.<br>– Ha a megadott nevű VMSwitch van, akkor az ilyen VMSwitch lesz kiválasztva.<br>– Ha nincs VMSwitch a megadott névvel, a rendszer létrehoz egy VMSwitch a megadott névvel.<br> |
| Hozza létre újra                   | Választható | A virtuális gép eltávolítása és újbóli létrehozása, ha már létezik azonos nevű virtuális gép. |

## <a name="check-the-oaw-vm-version"></a>A OAW VM-verziójának keresése

1. Jelentkezzen be a OAW virtuális gépre a hitelesítő adataival.
1. Nyissa meg a PowerShell ISE-t, és futtassa a következő parancsfájlt:

   ```powershell
   'C:\Version\Get-Version.ps1'
   ```

   Például:

   ![A hardver életciklus-gazdagép verziójának ellenőrzését szolgáló PowerShell-parancsmag képernyőképe](./media/operator-access-workstation/check-operator-access-workstation-vm-version.png)

## <a name="transfer-files-between-the-hlh-and-oaw"></a>Fájlok átvitele a HLH és a OAW között

Ha fájlokat kell átvinnie a HLH és a OAW között, hozzon létre egy SMB-megosztást a [New-SmbShare](/powershell/module/smbshare/new-smbshare?view=win10-ps) parancsmag használatával. A New-SmbShare egy fájlrendszerbeli mappát tesz elérhetővé a távoli ügyfelek számára kiszolgálói üzenetblokk (SMB) megosztásként. Például:

A parancsmag által létrehozott megosztás törléséhez használja a [Remove-SmbShare](/powershell/module/smbshare/remove-smbshare?view=win10-ps) parancsmagot. Például:

## <a name="remove-the-oaw-vm"></a>A OAW virtuális gép eltávolítása

A következő szkript eltávolítja a OAW virtuális gépet, amely az Azure Stack hub felügyeleti és diagnosztikai célú elérésére szolgál. Ez a szkript eltávolítja a virtuális géphez tartozó lemez-és Guardian-fájlokat is.

1. Jelentkezzen be a HLH a hitelesítő adataival.
1. Nyisson meg egy rendszergazda jogú PowerShell-munkamenetet. 
1. Navigáljon a telepített OAW.zip fájl kibontott tartalmához.
1. Távolítsa el a virtuális gépet a Remove-OAW.ps1 parancsfájl futtatásával: 

   ```powershell
   Remove-OAW.ps1 -VirtualMachineName <name>
   ```

   Ahol az az \<name\> eltávolítandó virtuális gép neve. Alapértelmezés szerint a név **AzSOAW**.

   Például:

   ```powershell
   Remove-OAW.ps1 -VirtualMachineName AzSOAW
   ```

## <a name="next-steps"></a>Következő lépések

[Azure Stack felügyeleti feladatok](azure-stack-manage-basics.md)
