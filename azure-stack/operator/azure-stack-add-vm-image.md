---
title: Virtuálisgép-rendszerkép hozzáadása a Azure Stackhoz | Microsoft Docs
description: Vegyen fel egy virtuálisgép-rendszerképet, vagy távolítson el egy rendszerképet a szervezet egyéni Windows-vagy Linux-alapú virtuálisgép-rendszerképéhez a bérlők számára.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 84aa627f6c274d22ebdab411d6abd1064c6ecd6d
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68417476"
---
# <a name="add-a-vm-image-to-offer-in-azure-stack"></a>Adja meg az ajánlathoz használandó virtuálisgép-rendszerképet Azure Stack

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Azure Stack a virtuális gép (VM) rendszerképét hozzáadhatja a piactérhez, hogy elérhető legyen a felhasználók számára. Virtuálisgép-lemezképeket Azure Resource Manager sablonok használatával adhat hozzá Azure Stackhoz. Piactéri elemként hozzáadhat virtuálisgép-lemezképeket is az Azure Marketplace felhasználói felületéhez. A globális Azure Marketplace-ről vagy a saját egyéni virtuálisgép-rendszerképéről is használhat rendszerképet. Virtuálisgép-rendszerképet a felügyeleti portál vagy a Windows PowerShell használatával adhat hozzá.

## <a name="add-a-vm-image-through-the-portal"></a>Virtuálisgép-rendszerkép hozzáadása a portálon keresztül

> [!NOTE]  
> Ezzel a módszerrel külön kell létrehoznia a Piactéri elemeket.

A lemezképeket blob Storage URI-azonosítóval kell tudni hivatkozni. Készítse elő a Windows vagy Linux operációs rendszer rendszerképét VHD formátumban (ne VHDX), majd töltse fel a rendszerképet egy Azure-beli vagy Azure Stack-beli Storage-fiókba. Ha a rendszerkép már fel van töltve a blob Storage-ba az Azure-ban vagy Azure Stack, kihagyhatja az 1. lépést.

1. [Töltsön fel egy Windows rendszerű virtuálisgép-lemezképet az Azure-ba a Resource Manager-alapú üzembe helyezéshez](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) , vagy Linux-lemezkép esetén kövesse az [Azure stack Linux rendszerű virtuális gépek üzembe helyezése](azure-stack-linux.md)című témakör útmutatását. A rendszerkép feltöltése előtt fontos figyelembe venni a következő tényezőket:

   - A Azure Stack csak az 1. generációs virtuális gépeket támogatja a rögzített lemezes VHD formátumban. A rögzített formátumú szerkezet a logikai lemezt lineárisan a fájlban tárolja, így az X lemez eltolása az X blob eltolása alatt tárolódik. A blob végén található kisméretű lábléc a virtuális merevlemez tulajdonságait írja le. Ha ellenőrizni szeretné, hogy a lemez kijavítva van-e, használja a [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) PowerShell-parancsot.  

     > [!IMPORTANT]  
     >  A Azure Stack nem támogatja a dinamikus lemezes virtuális merevlemezeket. Egy virtuális géphez csatolt dinamikus lemez átméretezése sikertelen állapotban hagyja a virtuális gépet. A probléma megoldásához törölje a virtuális GÉPET a virtuális gép lemezének törlése nélkül, egy VHD-blobot egy Storage-fiókban. A verzióban alakítsa át a virtuális merevlemezt egy dinamikus lemezről egy rögzített lemezre, majd hozza létre újra a virtuális gépet.

   - Hatékonyabban tölthet fel egy rendszerképet Azure Stack blob Storage-ba, mint az Azure Blob Storage-ba, mert kevesebb időt vesz igénybe a rendszerkép leküldése a Azure Stack rendszerkép-tárházba.

   - A [Windows rendszerű virtuális gép rendszerképének](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)feltöltésekor ügyeljen arra, hogy az **Azure-** ba való bejelentkezést a [Azure stack operátor PowerShell-környezetének konfigurálása](azure-stack-powershell-configure-admin.md) lépéssel helyettesítse.  

   - Jegyezze fel a blob Storage URI-jét, ahová feltölti a rendszerképet. A blob Storage URI formátuma a következő:  *&lt;storageAccount&gt;&lt;&gt;/blobContainer/targetVHDName&gt;.VHD.&lt;*

   - Ha névtelenül szeretné elérhetővé tenni a blobot, nyissa meg a Storage-fiók blob-tárolóját, ahol a virtuális gép rendszerképét feltöltötte. Válassza a **blob**lehetőséget, majd válassza a **hozzáférési házirend**elemet. Szükség esetén létrehozhat egy közös hozzáférési aláírást a tárolóhoz, és belefoglalhatja azt a blob URI részeként. Ez a lépés biztosítja, hogy a blob elérhető legyen a rendszerképként való hozzáadásához. Ha a blob nem érhető el névtelenül, a rendszer a virtuális gép rendszerképét sikertelen állapotba hozza létre.

     ![Nyissa meg a Storage-fiók blobokat](./media/azure-stack-add-vm-image/image1.png)

     ![BLOB-hozzáférés beállítása nyilvánoshoz](./media/azure-stack-add-vm-image/image2.png)

