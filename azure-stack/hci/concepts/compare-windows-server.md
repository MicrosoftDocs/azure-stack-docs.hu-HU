---
title: Azure Stack HCI összehasonlítása Azure Stack hub és a Windows Server között
description: Ez a témakör segít megállapítani, hogy a Azure Stack HCI, Azure Stack hub vagy a Windows Server megfelelő-e a szervezete számára.
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/22/2020
ms.openlocfilehash: a4429113088497fd6a7c887a65f524a7495c6b74
ms.sourcegitcommit: a745662c7a5a18f135accf3f70d8508b57e83e2b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97737810"
---
# <a name="compare-azure-stack-hci-to-azure-stack-hub-and-windows-server"></a>Azure Stack HCI összehasonlítása Azure Stack hub és a Windows Server között

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019; Azure Stack hub

Sok ügyfél fogja tudni, hogy a Windows Server, a Azure Stack HCI vagy a Azure Stack hub jobban illeszkedik-e az igényeihez. Ez a témakör segít meghatározni, hogy melyik a legmegfelelőbb a szervezet számára. 

## <a name="compare-azure-stack-hci-to-windows-server"></a>Azure Stack HCI összehasonlítása a Windows Serverrel

A Windows Server és a Azure Stack HCI is ugyanazt a kiváló minőségű felhasználói élményt nyújtja, mint az új kiadásokra vonatkozó útitervet.

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Legjobb vendég és hagyományos kiszolgáló | A legjobb virtualizációs gazdagép egy szoftveresen definiált adatközponthoz, beleértve a Közvetlen tárolóhelyek |
| Bárhol, hagyományos szoftverlicenc-modell használatával fut | Az előnyben részesített gyártótól származó hardveren fut, de Azure-szolgáltatásként érhető el, és az Azure-fiókba kerül. |
| Két telepítési lehetőség: kiszolgáló asztali felülettel vagy Server Core-val | Egy egyszerűen testre szabott Server Core alapján |

### <a name="when-to-use-windows-server"></a>Mikor kell használni a Windows Servert?

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| A Windows Server egy sokoldalú, többcélú operációs rendszer, több tucat szerepkörrel és több száz funkcióval, beleértve a vendég jogosultságokat is. | Azure Stack HCI nem tartalmaz vendég jogosultságokat, és a rendszer egy modern, hiperkonvergens architektúrára kíván használni. |
| A Windows Server használatával virtuális gépeket futtathat, vagy operációs rendszer nélküli telepítéseket, beleértve az összes hagyományos kiszolgálói szerepkört, többek között a Active Directory, a Fájlszolgáltatások, a DNS, a DHCP, a Internet Information Services (IIS), a Container Host/Guest, a SQL Server, az Exchange Server, a Host Guardian Service (HGS) és sok más. | Hyper-V virtualizációs gazdagépként való használatra Azure Stack a HCI csak kis számú kiszolgálói szerepkör futtatására jogosult. minden más szerepkörnek a virtuális gépeken belül kell futnia. |

### <a name="when-to-use-azure-stack-hci"></a>Mikor kell használni a Azure Stack HCI-t

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| A Windows Server a helyszínen vagy a felhőben is futtatható, de a legújabb hiperkonvergens funkciók nem lesznek elérhetők.| Azure Stack HCI a Microsoft Premier hiperkonvergens infrastruktúra-platformja a helyszíni virtuális gépek futtatásához, opcionálisan két hely között, valamint az Azure Hybrid Serviceshez való csatlakozással. Az adatközpontok és fiókirodák korszerűsítésének és biztonságossá tételének egyszerű módja, az iparág legjobb teljesítményének elérése SQL Server adatbázisokhoz, valamint virtuális gépek vagy virtuális asztalok futtatása a helyszínen az alacsony késés és az adatszuverenitás érdekében.|
| A Windows Server egy nagyszerű, többcélú "svájci bicska" az összes Windows Server-szerepkörhöz, virtualizálva vagy nem. | A Azure Stack HCI használatával virtualizálhatja a klasszikus vállalati alkalmazásokat, például az Exchange-et, a SharePointot és a SQL Servert, és virtualizálhatja a Windows Server-szerepköröket, például a fájlkiszolgáló, a DNS, a DHCP, az IIS és az AD. Korlátlan hozzáférést biztosít minden olyan Hyper-V-szolgáltatáshoz, mint például a védett virtuális gépek.|
| Számos Windows Server-példány fut az öregedési hardveren. | A Azure Stack HCI használatával szoftveresen definiált infrastruktúrát használhat az öregedő tárolási tömbök vagy hálózati berendezések helyett, jelentős Újraépítés nélkül. A beépített Hyper-V, Közvetlen tárolóhelyek és Software-Defined hálózatkezelés (SDN) közvetlenül elérhető és kezelhető. Alkalmazások futtatása Windows vagy Linux rendszerű virtuális gépeken belül.|

