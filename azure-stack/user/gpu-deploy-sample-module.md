---
title: Grafikus processzorral (GPU) rendelkező IoT-modul üzembe helyezése Azure Stack központban
description: Grafikus processzorral (GPU) rendelkező IoT-modul üzembe helyezése Azure Stack központban
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/13/2020
ms.reviewer: gara
ms.lastreviewed: 11/13/2020
ms.openlocfilehash: 7a5a29c48533874cf2c859780ab0332b8410811f
ms.sourcegitcommit: 8526f642ef859b0006c3991d966f93608a87288a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2021
ms.locfileid: "98243476"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-hub"></a>GPU-t támogató IoT-modul üzembe helyezése Azure Stack központban

A GPU-t támogató Azure Stack hubhoz a processzor-igényes modulokat telepítheti a IoT Edge futtató Linux-eszközökre. A GPU-ra optimalizált virtuálisgép-méretek olyan speciális virtuális gépek, amelyek egy vagy több NVIDIA GPU-val érhetők el. Ebből a cikkből megtudhatja, hogyan használhatók a GPU-ra optimalizált virtuális gépek a nagy számítási igényű, a grafikus igényű és a vizualizációs feladatok futtatásához.

Mielőtt elkezdené, szüksége lesz egy Azure Active Directory (Azure AD) előfizetésre, amely hozzáféréssel rendelkezik a globális Azure-hoz és a Azure Stack hubhoz, egy Azure Container Registry (ACR) és egy IoT hubhoz.

Ebben a cikkben:
  - Telepítsen egy GPU-t támogató linuxos virtuális gépet, és telepítse a megfelelő illesztőprogramokat.
  - Telepítse a Docker-t, és engedélyezze a GPU-t a futtatókörnyezetben.
  - Kapcsolódjon a IoT-eszközhöz az iOT hub-hoz, és telepítse a iOT Marketplace modellből: `Getting started with GPUs` .
  - Az eszközt az Azure IoT Explorer használatával telepítheti és figyelheti egy helyi gépről.
  - És opcionálisan telepítheti és figyelheti az eszközt az Azure IoT bővítmény használatával a Visual Studio Code-ban.

## <a name="prerequisites"></a>Előfeltételek

A következő erőforrásokkal kell rendelkeznie a Azure Stack hub-példányban, a globális Azure-ban és a helyi fejlesztési gépen.

### <a name="azure-stack-hub-and-azure"></a>Azure Stack hub és az Azure

  - Egy előfizetést felhasználóként Azure Active Directory (Azure AD) használatával egy Azure Stack hub integrált rendszerében egy NVIDA GPU-val. A következő zsetonok működnek az iOT hub-val:
    - NCv3
    - NCas_v4

    További információ a Azure Stack hub GPU-ról: [Graphics Processing Unit (GPU) VM Azure stack hub-on](gpu-vms-about.md).
  - Globális Azure-előfizetés. Ha nem rendelkezik globális Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.
- Egy [Azure Container Registry (ACR)](/). Jegyezze fel az ACR bejelentkezési kiszolgálóját, a felhasználónevet és a jelszót.
-   Egy ingyenes vagy standard szintű [IoT hub](/azure/iot-hub/iot-hub-create-through-portal) a globális Azure-ban.

### <a name="a-development-machine"></a>Egy fejlesztői gép

