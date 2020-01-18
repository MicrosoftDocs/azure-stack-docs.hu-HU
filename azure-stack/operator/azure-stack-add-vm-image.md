---
title: Egyéni virtuálisgép-rendszerkép hozzáadása Azure Stack hubhoz | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá vagy távolíthat el egy egyéni virtuálisgép-rendszerképet Azure Stack hubhoz.
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
ms.date: 10/16/2019
ms.author: Justinha
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: f0d0b268445d3de95e8f4dcaa0d44cb8d553111c
ms.sourcegitcommit: 7dd685fddf2f5d7a0c0a20fb8830ca5a061ed031
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76259817"
---
# <a name="add-a-custom-vm-image-to-azure-stack-hub"></a>Egyéni virtuálisgép-rendszerkép hozzáadása Azure Stack hubhoz

Azure Stack hub-ban hozzáadhat egyéni virtuálisgép-rendszerképet a piactérhez, és elérhetővé teheti azt a felhasználók számára. A Azure Stack hub Marketplace-hez a felügyeleti portálon vagy a Windows PowerShellen keresztül adhat hozzá virtuálisgép-lemezképeket. A globális Azure Marketplace-ről származó rendszerképeket használhatja az egyéni rendszerkép alapjaként, vagy saját maga hozhat létre a Hyper-V használatával.

## <a name="step-1-create-the-custom-vm-image"></a>1\. lépés: az egyéni virtuálisgép-rendszerkép létrehozása

### <a name="windows"></a>Windows

Hozzon létre egy egyéni általánosított virtuális merevlemezt. 

**Ha a VHD**-t az Azure-on kívülről futtatja, kövesse az [általánosított virtuális merevlemez feltöltése](/azure/virtual-machines/windows/upload-generalized-managed) című témakör lépéseit, és használja az új virtuális gépek létrehozásához az Azure-ban, hogy a virtuális merevlemezt **helyesen** hozza létre, és általánosítsa.

**Ha a virtuális merevlemez az Azure-ból származik**, a virtuális gép általánosítása előtt ügyeljen a következőkre:
1) Amikor kiépíti a virtuális gépet az Azure-ban, használja a PowerShellt, és kiépítse a `-ProvisionVMAgent` jelző nélkül 
2) Az Azure-beli virtuális gép általánosítása előtt távolítsa el az összes virtuálisgép-bővítményt a **Remove-azurermvmextension paranccsal** parancsmag használatával a virtuális gépről. A Windows (C:) segítségével megtalálhatja, hogy mely virtuálisgép-bővítmények legyenek telepítve. > WindowsAzure > Naplók > beépülő modulokat.

```Powershell
Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```                       
A fentiek elvégzése után kövesse a [jelen dokumentum](/azure/virtual-machines/windows/download-vhd) utasításait a virtuális merevlemez megfelelő általánosítása és letöltése előtt a Azure stack hubhoz való csatlakozás előtt.

### <a name="linux"></a>Linux

**Ha a virtuális merevlemez az Azure-on kívülről van**, kövesse a megfelelő utasításokat a virtuális merevlemez általánosításához:

