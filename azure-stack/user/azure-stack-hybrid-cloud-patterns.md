---
title: A hibrid felhőbeli alkalmazások létrehozása az Azure és az Azure Stackben |} A Microsoft Docs
description: A hibrid felhőbeli alkalmazások létrehozása az Azure és az Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 05/06/2019
ms.openlocfilehash: a1e13b471c9eaed9dcac79c4002ceca6b3b8e7d2
ms.sourcegitcommit: a78c0d143eadcab65a601746b9ea24be28091ad2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212684"
---
# <a name="create-hybrid-cloud-apps-with-azure-and-azure-stack"></a>A hibrid felhőbeli alkalmazások létrehozása az Azure és az Azure Stackben

A Microsoft Azure Stack az Azure bővítménye. Az Azure Stack a felhőalapú számítástechnika a helyszíni környezetben és az Edge-ben a hibrid felhő engedélyezésével rugalmasságot biztosít. Az Azure-beli hibrid felhőbeli alkalmazások készítése, és telepítse őket a csatlakoztatott vagy leválasztott adatközpontját a tetszőleges helyen található.

A Microsoft Azure-ban a konzisztens hibrid felhő. Az Azure lehetővé teszi, hogy újból felhasználja a kódot az Azure-ban fejlesztett és az alkalmazás üzembe helyezése az Azure-szuverén felhők és az Azure Stackben. Alkalmazásokat, amelyek részben a felhőben is nevezzük *hibrid alkalmazások*.

A hibrid forgatókönyvek érhetők el fejlesztési erőforrások, például földrajzi hely, a biztonság, az Internet-hozzáféréssel kapcsolatos szempontok és egyéb szempontok nagy mértékben függenek. Bár ezek a forgatókönyvek nem oldja meg az adott igények szerint, azok néhány kulcsfontosságú útmutatást és példák segítségével megismerheti a végrehajtási hibrid megoldásokat biztosíthat.

A hibrid felhő segítségével kezelni:
- Minta rétegzett adatok.
- Az SQL Server az Azure és az Azure Stack.
- Mongo-adatbázis teljes körű központi telepítések, az Azure és az Azure Stackben.
- A Microsoft Edge mesterséges intelligencia következtetésekhez érvényteleníteni az észlelést.

## <a name="step-by-step-tutorials"></a>Részletes útmutatók

- [Alkalmazások üzembe helyezése az Azure és az Azure Stackben](azure-stack-solution-pipeline.md)
- [Alkalmazások üzembe helyezése az Azure Stacken és az Azure-ban](azure-stack-solution-hybrid-identity.md)
- [A hibrid felhőbeli identitás konfigurálása az Azure és az Azure Stack-alkalmazásokkal](azure-stack-solution-hybrid-connectivity.md)
- [Hibrid felhő-kapcsolat konfigurálása az Azure és az Azure Stack](azure-stack-solution-staged-data-analytics.md)
- [Hozzon létre egy előkészített adatelemzési megoldással az Azure és az Azure Stackben](azure-stack-solution-cloud-burst.md)
- [A felhőbe irányuló méretezési megoldások létrehozása az Azure-ral](azure-stack-solution-cloud-burst.md)
- [Földrajzilag elosztott alkalmazás megoldás létrehozása az Azure és az Azure Stackben](azure-stack-solution-geo-distributed.md)
- [Az Azure és az Azure Stack egy hibrid felhőmegoldás üzembe helyezése](azure-stack-solution-hybrid-cloud.md)

## <a name="next-steps"></a>További lépések

- A tanulmány elolvasása [hibrid alkalmazások kapcsolatos kialakítási szempontok](https://aka.ms/hybrid-cloud-applications-pillars) , tekintse át a szoftverminőség alappillérei tervezésével, telepítésével és üzemeltetésével hibrid alkalmazások számára.
- [Állítsa be a fejlesztési környezetet az Azure Stacken](azure-stack-dev-start.md) és [az első alkalmazás üzembe helyezése](azure-stack-dev-start-deploy-app.md) az Azure Stacken.
