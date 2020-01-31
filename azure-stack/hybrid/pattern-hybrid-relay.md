---
title: Hibrid továbbító megoldás implementálása az Azure és Azure Stack hub használatával.
description: Ismerje meg, hogyan használhatja az Azure-t és Azure Stack hub-szolgáltatásokat a tűzfalak által védett peremhálózati erőforrásokhoz vagy eszközökhöz való kapcsolódáshoz.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 7315013253c04ebbb231a0915dba610e459848e7
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890068"
---
# <a name="hybrid-relay-pattern"></a>Hibrid továbbítási minta

Megtudhatja, hogyan csatlakozhat a peremhálózati erőforrásokhoz vagy eszközökhöz, amelyeket a tűzfalak a Azure Service Bus Relays használatával védenek.

## <a name="context-and-problem"></a>Kontextus és probléma

Az Edge-eszközök gyakran vállalati tűzfal vagy NAT-eszköz mögött találhatók. Bár biztonságosak, előfordulhat, hogy nem tudnak kommunikálni a nyilvános felhővel, vagy más vállalati hálózatokon található peremhálózati eszközöket. A nyilvános felhőben lévő felhasználók számára bizonyos portok és funkciók biztonságos módon történő közzététele szükséges lehet. 

## <a name="solution"></a>Megoldás

A hibrid továbbítási minta Azure Service Bus-továbbítókat használ egy WebSocket-alagút létrehozásához két végpont között, amelyek nem tudnak közvetlenül kommunikálni. Azok az eszközök, amelyek nem a helyszínen vannak, de csatlakozniuk kell egy helyszíni végponthoz, csatlakozni fognak a nyilvános felhőben lévő végponthoz. Ez a végpont átirányítja a forgalmat az előre meghatározott útvonalakon egy biztonságos csatornán keresztül. A helyszíni környezetben található végpont fogadja a forgalmat, és átirányítja a megfelelő helyre. 

![hibrid továbbító megoldás architektúrája](media/pattern-hybrid-relay/solution-architecture.png)

A megoldás működése: 

1. Az eszköz egy előre meghatározott porton csatlakozik a virtuális géphez (VM) az Azure-ban.
2. A rendszer továbbítja a forgalmat az Azure-beli Service Bus-továbbítónak.
3. Azure Stack hub-beli virtuális gép, amely már régóta fennálló kapcsolattal rendelkezik a Service Bus Relay fogadja a forgalmat, és továbbítja azt a célhelyre.
4. A helyszíni szolgáltatás vagy végpont dolgozza fel a kérelmet. 

## <a name="components"></a>Összetevők

Ez a megoldás a következő összetevőket használja:

| Réteg | Component (Összetevő) | Leírás |
|----------|-----------|-------------|
| Azure | Azure VM | Az Azure-beli virtuális gépek nyilvánosan elérhető végpontot biztosítanak a helyszíni erőforráshoz. |
| | Azure Service Bus Relay | Az [Azure Service Bus Relay](/azure/service-bus-relay/) biztosítja az infrastruktúra fenntartásához szükséges infrastruktúrát és az Azure-beli virtuális gép és a Azure stack hub virtuális gép közötti kapcsolatot.|
| Azure Stack Hub | Számítási szolgáltatások | Az Azure Stack hub-alapú virtuális gép a hibrid továbbító alagút kiszolgálóoldali részét biztosítja. |
| | Adattárolás | Azure Stack hubhoz üzembe helyezett AK-motor-fürt méretezhető, rugalmas motort biztosít az Face API tároló futtatásához.|

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok

A megoldás megvalósításának eldöntése során vegye figyelembe a következő szempontokat:

### <a name="scalability"></a>Skálázhatóság 

Ez a minta csak a 1:1-es port-hozzárendelések használatát teszi lehetővé az ügyfélen és a kiszolgálón. Ha például az 80-as port az Azure-végpont egyik szolgáltatásának bújtatása, akkor nem használható másik szolgáltatáshoz. A portok megfeleltetéseit ennek megfelelően kell tervezni. Az Service Bus továbbítót és a virtuális gépeket megfelelő méretezéssel kell ellátni a forgalom kezeléséhez.

### <a name="availability"></a>Elérhetőség

Ezek az alagutak és kapcsolatok nem redundánsak. A magas rendelkezésre állás biztosítása érdekében érdemes lehet végrehajtani a hibakódot. Egy másik lehetőség, hogy a terheléselosztó mögött Service Bus Relay-kapcsolattal rendelkező virtuális gépek készletét is.

### <a name="manageability"></a>Kezelhetőség

Ez a megoldás számos eszközre és helyszínre képes, így nem lehet megszerezni a megoldást. Az Azure IoT-szolgáltatásai automatikusan online állapotba helyezhetik az új telephelyeket és eszközöket, és naprakészen tarthatják őket.

### <a name="security"></a>Biztonság

Ez a minta lehetővé teszi, hogy korlátlan hozzáférést biztosítson egy belső eszköz portjához a szélétől. Érdemes lehet hitelesítési mechanizmust hozzáadni a szolgáltatáshoz a belső eszközön vagy a hibrid továbbító végpontja előtt. 

## <a name="next-steps"></a>Következő lépések

További információ a cikkben bemutatott témakörökről:
- Ez a minta Azure Service Bus reléket használ. További információt a [Azure Service Bus Relay dokumentációjában](/azure/service-bus-relay/)talál.
- Az ajánlott eljárásokkal kapcsolatos további információkért és a további kérdések megválaszolásáért tekintse meg a [hibrid alkalmazások kialakításával kapcsolatos szempontokat](overview-app-design-considerations.md) .
- A termékek és megoldások teljes portfóliójának megismeréséhez tekintse meg a [Azure stack termékcsaládot és megoldásokat](/azure-stack).

Ha készen áll a megoldás tesztelésére, folytassa a [hibrid továbbító megoldás telepítési útmutatóját](https://aka.ms/hybridrelaydeployment). A telepítési útmutató részletes útmutatást nyújt az összetevők üzembe helyezéséhez és teszteléséhez.