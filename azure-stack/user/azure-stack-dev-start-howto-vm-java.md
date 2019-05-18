---
title: Egy virtuális gépet az Azure Stack üzembe helyezése egy Java WAR |} A Microsoft Docs
description: Egy virtuális géphez az Azure Stack üzembe helyezése egy Java WAR.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 1738d106a0688518f7a739d3fb02ec1b16c2b8b9
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838367"
---
# <a name="deploy-a-java-web-app-to-a-vm-in-azure-stack"></a>Java-webalkalmazás üzembe helyezése egy virtuális Gépet az Azure Stackben

Létrehozhat egy virtuális gépet (VM), a Python-webalkalmazás létrehozása az Azure Stack futtatásához. Ebben a cikkben, telepítse és konfigurálja az Apache Tomcat kiszolgálót egy Linux rendszerű virtuális gépen az Azure Stackben. Ezután töltse be egy Java webes alkalmazás erőforrás-(WAR-) fájlt a kiszolgálóra. WAR-fájl segítségével terjesztése Java archív (JAR) fájlok, tömörített fájlok, amelyek tartalmazzák a Java-erőforrások, például az osztályok, szöveg, képek, XML és HTML, és más erőforrások, amelyek egy webes alkalmazás biztosításához.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gép az Azure Stackben utasításait követve [Linux virtuális gép üzembe helyezése az Azure Stackben webalkalmazás üzemeltetéséhez](azure-stack-dev-start-howto-deploy-linux.md).

2. A virtuális gép hálózati ablaktáblán győződjön meg arról, hogy elérhetők-e a következő portokat:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az a weblapok kiszolgálókról való küldéséhez használt protokoll. DNS-nevét vagy IP-cím az ügyfelek csatlakoznak a HTTP Protokollon keresztül. |
    | 443 | HTTPS | Hypertext Transfer Protocol biztonságos (HTTPS), amely szükséges a biztonsági tanúsítvány, és lehetővé teszi, hogy az információ titkosított továbbításába HTTP biztonságos verziója is. |
    | 22 | SSH | Secure Shell (SSH) nem titkosított hálózati protokoll biztonságos kommunikációhoz. Ezt a kapcsolatot használja az SSH-ügyfelet konfigurálja a virtuális Gépet, és az alkalmazás üzembe helyezéséhez. |
    | 3389 | RDP | Választható. A távoli asztal protokoll (RDP) lehetővé teszi, hogy egy távoli asztali kapcsolatot egy grafikus felhasználói felületet használja a gépén.   |
    | 8080 | Egyéni | Az alapértelmezett port az Apache Tomcat-szolgáltatás. Üzemi kiszolgáló esetén irányíthatja a forgalmat a 80-as és 443-as porton keresztül. |

## <a name="install-java"></a>Telepítheti a javát

