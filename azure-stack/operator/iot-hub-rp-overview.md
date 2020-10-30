---
title: Az Azure Stack hub IoT Hub áttekintése
description: Ismerje meg a Azure Stack hub IoT Hub erőforrás-szolgáltatóját, valamint a IoT Hub Azure-ban futtatott verziójával kapcsolatos különbségeket.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: adf6635fbdcf62398218a8e6ad0ce061900e0059
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93049781"
---
# <a name="iot-hub-on-azure-stack-hub-overview"></a>Az Azure Stack hub IoT Hub áttekintése

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

A IoT Hub on Azure Stack hub lehetővé teszi hibrid IoT-megoldások létrehozását. A IoT Hub felügyelt szolgáltatás, amely központi üzenetsor a IoT-alkalmazás és az általa felügyelt eszközök közötti kétirányú kommunikációhoz. A IoT Hub on Azure Stack hub használatával IoT-megoldásokat hozhat létre a IoT-eszközök és egy helyszíni megoldási háttér közötti megbízható és biztonságos kommunikációhoz. 

## <a name="differences-between-azure-iot-hub-and-iot-hub-on-azure-stack-hub"></a>Az Azure IoT Hub és az Azure Stack hub IoT Hub közötti különbségek

| Funkció | Azure IoT Hub | IoT Hub Azure Stack központban |
|-|-|-|
| Méretezés | https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling | Az S2 és az S3 IoT hubok támogatottak|
| Szolgáltatás frissítése | Automatikus | Kézi |

## <a name="iot-hub-throttling"></a>IoT Hub szabályozás

Az Azure Stack hub S2 és S3 SKU-ban lévő IoT Hub sávszélesség-szabályozása és kvótája ugyanaz, mint az Azure-beli IoT Hub. További részletekért tekintse meg az [Azure IoT hub kvótákat és a szabályozást](/azure/iot-hub/iot-hub-devguide-quotas-throttling#quotas-and-throttling) ismertető témakört.

## <a name="next-steps"></a>További lépések

Az IoT Hub Azure Stack hubhoz való telepítésének előkészítéséhez tekintse át az [Előfeltételek](iot-hub-rp-prerequisites.md) dokumentumot.

Ha többet szeretne megtudni a csatlakoztatott és a leválasztott Azure Stack hub közötti különbségről, tekintse át [Azure stack kapcsolati modelljét](azure-stack-connection-models.md).
