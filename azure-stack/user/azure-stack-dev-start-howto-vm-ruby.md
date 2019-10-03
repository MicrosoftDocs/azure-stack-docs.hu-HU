---
title: Ruby-alkalmazás üzembe helyezése egy virtuális gépen Azure Stackban | Microsoft Docs
description: Ruby-alkalmazás üzembe helyezése Azure Stack-beli virtuális gépen.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: e54fc1cd1e8fe8588a9498b5af8476437880e988
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824185"
---
# <a name="deploy-a-ruby-web-app-to-a-vm-in-azure-stack"></a>Ruby-webalkalmazás üzembe helyezése Azure Stack-beli virtuális gépen

Létrehozhat egy virtuális gépet a Ruby-webalkalmazás üzemeltetéséhez Azure Stackban. Ebben a cikkben egy kiszolgálót állít be, konfigurálhatja a kiszolgálót a Ruby-webalkalmazás üzemeltetéséhez, majd az alkalmazást Azure Stackre telepítheti.

Ez a cikk a Ruby és a Ruby on Rails webes keretrendszer használatát ismerteti.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. A virtuális gép beállítása Azure Stackban. Útmutatásért lásd: [Linux rendszerű virtuális gép üzembe helyezése Azure stack-ban webalkalmazás üzemeltetéséhez](azure-stack-dev-start-howto-deploy-linux.md).

2. A virtuálisgép-hálózat ablaktáblán győződjön meg arról, hogy a következő portok elérhetők:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az a protokoll, amelyet a weblapok kiszolgálókról történő kézbesítéséhez használtak. Az ügyfelek HTTP-n keresztül csatlakoznak a DNS-név vagy IP-cím használatával. |
    | 443 | HTTPS | A Hypertext Transfer Protocol Secure (HTTPS) a HTTP olyan biztonságos verziója, amelynek biztonsági tanúsítványra van szüksége, és lehetővé teszi az adatok titkosított átvitelét. |
    | 22 | SSH | A Secure Shell (SSH) egy titkosított hálózati protokoll a biztonságos kommunikációhoz. Ez a kapcsolódás egy SSH-ügyféllel a virtuális gép konfigurálásához és az alkalmazás üzembe helyezéséhez használható. |
    | 3389 | RDP | Nem kötelező. A RDP protokoll (RDP) lehetővé teszi, hogy a távoli asztali kapcsolat grafikus felhasználói felületet használjon a gépen.   |
    | 3000 | Egyéni | A Ruby on Rails webes keretrendszer által a fejlesztés során használt port. Üzemi kiszolgálók esetében a 80-es és a 443-os adatforgalmat irányítja át. |

## <a name="install-ruby"></a>A Ruby telepítése

1. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával. Útmutatásért lásd: [kapcsolat SSH-n keresztül a PuTTY használatával](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Telepítse a PPA-tárházat. A virtuális gépen a bash-parancssorba írja be a következő parancsokat:

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. Telepítse a Ruby és a Ruby on Rails szolgáltatást a virtuális gépen. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsokat:

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. Telepítse a Ruby on Rails függőségeit. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsokat:

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!Note]  
    > A Ruby on Rails-függőségek telepítésekor előfordulhat, hogy többször is futtatnia kell a `sudo gem install bundler` értéket. Ha a telepítés sikertelen, tekintse át a hibák naplóit, és oldja meg a problémákat.

4. Ellenőrizze a telepítést. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsot:

    ```bash  
        ruby -v
    ```

3. [Telepítse a git](https://git-scm.com)-t, amely egy széles körben elosztott verziókövetés és egy forráskód-kezelési (SCM) rendszer. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsot:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>Alkalmazás létrehozása és futtatása

1. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsokat:

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2. Nyissa meg az új kiszolgálót. Látnia kell a futó webalkalmazást.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>További lépések

- További információ a [Azure stack fejlesztéséről](azure-stack-dev-start.md).
- Ismerkedjen meg a [Azure stack IaaS való gyakori központi telepítésekkel](azure-stack-dev-start-deploy-app.md).
- A Ruby programozási nyelvének megismeréséhez és a Ruby további erőforrásainak megkereséséhez lásd: [Ruby-lang.org](https://www.ruby-lang.org).
