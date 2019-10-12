---
title: Egyéni virtuálisgép-rendszerkép hozzáadása a Azure Stackhoz | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá vagy távolíthat el egy egyéni virtuálisgép-rendszerképet a Azure Stackhoz.
services: azure-stack
documentationcenter: ''
author: Justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: Justinha
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 9dc5039a2c8b74b14da59573758a4cf8d1a3657a
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72282658"
---
# <a name="add-a-custom-vm-to-azure-stack"></a>Egyéni virtuális gép hozzáadása Azure Stackhoz

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Azure Stack a virtuális gép (VM) rendszerképét felveheti a piactérre, és elérhetővé teheti a felhasználók számára. A rendszerképeket a Azure Stack Azure Resource Manager sablonjaival adja hozzá a rendszer. Virtuálisgép-lemezképeket is hozzáadhat az Azure Marketplace felhasználói felületéhez Piactéri elemként a felügyeleti portál vagy a Windows PowerShell használatával. Használjon egy rendszerképet a globális Azure piactéren, vagy a saját egyéni virtuálisgép-rendszerképét.

## <a name="generalize-the-vm-image"></a>A virtuálisgép-rendszerkép általánosítása

### <a name="windows"></a>Windows

Hozzon létre egy egyéni általánosított virtuális merevlemezt. Ha a VHD-t az Azure-on kívülről futtatja, kövesse az [általánosított virtuális merevlemez feltöltése](/azure/virtual-machines/windows/upload-generalized-managed) című témakör lépéseit, és használja az új virtuális gépek létrehozásához az Azure-ban, hogy a virtuális merevlemezt **helyesen** hozza létre, és általánosítsa.

