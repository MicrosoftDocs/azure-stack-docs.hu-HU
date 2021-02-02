---
title: Általánosított virtuális gép áthelyezése a helyszínről Azure Stack hubhoz
description: Megtudhatja, hogyan helyezhet át egy általánosított virtuális gépet a helyszínről Azure Stack hubhoz.
author: mattbriggs
ms.topic: how-to
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 8e26ed6e67fd8ad6269c95b22e4f2e1879b96300
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247863"
---
# <a name="move-a-generalized-vm-from-on-premises-to-azure-stack-hub"></a>Általánosított virtuális gép áthelyezése a helyszínről Azure Stack hubhoz

A virtuális gép (VM) rendszerképét a helyszíni környezetből is hozzáadhatja. A lemezképet virtuális merevlemezként (VHD) is létrehozhatja, és feltöltheti a lemezképet egy Storage-fiókba az Azure Stack hub-példányban. Ezután létrehozhat egy virtuális gépet a VHD-ből.

Egy általánosított lemezkép az egyik, amely készen áll a **Sysprep** használatával az egyedi információk (például a felhasználói fiókok) eltávolítására, amely lehetővé teszi, hogy újból felhasználja a több virtuális gép létrehozásához. Az általánosított VHD-k jó illeszkedést biztosítanak az olyan rendszerképek létrehozásához, amelyeket az Azure Stack hub Cloud Operator a Piactéri elemekként való használatra tervez.

## <a name="how-to-move-an-image"></a>Rendszerkép áthelyezése

Keresse meg a virtuális merevlemez előkészítésekor a saját igényeinek megfelelő szakaszt.

#### <a name="windows-vm"></a>[Windows rendszerű virtuális gép](#tab/port-win)

Kövesse a [Windows rendszerű virtuális merevlemezek vagy VHDX előkészítése az Azure](/azure/virtual-machines/windows/prepare-for-upload-vhd-image) -ba való feltöltéséhez című témakör lépéseit, hogy a virtuális merevlemezt a feltöltés előtt megfelelően általánosítsa. Azure Stack hubhoz virtuális merevlemezt kell használnia.

#### <a name="linux-vm"></a>[Linux rendszerű virtuális gép](#tab/port-linux)

Kövesse a megfelelő utasításokat a virtuális merevlemez általánosításához a linuxos operációs rendszerhez:

- [CentOS-alapú disztribúciók](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](../operator/azure-stack-redhat-create-upload-vhd.md)
- [SLES vagy openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

---

## <a name="verify-your-vhd"></a>A VHD ellenőrzése

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]
## <a name="upload-to-a-storage-account"></a>Feltöltés egy Storage-fiókba

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-image-in-azure-stack-hub"></a>Rendszerkép létrehozása Azure Stack központban

[!INCLUDE [Create the image in Azure Stack Hub](../includes/user-compute-create-image.md)]

## <a name="next-steps"></a>Következő lépések

[Virtuális gép áthelyezése Azure Stack hub-ra – áttekintés](vm-move-overview.md)
