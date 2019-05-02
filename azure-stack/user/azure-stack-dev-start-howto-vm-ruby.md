---
title: Egy Ruby-alkalmazás üzembe helyezése egy virtuális géphez az Azure Stackben |} A Microsoft Docs
description: Egy Ruby-alkalmazás üzembe helyezése egy virtuális géphez az Azure Stackben.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 7744d1adcdcb1dde53c6ef887498a9a3978f4513
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64481939"
---
# <a name="how-to-deploy-a-ruby-web-app-to-a-vm-in-azure-stack"></a>Ruby-webalkalmazás üzembe helyezése egy virtuális Gépet az Azure Stackben

Létrehozhat egy Virtuálisgép-a Ruby-webalkalmazás létrehozása az Azure Stack futtatásához. Ez a cikk megvizsgálja a lépéseket követheti a kiszolgáló, a Ruby-webalkalmazás üzemeltetéséhez a kiszolgáló konfigurálása, és üzembe kell helyezni az alkalmazás beállítását.

A Ruby egy nyelv alapos egyenleg. A létrehozója, Yukihiro "Matz" Matsumoto, létesítéssel részek saját kedvenc nyelvén (Perl, Smalltalk, Eiffel, akadálymentes és Lisp), funkcionális programozási imperatív programozási az elosztott terhelésű új nyelv kialakításához. Ismerje meg, a Ruby programozási nyelvet és további források keresése Python: [Ruby-lang.org](https://www.ruby-lang.org).

Ez a cikk fogja használni a Ruby és a egy Ruby on Rails webalkalmazás keretrendszerben.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gép, állítsa be az Azure Stackben. Kövesse a [Linux virtuális gép üzembe helyezése az Azure Stackben webalkalmazás üzemeltetéséhez](azure-stack-dev-start-howto-deploy-linux.md).

2. A virtuális gép hálózati panelen ellenőrizze, a következő portokat érhetők el:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az alkalmazásprotokoll elosztott, együttműködő, hipermédia információs rendszerekhez. Az ügyfelek a webalkalmazás nyilvános IP- vagy DNS-nevét, illetve a virtuális gép fog csatlakozni. |
    | 443 | HTTPS | Hypertext Transfer Protocol biztonságos (HTTPS) az kiterjesztése a Hypertext Transfer Protocol (HTTP). A számítógép hálózaton szolgál a biztonságos kommunikáció érdekében. Az ügyfelek a web Apps, és a nyilvános IP- vagy DNS-nevét, illetve a virtuális gép fog csatlakozni. |
    | 22 | SSH | Secure Shell (SSH) egy olyan titkosítási hálózati protokoll, a hálózati szolgáltatások biztonságos működő-nem biztonságos hálózaton keresztül. Az SSH-ügyfelet konfigurálja a virtuális Gépet, és az alkalmazás üzembe helyezéséhez használandó ehhez a kapcsolathoz. |
    | 3389 | RDP | Választható. A távoli asztali protokoll lehetővé teszi egy távoli asztali kapcsolatot egy grafikus felhasználói felületen a gép.   |
    | 3000 | Egyéni | 3000 portot használja a Ruby-a-rails webes keretrendszer fejlesztés alatt. Üzemi kiszolgáló esetén célszerű továbbítani a forgalmat a 80-as és 443-as porton keresztül. |

## <a name="install-ruby"></a>A Ruby telepítése

1. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával. Útmutatásért lásd: [a PuTTy SSH-n keresztüli csatlakozás](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
1. Telepítse a PPA-val tárház. A bash parancssorban a virtuális Gépen írja be a következő parancsokat:

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. Telepítse a Ruby és Ruby rails, a virtuális Gépen. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. Telepítse a Ruby rails függőségeit. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!Note]  
    > A telepítés, előfordulhat, hogy ismételten futtatni szeretné `sudo gem install bundler`. Próbálja a függőségek telepítése és a sikertelen, tekintse át a hiba jelentkezik, és a problémák megoldásához.

4. A telepítés ellenőrzése. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
        ruby -v
    ```

3. A Git telepítése. [A Git](https://git-scm.com) egy rendszer széles körben elterjedt változat és forráskód kód management (SCM). Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>Hozzon létre és futtathat egy alkalmazást

1. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2.  Most nyissa meg az új kiszolgálóra, és megjelenik a futó webalkalmazás.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [fejlesztés az Azure Stackhez](azure-stack-dev-start.md)
- Ismerje meg [közös üzemelő példányok az Azure stack-beli iaas](azure-stack-dev-start-deploy-app.md).