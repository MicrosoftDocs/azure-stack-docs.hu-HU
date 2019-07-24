---
title: Java-háború üzembe helyezése virtuális gépen Azure Stackban | Microsoft Docs
description: Java-háború üzembe helyezése Azure Stack-beli virtuális gépen.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 28d60e8fc5b575cd2fbefee1298220418e4f59a1
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418238"
---
# <a name="deploy-a-java-web-app-to-a-vm-in-azure-stack"></a>Java-webalkalmazás üzembe helyezése egy virtuális gépen Azure Stack

Létrehozhat egy virtuális gépet (VM) a Python-webalkalmazás üzemeltetéséhez Azure Stackban. Ebben a cikkben egy Apache Tomcat-kiszolgálót telepít és konfigurál egy Linux rendszerű virtuális gépen Azure Stackon. Ezután betölt egy Java webalkalmazás-erőforrást (WAR-fájlt) a-kiszolgálóra. A WAR-fájlok a Java Archive-(JAR-) fájlok, a Java-erőforrásokat (például osztályokat, szövegeket, képeket, XML-fájlokat és HTML-ket, valamint a webalkalmazások kézbesítéséhez használt egyéb erőforrásokat) tartalmazó tömörített fájlok terjesztésére szolgálnak.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Állítsa be a virtuális gépet Azure Stack egy olyan [linuxos virtuális gép üzembe helyezése](azure-stack-dev-start-howto-deploy-linux.md)című témakör útmutatását követve, amely Azure stack a webalkalmazások üzemeltetésére.

2. A virtuálisgép-hálózat ablaktáblán győződjön meg arról, hogy a következő portok elérhetők:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az a protokoll, amelyet a weblapok kiszolgálókról történő kézbesítéséhez használtak. Az ügyfelek HTTP-n keresztül csatlakoznak a DNS-név vagy IP-cím használatával. |
    | 443 | HTTPS | A Hypertext Transfer Protocol Secure (HTTPS) a HTTP olyan biztonságos verziója, amelynek biztonsági tanúsítványra van szüksége, és lehetővé teszi az adatok titkosított átvitelét. |
    | 22 | SSH | A Secure Shell (SSH) egy titkosított hálózati protokoll a biztonságos kommunikációhoz. Ez a kapcsolódás egy SSH-ügyféllel a virtuális gép konfigurálásához és az alkalmazás üzembe helyezéséhez használható. |
    | 3389 | RDP | Választható. A RDP protokoll (RDP) lehetővé teszi, hogy a távoli asztali kapcsolat grafikus felhasználói felületet használjon a gépen.   |
    | 8080 | Egyéni | Az Apache Tomcat szolgáltatás alapértelmezett portja. Üzemi kiszolgálók esetében a 80-es és a 443-os adatforgalmat irányítja át. |

## <a name="install-java"></a>Java telepítése

1. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával. Útmutatásért lásd: [kapcsolat SSH-n keresztül a PuTTY használatával](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

2. Futtassa a következő parancsot a virtuális gépen a bash-parancssorban:

    ```bash  
        sudo apt-get install default-jdk
    ```

3. Ellenőrizze a telepítést. Az SSH-munkamenetben továbbra is csatlakozik a virtuális géphez, futtassa a következő parancsot:

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>A Tomcat telepítése és konfigurálása

1. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával. Útmutatásért lásd: [kapcsolat SSH-n keresztül a PuTTY használatával](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Hozzon létre egy tomcat-felhasználót a következő módon:

    a. Hozzon létre egy új tomcat-csoportot a következő parancs futtatásával:

    ```bash  
        sudo groupadd tomcat
    ```
     
    b. Hozzon létre egy új tomcat-felhasználót. Adja hozzá ezt a felhasználót a Tomcat-csoporthoz a */opt/tomcat*otthoni könyvtára alapján. A Tomcat üzembe helyezése erre a könyvtárba:

    ```bash  
        sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
    ```

1. A Tomcat a következő módon telepíthető:

    a. A Tomcat 8 [letöltési oldaláról](http://tomcat.apache.org/download-80.cgi)szerezze be a következőt: a szurokcsapda URL-címe a Tomcat legújabb verziójához.

    b. A cURL használatával töltse le a legújabb verziót a hivatkozással. Futtassa az alábbi parancsot:

    ```bash  
        cd /tmp 
        curl -O <URL for the tar for the latest version of Tomcat 8>
    ```

    c. Telepítse a Tomcatt a */opt/tomcat* könyvtárba. Hozza létre a mappát, majd nyissa meg az archívumot:

    ```bash  
        sudo mkdir /opt/tomcat
        sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
        sudo chown -R tomcat webapps/ work/ temp/ logs/
    ```

1. Frissítse a Tomcat engedélyeit a következő parancsok futtatásával:

    ```bash  
        sudo chgrp -R tomcat /opt/tomcat
        sudo chmod -R g+r conf
        sudo chmod g+x conf
    ```

1. Hozzon létre egy *rendszerszolgáltatási* fájlt, hogy a Tomcat szolgáltatásként is futtatható legyen.

   a. A Tomcat-nek tudnia kell, hogy hol telepítette a javát. Ezt az elérési utat általában *JAVA_HOME*-ként nevezzük. Keresse meg a helyet a futtatásával:

    ```bash  
        sudo update-java-alternatives -l
    ```

    Ez a következőhöz hasonló módon jön létre:

    ```Text  
        Output
        java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
    ```

    A *JAVA_HOME* változó értékét a kimenetből és a */JRE*hozzáadásával hozhatja létre. Például használja az előző példát, a */usr/lib/JVM/Java-1.8.0-OpenJDK-amd64/JRE*.

    b. Használja a kiszolgáló értékét a rendszerfájl létrehozásához:

    ```bash  
        sudo nano /etc/systemd/system/tomcat.service
    ```

    c. Illessze be a következő tartalmakat a szolgáltatás fájljába. Szükség esetén módosítsa a *JAVA_HOME*értékét úgy, hogy az megfeleljen a rendszeren talált értéknek. Előfordulhat, hogy módosítani szeretné a CATALINA_OPTS megadott memória-foglalási beállításokat is:

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

    e. Töltse be újra a rendszerszintű démont, hogy az tudja a szolgáltatás fájlját:

    ```bash  
        sudo systemctl daemon-reload
    ```

    f. A Tomcat szolgáltatás elindítása: 

    ```bash  
        sudo systemctl start tomcat
    ```

    g. A megadásával ellenőrizze, hogy hiba nélkül indította el a következőket:

    ```bash  
        sudo systemctl status tomcat
    ```

1. Ellenőrizze a Tomcat-kiszolgálót. A Tomcat a 8080-es portot használja a hagyományos kérések fogadására. A következő parancs futtatásával engedélyezheti a port forgalmát:

    ```bash  
        sudo ufw allow 8080
    ```

    Ha nem adta hozzá a Azure Stack virtuális géphez tartozó *bejövő portszabályok* adatait, adja hozzá őket. További információ: [virtuális gép létrehozása](#create-a-vm).

1. Nyisson meg egy böngészőt ugyanabban a hálózatban, mint a Azure Stack, majd nyissa meg a kiszolgálót, a *yourmachine. local. cloudapp. azurestack. external: 8080*.

    ![Apache Tomcat Azure Stack virtuális gépen](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    A kiszolgáló Apache Tomcat-lapja betöltődik. Ezután konfigurálja a kiszolgálót úgy, hogy a kiszolgáló állapotát, a Manager alkalmazást és a gazdagép-kezelőt is hozzáférhessen.

1. Engedélyezze a szolgáltatást, hogy a Tomcat automatikusan elindul a kiszolgáló újraindításakor:

    ```bash  
        sudo systemctl enable tomcat
    ```

1. A Tomcat-kiszolgáló konfigurálása a webkezelési felület elérésének engedélyezéséhez. 

   a. Szerkessze a *tomcat-users. XML* fájlt, és Definiáljon egy szerepkört és egy felhasználót, hogy be tudja jelentkezni. Adja meg a felhasználót a és `manager-gui` `admin-gui`a eléréséhez.

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

   b. Adja hozzá a következő elemeket `<tomcat-users>` a szakaszhoz:

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    A végső fájl például az alábbihoz hasonló lehet:

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

1. A Tomcat korlátozza a *kezelő* és a gazdagép- *kezelő* alkalmazásokhoz való hozzáférést a kiszolgálóról érkező kapcsolatokhoz. Mivel a Tomcat-t Azure Stack-beli virtuális gépen telepíti, el kell távolítania ezt a korlátozást. Módosítsa ezen alkalmazások IP-címének korlátozásait a megfelelő *Context. XML* fájlok szerkesztésével.

    a. A *Context. XML* frissítése a Manager alkalmazásban:

    ```bash  
        sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
    ```

    b. Az IP-cím korlátozásával engedélyezze a kapcsolatokat bárhonnan, vagy adja hozzá a használt gép IP-címét a Tomcat-hez való csatlakozáshoz.

    ```XML  
    <Context antiResourceLocking="false" privileged="true" >
        <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
    </Context>
    ```

    c. Mentse és zárja be a fájlt.

    d. Frissítse a *Context. xml fájlt* a Host Manager alkalmazáshoz hasonló frissítéssel:

    ```bash  
        sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
    ```

    e. Mentse és zárja be a fájlt.

1. Ha frissíteni szeretné a kiszolgálót a módosításokkal, indítsa újra a Tomcat szolgáltatást:

    ```bash  
        sudo systemctl restart tomcat
    ```

1. Nyisson meg egy böngészőt ugyanabban a hálózatban, mint a Azure Stack, majd nyissa meg a kiszolgálót: *yourmachine. local. cloudapp. azurestack. external: 8080*.

    a. A Tomcat-kiszolgáló állapotának áttekintéséhez és annak ellenőrzéséhez, hogy van-e hozzáférése, válassza a **kiszolgáló állapota**lehetőséget.

    b. Jelentkezzen be a Tomcat hitelesítő adataival.

    ![Apache Tomcat Azure Stack virtuális gépen](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>Alkalmazás létrehozása

Létre kell hoznia egy HÁBORÚt a Tomcat üzembe helyezéséhez. Ha csak az Ön környezetét szeretné megtekinteni, az [Apache Tomcat webhelyén](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/)talál egy példát.

A Java-alkalmazások Azure-ban való fejlesztésével kapcsolatos útmutatásért lásd: [Java-alkalmazások létrehozása és üzembe helyezése az Azure](https://azure.microsoft.com/develop/java/)-ban.

## <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása

1. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával. Útmutatásért lásd: [kapcsolat SSH-n keresztül a PuTTY használatával](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Ha szeretné frissíteni a kiszolgálót az alkalmazáscsomag használatával, állítsa le a Tomcat szolgáltatást:

    ```bash  
        sudo systemctl stop tomcat
    ```

1. Ahhoz, hogy írni lehessen a webapps mappába, adja hozzá az FTP-felhasználót a Tomcat-csoporthoz. Az FTP-felhasználó a virtuális gép Azure Stack való létrehozásakor megadott felhasználó.

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

1. A webapps mappa törléséhez és az új vagy frissített háború betöltéséhez kapcsolódjon a virtuális géphez a FileZilla használatával. Útmutatásért lásd: [az SFTP és a FileZilla közötti kapcsolat](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla).

    a. *TOMCAT_HOME/webapps*törlése.

    b. Vegye fel a HÁBORÚt a *TOMCAT_HOME/webapps* (például */opt/tomcat/webapps/* ).

1.  A Tomcat automatikusan kibontja és üzembe helyezi az alkalmazást. A nevet a korábban létrehozott DNS-név használatával tekintheti meg. Példa:

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample
    ```
    
## <a name="next-steps"></a>További lépések

- További információ a [Azure stack fejlesztéséről](azure-stack-dev-start.md).
- Ismerkedjen meg a [Azure stack IaaS való gyakori központi telepítésekkel](azure-stack-dev-start-deploy-app.md).
- A Java programozási nyelv megismeréséhez és a Java további erőforrásainak megkereséséhez tekintse meg a következőt: [Java.com](https://www.java.com).