-   A fejlesztési beállításoktól függően saját számítógépet vagy virtuális gépet is használhat. A fejlesztői gépnek támogatnia kell a beágyazott virtualizálás szolgáltatást. Ez a funkció szükséges a Docker, a jelen cikkben használt tároló motor futtatásához.

  - A fejlesztői gépnek a következő erőforrásokra lesz szüksége:
      - [Python 3.x](https://www.python.org/downloads/)
      - [Pip](https://pypi.org/project/pip/) a Python-csomagok telepítéséhez. Ez a Python-telepítéssel együtt települt. Ha a pip telepítve van, előfordulhat, hogy frissítenie kell a legújabb verzióra. A pip-et saját maga is frissítheti. Írja be a következőt: `pip install --upgrade pip` .
      - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest)
      - [Git](https://git-scm.com/downloads)
      - [Docker](https://docs.docker.com/get-docker/)
      - [Visual Studio Code](https://code.visualstudio.com/)
      - A Visual Studio Code-hoz készült [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
      - [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python-extension-pack) a Visual Studio Code-hoz

## <a name="register-an-iot-edge-device"></a>IoT Edge-eszköz regisztrálása

Használjon külön eszközt a IoT Edge eszköz üzemeltetéséhez. Egy különálló eszköz használata elválasztja a fejlesztői gép és IoT Edge eszköz pontosabban tükrözi az üzembe helyezési forgatókönyvet. 

IoT Edge-eszköz létrehozása az Azure-ban Linux rendszerű virtuális géppel:

1.  [Hozzon létre egy N sorozatú Linux Server rendszerű virtuális gépet](./azure-stack-quick-linux-portal.md) Azure stack hub-on. A kiszolgálóhoz tartozó összetevők telepítésekor a kiszolgáló SSH-n keresztül fog működni. További információ: [nyilvános SSH-kulcs használata](./azure-stack-dev-start-howto-ssh-public-key.md).

2.  [IoT Edge-eszköz létrehozása és regisztrálása](/azure/iot-edge/how-to-register-device)

## <a name="prepare-a-gpu-enabled-vm"></a>GPU-t támogató virtuális gép előkészítése

1. Telepítse a NVIDA GPU-illesztőprogramokat az N sorozatú Linux-kiszolgálóra a cikk lépéseit követve, [telepítse az NVIDIA GPU-illesztőprogramokat a Linux rendszerű n sorozatú virtuális gépekre](/azure/virtual-machines/linux/n-series-driver-setup).

    > [!NOTE]  
    > A szoftver telepítéséhez a bash parancssorát fogja használni. Jegyezze fel a parancsokat, mivel ugyanazokat a parancsokat fogja használni az illesztőprogramok telepítéséhez a Docker-t futtató tárolón a GPU-t támogató virtuális gépen

2.  Telepítse a legújabb IoT Edge futtatókörnyezetet az N sorozatú Linux-kiszolgálón Azure Stack hub-ban. Útmutatásért lásd: [a Azure IoT Edge futtatókörnyezet telepítése Debian-alapú Linux rendszereken](/azure/iot-edge/how-to-install-iot-edge-linux#install-the-latest-runtime-version)

## <a name="install-docker"></a>A Docker telepítése

Telepítse a Docker-t a GPU-t támogató virtuális gépre. A modult a IoT Edge piactéren fogja futtatni a virtuális gép egy tárolójában.

Telepítenie kell a Docker 19,02-es vagy újabb telepítését. A Docker Runtime mostantól támogatja az NVIDIA GPU-ket. Ha többet szeretne megtudni a Docker-beli GPU-k megismeréséről, tekintse meg a következő cikket: Docker docs, [Futásidejű beállítások a memóriával, a processzorokkal és a GPU](https://docs.docker.com/config/containers/resource_constraints/#gpu)-k.

### <a name="install-docker"></a>A Docker telepítése

A Docker-tárolók bárhol, a helyszíni adatközpontban, egy külső szolgáltatón vagy a felhőben, az Azure-on futhatnak. A Docker-rendszerkép tárolói natív módon futtathatók Linux és Windows rendszeren. A Windows-lemezképek azonban csak Windows-gazdagépeken és Linux-rendszerképeken futtathatók Linux-gazdagépeken és Windows-gazdagépeken (eddig egy Hyper-V Linux rendszerű virtuális gép használatával), ahol a gazdagép egy kiszolgálót vagy egy virtuális gépet jelent. További információ: [Mi az a Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined).

1. Csatlakozzon az N-sorozatú Linux-kiszolgálóhoz az SSH-ügyfél használatával.

1.  Frissítse az apt-indexet és-listát.

    ```bash  
    sudo apt-get update
    ```

1.  A meglévő csomagok új verzióinak beolvasása a gépen.

    ```bash  
    sudo apt-get upgrade
    ```

2.  Telepítse a Docker apt-tárházának hozzáadásához szükséges függőségeket.

    ```bash  
    sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
    ```

3.  Adja hozzá a Docker GPG-kulcsát.

    ```bash  
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

4.  Adja hozzá a Docker apt-tárházát.

    ```bash  
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

5.  Frissítse az apt indexet és a listát, és telepítse a Docker Community Editiont.

    ```bash  
    sudo apt-get update 
    sudo apt-get install docker-ce
    ```

6.  Ellenőrizze a telepítést a Docker-verzió ellenőrzésével.

    ```bash  
    docker -v
    ```

7. Tegye elérhetővé a GPU-forrásokat a Docker-ben.

    ```bash
    docker run -it --rm --gpus all ubuntu nvidia-smi
    ```

## <a name="get-the-item-from-the-marketplace"></a>Az elem beolvasása a piactéren

Térjen vissza a Azure Portalhoz, és adja hozzá a modellt a peremhálózati eszközéhez a iOT Marketplace használatával. Válassza ki a **piactér modult** a menüből. És keresse meg a kifejezést `Getting started with GPUs` , és kövesse az utasításokat a modul hozzáadásához.

Útmutatásért lásd: [eszköz kiválasztása és modulok hozzáadása](/azure/iot-edge/how-to-deploy-modules-portal#select-device-and-add-modules)

## <a name="enable-monitoring"></a>Monitorozás engedélyezése

1. Töltse le az [Azure IoT Explorer](/azure/iot-pnp/howto-use-iot-explorer)alkalmazást, és kapcsolódjon az alkalmazáshoz a IoT hub.

2. Válassza ki a IoT eszközt, és navigáljon a telemetria elemre a navigációs menüből.

3. A **Start** gombra kattintva megkezdheti a IoT Edge eszköz kimenetének figyelését.

![érvényes telepítés](media/gpu-deploy-sample-module/user-azure-iot-explorer-gpu.png)

## <a name="monitor-the-module-optional"></a>A modul figyelése (nem kötelező)  

1. A VS Code parancskatalógusában futtassa az **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: IoT Hub kiválasztása) parancsot.

2. Válassza ki a konfigurálni kívánt IoT Edge-eszközt tartalmazó előfizetést és IoT Hubot. Ebben az esetben válassza ki az Azure Stack Edge-eszköz telepítéséhez használt előfizetést, és válassza ki az Azure Stack Edge-eszközhöz létrehozott IoT Edge eszközt. Ez akkor fordul elő, ha a számítást a korábbi lépések Azure Portalján keresztül konfigurálja.

3. A VS Code Explorerben bontsa ki az Azure IoT Hub szakaszt. Az **eszközök** területen az Azure stack Edge-eszköznek megfelelő IoT Edge eszköznek kell megjelennie. 

    1. Válassza ki az eszközt, kattintson a jobb gombbal, majd válassza a **figyelés beépített esemény végpontja** lehetőséget.

    2. Nyissa meg az **eszközök > modulokat** , és ellenőrizze, hogy fut-e a **GPU-modul** .

    3. A VS Code terminálnak a Azure Stack Edge-eszköz figyelési kimenetének IoT Hubi eseményeit is meg kell jelenítenie.

    ![érvényes telepítés](media/gpu-deploy-sample-module/gpu-monitor-events-output.png)

    Láthatja, hogy a GPU által az azonos műveletek végrehajtásához szükséges idő (5000-es ismétlés) nem sokkal kisebb, mint a CPU esetében.

## <a name="next-steps"></a>Következő lépések

  - További információ a [grafikus processzor (GPU) virtuális gépről Azure stack hub-on](gpu-vms-about.md)

  - Tudjon meg többet a Azure Stack hub-ról, a Data Box Edgeről és az intelligens környezetről, [a számítástechnika jövőjéről: intelligens felhő és intelligens peremhálózat](https://azure.microsoft.com/overview/future-of-cloud)

  - További információ a hibrid felhőalapú alkalmazásokról: [hibrid felhőalapú megoldások](/hybrid/app-solutions/)