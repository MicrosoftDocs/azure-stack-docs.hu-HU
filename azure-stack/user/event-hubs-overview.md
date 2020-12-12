---
title: Az Azure Stack hub Event Hubs áttekintése
description: Ismerkedjen meg a Azure Stack hub Event Hubsával. Megtudhatja, hogyan hozhat létre hibrid megoldásokat. Az Azure Event Hubs és a Event Hubs funkcióinak összehasonlítása Azure Stack hub-on.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/27/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: cb09f605f66e4950e0e483fbee920a1a4eba40e5
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97343762"
---
# <a name="overview-of-event-hubs-on-azure-stack-hub"></a>Az Azure Stack hub Event Hubs áttekintése

A Event Hubs on Azure Stack hub lehetővé teszi a hibrid Felhőbeli forgatókönyvek megvalósítását. A folyamatos átviteli és eseményvezérelt megoldások támogatottak mind a helyszíni, mind az Azure-beli Felhőbeli feldolgozáshoz. Ha a forgatókönyv hibrid (csatlakoztatva) vagy le van választva, a megoldás képes támogatni az események/streamek nagy léptékű feldolgozását. A forgatókönyvet csak a Event Hubs-fürt mérete köti, amelyet az igényei szerint hozhat létre. 

## <a name="run-event-processing-tasks-and-build-event-driven-applications-on-site"></a>Esemény-feldolgozási feladatok futtatása és eseményvezérelt alkalmazások készítése a helyen

Az Azure Stack hub Event Hubs használatával olyan üzleti forgatókönyveket valósíthat meg, mint például a következők:

- AI-és gépi tanulási munkaterhelések, ahol a Event Hubs az Event streaming Engine.
- Az Azure-adatközpontokon kívül saját webhelyein is megvalósíthat eseményvezérelt architektúrákat.
- Kattintássorozat-elemzések a helyszínen üzembe helyezett webalkalmazás (ok) hoz.
- Eszköz telemetria elemzése.
- A stream feldolgozása olyan nyílt forráskódú keretrendszerekkel, amelyek Apache Kafka, például a Apache Spark, a flink, a Storm és a Samza-t használják.
- [Számítási vendég operációs rendszer metrikáinak és eseményeinek felhasználása](azure-stack-metrics-monitor.md).

## <a name="build-hybrid-solutions"></a>Hibrid megoldások készítése

Olyan hibrid megoldásokat hozhat létre, amelyek helyileg töltik fel és dolgozzák fel a peremhálózat adatait az Azure Stack hub-on. Összesített adatokat küldhet az Azure-ba további feldolgozáshoz, vizualizációhoz és tároláshoz. Ha szükséges, használja ki a kiszolgáló nélküli számítástechnikai szolgáltatást az Azure-ban.

[![Hibrid megoldások diagramja](media/event-hubs-overview/hybrid-architecture-ehoash.png)](media/event-hubs-overview/hybrid-architecture-ehoash.png#lightbox)

## <a name="features"></a>Funkciók 

A Event Hubs kiadásai (Azure Stack hub és az Azure-on) magas fokú szolgáltatási paritást kínálnak. Ez a paritás azt jelenti, hogy az SDK-k, minták, PowerShell, CLI és portálok hasonló élményt nyújtanak, néhány különbséggel. A következő táblázat összefoglalja, hogy a szolgáltatások rendelkezésre állása milyen magas szintű különbségeket tartalmaz a kiadások között.  

| Funkció | Event Hubs az Azure Stack Hubon | Azure Event Hubs |
|-|-|-|-|
| Kezelői rendszergazdai élmény | ✔ | ✘ |
| Kafka-támogatás | ✔ | ✔ |
| Azonos SDK-készlet | ✔ | ✔ |
| Event Hubs hozzáférésének engedélyezése Azure Active Directory használatával | ✘ | ✔ |
| Funkció rögzítése | ✘ | ✔ |
| Geo-vészhelyreállítás | ✘ | ✔ |
| Azure Monitor | ✔ | ✔ |
| Funkció automatikus kivonása | ✘ | ✔ |

Az Azure erőforrás-kezelési műveletei Azure Resource Manager sablonok, a [PowerShell](/powershell/module/Az.eventhub/)és az [Azure CLI](/cli/azure/eventhubs/eventhub/)használatával is elvégezhetők. Jelenleg a PowerShellben és az Azure CLI-ben a kezelői felügyeleti műveletek nem támogatottak.

## <a name="feature-documentation"></a>A szolgáltatás dokumentációja

Az [azure Event Hubs dokumentációja](/azure/event-hubs/) a Event Hubs mindkét kiadására vonatkozik: Event Hubs az Azure stack hub és az Azure Event Hubs. Ez a dokumentáció a Event Hubs és a tevékenységek, például a következők használatával kapcsolatos témaköröket tartalmazza:

- [Event Hubs fogalmak](/azure/event-hubs/event-hubs-features) részletei
- [Event Hubs-fürt és-névtér létrehozása](event-hubs-quickstart-cluster-portal.md)
- [Event hub](/azure/event-hubs/event-hubs-create#create-an-event-hub) létrehozása
- Stream [használata a Kafka protokoll használatával](/azure/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)

### <a name="operator-documentation"></a>Kezelői dokumentáció 
 
Ha többet szeretne megtudni a Event Hubs Azure Stack hub-kezelői felülettel kapcsolatban, tekintse meg a [Event Hubs operátor dokumentációját](../operator/event-hubs-rp-overview.md). Ez a dokumentáció a következő tevékenységekkel kapcsolatos információkat tartalmazza:

- Event Hubs telepítése.
- Event Hubs elérhetővé tétele a felhasználók számára.
- Információk beszerzése a szolgáltatás állapotáról.
- Naplók gyűjtése.


## <a name="next-steps"></a>Következő lépések

Ha Event Hubs nem érhető el az előfizetésben, akkor a rendszergazdával együttműködve [telepítse a Event Hubst Azure stack hub erőforrás-szolgáltatón](../operator/event-hubs-rp-overview.md).

Ha Event Hubs van telepítve, és készen áll a használatára, a szolgáltatással kapcsolatos további információkért tekintse meg a [Event Hubs dokumentációját](/azure/event-hubs/event-hubs-about) .
