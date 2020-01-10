---
title: Hibrid minták és megoldási példák az Azure-ra és a Azure Stack
description: A hibrid minták és megoldások példáinak áttekintése, amelyek hasznosak lehetnek az Azure-ban és Azure Stack-ban hibrid megoldások tanulásához és létrehozásához.
author: BryanLa
ms.service: azure-stack
ms.topic: overview
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 82f933babd0be4c001bd3001a631a4345bd0e488
ms.sourcegitcommit: b96a0b151b9c0d3eea59e7c2d39119a913782624
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75718062"
---
# <a name="hybrid-patterns-and-solution-examples-for-azure-and-azure-stack"></a>Hibrid minták és megoldási példák az Azure-ra és a Azure Stack

A Microsoft az Azure-t és Azure Stack termékeket és megoldásokat biztosít egyetlen egységes Azure-ökoszisztémaként. A Microsoft Azure Stack család az Azure kiterjesztése. 

## <a name="the-hybrid-cloud-and-hybrid-apps"></a>Hibrid Felhőbeli és hibrid alkalmazások

A *hibrid felhő*engedélyezésével Azure stack a felhő-számítástechnika rugalmasságát a helyszíni környezet és a peremhálózat számára. Azure Stack hub, Azure Stack HCI és Azure Stack Edge kiterjeszti az Azure-t a felhőből a szuverén adatközpontokban, a fiókirodákban, a mezőkben és azon kívül. Ezzel a különböző funkciókkal a következőket teheti:

- Az Azure-ban és a helyszíni környezetekben konzisztens módon használhat programkódot, és a felhőben natív alkalmazásokat is futtathat.
- Hagyományos virtualizált számítási feladatok futtatása az Azure-szolgáltatásokhoz való opcionális csatlakozással.
- Vigye át az adatait a felhőbe, vagy tartsa meg a szuverén adatközpontban a megfelelőség fenntartása érdekében.
- Hardveres gyorsítású gépi tanulási, tárolós vagy virtualizált munkaterhelések futtatása az intelligens peremhálózat minden részén.

A felhőkre kiterjedő alkalmazásokat *hibrid alkalmazásoknak*is nevezzük. Hibrid felhőalapú alkalmazásokat hozhat létre az Azure-ban, és üzembe helyezheti azokat a csatlakoztatott vagy leválasztott adatközpontban bárhol.

A hibrid alkalmazás-forgatókönyvek nagy mértékben eltérnek a fejlesztéshez rendelkezésre álló erőforrásokkal. Olyan szempontokat is figyelembe kell venniük, mint például a földrajz, a biztonság, az Internet-hozzáférés és egyebek. Bár az itt ismertetett minták és megoldások nem minden követelményre vonatkoznak, útmutatást és példákat biztosítanak a hibrid megoldások megvalósítása során felderített és újbóli felhasználáshoz.

## <a name="design-patterns"></a>Tervezési minták

A tervezési minták kiselejtezett általánosított kialakítási útmutatást nyújtanak a valós felhasználói forgatókönyvek és a tapasztalatok alapján. A minta absztrakt, amely lehetővé teszi, hogy a különböző típusú forgatókönyvekre vagy vertikális iparágakra alkalmazható legyen. Minden minta dokumentálja a környezetet és a problémát, és áttekintést nyújt a megoldásról. A megoldás példaként a minta lehetséges megvalósítását jelenti.

A minták két típusa létezik:

- Egyetlen minta: tervezési útmutatót biztosít egyetlen általános célú forgatókönyvhöz.
- Multi-Pattern: tervezési útmutatást biztosít, ahol több minta alkalmazása is használható. Ez gyakran szükséges az összetettebb forgatókönyvek, illetve az iparági problémák megoldásához.

## <a name="solution-deployment-guides"></a>Megoldás-telepítési útmutatók

A lépésenkénti üzembe helyezési útmutatók segítséget nyújtanak a megoldások üzembe helyezésében. Az útmutató a GitHub [Solutions](https://github.com/Azure-Samples/azure-intelligent-edge-patterns)-tárházban tárolt Companion Code-mintára is hivatkozhat. 

## <a name="next-steps"></a>Következő lépések

- A termékek és megoldások teljes portfóliójának megismeréséhez tekintse meg a [Azure stack termékcsaládot és megoldásokat](/azure-stack).
- Ismerkedjen meg a "Patterns" és a "megoldás központi telepítési útmutatók" fejezeteivel a TARTALOMJEGYZÉKben.
- További információ a hibrid alkalmazások [kialakításával kapcsolatos szempontokról](overview-app-design-considerations.md) : a szoftverek minőségének alappillérei a hibrid alkalmazások tervezéséhez, üzembe helyezéséhez és működtetéséhez.
- [Hozzon létre egy fejlesztési környezetet a Azure stackon](../user/azure-stack-dev-start.md) , és helyezze [üzembe az első alkalmazást](../user/azure-stack-dev-start-deploy-app.md) Azure stackon.