- [CentOS-alapú disztribúciók](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](/azure/azure-stack/azure-stack-redhat-create-upload-vhd)
- [SLES vagy openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

**Ha a VHD az Azure-ból származik**, kövesse az alábbi utasításokat a virtuális merevlemez általánosításához és letöltéséhez:

1. A **waagent** szolgáltatás leállítása:

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   Tartsa szem előtt az Azure Linux-ügynök azon verzióit, amelyek a Azure Stack hub-ban is működnek az [itt dokumentált módon](azure-stack-linux.md#azure-linux-agent). Győződjön meg arról, hogy a Sysprep használatával létrehozott-rendszerkép rendelkezik az Azure Linux-ügynök olyan verziójával, amely kompatibilis az Azure Stack hubhoz.

2. Állítsa le a virtuális gép felszabadítását.

3. Töltse le a VHD-t.

   1. A VHD-fájl letöltéséhez egy közös hozzáférési aláírás (SAS) URL-címét kell létrehoznia. Az URL-cím létrehozásakor a rendszer lejárati időt rendel az URL-címhez.

   1. A virtuális gép paneljének menüjében válassza a **lemezek**lehetőséget.

   1. Válassza ki a virtuális gép operációsrendszer-lemezét, majd válassza a **lemez exportálása**lehetőséget.

   1. Állítsa be az URL-cím lejárati idejét 36000-ra.

   1. Válassza az **URL-cím előállítása**lehetőséget.

   1. Az URL-cím előállítása.
   
   1. A létrehozott URL-cím alatt válassza **a VHD-fájl letöltése**lehetőséget.

   1. Előfordulhat, hogy a letöltés indításához a böngésző **Mentés** elemét kell választania. A VHD-fájl alapértelmezett neve _ABCD_.

### <a name="considerations"></a>Megfontolandó szempontok

A rendszerkép feltöltése előtt fontos figyelembe venni a következőket:

- Azure Stack hub csak az 1. generációs virtuális gépeket támogatja a rögzített lemezes VHD formátumban. A rögzített formátumú szerkezet a logikai lemezt lineárisan a fájlban tárolja, így az *x* lemez eltolása az *x*blob eltolása alatt tárolódik. A blob végén található kisméretű lábléc a virtuális merevlemez tulajdonságait írja le. Ha ellenőrizni szeretné, hogy a lemez kijavítva van-e, használja a **Get-VHD** PowerShell-parancsmagot.

- Azure Stack hub nem támogatja a dinamikus lemezes virtuális merevlemezeket. 

## <a name="step-2-upload-the-vm-image-to-a-storage-account"></a>2\. lépés: a virtuális gép rendszerképének feltöltése egy Storage-fiókba

1. [Telepítse a powershellt Azure stack hubhoz](azure-stack-powershell-install.md).  

2. Jelentkezzen be Azure Stack hubhoz operátorként. Útmutatásért lásd: [bejelentkezés Azure stack hubhoz operátorként](azure-stack-powershell-configure-admin.md).

3. A lemezképeket blob Storage URI-azonosítóval kell tudni hivatkozni. Készítse elő a Windows vagy Linux operációs rendszer rendszerképét VHD formátumban (ne VHDX), majd töltse fel a rendszerképet egy Azure Stack hub-beli Storage-fiókba.

   - Ha a VHD az Azure-ban található, a [Azcopy](/azure/storage/common/storage-use-azcopy) eszközzel közvetlenül átviheti a virtuális merevlemezt egy Azure és az Azure stack hub Storage-fiók között, ha egy csatlakoztatott Azure stack központban fut.

   - Ha a virtuális merevlemez az Azure-ban van leAzure Stack választva, akkor le kell töltenie a virtuális merevlemezt egy olyan gépre, amely az Azure-hoz és a Azure Stack hub-hoz is kapcsolódik. Ezután másolja a virtuális merevlemezt erre a gépre az Azure-ból, mielőtt a VHD-t átmásolja Azure Stack hubhoz az Azure-ban és Azure Stack hub-ban használható közös [Storage adatátviteli eszköz](../user/azure-stack-storage-transfer.md) használatával.

     A példában szereplő egyik ilyen eszköz az Add-AzureRmVHD parancs, amely egy virtuális merevlemezt tölt fel egy Storage-fiókba az Azure Stack hub felügyeleti portálján.  

     ```powershell
     Add-AzureRmVhd -Destination "https://bash.blob.redmond.azurestack.com/sample/vhdtestingmgd.vhd" -LocalFilePath "C:\vhd\vhdtestingmgd.vhd" 
     ```

3. Jegyezze fel a blob Storage URI-jét, ahová feltölti a rendszerképet. A blob Storage URI formátuma a következő: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* . vhd.

4. Ha névtelenül szeretné elérhetővé tenni a blobot, nyissa meg a Storage-fiók blob-tárolóját, ahol a virtuális gép rendszerképét feltöltötte. Válassza a **blob**lehetőséget, majd válassza a **hozzáférési házirend**elemet. Szükség esetén létrehozhat egy közös hozzáférési aláírást a tárolóhoz, és belefoglalhatja azt a blob URI részeként. Ez a lépés gondoskodik arról, hogy a blob elérhető legyen a használathoz. Ha a blob nem érhető el névtelenül, a virtuálisgép-rendszerkép sikertelen állapotban lesz létrehozva.

   ![Nyissa meg a Storage-fiók blobokat](./media/azure-stack-add-vm-image/tca1.png)

   ![BLOB-hozzáférés beállítása nyilvánoshoz](./media/azure-stack-add-vm-image/tca2.png)

   ![BLOB-hozzáférés beállítása nyilvánoshoz](./media/azure-stack-add-vm-image/tca3.png)
   

## <a name="step-3-option-1-add-the-vm-image-as-an-azure-stack-hub-operator-using-the-portal"></a>3\. lépés, 1. lehetőség: a virtuálisgép-rendszerkép hozzáadása Azure Stack hub-kezelőként a portál használatával

1. Jelentkezzen be Azure Stack hub operátorként. A menüben válassza a **minden szolgáltatás** > a **számítási** virtuálisgép- **lemezképek** alatt > **Hozzáadás**elemet.

   ![Egyéni rendszerkép közvetlen telepítési felhasználói felülete](./media/azure-stack-add-vm-image/tca4.png)

2. A **rendszerkép létrehozása**területen adja meg a közzétevőt, az ajánlatot, az SKU-t, a verziót és az operációsrendszer-lemez blob URI-ját. Ezután válassza a **Létrehozás** lehetőséget a virtuálisgép-rendszerkép létrehozásának megkezdéséhez.
   
   ![Egyéni rendszerkép közvetlen telepítési felhasználói felülete](./media/azure-stack-add-vm-image/tca5.png)

   A rendszerkép sikeres létrehozása után a virtuális gép rendszerképének állapota **sikeresre**változik.
   
3. Rendszerkép hozzáadásakor csak Azure Resource Manager-alapú sablonok és PowerShell-telepítések esetén érhető el. Ha egy képet Piactéri elemként kíván elérhetővé tenni a felhasználók számára, tegye közzé a piactér-elemeket a piactér-elem [létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)című cikkben ismertetett lépések alapján. Ügyeljen rá, hogy a **közzétevő**, az **ajánlat**, az **SKU**és a **Version** értékeket jegyezze fel. Szüksége lesz rájuk, amikor szerkeszti a Resource Manager-sablont és a manifest. JSON fájlt az egyéni. azpkg.

## <a name="step-3-option-2-add-a-vm-image-as-an-azure-stack-hub-operator-using-powershell"></a>3\. lépés, 2. lehetőség: virtuálisgép-rendszerkép hozzáadása Azure Stack hub-kezelőként a PowerShell használatával

1. [Telepítse a powershellt Azure stack hubhoz](azure-stack-powershell-install.md).  

2. Jelentkezzen be Azure Stack hubhoz operátorként. Útmutatásért lásd: [bejelentkezés Azure stack hubhoz operátorként](azure-stack-powershell-configure-admin.md).

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
     Azon virtuálisgép-rendszerkép verziója, amelyet a felhasználók a virtuálisgép-lemezkép telepítésekor használnak. Ennek a verziónak a formátuma *\#.\#.\#* . Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  
   - **osType**  
     Például:`Linux`  
     A rendszerkép **OsType** Windows vagy **Linux** **rendszernek** kell lennie.  
   - **OSUri**  
     Például:`https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Megadhat egy blob Storage URI-t egy `osDisk`hoz.  

     További információkért lásd az [Add-AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage) parancsmag PowerShell-referenciáját.
     
4. Rendszerkép hozzáadásakor csak Azure Resource Manager-alapú sablonok és PowerShell-telepítések esetén érhető el. Ha egy képet Piactéri elemként kíván elérhetővé tenni a felhasználók számára, tegye közzé a piactér-elemeket a piactér-elem [létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)című cikkben ismertetett lépések alapján. Ügyeljen rá, hogy a **közzétevő**, az **ajánlat**, az **SKU**és a **Version** értékeket jegyezze fel. Szüksége lesz rájuk, amikor szerkeszti a Resource Manager-sablont és a manifest. JSON fájlt az egyéni. azpkg.

## <a name="remove-the-vm-image-as-an-azure-stack-hub-operator-using-the-portal"></a>A virtuális gép rendszerképének eltávolítása Azure Stack hub-kezelőként a portál használatával

1. Nyissa meg az Azure Stack hub [felügyeleti portált](https://adminportal.local.azurestack.external).

2. Ha a virtuálisgép-rendszerképhez társított Piactéri elem tartozik, válassza a **piactér-kezelés**elemet, majd válassza ki a törölni kívánt virtuálisgép-Piactéri elemet.

3. Ha a virtuálisgép-rendszerkép nem rendelkezik társított Piactéri elemmel, navigáljon az **összes szolgáltatás > a számítási >** virtuálisgép-lemezképek elemre, majd válassza a virtuális gép lemezképe melletti három pontot ( **..** .).

4. Válassza a **Törlés** elemet.

## <a name="remove-a-vm-image-as-an-azure-stack-hub-operator-using-powershell"></a>Virtuálisgép-rendszerkép eltávolítása Azure Stack hub-kezelőként a PowerShell használatával

Ha már nincs szüksége a feltöltött virtuálisgép-rendszerképre, a következő parancsmag használatával törölheti azt a piactérről:

1. [Telepítse a powershellt Azure stack hubhoz](azure-stack-powershell-install.md).

2. Jelentkezzen be Azure Stack hubhoz operátorként.

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
     Azon virtuálisgép-rendszerkép verziója, amelyet a felhasználók a virtuálisgép-lemezkép telepítésekor használnak. Ennek a verziónak a formátuma *\#.\#.\#* . Ebben a mezőben ne szerepeljen szóköz vagy egyéb speciális karakter.  

     A **Remove-AzsPlatformImage** parancsmaggal kapcsolatos további információkért tekintse meg a Microsoft PowerShell [Azure stack hub kezelő moduljának dokumentációját](/powershell/module/).

## <a name="next-steps"></a>Következő lépések

- [Egyéni Azure Stack hub Marketplace-elemek létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)
- [Virtuális gép kiépítése](../user/azure-stack-create-vm-template.md)