## <a name="compare-azure-stack-hci-to-azure-stack-hub"></a>Azure Stack HCI összehasonlítása Azure Stack hubhoz

A szervezet digitális átalakításának köszönhetően a nyilvános Cloud Services használatával gyorsabban átalakíthatja a modern architektúrákat, és frissítheti a régi alkalmazásokat. A technológiai és szabályozási akadályokat is magában foglaló okok miatt azonban sok számítási feladatnak a helyszínen kell maradnia. A táblázat segítségével meghatározhatja, hogy melyik Microsoft Hybrid Cloud-stratégia biztosítja, hogy mire van szüksége, hol van szüksége a Felhőbeli innovációra a számítási feladatokhoz, bárhol legyenek is.

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Új ismeretek, innovatív folyamatok | Ugyanazok a képességek, ismerős folyamatok |
| Az adatközpontban található Azure-szolgáltatások | Az adatközpont összekötése az Azure-szolgáltatásokkal |

### <a name="when-to-use-azure-stack-hub"></a>Mikor kell használni a Azure Stack hub-t

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Az önkiszolgáló infrastruktúra-szolgáltatás (IaaS) esetében használja az Azure Stack hub-t, amely erős elkülönítést és pontos használatot biztosít a több közös elhelyezésű bérlő esetében. Ideális a szolgáltatók és a vállalati privát felhők számára. Sablonok az Azure piactéren. | Azure Stack HCI nem kényszerített natív módon, vagy nem biztosít több bérlőt. |
| Az Azure Stack hub használatával olyan alkalmazásokat fejleszthet és futtathat, amelyek a szolgáltatásként nyújtott platformon alapuló (például a Web Apps, a functions vagy a Event Hubs helyszíni) szolgáltatásokra támaszkodnak. Ezek a szolgáltatások a Azure Stack hub-on futnak, ugyanúgy, mint az Azure-ban, és egységes hibrid fejlesztési és futtatókörnyezeti környezetet biztosítanak. | Azure Stack HCI nem futtatja a Pásti-szolgáltatásokat a helyszínen. |
| A Azure Stack hub használatával modernizálhatja az alkalmazások üzembe helyezését és működését olyan DevOps eljárásokkal, mint például a kód, a folyamatos integráció és a folyamatos üzembe helyezés (CI/CD), valamint a kényelmes funkciók, például az Azure-konzisztens virtuálisgép-bővítmények. Ideális fejlesztői és DevOps csapatoknak. | Azure Stack HCI nem natív módon tartalmaz bármilyen DevOps-eszközt. |

### <a name="when-to-use-azure-stack-hci"></a>Mikor kell használni a Azure Stack HCI-t

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Azure Stack hub minimális 4 csomópontot és saját hálózati kapcsolókat igényel. | A távoli irodák és ágak minimális helyigényéhez használjon Azure Stack HCI-t. Az egyszerűség és a megfizethetőség érdekében csak 2 kiszolgáló-csomóponttal és visszaváltható, visszatérhető hálózatkezeléssel kezdheti a munkát. A hardveres ajánlatok 4 meghajtóval, 64 GB memóriával, valamint a $10k/Node értékkel kezdődnek. |
| Azure Stack hub korlátozza a Hyper-V konfigurálását és az Azure-hoz való konzisztencia-szolgáltatást. | Használjon Azure Stack HCI-t a klasszikus vállalati alkalmazások, például az Exchange, a SharePoint és a SQL Server, valamint a Windows Server-szerepkörök, például a fájlkiszolgáló, a DNS, a DHCP, az IIS és az AD virtualizálása érdekében. Korlátlan hozzáférés az összes Hyper-V-szolgáltatáshoz, például a védett virtuális gépekhez.|
| Azure Stack hub nem teszi elérhetővé ezeket az infrastrukturális technológiákat. | A Azure Stack HCI használatával szoftveresen definiált infrastruktúrát használhat az öregedő tárolási tömbök vagy hálózati berendezések helyett, jelentős Újraépítés nélkül. A beépített Hyper-V, Közvetlen tárolóhelyek és Software-Defined hálózatkezelés (SDN) közvetlenül elérhető és kezelhető. |

## <a name="next-steps"></a>További lépések

- [Mi az Azure Stack HCI?](../overview.md)
