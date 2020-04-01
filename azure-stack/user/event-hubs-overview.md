---
title: Az Azure Stack hub Event Hubs áttekintése
description: Ismerkedjen meg a Azure Stack hub Event Hubs erőforrás-szolgáltatóval.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/27/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: b0a4e18afdc8ce68ffa212bcc7bc78506420c235
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80424245"
---
# <a name="event-hubs-on-azure-stack-hub-overview"></a>Az Azure Stack hub Event Hubs áttekintése

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

A Event Hubs on Azure Stack hub lehetővé teszi a hibrid Felhőbeli forgatókönyvek megvalósítását. A folyamatos átviteli és eseményvezérelt megoldások támogatottak mind a helyszíni, mind az Azure-beli Felhőbeli feldolgozáshoz. Ha a forgatókönyv hibrid (csatlakoztatva) vagy le van választva, a megoldás képes támogatni az események/streamek nagy léptékű feldolgozását. A forgatókönyvet csak a Event Hubs-fürt mérete köti, amelyet az igényei szerint hozhat létre. 

## <a name="features"></a>Szolgáltatások 

A Event Hubs kiadásai (Azure Stack hub és az Azure-on) magas fokú szolgáltatási paritást kínálnak. Ez a paritás azt jelenti, hogy az SDK-k, minták, PowerShell, CLI és portálok hasonló élményt nyújtanak, néhány különbséggel. A következő táblázat összefoglalja, hogy a szolgáltatások rendelkezésre állása milyen magas szintű különbségeket tartalmaz a kiadások között.  

| Funkció | Event Hubs Azure Stack központban | Azure Event Hubs |
|-|-|-|-|
| Kezelői rendszergazdai élmény | ✔ | ✘ |
| Kafka-támogatás | ✔ | ✔ |
| Azonos SDK-készlet | ✔ | ✔ |
| Event Hubs hozzáférésének engedélyezése Azure Active Directory használatával | ✘ | ✔ |
| Funkció rögzítése | ✘ | ✔ |
| Geo-vészhelyreállítás | ✘ | ✔ |
| Azure Monitor | ✔ | ✔ |
| Funkció automatikus kivonása | ✘ | ✔ |

Emellett az Azure erőforrás-kezelési műveletei Azure Resource Manager sablonok, a [PowerShell](/powershell/module/azurerm.eventhub/)és az [Azure CLI](/cli/azure/eventhubs/eventhub/)használatával is elvégezhetők. Jelenleg a PowerShellben és az Azure CLI-ben a kezelői felügyeleti műveletek nem támogatottak.

## <a name="feature-documentation"></a>A szolgáltatás dokumentációja

Az [azure Event Hubs dokumentációja](/azure/event-hubs/) a Event Hubs mindkét kiadására vonatkozik: Event Hubs az Azure stack hub és az Azure Event Hubs. Ez a dokumentáció a Event Hubs és a tevékenységek, például a következők használatával kapcsolatos témaköröket tartalmazza:

- [Event Hubs fogalmak](/azure/event-hubs/event-hubs-features) részletei
- [Event Hubs-fürt és-névtér létrehozása](event-hubs-quickstart-cluster-portal.md)
- [Event hub](/azure/event-hubs/event-hubs-create#create-an-event-hub) létrehozása
- Stream [használata a Kafka protokoll használatával](/azure/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)

### <a name="operator-documentation"></a>Kezelői dokumentáció 
 
Ha többet szeretne megtudni a Event Hubs Azure Stack hub-kezelői felülettel kapcsolatban, tekintse meg a [Event Hubs operátor dokumentációját](/azure-stack/operator/event-hubs-rp-overview). Ez a dokumentáció a következő tevékenységekkel kapcsolatos információkat tartalmazza:

- Event Hubs telepítése.
- Event Hubs elérhetővé tétele a felhasználók számára.
- Információk beszerzése a szolgáltatás állapotáról.
- Naplók gyűjtése.


## <a name="next-steps"></a>Következő lépések

Ha Event Hubs nem érhető el az előfizetésben, akkor a rendszergazdával együttműködve [telepítse a Event Hubst Azure stack hub erőforrás-szolgáltatón](../operator/event-hubs-rp-overview.md).

Ha Event Hubs van telepítve, és készen áll a használatára, a szolgáltatással kapcsolatos további információkért tekintse meg a [Event Hubs dokumentációját](/azure/event-hubs/event-hubs-about) .
