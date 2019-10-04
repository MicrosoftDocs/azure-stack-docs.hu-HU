---
title: Azure Stack integrált rendszerek kapcsolatainak modelljei | Microsoft Docs
description: A kapcsolódási modellek és az egyéb központi telepítési tervezési döntések meghatározása Azure Stack integrált rendszerekhez.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 8646e9d1936c865482368d176194c095c9dd0483
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909416"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Azure Stack integrált rendszerek csatlakoztatási modelljei
Ha érdekli egy Azure Stack integrált rendszer megvásárlása, meg kell ismernie a Azure Stack üzembe helyezésének [számos adatközpont-integrációs megfontolását](azure-stack-datacenter-integration.md) , hogy meghatározza, hogyan illeszkedik a rendszer az adatközpontba. Emellett el kell döntenie, hogyan integrálja a Azure Stackt a hibrid felhőalapú környezetbe. Ez a cikk áttekintést nyújt ezekről a főbb döntésekről, például az Azure-beli kapcsolatok modelljeiről, az Identity Store lehetőségeiről és a számlázási modell lehetőségeiről.

Ha úgy dönt, hogy egy integrált rendszer megvásárlását választotta, az eredeti berendezésgyártó (OEM) hardver-gyártója segítséget nyújt a tervezési folyamat részletesebb ismertetésében. Az OEM hardver gyártója a tényleges telepítést is végrehajtja.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Azure Stack telepítési kapcsolódási modell kiválasztása
Dönthet úgy, hogy Azure Stack az internethez (és az Azure-hoz) csatlakozik, vagy le van választva. Az Azure-hoz csatlakoztatott üzembe helyezéssel kihasználhatja a Azure Stack legnagyobb előnyeit, beleértve a Azure Stack és az Azure közötti hibrid forgatókönyveket is. Ez a választási lehetőség határozza meg, hogy mely lehetőségek érhetők el az Identity Store-ban (Azure Active Directory vagy Active Directory összevonási szolgáltatások (AD FS)) és a számlázási modellben (a használaton kívüli számlázási vagy kapacitás-alapú számlázás alapján), az alábbi ábrán és táblázatban foglaltak szerint:

![Központi telepítési és számlázási forgatókönyvek Azure Stack](media/azure-stack-connection-models/azure-stack-scenarios.png)
  
> [!IMPORTANT]
> Ez egy kulcsfontosságú döntési pont! Active Directory összevonási szolgáltatások (AD FS) (AD FS) vagy Azure Active Directory (Azure AD) kiválasztása egy egyszeri döntés, amelyet a telepítéskor kell elvégeznie. Ez később nem módosítható a teljes rendszer újbóli telepítése nélkül.  


|Beállítások|Csatlakoztatva az Azure-hoz|Leválasztva az Azure-ból|
|-----|:-----:|:-----:|
|Azure AD|![Támogatott](media/azure-stack-connection-models/check.png)| |
|AD FS|![Támogatott](media/azure-stack-connection-models/check.png)|![Támogatott](media/azure-stack-connection-models/check.png)|
|Fogyasztáson alapuló számlázás|![Támogatott](media/azure-stack-connection-models/check.png)| |
|Kapacitás alapú számlázás|![Támogatott](media/azure-stack-connection-models/check.png)|![Támogatott](media/azure-stack-connection-models/check.png)|
|Licencelés| Nagyvállalati Szerződés vagy felhőalapú megoldás szolgáltatója | Nagyvállalati szerződés |
|Javítás és frissítés|A frissítési csomag közvetlenül az internetről Azure Stackra tölthető le |  Szükséges<br><br>Cserélhető adathordozót is igényel<br> és egy külön csatlakoztatott eszköz |
| Regisztráció | Automatikus | Szükséges<br><br>Cserélhető adathordozót is igényel<br> és egy külön csatlakoztatott eszköz |

Miután eldöntötte, hogy az Azure-beli kapcsolódási modellt szeretné-e használni a Azure Stack üzembe helyezéséhez, további, egymástól függő döntéseket kell hoznia az Identity Store és a számlázási módszerhez.

## <a name="next-steps"></a>További lépések

[Az Azure-ra csatlakoztatott Azure Stack üzemelő példányaival kapcsolatos döntések](azure-stack-connected-deployment.md)

[Az Azure-ra nem csatlakozó Azure Stack üzemelő példányaival kapcsolatos döntések](azure-stack-disconnected-deployment.md)
