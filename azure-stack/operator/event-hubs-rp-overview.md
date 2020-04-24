---
title: Az Azure Stack hub Event Hubs áttekintése
description: Ismerkedjen meg a Azure Stack hub Event Hubs erőforrás-szolgáltatóval.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 4a7e7987a5b5b7bb60e6acc882c07b80519bc052
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "80423948"
---
# <a name="event-hubs-on-azure-stack-hub-overview"></a>Az Azure Stack hub Event Hubs áttekintése

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

A Event Hubs on Azure Stack hub lehetővé teszi a hibrid Felhőbeli forgatókönyvek megvalósítását. A folyamatos átviteli és eseményvezérelt megoldások támogatottak mind a helyszíni, mind az Azure-beli Felhőbeli feldolgozáshoz. Ha a forgatókönyv hibrid (csatlakoztatva) vagy le van választva, a megoldás képes támogatni az események/streamek nagy léptékű feldolgozását. A forgatókönyv csak a fürt méretétől függ, amelyet az igényei szerint lehet kiépíteni. 

## <a name="features"></a>Szolgáltatások

Tekintse meg az [Azure stack hub felhasználói dokumentációját](/azure-stack/user/event-hubs-overview) a szolgáltatások összehasonlításához a Azure stack és az Azure Event Hubs Event Hubs között.

## <a name="feature-documentation"></a>A szolgáltatás dokumentációja

Ha többet szeretne megtudni a Event Hubs felhasználói élményről, tekintse meg az [Azure Event Hubs dokumentációját](/azure/event-hubs/). Ez a dokumentáció a Event Hubs mindkét kiadására vonatkozik, és olyan témákat tartalmaz, mint például:

- [Event Hubs fogalmak](/azure/event-hubs/event-hubs-features) részletei
- [Event Hubs-fürt és-névtér létrehozása](/azure/event-hubs/event-hubs-dedicated-cluster-create-portal)
- [Event hub](/azure/event-hubs/event-hubs-create#create-an-event-hub) létrehozása
- Stream [használata a Kafka protokoll használatával](/azure/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)


## <a name="next-steps"></a>További lépések

A telepítési folyamat megkezdése előtt tekintse át a [Azure stack Hub Event Hubs kapacitásának megtervezése](event-hubs-rp-capacity-planning.md)című ismertetőt. A kapacitás megtervezésének megismerése segít biztosítani, hogy a felhasználók rendelkezzenek a szükséges kapacitással.