---
title: Azure Site Recovery feladat-visszavételi eszköz felhasználói útmutatója
description: Megtudhatja, hogyan használhatja a Azure Site Recovery feladat-visszavétel eszközt a virtuális gépek (VM-EK) elleni védelemhez.
author: sethmanheim
ms.author: sethm
ms.date: 9/18/2020
ms.topic: how-to
ms.reviewer: rtiberiu
ms.lastreviewed: 9/18/2020
ms.openlocfilehash: 2b57527f3a65e97f5b83ada115faa63ace563ea4
ms.sourcegitcommit: 0f2852c3302c6723e7afad637f55b80359182ae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91366250"
---
# <a name="azure-site-recovery-failback-tool"></a>Azure Site Recovery feladat-visszavételi eszköz

Csatlakoztatott környezetben a Azure Site Recovery használatával biztosítható a Azure Stack hub-on futó virtuális gépek (VM-EK) védelmének biztosítása. [Ez a cikk](/azure/site-recovery/azure-stack-site-recovery) bemutatja, hogyan állíthatja be a környezetet, és hogyan járul hozzá a site Recovery a számítási feladatok általános üzletmenet-folytonossági és vész-helyreállítási stratégiájához.

Kimaradás esetén a Azure Stack hub operátor a *feladatátvételi* eljáráson keresztül halad át; Ha Azure Stack hub újra működik, a feladat- *visszavételi* folyamaton haladnak át. A feladatátvételi folyamatot ebben a [site Recovery cikkben](/azure/site-recovery/azure-stack-site-recovery)ismertetjük, de a feladat-visszavételi folyamat több manuális lépést is magában foglal:

- Állítsa le az Azure-ban futó virtuális gépet.
- Töltse le a VHD-ket.
- Töltse fel a virtuális merevlemezeket Azure Stack hubhoz.
- Hozza létre újra a virtuális gépeket.
- Végezetül indítsa el Azure Stack hub-on futó virtuális gépet. 

Mivel ez a folyamat hibás és időigényes lehet, a létrehozott szkripteket a folyamat felgyorsításához és automatizálásához.

## <a name="failback-procedure"></a>Feladat-visszavételi eljárás

Az automatikus feladat-visszavételi folyamat három fő részből áll:

- **Copy-AzSiteRecoveryVmVHD**:
  - Leállítja az Azure-beli virtuális gépet.
  - Előkészíti a lemez exportálását.
  - A lemez másolása a AzCopy vagy a StorageBlobCopy használatával.
  - Feltölti a lemezt egy Azure Stack hub Storage-fiókba.

- A lemez másolását követően két forgatókönyv vonatkozik a **Prepare-AzSiteRecoveryVMFailBack**:
  - Az eredeti Azure Stack hub helyreállítása megtörtént. Az eredeti virtuális gép továbbra is létezik, és csak a virtuális merevlemezeket kell módosítania.
  - Vészhelyzet esetén, ha az eredeti virtuális gépek elvesznek, újra kell építenie a teljes virtuális gépet.

  Ez az eljárás mindkét forgatókönyvet magában foglalja a sablon és a szükséges paraméterérték létrehozásával.

- A Azure Resource Manager sablon tényleges telepítése a paraméter fájljának használatával, és a virtuális gép üzembe helyezése/létrehozása Azure Stack központban.

### <a name="prerequisites"></a>Előfeltételek

A feladat-visszavételi eljárás végrehajtásához a következő előfeltételek szükségesek:

