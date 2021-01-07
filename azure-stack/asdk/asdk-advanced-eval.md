---
title: Speciális ASDK-értékelési feladatok
description: További tudnivalók a speciális Azure Stack Development KIt (ASDK) próbaverziós feladatairól.
author: PatAltimore
ms.topic: article
ms.date: 02/12/2019
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 853a67fab66c064f7cc68093dece1030319ea1f1
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97973316"
---
# <a name="advanced-asdk-evaluation-tasks"></a>Speciális ASDK-értékelési feladatok
Miután megismerte az alapszintű Azure Stack Development Kit (ASDK) szolgáltatás funkcióit és képességeit, a fejlettebb forgatókönyvek kivizsgálása révén mélyebben megismerheti Azure Stack további ismereteket. Ezek a speciális értékelési feladatok teljes mértékben dokumentálva vannak a Azure Stack kezelő dokumentációjában.

> [!NOTE]
> Habár számos kezelői feladat támogatott mind a ASDK, mind a több csomópontos Azure Stack üzemelő példányok esetében, nem minden használati forgatókönyv támogatott a ASDK üzemelő példányok esetében. További információ: [ASDK és több csomópontos Azure stack különbségek](asdk-what-is.md#asdk-and-multi-node-azure-stack-hub-differences).

## <a name="delegate-offers-in-azure-stack"></a>Ajánlat delegálása az Azure Stackben
A Azure Stack-kezelővel gyakran más személyeket is fel kell vennie az ajánlatok létrehozására és a felhasználók regisztrálására. Ha például Ön szolgáltató, érdemes lehet viszonteladókat regisztrálni az ügyfelek regisztrálásához és az Ön nevében történő felügyeletéhez. Ha egy vállalaton belül egy központi informatikai csoport tagja, érdemes lehet a leányvállalatok számára a beavatkozás nélkül regisztrálni a felhasználókat.

A [Azure stack delegált ajánlatok](../operator/azure-stack-delegated-provider.md) segítséget nyújt ezen feladatok elvégzésében azáltal, hogy több felhasználót is elérhet és kezelhet, mint amennyit közvetlenül tud.

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>SQL-adatbázisok elérhetővé tétele a Azure Stack-felhasználók számára
Azure Stack kezelőként létrehozhat olyan ajánlatokat, amelyek lehetővé teszik a felhasználók (bérlők) számára, hogy olyan SQL-adatbázisokat hozzanak létre, amelyeket használhatnak a felhőalapú alkalmazásaikkal, webhelyeivel és munkaterhelésekkel. Ha ezeket az egyéni, igény szerinti felhőalapú adatbázisokat a felhasználók számára biztosítja, időt és erőforrásokat takaríthat meg.

Az SQL Server erőforrás-szolgáltatói adapter használatával az [SQL-adatbázisok elérhetővé válnak a Azure stack-felhasználók](../operator/azure-stack-sql-resource-provider.md) számára Azure stack-szolgáltatásként. Az erőforrás-szolgáltató telepítése után egy vagy több SQL Server példányhoz kell csatlakoznia.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Webes és API-alkalmazások elérhetővé tétele a Azure Stack-felhasználók számára
Azure Stack operátorként olyan ajánlatokat hozhat létre, amelyek lehetővé teszik a felhasználók (bérlők) számára Azure Functions és webes és API-alkalmazások létrehozását. Ha hozzáférést biztosít ezen igény szerinti és felhőalapú alkalmazásokhoz a felhasználók számára, akkor időt és erőforrásokat takaríthat meg.

Telepítse a App Service erőforrás-szolgáltatót a [webes és API-alkalmazások elérhetővé tételéhez a Azure stack felhasználók](../operator/azure-stack-app-service-overview.md)számára.

## <a name="next-steps"></a>Következő lépések

[További információ a szolgáltatások Azure Stack integrált rendszerekkel való ellátásáról](../operator/service-plan-offer-subscription-overview.md)
