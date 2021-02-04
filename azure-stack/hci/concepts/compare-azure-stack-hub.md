---
title: Azure Stack HCI összehasonlítása Azure Stack hubhoz
description: Ez a témakör segít megállapítani, hogy Azure Stack HCI vagy Azure Stack hub megfelelő-e a szervezet számára.
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/03/2021
ms.openlocfilehash: e82980a5dca347549672dd519132c1f1754c3929
ms.sourcegitcommit: af2bec84471795c0f3ac62dcaf1347a64e529906
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554057"
---
# <a name="compare-azure-stack-hci-to-azure-stack-hub"></a>Azure Stack HCI összehasonlítása Azure Stack hubhoz

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Azure Stack hub

A szervezet digitális átalakításának köszönhetően a nyilvános Cloud Services használatával gyorsabban átalakíthatja a modern architektúrákat, és frissítheti a régi alkalmazásokat. A technológiai és szabályozási akadályokat is magában foglaló okok miatt azonban sok számítási feladatnak a helyszínen kell maradnia. A táblázat segítségével meghatározhatja, hogy melyik Microsoft Hybrid Cloud-stratégia biztosítja, hogy mire van szüksége, hol van szüksége a Felhőbeli innovációra a számítási feladatokhoz, bárhol legyenek is.

| Azure Stack HCI | Azure Stack Hub |
| --------------- | --------------- |
| Ugyanazok a képességek, ismerős folyamatok | Új ismeretek, innovatív folyamatok |
| Az adatközpont összekötése az Azure-szolgáltatásokkal | Az adatközpontban található Azure-szolgáltatások |

## <a name="when-to-use-azure-stack-hci"></a>Mikor kell használni a Azure Stack HCI-t

A következő táblázat összehasonlítja azokat a használati eseteket, amelyekhez Azure Stack a HCI jobban illeszkedik, mint Azure Stack hub.

| Azure Stack HCI                                                                 | Azure Stack Hub                                                                         |
| ------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| A távoli irodák és ágak minimális helyigényéhez használjon Azure Stack HCI-t. Az egyszerűség és a megfizethetőség érdekében csak két kiszolgálóval kezdheti meg a működést, és visszakapcsolhatja a háttér-visszaállítást. | Azure Stack hubhoz legalább négy kiszolgáló és a saját hálózati kapcsolók szükségesek. |
| A Azure Stack HCI használatával virtualizálhatja a klasszikus vállalati alkalmazásokat, például az Exchange-et, a SharePointot és a SQL Servert, és virtualizálhatja a Windows Server-szerepköröket, például a fájlkiszolgáló, a DNS, a DHCP, az IIS és a Active Directory. Korlátlan hozzáférést biztosít a Hyper-V-funkciókhoz.| Azure Stack hub korlátozza a Hyper-V konfigurálását és az Azure-hoz való konzisztens szolgáltatást. | 
| A Azure Stack HCI használatával szoftveresen definiált infrastruktúrát használhat az öregedő tárolási tömbök vagy hálózati berendezések helyett, jelentős Újraépítés nélkül. A beépített Hyper-V, Közvetlen tárolóhelyek és Software-Defined hálózatkezelés (SDN) közvetlenül elérhető és kezelhető. | Azure Stack hub nem teszi elérhetővé ezeket az infrastrukturális technológiákat. |

## <a name="when-to-use-azure-stack-hub"></a>Mikor kell használni a Azure Stack hub-t

A következő táblázat összehasonlítja azokat a használati eseteket, amelyekhez Azure Stack hub jobban illeszkedik, mint Azure Stack HCI.

| Azure Stack HCI                                                                 | Azure Stack Hub                                                                          |
| ------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| Azure Stack HCI nem kényszerített natív módon, vagy nem biztosít több bérlőt. | Az önkiszolgáló infrastruktúra-szolgáltatás (IaaS) esetében használja az Azure Stack hub-t, amely erős elkülönítést és pontos használatot biztosít a több közös elhelyezésű bérlő esetében. Ideális a szolgáltatók és a vállalati privát felhők számára. Sablonok az Azure piactéren. | 
| Azure Stack a HCI-nek nem célja a szolgáltatásként nyújtott platformon futó (a szolgáltatáson kívüli) szolgáltatások helyszíni futtatása, bár ez magában foglalja az [Azure Kubernetes szolgáltatás](/azure-stack/aks-hci/overview.md)üzemeltetésének lehetőségét is. | A Azure Stack hub használatával olyan alkalmazásokat fejleszthet és futtathat, amelyek olyan Pásti-szolgáltatásokon alapulnak, mint például a Web Apps, a functions vagy a Event Hubs a helyszínen. Ezek a szolgáltatások a Azure Stack hub-on futnak, ugyanúgy, mint az Azure-ban, és egységes hibrid fejlesztési és futtatókörnyezeti környezetet biztosítanak. |
| Azure Stack HCI nem natív módon tartalmaz bármilyen DevOps-eszközt. | A Azure Stack hub használatával modernizálhatja az alkalmazások üzembe helyezését és működését olyan DevOps eljárásokkal, mint például a kód, a folyamatos integráció és a folyamatos üzembe helyezés (CI/CD), valamint a kényelmes funkciók, például az Azure-konzisztens virtuálisgép-bővítmények. Ideális fejlesztői és DevOps csapatoknak. |

## <a name="next-steps"></a>Következő lépések

- [A Azure Stack HCI és a Windows Server 2019 összehasonlítása](compare-windows-server.md)
