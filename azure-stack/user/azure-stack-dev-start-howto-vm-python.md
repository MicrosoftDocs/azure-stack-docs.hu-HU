---
title: Python-webalkalmazás üzembe helyezése egy virtuális gépen Azure Stackban | Microsoft Docs
description: Egy Python-webalkalmazás üzembe helyezése Azure Stack-beli virtuális gépen.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 9fdb5767e225f9d5d8372058984c9128609339f1
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824228"
---
# <a name="deploy-a-python-web-app-to-a-vm-in-azure-stack"></a>Python-webalkalmazás üzembe helyezése Azure Stack-beli virtuális gépen

Létrehozhat egy virtuális gépet a Python-webalkalmazás üzemeltetéséhez Azure Stackban. Ebben a cikkben egy kiszolgálót állít be, konfigurálja a kiszolgálót a Python-webalkalmazás üzemeltetéséhez, majd telepítse az alkalmazást Azure Stack.

Ez a cikk a Python 3. x verzióját használja egy virtuális környezetben egy Nginx-kiszolgálón.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gépet Azure Stack egy olyan [linuxos virtuális gép üzembe helyezése](azure-stack-dev-start-howto-deploy-linux.md)című témakör útmutatását követve, amely Azure stack a webalkalmazások üzemeltetésére.

2. A virtuálisgép-hálózat ablaktáblán győződjön meg arról, hogy a következő portok elérhetők:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az a protokoll, amelyet a weblapok kiszolgálókról történő kézbesítéséhez használtak. Az ügyfelek HTTP-n keresztül csatlakoznak a DNS-név vagy IP-cím használatával. |
    | 443 | HTTPS | A Hypertext Transfer Protocol Secure (HTTPS) a HTTP olyan biztonságos verziója, amelynek biztonsági tanúsítványra van szüksége, és lehetővé teszi az adatok titkosított átvitelét. |
    | 22 | SSH | A Secure Shell (SSH) egy titkosított hálózati protokoll a biztonságos kommunikációhoz. Ez a kapcsolódás egy SSH-ügyféllel a virtuális gép konfigurálásához és az alkalmazás üzembe helyezéséhez használható. |
    | 3389 | RDP | Nem kötelező. A RDP protokoll (RDP) lehetővé teszi, hogy a távoli asztali kapcsolat grafikus felhasználói felületet használjon a gépen.   |
    | 5000, 8000 | Egyéni | A lombik webes keretrendszere által a fejlesztés során használt portok. Üzemi kiszolgálók esetében a 80-es és a 443-os adatforgalmat irányítja át. |

## <a name="install-python"></a>Telepítse a Pythont

1. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával. Útmutatásért lásd: [kapcsolat SSH-n keresztül a PuTTY használatával](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).
2. A virtuális gépen a bash-parancssorba írja be a következő parancsot:

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. Ellenőrizze a telepítést. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsot:

    ```bash  
        python -version
    ```

3. [Telepítse az Nginx](https://www.nginx.com/resources/wiki/)-et egy egyszerű webkiszolgálóként. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsot:

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. [Telepítse a git](https://git-scm.com)-t, amely egy széles körben elosztott verziókövetés és egy forráskód-kezelési (SCM) rendszer. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsot:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása

1. Állítsa be a git-tárházat a virtuális gépen. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsokat:

    ```bash  
       git clone https://github.com/mattbriggs/flask-hello-world.git
    
       cd flask-hello-world
    ```

2. Hozzon létre egy virtuális környezetet, és töltse fel az összes csomag-függőséggel. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsokat:

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3. Nyissa meg az új kiszolgálót. Látnia kell a futó webalkalmazást.

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>A kiszolgáló frissítése

1. Csatlakozzon a virtuális géphez az SSH-munkamenetben. A CTRL + C billentyűkombináció beírásával állítsa le a kiszolgálót.

2. Adja meg a következő parancsokat:

    ```bash  
    deactivate
    open the git repo
    git pull
    ```

3. Aktiválja a virtuális környezetet, és indítsa el az alkalmazást:

    ```bash  
    source venv/bin/activate
    export FLASK_APP=application.py
    flask run -h 0.0.0.0
    ```

## <a name="next-steps"></a>További lépések

- További információ a [Azure stack fejlesztéséről](azure-stack-dev-start.md).
- Ismerkedjen meg a [Azure stack IaaS való gyakori központi telepítésekkel](azure-stack-dev-start-deploy-app.md).
- A Python programozási nyelvének megismeréséhez és a Python további erőforrásainak megtalálásához tekintse meg a következőt: [Python.org](https://www.python.org).
