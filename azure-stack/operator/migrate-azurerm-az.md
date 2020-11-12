---
title: Azure PowerShell parancsfájlok migrálása a AzureRM-ből az az-ba Azure Stack hub-ban
description: A AzureRM modulban található parancsfájlok áttelepítésének lépései és eszközei a Azure Stack hub új az moduljába.
author: mattbriggs
ms.author: mabrigg
ms.topic: conceptual
ms.date: 10/05/2020
ms.reviewer: sijuman
ms.lastreviewed: 04/14/2020
ms.openlocfilehash: bfb385e29a89ad8d47ace3b15776bd368e0cf521
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546171"
---
# <a name="migrate-from-azurerm-to-azure-powershell-az-in-azure-stack-hub"></a>Migrálás a AzureRM-ről a Azure PowerShell az Azure Stack hub-ban

Az Az modul a funkciók tekintetében paritásban van az AzureRM modullal, de rövidebb és egységesebb parancsmagneveket használ.
Az AzureRM-parancsmagokhoz írt szkriptek nem működnek automatikusan az új modullal. Az áttérés megkönnyítése érdekében az Az olyan eszközöket nyújt, amelyekkel futtathatja az AzureRM-et használó meglévő szkripteket. Az új parancskészletekre való áttérés soha nem egyszerű, ebben a cikkben azonban segítünk megismerkedni az új modulra való átállás alapjaival.

Az AzureRM és az Az közötti kompatibilitástörő változások teljes listáját [az Az 1.0.0 migrálási útmutatójában](/powershell/azure/migrate-az-1.0.0) találja.

## <a name="check-for-installed-versions-of-azurerm"></a>Az AzureRM telepített verzióinak megtekintése

A migrálási lépések előtt ellenőrizze, hogy az AzureRM mely verziói vannak telepítve a rendszeren. Ha így tesz, biztosíthatja, hogy a szkriptek már a legújabb kiadáson fussanak, és megtudhatja, hogy engedélyezheti-e a parancsaliasokat az AzureRM eltávolítása nélkül.

Az AzureRM telepített verziója/verziói megtekintéséhez futtassa a következő parancsot:

```powershell-interactive
Get-InstalledModule -Name AzureRM -AllVersions
```

## <a name="check-current-scripts-work-with-azurerm"></a>Az aktuális parancsfájlok használata a AzureRM

Ez az a legfontosabb lépés! Futtasson meglévő parancsfájlokat, és győződjön meg arról, hogy a AzureRM ( __2.5.0__ ) _legújabb_ kiadásával működnek. Ha a szkriptek nem működnek, tekintse át [az AzureRM áttelepítési útmutatóját](/powershell/azure/azurerm/migration-guide.6.0.0).

## <a name="install-the-azure-powershell-az-module"></a>Az Azure PowerShell Az modul telepítése

Az első lépés az Az modul telepítése a platformra. Az Az telepítésekor ajánlott eltávolítani az AzureRM-et. A következő lépésekben bemutatjuk, hogyan futtathatja továbbra is meglévő szkriptjeit, és hogyan biztosíthatja a régi parancsmagnevek kompatibilitását.

Az Azure PowerShell Az modul telepítéséhez hajtsa végre az alábbi lépéseket:

* __Ajánlott__ : [távolítsa el a AzureRM modult](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module).
  Győződjön meg róla, hogy az AzureRM _összes_ telepített verzióját eltávolítja, és nem csak a legutóbbi verziót.
* [Az Az modul telepítése](/powershell/azure/install-az-ps)

## <a name="enable-azurerm-compatibility-aliases"></a>Az AzureRM kompatibilitási aliasok engedélyezése 

> [!IMPORTANT]
>
> Csak akkor engedélyezze a kompatibilitási módot, ha az AzureRM _összes_ verzióját eltávolította. A kompatibilitási mód a továbbra is elérhető AzureRM parancsmagokkal való engedélyezése váratlan működést okozhat. Hagyja ki ezt a lépést, ha úgy dönt, nem távolítja el az AzureRM-et, de vegye figyelembe, hogy az AzureRM-parancsmagok a régebbi modulokat használják, és nem hívnak meg Az-parancsmagokat.

