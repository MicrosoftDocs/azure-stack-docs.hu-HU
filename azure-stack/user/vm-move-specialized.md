---
title: Speciális virtuális gép áthelyezése a helyszínről a Azure Stack hubhoz
description: Megtudhatja, hogyan helyezhet át egy speciális virtuális gépet a helyszínről Azure Stack hubhoz.
author: mattbriggs
ms.topic: how-to
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: b5d46af0ebb5a1ae059ff7f778c8177e0ea4a04d
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247846"
---
# <a name="move-a-specialized-vm-from-on-premises-to-azure-stack-hub"></a>Speciális virtuális gép áthelyezése a helyszínről a Azure Stack hubhoz

A virtuális gép (VM) rendszerképét a helyszíni környezetből is hozzáadhatja. A lemezképet virtuális merevlemezként (VHD) is létrehozhatja, és feltöltheti a lemezképet egy Storage-fiókba az Azure Stack hub-példányban. Ezután létrehozhat egy virtuális gépet a VHD-ből.

A speciális lemezképek a virtuális merevlemezek (VHD-k) másolatai egy meglévő virtuális gépről, amely tartalmazza az eredeti virtuális gépről származó felhasználói fiókokat, alkalmazásokat és egyéb állapotinformációkat. Ez általában a virtuális gépek áttelepítésének formátuma Azure Stack hubhoz. A speciális VHD-k jó illeszkedést biztosítanak, ha a helyszíni rendszerből Azure Stack hubhoz kell áttelepítenie a virtuális gépeket.

## <a name="how-to-move-an-image"></a>Rendszerkép áthelyezése

Keresse meg a virtuális merevlemez előkészítésekor a saját igényeinek megfelelő szakaszt.

#### <a name="windows-vm"></a>[Windows rendszerű virtuális gép](#tab/port-win)

- Kövesse a [Windows VHD vagy VHDX előkészítése az Azure](/azure/virtual-machines/windows/prepare-for-upload-vhd-image) -ba való feltöltéséhez című témakör lépéseit a VHD megfelelő előkészítéséhez. Azure Stack hubhoz virtuális merevlemezt kell használnia.
   > [!NOTE]  
   > **Ne általánosítsa** a virtuális gépet a Sysprep használatával.
- Távolítsa el a virtuális gépre telepített összes vendég virtualizációs eszközt és ügynököt (például a VMware-eszközökre).
- Győződjön meg arról, hogy a virtuális gép konfigurálva van a DHCP IP-címének és DNS-beállításainak lekérésére. Ez biztosítja, hogy a kiszolgáló az indításkor megszerezze a virtuális hálózaton belüli IP-címet.
- Győződjön meg arról, hogy az RDP/SSH engedélyezve van, és a tűzfal engedélyezi a kommunikációt.
- Virtuálisgép-bővítmények üzembe helyezéséhez győződjön meg arról, hogy a virtuális gép ügynöke `.msi` elérhető. Útmutatásért lásd: az [Azure Virtual Machine Agent áttekintése](/azure/virtual-machines/extensions/agent-windows). Ha a virtuális gép ügynöke nem található a VHD-ben, a bővítmény telepítése sikertelen lesz. Nincs szükség az operációs rendszer profiljának beállítására a kiépítés során vagy a beállításakor `$vm.OSProfile.AllowExtensionOperations = $true` .

#### <a name="linux-vm"></a>[Linux rendszerű virtuális gép](#tab/port-linux)

#### <a name="generalize-the-vhd"></a>A VHD általánosítása

Kövesse a megfelelő utasításokat a virtuális merevlemez előkészítéséhez a linuxos operációs rendszerhez:

- [CentOS-alapú disztribúciók](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](../operator/azure-stack-redhat-create-upload-vhd.md)
- [SLES vagy openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

> [!IMPORTANT]
> Ne futtassa az utolsó lépést: ( `sudo waagent -force -deprovision` ), mivel ez általánosítja a VHD-t.

#### <a name="identify-the-version-of-the-linux-agent"></a>A Linux-ügynök verziójának azonosítása

Határozza meg, hogy a Linux-ügynök melyik verziója van telepítve a forrásként szolgáló virtuálisgép-rendszerképben. Futtassa a következő parancsokat. A kiépítési kódot leíró verziószám a `WALinuxAgent-` `Goal state agent` következőket mutatja:

   ```bash  
   waagent -version
   ```
    
   Például:
    
   ```bash  
   waagent -version
   WALinuxAgent-2.2.45 running on centos 7.7.1908
   Python: 2.7.5
   Goal state agent: 2.2.46
   ```

#### <a name="linux-agent-224-and-earlier-disable-the-linux-agent-provisioning"></a>A Linux-ügynök 2.2.4-es és korábbi verzióiban tiltsa le a Linux-ügynök üzembe helyezését 

Tiltsa le a Linux-ügynök kiépítési felállítását a 2.2.4-nél alacsonyabb Linux-ügynökkel, állítsa be a következő paramétereket a **/etc/waagent.conf**: `Provisioning.Enabled=n, and Provisioning.UseCloudInit=n` .

#### <a name="linux-agent-2245-and-later-disable-the-linux-agent-provisioning"></a>Linux-ügynök 2.2.45 és újabb verziók, a Linux-ügynök üzembe helyezésének letiltása

Ha le szeretné tiltani az üzembe helyezést a Linux-ügynök 2.2.45 és újabb verzióival, végezze el a következő konfigurációs beállítások módosítását:

`Provisioning.Enabled` és `Provisioning.UseCloudInit` mostantól figyelmen kívül maradnak.

Ebben a verzióban jelenleg nincs `Provisioning.Agent` lehetőség a teljes kiépítés letiltására, azonban a kiépítési jelölő fájlját is hozzáadhatja, és a következő beállításokkal a kiépítés figyelmen kívül lesz hagyva:

1. A **/etc/waagent.conf** adja hozzá ezt a konfigurációs lehetőséget: `Provisioning.Agent=Auto` .
2. A walinuxagent kiépítés letiltásának biztosításához futtassa a következőt: `mkdir -p /var/lib/waagent && touch /var/lib/waagent/provisioned` .
3. Tiltsa le a Cloud-init telepítését a következő futtatásával:

   ```bash  
   touch /etc/cloud/cloud-init.disabled
   sudo sed -i '/azure_resource/d' /etc/fstab
   ```

4. Jelentkezzen ki.

#### <a name="run-an-extension"></a>Bővítmény futtatása

1. Állítsa be a következő paramétert a **/etc/waagent.conf**-ben:

   - `Provisioning.Enabled=n`
   - `Provisioning.UseCloudInit=n`

2. A walinuxagent kiépítés letiltásának biztosításához futtassa a következőt: `mkdir -p /var/lib/waagent && touch /var/lib/waagent/provisioned`

3. Ha a rendszerképben a Cloud-init van, tiltsa le a Cloud init:

    ```bash  
   touch /etc/cloud/cloud-init.disabled
   sudo sed -i '/azure_resource/d' /etc/fstab
   ```

4. Kijelentkezés végrehajtása.

---

## <a name="verify-your-vhd"></a>A VHD ellenőrzése

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]

## <a name="upload-to-a-storage-account"></a>Feltöltés egy Storage-fiókba

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-disk-in-azure-stack-hub"></a>Lemez létrehozása Azure Stack központban

[!INCLUDE [Create the disk in Azure Stack Hub](../includes/user-compute-create-disk.md)]

## <a name="next-steps"></a>Következő lépések

[Virtuális gép áthelyezése Azure Stack hub-ra – áttekintés](vm-move-overview.md)
