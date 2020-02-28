---
title: Red Hat-alapú virtuális gép előkészítése Azure Stack hubhoz
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan hozhat létre és tölthet fel egy Red Hat Linux operációs rendszert tartalmazó Azure-beli virtuális merevlemezt (VHD-t).
author: sethmanheim
ms.topic: article
ms.date: 12/11/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 12/11/2019
ms.openlocfilehash: d3df1040faec4c14b08f358f49fe9a6e9404fdc0
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698062"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack-hub"></a>Red Hat-alapú virtuális gép előkészítése Azure Stack hubhoz

Ebből a cikkből megtudhatja, hogyan készítheti elő Red Hat Enterprise Linux (RHEL) virtuális gépet (VM) Azure Stack hub-ban való használatra. A cikkben tárgyalt RHEL-verziók 7.1-es és újabb verziói. A cikkben tárgyalt, a Hyper-V, a kernel-alapú virtuális gép (KVM) és a VMware.

Red Hat Enterprise Linux támogatási információkat a [Red Hat és a Azure stack hub: gyakori kérdések](https://access.redhat.com/articles/3413531)című témakörben talál.

## <a name="prepare-a-red-hat-based-vm-from-hyper-v-manager"></a>Red Hat-alapú virtuális gép előkészítése a Hyper-V kezelőjéből

Ez a szakasz azt feltételezi, hogy már rendelkezik egy ISO-fájllal a Red Hat webhelyén, és telepítette a RHEL-lemezképet egy virtuális merevlemezre (VHD). További információ arról, hogyan használható a Hyper-V kezelője operációsrendszer-rendszerkép telepítéséhez: [a Hyper-v szerepkör telepítése és a virtuális gép konfigurálása](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="rhel-installation-notes"></a>RHEL-telepítési megjegyzések

* Azure Stack hub nem támogatja a VHDX formátumot. Az Azure csak a rögzített VHD-t támogatja. A Hyper-V kezelőjével átalakíthatja a lemezt VHD formátumba, vagy használhatja a convert-VHD parancsmagot is. Ha VirtualBox-t használ, a lemez létrehozásakor válassza a **rögzített méret** lehetőséget a dinamikusan lefoglalt alapértelmezett beállítással szemben.
* Azure Stack hub csak az 1. generációs virtuális gépeket támogatja. Az 1. generációs virtuális gépeket átalakíthatja a VHDX-ből a VHD-fájlformátumba, és dinamikusan bővült egy rögzített méretű lemezre. A virtuális gép generációja nem módosítható. További információ: [hozzon létre egy 1. vagy 2. generációs virtuális gépet a Hyper-V-ben?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* A VHD számára engedélyezett maximális méret 1 023 GB.
* A Linux operációs rendszer telepítésekor azt javasoljuk, hogy a logikai kötet-kezelő (LVM) helyett használjon standard partíciókat, ami gyakran az alapértelmezett a sok telepítésnél. Ezzel a gyakorlattal elkerülhető, hogy az LVM neve ütközik a klónozott virtuális gépekkel, különösen akkor, ha az operációs rendszer lemezét egy másik, azonos virtuális géphez kell csatlakoztatnia a hibaelhárításhoz.
* Az univerzális lemezes formázású (UDF-) fájlrendszerek kernel-támogatása szükséges. Az első rendszerindítás során a vendéghez csatolt UDF formátumú adathordozó továbbítja a kiépítési konfigurációt a Linux rendszerű virtuális gépre. Az Azure Linux-ügynöknek csatlakoztatnia kell az UDF fájlrendszert a konfigurációjának olvasásához és a virtuális gép kiépítéséhez.
* Ne állítson be swap partíciót az operációs rendszer lemezén. A Linux-ügynök úgy konfigurálható, hogy lapozófájlt hozzon létre az ideiglenes erőforrás lemezén. További információt a következő lépésekben talál.
* Minden Azure-beli virtuális merevlemeznek 1 MB-ra igazított virtuális mérettel kell rendelkeznie. Nyers lemezről VHD-re való konvertáláskor győződjön meg arról, hogy a nyers lemez mérete 1 MB-nál több, az átalakítás előtt. További részleteket az alábbi lépésekben találhat.
* Azure Stack hub támogatja a Cloud-init-t. A [cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. A cloud-init nevezzük az első rendszerindítás során, mert nincsenek további lépéseket vagy szükséges ügynökök a alkalmazni a konfigurációt. A Cloud-init rendszerképhez való hozzáadásával kapcsolatos útmutatásért lásd: [meglévő Linux Azure-beli virtuálisgép-rendszerkép előkészítése a Cloud-init használatával](https://docs.microsoft.com/azure/virtual-machines/linux/cloudinit-prepare-custom-image).

### <a name="prepare-an-rhel-7-vm-from-hyper-v-manager"></a>RHEL 7 virtuális gép előkészítése a Hyper-V kezelőjéből

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.

1. Válassza a **Kapcsolódás** lehetőséget a konzol ablakának megnyitásához a virtuális géphez.

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és szükség szerint adja hozzá a következő szöveget:

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

1. A következő parancs futtatásával győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul:

    ```bash
    sudo systemctl enable network
    ```

1. Regisztrálja a Red Hat-előfizetését, hogy lehetővé tegye a csomagok telepítését a RHEL adattárból a következő parancs futtatásával:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. A módosítás elvégzéséhez nyissa meg `/etc/default/grub` egy szövegszerkesztőben, és módosítsa a `GRUB_CMDLINE_LINUX` paramétert. Például:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Ez a módosítás biztosítja az összes konzolos üzenet küldését az első soros portra, amely a hibakeresési problémákkal segíti az Azure-támogatást. Ez a konfiguráció a hálózati adapterek új RHEL 7 elnevezési konvencióit is kikapcsolja.

   A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra. Ha szükséges, hagyja a `crashkernel` beállítást. Ez a paraméter csökkenti a virtuális gép rendelkezésre álló memóriájának mennyiségét 128 MB vagy több értékkel, ami problémát okozhat a kisebb virtuálisgép-méreteknél. Javasoljuk, hogy távolítsa el a következő paramétereket:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Miután végzett a `/etc/default/grub`szerkesztésével, futtassa a következő parancsot a grub-konfiguráció újraépítéséhez:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. [Opcionális a 1910-es kiadás után] Cloud-init leállítása és eltávolítása:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik, ami általában az alapértelmezett. Módosítsa `/etc/ssh/sshd_config` a következő sor belefoglalásával:

    ```sh
    ClientAliveInterval 180
    ```

1. Azure Stack hub egyéni virtuális merevlemezének létrehozásakor vegye figyelembe, hogy a 2.2.20 és a 2.2.35 közötti WALinuxAgent-verzió (mindkettő kizárólagos) nem működik Azure Stack hub-környezetekben az 1910-es kiadás előtt. A 2.2.20/2.2.35 verziók segítségével előkészítheti a rendszerképet. Ha az egyéni rendszerkép előkészítéséhez a 2.2.35 fenti verzióit szeretné használni, frissítse a Azure Stack hubot 1903-es kiadásra, vagy alkalmazza a 1901/1902-es gyorsjavítást.
    
    [A 1910-as kiadás előtt] A kompatibilis WALinuxAgent letöltéséhez kövesse az alábbi utasításokat:
    
    1. Töltse le a setuptools.
        
        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```
    
    1. Töltse le és csomagolja ki az ügynök 2.2.20 verzióját a GitHubról.

        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```

    1. Telepítse a setup.py.

        ```bash
        sudo python setup.py install
        ```

    1. Indítsa újra a waagent.
    
        ```bash
        sudo systemctl restart waagent
        ```

    1. Ellenőrizze, hogy az ügynök verziója megfelel-e a letöltöttnek. Ebben a példában 2.2.20 kell lennie.

        ```bash
        waagent -version
        ```

    [A 1910-es kiadás után] A kompatibilis WALinuxAgent letöltéséhez kövesse az alábbi utasításokat:
    
    1. A WALinuxAgent-csomagot (`WALinuxAgent-<version>`) leküldte a Red Hat extrák tárházba. Engedélyezze az extrák tárházát a következő parancs futtatásával:

        ```bash
        subscription-manager repos --enable=rhel-7-server-extras-rpms
        ```

    1. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

        ```bash
        sudo yum install WALinuxAgent
        sudo systemctl enable waagent.service
        ```
    

1. Ne hozzon létre lapozófájlt az operációs rendszer lemezén.

    Az Azure Linux-ügynök a virtuális gép Azure-beli üzembe helyezése után automatikusan konfigurálhatja a swap-helyet a virtuális géphez csatolt helyi erőforrás-lemez használatával. A helyi erőforrás lemez egy ideiglenes lemez, és előfordulhat, hogy a virtuális gép kiépítésekor ki van ürítve. Miután telepítette az Azure Linux-ügynököt az előző lépésben, `/etc/waagent.conf` megfelelő módon módosítsa a következő paramétereket:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Ha törölni szeretné az előfizetés regisztrációját, futtassa a következő parancsot:

    ```bash
    sudo subscription-manager unregister
    ```

1. Ha vállalati hitelesítésszolgáltató használatával üzembe helyezett rendszereket használ, a RHEL virtuális gép nem bízik meg az Azure Stack hub főtanúsítványában. Ezt a megbízható legfelső szintű tárolóba kell helyeznie. További információ: [megbízható legfelső szintű tanúsítványok hozzáadása a kiszolgálóhoz](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Válassza a **művelet** > **Leállítás** a Hyper-V kezelőjében lehetőséget.

1. Alakítsa át a virtuális merevlemezt rögzített méretű virtuális merevlemezre a Hyper-V kezelője "lemez szerkesztése" funkció vagy a convert-VHD PowerShell-parancs használatával. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Red Hat-alapú virtuális gép előkészítése KVM-ből

1. Töltse le a RHEL 7 KVM-rendszerképét a Red Hat webhelyről. Ez az eljárás a RHEL 7 módszert használja példaként.

1. Adja meg a gyökér jelszavát.

    Titkosított jelszó létrehozása, és a parancs kimenetének másolása:

    ```bash
    openssl passwd -1 changeme
    ```

   Adja meg a root jelszót a guestfish:

    ```sh
    guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   A root felhasználó második mezőjének módosítása a következőről: "!!" a titkosított jelszóhoz.

1. Hozzon létre egy virtuális gépet a qcow2-rendszerképből a KVM-ben. Állítsa be a lemez típusát a **qcow2**értékre, és állítsa a virtuális hálózati adapter modelljét a **virtio**értékre. Ezután indítsa el a virtuális gépet, és jelentkezzen be root-ként.

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és adja hozzá a következő szöveget:

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

1. A következő parancs futtatásával győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul:

    ```bash
    sudo systemctl enable network
    ```

1. Regisztrálja a Red Hat-előfizetését, hogy engedélyezze a csomagok telepítését a RHEL adattárból a következő parancs futtatásával:

    ```bash
    subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. A konfiguráció elvégzéséhez nyissa meg `/etc/default/grub` egy szövegszerkesztőben, és módosítsa a `GRUB_CMDLINE_LINUX` paramétert. Például:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Ez a parancs azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, amely a hibakeresési problémákkal segíti az Azure-támogatást. A parancs a hálózati adapterek új RHEL 7 elnevezési konvencióit is kikapcsolja.

   A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol a rendszer az összes naplót elküldi a soros portnak. Ha szükséges, hagyja a `crashkernel` beállítást. Ez a paraméter csökkenti a virtuális gép rendelkezésre álló memóriájának mennyiségét 128 MB vagy több értékkel, ami problémát okozhat a kisebb virtuálisgép-méreteknél. Javasoljuk, hogy távolítsa el a következő paramétereket:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Miután végzett a `/etc/default/grub`szerkesztésével, futtassa a következő parancsot a grub-konfiguráció újraépítéséhez:

    ```bash
    grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Hyper-V modulok hozzáadása a initramfs-hez.

    `/etc/dracut.conf` szerkesztése és tartalom hozzáadása:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Initramfs újraépítése:

    ```bash
    dracut -f -v
    ```

1. [Opcionális a 1910-es kiadás után] Cloud-init leállítása és eltávolítása:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van és konfigurálva van a rendszerindítás indítására:

    ```bash
    systemctl enable sshd
    ```

    Módosítsa a/etc/ssh/sshd_config a következő sorok belefoglalásával:

    ```sh
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. Azure Stack hub egyéni virtuális merevlemezének létrehozásakor vegye figyelembe, hogy a 2.2.20 és a 2.2.35 közötti WALinuxAgent-verzió (mindkettő kizárólagos) nem működik Azure Stack hub-környezetekben az 1910-es kiadás előtt. A 2.2.20/2.2.35 verziók segítségével előkészítheti a rendszerképet. Ha az egyéni rendszerkép előkészítéséhez a 2.2.35 fenti verzióit szeretné használni, frissítse a Azure Stack hubot 1903-es kiadásra, vagy alkalmazza a 1901/1902-es gyorsjavítást.

    [A 1910-as kiadás előtt] A kompatibilis WALinuxAgent letöltéséhez kövesse az alábbi utasításokat:

    1. Töltse le a setuptools.
        
        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```
        
    1. Töltse le és csomagolja ki az ügynök 2.2.20 verzióját a GitHubról.
        
        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```
        
    1. Telepítse a setup.py.
        
        ```bash
        sudo python setup.py install
        ```
        
    1. Indítsa újra a waagent.
        
        ```bash
        sudo systemctl restart waagent
        ```
        
    1. Ellenőrizze, hogy az ügynök verziója megfelel-e a letöltöttnek. Ebben a példában 2.2.20 kell lennie.
        
        ```bash
        waagent -version
        ```
        
    [A 1910-es kiadás után] A kompatibilis WALinuxAgent letöltéséhez kövesse az alábbi utasításokat:
    
    1. A WALinuxAgent-csomagot (`WALinuxAgent-<version>`) leküldte a Red Hat extrák tárházba. Engedélyezze az extrák tárházát a következő parancs futtatásával:

        ```bash
        subscription-manager repos --enable=rhel-7-server-extras-rpms
        ```

        1. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

            ```bash
            sudo yum install WALinuxAgent
            sudo systemctl enable waagent.service
            ```

1. Ne hozzon létre lapozófájlt az operációs rendszer lemezén.

    Az Azure Linux-ügynök a virtuális gép Azure-beli üzembe helyezése után automatikusan konfigurálhatja a swap-helyet a virtuális géphez csatolt helyi erőforrás-lemez használatával. A helyi erőforrás lemez egy ideiglenes lemez, és előfordulhat, hogy a virtuális gép kiépítésekor ki van ürítve. Miután telepítette az Azure Linux-ügynököt az előző lépésben, `/etc/waagent.conf` megfelelő módon módosítsa a következő paramétereket:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. A következő parancs futtatásával szüntesse meg az előfizetés regisztrációját (ha szükséges):

    ```bash
    subscription-manager unregister
    ```

1. Ha vállalati hitelesítésszolgáltató használatával üzembe helyezett rendszereket használ, a RHEL virtuális gép nem bízik meg az Azure Stack hub főtanúsítványában. Ezt a megbízható legfelső szintű tárolóba kell helyeznie. További információ: [megbízható legfelső szintű tanúsítványok hozzáadása a kiszolgálóhoz](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Állítsa le a virtuális gépet a KVM-ban.

1. Alakítsa át a qcow2-rendszerképet a VHD formátumba.

    > [!NOTE]
    > Létezik egy ismert hiba a qemu-IMG verzióban > = 2.2.1, amely egy nem megfelelően formázott VHD-t eredményez. A probléma a QEMU 2,6-es verziójában lett kijavítva. Javasoljuk, hogy használja a qemu-IMG 2.2.0 vagy az alacsonyabb, vagy frissítsen 2,6-es vagy újabb verzióra. Hivatkozás: https://bugs.launchpad.net/qemu/+bug/1490611.

    Először alakítsa át a képet nyers formátumba:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Győződjön meg arról, hogy a nyers rendszerkép mérete 1 MB-ra van igazítva. Ellenkező esetben az 1 MB-ra való igazításhoz kerekítse a méretet:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    A nyers lemez átalakítása rögzített méretű virtuális merevlemezre:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Vagy a (z) és a (z) a qemu **2.6 +** verziójában a `force_size` kapcsolót is tartalmazza:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-vmware"></a>Red Hat-alapú virtuális gép előkészítése VMware-ről

Ez a szakasz azt feltételezi, hogy már telepített egy RHEL virtuális gépet a VMware-ben. A VMware operációs rendszer telepítésével kapcsolatos további információkért lásd: a [VMware vendég operációs rendszer telepítési útmutatója](https://aka.ms/aa6z600).

* A Linux operációs rendszer telepítésekor azt javasoljuk, hogy az LVM helyett a standard partíciót használja, ami gyakran az alapértelmezett a sok telepítés esetén. Ezzel a módszerrel elkerülhető, hogy az LVM neve ütközik a klónozott virtuális gépekkel, különösen akkor, ha egy operációsrendszer-lemezt egy másik virtuális géphez csatlakoztatni kell a hibaelhárításhoz. Az LVM vagy RAID adatlemezeken is használható, ha az előnyben részesített.
* Ne állítson be swap partíciót az operációs rendszer lemezén. A Linux-ügynököt beállíthatja úgy, hogy lapozófájlt hozzon létre az ideiglenes erőforrás lemezén. Erről a konfigurációról a következő lépésekben talál további információt.
* A virtuális merevlemez létrehozásakor válassza a **virtuális lemez tárolása egyetlen fájlként**lehetőséget.

### <a name="prepare-an-rhel-7-vm-from-vmware"></a>RHEL 7 virtuális gép előkészítése VMware-ről

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és adja hozzá a következő szöveget:

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

1. A következő parancs futtatásával győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul:

    ```bash
    sudo chkconfig network on
    ```

1. Regisztrálja a Red Hat-előfizetését, hogy lehetővé tegye a csomagok telepítését a RHEL adattárból a következő parancs futtatásával:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. A módosítás elvégzéséhez nyissa meg `/etc/default/grub` egy szövegszerkesztőben, és módosítsa a `GRUB_CMDLINE_LINUX` paramétert. Például:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

    Ez a konfiguráció azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, amely a hibakeresési problémákkal segíti az Azure-támogatást. Emellett kikapcsolja a hálózati adapterek új RHEL 7 elnevezési konvencióit is. Javasoljuk, hogy távolítsa el a következő paramétereket:

    ```sh
    rhgb quiet crashkernel=auto
    ```

    A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra. Ha szükséges, hagyja a `crashkernel` beállítást. Ez a paraméter csökkenti a virtuális gép rendelkezésre álló memóriájának mennyiségét 128 MB vagy több értékkel, ami problémát okozhat a kisebb virtuálisgép-méreteknél.

1. Miután végzett a `/etc/default/grub`szerkesztésével, futtassa a következő parancsot a grub-konfiguráció újraépítéséhez:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Hyper-V modulok hozzáadása a initramfs-hez.

    `/etc/dracut.conf`szerkesztése, tartalom hozzáadása:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Initramfs újraépítése:

    ```bash
    dracut -f -v
    ```

1. [Opcionális a 1910-es kiadás után] Cloud-init leállítása és eltávolítása:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik. Ez a beállítás általában az alapértelmezett. Módosítsa `/etc/ssh/sshd_config` a következő sor belefoglalásával:

    ```sh
    ClientAliveInterval 180
    ```

1. Azure Stack hub egyéni virtuális merevlemezének létrehozásakor vegye figyelembe, hogy a 2.2.20 és a 2.2.35 közötti WALinuxAgent-verzió (mindkettő kizárólagos) nem működik Azure Stack hub-környezetekben az 1910-es kiadás előtt. A 2.2.20/2.2.35 verziók segítségével előkészítheti a rendszerképet. Ha az egyéni rendszerkép előkészítéséhez a 2.2.35 fenti verzióit szeretné használni, frissítse a Azure Stack hubot 1903-es kiadásra, vagy alkalmazza a 1901/1902-es gyorsjavítást.

    [A 1910-as kiadás előtt] A kompatibilis WALinuxAgent letöltéséhez kövesse az alábbi utasításokat:

    1. Töltse le a setuptools.
    
        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```
        
    1. Töltse le és csomagolja ki az ügynök 2.2.20 verzióját a GitHubról.
        
        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```
        
    1. Telepítse a setup.py.
        
        ```bash
        sudo python setup.py install
        ```
        
    1. Indítsa újra a waagent.
        
        ```bash
        sudo systemctl restart waagent
        ```
        
    1. Ellenőrizze, hogy az ügynök verziója megfelel-e a letöltöttnek. Ebben a példában 2.2.20 kell lennie.
        
        ```bash
        waagent -version
        ```
        
    [A 1910-es kiadás után] A kompatibilis WALinuxAgent letöltéséhez kövesse az alábbi utasításokat:
    
    1. A WALinuxAgent-csomagot (`WALinuxAgent-<version>`) leküldte a Red Hat extrák tárházba. Engedélyezze az extrák tárházát a következő parancs futtatásával:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

    1. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:
        
        ```bash
        sudo yum install WALinuxAgent
        sudo systemctl enable waagent.service
        ```
        
1. Ne hozzon létre lapozófájlt az operációs rendszer lemezén.

    Az Azure Linux-ügynök a virtuális gép Azure-beli üzembe helyezése után automatikusan konfigurálhatja a swap-helyet a virtuális géphez csatolt helyi erőforrás-lemez használatával. Vegye figyelembe, hogy a helyi erőforrás lemeze egy ideiglenes lemez, és a virtuális gép kiépítésekor kiüríthető. Miután telepítette az Azure Linux-ügynököt az előző lépésben, `/etc/waagent.conf` megfelelő módon módosítsa a következő paramétereket:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    NOTE: set this to whatever you need it to be.
    ```

1. Ha törölni szeretné az előfizetés regisztrációját, futtassa a következő parancsot:

    ```bash
    sudo subscription-manager unregister
    ```

1. Ha vállalati hitelesítésszolgáltató használatával üzembe helyezett rendszereket használ, a RHEL virtuális gép nem bízik meg az Azure Stack hub főtanúsítványában. Ezt a megbízható legfelső szintű tárolóba kell helyeznie. További információ: [megbízható legfelső szintű tanúsítványok hozzáadása a kiszolgálóhoz](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Állítsa le a virtuális gépet, és alakítsa át a VMDK-fájlt a VHD formátumba.

    > [!NOTE]
    > Létezik egy ismert hiba a qemu-IMG verzióban > = 2.2.1, amely egy nem megfelelően formázott VHD-t eredményez. A probléma a QEMU 2,6-es verziójában lett kijavítva. Javasoljuk, hogy használja a qemu-IMG 2.2.0 vagy az alacsonyabb, vagy frissítsen 2,6-es vagy újabb verzióra. Hivatkozás: <https://bugs.launchpad.net/qemu/+bug/1490611>.

    Először alakítsa át a képet nyers formátumba:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Győződjön meg arról, hogy a nyers rendszerkép mérete 1 MB-ra van igazítva. Ellenkező esetben az 1 MB-ra való igazításhoz kerekítse a méretet:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    A nyers lemez átalakítása rögzített méretű virtuális merevlemezre:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Vagy a (z) és a (z) a qemu **2.6 +** verziójában a `force_size` kapcsolót is tartalmazza:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-an-iso-by-using-a-kickstart-file-automatically"></a>Red Hat-alapú virtuális gép előkészítése ISO-fájlból egy Kickstart-fájl automatikus használatával

1. Hozzon létre egy olyan Kickstart-fájlt, amely tartalmazza a következő tartalmat, és mentse a fájlt. A Cloud-init leállítása és eltávolítása nem kötelező (a Cloud-init használata a Azure Stack hub 1910-es kiadásának kiadása után támogatott). Az ügynököt csak a 1910-es kiadás után telepítse a RedHat-tárházból. 1910 előtt az Azure-tárházat az előző szakaszban leírtak szerint használhatja. A Kickstart telepítésével kapcsolatos részletekért tekintse meg a [Kickstart telepítési útmutatóját](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

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

1. Helyezze el azt a Kickstart-fájlt, ahová a telepítési rendszer hozzá tud férni.

1. A Hyper-V kezelőjében hozzon létre egy új virtuális gépet. A **virtuális merevlemez csatlakoztatása** lapon válassza a **virtuális merevlemez csatolása később**lehetőséget, és fejezze be az új virtuális gép varázslót.

1. Nyissa meg a virtuális gép beállításait:

    a. Csatlakoztassa a virtuális GÉPET egy új virtuális merevlemezhez. Győződjön meg arról, hogy a **VHD-formátum** és a **rögzített méret**van kiválasztva.

    b. Csatlakoztassa a telepítési ISO-t a DVD-meghajtóhoz.

    c. Állítsa be a BIOS-t a CD-ről történő rendszerindításhoz.

1. Indítsa el a virtuális gépet. Amikor megjelenik a telepítési útmutató, nyomja le a **Tab** billentyűt a rendszerindítási beállítások konfigurálásához.

1. Adja meg `inst.ks=<the location of the kickstart file>` a rendszerindítási beállítások végén, majd nyomja le az **ENTER**billentyűt.

1. Várjon, amíg a telepítés befejeződik. Ha elkészült, a rendszer automatikusan leállítja a virtuális gépet. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="known-issues"></a>Ismert problémák

### <a name="the-hyper-v-driver-couldnt-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>A Hyper-V-illesztőprogramot nem lehetett a kezdeti RAM-lemezbe foglalni, ha nem Hyper-V Hypervisort használ

Bizonyos esetekben előfordulhat, hogy a Linux-telepítők nem tartalmazzák a Hyper-V illesztőprogramjait a kezdeti RAM-lemezen (initrd vagy initramfs), kivéve, ha a Linux azt észleli, hogy egy Hyper-V környezetben fut.

Ha más virtualizációs rendszert használ (például az Oracle VM VirtualBox, a Xen Project stb.) a Linux-rendszerkép előkészítéséhez, szükség lehet a initrd újraépítésére, hogy legalább a hv_vmbus és hv_storvsc kernel-modulok elérhetők legyenek a kezdeti RAM-ban. lemez. Ez egy ismert probléma, amely legalább a felsőbb rétegbeli Red Hat-disztribúción alapuló rendszereken jelent meg.

A probléma megoldásához vegyen fel Hyper-V modulokat a initramfs és az újraépítéshez:

Szerkessze `/etc/dracut.conf`, és adja hozzá a következő tartalmat:

```sh
add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
```

Initramfs újraépítése:

```bash
dracut -f -v
```

További információ: a [initramfs újraépítése](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Következő lépések

Most már készen áll arra, hogy a Red Hat Enterprise Linux virtuális merevlemezt új virtuális gépeket hozzon létre Azure Stack hub-ban. Ha első alkalommal tölti fel a VHD-fájlt Azure Stack hubhoz, tekintse meg a Marketplace- [elem létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)című témakört.

További információ a Red Hat Enterprise Linux futtatására hitelesített hypervisorokról: [Red Hat webhely](https://access.redhat.com/certified-hypervisors).