Miután az AzureRM-et eltávolította, és megbizonyosodott róla, hogy a szkriptek működnek az AzureRM legfrissebb verziójával, a következő lépés az Az modul kompatibilitási módjának engedélyezése. A kompatibilitás a következő paranccsal engedélyezhető:

```powershell-interactive
Enable-AzureRmAlias -Scope CurrentUser
```

Az aliasok segítségével továbbra is használhatja a régi parancsmagneveket, ha az Az modul telepítve van. Az aliasokat a kiválasztott hatókör felhasználói profiljába lesznek írva. Ha nincs felhasználói profil, a rendszer létrehoz egyet.

> [!WARNING]
>
> Használhat más hatókört (`-Scope`) is a parancsban, ez azonban nem ajánlott. Ezek az aliasok a kiválasztott hatókör felhasználói profiljába lesznek írva, ezért érdemes őket minél korlátozottabb hatókörhöz engedélyezni. Az aliasok rendszerszintű engedélyezése emellett a többi felhasználónak is problémát okozhat, akiknél az AzureRM van telepítve a helyi hatókörükben.

Az alias mód engedélyezése után futtassa újra a szkripteket, és győződjön meg róla, hogy továbbra is a vártnak megfelelően működnek. 

## <a name="change-module-and-cmdlet-names"></a>Modul és parancsmag nevének módosítása

Általánosságban véve a modulnevek módosultak, így az `AzureRM` és az `Azure` is `Az` lett, és ugyanez igaz a parancsmagokra is.
Az `AzureRM.Compute` modul új neve például `Az.Compute`. A `New-AzureRMVM``New-AzVM` lett, a `Get-AzureStorageBlob` pedig `Get-AzStorageBlob`.

Van néhány kivétel is ez alól a szabály alól, amelyeket érdemes figyelembe venni. Egyes modulok a parancsmagok utótagjainak módosítása nélkül lettek átnevezve vagy meglévő modulokba egyesítve, csak az `AzureRM` vagy az `Azure` helyett szerepel `Az`. Egyéb esetben a parancsmag teljes utótagja módosult az új modulnévnek megfelelően.

| AzureRM modul | Az modul | Módosult a parancsmag utótagja? |
|----------------|-----------|------------------------|
| AzureRM.Profile | Az.Accounts | Yes |
| AzureRM.Insights | Az.Monitor | Yes |
| AzureRM.Tags | Az.Resources | No |
| AzureRM.UsageAggregates | Az.Billing | No |
| AzureRM.Consumption | Az.Billing | No |

## <a name="summary"></a>Összefoglalás

Ezeknek a lépéseknek a végrehajtásával frissítheti az összes meglévő szkriptet az új modul használatára. Amennyiben az áttelepítést során valamelyik lépéssel kapcsolatban kérdés vagy probléma merült fel, amely megnehezítette a dolgát, várjuk a megjegyzéseit a cikk alatt, hogy fejleszthessük az útmutatót.

## <a name="breaking-changes-for-az-100"></a>Az Az 1.0.0 kompatibilitástörő változásai

Ez a dokumentum részletes információkat tartalmaz az AzureRM 6.x és az új Az modul 1.x és újabb verziója közötti változásokról. A tartalomjegyzék végigvezeti a teljes migrálási folyamaton, és bemutatja a modulspecifikus módosításokat is, amelyek befolyásolhatják a szkripteket.

## <a name="general-breaking-changes"></a>Általános kompatibilitástörő változások

Ez a szakasz részletesen bemutatja az Az modul újratervezésének részét képező általános kompatibilitástörő változásokat.

### <a name="cmdlet-noun-prefix-changes"></a>A parancsmag főnévi előtagjának módosításai

Az AzureRM modulban a parancsmagok az `AzureRM` vagy az `Azure` főnévi előtagot használták.  Az Az egyszerűsíti és normalizálja a parancsmagok nevét, így minden parancsmag főnévi előtagja „Az” lesz. Például:

```powershell  
Get-AzureRMVM
Get-AzureKeyVaultSecret
```

