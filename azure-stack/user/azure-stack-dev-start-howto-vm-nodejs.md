---
title: Egy Node.js-alkalmazás üzembe helyezése egy virtuális géphez az Azure Stackben |} A Microsoft Docs
description: Egy Node.js-alkalmazás üzembe helyezése az Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: bebf3b349a994379d5f54bd387533b8d4a63ccdd
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64481928"
---
# <a name="how-to-deploy-a-nodejs-web-app-to-a-vm-in-azure-stack"></a>Node.js-webalkalmazás üzembe helyezése egy virtuális Gépet az Azure Stackben

Létrehozhat egy virtuális Gépet, a Node.js webalkalmazás az Azure Stackben üzemeltetéséhez. Ez a cikk megvizsgálja a lépéseket követheti a kiszolgáló, a csomópont webalkalmazás üzemeltetéséhez a kiszolgáló konfigurálása, és üzembe kell helyezni az alkalmazás beállítását.

NODE.js egy JavaScript futtatókörnyezet, amely a Chrome 8 JavaScript-motor. Egy aszinkron eseményvezérelt JavaScript futtatókörnyezet, mint a csomópont célja a méretezhető hálózati alkalmazásokat hozhat létre. További tudnivalók a programozási nyelv csomópont és további források keresése csomópont: [Nodejs.org](https://nodejs.org).

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gép, állítsa be az Azure Stackben. Kövesse a [Linux virtuális gép üzembe helyezése az Azure Stackben webalkalmazás üzemeltetéséhez](azure-stack-dev-start-howto-deploy-linux.md).

2. A virtuális gép hálózati panelen ellenőrizze, a következő portokat érhetők el:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az alkalmazásprotokoll elosztott, együttműködő, hipermédia információs rendszerekhez. Az ügyfelek a webalkalmazás nyilvános IP- vagy DNS-nevét, illetve a virtuális gép fog csatlakozni. |
    | 443 | HTTPS | Hypertext Transfer Protocol biztonságos (HTTPS) az kiterjesztése a Hypertext Transfer Protocol (HTTP). A számítógép hálózaton szolgál a biztonságos kommunikáció érdekében. Az ügyfelek a web Apps, és a nyilvános IP- vagy DNS-nevét, illetve a virtuális gép fog csatlakozni. |
    | 22 | SSH | Secure Shell (SSH) egy olyan titkosítási hálózati protokoll, a hálózati szolgáltatások biztonságos működő-nem biztonságos hálózaton keresztül. Az SSH-ügyfelet konfigurálja a virtuális Gépet, és az alkalmazás üzembe helyezéséhez használandó ehhez a kapcsolathoz. |
    | 3389 | RDP | Választható. A távoli asztali protokoll lehetővé teszi egy távoli asztali kapcsolatot egy grafikus felhasználói felületen a gép.   |
    | 1337 | Egyéni | A Node.js 1337 portot használja. Üzemi kiszolgáló esetén célszerű továbbítani a forgalmat a 80-as és 443-as porton keresztül. |

## <a name="install-node"></a>Csomópont telepítése

1. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával. Útmutatásért lásd: [a PuTTy SSH-n keresztüli csatlakozás](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
1. A bash parancssorban a virtuális Gépen írja be a következő parancsokat:

    ```bash  
      sudo apt install nodejs-legacy
    ```

2. Telepítse az npm-et. [Az NPM](https://www.npmjs.com/) egy Csomagkezelő, a Node.js-csomagokat vagy modulok. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
       go version
    ```

3. A Git telepítése. [A Git](https://git-scm.com) egy rendszer széles körben elterjedt változat és forráskód kód management (SCM). Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
       sudo apt-get -y install git
    ```

3. A telepítés ellenőrzése. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
       node -v
    ```

## <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása

1. Állítsa be a virtuális gépen a Git-tárházhoz. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
       git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
    
       cd nodejs-docs-hello-world
        npm start
    ```

2. Az alkalmazás elindításához. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsot:

    ```bash  
       sudo node app.js
    ```

3.  Most nyissa meg az új kiszolgálóra, és megjelenik a futó webalkalmazás.

    ```HTTP  
       http://yourhostname.cloudapp.net:1337
    ```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [fejlesztés az Azure Stackhez](azure-stack-dev-start.md)
- Ismerje meg [közös üzemelő példányok az Azure stack-beli iaas](azure-stack-dev-start-deploy-app.md).