1. Csatlakozás a virtuális gép az SSH-ügyfél használatával. Útmutatásért lásd: [a PuTTY SSH-n keresztüli csatlakozás](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

2. A bash parancssorban a virtuális Gépen futtassa a következő parancsot:

    ```bash  
        sudo apt-get install default-jdk
    ```

3. A telepítés ellenőrzése. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, futtassa a következő parancsot:

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>Telepítse és konfigurálja a Tomcat

1. Csatlakozás a virtuális gép az SSH-ügyfél használatával. Útmutatásért lásd: [a PuTTY SSH-n keresztüli csatlakozás](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. A Tomcat felhasználó létrehozásához az alábbiak szerint:

    a. Hozzon létre egy új Tomcat-csoportot a következő parancs futtatásával:

    ```bash  
        sudo groupadd tomcat
    ```
     
    b. Hozzon létre egy új Tomcat-felhasználót. Adja hozzá a felhasználót a Tomcat csoport egy kezdőkönyvtárát */opt/tomcat*. Ez a könyvtár Tomcat telepít:

    ```bash  
        sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
    ```

1. Tomcat telepítése az alábbiak szerint:

    a. URL beolvasása a Tomcat 8 legújabb verzióját a tar a [Tomcat 8 letöltési oldal](http://tomcat.apache.org/download-80.cgi).

    b. A cURL használatával töltse le a legújabb verzióra a hivatkozással. Futtassa az alábbi parancsot:

    ```bash  
        cd /tmp 
        curl -O <URL for the tar for the latest version of Tomcat 8>
    ```

    c. Telepítse a Tomcat a */opt/tomcat* könyvtár. Hozza létre a mappát, és nyissa meg az archívum:

    ```bash  
        sudo mkdir /opt/tomcat
        sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
        sudo chown -R tomcat webapps/ work/ temp/ logs/
    ```

1. A Tomcat engedélyeinek frissítése a következő parancsok futtatásával:

    ```bash  
        sudo chgrp -R tomcat /opt/tomcat
        sudo chmod -R g+r conf
        sudo chmod g+x conf
    ```

1. Hozzon létre egy *systemd* szolgáltatás fájlt, így Tomcat szolgáltatásként is futtathatja.

   a. Tomcat tudnia kell, amelyre telepítve van a Java. Ezt az elérési utat gyakran nevezik *JAVA_HOME*. A hely megkereséséhez futtatásával:

    ```bash  
        sudo update-java-alternatives -l
    ```

    Ez létrehozza a következőhöz hasonló:

    ```Text  
        Output
        java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
    ```

    Összeállíthatja a *JAVA_HOME* változó értékét, és vegye fel az elérési utat, a kimenetből véve */jre*. A fenti példa használata esetén például */usr/lib/jvm/java-1.8.0-openjdk-amd64/jre*.

    b. A systemd fájl létrehozásához használja az értéket a kiszolgálóról:

    ```bash  
        sudo nano /etc/systemd/system/tomcat.service
    ```

    c. A szolgáltatás fájlba illessze be az alábbiakat. Módosítsa az értékét *JAVA_HOME*, ha szükséges, az érték található a rendszeren megfelelően. Érdemes azt is, a foglalási memóriabeállításokat CATALINA_OPTS megadott módosítása:

    ```Text  
        [Unit]
        Description=Apache Tomcat Web Application Container
        After=network.target
        
        [Service]
        Type=forking
        
        Environment=JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre
        Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
        Environment=CATALINA_HOME=/opt/tomcat
        Environment=CATALINA_BASE=/opt/tomcat
        Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
        Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'
        
        ExecStart=/opt/tomcat/bin/startup.sh
        ExecStop=/opt/tomcat/bin/shutdown.sh
    
        User=tomcat
        Group=tomcat
        UMask=0007
        RestartSec=10
        Restart=always
        
        [Install]
        WantedBy=multi-user.target
    ```

    d. Mentse és zárja be a fájlt.

    e. Töltse be újra a systemd démon, így az tudni fogja a szolgáltatás fájllal kapcsolatos:

    ```bash  
        sudo systemctl daemon-reload
    ```

    f. A Tomcat szolgáltatás elindítása: 

    ```bash  
        sudo systemctl start tomcat
    ```

    g. Győződjön meg arról, hogy indulása hibák nélkül megadásával:

    ```bash  
        sudo systemctl status tomcat
    ```

1. Ellenőrizze a Tomcat kiszolgálót. Tomcat 8080-as portot használja a hagyományos kérelmek fogadásához. Erre a portra forgalom engedélyezése a következő parancs futtatásával:

    ```bash  
        sudo ufw allow 8080
    ```

    Ha még nem vett a *bejövőport-szabályok* az Azure Stack virtuális Gépen, vegye fel őket most. További információkért lásd: [hozzon létre egy virtuális Gépet](#create-a-vm).

1. Egy böngészőben nyissa meg az Azure Stack ugyanazon a hálózaton, és nyissa meg a kiszolgáló *yourmachine.local.cloudapp.azurestack.external:8080*.

    ![Egy virtuális Gépet az Azure Stack az Apache Tomcat](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    Az Apache Tomcat oldal, a kiszolgálón betöltött. Ezután állítsa be, hogy a kiszolgáló állapotát, Manager alkalmazás- és gazdagép-kezelő eléréséhez a kiszolgálón.

1. Engedélyezi a a fájlt, így a Tomcat automatikusan elindul, a kiszolgáló újraindításakor:

    ```bash  
        sudo systemctl enable tomcat
    ```

1. Hogy saját maga a webes kezelőfelület való hozzáférést, a Tomcat-kiszolgáló konfigurálása. 

   a. Szerkessze a *tomcat-users.xml* fájlt, és a egy szerepkör és a felhasználó határozza meg, hogy a bejelentkezés. Adja meg a felhasználó elérheti a `manager-gui` és `admin-gui`.

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

   b. Adja hozzá a következő elemeket az `<tomcat-users>` szakaszban:

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    Ha például a fájl végső következőhöz hasonló:

    ```XML  
        <tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
        <role rolename="tomcat"/>
        <user username="tomcatuser" password="changemepassword" roles="tomcat,manager-gui,admin-gui"/>
        </tomcat-users>
    ```

    c. Mentse és zárja be a fájlt.

1. Tomcat korlátozza a hozzáférést a *Manager* és *gazdagép Manager* a kiszolgálótól érkező kapcsolatok az alkalmazásokat. Tomcat virtuális gépen az Azure Stack telepítése, mert érdemes a korlátozás feloldását. Módosítsa az IP-címkorlátozások ezek az alkalmazások a megfelelő szerkesztésével *context.xml* fájlokat.

    a. Frissítés *context.xml* Manager alkalmazásban:

    ```bash  
        sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
    ```

    b. Tegye megjegyzésbe az IP-címek korlátozása bárhonnan kapcsolatok engedélyezése, vagy adja hozzá az IP-cím a gép Tomcat való kapcsolódáshoz használ.

    ```XML  
    <Context antiResourceLocking="false" privileged="true" >
        <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
    </Context>
    ```

    c. Mentse és zárja be a fájlt.

    d. Frissítés *context.xml* az állomás Manager alkalmazást egy hasonló frissítése:

    ```bash  
        sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
    ```

    e. Mentse és zárja be a fájlt.

1. A módosítások a kiszolgáló frissítéséhez indítsa újra a Tomcat szolgáltatást:

    ```bash  
        sudo systemctl restart tomcat
    ```

1. Egy böngészőben nyissa meg az Azure Stack ugyanazon a hálózaton, és nyissa meg a kiszolgáló: *yourmachine.local.cloudapp.azurestack.external:8080*.

    a. Tekintse át a Tomcat-kiszolgáló állapotát, és győződjön meg arról, hogy rendelkezik hozzáféréssel, válassza ki a **kiszolgáló állapota**.

    b. Jelentkezzen be a Tomcat hitelesítő adataival.

    ![Egy virtuális Gépet az Azure Stack az Apache Tomcat](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>Alkalmazás létrehozása

Hozzon létre egy WAR Tomcat üzembe kell. Ha csak szeretné ellenőrizni a környezetben, például WAR talál, a [Apache Tomcat hely](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/).

Az Azure-ban a Java-alkalmazások fejlesztésével kapcsolatos útmutatásért lásd: [létrehozása és üzembe helyezése az Azure-ban a Java-alkalmazások](https://azure.microsoft.com/develop/java/).

## <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása

1. Csatlakozás a virtuális gép az SSH-ügyfél használatával. Útmutatásért lásd: [a PuTTY SSH-n keresztüli csatlakozás](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Az alkalmazáscsomag a kiszolgáló frissítéséhez állítsa le a Tomcat szolgáltatást:

    ```bash  
        sudo systemctl stop tomcat
    ```

1. Tudni írni a webapps mappát, a Tomcat csoporthoz adja hozzá az FTP-felhasználó. Az FTP-felhasználó az a felhasználó, ha a virtuális gép létrehozása az Azure Stackben határoz meg.

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

1. A webapps mappát törölje, és töltsön be az új vagy frissített WAR, csatlakoznia kell a virtuális gép Filezillát. Útmutatásért lásd: [kapcsolódás az SFTP-Filezillát](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla).

    a. Egyértelmű *TOMCAT_HOME/webapps*.

    b. Adja hozzá a WAR való *TOMCAT_HOME/webapps* (például */opt/tomcat/webapps/*).

1.  Tomcat automatikusan oktatóanyagon, és központilag telepíti az alkalmazást. Megtekintheti a korábban létrehozott DNS-név használatával. Példa:

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample

## Next steps

- Learn more about how to [develop for Azure Stack](azure-stack-dev-start.md).
- Learn about [common deployments for Azure Stack as IaaS](azure-stack-dev-start-deploy-app.md).
- To learn the Java programming language and find additional resources for Java, see [Java.com](https://www.java.com).
