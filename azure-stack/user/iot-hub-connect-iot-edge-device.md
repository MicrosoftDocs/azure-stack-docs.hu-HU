---
title: IoT Edge-eszköz csatlakoztatása a IoT Hubhoz Azure Stack hub-on
description: Megtudhatja, hogyan csatlakoztathat IoT Edge eszközt az Azure Stack hub-beli IoT Hubhoz.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/20/2020
ms.reviewer: dmolokanov
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 768952db6d93930f710dd80ea10e23ae6d8297a5
ms.sourcegitcommit: 0765de47f4a73e09192d34739e40c750b6e7abaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920689"
---
# <a name="connect-an-iot-edge-device-to-iot-hub-on-azure-stack-hub"></a>IoT Edge-eszköz csatlakoztatása a IoT Hubhoz Azure Stack hub-on

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

Ebből a cikkből megtudhatja, hogyan csatlakoztathat egy virtuális IoT Edge eszközt Azure Stack hub-on futó IoT Hubhoz. Ugyanazt az általános eljárást használhatja a fizikai eszköz a IoT Hubhoz való csatlakoztatásához.

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt végezze el a következő előfeltételeket:

- Szüksége lesz egy olyan fiókra, amely hozzáfér a [Azure stack hub felhasználói portálhoz](../user/azure-stack-use-portal.md), amely legalább ["közreműködői" engedélyekkel](../user/azure-stack-manage-permissions.md)rendelkezik.

- Működjön együtt a rendszergazdával [a IoT Hub Azure stack hub erőforrás-szolgáltatón való telepítéséhez](../operator/iot-hub-rp-overview.md). A telepítési lépések kiterjednek az IoT Hub szolgáltatást tartalmazó ajánlat létrehozására is. 

- Ha egy ajánlat elérhetővé válik, a rendszergazda létrehozhatja vagy frissítheti Azure Stack hub-előfizetését, hogy tartalmazza a IoT Hub. Másik lehetőségként [előfizethet az új ajánlatra, és létrehozhatja saját előfizetését](azure-stack-subscribe-services.md)is.

- Hasznos lehet a nyilvános kulcsú titkosítás (PKI) alapszintű ismerete. Pontosabban a hitelesítésszolgáltató (CA) és a X509-tanúsítványok használata a megbízhatósági lánc létrehozásához. Ez a megbízhatóság lehetővé teszi, hogy a hálózati csomópontok biztonságosan hitelesítsék és kommunikálhassanak egymással, például a IoT Hub szolgáltatással és IoT Edge eszközzel. 

## <a name="overview"></a>Áttekintés

Az alábbi összefoglalás részletesen ismertetjük, hogyan csatlakoztathat egy IoT Edge eszközt a IoT Hubhoz Azure Stack hub-on:

1. Hozzon létre IoT Hub és Linux rendszerű virtuális gép erőforrásait az Azure Stack hub-példányon. A Linux rendszerű virtuális gép virtuális IoT Edge eszközként fog szolgálni. 
2. Konfigurálja a IoT Edge eszközhöz szükséges tanúsítványokat. A szükséges lépések száma a IoT Hub legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát kiállító HITELESÍTÉSSZOLGÁLTATÓ típusától függ.
3. Konfigurálja a IoT Edge eszközhöz szükséges szoftvereket és szolgáltatásokat.
4. Tesztelje a IoT Edge eszközt, és győződjön meg arról, hogy megfelelően működik-e, és kommunikál-e a IoT Hubval.

A szkriptnek az egyes szakaszokban való futtatása előtt mindenképpen frissítse a parancsfájl-helyőrzőket, hogy megfeleljenek a környezet konfigurációjának. Jegyezze fel a használt értékeket, mivel a későbbi részekben szüksége lesz rájuk:

