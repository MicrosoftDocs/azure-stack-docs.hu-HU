---
title: Ruby-alkalmazás üzembe helyezése egy virtuális géphez az Azure Stackben |} A Microsoft Docs
description: Ruby-alkalmazás üzembe helyezése egy virtuális géphez az Azure Stackben.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: f7867e963551d04336ac4092e8b1b2f033c29e94
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838337"
---
# <a name="deploy-a-ruby-web-app-to-a-vm-in-azure-stack"></a>Ruby-webalkalmazás üzembe helyezése egy virtuális Gépet az Azure Stackben

Létrehozhat egy virtuális Gépet az Azure Stack a Ruby webalkalmazás üzemeltetéséhez. Ebben a cikkben állítson be egy kiszolgálót, konfigurálja a kiszolgálót, a Ruby-webalkalmazás üzemeltetéséhez, és az alkalmazás üzembe helyezése az Azure Stack.

Ez a cikk a Ruby és a egy Ruby on Rails webalkalmazás keretrendszerben használja.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gép az Azure Stackben. Útmutatásért lásd: [Linux virtuális gép üzembe helyezése az Azure Stackben webalkalmazás üzemeltetéséhez](azure-stack-dev-start-howto-deploy-linux.md).

2. A virtuális gép hálózati ablaktáblán győződjön meg arról, a következő portokat érhetők el:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az a weblapok kiszolgálókról való küldéséhez használt protokoll. DNS-nevét vagy IP-cím az ügyfelek csatlakoznak a HTTP Protokollon keresztül. |
    | 443 | HTTPS | Hypertext Transfer Protocol biztonságos (HTTPS), amely szükséges a biztonsági tanúsítvány, és lehetővé teszi, hogy az információ titkosított továbbításába HTTP biztonságos verziója is. |
    | 22 | SSH | Secure Shell (SSH) nem titkosított hálózati protokoll biztonságos kommunikációhoz. Ezt a kapcsolatot használja az SSH-ügyfelet konfigurálja a virtuális Gépet, és az alkalmazás üzembe helyezéséhez. |
    | 3389 | RDP | Választható. A távoli asztal protokoll (RDP) lehetővé teszi, hogy egy távoli asztali kapcsolatot egy grafikus felhasználói felületet használja a gépén.   |
    | 3000 | Egyéni | A Ruby on Rails webalkalmazás-keretrendszer fejlesztési által használt port. Üzemi kiszolgáló esetén irányíthatja a forgalmat a 80-as és 443-as porton keresztül. |

## <a name="install-ruby"></a>A Ruby telepítése

1. Csatlakozás a virtuális gép az SSH-ügyfél használatával. Útmutatásért lásd: [a PuTTy SSH-n keresztüli csatlakozás](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Telepítse a PPA-val tárház. A bash parancssorban a virtuális Gépen adja meg a következő parancsokat:

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. Telepítse a Ruby és a Ruby on Rails a virtuális Gépen. Miközben továbbra is csatlakozik a virtuális gép az SSH-munkamenetben, adja meg a következő parancsokat:

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. Telepítse a Ruby on Rails-függőségeket. Miközben továbbra is csatlakozik a virtuális gép az SSH-munkamenetben, adja meg a következő parancsokat:

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!Note]  
    > Ruby telepít a Rails függőségek, bár előfordulhat, hogy ismételten futtatni szeretné `sudo gem install bundler`. Ha a telepítés nem sikerül, tekintse át a hibanaplókat, és a problémák megoldásához.

4. A telepítés ellenőrzése. Miközben továbbra is csatlakozik a virtuális gép az SSH-munkamenetben, adja meg a következő parancsot:

    ```bash  
        ruby -v
    ```

3. [A Git telepítése](https://git-scm.com), a széles körben elosztott verziókezelő és forráskód code system management (SCM). Miközben továbbra is csatlakozik a virtuális gép az SSH-munkamenetben, adja meg a következő parancsot:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>Hozzon létre és futtathat egy alkalmazást

1. Miközben továbbra is csatlakozik a virtuális gép az SSH-munkamenetben, adja meg a következő parancsokat:

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2. Nyissa meg az új kiszolgálóra. Megtekintheti a futó webalkalmazás.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [fejlesztés az Azure Stackhez](azure-stack-dev-start.md).
- Ismerje meg [közös üzemelő példányok az Azure stack-beli iaas](azure-stack-dev-start-deploy-app.md).
- Ismerje meg, a Ruby programozási nyelvet és további források keresése Python: [Ruby-lang.org](https://www.ruby-lang.org).
