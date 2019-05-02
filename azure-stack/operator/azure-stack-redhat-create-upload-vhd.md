---
title: Hozzon létre és töltse fel a Red Hat Enterprise Linux rendszerű virtuális merevlemez használni az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hozhat létre és töltse fel az Azure virtuális merevlemez (VHD), amely egy Red Hat Linux operációs rendszert tartalmazza.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 04/15/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: jeffgo
ms.lastreviewed: 08/15/2018
ms.openlocfilehash: 636ac38785f2604b97ee5bf1cfc3615baa259d26
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64293827"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack"></a>Red Hat-alapú virtuális gép előkészítése az Azure Stackhez

Ebből a cikkből megtudhatja, Red Hat Enterprise Linux (RHEL) virtuális gép előkészítése az Azure Stack használata. Az RHEL, amely ebben a cikkben ismertetett verziói 7.1 +. Előkészítésekor a hipervizorok, amelyekre ez a cikk a Hyper-V, a kernel-alapú virtuális gép (KVM), és a VMware rendszer.

Red Hat Enterprise Linux-támogatás információkért tekintse meg a [Red Hat és az Azure Stack: Gyakori kérdések](https://access.redhat.com/articles/3413531).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>A Hyper-V Manager Red Hat-alapú virtuális gép előkészítése

Ez a szakasz azt feltételezi, hogy már rendelkezik egy ISO-fájlt, a Red Hat-webhelyről, és az RHEL-lemezkép telepítése virtuális merevlemezre (VHD). Operációs rendszer lemezképének telepítése Hyper-V kezelőjének használatával kapcsolatos további információkért lásd: [a Hyper-V szerepkör telepítése és konfigurálása a virtuális gép](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="rhel-installation-notes"></a>RHEL telepítési jegyzetek

* Az Azure Stack nem támogatja a VHDX formátumot. Az Azure támogatja a csak rögzített VHD. Hyper-V kezelője segítségével a lemez VHD formátumra történő konvertálása, vagy használja a convert-vhd-parancsmagot. Ha VirtualBox használ, válassza ki a **rögzített méretű** figyelésekor az alapértelmezett beállítás dinamikusan kiosztott, a lemez létrehozásakor.
* Az Azure Stack csak az 1. generációs virtuális gépeket támogatja. 1. generációs virtuális gép és a rögzített méretű lemez dinamikusan növekvő VHDX-re a VHD-fájl formátumát származó alakíthatók. Egy virtuálisgép-generáció nem módosítható. További információkért lásd: [kell hozhatok létre 1 vagy 2. generációs virtuális gépek a Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* A maximális engedélyezett a virtuális merevlemez mérete 1,023 GB.
* Amikor Linux operációs rendszert telepít, azt javasoljuk, hogy, standard partíciók helyett használjon logikai kötet-kezelő (LVM), amelyek gyakran sok telepítés az alapértelmezett érték. Ezzel a gyakorlattal elkerülheti a LVM neve nem felel meg a klónozott virtuális gépeket, különösen akkor, ha a átállítására lenne szükség hibaelhárítási egy másik virtuális gép operációsrendszer-lemez csatolása.
* Univerzális lemez formátum (UDF) fájlrendszer csatlakoztatási kernel támogatására szükség. Első rendszerindításkor az UDF-formátumú adathordozót, amely csatolva van a Vendég továbbítja az üzembe helyezési konfiguráció a Linux rendszerű virtuális gépet. Az Azure Linux-ügynök csatlakoztatnia kell az UDF-fájlrendszer, olvassa el a konfigurációját, és a virtuális gép üzembe helyezése.
* Az operációsrendszer-lemez nem konfigurál egy lapozó partíciót. A Linux-ügynök beállítható úgy, hogy hozzon létre egy ideiglenes erőforrás lemezen a lapozófájl. További információ található a következő lépéseket.
* Az Azure-ban minden virtuális merevlemezek rendelkeznie kell egy virtuális méret 1 MB igazítva. Nyers lemezről történő konvertáláskor a virtuális merevlemez, ha biztosítania kell, hogy a nyers lemez mérete nagyobb-e az átalakítás előtt 1 MB többszöröse. További részleteket az alábbi lépéseket is található.
* Az Azure Stack nem támogatja a cloud-Init használatával. A virtuális Gépet egy támogatott verzióját a Windows Azure Linux ügynök (WALA) kell konfigurálni.

### <a name="prepare-an-rhel-7-virtual-machine-from-hyper-v-manager"></a>A Hyper-V kezelőjében az RHEL 7 virtuális gép előkészítése

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.

1. Kattintson a **Connect** , nyisson meg egy konzolablakot, a virtuális gép.

1. Hozzon létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Hozzon létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és igény szerint, adja hozzá a következő szöveget:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Győződjön meg arról, hogy a hálózati szolgáltatás elindul, a rendszer indításakor a következő parancs futtatásával:

    ```bash
    sudo systemctl enable network
    ```

1. Regisztráljon a Red Hat-előfizetés az RHEL-adattárat a csomagok telepítésének engedélyezése a következő parancs futtatásával:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Módosítsa a rendszermag rendszerindítási sorához további kernel paramétereket tartalmazza az Azure-hoz a grub-hibát konfigurációjában. Hajtsa végre ezt a módosítást, nyissa meg a `/etc/default/grub` egy szövegszerkesztőben, és módosítsa a `GRUB_CMDLINE_LINUX` paraméter. Példa:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Ez biztosítja, hogy a konzol összes üzenetet kapnak-e az első soros porthoz, amely segítheti az Azure támogatási problémák hibakeresésében. Ez a konfiguráció is kikapcsolja az RHEL 7 újakat a hálózati adapterek.

   Grafikus és a csendes rendszerindító, amelyek nem hasznos, ha egy felhőalapú környezetben, ahol szeretnénk a soros port kell küldeni a naplókat. Hagyhatja az `crashkernel` konfigurálható, ha a kívánt beállítást. Vegye figyelembe, hogy ezt a paramétert csökkenti az elérhető memória a virtuális gépen legalább 128 MB, amely kisebb virtuálisgép-méretek problémás lehet. Javasoljuk, hogy távolítsa el a következő paraméterekkel:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Miután végzett szerkesztési `/etc/default/grub`, újraépítése a grub-konfiguráció a következő parancsot:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Állítsa le, és a cloud-init eltávolítása:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve és konfigurálva van rendszerindításkor, amely általában az alapértelmezett elindításához. Módosítsa `/etc/ssh/sshd_config` tartalmazza a következő sort:

    ```sh
    ClientAliveInterval 180
    ```

1. A WALinuxAgent csomag `WALinuxAgent-<version>`, a Red Hat kiegészítő funkciók tárház lett leküldve. A kiegészítő funkciók tárház engedélyezze a következő parancs futtatásával:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Az Azure Linux-ügynök telepítése a következő parancs futtatásával:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent.service
    ```

1. Nem hozható létre lapozófájl-kapacitás az operációsrendszer-lemez.

    Az Azure Linux-ügynök a helyi erőforrás-lemez, amely a virtuális gép csatlakozik az Azure-ban a virtuális gép kiépítése után segítségével automatikusan konfigurálhatják a lapozófájl-kapacitás. A helyi erőforrás lemez egy ideiglenes lemez, és előfordulhat, hogy kell üríteni, ha a virtuális gép megszüntetése van. Miután telepítette az Azure Linux-ügynök az előző lépésben, módosítsa a következő paramétereket lévő `/etc/waagent.conf` megfelelően:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Ha szeretne az előfizetés regisztrációjának törlése, futtassa a következő parancsot:

    ```bash
    sudo subscription-manager unregister
    ```

1. Ha egy rendszer, amely egy vállalati hitelesítésszolgáltató használatával tették használ, az RHEL virtuális gép nem megbízhatóként fognak az Azure Stack főtanúsítványának. Kell elhelyezni, hogy a megbízható főtanúsítvány-tárolóba. Lásd: [, a kiszolgáló hozzáadása a megbízható főtanúsítványok](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Futtassa az alábbi parancsokat a virtuális gép megszüntetése és kiépítése az Azure előkészítése:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Kattintson a **művelet** > **Leállítás** a Hyper-V kezelőjében.

1. A virtuális merevlemez átalakítása rögzített méretű VHD a Hyper-V Manager "lemez Szerkesztés" funkciót, vagy a Convert-VHD PowerShell-parancs használatával. A Linux rendszerű VHD-t most már készen áll a tölthető fel az Azure-bA.

## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>A KVM Red Hat-alapú virtuális gép előkészítése

1. Töltse le az RHEL 7 KVM képe a Red Hat-webhelyről. Ez az eljárás RHEL 7 használja példaként.

1. Állítsa be egy rendszergazdai jelszót.

    Hozzon létre egy titkosított jelszót, és másolja a parancs kimenete:

    ```bash
    openssl passwd -1 changeme
    ```

   Állítsa be egy rendszergazdai jelszót a guestfish:

    ```sh
    guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   A gyökér szintű felhasználó, a második mező módosítása "el" a titkosított jelszót.

1. Hozzon létre egy virtuális gép KVM qcow2 lemezképről. A lemez típusa **qcow2**, és állítsa a virtuális hálózati adapter eszközmodell **virtio**. Ezután indítsa el a virtuális gépet, és jelentkezzen be gyökérszintű felhasználóként.

1. Hozzon létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Hozzon létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és adja hozzá a következő szöveget:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Győződjön meg arról, hogy a hálózati szolgáltatás elindul, a rendszer indításakor a következő parancs futtatásával:

    ```bash
    sudo systemctl enable network
    ```

1. Regisztráljon a Red Hat-előfizetés az RHEL-adattárat a csomagok telepítésének engedélyezését a következő parancs futtatásával:

    ```bash
    subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Módosítsa a rendszermag rendszerindítási sorához további kernel paramétereket tartalmazza az Azure-hoz a grub-hibát konfigurációjában. A konfiguráció végrehajtását, nyissa meg a `/etc/default/grub` egy szövegszerkesztőben, és módosítsa a `GRUB_CMDLINE_LINUX` paraméter. Példa:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Ezzel a paranccsal emellett biztosítja, hogy a konzol összes üzenetet kapnak-e az első soros porthoz, amely segítheti az Azure támogatási problémák hibakeresésében. A parancs emellett kapcsolja ki az új RHEL 7 elnevezési konvencióinak hálózati adapterek

   Grafikus és a csendes rendszerindító, amelyek nem hasznos, ha egy felhőalapú környezetben, ahol minden napló a soros port érkeznek. Hagyhatja az `crashkernel` konfigurálható, ha a kívánt beállítást. Ezt a paramétert legalább 128 MB, a virtuális gépet a rendelkezésre álló memória mennyisége csökkenti, ami kisebb virtuálisgép-méretek problémás lehet. Javasoljuk, hogy távolítsa el a következő paraméterekkel:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Miután végzett szerkesztési `/etc/default/grub`, újraépítése a grub-konfiguráció a következő parancsot:

    ```bash
    grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Adja hozzá a Hyper-V-modulokkal initramfs.

    Szerkesztés `/etc/dracut.conf` , és adja hozzá a tartalmat:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Építse újra initramfs:

    ```bash
    dracut -f -v
    ```

1. Állítsa le, és a cloud-init eltávolítása:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve és konfigurálva van rendszerindítás elindításához:

    ```bash
    systemctl enable sshd
    ```

    Módosítsa a /etc/ssh/sshd_config tartalmazza a következő sorokat:

    ```sh
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. Ha egy egyéni virtuális merevlemezt hoz létre az Azure Stack, vegye figyelembe, hogy WALinuxAgent verzió 2.2.20 és 2.2.35 (mindkét kizárólagos) között az Azure Stack-környezetekben nem működnek. Verziók 2.2.20/2.2.35 verzió használatával készítse elő a képet. 2.2.35 újabb verziók készíti elő az egyéni rendszerkép használatához frissítse az Azure Stack 1903 kiadásban, vagy 1901/1902 gyorsjavítást. 

     Kövesse ezeket az utasításokat a WALinuxAgent letöltése:
    
   a.   Setuptools letöltése
    ```bash
    wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
    tar xzf setuptools-7.0.tar.gz
    cd setuptools-7.0
    ```
   b. Itt látható egy példa, hogy töltse le az "2.2.20" verzió, a GitHub-adattárból. Töltse le és csomagolja ki a 2.2.20 a github-ügynök verziója. 
    ```bash
    wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
    unzip v2.2.20.zip
    cd WALinuxAgent-2.2.20
    ```
    c. Telepítse a setup.py
    ```bash
    sudo python setup.py install
    ```
    d. Indítsa újra a waagent
    ```bash
    sudo systemctl restart waagent
    ```
    e. Ha az ügynök verziója megegyezik a letöltött teszteléséhez. Ebben a példában 2.2.20 kell lennie.
    
    ```bash
    waagent -version
    ```

1. Nem hozható létre lapozófájl-kapacitás az operációsrendszer-lemez.

    Az Azure Linux-ügynök a helyi erőforrás-lemez, amely a virtuális gép csatlakozik az Azure-ban a virtuális gép kiépítése után segítségével automatikusan konfigurálhatják a lapozófájl-kapacitás. A helyi erőforrás lemez egy ideiglenes lemez, és előfordulhat, hogy kell üríteni, ha a virtuális gép megszüntetése van. Miután telepítette az Azure Linux-ügynök az előző lépésben, módosítsa a következő paramétereket lévő `/etc/waagent.conf` megfelelően:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Előfizetés regisztrációjának törlése a (ha szükséges) a következő parancs futtatásával:

    ```bash
    subscription-manager unregister
    ```

1. Ha egy rendszer, amely egy vállalati hitelesítésszolgáltató használatával tették használ, az RHEL virtuális gép nem megbízhatóként fognak az Azure Stack főtanúsítványának. Kell elhelyezni, hogy a megbízható főtanúsítvány-tárolóba. Lásd: [, a kiszolgáló hozzáadása a megbízható főtanúsítványok](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Futtassa az alábbi parancsokat a virtuális gép megszüntetése és kiépítése az Azure előkészítése:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Állítsa le a virtuális gép KVM.

1. Konvertálja a qcow2 kép VHD formátumra.

    > [!NOTE]
    > Egy ismert hiba tapasztalható qemu-img verzió > = 2.2.1-es, amely egy helytelenül formázott virtuális merevlemez eredményez. A hiba elhárítása QEMU 2.6. Ajánlott qemu-img 2.2.0 vagy alacsonyabb, vagy az 2.6 vagy újabb verziójára frissíteni. Referencia: https://bugs.launchpad.net/qemu/+bug/1490611.

    Először konvertálja a kép fájlt nyers formátumban:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Győződjön meg arról, hogy a nyers kép méretének 1 MB teljesíti. Ellenkező esetben a méret 1 MB igazodva felfelé kerekítés:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    A nyers lemez konvertálása rögzített méretű virtuális merevlemez:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Vagy qemu verziójával **2.6** közé tartozik a `force_size` lehetőséget:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Red Hat-alapú virtuális gép előkészítése a VMware-ből

Ez a szakasz azt feltételezi, hogy már telepítve van egy RHEL virtuális gép VMware-ben. VMware-ben az operációs rendszer telepítésével kapcsolatos részletekért lásd: [VMware vendég operációs rendszer telepítési útmutató](https://partnerweb.vmware.com/GOSIG/home.html).

* A Linux operációs rendszer telepítésekor azt javasoljuk, hogy standard partíciók helyett használ LVM, amelyek gyakran sok telepítés az alapértelmezett érték. LVM neve nem felel meg a klónozott virtuális gép, ezáltal elkerülhető, különösen, ha az operációsrendszer-lemez minden eddiginél kell hibaelhárítási egy másik virtuális géphez csatlakoztatható. LVM vagy RAID is használható adatlemezek beküldésére.
* Az operációsrendszer-lemez nem konfigurál egy lapozó partíciót. Konfigurálhatja a Linux-ügynök az ideiglenes erőforrás lemezen a lapozófájl létrehozásához. További információ található a szükséges.
* A virtuális merevlemez létrehozásakor válassza **Store virtuális lemez egyetlen fájlként**.

### <a name="prepare-an-rhel-7-virtual-machine-from-vmware"></a>A VMware-ből egy RHEL 7 virtuális gép előkészítése

1. Hozzon létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Hozzon létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és adja hozzá a következő szöveget:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Győződjön meg arról, hogy a hálózati szolgáltatás indításakor elindul a következő parancs futtatásával:

    ```bash
    sudo chkconfig network on
    ```

1. Regisztráljon a Red Hat-előfizetés az RHEL-adattárat a csomagok telepítésének engedélyezése a következő parancs futtatásával:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Módosítsa a rendszermag rendszerindítási sorához további kernel paramétereket tartalmazza az Azure-hoz a grub-hibát konfigurációjában. Hajtsa végre ezt a módosítást, nyissa meg a `/etc/default/grub` egy szövegszerkesztőben, és módosítsa a `GRUB_CMDLINE_LINUX` paraméter. Példa:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

    Ez a konfiguráció emellett biztosítja, hogy a konzol összes üzenetet kapnak-e az első soros porthoz, amely segítheti az Azure támogatási problémák hibakeresésében. Azt is kikapcsolja az RHEL 7 újakat hálózati adapterek. Emellett javasoljuk, hogy távolítsa el a következő paraméterekkel:

    ```sh
    rhgb quiet crashkernel=auto
    ```

    Grafikus és a csendes rendszerindító, amelyek nem hasznos, ha egy felhőalapú környezetben, ahol szeretnénk a soros port kell küldeni a naplókat. Hagyhatja az `crashkernel` konfigurálható, ha a kívánt beállítást. Vegye figyelembe, hogy ezt a paramétert csökkenti az elérhető memória a virtuális gépen legalább 128 MB, amely kisebb virtuálisgép-méretek problémás lehet.

1. Miután végzett szerkesztési `/etc/default/grub`, újraépítése a grub-konfiguráció a következő parancsot:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Adja hozzá a Hyper-V-modulokkal initramfs.

    Szerkesztés `/etc/dracut.conf`, adja hozzá a tartalmat:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Építse újra initramfs:

    ```bash
    dracut -f -v
    ```

1. Állítsa le, és a cloud-init eltávolítása:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve és konfigurálva van rendszerindítás elindításához. Ez a beállítás általában az alapértelmezett érték. Módosítsa `/etc/ssh/sshd_config` tartalmazza a következő sort:

    ```sh
    ClientAliveInterval 180
    ```

1. A WALinuxAgent csomag `WALinuxAgent-<version>`, a Red Hat kiegészítő funkciók tárház lett leküldve. A kiegészítő funkciók tárház engedélyezze a következő parancs futtatásával:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Az Azure Linux-ügynök telepítése a következő parancs futtatásával:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent.service
    ```

1. Nem hozható létre lapozófájl-kapacitás az operációsrendszer-lemez.

    Az Azure Linux-ügynök a helyi erőforrás-lemez, amely a virtuális gép csatlakozik az Azure-ban a virtuális gép kiépítése után segítségével automatikusan konfigurálhatják a lapozófájl-kapacitás. Vegye figyelembe, hogy a helyi erőforrás lemez egy ideiglenes lemez, és előfordulhat, hogy kell üríteni, ha a virtuális gép megszüntetése van. Miután telepítette az Azure Linux-ügynök az előző lépésben, módosítsa a következő paramétereket lévő `/etc/waagent.conf` megfelelően:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    NOTE: set this to whatever you need it to be.
    ```

1. Ha szeretne az előfizetés regisztrációjának törlése, futtassa a következő parancsot:

    ```bash
    sudo subscription-manager unregister
    ```

1. Ha egy rendszer, amely egy vállalati hitelesítésszolgáltató használatával tették használ, az RHEL virtuális gép nem megbízhatóként fognak az Azure Stack főtanúsítványának. Kell elhelyezni, hogy a megbízható főtanúsítvány-tárolóba. Lásd: [, a kiszolgáló hozzáadása a megbízható főtanúsítványok](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Futtassa az alábbi parancsokat a virtuális gép megszüntetése és kiépítése az Azure előkészítése:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Állítsa le a virtuális gépet, és a VMDK-fájl átalakítása a VHD formátum.

    > [!NOTE]
    > Egy ismert hiba tapasztalható qemu-img verzió > = 2.2.1-es, amely egy helytelenül formázott virtuális merevlemez eredményez. A hiba elhárítása QEMU 2.6. Ajánlott qemu-img 2.2.0 vagy alacsonyabb, vagy az 2.6 vagy újabb verziójára frissíteni. Referencia: <https://bugs.launchpad.net/qemu/+bug/1490611>.

    Először konvertálja a kép fájlt nyers formátumban:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Győződjön meg arról, hogy a nyers kép méretének 1 MB teljesíti. Ellenkező esetben a méret 1 MB igazodva felfelé kerekítés:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    A nyers lemez konvertálása rögzített méretű virtuális merevlemez:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Vagy qemu verziójával **2.6** közé tartozik a `force_size` lehetőséget:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Red Hat-alapú virtuális gép előkészítése ISO-fájlból automatikusan kickstart fájl használatával

1. Hozzon létre egy kickstart fájlt, amely tartalmazza a következő tartalmat, és mentse a fájlt. Kickstart telepítéssel kapcsolatos részletekért lásd: a [Kickstart telepítési útmutató](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

    ```sh
    Kickstart for provisioning a RHEL 7 Azure VM

    System authorization information
    auth --enableshadow --passalgo=sha512

    Use graphical install
    text

    Do not run the Setup Agent on first boot
    firstboot --disable

    Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    System language
    lang en_US.UTF-8

    Network information
    network  --bootproto=dhcp

    Root password
    rootpw --plaintext "to_be_disabled"

    System services
    services --enabled="sshd,waagent,NetworkManager"

    System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    Partition clearing information
    clearpart --all --initlabel

    Clear the MBR
    zerombr

    Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    System bootloader configuration
    bootloader --location=mbr

    Firewall configuration
    firewall --disabled

    Enable SELinux
    selinux --enforcing

    Don't configure X
    skipx

    Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    Install latest repo update
    yum update -y

    Stop and Uninstall cloud-init
    systemctl stop cloud-init
    yum remove cloud-init
    
    Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    Install WALinuxAgent
    yum install -y WALinuxAgent

    Unregister Red Hat subscription
    subscription-manager unregister

    Enable waaagent at boot-up
    systemctl enable waagent

    Disable the root account
    usermod root -p '!!'

    Configure swap in WALinuxAgent
    sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
    sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

    Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    EOF

    Deprovision and prepare for Azure
    waagent -force -deprovision

    %end
    ```

1. Helyezze a kickstart fájlt, ahol a telepítési rendszer hozzáférhet.

1. A Hyper-V kezelőjében hozzon létre egy új virtuális gépet. Az a **virtuális merevlemez csatlakoztatása** lapon jelölje be **egy virtuális merevlemez csatlakoztatása később**, és végezze el az új virtuális gép varázsló.

1. Nyissa meg a virtuális gép beállításait:

    a. Egy új virtuális merevlemez csatlakoztatása a virtuális géphez. Ügyeljen arra, hogy válassza ki, hogy **VHD formátumú** és **rögzített méretű**.

    b. A telepítés ISO csatolja a DVD-meghajtóhoz.

    c. Állítsa be a CD-ről a BIOS-ban.

1. Virtuális gép elindítása. Amikor megjelenik a telepítési útmutatóban, nyomja le az ENTER **lapon** a rendszerindítási beállításokat.

1. Adjon meg `inst.ks=<the location of the kickstart file>` a rendszerindítási beállítások, majd nyomja le az végén **Enter**.

1. Várjon, amíg a telepítés befejeződik. Amikor elkészült, a virtuális gép automatikus leállítása. A Linux rendszerű VHD-t most már készen áll a tölthető fel az Azure-bA.

## <a name="known-issues"></a>Ismert problémák

### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>A Hyper-V-illesztőprogram sikerült nem szerepelnek a kezdeti RAM diszk nem Hyper-V hipervizort használatakor

Bizonyos esetekben Linux telepítők előfordulhat, hogy nem tartalmaznak az illesztőprogramokat a Hyper-V a kezdeti RAM diszk (initrd vagy initramfs), ha a Hyper-V környezetben futó Linux nem észlel.

A Linux-rendszerképek előkészítése használ a különböző virtualizálási rendszerek (azaz Oracle virtuális gép VirtualBox, Xen-projekt, stb.), amikor szüksége lehet építeni initrd annak érdekében, hogy legalább az hv_vmbus, hv_storvsc és kernel-modulok elérhetők a kezdeti RAM a lemez. Ez az egy ismert probléma, legalább a felsőbb rétegbeli Red Hat terjesztési alapuló rendszereken.

A probléma megoldásához initramfs ad hozzá a Hyper-V-modulokkal és azt újjáépítenie:

Szerkesztés `/etc/dracut.conf`, és adja hozzá az alábbi tartalommal:

```sh
add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
```

Építse újra initramfs:

```bash
dracut -f -v
```

További információkért lásd: [initramfs újraépítése](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>További lépések

Most már készen áll a Red Hat Enterprise Linux virtuális merevlemez használatával hozzon létre új virtuális gépek az Azure Stackben. Ha ez az első alkalommal videófájl fel a VHD-fájlt az Azure Stack, lásd: [létrehozása és a Piactéri elem közzététele](azure-stack-create-and-publish-marketplace-item.md).

A hipervizorok, amely tanúsítottan futtatja Red Hat Enterprise Linux kapcsolatos további információkért lásd: [a Red Hat-webhely](https://access.redhat.com/certified-hypervisors).
