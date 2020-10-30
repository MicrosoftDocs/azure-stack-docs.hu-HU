---
title: A IoT Hub titkainak elforgatása Azure Stack hub erőforrás-szolgáltatón
description: Megtudhatja, hogyan forgathatja el a IoT Hub titkait Azure Stack hub erőforrás-szolgáltatón
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/20/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: f648aaf0b8a8d90ca8f4789cb6dd58bf5c9fb8c9
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93049776"
---
# <a name="how-to-rotate-secrets-for-iot-hub-on-azure-stack-hub"></a>A Azure Stack hub IoT Hub titkainak elforgatása

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

Ez a cikk bemutatja, hogyan forgathatja el a IoT Hub erőforrás-szolgáltató által használt titkokat.

## <a name="overview-and-prerequisites"></a>Áttekintés és előfeltételek

[!INCLUDE [prereqs](../includes/resource-provider-va-rotate-secrets-prereqs.md)]

## <a name="prepare-a-new-tls-certificate"></a>Új TLS-tanúsítvány előkészítése

[!INCLUDE [prepare](../includes/resource-provider-va-rotate-secrets-prepare.md)]

## <a name="rotate-secrets"></a>Titkok elforgatása

[!INCLUDE [rotate](../includes/resource-provider-va-rotate-secrets-rotate.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

A titkos elforgatást hibák nélkül kell végrehajtani. Ha az alábbi feltételek bármelyikét tapasztalja a felügyeleti portálon, [Nyisson meg egy támogatási kérést](azure-stack-manage-basics.md#where-to-get-support) :

   - Hitelesítési problémák, beleértve a IoT Hub erőforrás-szolgáltatóhoz való kapcsolódási problémákat.
   - Nem lehet frissíteni az erőforrás-szolgáltatót, vagy szerkeszteni a konfigurációs paramétereket.
   - A használati metrikák nem láthatók.
   - A számlák nem jönnek létre.
   - A biztonsági mentések nem történnek.

## <a name="next-steps"></a>További lépések

Az Azure Stack hub-infrastruktúra titkainak elforgatásával kapcsolatos részletekért látogasson el a [titkok Elforgatása Azure stack központban](azure-stack-rotate-secrets.md).