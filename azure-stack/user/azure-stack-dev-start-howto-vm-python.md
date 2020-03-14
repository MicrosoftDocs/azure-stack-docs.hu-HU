---
title: Python-webalkalmazás üzembe helyezése Azure Stack hub-beli virtuális gépen
description: Python-webalkalmazás üzembe helyezése Azure Stack hub virtuális gépén.
author: mattbriggs
ms.topic: overview
ms.date: 3/12/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 3/12/2020
ms.openlocfilehash: 700589e985aa651a1a7ae4a5ffbc020b8e6a2d3f
ms.sourcegitcommit: 4301e8dee16b4db32b392f5979dfec01ab6566c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79313010"
---
# <a name="deploy-a-python-web-app-to-a-vm-in-azure-stack-hub"></a>Python-webalkalmazás üzembe helyezése Azure Stack hub-beli virtuális gépen

Létrehozhat egy virtuális gépet, amely a Python-webalkalmazást Azure Stack hub-ban futtatja. Ebben a cikkben egy kiszolgálót állít be, konfigurálja a kiszolgálót a Python-webalkalmazás üzemeltetéséhez, majd telepítse az alkalmazást Azure Stack hubhoz.

Ez a cikk a Python 3. x verzióját használja egy virtuális környezetben egy Nginx-kiszolgálón.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gépet Azure Stack hub-ban a [Linux rendszerű virtuális gép üzembe helyezése című témakör útmutatását követve Azure stack hub webalkalmazásának üzemeltetéséhez](azure-stack-dev-start-howto-deploy-linux.md).

2. A virtuálisgép-hálózat ablaktáblán győződjön meg arról, hogy a következő portok elérhetők:

    | Port | Protokoll | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az a protokoll, amelyet a weblapok kiszolgálókról történő kézbesítéséhez használtak. Az ügyfelek HTTP-n keresztül csatlakoznak a DNS-név vagy IP-cím használatával. |
    | 443 | HTTPS | A Hypertext Transfer Protocol Secure (HTTPS) a HTTP olyan biztonságos verziója, amelynek biztonsági tanúsítványra van szüksége, és lehetővé teszi az adatok titkosított átvitelét. |
    | 22 | SSH | A Secure Shell (SSH) egy titkosított hálózati protokoll a biztonságos kommunikációhoz. Ez a kapcsolódás egy SSH-ügyféllel a virtuális gép konfigurálásához és az alkalmazás üzembe helyezéséhez használható. |
    | 3389 | RDP | Választható. A RDP protokoll (RDP) lehetővé teszi, hogy a távoli asztali kapcsolat grafikus felhasználói felületet használjon a gépen.   |
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
       git clone https://github.com/Azure-Samples/azure-stack-hub-flask-hello-world.git
    
       cd azure-stack-hub-flask-hello-world
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

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure stack hub fejlesztéséről](azure-stack-dev-start.md).
- Ismerkedjen meg [az Azure stack hub általános telepítései IaaS](azure-stack-dev-start-deploy-app.md).
- A Python programozási nyelvének megismeréséhez és a Python további erőforrásainak megtalálásához tekintse meg a következőt: [Python.org](https://www.python.org).
