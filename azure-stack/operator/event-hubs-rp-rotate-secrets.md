---
title: A Event Hubs titkainak elforgatása Azure Stack hub erőforrás-szolgáltatón
description: Megtudhatja, hogyan forgathatja el a Event Hubs titkait Azure Stack hub erőforrás-szolgáltatón
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/15/2020
ms.reviewer: jfggdl
ms.lastreviewed: 10/15/2020
ms.openlocfilehash: fe238576814d1aae5d7469e9438f8921f912f0ea
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97349447"
---
# <a name="how-to-rotate-secrets-for-event-hubs-on-azure-stack-hub"></a>A Azure Stack hub Event Hubs titkainak elforgatása

Ez a cikk bemutatja, hogyan forgathatja el a Event Hubs erőforrás-szolgáltató által használt titkokat.

## <a name="overview-and-prerequisites"></a>Áttekintés és előfeltételek

[!INCLUDE [prereqs](../includes/resource-provider-va-rotate-secrets-prereqs.md)]

## <a name="prepare-a-new-tls-certificate"></a>Új TLS-tanúsítvány előkészítése

[!INCLUDE [prepare](../includes/resource-provider-va-rotate-secrets-prepare.md)]

## <a name="rotate-secrets"></a>Titkok elforgatása

[!INCLUDE [rotate](../includes/resource-provider-va-rotate-secrets-rotate.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

A titkos elforgatást hibák nélkül kell végrehajtani. Ha az alábbi feltételek bármelyikét tapasztalja a felügyeleti portálon, [Nyisson meg egy támogatási kérést](azure-stack-manage-basics.md#where-to-get-support) :

   - Hitelesítési problémák, beleértve a Event Hubs erőforrás-szolgáltatóhoz való kapcsolódási problémákat.
   - Nem lehet frissíteni az erőforrás-szolgáltatót, vagy szerkeszteni a konfigurációs paramétereket.
   - A használati metrikák nem láthatók.
   - A számlák nem jönnek létre.
   - A biztonsági mentések nem történnek.

## <a name="next-steps"></a>Következő lépések

Az Azure Stack hub-infrastruktúra titkainak elforgatásával kapcsolatos részletekért látogasson el a [titkok Elforgatása Azure stack központban](azure-stack-rotate-secrets.md).