| Helyőrző | Példa | Leírás |
|-------------|---------|-------------|
| `<DEVICE-CA-CERT-NAME>` | `edged1cert`| Az IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát adja meg. |
| `<IOT-HUB-HOSTNAME>`| `test-hub-1.mgmtiothub.region.mydomain.com`| IoT Hub állomásneve. |
| `<IOT-HUB-ROOT-CA>`| `root.cer`| Az exportált IoT Hub legfelső szintű HITELESÍTÉSSZOLGÁLTATÓhoz megadott fájlnév (Ha privát HITELESÍTÉSSZOLGÁLTATÓT használ). |
| `<DATA-DIR>`| `/home/edgeadmin/edged1`| A Linux rendszerű virtuális gépen létrehozott adatkönyvtár teljes elérési útja. |
| `<USER-ACCOUNT>`| `edgeadmin`| A Linux rendszerű virtuális gépre való bejelentkezéshez használt fiók. |

## <a name="create-azure-stack-hub-resources"></a>Azure Stack hub-erőforrások létrehozása

Először létre kell hoznia a szükséges Azure Stack hub-erőforrásokat, beleértve a IoT Hub és a Linux rendszerű virtuális gépet is. Ezeket az erőforrásokat az [Azure stack hub felhasználói portál](../user/azure-stack-use-portal.md) használatával hozhatja létre.

### <a name="create-an-iot-hub-resource"></a>IoT Hub-erőforrás létrehozása

1. Jelentkezzen be az [Azure stack hub felhasználói portálra](../user/azure-stack-use-portal.md) egy olyan számítógépről, amely hozzáfér az Ön Azure stack hub-példányához.
2. Ha még nem hozott létre ilyet, kövesse az IoT hub létrehozása [IoT hub](/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub) létrehozása című szakasz utasításait a **Azure Portal használatával** IoT hub erőforrás létrehozásához. 

   > [!IMPORTANT]
   > Ügyeljen arra, hogy az [Azure stack hub felhasználói portált](../user/azure-stack-use-portal.md) használja a cikk lépéseit követve, és ne a Azure Portal. Azt is vegye figyelembe, hogy egyes képernyőképek és utasítások némileg eltérőek lehetnek, mivel nem minden Azure-szolgáltatás érhető el Azure Stack hub-on. 

### <a name="deploy-and-configure-a-linux-vm"></a>Linux rendszerű virtuális gép üzembe helyezése és konfigurálása 

Ebben a szakaszban egy új linuxos virtuális GÉPET telepít, amely virtuális IoT Edge eszközként fog szolgálni:

1. A [Linux Server-alapú virtuális gép létrehozása az Azure stack hub Portalon](azure-stack-quick-linux-portal.md) gyors üzembe helyezésével telepítsen egy Linux rendszerű virtuális gépet az Azure stack hub-példányra. Ügyeljen arra, hogy engedélyezze a port **SSH (22)** beállítást a **nyilvános bejövő portok kiválasztása** tulajdonságban, amikor a **Beállítások** lapra kerül. 

   > [!NOTE]
   > Csak az első 5 szakaszt kell végrehajtania, a **virtuális géphez való kapcsolódással**, mivel nem lesz szüksége az NGINX webkiszolgálóra. 

2. Miután létrehozta és csatlakoztatta a virtuális gépet a PuTTY használatával, hozzon létre egy alkönyvtárat a felhasználói fiókjának kezdőlapjára, például:

   ```bash
   mkdir edged1
   ```

3. Állítsa be a tanúsítvány-létrehozási eszközt az alábbi módszerek egyikével. Vagy letölti a fájlokat a IoT Edge GitHub-tárházból, amely az eszközök tanúsítványainak generálásához később szükséges:

   **CURL-parancsfájl használata:**
   ```bash
   # Navigate to the edge device data directory
   cd <DATA-DIR>
   
   # Download certGen.sh script file, and openssl_root_ca.cnf config file
   curl -Lo certGen.sh https://raw.githubusercontent.com/Azure/iotedge/master/tools/CACertificates/certGen.sh
   curl -Lo openssl_root_ca.cnf https://raw.githubusercontent.com/Azure/iotedge/master/tools/CACertificates/openssl_root_ca.cnf
   ```
   **Az adattár klónozása a git használatával:**
   
   ```bash
   # Navigate to the home directory
   cd /home/<USER-ACCOUNT>
   
   # Clone the iotedge repo, which contains the certGen.sh script file and openssl_root_ca.cnf config file
   git clone https://github.com/Azure/iotedge.git
   
   # Navigate to the edge device data directory
   cd <DATA-DIR>
   
   # Copy certGen.sh and openssl_root_ca.cnf into your working directory
   cp /home/<USER-ACCOUNT>/iotedge/tools/CACertificates/*.cnf .
   cp /home/<USER-ACCOUNT>/iotedge/tools/CACertificates/certGen.sh .
   ```

