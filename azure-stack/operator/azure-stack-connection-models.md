---
title: Az Azure Stackkel integrált rendszerek kapcsolati modellek |} A Microsoft Docs
description: Tervezési megfontolások az Azure Stack több csomópontos központi telepítés határozza meg.
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
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: da48a9a04c9daaf2d7a29bc2d4b300563bebd6e5
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618964"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Az Azure Stackkel integrált rendszerek kapcsolati modellek
Ha Ön a powerappsot, olvassa az Azure Stackkel integrált rendszer, kell megérteni [több adatközpont integrációja szempontok](azure-stack-datacenter-integration.md) való határozza meg, hogyan elférnek a rendszer a helyi adatközpontban Azure Stack üzembe helyezéshez. Emellett szüksége annak eldöntése, hogyan fogja integrálja az Azure Stack a hibridfelhő-környezet. Ez a cikk a főbb kérdései, többek között az Azure-kapcsolat, ügyfélidentitás-tárolóval, és a számlázási modell döntések áttekintést nyújt.

Ha úgy dönt, hogy vásárol egy integrált rendszer, a számítógépgyártó (OEM) hardvergyártójához segítségével vezeti végig a tervezési folyamat további részleteket a jelentős részét. Azok a tényleges telepítési is elvégzi.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Az Azure Stack üzembe helyezési kapcsolat modell kiválasztása
Kiválaszthatja, hogy csatlakozik az internethez (és az Azure-bA), vagy csatlakoztatva az Azure Stack üzembe helyezése. Hozhatja ki a legnagyobb előnnyel az Azure Stack, hibrid forgatókönyvek között az Azure Stacket és Azure-ban, beleértve a szeretne üzembe helyezése az Azure-hoz csatlakoztatva. Ez a választás határozza meg, melyik lehetőség áll rendelkezésre az ügyfélidentitás-tárolóval (Azure Active Directory vagy Active Directory összevonási szolgáltatások) és a számlázási modellt (kell fizetnie, a használat alapú számlázási vagy a kapacitás-alapú számlázási) a következő ábra és táblázat foglalja össze: 

![Az Azure Stack üzemelő példányához és számlázási forgatókönyvek](media/azure-stack-connection-models/azure-stack-scenarios.png)  
  
> [!IMPORTANT]
> Ez a fő döntési pont! Az Active Directory összevonási szolgáltatások (AD FS) vagy az Azure Active Directory (Azure AD)-e egy egyszeri döntés üzembe helyezéskor kell végrehajtania. Nem módosíthatja ezt később ismételt üzembe helyezése a teljes rendszer nélkül.  


|Beállítások|Az Azure-hoz csatlakoztatva|Az Azure-ból leválasztva|
|-----|:-----:|:-----:|
|Azure AD|![Támogatott](media/azure-stack-connection-models/check.png)| |
|AD FS|![Támogatott](media/azure-stack-connection-models/check.png)|![Támogatott](media/azure-stack-connection-models/check.png)|
|Fogyasztás alapú számlázáshoz|![Támogatott](media/azure-stack-connection-models/check.png)| |
|Kapacitás-alapú számlázás|![Támogatott](media/azure-stack-connection-models/check.png)|![Támogatott](media/azure-stack-connection-models/check.png)|
|Licencelés| Nagyvállalati szerződés vagy a Cloud Solution Provider | Nagyvállalati szerződés |
|A javítások és frissítések|Frissítési csomag közvetlenül az internetről letölthető az Azure Stackhez |  Szükséges<br><br>Cserélhető adathordozó is szükséges<br> és a egy különálló csatlakoztatott eszköz |
| Regisztráció | Automatikus | Szükséges<br><br>Cserélhető adathordozó is szükséges<br> és a egy különálló csatlakoztatott eszköz |

Miután kiválasztotta az az Azure-kapcsolat modell használható az Azure Stack üzemelő példányához, további, a kapcsolat-függő döntéseket kell meghozni az ügyfélidentitás-tárolóval és a számlázási mód. 

## <a name="next-steps"></a>További lépések

[Az Azure-ra csatlakoztatott Azure Stack üzemelő példányaival kapcsolatos döntések](azure-stack-connected-deployment.md)

[Az Azure-ra nem csatlakozó Azure Stack üzemelő példányaival kapcsolatos döntések](azure-stack-disconnected-deployment.md)
