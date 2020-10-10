---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 4c0e9fd823b7b932ec11c61e24ce650df1cf9cc9
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91936052"
---
A virtuális merevlemez feltöltése előtt ellenőriznie kell, hogy a VHD megfelel-e a követelményeknek. Azok a VHD-k, amelyek nem felelnek meg a követelményeknek, nem töltődnek be Azure Stack központban.

1. A Hyper-V-vel talált PowerShell-modulokat fogja használni. A Hyper-V aktiválása PowerShell-modulokat támogató telepítéseket is támogat. A modult úgy is megtekintheti, hogy a PowerShellt egy emelt szintű parancssorral nyitja meg, és futtatja a következő parancsmagot:

    ```powershell  
    Get-Command -Module hyper-v
    ```

    Ha nem rendelkezik a Hyper-V-parancsokkal, tekintse meg a következő témakört: a [Hyper-v és a Windows PowerShell használata](/virtualization/hyper-v-on-windows/quick-start/try-hyper-v-powershell). 

2. Szerezze be a virtuális merevlemez elérési útját a gépen. Futtassa a következő parancsmagot:

    ```powershell  
    get-vhd <path-to-your-VHD>
    ```

    A parancsmag a VHD-objektumot fogja visszaadni, és megjeleníti az attribútumokat, például:
    
    ```powershell  
    ComputerName            : YOURMACHINENAME
    Path                    : <path-to-your-VHD>
    VhdFormat               : VHD
    VhdType                 : Fixed
    FileSize                : 68719477248
    Size                    : 68719476736
    MinimumSize             : 32212254720
    LogicalSectorSize       : 512
    PhysicalSectorSize      : 512
    BlockSize               : 0
    ParentPath              :
    DiskIdentifier          : 3C084D21-652A-4C0E-B2D1-63A8E8E64C0C
    FragmentationPercentage : 0
    Alignment               : 1
    Attached                : False
    DiskNumber              :
    IsPMEMCompatible        : False
    AddressAbstractionType  : None
    Number                  :
    ```

3. A VHD-objektummal győződjön meg arról, hogy megfelel az Azure Stack hub követelményeinek.
    - [A VHD rögzített típusú.](#vhd-is-of-fixed-type)
    - [A VHD minimális virtuális mérete legalább 20 MB.](#vhd-has-minimum-virtual-size-of-at-least-20-mb)
    - [A VHD igazítása megtörténik.](#vhd-is-aligned)
    - [VHD blob hossza = virtuális méret + VHD-lábléc hossza (512).](#vhd-blob-length) 
    
    Emellett a Azure Stack hub csak az [1. generációs virtuális gépek](#generation-one-vms) lemezképeit támogatja.

4. Ha a VHD nem kompatibilis a Azure Stack hubhoz, vissza kell térnie a forrás-és a Hyper-V-hez, létre kell hoznia egy virtuális merevlemezt, amely megfelel a követelményeknek, és feltölti a fájlt. A feltöltési folyamat lehetséges sérülésének csökkentése érdekében használja a AzCopy.

### <a name="how-to-fix-your-vhd"></a>A virtuális merevlemez javítása

A következő követelményeknek kell teljesülniük a virtuális merevlemez Azure Stack hubhoz való kompatibilitásához.

#### <a name="vhd-is-of-fixed-type"></a>A VHD rögzített típusú
**Azonosítás**: használja `get-vhd` a parancsmagot a VHD-objektum beolvasásához.  
**Javítás**: átalakíthat egy VHDX-fájlt a VHD-be, konvertálhatja a dinamikusan bővülő lemezt egy rögzített méretű lemezre, de nem módosíthatja a virtuális gép generációját.
A lemez konvertálásához használja a [Hyper-V kezelőjét vagy a PowerShellt](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-convert-the-disk) .

### <a name="vhd-has-minimum-virtual-size-of-at-least-20-mb"></a>A VHD minimális virtuális mérete legalább 20 MB
**Azonosítás**: használja `get-vhd` a parancsmagot a VHD-objektum beolvasásához.  
**Javítás**: a lemez átméretezéséhez használja a [Hyper-V kezelőjét vagy a PowerShellt](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) . 

### <a name="vhd-is-aligned"></a>A VHD igazított
**Azonosítás**: használja `get-vhd` a parancsmagot a VHD-objektum beolvasásához.  
**Javítás**: a virtuális méretnek egy (1) MB-nyi többszörösnek kell lennie. 

A lemezeknek 1 MiB-hez igazított virtuális mérettel kell rendelkezniük. Ha a VHD 1 MiB-töredék, akkor át kell méreteznie a lemezt egy több MiB-re. Azok a lemezek, amelyek egy MiB-töredékek, hibákat okoznak, amikor lemezképeket hoznak létre a feltöltött virtuális merevlemezről. A méret ellenőrzéséhez használhatja a PowerShell Get-VHD parancsmagot a "size" érték megjelenítéséhez, amelynek az Azure-ban az 1 MiB többszörösének kell lennie, a "FileSize" pedig a "size" értéket, a VHD-lábléchez pedig 512 bájtot.

A lemez átméretezéséhez használja a [Hyper-V kezelőjét vagy a PowerShellt](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) . 


### <a name="vhd-blob-length"></a>VHD blob hossza
**Azonosítás**: a parancsmag használata a `get-vhd` megjelenítéshez `Size`   
**Javítás**: a VHD-blob hossza = virtuális méret + VHD-lábléc hossza (512). A blob végén található kisméretű lábléc a virtuális merevlemez tulajdonságait írja le. `Size` Az Azure-ban az 1 MiB többszörösének kell lennie, `FileSize` amely `Size` a VHD-lábléchez tartozó + 512 bájttal egyenlő lesz.

A lemez átméretezéséhez használja a [Hyper-V kezelőjét vagy a PowerShellt](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) . 

### <a name="generation-one-vms"></a>Egy virtuális gép létrehozása
**Azonosítás**: annak megerősítéséhez, hogy a virtuális gép 1. generáció-e, használja a parancsmagot `Get-VM | Format-Table Name, Generation` .  
**Javítás**: újra létre kell hoznia a virtuális gépet a hypervisorban (Hyper-V).