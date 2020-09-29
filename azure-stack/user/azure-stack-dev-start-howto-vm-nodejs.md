---
title: Node.js alkalmazás üzembe helyezése a virtuális gépen Azure Stack hub-ban
description: Node.js alkalmazás üzembe helyezése Azure Stack hubhoz.
author: mattbriggs
ms.topic: overview
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/20
ms.openlocfilehash: 72bd63e7f7c007a7c39c6150d0e2feac29b5d91d
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573989"
---
# <a name="deploy-a-nodejs-web-app-to-a-vm-in-azure-stack-hub"></a>Node.js webalkalmazás üzembe helyezése egy virtuális gépen Azure Stack hub-ban

Létrehozhat egy virtuális gépet (VM) Node.js webalkalmazás üzemeltetéséhez Azure Stack központban. Ebben a cikkben egy kiszolgálót állít be, konfigurálja a kiszolgálót a Node.js webalkalmazás üzemeltetéséhez, majd helyezze üzembe az alkalmazást az Azure Stack hub szolgáltatásban.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gépet Azure Stack hub-ban a [Linux rendszerű virtuális gép üzembe helyezése című témakör útmutatását követve Azure stack hub webalkalmazásának üzemeltetéséhez](azure-stack-dev-start-howto-deploy-linux.md).

2. A virtuálisgép-hálózat ablaktáblán győződjön meg arról, hogy a következő portok elérhetők:

    | Port | Protokoll | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az a protokoll, amelyet a weblapok kiszolgálókról történő kézbesítéséhez használtak. Az ügyfelek HTTP-n keresztül csatlakoznak a DNS-név vagy IP-cím használatával. |
    | 443 | HTTPS | A Hypertext Transfer Protocol Secure (HTTPS) a HTTP olyan biztonságos verziója, amelynek biztonsági tanúsítványra van szüksége, és lehetővé teszi az adatok titkosított átvitelét. |
    | 22 | SSH | A Secure Shell (SSH) egy titkosított hálózati protokoll a biztonságos kommunikációhoz. Ez a kapcsolódás egy SSH-ügyféllel a virtuális gép konfigurálásához és az alkalmazás üzembe helyezéséhez használható. |
    | 3389 | RDP | Választható. A RDP protokoll (RDP) lehetővé teszi, hogy a távoli asztali kapcsolat grafikus felhasználói felületet használjon a gépen.   |
    | 1337 | Egyéni | Az Node.js által használt port. Üzemi kiszolgálók esetében a 80-es és a 443-os adatforgalmat irányítja át. |

## <a name="install-node"></a>Csomópont telepítése

1. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával. Útmutatásért lásd: [kapcsolat SSH-n keresztül a PuTTY használatával](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. A virtuális gépen a bash-parancssorba írja be a következő parancsot:

    ```bash  
      sudo apt install nodejs-legacy
    ```

2. [Telepítse a NPM](https://www.npmjs.com/)-t, egy csomagkezelő Node.js csomagokat vagy modulokat. Az SSH-munkamenetben továbbra is csatlakozik a virtuális géphez, írja be a következő parancsot:

    ```bash  
       node --version
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