A következőre módosult:

```powershell  
Get-AzVM
Get-AzKeyVaultSecret
```

Az új parancsmagokra való áttérés megkönnyítése érdekében az Az két új parancsmagot vezet be: [Enable-AzureRmAlias](/powershell/module/az.accounts/enable-azurermalias) és [Disable-AzureRmAlias](/powershell/module/az.accounts/disable-azurermalias).  Az `Enable-AzureRmAlias` a régebbi parancsmagok nevéhez hoz létre aliasokat az AzureRM-ben, amelyek az újabb Az-parancsmagok nevének felelnek meg. A `-Scope` argumentum és az `Enable-AzureRmAlias` együttes használatával kiválaszthatja, hol legyenek engedélyezve az aliasok.

A következő AzureRM-szkript például:

```powershell  
#Requires -Modules AzureRM.Storage
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

Az `Enable-AzureRmAlias` használatával minimális változtatásokkal futtatható:

```powershell  
#Requires -Modules Az.Storage
Enable-AzureRmAlias -Scope Process
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

Az `Enable-AzureRmAlias -Scope CurrentUser` futtatása engedélyezi az aliasokat az összes megnyitott PowerShell-munkamenetben, így a parancsmag végrehajtása után az ehhez hasonló szkripteket egyáltalán nem kell módosítani:

```powershell  
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

A parancsmagok aliasaival kapcsolatos részletes információért tekintse meg az [Enable-AzureRmAlias referenciáját](/powershell/module/az.accounts/enable-azurermalias).

Amikor készen áll az aliasok letiltására, a `Disable-AzureRmAlias` eltávolítja a létrehozott aliasokat. Részletes információért tekintse meg a [Disable-AzureRmAlias referenciáját](/powershell/module/az.accounts/disable-azurermalias).

> [!IMPORTANT]
> Az aliasok letiltásakor győződjön meg arról, hogy _minden_ olyan hatókörnél le lettek tiltva, amelyekhez engedélyezve voltak az aliasok.

### <a name="module-name-changes"></a>A modul nevének változásai

A modul neve a korábbi `AzureRM.*` helyett `Az.*` lett, kivéve a következő modulokban:

| AzureRM modul | Az modul |
|----------------|-----------|
| Azure.Storage | Az.Storage |
| Azure.AnalysisServices | Az.AnalysisServices |
| AzureRM.Profile | Az.Accounts |
| AzureRM.Insights | Az.Monitor |
| AzureRM.RecoveryServices.Backup | Az.RecoveryServices |
| AzureRM.RecoveryServices.SiteRecovery | Az.RecoveryServices |
| AzureRM.Tags | Az.Resources |
| AzureRM.MachineLearningCompute | Az.MachineLearning |
| AzureRM.UsageAggregates | Az.Billing |
| AzureRM.Consumption | Az.Billing |

A modul nevének változása azt jelenti, hogy ha egy szkript a `#Requires` vagy az `Import-Module` használatával tölt be bizonyos modulokat, módosításokat kell végezni az új modul használatához. Azoknál a moduloknál, ahol a parancsmag utótagja nem változott, ez azt jelenti, hogy a modul neve megváltozott, a művelet helyét jelző utótag viszont _nem_.

#### <a name="migrating-requires-and-import-module-statements"></a>Migrálás szükséges és importálási modul-utasítások

Ha egy szkript `#Requires` vagy `Import-Module` használatával deklarálja egy AzureRM-modultól való függőségét, akkor frissíteni kell az új modulnevek használatára. Például:

```powershell  
#Requires -Module AzureRM.Compute
```

A következőre kell módosítani:

```powershell  
#Requires -Module Az.Compute
```

`Import-Module` esetében:

```powershell  
Import-Module -Name AzureRM.Compute
```

A következőre kell módosítani:

```powershell  
Import-Module -Name Az.Compute
```

### <a name="migrating-fully-qualified-cmdlet-invocations"></a>Teljesen minősített parancsmag-meghívások áttelepítése

Modulhoz minősített parancsmaghívásokat használó szkriptek, például:

