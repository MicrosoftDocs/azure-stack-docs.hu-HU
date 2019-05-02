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
ms.openlocfilehash: b1a588dd084962c095a534f6569333b34e694bc6
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64481840"
---
# <a name="how-to-deploy-a-python-web-app-to-a-vm-in-azure-stack"></a>Python-WebApp üzembe helyezése egy virtuális Gépet az Azure Stackben

Létrehozhat egy virtuális Gépet, a Python webalkalmazás az Azure Stackben üzemeltetéséhez. Ez a cikk megvizsgálja a lépéseket követheti a kiszolgáló, a Python-webalkalmazás üzemeltetéséhez a kiszolgáló konfigurálása, és üzembe kell helyezni az alkalmazás beállítását.

Python-értelmezett, magas szintű, általános célú programozási nyelv. Béla van Rossum által létrehozott, és 1991 ben jelent meg először, a Python rendelkezik, amelyek kiemelik a kód olvashatóság érdekében a Tervező filozófia nevezetesen használatával jelentős elválasztó karakter. Biztosít, amelyek lehetővé teszik a kis- és nagy skálázását követve rugalmasan méretezhető egyértelmű programozási szerkezeteket. Ismerje meg, a Python programozási nyelvet és további források keresése Python: [Python.org](https://www.python.org).

Ez a cikk fogja használni a Python Flask Ngnix kiszolgálón virtuális környezetben futó 3.x.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gép, állítsa be az Azure Stackben. Kövesse a [Linux virtuális gép üzembe helyezése az Azure Stackben webalkalmazás üzemeltetéséhez](azure-stack-dev-start-howto-deploy-linux.md).

2. A virtuális gép hálózati panelen ellenőrizze, a következő portokat érhetők el:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az alkalmazásprotokoll elosztott, együttműködő, hipermédia információs rendszerekhez. Az ügyfelek a webalkalmazás nyilvános IP- vagy DNS-nevét, illetve a virtuális gép fog csatlakozni. |
    | 443 | HTTPS | Hypertext Transfer Protocol biztonságos (HTTPS) az kiterjesztése a Hypertext Transfer Protocol (HTTP). A számítógép hálózaton szolgál a biztonságos kommunikáció érdekében. Az ügyfelek a web Apps, és a nyilvános IP- vagy DNS-nevét, illetve a virtuális gép fog csatlakozni. |
    | 22 | SSH | Secure Shell (SSH) egy olyan titkosítási hálózati protokoll, a hálózati szolgáltatások biztonságos működő-nem biztonságos hálózaton keresztül. Az SSH-ügyfelet konfigurálja a virtuális Gépet, és az alkalmazás üzembe helyezéséhez használandó ehhez a kapcsolathoz. |
    | 3389 | RDP | Választható. A távoli asztali protokoll lehetővé teszi egy távoli asztali kapcsolatot egy grafikus felhasználói felületen a gép.   |
    | 5000, 8000 | Egyéni | A Flask webes keretrendszer fejlesztés alatt által használt portok 5000-es, a 8000-es. Üzemi kiszolgáló esetén célszerű továbbítani a forgalmat a 80-as és 443-as porton keresztül. |

## <a name="install-python"></a>Telepítse a Pythont

1. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával. Útmutatásért lásd: [a PuTTy SSH-n keresztüli csatlakozás](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
2. A bash parancssorban a virtuális Gépen írja be a következő parancsokat:

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. A telepítés ellenőrzése. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
        python -version
    ```


3. Telepítse az nginx-et. [Az Nginx](https://www.nginx.com/resources/wiki/) egy webkiszolgáló, amely fordított is használható, a terheléselosztó, levelezési proxy és HTTP-gyorsítótár. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. A Git telepítése. [A Git](https://git-scm.com) egy rendszer széles körben elterjedt változat és forráskód kód management (SCM). Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása

1. Állítsa be a virtuális gépen a Git-tárházhoz. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
       git clone https://github.com/mattbriggs/flask-hello-world.git
    
       cd flask-hello-world
    ```

2. Hozzon létre egy virtuális környezetet, és adja meg azt a csomagfüggőségek.  Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3.  Most nyissa meg az új kiszolgálóra, és megjelenik a futó webalkalmazás.

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>A kiszolgáló frissítése

1. Csatlakozhat a virtuális gép az SSH-munkamenet. Írja be a kiszolgáló leállítására `CTRL+C`.
2. Írja be a következő parancsokat:

    ```bash  
    deactivate
    open the git repo
    git pull
    ```

3. Aktiválja a virtuális környezetet, és indítsa el az alkalmazást

    ```bash  
    source venv/bin/activate
    export FLASK_APP=application.py
    flask run -h 0.0.0.0
    ```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [fejlesztés az Azure Stackhez](azure-stack-dev-start.md)
- Ismerje meg [közös üzemelő példányok az Azure stack-beli iaas](azure-stack-dev-start-deploy-app.md).
