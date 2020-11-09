---
title: GPU csatlakoztatása Linux rendszerű virtuális géphez Azure Stack HCI-ben
description: A GPU használata Ubuntu Linux virtuális gépen futó AI-munkaterhelésekkel Azure Stack HCI-ben.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/01/2020
ms.openlocfilehash: de2526b2807f4deff66efdf6db69bf4b791f5814
ms.sourcegitcommit: ce864e1d86ad05a03fe896721dea8f0cce92085f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94383683"
---
# <a name="attaching-a-gpu-to-an-ubuntu-linux-vm-on-azure-stack-hci"></a>GPU csatlakoztatása Ubuntu Linux virtuális géphez Azure Stack HCI-ben

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Ez a témakör részletesen ismerteti, hogyan telepítheti és konfigurálhatja az NVIDIA Graphics Processing Unit (GPU) szolgáltatást Azure Stack HCI-vel egy Ubuntu rendszerű virtuális gép (DOHAI) technológiájának használatával.
Ez a dokumentum feltételezi, hogy a Azure Stack HCI-fürtöt telepítette, és telepíti a virtuális gépeket.

## <a name="install-the-gpu-and-then-dismount-it-in-powershell"></a>Telepítse a GPU-t, majd távolítsa el a PowerShellben

1. Telepítse az NVIDIA GPU (ka) t fizikailag a megfelelő kiszolgáló (k) ra az OEM-utasítások és a BIOS-javaslatok után.
2. Kapcsolja be az összes kiszolgálót.
3. Jelentkezzen be rendszergazdai jogosultságokkal rendelkező fiókkal a kiszolgáló (k) re, amelyen telepítve van az NVIDIA GPU.
4. Nyissa meg **Eszközkezelő** és navigáljon a *többi eszköz* szakaszhoz. Meg kell jelennie a "3D video Controller" néven látható eszköznek.
5. A **Tulajdonságok** lap megjelenítéséhez kattintson a jobb gombbal a "3D video Controller" elemre. Kattintson a **részletek** gombra. A legördülő menüben **Property** válassza a "hely elérési útjai" lehetőséget.
6. Jegyezze fel az értéket a string PCIRoot az alábbi képernyőképen látható módon. Kattintson a jobb gombbal az **értékre** , és másolja/mentse.
    :::image type="content" source="media/attach-gpu-to-linux-vm/pciroot.png" alt-text="Elérési út képernyőképe":::
7. Nyissa meg a Windows PowerShellt emelt szintű jogosultságokkal, és hajtsa végre a parancsmagot, hogy leválasztsa `Dismount-VMHostAssignableDevice` a GPU-eszközt a dohai Cserélje le a *LocationPath* értéket a 6. lépésben megszerzett eszköz értékére.
    ```PowerShell
    Dismount-VMHostAssignableDevice -LocationPath "PCIROOT(16)#PCI(0000)#PCI(0000)" -force
    ```
8. Ellenőrizze, hogy az eszköz megjelenik-e a **Eszközkezelő** Rendszereszközök területen a leválasztva beállításnál.
    :::image type="content" source="media/attach-gpu-to-linux-vm/dismounted.png" alt-text="Leválasztott eszköz képernyőképe":::

## <a name="create-and-configure-an-ubuntu-virtual-machine"></a>Ubuntu rendszerű virtuális gép létrehozása és konfigurálása