## <a name="configure-iot-edge-device-certificates"></a>IoT Edge eszköz tanúsítványainak konfigurálása

Ebben a szakaszban a virtuális IoT Edge eszköz által igényelt virtuálisgép-tanúsítvány konfigurációját kell végrehajtania. 

A IoT Hub szolgáltatás és a IoT Edge eszköz X509-tanúsítványokkal van védve. Mindkettőnek egy ugyanazon HITELESÍTÉSSZOLGÁLTATÓ által kiadott legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt kell használnia. Válassza ki az alábbi megfelelő lapot a tanúsítvány konfigurálásának befejezéséhez a IoT Hub által használt legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ típusa alapján.

# <a name="public-ca"></a>[Nyilvános HITELESÍTÉSSZOLGÁLTATÓ](#tab/public-ca)

[!INCLUDE [common-cert-config](../includes/iot-hub-connect-an-iot-edge-device-cert-common.md)]

# <a name="private-ca"></a>[Privát HITELESÍTÉSSZOLGÁLTATÓ](#tab/private-ca)

> [!IMPORTANT]
> Ha privát HITELESÍTÉSSZOLGÁLTATÓT használ, további lépésekre van szükség ahhoz, hogy a IoT Hub legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt a IoT Edge eszközre vigye át. Hajtsa végre ezeket a lépéseket ***csak** _, ha saját privát hitelesítésszolgáltatót használ a tanúsítvány generálásához. Ha a IoT Hub legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt egy nyilvános HITELESÍTÉSSZOLGÁLTATÓ adta ki, válassza az előző _ *nyilvános hitelesítésszolgáltató** lapot. 

### <a name="export-the-root-ca-certificate-from-your-iot-hub"></a>A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány exportálása a IoT Hub