Ha a virtuális merevlemez az Azure-ból származik, kövesse a [forrás virtuális gép általánosítása a Sysprep használatával](/azure/virtual-machines/windows/upload-generalized-managed#generalize-the-source-vm-by-using-sysprep) című témakör utasításait, mielőtt a Azure Stackbe portolja őket.

### <a name="linux"></a>Linux

Ha a virtuális merevlemez az Azure-on kívülről van, kövesse a megfelelő utasításokat a virtuális merevlemez általánosításához:

- [CentOS-alapú disztribúciók](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](/azure/azure-stack/azure-stack-redhat-create-upload-vhd)
- [SLES vagy openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Ha a VHD az Azure-ból származik, kövesse az alábbi utasításokat a virtuális merevlemez általánosítása érdekében:

1. A **waagent** szolgáltatás leállítása:

   ```bash
   # sudo waagent -force -deprovision
   # export HISTSIZE=0
   # logout
   ```

2. Állítsa le a virtuális gépet, és töltse le a VHD-t. Ha a virtuális merevlemezt az Azure-ból hozza, ezt a lemez exportálása paranccsal végezheti el, ahogyan az az [Azure-ból származó Windows VHD letöltése](/azure/virtual-machines/windows/download-vhd)című cikkben látható.

### <a name="common-steps-for-both-windows-and-linux"></a>A Windows és a Linux közös lépései

A rendszerkép feltöltése előtt fontos figyelembe venni a következőket:

- Azure Stack csak egy (1) virtuális gép létrehozását támogatja a rögzített lemez VHD-formátumában. A rögzített formátumú szerkezet a logikai lemezt lineárisan a fájlban tárolja, így az *x* lemez eltolása az *x*blob eltolása alatt tárolódik. A blob végén található kisméretű lábléc a virtuális merevlemez tulajdonságait írja le. Ha ellenőrizni szeretné, hogy a lemez kijavítva van-e, használja a **Get-VHD** PowerShell-parancsmagot.

- A Azure Stack nem támogatja a dinamikus lemezes virtuális merevlemezeket. Egy virtuális géphez csatolt dinamikus lemez átméretezése sikertelen állapotban hagyja a virtuális gépet. A probléma megoldásához törölje a virtuális GÉPET a virtuális gép lemezének törlése nélkül, egy VHD-blobot egy Storage-fiókban. Ezután alakítsa át a virtuális merevlemezt egy dinamikus lemezről egy rögzített lemezre, majd hozza létre újra a virtuális gépet.

## <a name="add-a-vm-image-as-an-azure-stack-operator-using-the-portal"></a>Virtuálisgép-rendszerkép hozzáadása Azure Stack operátorként a portál használatával

1. A lemezképeket blob Storage URI-azonosítóval kell tudni hivatkozni. Készítse elő a Windows vagy Linux operációs rendszer rendszerképét VHD formátumban (ne VHDX), majd töltse fel a rendszerképet egy Azure-beli vagy Azure Stack-beli Storage-fiókba.

   - Ha a VHD az Azure-ban található, a [Azcopy](/azure/storage/common/storage-use-azcopy) eszközzel közvetlenül átviheti a virtuális merevlemezt egy Azure és a Azure stack Storage-fiók között, ha csatlakoztatott Azure stackon fut.

   - Leválasztott Azure Stack esetén, ha a VHD-je az Azure-ban van, le kell töltenie a virtuális merevlemezt egy olyan gépre, amely az Azure-hoz és a Azure Stackhoz is kapcsolódik. Ezután másolja a virtuális merevlemezt erre a gépre az Azure-ból, mielőtt a VHD-t átmásolja Azure Stack az Azure-ban és a Azure Stack-ban használható közös [Storage adatátviteli eszközök](../user/azure-stack-storage-transfer.md) bármelyikével.

2. [Ezt a példát](/powershell/module/azurerm.compute/add-azurermvhd?view=azurermps-6.13.0) követve feltöltheti a virtuális merevlemezt egy Storage-fiókba a Azure stack felügyeleti portálon.

   - Hatékonyabban tölthet fel egy rendszerképet Azure Stack blob Storage-ba, mint az Azure Blob Storage-ba, mert kevesebb időt vesz igénybe a rendszerkép leküldése a Azure Stack rendszerkép-tárházba.

   - A [Windows rendszerű virtuális gép rendszerképének](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/)feltöltésekor ügyeljen arra, hogy az Azure-ba való **bejelentkezést** a [Azure stack operátor PowerShell-környezetének konfigurálása](azure-stack-powershell-configure-admin.md) lépéssel váltsa át.  

3. Jegyezze fel a blob Storage URI-jét, ahová feltölti a rendszerképet. A blob Storage URI formátuma a következő: *&lt;storageAccount @ no__t-2 @ no__t-3 @ no__t-4blobContainer @ no__t-5 @ no__t-6 @ no__t-7targetVHDName @ no__t-8*. vhd.

4. Ha névtelenül szeretné elérhetővé tenni a blobot, nyissa meg a Storage-fiók blob-tárolóját, ahol a virtuális gép rendszerképét feltöltötte. Válassza a **blob**lehetőséget, majd válassza a **hozzáférési házirend**elemet. Szükség esetén létrehozhat egy közös hozzáférési aláírást a tárolóhoz, és belefoglalhatja azt a blob URI részeként. Ez a lépés gondoskodik arról, hogy a blob elérhető legyen a használathoz. Ha a blob nem érhető el névtelenül, a virtuálisgép-rendszerkép sikertelen állapotban lesz létrehozva.

   ![Nyissa meg a Storage-fiók blobokat](./media/azure-stack-add-vm-image/image1.png)

   ![BLOB-hozzáférés beállítása nyilvánoshoz](./media/azure-stack-add-vm-image/image2.png)

5. Jelentkezzen be Azure Stack operátorként. A menüben válassza a **minden szolgáltatás** > **lemezkép** lehetőséget a **számítás**@no__t – 4**Hozzáadás**elemnél.

6. A **rendszerkép létrehozása**területen adja meg a nevet, az előfizetést, az erőforráscsoportot, a helyet, az operációsrendszer-lemezt, az operációs rendszer típusát, a Storage blob URI-ját, a fióktípus és a gazdagép gyorsítótárazását. Ezután válassza a **Létrehozás** lehetőséget a virtuálisgép-rendszerkép létrehozásának megkezdéséhez.

   ![A rendszerkép létrehozásának megkezdése](./media/azure-stack-add-vm-image/image4.png)

   A rendszerkép sikeres létrehozása után a virtuális gép rendszerképének állapota **sikeresre**változik.

7. Rendszerkép hozzáadásakor csak Azure Resource Manager-alapú sablonok és PowerShell-telepítések esetén érhető el. Ha egy képet Piactéri elemként kíván elérhetővé tenni a felhasználók számára, tegye közzé a piactér-elemeket a piactér-elem [létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)című cikkben ismertetett lépések alapján. Ügyeljen rá, hogy a **közzétevő**, az **ajánlat**, az **SKU**és a **Version** értékeket jegyezze fel. Szüksége lesz rájuk, amikor szerkeszti a Resource Manager-sablont és a manifest. JSON fájlt az egyéni. azpkg.

## <a name="remove-the-vm-image-as-an-azure-stack-operator-using-the-portal"></a>A virtuálisgép-rendszerkép eltávolítása Azure Stack operátor használatával a portálon

1. Nyissa meg a Azure Stack [felügyeleti portált](https://adminportal.local.azurestack.external).

2. Ha a virtuálisgép-rendszerképhez társított Piactéri elem tartozik, válassza a **piactér-kezelés**elemet, majd válassza ki a törölni kívánt virtuálisgép-Piactéri elemet.

3. Ha a virtuálisgép-rendszerkép nem rendelkezik társított Piactéri elemmel, navigáljon az **összes szolgáltatás > a számítási >** virtuálisgép-lemezképek elemre, majd válassza a virtuális gép lemezképe melletti három pontot ( **..** .).

4. Válassza a **Törlés** elemet.

## <a name="add-a-vm-image-as-an-azure-stack-operator-using-powershell"></a>Virtuálisgép-rendszerkép hozzáadása Azure Stack operátorként a PowerShell használatával

1. [Telepítse a powershellt Azure Stackhoz](azure-stack-powershell-install.md).  

2. Jelentkezzen be Azure Stack operátorként. Útmutatásért lásd: [Bejelentkezés a Azure stack operátorként](azure-stack-powershell-configure-admin.md).

3. Nyissa meg a PowerShellt egy emelt szintű parancssorral, és futtassa a következőket:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<Offer>" `
      -sku "<SKU>" `
      -version "<#.#.#>" `
      -OSType "<OS type>" `
      -OSUri "<OS URI>"
   ```

   Az **Add-AzsPlatformimage** parancsmag a Azure Resource Manager sablonok által a virtuálisgép-rendszerképre való hivatkozáshoz használt értékeket határozza meg. Az értékek a következők:
   - **Publisher**  
     Például:`Canonical`  
     A felhasználó által a lemezkép telepítésekor használt virtuálisgép-rendszerkép **közzétevő** neve szegmense. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **ajánlat**  
     Például:`UbuntuServer`  
     A virtuálisgép-lemezkép telepítésekor a felhasználók által használt virtuálisgép-rendszerkép **ajánlatának** neve szegmense. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **SKU**  
     Például:`14.04.3-LTS`  
     Azon virtuálisgép-rendszerkép **SKU** -neve szegmense, amelyet a felhasználók a virtuálisgép-lemezkép telepítésekor használnak. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **verziója**  
     Például:`1.0.0`  
     Azon virtuálisgép-rendszerkép verziója, amelyet a felhasználók a virtuálisgép-lemezkép telepítésekor használnak. Ennek a verziónak a formátuma *\#. \#. \#* . Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **osType**  
     Például:`Linux`  
     A rendszerkép **OsType** Windows vagy **Linux** **rendszernek** kell lennie.  
   - **OSUri**  
     Például:`https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Megadhat egy blob Storage URI-t egy `osDisk` értékhez.  

     További információkért lásd az [Add-AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage) és a [New-DataDiskObject](/powershell/module/Azs.Compute.Admin/New-DataDiskObject) parancsmagok PowerShell-referenciáját.

## <a name="remove-a-vm-image-as-an-azure-stack-operator-using-powershell"></a>Virtuálisgép-rendszerkép eltávolítása Azure Stack operátorként a PowerShell használatával

Ha már nincs szüksége a feltöltött virtuálisgép-rendszerképre, a következő parancsmag használatával törölheti azt a piactérről:

1. [Telepítse a powershellt Azure Stackhoz](azure-stack-powershell-install.md).

2. Jelentkezzen be Azure Stack operátorként.

3. Nyissa meg a PowerShellt egy emelt szintű parancssorral, és futtassa a következőket:

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<Publisher>" `
    -offer "<Offer>" `
    -sku "<SKU>" `
    -version "<Version>" `
   ```

   A **Remove-AzsPlatformImage** parancsmag a Azure Resource Manager sablonok által a virtuálisgép-rendszerképre való hivatkozáshoz használt értékeket határozza meg. Az értékek a következők:
   - **Publisher**  
     Például:`Canonical`  
     A felhasználó által a lemezkép telepítésekor használt virtuálisgép-rendszerkép **közzétevő** neve szegmense. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **ajánlat**  
     Például:`UbuntuServer`  
     A virtuálisgép-lemezkép telepítésekor a felhasználók által használt virtuálisgép-rendszerkép **ajánlatának** neve szegmense. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **SKU**  
     Például:`14.04.3-LTS`  
     Azon virtuálisgép-rendszerkép **SKU** -neve szegmense, amelyet a felhasználók a virtuálisgép-lemezkép telepítésekor használnak. Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **verziója**  
     Például:`1.0.0`  
     Azon virtuálisgép-rendszerkép verziója, amelyet a felhasználók a virtuálisgép-lemezkép telepítésekor használnak. Ennek a verziónak a formátuma *\#. \#. \#* . Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  

     A **Remove-AzsPlatformImage** parancsmaggal kapcsolatos további információkért tekintse meg a Microsoft PowerShell [Azure stack kezelő moduljának dokumentációját](/powershell/module/).

## <a name="next-steps"></a>Következő lépések

- [Egyéni Azure Stack Marketplace-elemek létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)
- [Virtuális gép kiépítése](../user/azure-stack-create-vm-template.md)
