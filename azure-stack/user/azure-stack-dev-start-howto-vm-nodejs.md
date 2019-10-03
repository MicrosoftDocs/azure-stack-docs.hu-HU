---
title: Node. js-alkalmazás üzembe helyezése egy virtuális gépen Azure Stackban | Microsoft Docs
description: Egy Node. js-alkalmazás üzembe helyezése Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 0b145ab315e855ee08b25ea4980bdde40d0bfc1c
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824198"
---
# <a name="deploy-a-nodejs-web-app-to-a-vm-in-azure-stack"></a>Node. js-alapú webalkalmazás üzembe helyezése Azure Stack-beli virtuális gépen

Létrehozhat egy virtuális gépet (VM) egy Node. js-webalkalmazás üzemeltetéséhez Azure Stackban. Ebben a cikkben egy kiszolgálót állít be, konfigurálja a kiszolgálót a Node. js-webalkalmazás üzemeltetéséhez, majd telepítse az alkalmazást Azure Stack.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gépet Azure Stack egy olyan [linuxos virtuális gép üzembe helyezése](azure-stack-dev-start-howto-deploy-linux.md)című témakör útmutatását követve, amely Azure stack a webalkalmazások üzemeltetésére.

2. A virtuálisgép-hálózat ablaktáblán győződjön meg arról, hogy a következő portok elérhetők:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az a protokoll, amelyet a weblapok kiszolgálókról történő kézbesítéséhez használtak. Az ügyfelek HTTP-n keresztül csatlakoznak a DNS-név vagy IP-cím használatával. |
    | 443 | HTTPS | A Hypertext Transfer Protocol Secure (HTTPS) a HTTP olyan biztonságos verziója, amelynek biztonsági tanúsítványra van szüksége, és lehetővé teszi az adatok titkosított átvitelét. |
    | 22 | SSH | A Secure Shell (SSH) egy titkosított hálózati protokoll a biztonságos kommunikációhoz. Ez a kapcsolódás egy SSH-ügyféllel a virtuális gép konfigurálásához és az alkalmazás üzembe helyezéséhez használható. |
    | 3389 | RDP | Nem kötelező. A RDP protokoll (RDP) lehetővé teszi, hogy a távoli asztali kapcsolat grafikus felhasználói felületet használjon a gépen.   |
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

## <a name="next-steps"></a>További lépések

- További információ a [Azure stack fejlesztéséről](azure-stack-dev-start.md).
- Ismerkedjen meg a [Azure stack IaaS való gyakori központi telepítésekkel](azure-stack-dev-start-deploy-app.md).
- A csomópont programozási nyelvének megismeréséhez és a csomópont további erőforrásainak megkereséséhez lásd: [NodeJS.org](https://nodejs.org).
