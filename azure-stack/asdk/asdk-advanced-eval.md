---
title: Speciális Azure Stack értékelési feladatok |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Stack értékelési speciális feladatokat.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: a0b42a600036cca90741cd64517a86e8ae3a7344
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65615820"
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>Az Azure Stack Development Kit értékelést speciális feladatok
Után az Azure Stack Development Kit (ASDK) szolgáltatás alapszintű funkciók és képességek ismeretét, révén, akkor is mélyítheti a Tovább az Azure Stack által speciális forgatókönyvek tesztelését. Ezek összetettebb kiértékelése a feladatok teljes körűen vannak dokumentálva az Azure Stack operátori dokumentációja.

> [!NOTE]
> Míg számos operátor feladat ASDK és termelési célra egyaránt, több csomópontos Azure Stack üzemelő példánya esetében támogatottak nem minden használati forgatókönyvek ASDK központi telepítések támogatottak. Lásd: [ASDK és több csomópontos Azure Stack különbségek](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences) további információt.

## <a name="delegate-offers-in-azure-stack"></a>Ajánlat delegálása az Azure Stackben
Az Azure Stack-operátori, milyen gyakran szeretné ajánlatok létrehozásához, és regisztráló felhasználók felelős mások. Például ha Ön a szolgáltató, érdemes lehet a viszonteladók számára a felhasználók regisztrálása és kezelheti azokat az Ön nevében is. Vagy ha Ön egy vállalat egy központi informatikai csoport része, érdemes lehet leányvállalatai Ön beavatkozása nélkül felhasználókat regisztrálhat.

[Ajánlat delegálása az Azure Stack](../operator/azure-stack-delegated-provider.md) segítségével éri el, és közvetlenül választhat, mint további felhasználók kezelése lehetővé teszi ezeket a feladatokat.

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>SQL-adatbázisok az Azure Stack-felhasználók számára elérhetővé
Az Azure Stack-operátori is kínál, amelyek lehetővé teszik a felhasználók vásárolhatnak (bérlők) is használható a natív felhőalkalmazásokat, a websites és a számítási feladatokat az SQL-adatbázisok létrehozása. Az egyéni, igény szerinti, felhőalapú adatbázisok biztosításával a felhasználók számára, mentheti őket időt és erőforrásokat.

Használja az SQL Server erőforrás-szolgáltató adapter való [SQL-adatbázisok az Azure Stack-felhasználók számára elérhetővé](../operator/azure-stack-tutorial-sql-server.md) szolgáltatásként az Azure Stack. Az erőforrás-szolgáltató telepítése után, csatlakoztassa azt egy vagy több SQL Server-példányt.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Webes és API-alkalmazások elérhetővé tétele az Azure Stack-felhasználók számára
Az Azure Stack-operátori is kínál, amelyek lehetővé teszik a felhasználók vásárolhatnak (bérlők) Azure Functions és a webes és API-alkalmazások létrehozása. Ezek igény szerinti, a felhőbeli alkalmazásokhoz való hozzáférés biztosításával a felhasználók számára, mentheti őket időt és erőforrásokat.

Az App Service-ben az erőforrás-szolgáltató üzembe helyezése [web- és API-alkalmazások elérhetővé tétele az Azure Stack-felhasználók számára](../operator/azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>További lépések

[Tudjon meg többet az Azure Stack integrált rendszerek szolgáltatásajánlások](../operator/azure-stack-offer-services-overview.md)