```powershell  
AzureRM.Compute\Get-AzureRmVM
```

Úgy kell módosítani, hogy az új modul- és parancsmagneveket használja:

```powershell  
Az.Compute\Get-AzVM
```

### <a name="migrating-module-manifest-dependencies"></a>Moduljegyzék-függőségek migrálása

Amennyiben egy modul AzureRM-modultól való függését egy moduljegyzék (.psd1) fájl fejezi ki, a modul `RequiredModules` szakaszában frissíteni kell a modulok nevét:

```powershell
RequiredModules = @(@{ModuleName="AzureRM.Profile"; ModuleVersion="5.8.2"})
```

A következőre kell módosítani:

```powershell
RequiredModules = @(@{ModuleName="Az.Accounts"; ModuleVersion="1.0.0"})
```

### <a name="removed-modules"></a>Eltávolított modulok

Az alábbi modulok lettek eltávolítva:

- `AzureRM.Backup`
- `AzureRM.Compute.ManagedService`
- `AzureRM.Scheduler`

E szolgáltatások eszközeit már nem támogatja aktívan a rendszer.  Azt javasoljuk ügyfeleinknek, hogy amint lehetséges, térjenek át más szolgáltatások használatára.

### <a name="windows-powershell-51-and-net-472"></a>Windows PowerShell 5.1 és .NET 4.7.2

Az Az Windows PowerShell 5.1-gyel történő használatához szükséges a .NET-keretrendszer 4.7.2 telepítése. A PowerShell Core 6.x vagy újabb verziójának használatához nincs szükség a .NET-keretrendszerre.

### <a name="temporary-removal-of-user-login-using-pscredential"></a>Felhasználói bejelentkezés ideiglenes eltávolítása a PSCredential használatával

