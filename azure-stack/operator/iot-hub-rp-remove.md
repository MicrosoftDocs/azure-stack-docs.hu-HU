---
title: IoT Hub eltávolítása Azure Stack hub-on
description: Útmutatás az IoT Hub Azure Stack hub-beli eltávolításához
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: 4095336f6a18441512e8c1052051d6e1608d904a
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93049933"
---
# <a name="how-to-remove-iot-hub-on-azure-stack-hub"></a>IoT Hub eltávolítása Azure Stack hub-on

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

Ez a cikk a Azure Stack hub IoT Hub erőforrás-szolgáltatójának eltávolításához nyújt útmutatást. Ez a folyamat körülbelül 37 percet vesz igénybe.

> [!WARNING]
> IoT Hub eltávolítása után a rendszer **_minden eszközt és adatsort töröl_** . A művelet **nem** helyreállítható.

## <a name="uninstalling-iot-hub"></a>IoT Hub eltávolítása

1) Nyissa meg a **Marketplace-kezelőt** . IoT Hub lesz a listában, és a rendszer telepítettként van megjelölve. Kattintson **IoT hub** .

    [![Erőforrás-szolgáltatói lista](../operator/media/iot-hub-rp-remove/uninstall1.png)](../operator/media/iot-hub-rp-remove/uninstall1.png#lightbox)

2) Kattintson a **IoT hub** alatt az **Eltávolítás** elemre, adja meg a **Microsoft. iothub** erőforrás-szolgáltató nevét, majd kattintson a alatta lévő **Eltávolítás** gombra.

    [![Távolítsa el IoT Hub és erősítse meg](../operator/media/iot-hub-rp-remove/uninstall2.png)](../operator/media/iot-hub-rp-remove/uninstall2.png#lightbox)

3) Várjon, amíg az Eltávolítás befejeződik. Mielőtt újra megpróbálja telepíteni a IoT Hubt, várjon legalább 10 percet.

>[!IMPORTANT]
>A függőségek (például Az Event hub **nem** lesz eltávolítva. Ha el kívánja távolítani/el szeretné távolítani a piactér függőségeit, külön kell elvégeznie.

## <a name="next-steps"></a>További lépések

Az újratelepítéshez lásd: [IoT hub erőforrás-szolgáltató telepítése csatlakoztatott Azure stack](iot-hub-rp-install.md).
