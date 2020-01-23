---
title: Azure Stack hub integrált rendszerek kapcsolatainak modelljei | Microsoft Docs
description: A Azure Stack hub integrált rendszereihez tartozó kapcsolódási modellek és egyéb üzembe helyezési tervezési döntések meghatározása.
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
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 53057c5235422f7d5ec016fcd8f9f3286c41dc58
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534073"
---
# <a name="azure-stack-hub-integrated-systems-connection-models"></a>Azure Stack hub integrált rendszerek kapcsolatainak modelljei
Ha érdekli egy Azure Stack hub integrált rendszer megvásárlása, meg kell ismernie a Azure Stack hub üzembe helyezésének [számos adatközpont-integrációs megfontolását](azure-stack-datacenter-integration.md) annak meghatározására, hogy a rendszer hogyan illeszkedik az adatközpontba. Emellett el kell döntenie, hogyan integrálja Azure Stack hub-t a hibrid felhőalapú környezetbe. Ez a cikk áttekintést nyújt ezekről a főbb döntésekről, például az Azure-beli kapcsolatok modelljeiről, az Identity Store lehetőségeiről és a számlázási modell lehetőségeiről.

Ha úgy dönt, hogy egy integrált rendszer megvásárlását választotta, az eredeti berendezésgyártó (OEM) hardver-gyártója segítséget nyújt a tervezési folyamat részletesebb ismertetésében. Az OEM hardver gyártója a tényleges telepítést is végrehajtja.

## <a name="choose-an-azure-stack-hub-deployment-connection-model"></a>Azure Stack hub központi telepítési modell kiválasztása
Dönthet úgy, hogy Azure Stack hubot az internethez (és az Azure-hoz) csatlakozik, vagy le van választva. Az Azure-hoz csatlakoztatott üzembe helyezéssel Azure Stack hub legjavát veheti igénybe, beleértve az Azure Stack hub és az Azure közötti hibrid forgatókönyveket is. Ez a választási lehetőség határozza meg, hogy mely lehetőségek érhetők el az Identity Store-ban (Azure Active Directory vagy Active Directory összevonási szolgáltatások (AD FS)) és a számlázási modellben (a használaton kívüli számlázási vagy kapacitás-alapú számlázás alapján), az alábbi ábrán és táblázatban foglaltak szerint:

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
|Javítás és frissítés|A frissítési csomag közvetlenül az internetről Azure Stack hubhoz tölthető le |  Szükséges<br><br>Cserélhető adathordozót is igényel<br> és egy külön csatlakoztatott eszköz |
| Regisztráció | Automatikus | Szükséges<br><br>Cserélhető adathordozót is igényel<br> és egy külön csatlakoztatott eszköz |

Miután eldöntötte, hogy az Azure-beli kapcsolódási modellt használja-e az Azure Stack hub üzembe helyezéséhez, további, egymástól függő döntéseket kell hoznia az Identity Store-hoz és a számlázási módszerhez.

## <a name="next-steps"></a>Következő lépések

[Azure Connected Azure Stack hub telepítési döntései](azure-stack-connected-deployment.md)

[Az Azure leválasztott Azure Stack hub telepítési döntéseit](azure-stack-disconnected-deployment.md)
