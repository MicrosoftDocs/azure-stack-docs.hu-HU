---
title: Fiókiroda és Edge üzembe helyezése Azure Stack HCI-ben
description: Ez a témakör útmutatást nyújt a fiókirodai és Edge-forgatókönyvek megtervezéséhez, konfigurálásához és üzembe helyezéséhez az Azure Stack HCI operációs rendszeren.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 672a97a9804de324edde7c3802849a32ea44c0c4
ms.sourcegitcommit: dd34ae1c6207aafb5218c31658123e913f51bf7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98691044"
---
# <a name="deploy-branch-office-and-edge-on-azure-stack-hci"></a>Fiókiroda és Edge üzembe helyezése Azure Stack HCI-ben

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ez a témakör útmutatást nyújt a fiókirodai és Edge-forgatókönyvek megtervezéséhez, konfigurálásához és üzembe helyezéséhez az Azure Stack HCI operációs rendszeren. Az útmutató a szervezet számára összetett, magasan elérhető számítási feladatok futtatását helyezi üzembe a távoli fiókirodákban és a peremhálózati üzemelő példányokban lévő virtuális gépeken és tárolókban. Az Edge számítástechnikai szolgáltatás a legtöbb adatfeldolgozást egy központi rendszerről a hálózat peremére helyezi át, közelebb egy olyan eszközhöz vagy rendszerhez, amely az adatok gyors megadását igényli.

