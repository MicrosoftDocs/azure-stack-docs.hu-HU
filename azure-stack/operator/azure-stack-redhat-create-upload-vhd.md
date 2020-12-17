---
title: Red Hat-alapú virtuális gép előkészítése az Azure Stack Hub-beli használatra
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan hozhat létre és tölthet fel egy Red Hat Linux operációs rendszert tartalmazó Azure-beli virtuális merevlemezt (VHD-t).
author: sethmanheim
ms.topic: article
ms.date: 12/15/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 12/11/2019
ms.openlocfilehash: f0a408cdbd7b1c76a0a24f03537f20d1ae7b851e
ms.sourcegitcommit: f30e5178e0b4be4e3886f4e9f699a2b51286e2a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97620602"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack-hub"></a>Red Hat-alapú virtuális gép előkészítése az Azure Stack Hub-beli használatra

Ez a cikk azt ismerteti, hogyan lehet előkészíteni egy Red Hat Enterprise Linux (RHEL) virtuális gépet (VM) Azure Stack hub-ban való használatra. A cikkben tárgyalt RHEL-verziók 7,1-es vagy újabb verziójúak. A cikkben tárgyalt, a Hyper-V, a kernel-alapú virtuális gép (KVM) és a VMware.

Red Hat Enterprise Linux támogatási információkat a [Red Hat és a Azure stack: gyakori kérdések](https://access.redhat.com/articles/3413531)című témakörben talál.

## <a name="prepare-a-red-hat-based-vm-from-hyper-v-manager"></a>Red Hat-alapú virtuális gép előkészítése a Hyper-V kezelőjéből

Ez a szakasz azt feltételezi, hogy már rendelkezik egy ISO-fájllal a Red Hat webhelyén, és telepítette a RHEL-lemezképet egy virtuális merevlemezre (VHD). További információ arról, hogyan használható a Hyper-V kezelője operációsrendszer-rendszerkép telepítéséhez: [a Hyper-v szerepkör telepítése és a virtuális gép konfigurálása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

### <a name="rhel-installation-notes"></a>RHEL-telepítési megjegyzések

* Azure Stack hub nem támogatja a VHDX formátumot. Az Azure csak a rögzített VHD-t támogatja. A Hyper-V kezelőjével átalakíthatja a lemezt VHD formátumba, vagy használhatja a **Convert-VHD** parancsmagot is. Ha VirtualBox-t használ, a lemez létrehozásakor válassza a **rögzített méret** lehetőséget a dinamikusan lefoglalt alapértelmezett beállítással szemben.
* Azure Stack hub csak az 1. generációs virtuális gépeket támogatja. Az 1. generációs virtuális gépeket átalakíthatja a VHDX-ből a VHD-fájlformátumba, és dinamikusan bővül a rögzített méretű lemezre. A virtuális gép generációja nem módosítható. További információ: [hozzon létre egy 1. vagy 2. generációs virtuális gépet a Hyper-V-ben?](/windows-server/virtualization/hyper-v/plan/Should-I-create-a-generation-1-or-2-virtual-machine-in-Hyper-V).
* A VHD számára engedélyezett maximális méret 1 023 GB.
* A Linux operációs rendszer telepítésekor azt javasoljuk, hogy a logikai kötet-kezelő (LVM) helyett használjon standard partíciókat, ami gyakran az alapértelmezett a sok telepítésnél. Ezzel a gyakorlattal elkerülhető, hogy az LVM neve ütközik a klónozott virtuális gépekkel, különösen akkor, ha az operációs rendszer lemezét egy másik, azonos virtuális géphez kell csatlakoztatnia a hibaelhárításhoz.
* Az univerzális lemezes formázású (UDF-) fájlrendszerek kernel-támogatása szükséges. Az első rendszerindítás során a vendéghez csatolt UDF formátumú adathordozó továbbítja a kiépítési konfigurációt a Linux rendszerű virtuális gépre. Az Azure Linux-ügynöknek csatlakoztatnia kell az UDF fájlrendszert a konfigurációjának olvasásához és a virtuális gép kiépítéséhez.
* Ne állítson be swap partíciót az operációs rendszer lemezén. A Linux-ügynök úgy konfigurálható, hogy lapozófájlt hozzon létre az ideiglenes erőforrás lemezén. További információt a következő lépésekben talál.
* Minden Azure-beli virtuális merevlemeznek 1 MB-ra igazított virtuális mérettel kell rendelkeznie. Nyers lemezről VHD-re való konvertáláskor győződjön meg arról, hogy a nyers lemez mérete 1 MB-nál több, az átalakítás előtt. További részletek a következő lépésekben találhatók.
* Azure Stack hub támogatja a Cloud-init-t. A [cloud-init](/azure/virtual-machines/linux/using-cloud-init) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a Cloud-init a kezdeti rendszerindítási folyamat során hívásra kerül, nincs szükség további lépésekre vagy ügynökökre a konfiguráció alkalmazásához. A Cloud-init rendszerképhez való hozzáadásával kapcsolatos útmutatásért lásd: [meglévő Linux Azure-beli virtuálisgép-rendszerkép előkészítése a Cloud-init használatával](/azure/virtual-machines/linux/cloudinit-prepare-custom-image).

### <a name="prepare-an-rhel-7-vm-from-hyper-v-manager"></a>RHEL 7 virtuális gép előkészítése a Hyper-V kezelőjéből

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.

2. Válassza a **Kapcsolódás** lehetőséget a konzol ablakának megnyitásához a virtuális géphez.

3. Hozza létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:

    ```shell
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Hozza létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és szükség szerint adja hozzá a következő szöveget:

    ```shell
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. A következő parancs futtatásával győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul:

    ```bash
    sudo systemctl enable network
    ```

6. Regisztrálja a Red Hat-előfizetését, hogy lehetővé tegye a csomagok telepítését a RHEL adattárból a következő parancs futtatásával:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

7. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. A módosítás elvégzéséhez nyisson meg egy szövegszerkesztőben `/etc/default/grub` , és módosítsa a `GRUB_CMDLINE_LINUX` paramétert. Például:

    ```shell
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Ez a módosítás biztosítja az összes konzolos üzenet küldését az első soros portra, amely a hibakeresési problémákkal segíti az Azure-támogatást. Ez a konfiguráció a hálózati adapterek új RHEL 7 elnevezési konvencióit is kikapcsolja.

   A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra. Ha szükséges, hagyja a `crashkernel` beállítást. Ez a paraméter csökkenti a virtuális gép rendelkezésre álló memóriájának mennyiségét 128 MB vagy több értékkel, ami problémát okozhat a kisebb virtuálisgép-méreteknél. Javasoljuk, hogy távolítsa el a következő paramétereket:

    ```shell
    rhgb quiet crashkernel=auto
    ```

8. Miután végzett a szerkesztéssel `/etc/default/grub` , futtassa a következő parancsot a grub-konfiguráció újraépítéséhez:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

9. Nem kötelező: leállítás és eltávolítás `cloud-init` :

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

10. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik, ami általában az alapértelmezett. Módosítsa `/etc/ssh/sshd_config` a következő sor befoglalásával:

    ```shell
    ClientAliveInterval 180
    ```

11. Ha Azure Stack hubhoz hoz létre egyéni virtuális merevlemezt, vegye figyelembe, hogy a 2.2.20 és a 2.2.35 közötti **WALinuxAgent** -verziók (mindkettő kizárólagos) nem működnek Azure stack hub-környezetekben az 1910-es kiadás előtt. A rendszerképek előkészítéséhez használhatja a 2.2.20/2.2.35 verziót. Ha a 2.2.35-nál későbbi verziókban szeretné használni az egyéni rendszerkép előkészítését, frissítse az Azure Stack hub-t a 1903-es vagy újabb kiadásra, vagy alkalmazza a 1901/1902-es gyorsjavítást.

    1910-os kiadás előtt: kövesse az alábbi utasításokat egy kompatibilis **WALinuxAgent** letöltéséhez:

    1. Töltse le a telepítő eszközöket:

        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```

    2. Töltse le és csomagolja ki az ügynök 2.2.20 verzióját a GitHubról:

        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```

    3. A **Setup.py** telepítése:

        ```bash
        sudo python setup.py install
        ```

    4. **Waagent** újraindítása:

        ```bash
        sudo systemctl restart waagent
        ```

    5. Ellenőrizze, hogy az ügynök verziója megfelel-e a letöltöttnek. Ebben a példában 2.2.20 kell lennie:

        ```bash
        waagent -version
        ```

    Az 1910-es kiadás után kövesse az alábbi utasításokat egy kompatibilis **WALinuxAgent** letöltéséhez:

    1. A **WALinuxAgent** -csomagot `WALinuxAgent-<version>` leküldte a Red Hat extrák tárházba. Engedélyezze az extrák tárházát a következő parancs futtatásával:

        ```bash
        subscription-manager repos --enable=rhel-7-server-extras-rpms
        ```

    2. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

        ```bash
        sudo yum install WALinuxAgent
        sudo systemctl enable waagent.service
        ```

12. Ne hozzon létre lapozófájlt az operációs rendszer lemezén.

    Az Azure Linux-ügynök a virtuális gép Azure-beli üzembe helyezése után automatikusan konfigurálhatja a swap-helyet a virtuális géphez csatolt helyi erőforrás-lemez használatával. A helyi erőforrás lemez egy ideiglenes lemez, és előfordulhat, hogy a virtuális gép kiépítés után ki van ürítve. Miután telepítette az Azure Linux-ügynököt az előző lépésben, megfelelően módosítsa a következő paramétereket `/etc/waagent.conf` :

    ```shell
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

13. Ha nem szeretné regisztrálni az előfizetést, futtassa a következő parancsot:

    ```bash
    sudo subscription-manager unregister
    ```

14. Ha vállalati hitelesítésszolgáltató használatával üzembe helyezett rendszereket használ, a RHEL virtuális gép nem bízik meg az Azure Stack hub főtanúsítványában. Ezt a tanúsítványt a megbízható legfelső szintű tárolóba kell helyeznie. További információ: [megbízható legfelső szintű tanúsítványok hozzáadása a kiszolgálóhoz](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

15. Futtassa az alábbi parancsokat a virtuális gép kiépítéséhez és az Azure-beli üzembe helyezéshez való előkészítéséhez:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

16. Válassza a **művelet** lehetőséget, majd **állítsa le** a Hyper-V kezelőjében.

17. Alakítsa át a virtuális merevlemezt rögzített méretű VHD-re a Hyper-V kezelő **lemez szerkesztése** szolgáltatás vagy a `Convert-VHD` PowerShell-parancs használatával. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Red Hat-alapú virtuális gép előkészítése KVM-ből

1. Töltse le a RHEL 7 KVM-rendszerképét a Red Hat webhelyről. Ez az eljárás a RHEL 7 módszert használja példaként.

2. Adja meg a gyökér jelszavát.

    Titkosított jelszó létrehozása, és a parancs kimenetének másolása:

    ```bash
    openssl passwd -1 changeme
    ```

   Adja meg a root jelszót a guestfish:

    ```shell
    guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   A root felhasználó második mezőjének módosítása a következőről: "!!" a titkosított jelszóhoz.

3. Hozzon létre egy virtuális gépet a **qcow2** -rendszerképből a KVM-ben. Állítsa be a lemez típusát a **qcow2** értékre, és állítsa a virtuális hálózati adapter modelljét a **virtio** értékre. Ezután indítsa el a virtuális gépet, és jelentkezzen be root-ként.

4. Hozza létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:

    ```shell
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Hozza létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, majd adja hozzá a következő szöveget:

    ```shell
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

6. A következő parancs futtatásával győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul:

    ```bash
    sudo systemctl enable network
    ```

7. Regisztrálja a Red Hat-előfizetését, hogy engedélyezze a csomagok telepítését a RHEL adattárból a következő parancs futtatásával:

    ```bash
    subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

8. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. Ehhez a konfigurációhoz nyisson meg `/etc/default/grub` egy szövegszerkesztőben, és módosítsa a `GRUB_CMDLINE_LINUX` paramétert. Például:

    ```shell
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Ez a parancs azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, amely a hibakeresési problémákkal segíti az Azure-támogatást. A parancs a hálózati adapterek új RHEL 7 elnevezési konvencióit is kikapcsolja.

   A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol a rendszer az összes naplót elküldi a soros portnak. Ha szükséges, hagyja a `crashkernel` beállítást. Ez a paraméter csökkenti a virtuális gép rendelkezésre álló memóriájának mennyiségét 128 MB vagy több értékkel, ami problémát okozhat a kisebb virtuálisgép-méreteknél. Javasoljuk, hogy távolítsa el a következő paramétereket:

    ```shell
    rhgb quiet crashkernel=auto
    ```

9. Miután végzett a szerkesztéssel `/etc/default/grub` , futtassa a következő parancsot a grub-konfiguráció újraépítéséhez:

    ```bash
    grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Hyper-V modulok hozzáadása a **initramfs**-hez.

    `/etc/dracut.conf`Tartalom szerkesztése és hozzáadása:

    ```shell
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Initramfs újraépítése:

    ```bash
    dracut -f -v
    ```

11. Opcionális az 1910-es kiadás után: leállítás és eltávolítás `cloud-init` :

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

12. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van és konfigurálva van a rendszerindítás indítására:

    ```bash
    systemctl enable sshd
    ```

    Módosítsa a/etc/ssh/sshd_config a következő sorok belefoglalásával:

    ```shell
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

13. Ha Azure Stack hubhoz hoz létre egyéni virtuális merevlemezt, vegye figyelembe, hogy a 2.2.20 és a 2.2.35 közötti **WALinuxAgent** -verziók (mindkettő kizárólagos) nem működnek Azure stack hub-környezetekben az 1910-es kiadás előtt. A rendszerképek előkészítéséhez használhatja a 2.2.20/2.2.35 verziót. Ha a 2.2.35-nél újabb verziót szeretne használni az egyéni rendszerkép előkészítéséhez, frissítse az Azure Stack hubot 1903-es vagy újabb verzióra, vagy alkalmazza a 1901/1902-es gyorsjavítást.

    Az 1910-es kiadás előtt: kövesse az alábbi utasításokat egy kompatibilis **WALinuxAgent** letöltéséhez:

    1. Töltse le a telepítő eszközöket:

        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```

    2. Töltse le és csomagolja ki az ügynök 2.2.20 verzióját a GitHubról:

        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```

    3. A **Setup.py** telepítése:

        ```bash
        sudo python setup.py install
        ```

    4. Indítsa újra a **waagent**.

        ```bash
        sudo systemctl restart waagent
        ```

    5. Ellenőrizze, hogy az ügynök verziója megfelel-e a letöltöttnek. Ebben a példában 2.2.20 kell lennie:

        ```bash
        waagent -version
        ```

    Az 1910-es kiadás után: kövesse az alábbi utasításokat egy kompatibilis **WALinuxAgent** letöltéséhez:

    1. A **WALinuxAgent** -csomagot `WALinuxAgent-<version>` leküldte a Red Hat extrák tárházba. Engedélyezze az extrák tárházát a következő parancs futtatásával:

        ```bash
        subscription-manager repos --enable=rhel-7-server-extras-rpms
        ```

    2. Telepítse az Azure Linux-ügynököt az alábbi parancsok futtatásával:

        ```bash
        sudo yum install WALinuxAgent
        sudo systemctl enable waagent.service
        ```

14. Ne hozzon létre lapozófájlt az operációs rendszer lemezén.

    Az Azure Linux-ügynök a virtuális gép Azure-beli üzembe helyezése után automatikusan konfigurálhatja a swap-helyet a virtuális géphez csatolt helyi erőforrás-lemez használatával. A helyi erőforrás lemez egy ideiglenes lemez, és előfordulhat, hogy a virtuális gép kiépítés után ki van ürítve. Miután telepítette az Azure Linux-ügynököt az előző lépésben, megfelelően módosítsa a következő paramétereket `/etc/waagent.conf` :

    ```shell
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

15. A következő parancs futtatásával szüntesse meg az előfizetés regisztrációját (ha szükséges):

    ```bash
    subscription-manager unregister
    ```

16. Ha vállalati hitelesítésszolgáltató használatával üzembe helyezett rendszereket használ, a RHEL virtuális gép nem bízik meg az Azure Stack hub főtanúsítványában. Ezt a tanúsítványt a megbízható legfelső szintű tárolóba kell helyeznie. További információ: [megbízható legfelső szintű tanúsítványok hozzáadása a kiszolgálóhoz](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

17. Futtassa az alábbi parancsokat a virtuális gép kiépítéséhez és az Azure-beli üzembe helyezéshez való előkészítéséhez:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

18. Állítsa le a virtuális gépet a KVM-ban.

19. Alakítsa át a qcow2-rendszerképet a VHD formátumba.

    > [!NOTE]
    > Létezik egy ismert hiba a **QEMU-IMG** verzióban >= 2.2.1, amely egy nem megfelelően formázott VHD-t eredményez. A probléma a QEMU 2,6-es verziójában lett kijavítva. Javasoljuk, hogy használja a qemu-IMG 2.2.0 vagy az alacsonyabb, vagy frissítsen 2,6 vagy újabb verzióra. További információkért [tekintse meg ezt a qemu-bejegyzést](https://bugs.launchpad.net/qemu/+bug/1490611).

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

    A (z) és a (z) és a (z) a qemu **2.6**-os verziójával együtt `force_size`

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-vmware"></a>Red Hat-alapú virtuális gép előkészítése VMware-ről

Ez a szakasz azt feltételezi, hogy már telepített egy RHEL virtuális gépet a VMware-ben. A VMware operációs rendszer telepítésével kapcsolatos további információkért lásd: a [VMware vendég operációs rendszer telepítési útmutatója](https://aka.ms/aa6z600).

* A Linux operációs rendszer telepítésekor azt javasoljuk, hogy az LVM helyett a standard partíciót használja, ami gyakran az alapértelmezett a sok telepítés esetén. Ezzel a módszerrel elkerülhető, hogy az LVM neve ütközik a klónozott virtuális gépekkel, különösen akkor, ha egy operációsrendszer-lemezt egy másik virtuális géphez csatlakoztatni kell a hibaelhárításhoz. Az LVM vagy RAID adatlemezeken is használható, ha az előnyben részesített.
* Ne állítson be swap partíciót az operációs rendszer lemezén. A Linux-ügynököt beállíthatja úgy, hogy lapozófájlt hozzon létre az ideiglenes erőforrás lemezén. Erről a konfigurációról a következő lépésekben talál további információt.
* A virtuális merevlemez létrehozásakor válassza a **virtuális lemez tárolása egyetlen fájlként** lehetőséget.

### <a name="prepare-an-rhel-7-vm-from-vmware"></a>RHEL 7 virtuális gép előkészítése VMware-ről

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:

    ```shell
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

2. Hozza létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és adja hozzá a következő szöveget:

    ```shell
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

3. A következő parancs futtatásával győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul:

    ```bash
    sudo chkconfig network on
    ```

4. Regisztrálja a Red Hat-előfizetését, hogy lehetővé tegye a csomagok telepítését a RHEL adattárból a következő parancs futtatásával:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

5. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. A módosítás elvégzéséhez nyisson meg `/etc/default/grub` egy szövegszerkesztőben. Módosítsa a `GRUB_CMDLINE_LINUX` paramétert. Például:

    ```shell
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

    Ez a konfiguráció azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, amely a hibakeresési problémákkal segíti az Azure-támogatást. Emellett kikapcsolja a hálózati adapterek új RHEL 7 elnevezési konvencióit is. Javasoljuk, hogy távolítsa el a következő paramétereket:

    ```shell
    rhgb quiet crashkernel=auto
    ```

    A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra. Ha szükséges, hagyja a `crashkernel` beállítást. Ez a paraméter csökkenti a virtuális gép rendelkezésre álló memóriájának mennyiségét 128 MB vagy több értékkel, ami problémát okozhat a kisebb virtuálisgép-méreteknél.

6. Miután végzett a szerkesztéssel `/etc/default/grub` , futtassa a következő parancsot a grub-konfiguráció újraépítéséhez:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

7. Hyper-V modulok hozzáadása a initramfs-hez.

    Szerkesztés `/etc/dracut.conf` , tartalom hozzáadása:

    ```shell
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Initramfs újraépítése:

    ```bash
    dracut -f -v
    ```

8. Opcionális az 1910-es kiadás után: leállítás és eltávolítás `cloud-init` :

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

9. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik. Ez a beállítás általában az alapértelmezett. Módosítsa `/etc/ssh/sshd_config` a következő sor befoglalásával:

    ```shell
    ClientAliveInterval 180
    ```

10. Ha Azure Stack hubhoz hoz létre egyéni virtuális merevlemezt, vegye figyelembe, hogy a 2.2.20 és a 2.2.35 közötti WALinuxAgent-verziók (mindkettő kizárólagos) nem működnek Azure Stack hub-környezetekben az 1910-es kiadás előtt. A rendszerképek előkészítéséhez használhatja a 2.2.20/2.2.35 verziót. Ha a 2.2.35-nál későbbi verziókban szeretné használni az egyéni rendszerkép előkészítését, frissítse az Azure Stack hub-t a 1903-es vagy újabb kiadásra, vagy alkalmazza a 1901/1902-es gyorsjavítást.

    1910-os kiadás előtt: kövesse az alábbi utasításokat egy kompatibilis **WALinuxAgent** letöltéséhez:

    1. Töltse le a telepítő eszközöket:

        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```

    2. Töltse le és csomagolja ki az ügynök 2.2.20 verzióját a GitHubról:

        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```

    3. A **Setup.py** telepítése:

        ```bash
        sudo python setup.py install
        ```

    4. **Waagent** újraindítása:

        ```bash
        sudo systemctl restart waagent
        ```

    5. Ellenőrizze, hogy az ügynök verziója megfelel-e a letöltöttnek. Ebben a példában 2.2.20 kell lennie:

        ```bash
        waagent -version
        ```

    Az 1910-es kiadás után: kövesse az alábbi utasításokat egy kompatibilis **WALinuxAgent** letöltéséhez:

    1. A **WALinuxAgent** -csomagot `WALinuxAgent-<version>` leküldte a Red Hat extrák tárházba. Engedélyezze az extrák tárházát a következő parancs futtatásával:

        ```bash
        subscription-manager repos --enable=rhel-7-server-extras-rpms
        ```

    2. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

        ```bash
        sudo yum install WALinuxAgent
        sudo systemctl enable waagent.service
        ```

11. Ne hozzon létre lapozófájlt az operációs rendszer lemezén.

    Az Azure Linux-ügynök automatikusan konfigurálhatja a lapozófájlt a virtuális géphez csatlakoztatott helyi erőforrás lemez használatával, miután a virtuális gép üzembe lett helyezve az Azure-ban. Vegye figyelembe, hogy a helyi erőforrás lemeze ideiglenes lemez, és a virtuális gép kiépítés után kiüríthető. Miután telepítette az Azure Linux-ügynököt az előző lépésben, megfelelően módosítsa a következő paramétereket `/etc/waagent.conf` :

    ```shell
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    NOTE: set this to whatever you need it to be.
    ```

12. Ha nem szeretné regisztrálni az előfizetést, futtassa a következő parancsot:

    ```bash
    sudo subscription-manager unregister
    ```

13. Ha vállalati hitelesítésszolgáltató használatával üzembe helyezett rendszereket használ, a RHEL virtuális gép nem bízik meg az Azure Stack hub főtanúsítványában. Ezt a megbízható legfelső szintű tárolóba kell helyeznie. További információ: [megbízható legfelső szintű tanúsítványok hozzáadása a kiszolgálóhoz](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

14. Futtassa az alábbi parancsokat a virtuális gép kiépítéséhez és az Azure-beli üzembe helyezéshez való előkészítéséhez:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

15. Állítsa le a virtuális gépet, és alakítsa át a VMDK-fájlt a VHD formátumba.

    > [!NOTE]
    > A `qemu-img` >= 2.2.1 verzióban egy ismert hiba található, amely nem megfelelően FORMÁZOTT VHD-t eredményez. A probléma a QEMU 2,6-es verziójában lett kijavítva. Javasoljuk, hogy használja a qemu-IMG 2.2.0 vagy az alacsonyabb, vagy frissítsen 2,6 vagy újabb verzióra.

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

    A (z) és a (z) és a (z) a qemu **2.6**-os verziójával együtt `force_size`

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-an-iso-by-using-a-kickstart-file-automatically"></a>Red Hat-alapú virtuális gép előkészítése ISO-fájlból egy Kickstart-fájl automatikus használatával

1. Hozzon létre egy olyan Kickstart-fájlt, amely tartalmazza a következő tartalmat, és mentse a fájlt. A **Cloud-init** leállítása és eltávolítása nem kötelező (a **Cloud-init** a 1910-es kiadás után Azure stack hub esetében támogatott.) Az ügynököt csak a 1910-es kiadás után telepítse a RedHat-tárházból. 1910 előtt az Azure-tárházat az előző szakaszban leírtak szerint használhatja. A Kickstart telepítésével kapcsolatos részletekért tekintse meg a [Kickstart telepítési útmutatóját](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

    ```shell
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

2. Helyezze a Kickstart-fájlt egy olyan helyre, ahonnan a telepítési rendszer hozzá tud férni.

3. A Hyper-V kezelőjében hozzon létre egy új virtuális gépet. A **virtuális merevlemez csatlakoztatása** lapon válassza a **virtuális merevlemez csatolása később** lehetőséget, és fejezze be az új virtuális gép varázslót.

4. Nyissa meg a virtuális gép beállításait:

    a. Csatlakoztassa a virtuális GÉPET egy új virtuális merevlemezhez. Győződjön meg arról, hogy a **VHD-formátum** és a **rögzített méret** van kiválasztva.

    b. Csatlakoztassa a telepítési ISO-t a DVD-meghajtóhoz.

    c. Állítsa be a BIOS-t a CD-ről történő rendszerindításhoz.

5. Indítsa el a virtuális gépet. Amikor megjelenik a telepítési útmutató, nyomja le a **Tab** billentyűt a rendszerindítási beállítások konfigurálásához.

6. Adja meg a `inst.ks=<the location of the kickstart file>` rendszerindítási beállítások végét, majd nyomja le az **ENTER** billentyűt.

7. Várjon, amíg a telepítés befejeződik. Ha elkészült, a rendszer automatikusan leállítja a virtuális gépet. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="known-issues"></a>Ismert problémák

### <a name="the-hyper-v-driver-couldnt-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>A Hyper-V-illesztőprogramot nem lehetett a kezdeti RAM-lemezbe foglalni, ha nem Hyper-V Hypervisort használ

Bizonyos esetekben előfordulhat, hogy a Linux-telepítők nem tartalmazzák a Hyper-V illesztőprogramjait a kezdeti RAM-lemezen (initrd vagy initramfs), kivéve, ha a Linux azt észleli, hogy egy Hyper-V környezetben fut.

Ha más virtualizációs rendszert használ (például az Oracle VM VirtualBox, a Xen Project stb.) a linuxos lemezkép előkészítéséhez, szükség lehet a initrd újraépítésére, hogy legalább a hv_vmbus és hv_storvsc kernel-modulok elérhetők legyenek a kezdeti RAM-lemezen. Ez egy ismert probléma, amely legalább a felsőbb rétegbeli Red Hat-disztribúción alapuló rendszereken jelent meg.

A probléma megoldásához vegyen fel Hyper-V modulokat a initramfs és az újraépítéshez:

Szerkessze `/etc/dracut.conf` és adja hozzá a következő tartalmat:

```shell
add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
```

Initramfs újraépítése:

```bash
dracut -f -v
```

További információ: a [initramfs újraépítése](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>További lépések

Most már készen áll arra, hogy a Red Hat Enterprise Linux virtuális merevlemezt új virtuális gépeket hozzon létre Azure Stack hub-ban. Ha első alkalommal tölti fel a VHD-fájlt Azure Stack hubhoz, tekintse meg a Marketplace- [elem létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)című témakört.

További információ a Red Hat Enterprise Linux futtatására hitelesített hypervisorokról: [Red Hat webhely](https://access.redhat.com/certified-hypervisors).