2. Jelentkezzen be Azure Stack operátorként. A menüben válassza a **minden szolgáltatás** > **lemezkép** lehetőséget a **számítási** > **Hozzáadás**lehetőség alatt.

3. A **rendszerkép létrehozása**területen adja meg a nevet, az előfizetést, az erőforráscsoportot, a helyet, az operációsrendszer-lemezt, az operációs rendszer típusát, a Storage blob URI-ját, a fióktípus és a gazdagép gyorsítótárazását. Ezután válassza a **Létrehozás** lehetőséget a virtuálisgép-rendszerkép létrehozásának megkezdéséhez.

   ![A rendszerkép létrehozásának megkezdése](./media/azure-stack-add-vm-image/image4.png)

   A rendszerkép sikeres létrehozása után a virtuális gép rendszerképének állapota Sikeresre változik.

4. Annak érdekében, hogy a virtuális gép rendszerképe könnyebben elérhető legyen a felhasználói felületen, hasznos lehet [Piactéri elem létrehozása](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Virtuálisgép-rendszerkép eltávolítása a portálról

1. Nyissa meg a felügyeleti [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external)portált a következő címen:.

2. Válassza a **piactér-kezelés**lehetőséget, majd válassza ki a törölni kívánt virtuális gépet.

3. Kattintson a **Törlés** gombra.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Virtuálisgép-rendszerkép hozzáadása a piactérhez a PowerShell használatával

> [!Note]  
> Rendszerkép hozzáadásakor csak az Azure Resource Manger-alapú sablonok és a PowerShell-telepítések számára lesz elérhető. Ahhoz, hogy egy rendszerkép elérhető legyen a felhasználók számára Piactéri elemként, tegye közzé a piactér-elemeket a cikk lépéseit követve: Piactéri elem [létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)

1. [Telepítse a powershellt Azure Stackhoz](azure-stack-powershell-install.md).  

2. Jelentkezzen be Azure Stack operátorként. Útmutatásért lásd: [Bejelentkezés a Azure stack operátorként](azure-stack-powershell-configure-admin.md).

3. Nyissa meg a PowerShellt egy emelt szintű parancssorral, és futtassa a következőket:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>" `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
   ```

   Az **Add-AzsPlatformimage** parancsmag a Azure Resource Manager sablonok által a virtuálisgép-rendszerképre való hivatkozáshoz használt értékeket határozza meg. Az értékek a következők:
   - **publisher**  
     Például:`Canonical`  
     A felhasználó által a lemezkép telepítésekor használt virtuálisgép-rendszerkép közzétevő neve szegmense. Ilyen például a **Microsoft**. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **offer**  
     Például:`UbuntuServer`  
     A virtuálisgép-lemezkép telepítésekor a felhasználók által használt virtuálisgép-rendszerkép ajánlatának neve szegmense. Ilyen például a **windowsserver**. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **sku**  
     Például:`14.04.3-LTS`  
     Azon virtuálisgép-rendszerkép SKU-neve szegmense, amelyet a felhasználók a virtuálisgép-lemezkép telepítésekor használnak. Ilyen például a **Datacenter2016**. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **verziója**  
     Például:`1.0.0`  
     Azon virtuálisgép-rendszerkép verziója, amelyet a felhasználók a virtuálisgép-lemezkép telepítésekor használnak. Ezt a verziót a  *\#következő formátumban\#kell megadni:. \#* . Ilyen például a **1.0.0**. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **osType**  
     Például:`Linux`  
     A rendszerkép osType **Windows** vagy **Linux**rendszernek kell lennie.  
   - **OSUri**  
     Például:`https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Megadhatja a blob Storage URI-JÁT `osDisk`.  

     További információ: PowerShell-hivatkozás az [Add-AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) parancsmaghoz és a [New-DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) parancsmaghoz.

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Egyéni virtuálisgép-rendszerkép hozzáadása a piactérhez a PowerShell használatával
 
1. [Telepítse a powershellt Azure Stackhoz](azure-stack-powershell-install.md).

   ```powershell
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
   ```

2. **Active Directory összevonási szolgáltatások (AD FS)** használata esetén használja a következő parancsmagot:

   ```powershell
   # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAudience endpoint for your environment>"

   # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external
    ```

3. Jelentkezzen be Azure Stack operátorként. Útmutatásért lásd: [Bejelentkezés a Azure stack operátorként](azure-stack-powershell-configure-admin.md).

4. Hozzon létre egy Storage-fiókot a globális Azure-ban vagy Azure Stack az egyéni virtuálisgép-rendszerkép tárolásához. Útmutatásért lásd [: gyors útmutató: Blobok feltöltése, letöltése és listázása a Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)használatával.

5. Készítse elő a Windows vagy Linux operációs rendszer rendszerképét VHD formátumban (nem VHDX), töltse fel a rendszerképet a Storage-fiókjába, és szerezze be azt az URI-t, ahol a PowerShell lekérheti a virtuális gép rendszerképét.  

   ```powershell
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
   ```

6. Opcionálisan A virtuálisgép-rendszerkép részeként feltöltheti az adatlemezek tömbjét. Hozza létre az adatlemezeket a New-DataDiskObject parancsmag használatával. Nyisson meg egy emelt szintű parancssorból a PowerShellt, és futtassa a következőt:

   ```powershell
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
   ```

7. Nyissa meg a PowerShellt egy emelt szintű parancssorral, és futtassa a következőket:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>" -OSType "<ostype>" -OSUri "<osuri>"
   ```

    Az Add-AzsPlatformimage parancsmaggal és a New-DataDiskObject parancsmaggal kapcsolatos további információkért tekintse meg a Microsoft PowerShell [Azure stack kezelő moduljának dokumentációját](https://docs.microsoft.com/powershell/module/).

## <a name="remove-a-vm-image-by-using-powershell"></a>Virtuálisgép-rendszerkép eltávolítása a PowerShell használatával

Ha már nincs szüksége a feltöltött virtuálisgép-rendszerképre, a következő parancsmag használatával törölheti azt a piactérről:

1. [Telepítse a powershellt Azure Stackhoz](azure-stack-powershell-install.md).

2. Jelentkezzen be Azure Stack operátorként.

3. Nyissa meg a PowerShellt egy emelt szintű parancssorral, és futtassa a következőket:

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
   ```
   A **Remove-AzsPlatformImage** parancsmag a Azure Resource Manager sablonok által a virtuálisgép-rendszerképre való hivatkozáshoz használt értékeket határozza meg. Az értékek a következők:
   - **publisher**  
     Például:`Canonical`  
     A felhasználó által a lemezkép telepítésekor használt virtuálisgép-rendszerkép közzétevő neve szegmense. Ilyen például a **Microsoft**. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **offer**  
     Például:`UbuntuServer`  
     A virtuálisgép-lemezkép telepítésekor a felhasználók által használt virtuálisgép-rendszerkép ajánlatának neve szegmense. Ilyen például a **windowsserver**. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **sku**  
     Például:`14.04.3-LTS`  
     Azon virtuálisgép-rendszerkép SKU-neve szegmense, amelyet a felhasználók a virtuálisgép-lemezkép telepítésekor használnak. Ilyen például a **Datacenter2016**. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **verziója**  
     Például:`1.0.0`  
     Azon virtuálisgép-rendszerkép verziója, amelyet a felhasználók a virtuálisgép-lemezkép telepítésekor használnak. Ezt a verziót a  *\#következő formátumban\#kell megadni:. \#* . Ilyen például a **1.0.0**. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
    
     A Remove-AzsPlatformImage parancsmaggal kapcsolatos további információkért tekintse meg a Microsoft PowerShell [Azure stack kezelő moduljának dokumentációját](https://docs.microsoft.com/powershell/module/).

## <a name="next-steps"></a>További lépések

[Virtuális gép kiépítése](../user/azure-stack-create-vm-template.md)