Ha olyan gépet használ, amely hozzáféréssel rendelkezik a Azure Stack hubhoz, exportálja a IoT Hub legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt PEM formátumban. Az alábbi példa bemutatja, hogyan exportálhatja a tanúsítványt a [Microsoft Edge](https://www.microsoft.com/edge) vagy a [Google Chrome](https://www.google.com/chrome/index.html) böngésző használatával: 

   1. A IoT Hub **Áttekintés** lapján a **hostname (állomásnév** ) tulajdonság jobb oldalán található **másolás** gombra kattintva másolja a IoT hub-állomásnevet a vágólapra:  

      [![IoT Hub gazdagép neve](media\iot-hub-connect-an-iot-edge-device\copy-iot-hub-host-name.png)](media\iot-hub-connect-an-iot-edge-device\copy-iot-hub-host-name.png#lightbox)
   1. Nyisson meg egy új fület a Microsoft Edge vagy a Google Chrome böngészőben, írja be `https://` a parancsot, illessze be a IoT hub hostname fájlt az előző lépésben, majd nyomja le az **ENTER** billentyűt. 

   1. A hibaüzenet visszaadása után jelölje be a címsor bal oldalán található zárolás ikont, majd válassza a **tanúsítvány** elemet.
      [![tanúsítvány biztonságos kapcsolatai](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-connection.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-connection.png#lightbox)

   1. Az SSL/TLS-tanúsítvány megjelenítése után válassza a **minősítési útvonal** lapot. Ezután válassza ki a legfelső szintű tanúsítványt az elérési útban, majd kattintson a **Tanúsítvány megtekintése** gombra.  
      [![SSL-tanúsítvány biztonságos kapcsolata](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl.png#lightbox) 

   1. Amikor a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány látható, válassza a **részletek** lapot, majd a **Másolás fájlba.** .. gombra.
      [![tanúsítvány biztonságos kapcsolatai a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványhoz](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl-root-ca.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl-root-ca.png#lightbox) 

   1. Ha a **Tanúsítvány exportálása varázsló** megjeleníti, exportálja a tanúsítványt egy **Base-64 kódolású X. 509 formátumú** fájlba, például: **root. cer**. Ezt a fájlt a következő szakaszban fogja használni, ezért exportálja azt egy olyan helyre, amely elérhető a Linux rendszerű virtuális gépre, vagy onnan másolható.

   1. Az Exportálás sikeres befejezése után lezárhatja a böngésző fület.

### <a name="install-the-iot-hub-root-ca-certificate-on-the-iot-edge-device"></a>A IoT Hub legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány telepítése a IoT Edge eszközön

Most telepítse az előző szakaszban exportált IoT Hub legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt a IoT Edge eszköz megbízható tárolójába. 

1. Vigye át az előző szakaszban exportált IoT Hub legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI fájlt a IoT Edge eszközre. Mivel a Linux rendszerű virtuális gépet a IoT Edge eszközként használjuk, át kell másolnia a Linux rendszerű virtuális gépre. A környezettől függően érdemes lehet a PuTTY-t használni a PSCP paranccsal, vagy a megnyert programot a fájl másolásához a Linux rendszerű virtuális gépre.
2. Futtassa a következő szkriptet a linuxos VM Putty-munkamenetből, ahol a IoT Hub legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI fájlt tárolta. A parancsfájl ellenőrzi, hogy a TLS-kapcsolatok sikeresek-e, és telepíti a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT a IoT Edge eszköz megbízható tárolójában:

   ```bash
   # Verify connection failed first
   # Expected response: Verify return code: 2 (unable to get issuer certificate)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443
   
   # Verify connection succeeded when root CA provided
   # Expected response: Verify return code: 0 (ok)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443 -CAfile <IOT-HUB-ROOT-CA>
   
   # Install root CA in the trusted store on Edge device
   sudo cp <IOT-HUB-ROOT-CA> /usr/local/share/ca-certificates/
   sudo update-ca-certificates
   
   # Verify connection succeeded even when no root CA provided
   # Expected response: Verify return code: 0 (ok)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443
   ```

[!INCLUDE [common-cert-config](../includes/iot-hub-connect-an-iot-edge-device-cert-common.md)]

### <a name="append-the-iot-hub-root-ca-to-the-device-root-ca"></a>Az IoT Hub legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ hozzáfűzése az eszköz legfelső szintű HITELESÍTÉSSZOLGÁLTATÓhoz

Most fűzze hozzá az exportált és a IoT Edge eszközre másolt IoT Hub legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT a korábban létrehozott legfelső szintű HITELESÍTÉSSZOLGÁLTATÓhoz:

```bash
# Navigate to home directory
cd /home/<USER-ACCOUNT>

# Append IoT Hub root CA to the device root CA
cat <IOT-HUB-ROOT-CA> >> certs/azure-iot-test-only.root.ca.cert.pem
```
-----

## <a name="configure-iot-edge-device-software-and-services"></a>IoT Edge eszköz szoftverének és szolgáltatásainak konfigurálása

Ebben a szakaszban a virtuális IoT Edge eszköz által igényelt IoT Hub és virtuálisgép-konfigurációt kell végrehajtania.

### <a name="install-iot-edge-and-container-runtimes-on-the-vm"></a>IoT Edge-és tároló-futtatókörnyezetek telepítése a virtuális gépen

1. Térjen vissza a virtuális gép Putty-munkamenetéhez, és futtassa a következő szkriptet a Microsoft-kulcs és a szoftver tárház-hírcsatorna regisztrálásához:

   ```bash
   # Navigate to the home directory
   cd /home/<USER-ACCOUNT>

   # Install the repository configuration. 
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   
   # Copy the generated list.
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   
   # Install Microsoft GPG public key.
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

2. A tároló futtatókörnyezetének telepítése:

   ```bash
   sudo apt-get update
   sudo apt-get install moby-engine moby-cli
   ```

3. Telepítse a Azure IoT Edge biztonsági démont:

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

> [!NOTE]
> Figyelmen kívül hagyhatja a **Fontos: frissítse a konfigurációs fájlra** vonatkozó értesítést most, ahogy azt egy későbbi szakaszban is frissíti.

### <a name="create-an-iot-edge-device-in-iot-hub"></a>IoT Edge-eszköz létrehozása IoT Hub

1. Térjen vissza az [Azure stack hub felhasználói portálra](../user/azure-stack-use-portal.md), és navigáljon a **IoT hub** szolgáltatáshoz.

2. Válassza ki a IoT Hub erőforrást, majd válassza ki a **IoT Edge** lapot, majd **vegyen fel egy IoT Edge eszközt**.

   [![Erőforrás IoT Hub](media\iot-hub-connect-an-iot-edge-device\add-an-iot-edge-device.png)](media\iot-hub-connect-an-iot-edge-device\add-an-iot-edge-device.png#lightbox)

3. Az **eszköz létrehozása** lapon adja meg az **eszköz azonosítóját**, például: "edged1".
4. Ha elkészült, válassza a **Mentés** lehetőséget.

   [![IoT Edge – eszköz létrehozása](media\iot-hub-connect-an-iot-edge-device\create-iot-edge-device.png)](media\iot-hub-connect-an-iot-edge-device\create-iot-edge-device.png#lightbox)

5. Amikor a portál visszatér a **IoT Edge** lapra, válassza ki az új eszközt az eszközök listából.

   [![IoT Edge eszközök megtekintése](media\iot-hub-connect-an-iot-edge-device\view-iot-edge-devices.png)](media\iot-hub-connect-an-iot-edge-device\view-iot-edge-devices.png#lightbox)

6. Az eszköz részletei lapon az **elsődleges kapcsolati sztring** jobb oldalán található **Másolás** gombra kattintva másolja a karakterláncot a vágólapra. A következő szakaszban a kapcsolatok karakterláncát fogja használni.

   [![IoT Edge – eszköz részletei](media\iot-hub-connect-an-iot-edge-device\iot-edge-device-details.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-device-details.png#lightbox)

### <a name="configure-the-virtual-iot-edge-device-on-the-vm"></a>Virtuális IoT Edge eszköz konfigurálása a virtuális GÉPEN

1. Térjen vissza a VM Putty-munkamenethez. A bash használatával nyissa meg a konfigurációs fájlt a nano IoT Edge eszközön:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

2. Keresse meg a `provisioning` tulajdonságot a Megjegyzés **# manuális kiépítés konfigurációjában**, a létesítési **mód és a beállítások** szakaszban, Frissítse a IoT Edge eszköz-kapcsolódási karakterláncot úgy, hogy lecseréli a `<ADD DEVICE CONNECTION STRING HERE>` helyőrzőt az előző szakasz vágólapra másolt kapcsolódási karakterlánccal:

   > [!NOTE]
   > A vágólap tartalmának Nanoba való beillesztéséhez nyomja le és tartsa lenyomva a **SHIFT** billentyűt, és kattintson a jobb gombbal az egér gombra. Vagy nyomja le egyszerre a **SHIFT** billentyűt, és **szúrja be** a kulcsokat. Ha a beillesztési művelet a kurzort a kapcsolódási karakterlánc jobb szélső végére irányítja, a **kezdőlapon** a bal szélső végére térhet vissza.

   ```yaml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

3. Keresse meg és írja vissza a `certificates` tulajdonságot a **tanúsítvány beállításai** szakaszban. Adja meg a megjegyzéseit, és cserélje le a fájl URI azonosítóit a korábban létrehozott három tanúsítvány elérési útjára, például:

   ```yaml
   certificates:
     device_ca_cert: "<DATA-DIR>/certs/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>-full-chain.cert.pem"
     device_ca_pk: "<DATA-DIR>/private/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>.key.pem"
     trusted_ca_certs: "<DATA-DIR>/certs/azure-iot-test-only.root.ca.cert.pem"
   ```

4. Ha elkészült, a `provisioning` és a `certificates` Tulajdonságok a következő válaszokhoz hasonlóan néznek ki:

   [![Nano Editor – kiépítési tulajdonság](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-connection-string.png)](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-connection-string.png#lightbox)

   [![Nano Editor – tanúsítványok tulajdonság](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-certificates.png)](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-certificates.png#lightbox)


5. Mentse és zárjuk be a fájlt a **CTRL**  +  **X**, majd az **Y** billentyű használatával, majd **írja be a gombot**.

6. Miután visszatért a bash-hoz, indítsa újra a démont:
   
   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="verify-a-successful-installation"></a>Sikeres telepítés ellenőrzése

1. A IoT Edge démon állapotának ellenõrzése:

   ```bash
   systemctl status iotedge
   ```

2. Az alábbihoz hasonló választ kell látnia, hogy az IoT Edge szolgáltatás fut és "aktív". Ha igen, ugorjon a #4 lépésre.

   [![IoT Edge szolgáltatás sikeresen fut](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-running.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-running.png#lightbox)

3. Ha a IoT Edge szolgáltatás nem sikerült:
   - A következő példához hasonlóan a "sikertelen" hibaüzenet jelenik meg: [ ![ IoT Edge szolgáltatás nem sikerült](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-failed.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-failed.png#lightbox)   
   - A hibák megoldásához a következőket teheti:
     - Ellenőrizze a démon naplóit:

         ```bash
         journalctl -u iotedge --no-pager --no-full
         ```
     - A hibaelhárítási eszköz futtatásával keresse meg a leggyakoribb konfigurációs és hálózati hibákat. Ebben a példában a rendszer helytelenül formázott `/etc/iotedge/config.yaml` fájlt észlel:

         ```bash
         sudo iotedge check
         ```

         [![IoT Edge szolgáltatás-ellenőrzési](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-check.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-check.png#lightbox)

         > [!NOTE]
         > A `$edgeHub` rendszermodul nem kötelező, és nincs telepítve az eszközre, amíg nem telepíti az első IoT Edge modult. Ilyen esetben `iotedge check` olyan hibát adhat vissza, amely azt jelzi, hogy `$edgeHub` a gazdagép kapcsolatának ellenőrzése során nem köthető portokhoz. Ez a hiba figyelmen kívül hagyható, ha nincs rá `$edgeHub` szükség a központi telepítésben.           

   - Ha úgy találja, hogy helytelen formátumú. YML fájlt a fenti példában látható módon, egyszerűen:
      - A javításához ismételje meg a [virtuális IoT Edge eszköz beállítása a virtuális gépen](#configure-the-virtual-iot-edge-device-on-the-vm) című témakör lépéseit. YML-fájl.
      - Ismételje meg a [sikeres telepítés ellenőrzésének](#verify-a-successful-installation)lépéseit.

4. Miután meghatározta, hogy a IoT Edge szolgáltatás sikeresen elindult-e, sorolja fel a futó modulokat. A szolgáltatás állapota a következő `edgeAgent` `running` :

   ```bash
   sudo iotedge list
   ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nem használja őket, térjen vissza az [Azure stack hub felhasználói portálra](../user/azure-stack-use-portal.md) , és törölje a korábban létrehozott virtuális gépet és IoT hub erőforrásokat.

## <a name="next-steps"></a>Következő lépések

A következő kiegészítő erőforrások:

- [Az Azure IoT Edge-futtatókörnyezet telepítése Debian-alapú Linux rendszereken](/azure/iot-edge/how-to-install-iot-edge-linux)
- [IoT Edge-eszköz konfigurálása transzparens átjáróként való működéshez](/azure/iot-edge/how-to-create-transparent-gateway)
- [Bemutató tanúsítvány létrehozása az IoT Edge-eszköz funkcióinak teszteléséhez](/azure/iot-edge/how-to-create-test-certificates)