---
title: Node. js-alkalmazás üzembe helyezése egy virtuális gépen Azure Stack hub-ban | Microsoft Docs
description: Node. js-alkalmazás üzembe helyezése Azure Stack hubhoz.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: b848678eab2b913331208e13882c3b8b125c51a8
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820593"
---
# <a name="deploy-a-nodejs-web-app-to-a-vm-in-azure-stack-hub"></a>Node. js-alapú webalkalmazás üzembe helyezése Azure Stack hub-beli virtuális gépen

Létrehozhat egy virtuális gépet (VM), amely egy Node. js-webalkalmazást futtat Azure Stack hub-ban. Ebben a cikkben egy kiszolgálót állít be, konfigurálja a kiszolgálót a Node. js-webalkalmazás üzemeltetéséhez, majd telepítse az alkalmazást Azure Stack hubhoz.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gépet Azure Stack hub-ban a [Linux rendszerű virtuális gép üzembe helyezése című témakör útmutatását követve Azure stack hub webalkalmazásának üzemeltetéséhez](azure-stack-dev-start-howto-deploy-linux.md).

2. A virtuálisgép-hálózat ablaktáblán győződjön meg arról, hogy a következő portok elérhetők:

    | Port | Protocol (Protokoll) | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az a protokoll, amelyet a weblapok kiszolgálókról történő kézbesítéséhez használtak. Az ügyfelek HTTP-n keresztül csatlakoznak a DNS-név vagy IP-cím használatával. |
    | 443 | HTTPS | A Hypertext Transfer Protocol Secure (HTTPS) a HTTP olyan biztonságos verziója, amelynek biztonsági tanúsítványra van szüksége, és lehetővé teszi az adatok titkosított átvitelét. |
    | 22 | SSH | A Secure Shell (SSH) egy titkosított hálózati protokoll a biztonságos kommunikációhoz. Ez a kapcsolódás egy SSH-ügyféllel a virtuális gép konfigurálásához és az alkalmazás üzembe helyezéséhez használható. |
    | 3389 | RDP | Választható. A RDP protokoll (RDP) lehetővé teszi, hogy a távoli asztali kapcsolat grafikus felhasználói felületet használjon a gépen.   |
    | 1337 | Egyéni | A Node. js által használt port. Üzemi kiszolgálók esetében a 80-es és a 443-os adatforgalmat irányítja át. |

## <a name="install-node"></a>Csomópont telepítése

1. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával. Útmutatásért lásd: [kapcsolat SSH-n keresztül a PuTTY használatával](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. A virtuális gépen a bash-parancssorba írja be a következő parancsot:

    ```bash  
      sudo apt install nodejs-legacy
    ```

2. [Telepítse a NPM](https://www.npmjs.com/), a Node. js-csomagok vagy-modulok csomagkezelő-kezelőjét. Az SSH-munkamenetben továbbra is csatlakozik a virtuális géphez, írja be a következő parancsot:

    ```bash  
       go version
    ```

3. [Telepítse a git](https://git-scm.com)-t, amely egy széles körben elosztott verzió-ellenőrző és forráskód-kezelési (SCM) rendszer. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsot:

    ```bash  
       sudo apt-get -y install git
    ```

3. Ellenőrizze a telepítést. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsot:

    ```bash  
       node -v
    ```

## <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása

1. Állítsa be a git-tárházat a virtuális gépen. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsokat:

    ```bash  
       git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
    
       cd nodejs-docs-hello-world
        npm start
    ```

2. Indítsa el az alkalmazást. Ha továbbra is csatlakozik a virtuális géphez az SSH-munkamenetben, írja be a következő parancsot:

    ```bash  
       sudo node app.js
    ```

3. Nyissa meg az új kiszolgálót. Látnia kell a futó webalkalmazást.

    ```HTTP  
       http://yourhostname.cloudapp.net:1337
    ```

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure stack hub fejlesztéséről](azure-stack-dev-start.md).
- Ismerkedjen meg [az Azure stack hub általános telepítései IaaS](azure-stack-dev-start-deploy-app.md).
- A csomópont programozási nyelvének megismeréséhez és a csomópont további erőforrásainak megkereséséhez lásd: [NodeJS.org](https://nodejs.org).
