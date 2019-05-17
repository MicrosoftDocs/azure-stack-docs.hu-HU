---
title: Egy GO-webalkalmazás üzembe helyezése egy virtuális gépet az Azure Stackben |} A Microsoft Docs
description: Egy GO-webalkalmazás üzembe helyezése egy virtuális Gépet az Azure Stackben
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: f24970c334073928b2a74a1b0b349cafb8f093b1
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782982"
---
# <a name="how-to-deploy-a-go-web-app-to-a-vm-in-azure-stack"></a>Egy GO-webalkalmazás üzembe helyezése egy virtuális Gépet az Azure Stackben

Létrehozhat egy virtuális Gépet, a Go webalkalmazás az Azure Stackben üzemeltetéséhez. Ez a cikk megvizsgálja a lépéseket követheti a kiszolgáló, a GO-webalkalmazás üzemeltetéséhez a kiszolgáló konfigurálása, és üzembe kell helyezni az alkalmazás beállítását.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gép, állítsa be az Azure Stackben. Kövesse a [Linux virtuális gép üzembe helyezése az Azure Stackben webalkalmazás üzemeltetéséhez](azure-stack-dev-start-howto-deploy-linux.md).

2. A virtuális gép hálózati panelen ellenőrizze, a következő portokat érhetők el:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az a weblapok kiszolgálókról való küldéséhez használt protokoll. DNS-nevét vagy IP-cím az ügyfelek csatlakoznak a HTTP Protokollon keresztül. |
    | 443 | HTTPS | Hypertext Transfer Protocol biztonságos (HTTPS), amely szükséges a biztonsági tanúsítvány, és lehetővé teszi, hogy az információ titkosított továbbításába HTTP biztonságos verziója is.  |
    | 22 | SSH | Secure Shell (SSH) nem titkosított hálózati protokoll biztonságos kommunikációhoz. Az SSH-ügyfelet konfigurálja a virtuális Gépet, és az alkalmazás üzembe helyezéséhez használandó ehhez a kapcsolathoz. |
    | 3389 | RDP | Választható. A távoli asztali protokoll lehetővé teszi egy távoli asztali kapcsolatot egy grafikus felhasználói felületen a gép.   |
    | 3000 | Egyéni | 3000 portot használja a GO webes keretrendszer fejlesztés alatt. Üzemi kiszolgáló esetén célszerű továbbítani a forgalmat a 80-as és 443-as porton keresztül. |

## <a name="install-go"></a>Telepítse a GO

1. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával. Útmutatásért lásd: [a PuTTy SSH-n keresztüli csatlakozás](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
1. A bash parancssorban a virtuális Gépen írja be a következő parancsokat:

    ```bash  
    wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz
    sudo tar -xvf go1.10.linux-amd64.tar.gz
    sudo mv go /usr/local
    ```

2. Állítsa be a GO-környezetet a virtuális Gépen. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
    export GOROOT=/usr/local/go
    export GOPATH=$HOME/Projects/ADMFactory/Golang
    export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

    vi ~/.profile
    ```

3. A telepítés ellenőrzése. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
        go version
    ```

3. A Git telepítése. [A Git](https://git-scm.com) egy rendszer széles körben elterjedt változat és forráskód kód management (SCM). Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása

1. Állítsa be a virtuális gépen a Git-tárházhoz. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
       git clone https://github.com/appleboy/go-hello
    
       cd go-hello
       go get -d
    ```

2. Az alkalmazás elindításához. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsot:

    ```bash  
       go run hello-world.go
    ```

3.  Most nyissa meg az új kiszolgálóra, és megjelenik a futó webalkalmazás.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [fejlesztés az Azure Stackhez](azure-stack-dev-start.md)
- Ismerje meg [közös üzemelő példányok az Azure stack-beli iaas](azure-stack-dev-start-deploy-app.md).
- Ismerje meg, a Go programozási nyelvet, és további források keresése a GÓHOZ készült: [Golang.org](https://golang.org).