A .NET Standard hitelesítési folyamatának változásai miatt ideiglenesen eltávolítjuk a PSCredential használatával történő felhasználói bejelentkezés lehetőségét. A funkció a Windows PowerShell 5.1 2019. január 15-én megjelenő verziójában válik ismét elérhetővé. Erről részletesen [ebben a GitHub-cikkben](https://github.com/Azure/azure-powershell/issues/7430) olvashat.

### <a name="default-device-code-login-instead-of-web-browser-prompt"></a>Bejelentkezés alapértelmezett eszközkóddal a webböngészőben megjelenő kérdés helyett

A .NET Standard hitelesítési folyamatának változásai miatt az interaktív bejelentkezések során az eszközről történő bejelentkezés lesz az alapértelmezett bejelentkezési folyamat. A webböngésző-alapú bejelentkezés a Windows PowerShell 5.1 2019. január 15-én megjelenő verziójában válik ismét elérhetővé. Ezt követően a felhasználók a Switch paramétert használva választhatják az eszközről történő bejelentkezést.

## <a name="module-breaking-changes"></a>A modul kompatibilitástörő változásai

Ez a szakasz részletesen bemutatja az egyes modulok és parancsmagok kompatibilitástörő változásait.

### <a name="azapimanagement-previously-azurermapimanagement"></a>Az.ApiManagement (korábban AzureRM.ApiManagement)

- A következő parancsmagok lettek eltávolítva:
  - New-AzureRmApiManagementHostnameConfiguration
  - Set-AzureRmApiManagementHostnames
  - Update-AzureRmApiManagementDeployment
  - Import-AzureRmApiManagementHostnameCertificate
  - Ezeket a tulajdonságokat mostantól a **Set-AzApiManagement** parancsmaggal kell beállítani
- A következő tulajdonságok lettek eltávolítva:
  - A `PsApiManagementHostnameConfiguration` típus `PortalHostnameConfiguration`, `ProxyHostnameConfiguration`, `ManagementHostnameConfiguration` és `ScmHostnameConfiguration` tulajdonsága el lett távolítva a `PsApiManagementContext`ből. Ezek helyett a `PsApiManagementCustomHostNameConfiguration` típus `PortalCustomHostnameConfiguration`, `ProxyCustomHostnameConfiguration`, `ManagementCustomHostnameConfiguration` és `ScmCustomHostnameConfiguration` tulajdonságát használja.
  - A `StaticIPs` tulajdonság el lett távolítva a PsApiManagementContextből. Két új tulajdonságra lett osztva: ez a `PublicIPAddresses` és a `PrivateIPAddresses`.
  - A kötelező `Location` tulajdonság el lett távolítva a New-AzureApiManagementVirtualNetwork parancsmagból.

### <a name="azbilling-previously-azurermbilling-azurermconsumption-and-azurermusageaggregates"></a>Az.Billing (korábban AzureRM.Billing, AzureRM.Consumption és AzureRM.UsageAggregates)

- A `Get-AzConsumptionUsageDetail` parancsmag `InvoiceName` paramétere el lett távolítva.  A szkripteknek a számlák készítéséhez más identitásparamétereket kell használniuk.

### <a name="azcompute-previously-azurermcompute"></a>Az.Compute (korábban AzureRM.Compute)

- A `PSVirtualMachine` és `PSVirtualMachineScaleSet` objektum `Identity` tulajdonsága mostantól nem tartalmazza az `IdentityIds` azonosítókat. A szkriptek a jövőben nem veszik figyelembe a mező értékét a feldolgozási döntések során.
- A `PSVirtualMachineScaleSetVM` objektum `InstanceView` tulajdonságának típusa `VirtualMachineInstanceView` helyett `VirtualMachineScaleSetVMInstanceView` lett.
- Az `UpgradePolicy` tulajdonság `AutoOSUpgradePolicy` és `AutomaticOSUpgrade` tulajdonsága el lett távolítva.
- A `PSSnapshotUpdate` objektum `Sku` tulajdonságának típusa `DiskSku` helyett `SnapshotSku` lett.
- A `VmScaleSetVMParameterSet` el lett távolítva az `Add-AzVMDataDisk` parancsmagból, mostantól nem lehet önálló adatlemezt hozzáadni egy ScaleSet-virtuálisgéphez.

### <a name="azkeyvault-previously-azurermkeyvault"></a>Az.KeyVault (korábban AzureRM.KeyVault)

- A `PSKeyVaultKeyAttributes`, `PSKeyVaultKeyIdentityItem` és `PSKeyVaultSecretAttributes` objektum `PurgeDisabled` tulajdonsága el lett távolítva. A szkriptek a jövőben nem hivatkozhatnak a ```PurgeDisabled``` tulajdonságra a feldolgozási döntések során.

### <a name="azmonitor-previously-azurerminsights"></a>Az.Monitor (korábban AzureRM.Insights)

- A `Categories` és a `Timegrains` többes számú paraméternév el lett távolítva. Ezek helyett a `Set-AzDiagnosticSetting` parancsmag egyes számú paraméterneveit kell használni. A következőt használó szkripteket:
  ```powershell  
  Set-AzureRmDiagnosticSetting -Timegrains PT1M -Categories Category1, Category2
  ```

  A következőre kell módosítani:
  ```powershell  
  Set-AzDiagnosticSetting -Timegrain PT1M -Category Category1, Category2
  ```

### <a name="aznetwork-previously-azurermnetwork"></a>Az.Network (korábban AzureRM.Network)

- A `Get-AzServiceEndpointPolicyDefinition` parancsmag elavult `ResourceId` paramétere el lett távolítva.
- A `PSVirtualNetwork` objektum elavult `EnableVmProtection` tulajdonsága el lett távolítva.
- Az elavult `Set-AzVirtualNetworkGatewayVpnClientConfig` parancsmag el lett távolítva.

A szkriptek a jövőben nem hozhatnak feldolgozási döntéseket e mezők értéke alapján.

### <a name="azresources-previously-azurermresources"></a>Az.Resources (korábban AzureRM.Resources)

- A `New/Set-AzPolicyAssignment` parancsmag `Sku` paramétere el lett távolítva.
- A `New-AzADServicePrincipal` és `New-AzADSpCredential` parancsmag `Password` paramétere el lett távolítva. A jelszavak létrehozása automatikusan történik, a korábban jelszót biztosító szkripteket:

  ```powershell  
  New-AzAdSpCredential -ObjectId 1f99cf81-0146-4f4e-beae-2007d0668476 -Password $secPassword
  ```

  Úgy kell módosítani, hogy a kimenetből kérjék le a jelszót:

  ```powershell  
  $credential = New-AzAdSpCredential -ObjectId 1f99cf81-0146-4f4e-beae-2007d0668476
  $secPassword = $credential.Secret
  ```


### <a name="azstorage-previously-azurestorage-and-azurermstorage"></a>Az.Storage (korábban Azure.Storage és AzureRM.Storage)

- Annak érdekében, hogy létre lehessen hozni egy Oauth Storage-környezetet pusztán a tárfiók nevét használva, az új alapértelmezett paraméterkészlet a következő: `OAuthParameterSet`
  - Például: `$ctx = New-AzureStorageContext -StorageAccountName $accountName`
- A `Get-AzStorageUsage` parancsmagban kötelezővé vált a `Location` paraméter használata.
- A Storage API-metódusok mostantól feladatalapú aszinkron mintát (TAP) használnak az egyidejű API-hívások helyett. Az alábbi példák az új, aszinkron parancsokat mutatják be:

#### <a name="blob-snapshot"></a>BLOB pillanatképe

AzureRM:

```powershell  
$b = Get-AzureStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$b.ICloudBlob.Snapshot()
```

Az:

```powershell  
$b = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$task = $b.ICloudBlob.SnapshotAsync()
$task.Wait()
$snapshot = $task.Result
```

#### <a name="share-snapshot"></a>Pillanatkép megosztása

AzureRM:

```powershell  
$Share = Get-AzureStorageShare -Name $containerName -Context $ctx
$snapshot = $Share.Snapshot()
```

Az:

```powershell  
$Share = Get-AzStorageShare -Name $containerName -Context $ctx
$task = $Share.SnapshotAsync()
$task.Wait()
$snapshot = $task.Result
```

#### <a name="undelete-soft-deleted-blob"></a>Blob helyreállítható törlésének visszavonása

AzureRM:

```powershell  
$b = Get-AzureStorageBlob -Container $containerName -Blob $blobName -IncludeDeleted -Context $ctx
$b.ICloudBlob.Undelete()
```

Az:

```powershell  
$b = Get-AzStorageBlob -Container $containerName -Blob $blobName -IncludeDeleted -Context $ctx
$task = $b.ICloudBlob.UndeleteAsync()
$task.Wait()
```

#### <a name="set-blob-tier"></a>BLOB-rétegek beállítása

AzureRM:

```powershell  
$blockBlob = Get-AzureStorageBlob -Container $containerName -Blob $blockBlobName -Context $ctx
$blockBlob.ICloudBlob.SetStandardBlobTier("hot")

$pageBlob = Get-AzureStorageBlob -Container $containerName -Blob $pageBlobName -Context $ctx
$pageBlob.ICloudBlob.SetPremiumBlobTier("P4")
```

Az:

```powershell  
$blockBlob = Get-AzStorageBlob -Container $containerName -Blob $blockBlobName -Context $ctx
$task = $blockBlob.ICloudBlob.SetStandardBlobTierAsync("hot")
$task.Wait()

$pageBlob = Get-AzStorageBlob -Container $containerName -Blob $pageBlobName -Context $ctx
$task = $pageBlob.ICloudBlob.SetPremiumBlobTierAsync("P4")
$task.Wait()
```

### <a name="azwebsites-previously-azurermwebsites"></a>Az.Websites (korábban AzureRM.Websites)

- A `PSAppServicePlan`, `PSCertificate`, `PSCloningInfo` és `PSSite` objektum elavult tulajdonságai el lettek távolítva.

## <a name="next-steps"></a>Következő lépések

- További információ a Azure Stack hub PowerShell-ről: Ismerkedés [a PowerShell-lel Azure stack hub-ban](../user/azure-stack-powershell-overview.md)
- Telepítse a PowerShell az modult, lásd: a [PowerShell telepítése az Azure stack hub](powershell-install-az-module.md) -hoz