A Azure Stack HCI használatával virtualizált alkalmazásokat és munkaterheléseket futtathat a magas rendelkezésre állású ajánlott hardveren. A hardver támogatja a két kiszolgálóból álló fürtöket, amelyek beágyazott rugalmasságot biztosítanak a tároláshoz, egy egyszerű, kis teljesítményű USB-meghajtó-fürtöt, és az adminisztrációt a böngésző alapú Windows felügyeleti központban. Az USB-eszközök fürtjének létrehozásáról a tanúsító [fájlmegosztás központi telepítése](https://docs.microsoft.com/windows-server/failover-clustering/file-share-witness)című témakörben olvashat részletesen.

Azure IoT Edge áthelyezi a Felhőbeli elemzéseket és az egyéni üzleti logikát az eszközökre, így az adatkezelés helyett az üzleti elemzésekre koncentrálhat. Azure IoT Edge ötvözi a mesterséges intelligenciát, a felhőt és a peremhálózati számítástechnikai funkciókat a tároló Felhőbeli számítási feladatokban, például az Azure Cognitive Services, a Machine Learning, a Stream Analytics és a függvények esetében. A munkaterhelések a málna PI-ből egy átszervezett peremhálózat-kiszolgálóra is futtathatók. Az [Azure IoT hub](https://azure.microsoft.com/services/iot-hub) használatával felügyelheti az Edge-alkalmazásokat és-eszközöket.

Azure IoT Edge hozzáadása a Azure Stack HCI fiókirodához és az Edge üzemelő példányokhoz modernizálja a környezetet a [CI/CD-folyamat](https://docs.microsoft.com/azure/iot-edge/how-to-continuous-integration-continuous-deployment) alkalmazás-telepítési keretrendszerének támogatásához. A DevOps-munkatársak a hagyományos virtuálisgép-kezelési folyamatokon és eszközökön keresztül üzembe helyezhetik és megismétlik a ÁLTALa létrehozott és támogatott tároló alkalmazásokat.

A Azure IoT Edge elsődleges szolgáltatásai:
- Nyílt forráskódú szoftverek a Microsofttól
- Windows vagy Linux rendszeren fut
- A "on The Edge" futtatása a közel valós idejű válaszok biztosításához
- Biztonságos szoftver-és hardver-mechanizmusok
- Elérhető a [Azure IoT Edge AI-eszközkészletében](https://github.com/Azure/ai-toolkit-iot-edge)
- A programozási támogatás megnyitása a következőhöz: Java, .NET Core 2,0, Node.js, C és Python
- Offline és időszakos kapcsolat támogatása
- Natív felügyelet az Azure IoT Hub

További információ: [What is Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/about-iot-edge).

## <a name="deploy-branch-office-and-edge"></a>Fiókiroda és peremhálózat üzembe helyezése
Ez a szakasz részletesen ismerteti, hogyan vásárolhat hardvert a fiókirodák és a peremhálózati környezetek számára Azure Stack HCI-ben, és hogyan használhatja a Windows felügyeleti központot a felügyelethez. Emellett ismerteti Azure IoT Edge telepítését a Felhőbeli tárolók kezeléséhez.

### <a name="step-1-acquire-hardware-from-the-azure-stack-hci-catalog"></a>1. lépés: hardver beszerzése az Azure Stack HCI-katalógusból
Először be kell szereznie a hardvert. Ennek a legegyszerűbb módja, ha megkeresi a kívánt Microsoft-hardveres partnert a [Azure stack HCI-katalógusban](https://hcicatalog.azurewebsites.net) , és megvásárol egy integrált rendszert a Azure stack HCI operációs rendszerrel előre telepítve. A katalógusban szűrheti az ilyen típusú számítási feladatokhoz optimalizált szállítói hardvert.

Ellenkező esetben telepítenie kell a Azure Stack HCI operációs rendszert a saját hardverén. Az Azure Stack HCI telepítési lehetőségeivel és a Windows felügyeleti központ telepítésével kapcsolatos részletekért lásd: [a Azure stack HCI operációs rendszer](./operating-system.md)telepítése.

Ezután a Windows felügyeleti központban [hozzon létre egy Azure stack HCI-fürtöt](./create-cluster.md).

### <a name="step-2-set-up-azure-monitor-in-windows-admin-center"></a>2. lépés: a Azure Monitor beállítása a Windows felügyeleti központban
A Windows felügyeleti központban állítsa be Azure Monitor, hogy betekintést kapjon az Azure Stack HCI fiókiroda és a peremhálózati környezet alkalmazás-, hálózati és kiszolgálói állapotával.

További információ: [Azure stack HCI monitorozása Azure monitor](../manage/azure-monitor.md)használatával.

A Windows felügyeleti központ használatával további Azure Hybrid Services-szolgáltatásokat is beállíthat, például biztonsági mentést, File Sync, Site Recovery, pont – hely VPN-t, Update Managementt és Security Centert.

### <a name="step-3-use-container-based-apps-and-iot-data-processing"></a>3. lépés: a tároló-alapú alkalmazások és az IoT adatfeldolgozás használata
Most már készen áll a modern tároló-alapú alkalmazásfejlesztés és az IoT adatfeldolgozás használatára. A jelen szakaszban ismertetett lépések végrehajtásához használja a Windows felügyeleti központot Azure IoT Edge rendszert futtató virtuális gépek üzembe helyezéséhez.

További információ: [What is Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/about-iot-edge).

Azure IoT Edge üzembe helyezése Azure Stack HCI-ben:
1. [Hozzon létre egy új virtuális gépet Azure stack HCI-ben](https://docs.microsoft.com/windows-server/manage/windows-admin-center/use/manage-virtual-machines#create-a-new-virtual-machine)a Windows felügyeleti központban.

    További információ a támogatott operációsrendszer-verziókról, a virtuálisgép-típusokról, a processzor-architektúráról és a rendszerkövetelményekről: [Azure IoT Edge támogatott rendszerek](https://docs.microsoft.com/azure/iot-edge/support).

1. Ha még nem rendelkezik Azure-fiókkal, indítson el egy [ingyenes fiókot](https://azure.microsoft.com/free).
1. A Azure Portal [hozzon létre egy Azure IoT hubot](https://docs.microsoft.com/azure/iot-edge/quickstart#create-an-iot-hub).
1. Regisztrálja a Azure Portal [IoT Edge eszközét](https://docs.microsoft.com/azure/iot-edge/quickstart#register-an-iot-edge-device).

    >[!NOTE]
    > A IoT Edge eszköz egy Windows vagy Linux rendszerű virtuális gépen fut Azure Stack HCI-ben.

1. Az 1. lépésben létrehozott virtuális gépen [telepítse és indítsa el a IoT Edge futtatókörnyezetet](https://docs.microsoft.com/azure/iot-edge/quickstart#install-and-start-the-iot-edge-runtime).

   >[!IMPORTANT]
   > A futtatókörnyezet Azure IoT Hubhoz való csatlakoztatásához a 4. lépésben létrehozott eszköz sztringre van szükség.

1. [Modul üzembe helyezése](https://docs.microsoft.com/azure/iot-edge/quickstart#deploy-a-module) Azure IoT Edge.

    Az Azure Marketplace [IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) modules szakasza előre elkészített modulokat is kialakíthat és helyezhet üzembe.

## <a name="next-steps"></a>Következő lépések
A fiókirodával és az Edge-vel kapcsolatos további információkért és Azure IoT Edge a következő témakörben talál további információt:
- [Rövid útmutató: az első IoT Edge modul üzembe helyezése egy virtuális Linux-eszközön](https://docs.microsoft.com/azure/iot-edge/quickstart-linux?view=iotedge-2018-06&preserve-view=true)
- [Rövid útmutató: az első IoT Edge modul üzembe helyezése Windows-eszközön](https://docs.microsoft.com/azure/iot-edge/quickstart?view=iotedge-2018-06&preserve-view=true)
