---
title: Egy Go-webalkalmazás üzembe helyezése egy virtuális gépet az Azure Stackben |} A Microsoft Docs
description: Egy Go-webalkalmazás üzembe helyezése egy virtuális Gépet az Azure Stackben
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 8b1f207929e018b27bb3f20db8d2b7d5abe46088
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838349"
---
# <a name="deploy-a-go-web-app-to-a-vm-in-azure-stack"></a>Egy Go-webalkalmazás üzembe helyezése egy virtuális Géphez az Azure Stack

Létrehozhat egy virtuális gépet (VM) egy Go-webalkalmazás létrehozása az Azure Stack futtatásához. Ebben a cikkben állítson be egy kiszolgálót, konfigurálja a kiszolgálót, a Go-webalkalmazás üzemeltetéséhez, és az alkalmazás üzembe helyezése az Azure Stack.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gép az Azure Stackben utasításait követve [Linux virtuális gép üzembe helyezése az Azure Stackben webalkalmazás üzemeltetéséhez](azure-stack-dev-start-howto-deploy-linux.md).

2. A virtuális gép hálózati ablaktáblán győződjön meg arról, hogy elérhetők-e a következő portokat:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az a weblapok kiszolgálókról való küldéséhez használt protokoll. DNS-nevét vagy IP-cím az ügyfelek csatlakoznak a HTTP Protokollon keresztül. |
    | 443 | HTTPS | Hypertext Transfer Protocol biztonságos (HTTPS), amely szükséges a biztonsági tanúsítvány, és lehetővé teszi, hogy az információ titkosított továbbításába HTTP biztonságos verziója is. |
    | 22 | SSH | Secure Shell (SSH) nem titkosított hálózati protokoll biztonságos kommunikációhoz. Ezt a kapcsolatot használja az SSH-ügyfelet konfigurálja a virtuális Gépet, és az alkalmazás üzembe helyezéséhez. |
    | 3389 | RDP | Választható. A távoli asztal protokoll (RDP) lehetővé teszi, hogy egy távoli asztali kapcsolatot egy grafikus felhasználói felületet használja a gépén.   |
    | 3000 | Egyéni | 3000 portot használja a Go webes keretrendszer fejlesztés alatt. Üzemi kiszolgáló esetén irányíthatja a forgalmat a 80-as és 443-as porton keresztül. |

## <a name="install-go"></a>Telepítse a Go

1. Csatlakozás a virtuális gép az SSH-ügyfél használatával. Útmutatásért lásd: [a PuTTY SSH-n keresztüli csatlakozás ](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. A bash parancssorban a virtuális Gépen adja meg a következő parancsokat:

    ```bash  
    wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz
    sudo tar -xvf go1.10.linux-amd64.tar.gz
    sudo mv go /usr/local
    ```

2. Állítsa be a Go-környezetet a virtuális Gépen. Miközben továbbra is csatlakozik a virtuális gép az SSH-munkamenetben, adja meg a következő parancsokat:

    ```bash  
    export GOROOT=/usr/local/go
    export GOPATH=$HOME/Projects/ADMFactory/Golang
    export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

    vi ~/.profile
    ```

3. A telepítés ellenőrzése. Miközben továbbra is csatlakozik a virtuális gép az SSH-munkamenetben, adja meg a következő parancsot:

    ```bash  
        go version
    ```

3. [A Git telepítése](https://git-scm.com), a széles körben elosztott verziókezelő és forráskód code system management (SCM). Miközben továbbra is csatlakozik a virtuális gép az SSH-munkamenetben, adja meg a következő parancsot:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása

1. Állítsa be a virtuális gépen a Git-tárházhoz. Miközben továbbra is csatlakozik a virtuális gép az SSH-munkamenetben, adja meg a következő parancsokat:

    ```bash  
       git clone https://github.com/appleboy/go-hello
    
       cd go-hello
       go get -d
    ```

2. Az alkalmazás elindításához. Miközben továbbra is csatlakozik a virtuális gép az SSH-munkamenetben, adja meg a következő parancsot:

    ```bash  
       go run hello-world.go
    ```

3. Nyissa meg az új kiszolgálóra. Megtekintheti a futó webalkalmazás.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [fejlesztés az Azure Stackhez](azure-stack-dev-start.md).
- Ismerje meg [közös üzemelő példányok az Azure stack-beli iaas](azure-stack-dev-start-deploy-app.md).
- Ismerje meg, a Go programozási nyelvet, és további források keresése a Góhoz készült: [Golang.org](https://golang.org).
