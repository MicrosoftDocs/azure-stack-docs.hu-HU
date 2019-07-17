---
title: Az Azure Stackhez készült hibrid tervezési minták felhőkhöz megoldások készítése |} A Microsoft Docs
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
ms.date: 07/16/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 538a626b2e89d15aa4b816674dbb8c374ec4a262
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286811"
---
#  <a name="build-solutions-hybrid-cloud-design-patterns-for-azure-stack"></a>Az Azure Stackhez készült hibrid tervezési minták felhőkhöz megoldások készítése

A Microsoft Azure Stack az Azure bővítménye. Az Azure Stack a felhőalapú számítástechnika a helyszíni környezetben és az Edge-ben a hibrid felhő engedélyezésével rugalmasságot biztosít. Az Azure-beli hibrid felhőbeli alkalmazások készítése, és telepítse őket a csatlakoztatott vagy leválasztott adatközpontját a tetszőleges helyen található.

A Microsoft Azure-ban a konzisztens hibrid felhő. Az Azure lehetővé teszi, hogy újból felhasználja a kódot az Azure-ban fejlesztett és az alkalmazás üzembe helyezése az Azure-szuverén felhők és az Azure Stackben. Alkalmazásokat, amelyek részben a felhőben is nevezzük *hibrid alkalmazások*.

A hibrid forgatókönyvek érhetők el fejlesztési erőforrások, például földrajzi hely, a biztonság, az Internet-hozzáféréssel kapcsolatos szempontok és egyéb szempontok nagy mértékben függenek. Bár ezek a forgatókönyvek nem oldja meg az adott igények szerint, azok néhány kulcsfontosságú útmutatást és példák segítségével megismerheti a végrehajtási hibrid megoldásokat biztosíthat.

## <a name="hybrid-cloud-patterns"></a>Hibrid felhő minták

- [A felhőbe irányuló skálázási minta](azure-stack-edge-pattern-cross-cloud-scaling.md)
- [Földrajzilag elosztott minta](azure-stack-edge-pattern-geo-distribution.md)
- [Fejlesztési és üzemeltetési minta](azure-stack-edge-pattern-hybrid-ci-cd.md)

## <a name="step-by-step-tutorials"></a>Részletes útmutatók

- [Alkalmazások üzembe helyezése az Azure és az Azure Stackben](azure-stack-solution-pipeline.md)
- [Alkalmazások üzembe helyezése az Azure Stacken és az Azure-ban](azure-stack-solution-hybrid-identity.md)
- [A hibrid felhőbeli identitás konfigurálása az Azure és az Azure Stack-alkalmazásokkal](azure-stack-solution-hybrid-connectivity.md)
- [Hibrid felhő-kapcsolat konfigurálása az Azure és az Azure Stack](azure-stack-solution-staged-data-analytics.md)
- [Hozzon létre egy előkészített adatelemzési megoldással az Azure és az Azure Stackben](azure-stack-solution-staged-data.md)
- [A felhőbe irányuló méretezési megoldások létrehozása az Azure-ral](azure-stack-solution-cloud-burst.md)
- [Földrajzilag elosztott alkalmazás megoldás létrehozása az Azure és az Azure Stackben](azure-stack-solution-geo-distributed.md)
- [Az Azure és az Azure Stack egy hibrid felhőmegoldás üzembe helyezése](azure-stack-solution-hybrid-cloud.md)
- [Üzembe helyezése a mongodb-adatbázist az Azure és az Azure Stackben](azure-stack-solution-mongodb-ha.md)
- [Üzembe helyezése az SQL Server 2016 az Azure és az Azure Stackben](azure-stack-solution-sql-ha.md)


## <a name="next-steps"></a>További lépések

- Olvassa el a kapcsolatos [hibrid alkalmazások kapcsolatos kialakítási szempontok](azure-stack-edge-pattern-overview.md) , tekintse át a szoftverminőség alappillérei tervezésével, telepítésével és üzemeltetésével hibrid alkalmazások számára.
- [Állítsa be a fejlesztési környezetet az Azure Stacken](azure-stack-dev-start.md) és [az első alkalmazás üzembe helyezése](azure-stack-dev-start-deploy-app.md) az Azure Stacken.
