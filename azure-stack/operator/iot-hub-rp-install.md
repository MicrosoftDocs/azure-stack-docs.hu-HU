---
title: A IoT Hub telepítése Azure Stack hubhoz
description: Ismerje meg, hogyan telepítheti a IoT Hub erőforrás-szolgáltatót az Azure Stack hub-ra.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 13bac36ffd7d771d1b5504067bfe1d679cdda5e6
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050004"
---
# <a name="how-to-install-iot-hub-on-azure-stack-hub"></a>A IoT Hub telepítése Azure Stack hubhoz

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

Ez a cikk bemutatja, hogyan töltheti le és telepítheti az IoT Hub erőforrás-szolgáltatót, így elérhetővé teheti az ügyfelek számára az előfizetést. IoT Hub telepítési folyamata körülbelül 2 órát vesz igénybe.

## <a name="download-iot-hub"></a>IoT Hub letöltése

<!-- ### Connected Scenario -->
::: zone pivot="state-connected"
Ha az Azure Stack hub hozzáférhet az Azure Marketplace-hez, kövesse az ebben a szakaszban ismertetett lépéseket a IoT Hub és a függőségek letöltéséhez és telepítéséhez. 

Egy csatlakoztatott telepítés IoT Hub letöltéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be Azure Stack hub felügyeleti portálján. 
2. Válassza ki a **piactér-felügyelet** elemet a bal oldalon, majd válassza az **erőforrás-szolgáltatók** lehetőséget, majd kattintson a **+ Hozzáadás az Azure-ból** lehetőségre.

    [![Marketplace RP-keresés](media/iot-hub-rp-install/marketplace-rp-add-from-azure.png)](media/iot-hub-rp-install/marketplace-rp-add-from-azure.png#lightbox)

3. Ha szükséges, szűrje a "IoT Hub" szűrőt, majd válassza ki a **IoT hub** csomagot.

    [![Piactér IoT Hub RP](../operator/media/iot-hub-rp-install/download1.png)](../operator/media/iot-hub-rp-install/download1.png#lightbox)

4. A **IoT hub** csomag lapon válassza a **Letöltés** lehetőséget.

    [![IoT Hub csomag részletei](../operator/media/iot-hub-rp-install/download2.png)](../operator/media/iot-hub-rp-install/download2.png#lightbox)

5. Várjon, amíg a csomag letöltése befejeződik. Az állapot megmutatja a **letöltést** , és akár 10 percet is igénybe vehet.

    [![IoT Hub csomag letöltési állapota](../operator/media/iot-hub-rp-install/download3.png)](../operator/media/iot-hub-rp-install/download3.png#lightbox)

6. A csomag letöltése után az állapot módosul, hogy ne legyen **telepítve** a piactér- **kezelő** lapon.

    [![A IoT Hub csomag letöltése nincs telepítve](../operator/media/iot-hub-rp-install/download4.png)](../operator/media/iot-hub-rp-install/download4.png#lightbox)
::: zone-end

<!-- ### Disconnected or partially connected scenario -->
::: zone pivot="state-disconnected"
A leválasztott vagy részben csatlakoztatott központi telepítés IoT Hub letöltéséhez először töltse le a csomagokat a helyi gépre. A letöltés befejezése után importálja őket a Azure Stack hub-példányba.

1. Ha még nem tette meg, kövesse a [Marketplace-elemek letöltése – leválasztott vagy részben csatlakoztatott forgatókönyvek](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected)című témakör utasításait. Itt letöltheti és futtathatja a Marketplace Syndication eszközt, amely lehetővé teszi a IoT Hub csomagok letöltését.
2. Miután a szindikált eszköz "Azure Marketplace-elemek" ablaka megnyílik, keresse meg és válassza a "IoT Hub" lehetőséget a szükséges csomagok letöltéséhez a helyi gépre.
3. A letöltés befejeződése után importálja a csomagokat a Azure Stack hub-példányba, és tegye közzé a piactéren.
::: zone-end

## <a name="install-iot-hub"></a>IoT Hub telepítése

A **piactér-kezelés** lapon kövesse az alábbi lépéseket a IoT hub csomag telepítéséhez:

1. Kattintson a **IoT hub** sorra, majd **indítsa el a telepítést** .

    [![A telepítésre váró IoT Hub RP](../operator/media/iot-hub-rp-install/install1.png)](../operator/media/iot-hub-rp-install/install1.png#lightbox)

2. Kattintson a **telepítés előfeltételei** elemre.

    [![Az IoT Hub RP telepítésének előfeltételei](../operator/media/iot-hub-rp-install/install2.png)](../operator/media/iot-hub-rp-install/install2.png#lightbox)

3. A telepítés állapota nyomon követhető az értesítési ablaktáblán. Ez a lépés körülbelül 10 percet vesz igénybe.

    [![A IoT Hub RP telepítésének előfeltételei](../operator/media/iot-hub-rp-install/install3.png)](../operator/media/iot-hub-rp-install/install3.png#lightbox)

4. Várjon, amíg az Előfeltételek telepítése befejeződik. Ez a lépés általában a környezettől függően 5-10 percet vesz igénybe.

    [![IoT Hub RP titkokat készít](../operator/media/iot-hub-rp-install/install4.png)](../operator/media/iot-hub-rp-install/install4.png#lightbox)

5. Kattintson a **tanúsítványok hozzáadása** elemre a **titkok előkészítése** területen. Adja meg az előfeltételek között létrehozott pfx-tanúsítványt.

    [![IoT Hub RP – titkok előkészítése – tanúsítvány feltöltése](../operator/media/iot-hub-rp-install/install5.png)](../operator/media/iot-hub-rp-install/install5.png#lightbox)

6. Tallózzon a létrehozott pfx-fájlhoz, és adja meg a jelszót (a parancsfájl bemenete)

    [![IoT Hub RP – titkok előkészítése – tanúsítvány kiválasztása](../operator/media/iot-hub-rp-install/install6.png)](../operator/media/iot-hub-rp-install/install6.png#lightbox)

    [![IoT Hub RP titkokat készít – jelszó megadása](../operator/media/iot-hub-rp-install/install61.png)](../operator/media/iot-hub-rp-install/install61.png#lightbox)

7. Kattintson a **telepítés** elemre az **erőforrás-szolgáltató telepítése** alatt.

    [![IoT Hub RP-előkészítési titkok – kész](../operator/media/iot-hub-rp-install/install7.png)](../operator/media/iot-hub-rp-install/install7.png#lightbox)

8. A telepítés megkezdése után az üzembe helyezés állapota a piactéren vagy az értesítési panelen található.

    [![IoT Hub RP telepítés folyamatban](../operator/media/iot-hub-rp-install/install8.png)](../operator/media/iot-hub-rp-install/install8.png#lightbox)

9. A telepítés 90 – 120 percig is eltarthat. Várjon, amíg a telepítés befejeződik.

    [![IoT Hub RP telepítése befejeződött](../operator/media/iot-hub-rp-install/install91.png)](../operator/media/iot-hub-rp-install/install91.png#lightbox)

    [![Marketplace RPs – telepített RPs](../operator/media/iot-hub-rp-install/install92.png)](../operator/media/iot-hub-rp-install/install92.png#lightbox)

A IoT Hub erőforrás-szolgáltató telepítése sikerült! A kezdéshez hajtsa végre a következő lépéseket:

1. Ha szükséges, kövesse az utasításokat a [tervek, ajánlatok és előfizetések létrehozásához](azure-stack-plan-offer-quota-overview.md).

2. Ha már létezik előfizetés, frissítse a kapcsolódó ajánlatot/tervet a **Microsoft. Devices** szolgáltatás belefoglalásához. Nyissa meg a **terv**  ->  **elemet a**  ->  **szolgáltatás hozzáadása és a kvóta** frissítése érdekében.

3. Adja hozzá a **Microsoft. Devices szolgáltatást** , és kattintson a **Mentés** gombra.

    [![IoT Hub szolgáltatás hozzáadása a tervhez](../operator/media/iot-hub-rp-install/pd2.png)](../operator/media/iot-hub-rp-install/pd2.png#lightbox)

4. Minden be van állítva! A IoT hubok már létrehozhatók.

## <a name="using-iot-hub"></a>IoT Hub használata

Az IoT Hub használatának megismeréséhez tekintse meg az [Azure IoT hub dokumentációját](/azure/iot-hub).

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Stack hub IoT Hub kezeléséről, olvassa el az [IoT hub kezelése Azure stack hub-on](iot-hub-rp-manage.md)című témakört.
