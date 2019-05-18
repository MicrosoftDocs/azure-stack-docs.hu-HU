---
title: Python-WebApp üzembe helyezése egy virtuális gépet az Azure Stackben |} A Microsoft Docs
description: Egy Python webes alkalmazás üzembe helyezése egy virtuális géphez az Azure Stackben.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: f37e963ad73a361f9d4cd5a6e68ec4213d5f32fb
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838321"
---
# <a name="deploy-a-python-web-app-to-a-vm-in-azure-stack"></a>Python-WebApp üzembe helyezése egy virtuális Gépet az Azure Stackben

Létrehozhat egy virtuális Gépet, a Python-webalkalmazás létrehozása az Azure Stack futtatásához. Ebben a cikkben állítson be egy kiszolgálót, konfigurálja a kiszolgálót, a Python-webalkalmazás üzemeltetéséhez, és az alkalmazás üzembe helyezése az Azure Stack.

Ebben a cikkben Python 3.x Flask virtuális környezetben futó Nginx-kiszolgálón.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gép az Azure Stackben utasításait követve [Linux virtuális gép üzembe helyezése az Azure Stackben webalkalmazás üzemeltetéséhez](azure-stack-dev-start-howto-deploy-linux.md).

2. A virtuális gép hálózati ablaktáblán győződjön meg arról, hogy elérhetők-e a következő portokat:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az a weblapok kiszolgálókról való küldéséhez használt protokoll. DNS-nevét vagy IP-cím az ügyfelek csatlakoznak a HTTP Protokollon keresztül. |
    | 443 | HTTPS | Hypertext Transfer Protocol biztonságos (HTTPS), amely szükséges a biztonsági tanúsítvány, és lehetővé teszi, hogy az információ titkosított továbbításába HTTP biztonságos verziója is. |
    | 22 | SSH | Secure Shell (SSH) nem titkosított hálózati protokoll biztonságos kommunikációhoz. Ezt a kapcsolatot használja az SSH-ügyfelet konfigurálja a virtuális Gépet, és az alkalmazás üzembe helyezéséhez. |
    | 3389 | RDP | Választható. A távoli asztal protokoll (RDP) lehetővé teszi, hogy egy távoli asztali kapcsolatot egy grafikus felhasználói felületet használja a gépén.   |
    | 5000, 8000 | Egyéni | A fejlesztés, a Flask webes keretrendszer által használt portokat. Üzemi kiszolgáló esetén irányíthatja a forgalmat a 80-as és 443-as porton keresztül. |

## <a name="install-python"></a>Telepítse a Pythont

1. Csatlakozás a virtuális gép az SSH-ügyfél használatával. Útmutatásért lásd: [a PuTTy SSH-n keresztüli csatlakozás](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).
2. A bash parancssorban a virtuális Gépen adja meg a következő parancsot:

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. A telepítés ellenőrzése. Miközben továbbra is csatlakozik a virtuális gép az SSH-munkamenetben, adja meg a következő parancsot:

    ```bash  
        python -version
    ```

3. [Az Nginx telepítése](https://www.nginx.com/resources/wiki/), egy egyszerű webkiszolgálót. Miközben továbbra is csatlakozik a virtuális gép az SSH-munkamenetben, adja meg a következő parancsot:

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. [A Git telepítése](https://git-scm.com), a széles körben elosztott verziókezelő és forráskód code system management (SCM). Miközben továbbra is csatlakozik a virtuális gép az SSH-munkamenetben, adja meg a következő parancsot:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása

1. Állítsa be a virtuális gépen a Git-tárházhoz. Miközben továbbra is csatlakozik a virtuális gép az SSH-munkamenetben, adja meg a következő parancsokat:

    ```bash  
       git clone https://github.com/mattbriggs/flask-hello-world.git
    
       cd flask-hello-world
    ```

2. Hozzon létre egy virtuális környezetet, és adja meg azt a csomagfüggőségek. Miközben továbbra is csatlakozik a virtuális gép az SSH-munkamenetben, adja meg a következő parancsokat:

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3. Nyissa meg az új kiszolgálóra. Megtekintheti a futó webalkalmazás.

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>A kiszolgáló frissítése

1. Csatlakozhat a virtuális gép az SSH-munkamenet. A kiszolgáló leállításához írja be a Ctrl + C billentyűkombinációt.

2. A következő parancsokat:

    ```bash  
    deactivate
    open the git repo
    git pull
    ```

3. Aktiválja a virtuális környezetet, és az alkalmazás elindításához:

    ```bash  
    source venv/bin/activate
    export FLASK_APP=application.py
    flask run -h 0.0.0.0
    ```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [fejlesztés az Azure Stackhez](azure-stack-dev-start.md).
- Ismerje meg [közös üzemelő példányok az Azure stack-beli iaas](azure-stack-dev-start-deploy-app.md).
- Ismerje meg, a Python programozási nyelvet és további források keresése Python: [Python.org](https://www.python.org).