1. Töltse le az [Ubuntu asztali kiadás 18.04.02 ISO verzióját](http://old-releases.ubuntu.com/releases/18.04.2/).
2. Nyissa meg a **Hyper-V kezelőjét** a rendszer csomópontjára a telepített GPU-val.
   > [!NOTE]
   > A [dohai fejlesztési szolgálat nem támogatja a feladatátvételt](/windows-server/virtualization/hyper-v/plan/plan-for-deploying-devices-using-discrete-device-assignment). Ez egy virtuális gépekre vonatkozó korlátozás a DOHAI webszolgáltatással. Ezért javasoljuk, hogy a **Hyper-V kezelőjével** **Feladatátvevőfürt-kezelő** helyett a CSOMÓPONTON telepítse a virtuális gépet. Az **Feladatátvevőfürt-kezelő** a dohai szolgáltatással való használata meghiúsul, és hibaüzenet jelenik meg arról, hogy a virtuális gépnek nincs olyan eszköze, amely nem támogatja a magas rendelkezésre állást.
3. Ha az 1. lépésben letöltött Ubuntu ISO-t használja, hozzon létre egy új virtuális gépet a **Hyper-V kezelőjének** **új virtuális gép varázslójával** , és hozzon létre egy 2 GB-os memóriával és egy hozzá csatlakoztatott hálózati kártyával rendelkező Ubuntu Gen 1 VM-et.
4. A PowerShellben rendelje hozzá a leválasztott GPU-eszközt a virtuális géphez az alábbi parancsmagok használatával, és cserélje le a *LocationPath* értéket az eszköz értékére.
    ```PowerShell
    # Confirm that there are no DDA devices assigned to the VM
    Get-VMAssignableDevice -VMName Ubuntu

    # Assign the GPU to the VM
    Add-VMAssignableDevice -LocationPath "PCIROOT(16)#PCI(0000)#PCI(0000)" -VMName Ubuntu

    # Confirm that the GPU is assigned to the VM
    Get-VMAssignableDevice -VMName Ubuntu
    ```

    A GPU virtuális géphez való sikeres hozzárendelése az alábbi kimenetet jeleníti meg: a  :::image type="content" source="media/attach-gpu-to-linux-vm/assign-gpu.png" alt-text="GPU képernyőképének hozzárendelése":::

    Adja meg a további értékeket a [GPU dokumentációját](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)követve:

   ```PowerShell
    # Enable Write-Combining on the CPU
    Set-VM -GuestControlledCacheTypes $true -VMName VMName

    # Configure the 32 bit MMIO space
    Set-VM -LowMemoryMappedIoSpace 3Gb -VMName VMName

    # Configure greater than 32 bit MMIO space
    Set-VM -HighMemoryMappedIoSpace 33280Mb -VMName VMName
   ```

   > [!NOTE]
   > A legtöbb GPU esetében a 33280Mb értéknek elegendőnek kell lennie, de a GPU-memóriánál nagyobb értékkel kell helyettesíteni.

5. A Hyper-V kezelőjével kapcsolódjon a virtuális géphez, és indítsa el az Ubuntu operációs rendszer telepítését. Válassza ki az alapértelmezett beállításokat az Ubuntu operációs rendszer telepítéséhez a virtuális gépen.

6. A telepítés befejezése után a **Hyper-V kezelőjével** állítsa le a virtuális gépet, és konfigurálja a virtuális gép **automatikus leállítási műveletét** a vendég operációs rendszer leállításához, az alábbi képernyőképen látható módon:  :::image type="content" source="media/attach-gpu-to-linux-vm/guest-shutdown.png" alt-text="vendég operációsrendszer-leállítás képernyőképe":::

7. Jelentkezzen be az Ubuntuba, és nyissa meg a terminált az SSH telepítéséhez:

   ```shell
    $ sudo apt install openssh-server
   ```

8. Keresse meg az Ubuntu telepítéséhez használt TCP/IP-címet az **ifconfig** paranccsal, és másolja az **ETH0** -interfész IP-címét.

9. Használjon olyan SSH-ügyfelet, mint például az OpenSSH (alapértelmezés szerint a Windows 10 operációs rendszerre telepített ssh.exe) vagy a [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/) az Ubuntu virtuális géphez való kapcsolódás további konfigurálásához.

10. Az SSH-ügyféllel való bejelentkezés után adja ki a parancsot **lspci** , és ellenőrizze, hogy az NVIDIA GPU "3D vezérlő"-ként van-e felsorolva.

    > [!IMPORTANT]
    > Ha az NVIDIA GPU nem "3D vezérlő", akkor ne folytassa tovább. A folytatás előtt győződjön meg arról, hogy a fenti lépéseket követik.

11. A virtuális gépen keresse meg és nyissa meg a **szoftverek & frissítéseit**. Navigáljon a **további illesztőprogramok** elemre, majd válassza ki a listában szereplő legújabb NVIDIA GPU-illesztőprogramokat. Az illesztőprogram telepítésének befejezéséhez kattintson a **módosítások alkalmazása** gombra.
    :::image type="content" source="media/attach-gpu-to-linux-vm/driver-install.png" alt-text="Illesztőprogram-telepítési képernyőkép":::

12. Az illesztőprogram telepítésének befejeződése után indítsa újra az Ubuntu virtuális gépet. Miután a virtuális gép elindul, csatlakozzon az SSH-ügyfélen, és adja ki az **NVIDIA-SMI** parancsot annak ellenőrzéséhez, hogy az NVIDIA GPU-illesztőprogram telepítése sikeresen befejeződött-e. A kimenetnek az alábbi képernyőképhez hasonlónak kell lennie: :::image type="content" source="media/attach-gpu-to-linux-vm/nvidia-smi.png" alt-text="képernyőkép, amely az NVIDIA-SMI parancs kimenetét jeleníti meg.":::

13. Az SSH-ügyfél használatával állítsa be a tárházat, és telepítse a Docker CE motort:

    ```shell
    $ sudo apt-get update
    $ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
    ```
    Adja hozzá a Docker hivatalos GPG-kulcsát:

    ```shell
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

    Győződjön meg arról, hogy most már rendelkezik a kulcstal az ujjlenyomat utolsó nyolc karakterének megkeresésével a 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88 9DC8:

    ```shell
    $ sudo apt-key fingerprint 0EBFCD88
    ```

    A kimenetnek a következőhöz hasonlóan kell kinéznie:

    ```shell
    pub   rsa4096 2017-02-22 [SCEA]
    9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
    uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
    sub   rsa4096 2017-02-22 [S]
    ```

    Az Ubuntu AMD64 architektúra stabil tárházának beállítása:

    ```shell
    $ sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
    ```

    Csomagok frissítése és a Docker CE telepítése:

    ```shell
    $ sudo apt-get update
    $ sudo apt-get install docker-ce docker-ce-cli containerd.io
    ```

    Ellenőrizze a Docker CE telepítését:

    ```shell
    $ sudo docker run hello-world
    ```

## <a name="configure-azure-iot-edge"></a>Azure IoT Edge konfigurálása

Ennek a konfigurációnak az előkészítéséhez tekintse át az [NVIDIA-Deepstream-Azure-IoT-Edge-on-a-NVIDIA-Jetson-Nano](https://github.com/Azure-Samples/NVIDIA-Deepstream-Azure-IoT-Edge-on-a-NVIDIA-Jetson-Nano) GitHub-tárházban található GYIK-t, amely leírja, hogyan kell telepíteni a Docker-t a Moby helyett. A felülvizsgálás után folytassa az alábbi lépésekkel.

### <a name="install-nvidia-docker"></a>Az NVIDIA Docker telepítése

1. Az SSH-ügyfélből adja hozzá a csomagok tárházait:

    ```shell
    curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
    sudo apt-key add -
    distribution=$(. /etc/os-release;echo $ID$VERSION_ID)

    curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
    sudo tee /etc/apt/sources.list.d/nvidia-docker.list

    sudo apt-get update
    ```

2. Telepítse az NVIDIA-docker2-t, és töltse be újra a Docker-démon konfigurációját:

    ```shell
    sudo apt-get install -y nvidia-docker2
    sudo pkill -SIGHUP dockerd
    ```

3. Indítsa újra a virtuális gépet:

    ```shell
    sudo /sbin/shutdown -r now
    ```

4. Újraindítás után ellenőrizze az NVIDIA Docker sikeres telepítését:

    ```shell
    sudo docker run --runtime=nvidia --rm nvidia/cuda:9.0-base nvidia-smi
    ```

    A sikeres telepítés az alábbi képernyőképen látható kimenettel fog kinézni:  :::image type="content" source="media/attach-gpu-to-linux-vm/docker.png" alt-text="sikeres Docker-telepítés képernyőképe":::

5. Az itt leírt útmutatást követve folytassa a Azure IoT Edge telepítésével, kihagyva a futtatókörnyezet telepítését:

    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list

    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    sudo apt-get update

    sudo apt-get install iotedge
    ```

    > [!NOTE]
    > A Azure IoT Edge telepítése után ellenőrizze, hogy a config. YAML megtalálható-e az Ubuntu virtuális gépen a következő címen:/etc/iotedge/config.YAML

6. Hozzon létre egy IoT Edge eszköz identitását a Azure Portal [alábbi útmutatást](/azure/iot-edge/how-to-register-device#register-in-the-azure-portal). Ezután másolja az újonnan létrehozott IoT Edge eszköz-kapcsolatok sztringjét.

7. Az SSH-ügyfél használatával frissítse az eszköz-kapcsolatok karakterláncát a config. YAML fájlban az Ubuntu virtuális gépen:

    ```shell
    sudo nano /etc/iotedge/config.yaml
    ```

    Keresse meg a fájl kiépítési konfigurációit, és tegye meg a "manuális kiépítési konfiguráció" szakasz megjegyzését. Device_connection_string értékének frissítése a IoT Edge eszközhöz tartozó kapcsolatok karakterláncával. Ügyeljen rá, hogy a többi kiépítési szakaszt is jegyezze fel. Győződjön meg arról, hogy a kiépítési: sor nem rendelkezik korábbi szóközökkel, és hogy a beágyazott elemek két szóközzel vannak behúzva:

    :::image type="content" source="media/attach-gpu-to-linux-vm/manual-provisioning.png" alt-text="Manuális kiépítés konfigurációjának képernyőképe":::

    A vágólap tartalmának Nanoba való beillesztéséhez kattintson a SHIFT + jobb gombbal, vagy nyomja le a SHIFT + INSERT billentyűkombinációt. Mentse és zárjuk be a fájlt (Ctrl + X, Y, Enter).

8. Az SSH-ügyfél használatával indítsa újra a IoT Edge démont:

    ```shell
    sudo systemctl restart iotedge
    ```

    Ellenőrizze a telepítést, és ellenőrizze a IoT Edge démon állapotát:

    ```shell
    systemctl status iotedge

    journalctl -u iotedge --no-pager --no-full
    ```

9. Az SSH-ügyfél használatával hozza létre a következő címtár-struktúrát az Ubuntu virtuális gépen:

    ```shell
    cd /var
    sudo mkdir deepstream
    mkdir ./deepstream/custom_configs
    cd /var/deepstream
    sudo mkdir custom_streams
    sudo chmod -R 777 /var/deepstream
    cd ./custom_streams
    ```

10. Győződjön meg arról, hogy a munkakönyvtára/var/deepstream/custom_streams, és töltse le a [bemutató videók fájlt](https://github.com/Azure-Samples/NVIDIA-Deepstream-Azure-IoT-Edge-on-a-NVIDIA-Jetson-Nano) az SSH-ügyfél következő parancsának végrehajtásával:

    ```shell
    wget -O cars-streams.tar.gz --no-check-certificate https://onedrive.live.com/download?cid=0C0A4A69A0CDCB4C&resid=0C0A4A69A0CDCB4C%21588371&authkey=AAavgrxG95v9gu0
    ```

    A videofájlok kibontásának megszüntetése:

    ```shell
    tar -xzvf cars-streams.tar.gz
    ```

    A/var/deepstream/könyvtár tartalmának custom_streams az alábbi képernyőképhez hasonlónak kell lennie:

    :::image type="content" source="media/attach-gpu-to-linux-vm/custom-streams.png" alt-text="Egyéni streamek képernyőképe":::

11. Hozzon létre egy test5_config_file_src_infer_azure_iotedge_edited.txt nevű új fájlt a/var/deepstream/custom_configs könyvtárban. Egy szövegszerkesztővel nyissa meg a fájlt, és illessze be a következő kódot, majd mentse és zárjuk be a fájlt.

    ```shell
    # Copyright (c) 2018 NVIDIA Corporation.  All rights reserved.
    #
    # NVIDIA Corporation and its licensors retain all intellectual property
    # and proprietary rights in and to this software, related documentation
    # and any modifications thereto.  Any use, reproduction, disclosure or
    # distribution of this software and related documentation without an express
    # license agreement from NVIDIA Corporation is strictly prohibited.

    [application]
    enable-perf-measurement=1
    perf-measurement-interval-sec=5
    #gie-kitti-output-dir=streamscl

    [tiled-display]
    enable=1
    rows=2
    columns=2
    width=1280
    height=720
    gpu-id=0
    #(0): nvbuf-mem-default - Default memory allocated, specific to particular platform
    #(1): nvbuf-mem-cuda-pinned - Allocate Pinned/Host cuda memory, applicable for Tesla
    #(2): nvbuf-mem-cuda-device - Allocate Device cuda memory, applicable for Tesla
    #(3): nvbuf-mem-cuda-unified - Allocate Unified cuda memory, applicable for Tesla
    #(4): nvbuf-mem-surface-array - Allocate Surface Array memory, applicable for Jetson
    nvbuf-memory-type=0

    [source0]
    enable=1
    #Type - 1=CameraV4L2 2=URI 3=MultiURI
    type=3
    uri=file://../../../../../samples/streams/sample_1080p_h264.mp4
    num-sources=2
    gpu-id=0
    nvbuf-memory-type=0

    [source1]
    enable=1
    #Type - 1=CameraV4L2 2=URI 3=MultiURI
    type=3
    uri=file://../../../../../samples/streams/sample_1080p_h264.mp4
    num-sources=2
    gpu-id=0
    nvbuf-memory-type=0

    [sink0]
    enable=0

    [sink3]
    enable=1
    #Type - 1=FakeSink 2=EglSink 3=File 4=RTSPStreaming
    type=4
    #1=h264 2=h265
    codec=1
    sync=0
    bitrate=4000000
    # set below properties in case of RTSPStreaming
    rtsp-port=8554
    udp-port=5400

    [sink1]
    enable=1
    #Type - 1=FakeSink 2=EglSink 3=File 4=UDPSink 5=nvoverlaysink 6=MsgConvBroker
    type=6
    msg-conv-config=../configs/dstest5_msgconv_sample_config.txt
    #(0): PAYLOAD_DEEPSTREAM - Deepstream schema payload
    #(1): PAYLOAD_DEEPSTREAM_MINIMAL - Deepstream schema payload minimal
    #(256): PAYLOAD_RESERVED - Reserved type
    #(257): PAYLOAD_CUSTOM   - Custom schema payload
    msg-conv-payload-type=1
    msg-broker-proto-lib=/opt/nvidia/deepstream/deepstream-4.0/lib/libnvds_azure_edge_proto.so
    topic=mytopic
    #Optional:
    #msg-broker-config=../../../../libs/azure_protocol_adaptor/module_client/cfg_azure.txt

    [sink2]
    enable=0
    type=3
    #1=mp4 2=mkv
    container=1
    #1=h264 2=h265 3=mpeg4
    ## only SW mpeg4 is supported right now.
    codec=3
    sync=1
    bitrate=2000000
    output-file=out.mp4
    source-id=0

    [osd]
    enable=1
    gpu-id=0
    border-width=1
    text-size=15
    text-color=1;1;1;1;
    text-bg-color=0.3;0.3;0.3;1
    font=Arial
    show-clock=0
    clock-x-offset=800
    clock-y-offset=820
    clock-text-size=12
    clock-color=1;0;0;0
    nvbuf-memory-type=0

    [streammux]
    gpu-id=0
    ##Boolean property to inform muxer that sources are live
    live-source=0
    batch-size=4
    ##time out in usec, to wait after the first buffer is available
    ##to push the batch even if the complete batch is not formed
    batched-push-timeout=40000
    ## Set muxer output width and height
    width=1920
    height=1080
    ##Enable to maintain aspect ratio wrt source, and allow black borders, works
    ##along with width, height properties
    enable-padding=0
    nvbuf-memory-type=0

    [primary-gie]
    enable=1
    gpu-id=0
    batch-size=4
    ## 0=FP32, 1=INT8, 2=FP16 mode
    bbox-border-color0=1;0;0;1
    bbox-border-color1=0;1;1;1
    bbox-border-color2=0;1;1;1
    bbox-border-color3=0;1;0;1
    nvbuf-memory-type=0
    interval=0
    gie-unique-id=1
    model-engine-file=../../../../../samples/models/Primary_Detector/resnet10.caffemodel_b4_int8.engine
    labelfile-path=../../../../../samples/models/Primary_Detector/labels.txt
    config-file=../../../../../samples/configs/deepstream-app/config_infer_primary.txt
    #infer-raw-output-dir=../../../../../samples/primary_detector_raw_output/

    [tracker]
    enable=1
    tracker-width=600
    tracker-height=300
    ll-lib-file=/opt/nvidia/deepstream/deepstream-4.0/lib/libnvds_mot_klt.so
    #ll-config-file required for DCF/IOU only
    #ll-config-file=tracker_config.yml
    #ll-config-file=iou_config.txt
    gpu-id=0
    #enable-batch-process applicable to DCF only
    enable-batch-process=0

    [tests]
    file-loop=1
    ```

12. Lépjen az Azure Portalra. Válassza ki **IoT hub kiépített** elemet, kattintson az **automatikus eszközkezelés** elemre, majd a **IoT Edge** elemre:

    :::image type="content" source="media/attach-gpu-to-linux-vm/iot-edge.png" alt-text="Az eszközkezelés automatikus kezelése képernyőkép":::

13. A jobb oldali ablaktáblán válassza ki azt az eszköz-identitást, amelynek az eszköz-kapcsolatainak sztringjét használta. Kattintson a modulok beállítása elemre:

    :::image type="content" source="media/attach-gpu-to-linux-vm/set-modules.png" alt-text="Modulok beállítása képernyőfelvétel":::

14. A IoT Edge modulok területen kattintson és válassza a piactér modul elemet:

    :::image type="content" source="media/attach-gpu-to-linux-vm/marketplace-module.png" alt-text="Piactér-modul képernyőképe":::

15. Keressen az NVIDIA kifejezésre, és válassza az DeepStream SDK-t, például az alábbiakat:

    :::image type="content" source="media/attach-gpu-to-linux-vm/deepstream.png" alt-text="DeepStream SDK képernyőkép":::

16. Győződjön meg arról, hogy a NvidiaDeepStreamSDK modul szerepel a IoT Edge modulok területen:

    :::image type="content" source="media/attach-gpu-to-linux-vm/edge-modules.png" alt-text="IoT Edge modulok képernyőképe":::

17. Kattintson a "NVIDIADeepStreamSDK" modulra, és válassza a "tároló létrehozása lehetőségek" lehetőséget. Az alapértelmezett konfiguráció itt látható:

    :::image type="content" source="media/attach-gpu-to-linux-vm/container-create-options.png" alt-text="Tároló létrehozása – beállítások képernyőkép":::

    Cserélje le a fenti konfigurációt az alábbi konfigurációra:

    ```shell
    {
      "ExposedPorts": {
        "8554/tcp": {}
      },
      "Entrypoint": [
        "/usr/bin/deepstream-test5-app",
        "-c",
        "test5_config_file_src_infer_azure_iotedge_edited.txt",
        "-p",
        "1",
        "-m",
        "1"
      ],
      "HostConfig": {
        "runtime": "nvidia",
        "Binds": [
          "/var/deepstream/custom_configs:/root/deepstream_sdk_v4.0.2_x86_64/sources/apps/sample_apps/deepstream-test5/custom_configs/",
          "/var/deepstream/custom_streams:/root/deepstream_sdk_v4.0.2_x86_64/sources/apps/sample_apps/deepstream-test5/custom_streams/"
        ],
        "PortBindings": {
          "8554/tcp": [
            {
              "HostPort": "8554"
            }
          ]
        }
      },
      "WorkingDir": "/root/deepstream_sdk_v4.0.2_x86_64/sources/apps/sample_apps/deepstream-test5/custom_configs/"
    }
    ```

18. Kattintson a **felülvizsgálat és létrehozás** elemre, majd a következő lapon kattintson a **Létrehozás** gombra. Az alábbi három modult látnia kell a Azure Portal IoT Edge eszközéhez:

    :::image type="content" source="media/attach-gpu-to-linux-vm/edge-hub-connections.png" alt-text="Modulok és IoT Edge hub-kapcsolatok képernyőképe":::

19. Csatlakozzon az Ubuntu virtuális géphez az SSH-ügyfél használatával, és ellenőrizze, hogy a megfelelő modulok futnak-e:

    ```shell
    sudo iotedge list
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify-modules-sudo.png" alt-text="Képernyőkép, amely a iotedge listájának kimenetét jeleníti meg.":::

    ```shell
    nvidia-smi
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify-modules-nvidia-smi.png" alt-text="NVIDIA – SMI képernyőkép":::

    > [!NOTE]
    > A NvidiaDeepstream-tároló letöltése több percet is igénybe vehet. A letöltést a "journalctl-u iotedge--No-pager--No-Full" parancs használatával ellenőrizheti, hogy megnézze-e a iotedge démon naplóit.

20. Győződjön meg arról, hogy a NvdiaDeepStreem tároló működik. Az alábbi képernyőképek parancs kimenete a sikeres műveletet jelzi.

    ```shell
    sudo iotedge list
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify1.png" alt-text="A NvdiaDeepStreem tárolót megjelenítő kimenet képernyőképe.":::

    ```shell
    sudo iotedge logs -f NVIDIADeepStreamSDK
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify2.png" alt-text="Képernyőkép, amely a iotedge logs-f NVIDIADeepStreamSDK parancs kimenetét jeleníti meg.":::

    ```shell
    nvidia-smi
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify3.png" alt-text="Képernyőkép, amely az NVIDIA-SMI parancs további kimenetét jeleníti meg.":::

21. Erősítse meg az Ubuntu virtuális gép TCP/IP-címét az **ifconfig** parancs használatával, és keresse meg az **ETH0** felület melletti TCP/IP-címet.

22. Telepítse a VLC-lejátszót a munkaállomáson. A VLC-lejátszón kattintson a **Media-> Open Network stream** elemre, és írja be a következő formátumot a címben:

    rtsp://ipaddress:8554/ds-test

    ahol az IP-cím a virtuális gép TCP/IP-címe.

    :::image type="content" source="media/attach-gpu-to-linux-vm/vlc-player.png" alt-text="A VLC player képernyőképe":::

## <a name="next-steps"></a>Következő lépések

További információ a GPU-k és a dohai fejlesztési szolgáltatásról:

- [Az eszközök diszkrét eszköz-hozzárendeléssel történő telepítésének megtervezése](/windows-server/virtualization/hyper-v/plan/plan-for-deploying-devices-using-discrete-device-assignment)
- [Grafikus eszközök üzembe helyezése különálló eszköz-hozzárendelés használatával](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)
