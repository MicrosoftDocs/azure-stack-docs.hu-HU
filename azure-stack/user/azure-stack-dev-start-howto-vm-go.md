---
title: Go-webalkalmazás üzembe helyezése Azure Stack hub virtuális gépén
description: Go-webalkalmazás üzembe helyezése Azure Stack hub-beli virtuális gépen
author: mattbriggs
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: e57de0bbcb51b4e1bf2d3d4501b57cbc5115927e
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883759"
---
# <a name="deploy-a-go-web-app-to-a-vm-in-azure-stack-hub"></a>Go-webalkalmazás üzembe helyezése Azure Stack hub-beli virtuális gépen

Létrehozhat egy virtuális gépet (VM) egy go-webalkalmazás üzemeltetéséhez Azure Stack központban. Ebben a cikkben egy kiszolgálót állít be, konfigurálja a kiszolgálót a go-webalkalmazás üzemeltetéséhez, majd telepítse az alkalmazást Azure Stack hubhoz.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gépet Azure Stack hub-ban a [Linux rendszerű virtuális gép üzembe helyezése című témakör útmutatását követve Azure stack hub webalkalmazásának üzemeltetéséhez](azure-stack-dev-start-howto-deploy-linux.md).

2. A virtuálisgép-hálózat ablaktáblán győződjön meg arról, hogy a következő portok elérhetők:

    | Port | Protocol (Protokoll) | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az a protokoll, amelyet a weblapok kiszolgálókról történő kézbesítéséhez használtak. Az ügyfelek HTTP-n keresztül csatlakoznak a DNS-név vagy IP-cím használatával. |
    | 443 | HTTPS | A Hypertext Transfer Protocol Secure (HTTPS) a HTTP olyan biztonságos verziója, amelynek biztonsági tanúsítványra van szüksége, és lehetővé teszi az adatok titkosított átvitelét. |
    | 22 | SSH | A Secure Shell (SSH) egy titkosított hálózati protokoll a biztonságos kommunikációhoz. Ez a kapcsolódás egy SSH-ügyféllel a virtuális gép konfigurálásához és az alkalmazás üzembe helyezéséhez használható. |
    | 3389 | RDP | Választható. A RDP protokoll (RDP) lehetővé teszi, hogy a távoli asztali kapcsolat grafikus felhasználói felületet használjon a gépen.   |
    | 3000 | Egyéni | Az 3000-es portot a go web Framework használja a fejlesztéshez. Üzemi kiszolgálók esetében a 80-es és a 443-os adatforgalmat irányítja át. |

## <a name="install-go"></a>Telepítés menete

1. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával. Útmutatásért lásd: [kapcsolat SSH-n keresztül a PuTTY használatával ](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. A virtuális gépen a bash-parancssorba írja be a következő parancsokat:

    ```bash  
    wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz
    sudo tar -xvf go1.10.linux-amd64.tar.gz
    sudo mv go /usr/local
    ```

2. Állítsa be a go-környezetet a virtuális gépen. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsokat:

    ```bash  
    export GOROOT=/usr/local/go
    export GOPATH=$HOME/Projects/ADMFactory/Golang
    export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

    vi ~/.profile
    ```

3. Ellenőrizze a telepítést. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsot:

    ```bash  
        go version
    ```

3. [Telepítse a git](https://git-scm.com)-t, amely egy széles körben elosztott verziókövetés és egy forráskód-kezelési (SCM) rendszer. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsot:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása

1. Állítsa be a git-tárházat a virtuális gépen. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsokat:

    ```bash  
       git clone https://github.com/appleboy/go-hello
    
       cd go-hello
       go get -d
    ```

2. Indítsa el az alkalmazást. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsot:

    ```bash  
       go run hello-world.go
    ```

3. Nyissa meg az új kiszolgálót. Látnia kell a futó webalkalmazást.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure stack hub fejlesztéséről](azure-stack-dev-start.md).
- Ismerkedjen meg [az Azure stack hub általános telepítései IaaS](azure-stack-dev-start-deploy-app.md).
- A go programozási nyelvének megismeréséhez és a go további erőforrásainak megkereséséhez tekintse meg a következőt: [Golang.org](https://golang.org).