- Másolja a [Azure site Recovery feladat-visszavételi eszközt](https://aka.ms/azshasr).

- Importálja a FailbackTool. psm1 modult a PowerShellben.

- Kövesse az ebben a cikkben ismertetett eljárást az [az modul Azure stack hub-hoz való telepítéséhez](powershell-install-az-module.md).

- választható [Töltse le a AzCopy 10-es verzióját](/azure/storage/common/storage-use-azcopy-v10).

  - A blob **AzCopy** használatával történő másolása gyorsabb, de a blob-fájl ideiglenes tárolásához további helyi lemezterület szükséges.
  - Ha a **AzCopy** nincs használatban, a VHD-másolás a **AzStorageBlobCopy**használatával történik. Ez azt jelenti, hogy nincs szükség helyi tárterületre, de a folyamat tovább tart.

- Hozzáférés a Azure Portal lévő erőforrásokhoz, és hozzáférés a Azure Stack hub-beli erőforrások létrehozásához.

## <a name="step-1-copy-blob-from-azure-to-azure-stack-hub"></a>1. lépés: blob másolása az Azure-ból Azure Stack hubhoz

Hívja meg a **copy-AzSiteRecoveryVmVHD PowerShell-** parancsmagot az Azure-beli virtuális gép leállításához, töltse le a virtuális merevlemezeket az Azure-ból, és töltse fel őket Azure stack hubhoz. Például:

```powershell
$uris = Copy-AzSiteRecoveryVmVHD `
        -SourceVM $vmOnAzure `
        -TargetStorageAccountName "targetaccountName" `
        -TargetStorageEndpoint "redmond.ext-v.masd.stbtest.microsoft.com" `
        -TargetStorageAccountKey $accountKey `
        -AzCopyPath "C:\azcopy_v10\azcopy.exe" `
        -VhdLocalFolder "C:\tempfolder"
```

Vegye figyelembe az alábbi szempontokat:

- Ez a példa a `$uris` `SourceDiskVhdUris` 2. lépésben használt érték tárolására használatos.

- A `-SourceVM` paraméter a által lekért virtuálisgép-objektum `Get-AzVM` .
  - Ez az Azure Stack hub által az Azure-ban feladatátvétel alatt álló védett virtuális gép.
  - Nem számít, hogy a virtuális gép fut-e, mivel a szkript leállítja a virtuális gépet. Javasoljuk azonban, hogy explicit módon állítsa le, és ennek megfelelően állítsa le a virtuális gépeken belüli szolgáltatásokat.

- A Azure Stack hub oldalon megadhatja a Storage-fiókhoz tartozó fiók kulcsát (a használatával `TargetStorageAccountKey` ) vagy az SAS-tokent (a használatával `TargetStorageAccountSasToken` ). Az SAS-jogkivonatot a Storage-fiók szintjén kell létrehozni, legalább a következő engedélyekkel:

   :::image type="content" source="media/site-recovery-failback/sasperms.png" alt-text="SAS-jogkivonat engedélyei":::

- Megadhatja a tárolási végpontot, amely tartalmazza a régiót és a teljes tartománynevet is. például, `regionname.azurestack.microsoft.com` vagy az Azure stack hub környezetének neve, például: `AzureStackTenant` . Ha a környezet nevét használja, a **Get-AzEnvironment**használatával kell szerepelnie.

- Dönthet úgy, hogy a **AzCopy** vagy a **AzStorageBlobCopy** használatával másolja a VHD-t az Azure-ból Azure stack hub-ba. A **AzCopy** gyorsabb, de először le kell töltenie a VHD-fájlokat a helyi mappába:
  - A **AzCopy**használatához adja meg a paramétereket `-AzCopyPath` és a `-VhdLocalFolder` (a VHD-k másolási útvonalát).
  - Ha nincs elég hely helyileg, dönthet úgy, hogy a virtuális merevlemezt közvetlenül, **AzCopy**nélkül másolja, és kihagyja a paramétereket `-AzCopyPath` és `-VhdLocalFolder` . Alapértelmezés szerint ez a parancs a **AzStorageBlobCopy** használatával másolja át közvetlenül az Azure stack hub Storage-fiókba.

## <a name="step-2-generate-resource-manager-templates"></a>2. lépés: Resource Manager-sablonok előállítása

A lemez másolását követően a **Prepare-AzSiteRecoveryVMFailBack** parancsmaggal hozza létre a (z) `$templateFile` és `$parameterFile` szükséges a virtuális gép üzembe helyezéséhez Azure stack központban:

```powershell
$templateFile, $parameterFile = Prepare-AzSiteRecoveryVMFailBack `
                                -SourceContextName "PublicAzure" `
                                -SourceVM $vmOnAzure `
                                -SourceDiskVhdUris $uris `
                                -TargetResourceLocation "redmond" `
                                -ArmTemplateDestinationPath "C:\ARMtemplates" `
                                -TargetVM $vmOnHub `
                                -TargetContextName "AzureStack"

```

Vegye figyelembe az alábbi szempontokat:

- Ez a példa `-SourceDiskVhdUris` visszatérési értéket használ az 1. lépésben (a használatával `$uris` ).

- Ez a parancsmag két forgatókönyvet támogat:
  - A megadásával `-TargetVM` feltételezheti, hogy a virtuális gép aktív az Azure stack hub oldalon, és az Azure-ból legutóbb másolt lemezeket szeretné lecserélni.
  - A szkript létrehoz egy Resource Manager-sablont a virtuális gép üzembe helyezéséhez, és törli a meglévő virtuális gépet Azure Stack hub-ból.
  
  > [!NOTE]
  > Az Azure Stack hub-beli virtuális gép törlése nem távolítja el a többi objektumot (például VNET, Erőforráscsoport, NSG). Csak magát a virtuálisgép-erőforrást távolítja el, majd a sablont a `-incremental` paraméterrel telepíti.

  - Ha nem adja `-TargetVM` meg a paramétert, a parancsfájl feltételezi, hogy a virtuális gép már nem létezik az Azure stack hub oldalon, így a szkript létrehoz egy Resource Manager-sablont egy teljesen új virtuális gép üzembe helyezéséhez.

- A létrehozott Resource Manager-sablonfájlok a következő helyen találhatók `-ArmTemplateDestinationPath` , és a rendszer a sablonfájl vagy a paraméter fájljának teljes elérési útját adja vissza.

- Ha a `-TargetVM` paraméter meg van megadva, a parancsmag törli a virtuális gépet, így folytathatja a következő lépéseket.

## <a name="step-3-deploy-the-resource-manager-template"></a>3. lépés: a Resource Manager-sablon üzembe helyezése

Ekkor a rendszer feltölti a VHD-t Azure Stack központba, és létrehozza a Resource Manager-sablont és a megfelelő paramétereket. A virtuális gép üzembe helyezése Azure Stack központon történik.

Bizonyos esetekben érdemes lehet szerkeszteni a sablont, és néhány nevet vagy erőforrást hozzáadni, eltávolítani vagy módosítani. Ez engedélyezett, mert szükség szerint szerkesztheti és módosíthatja a sablont.

Ha készen áll, és a Resource Manager-sablon erőforrásainak megerősítése után a várt érték szerepel, a **New-AzResourceGroupDeployment** parancsmag meghívásával üzembe helyezheti az erőforrásokat. Például:

```powershell
New-AzResourceGroupDeployment `
  -Name "Failback" `
  -ResourceGroupName "failbackrg" `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile `
  -Mode Incremental
```

Vegye figyelembe az alábbi szempontokat:

- A `-ResourceGroupName` paraméternek egy meglévő erőforráscsoporthoz kell lennie.
- A `-TemplateFile` és a `-TemplateParameterFile` paraméterek a 2. lépésben megadott visszatérési értékekből származnak.

## <a name="next-steps"></a>Következő lépések

- [Azure Stack hub VM-funkciók](../user/azure-stack-vm-considerations.md)
- [Egyéni virtuálisgép-rendszerkép hozzáadása és eltávolítása Azure Stack hubhoz](azure-stack-add-vm-image.md)
- [Windows rendszerű virtuális gép létrehozása a PowerShell-lel Azure Stack hub-ban](../user/azure-stack-quick-create-vm-windows-powershell.md)