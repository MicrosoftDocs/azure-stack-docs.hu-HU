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
ms.openlocfilehash: e788be6315078fccee020fefe6ad79a20485c382
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482104"
---
# <a name="how-to-deploy-a-java-web-app-to-a-vm-in-azure-stack"></a>Java-webalkalmazás üzembe helyezése egy virtuális Gépet az Azure Stackben

Létrehozhat egy virtuális Gépet, a Python webalkalmazás az Azure Stackben üzemeltetéséhez. Ez a cikk megvizsgálja a lépéseket követheti a kiszolgáló, a Python-webalkalmazás üzemeltetéséhez a kiszolgáló konfigurálása, és üzembe kell helyezni az alkalmazás beállítását.

Java az egyidejű, osztály-alapú, objektumorientált, és rendelkezik, néhány megvalósítás függőségek, a lehető készült általános célú számítógép-programozási nyelvet. Az, hogy a "egyszer írható, bárhol futtatható", az alkalmazásfejlesztők szolgál, amely a lefordított kódot futtathatja az összes olyan platformon, amely támogatja a Java jelölője nélkül Java jelentése. Ismerje meg, a Java programozási nyelv és további források keresése a Javához készült: [Java.com](https://www.java.com).

Ez a cikk részletesen ismerteti az telepítéséhez és a egy Apache Tomcat-kiszolgáló konfigurálása Linux rendszerű virtuális gépen az Azure Stack, és ezután betöltése egy Java webes alkalmazás erőforrás-(WAR-) fájlt a kiszolgálóra. WAR-fájl segítségével terjesztése JAR-fájlok, JavaServer Pages, a Java Servlet, a Java osztályok, XML fájlok, címke kódtárak, statikus weblapok (HTML- és a kapcsolódó fájlokat) és más erőforrások, amelyek együtt alkotják a webalkalmazás gyűjteménye.

Az Apache Tomcat, más néven Tomcat kiszolgálót, egy nyílt forráskódú Java-Szervlet tároló az Apache Software Foundation által fejlesztett. Tomcat számos Java EE-alapú vonatkozó előírásokat, beleértve a Java Servlet, a JavaServer Pages, a Java-EL és a WebSocket valósítja meg, és a környezetet biztosít a "tiszta Java" HTTP web server mely javában kódot futtathatja.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gép, állítsa be az Azure Stackben. Kövesse a [Linux virtuális gép üzembe helyezése az Azure Stackben webalkalmazás üzemeltetéséhez](azure-stack-dev-start-howto-deploy-linux.md).

2. A virtuális gép hálózati panelen ellenőrizze, a következő portokat érhetők el:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az alkalmazásprotokoll elosztott, együttműködő, hipermédia információs rendszerekhez. Az ügyfelek a webalkalmazás nyilvános IP- vagy DNS-nevét, illetve a virtuális gép fog csatlakozni. |
    | 443 | HTTPS | Hypertext Transfer Protocol biztonságos (HTTPS) az kiterjesztése a Hypertext Transfer Protocol (HTTP). A számítógép hálózaton szolgál a biztonságos kommunikáció érdekében. Az ügyfelek a web Apps, és a nyilvános IP- vagy DNS-nevét, illetve a virtuális gép fog csatlakozni. |
    | 22 | SSH | Secure Shell (SSH) egy olyan titkosítási hálózati protokoll, a hálózati szolgáltatások biztonságos működő-nem biztonságos hálózaton keresztül. Az SSH-ügyfelet konfigurálja a virtuális Gépet, és az alkalmazás üzembe helyezéséhez használandó ehhez a kapcsolathoz. |
    | 3389 | RDP | Választható. A távoli asztali protokoll lehetővé teszi egy távoli asztali kapcsolatot egy grafikus felhasználói felületen a gép.   |
    | 8080 | Egyéni | Az alapértelmezett port az Apache Tomcat szolgáltatás a 8080-as. Üzemi kiszolgáló esetén célszerű továbbítani a forgalmat a 80-as és 443-as porton keresztül. |

## <a name="install-java"></a>Telepítheti a javát

1. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával. Útmutatásért lásd: [a PuTTy SSH-n keresztüli csatlakozás](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
2. A bash parancssorban a virtuális Gépen írja be a következő parancsokat:

    ```bash  
        sudo apt-get install default-jdk
    ```

3. A telepítés ellenőrzése. Az SSH-munkamenetben, továbbra is csatlakozik a virtuális géphez, írja be a következő parancsokat:

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>Telepítse és konfigurálja a Tomcat

1. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával. Útmutatásért lásd: [a PuTTy SSH-n keresztüli csatlakozás](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).

2. Create Tomcat user.
    - Először hozzon létre egy új Tomcat-csoportot:
        ```bash  
            sudo groupadd tomcat
        ```
     
    - Második, hozzon létre egy új Tomcat felhasználót, és adja hozzá a felhasználó a tomcat csoport az egy kezdőkönyvtárát `/opt/tomcat`, ahol Tomcat telepíti, és az-shell, `/bin/false` (így senki sem be tud jelentkezni a fiók):
        ```bash  
            sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
        ```

3. Install Tomcat.
    - A Tomcat 8 letöltési oldalról, először megkapja az URL-címet a Tomcat 8 legújabb verziójának tar:  [http://tomcat.apache.org/download-80.cgi](http://tomcat.apache.org/download-80.cgi)
    - Második, a felhasználó curl hivatkozást használva a legújabb verzió letöltéséhez.

        ```bash  
            cd /tmp 
            curl -O <URL for the tar for the latest version of Tomcat 8>
        ```

    - A Tomcat harmadik, telepítse a `/opt/tomcat` könyvtár. A következő könyvtár létrehozásának, majd bontsa ki az archívumot, a következő parancsokkal:

        ```bash  
            sudo mkdir /opt/tomcat
            sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
            sudo chown -R tomcat webapps/ work/ temp/ logs/
        ```

4. A Tomcat engedélyeinek frissítése.

    ```bash  
        sudo chgrp -R tomcat /opt/tomcat
        sudo chmod -R g+r conf
        sudo chmod g+x conf
    ```

5. Hozzon létre egy `systemd` fájlja. így a Tomcat szolgáltatásként is futtathatja.

    - Tomcat tudnia kell, amelyen telepítve van a Java. Ezt az elérési utat gyakran nevezik **JAVA_HOME**. A hely megkereséséhez futtatásával:

        ```bash  
            sudo update-java-alternatives -l
        ```

        Ezzel biztosítható a következőhöz hasonló:

        ```Text  
            Output
            java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
        ```

        A **JAVA_HOME** változó értékét az elérési utat, a kimenetből véve, és vegye fel lehet létrehozni `/jre`. A fenti példa használata esetén például: `/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre`

    - Használja az értéket, a kiszolgáló létrehozása a systemd fájlt.

        ```bash  
            sudo nano /etc/systemd/system/tomcat.service
        ```

    - A szolgáltatás fájlba illessze be az alábbiakat. Módosítsa az értékét **JAVA_HOME** Ha kell egyeznie azzal az értékkel, található a rendszeren. Emellett érdemes CATALINA_OPTS megadott memória foglalási beállításainak módosításához:

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

    - Mentse és zárja be a fájlt.
    - Töltse be újra a systemd démon, így az tudni fogja a szolgáltatás fájllal kapcsolatos:

        ```bash  
            sudo systemctl daemon-reload
        ```

    - A Tomcat szolgáltatás elindítása 

        ```bash  
            sudo systemctl start tomcat
        ```

    - Győződjön meg arról, hogy indulása hibák nélkül beírásával:

        ```bash  
            ssudo systemctl status tomcat
        ```

6. Ellenőrizze a Tomcat kiszolgálót. Tomcat 8080-as portot használja a hagyományos kérelmek fogadásához. Erre a portra forgalom engedélyezése a következő parancs futtatásával:

    ```bash  
        sudo ufw allow 8080
    ```

    Ha még nem vett a **bejövőport-szabályok** az Azure Stack virtuális gép, majd adja hozzá azokat, most. További információkért lásd: [hozzon létre egy virtuális Gépet](#create-a-vm).

7. Egy böngészőben nyissa meg az Azure Stack ugyanazon a hálózaton, és nyissa meg a kiszolgálót: `yourmachine.local.cloudapp.azurestack.external:8080`

    ![Egy virtuális Gépet az Azure Stack az Apache Tomcat](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    Az Apache Tomcat oldal, a kiszolgálón betöltött. Ezután konfigurálhatja, hogy a kiszolgáló állapotát, Manager alkalmazás- és gazdagép-kezelő eléréséhez a kiszolgálón.

8. Engedélyezi a a fájlt, így a Tomcat automatikusan elindul, a kiszolgáló újraindításakor:

    ```bash  
        sudo systemctl enable tomcat
    ```

9. Tomcat kiszolgálót, hogy a webes kezelőfelület a hozzáférést konfigurálja. Szerkessze a `tomcat-users.xml` és a egy szerepkör és a felhasználó határozza meg, hogy a bejelentkezés. Adja meg a felhasználó elérheti a `manager-gui` és `admin-gui`.

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

    - Adja hozzá a következő elemeket az `<tomcat-users>` szakaszban:

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    - Ha például az utolsó fájl előfordulhat, hogy következőhöz hasonló:

    ```XML  
        <tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
         <role rolename="tomcat"/>
        <user username="tomcatuser" password="changemepassword" roles="tomcat,manager-gui,admin-gui"/>
        </tomcat-users>
    ```

    - Mentse és zárja be a fájlt.


10. Tomcat korlátozza a hozzáférést a **Manager** és **gazdagép Manager** a kiszolgálótól érkező kapcsolatok az alkalmazásokat. Tomcat virtuális gépen az Azure Stack telepíti, mivel ez a korlátozás feloldását célszerű. Ezek az alkalmazások, az IP-címkorlátozások módosítsa a megfelelő szerkesztésével `context.xml` fájlokat.

    - Frissítés `context.xml` a kezelő alkalmazást:

        ```bash  
            sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
        ```

    - Tegye megjegyzésbe az IP-címek korlátozása bárhonnan kapcsolatok engedélyezése, vagy adja hozzá az IP-cím a gép Tomcat való kapcsolódáshoz használ.

        ```XML  
        <Context antiResourceLocking="false" privileged="true" >
          <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                 allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
        </Context>
        ```

    - Mentse és zárja be a fájlt.

    - Frissítés `context.xml` az állomás Manager alkalmazást egy hasonló frissítése:

        ```bash  
            sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
        ```

    - Mentse és zárja be a fájlt.

11. Indítsa újra a Tomcat szolgáltatást, hogy a kiszolgáló frissítése a változások:

    ```bash  
        sudo systemctl restart tomcat
    ```

12. Egy böngészőben nyissa meg az Azure Stack ugyanazon a hálózaton, és nyissa meg a kiszolgálót: `yourmachine.local.cloudapp.azurestack.external:8080`.

    - Válassza ki a kiszolgáló állapota, tekintse át a Tomcat-kiszolgáló állapotát, és ellenőrizze, hogy rendelkezik hozzáféréssel.
    - Jelentkezzen be a Tomcat hitelesítő adataival.

![Egy virtuális Gépet az Azure Stack az Apache Tomcat](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>Alkalmazás létrehozása

Hozzon létre egy WAR Tomcat üzembe kell. Ha csak szeretné ellenőrizni a környezetben, például az Apache TomCat helyen War talál: [mintaalkalmazás](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/).

Az Azure-ban a Java-alkalmazások fejlesztésével kapcsolatos útmutatásért lásd: [létrehozása és üzembe helyezése az Azure-ban a Java-alkalmazások](https://azure.microsoft.com/develop/java/).

## <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása

1. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával. Útmutatásért lásd: [a PuTTy SSH-n keresztüli csatlakozás](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
1. A kiszolgáló frissítéséhez az alkalmazáscsomag a Tomcat szolgáltatás leállításához:

    ```bash  
        sudo systemctl stop tomcat
    ```

2.  Az FTP-felhasználók hozzáadása a tomcat csoporthoz úgy, hogy az írható-e a webapps mappát. Az FTP-felhasználó az a felhasználó a meghatározhatja, amikor a virtuális gép létrehozása az Azure Stackben.

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

3. Csatlakozzon a virtuális Gépet a webapps mappát törölje, és töltsön be az új vagy frissített WAR Filezillát. FileZila használatával kapcsolatos utasításokért lásd: [kapcsolódás az SFTP-Filezillát](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla).
    - Egyértelmű `TOMCAT_HOME/webapps`.
    - Adja hozzá a WAR való ` TOMCAT_HOME/webapps`, például `/opt/tomcat/webapps/`.

4.  Tomcat automatikusan oktatóanyagon, és központilag telepíti az alkalmazást. Megtekintheti a korábban létrehozott DNS-névvel. Példa:

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample

## Next steps

- Learn more about how to [Develop for Azure Stack](azure-stack-dev-start.md)
- Learn about [common deployments for Azure Stack as IaaS](azure-stack-dev-start-deploy-app.md).