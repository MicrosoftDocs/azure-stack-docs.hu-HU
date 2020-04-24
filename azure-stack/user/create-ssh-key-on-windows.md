---
title: Kubernetes-fürt üzembe helyezése Azure Stack hub egyéni virtuális hálózatán
description: Megtudhatja, hogyan helyezhet üzembe egy Kubernetes-fürtöt egy egyéni virtuális hálózaton Azure Stack hub-on.
author: mattbriggs
ms.topic: article
ms.date: 2/28/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 2/28/2020
ms.openlocfilehash: b8bc4c1a9e56f363fa604e8df7a1fa0dbe37fcb0
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81624983"
---
# <a name="create-an-ssh-key-for-linux-on-azure-stack-hub"></a>SSH-kulcs létrehozása Linux rendszeren Azure Stack hub-on

Egy Windows rendszerű gépen létrehozhat egy SSH-kulcsot (Secure Shell) a Linux-gépen. A virtuális gépekkel való SSH-hitelesítéshez használja a cikkben ismertetett lépések által generált nyilvános kulcsot. Ha Windows rendszerű gépet használ a Windows rendszerű számítógépeken, olyan segédprogramokat kap, mint például a bash, az SSH, a git, az apt és sok más. Futtassa az **ssh-keygen** parancsot a kulcs létrehozásához.

## <a name="open-bash-on-windows"></a>Bash megnyitása Windows rendszeren

1. Ha a számítógépen nincs telepítve a Linux rendszerhez készült Windows alrendszer, telepítse az "[Ubuntu Windows](https://www.microsoft.com/en-us/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)rendszeren.  
    A Linux rendszerhez készült Windows alrendszer használatával kapcsolatos további információkért tekintse meg a [Linux Windows alrendszere dokumentációját](https://docs.microsoft.com/windows/wsl/about).

2. Írja be az **Ubuntu** elemet az eszköztárban, és válassza a **Megnyitás**lehetőséget.

## <a name="create-a-key-with-ssh-keygen"></a>Kulcs létrehozása ssh-keygen-vel

1. Írja be a következő parancsot a bash-parancssorból:

    ```bash  
    ssh-keygen -t rsa
    ```

    A bash a következő üzenetet jeleníti meg:

    ```bash
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/username/.ssh/id_rsa):
    ```

2. Írja be a fájlnevet és a hozzáférési kódot. Írja be újra a jelszót.

    A bash a következőket jeleníti meg:

    ```bash
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/user/.ssh/id_rsa): key.txt
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in key.txt.
    Your public key has been saved in key.txt.pub.
    The key fingerprint is:
    SHA256:xanotrealoN6z1/KChqeah0CYVeyhL50/0rq37qgy6Ik username@machine
    The key's randomart image is:
    +---[RSA 2048]----+
    |   o.     .      |
    |  . o.   +       |
    | + o .+ o o      |
    |o o .  O +       |
    | . o .o S .      |
    |  o +. .         |
    |.  o +..o. .     |
    |= . ooB +o+ .    |
    |E=..*X=*.. +.    |
    +----[SHA256]-----+
    ```

3. A nyilvános SSH-kulcs megtekintéséhez:

    ```bash
    cat /home/<username>/<filename>
    ```

    A bash a következőhöz hasonló módon jelenik meg:

    ```bash
    ssh-rsa AAAAB3NzaC1ycTHISISANEXAMPLEDITqEJRNrf6tXy9c0vKnMhiol1BFzHFV3
    +suXk6NDeFcA9uI58VdD/CuvG826R+3OPnXutDdl2MLyH3DGG1fJAHObUWQxmDWluhSGb
    JMHiw2L9Wnf9klG6+qWLuZgjB3TQdus8sZI8YdB4EOIuftpMQ1zkAJRAilY0p4QxHhKbU
    IkvWqBNR+rd5FcQx33apIrB4LMkjd+RpDKOTuSL2qIM2+szhdL5Vp5Y6Z1Ut1EpOrkbg1
    cVw7oW0eP3ROPdyNqnbi9m1UVzB99aoNXaepmYviwJGMzXsTkiMmi8Qq+F8/qy7i4Jxl0
    aignia880qOtQrvNEvyhgZOM5oDhgE3IJ username@machine
    ```

4. Másolja a szöveget `ssh-rsa [...]` a következőre: `username@machinename`. Győződjön meg arról, hogy a szöveg nem tartalmaz kocsivissza értéket. Ezt a szöveget használhatja a virtuális gép vagy a Kubernetes-fürt az AK-motor használatával történő létrehozásakor.

5. Ha Windows rendszerű gépen van, akkor a ** \\ \\wsl $** használatával érheti el a Linux-fájljait.

    1. Írja `\\wsl$` be az eszköztárát. A terjesztést megnyitó alapértelmezett ablak.

    2. Keresse meg a `\\wsl$\Ubuntu\home\<username>` következőt:, és keresse meg a nyilvános és a titkos kulcsot, és mentse egy biztonságos helyre.

## <a name="next-steps"></a>További lépések

- [Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stack hub-on](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
- [Gyors útmutató: linuxos kiszolgálói virtuális gép létrehozása az Azure Stack hub portál használatával](azure-stack-quick-linux-portal.md)
