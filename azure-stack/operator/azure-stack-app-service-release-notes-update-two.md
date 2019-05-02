---
title: Az Azure Stack App Service frissítése 2 kibocsátási megjegyzései |} A Microsoft Docs
description: Ismerje meg a frissítés két App Service-ben az Azure Stacken, az ismert hibákat, és hol töltse le a frissítést.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: sethm
ms.lastreviewed: 05/18/2018
ms.openlocfilehash: 790f01583d589cdea226133d3ded9693cdf68af0
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64290483"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>App Service-ben az Azure Stack 2-es frissítés – kibocsátási megjegyzések

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

A kibocsátási megjegyzések láthatók a fejlesztései, valamint a javításokat az Azure App Service az Azure Stack Update 2 és olyan ismert problémákat. Ismert problémák az üzembe helyezési, frissítési folyamat és a build (telepítés utáni) problémái közvetlenül kapcsolódó problémák vannak felosztva.

> [!IMPORTANT]
> Az Azure Stackkel integrált rendszereknél 1804 frissítés alkalmazása, vagy a legújabb Azure Stack fejlesztői készletének telepítése előtt üzembe helyezése az Azure App Service 1.2-es.
>
>

## <a name="build-reference"></a>Hivatkozás létrehozása

Az App Service az Azure Stack 2. frissítés buildszámát **72.0.13698.10**

### <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Új telepítések esetén az Azure App Service az Azure Stacken most szükséges egy [három-tulajdonos helyettesítő tanúsítvány](azure-stack-app-service-before-you-get-started.md#get-certificates) köszönhető, amelyben az Azure App Service mostantól kezelése a Kudu egyszeri Bejelentkezést. Az új tulajdonos  **\*. sso.appservice.\< régió\>.\< tartománynév\>.\< bővítmény\>**
>
>

Tekintse meg a [mielőtt elkezdené a dokumentáció](azure-stack-app-service-before-you-get-started.md) központi telepítésének megkezdése előtt.

### <a name="new-features-and-fixes"></a>Új funkciókkal és javításokkal

Az Azure App Service az Azure Stack Update 2 tartalmazza a következő fejlesztések és javítások:

- Frissítések **App Services-bérlő, a rendszergazda, a Functions-portálok és eszközök a Kudu**. Az Azure Stack Portal SDK-verzió összhangban.

- Frissítések **Azure Functions runtime** való **v1.0.11612**.

- Fokozható a megbízhatóság és a hibaüzenetek gyakori problémák egyszerűbb diagnosztika engedélyezése a core-szolgáltatás frissítése.

- **A következő alkalmazás-keretrendszerek és eszközök frissítések**:
  - A hozzáadott .NET-keretrendszer 4.7.1.
  - Hozzáadott **Node.JS** verziók:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Hozzáadott **NPM** verziók:
    - 5.6.0
  - Frissítve a .NET Core összetevők konzisztens az Azure App Service nyilvános felhőben.
  - Updated Kudu

- Automatikus felcserélés az üzembe helyezési tárhelyek szolgáltatás engedélyezve van - [automatikus felcserélés konfigurálása](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap)

- Az éles funkció engedélyezve van – tesztelés [tesztelés éles környezetben bemutatása](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Az Azure Functions-proxyk engedélyezése – [Azure Functions-proxyk használata](https://docs.microsoft.com/azure/azure-functions/functions-proxies)

- App Service-ben a felügyeleti bővítmény UX támogatja a következőkhöz:
  - Titkos kód rotációja
  - Tanúsítvány rotációja
  - Rendszer hitelesítő adatok elforgatása
  - Kapcsolati karakterlánc rotációja

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

- Feldolgozók nem érhető el a fájlkiszolgálót, amikor az App Service-ben meglévő virtuális hálózaton van üzembe helyezve, és a fájlkiszolgáló csak érhető el a magánhálózaton.

Ha úgy döntött, hogy egy meglévő virtuális hálózattal és belső IP-cím szeretne csatlakozni a fájlkiszolgáló üzembe helyezése, hozzá kell adnia egy kimenő biztonsági szabályt a feldolgozó és a fájlkiszolgáló között SMB-forgalom engedélyezése. Ehhez nyissa meg a WorkersNsg a felügyeleti portálon, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:
* Forrás: Bármelyik
* Forrás porttartomány: *
* Cél: IP-címek
* Cél IP-címtartomány: IP-címtartományt a fájlkiszolgálóhoz
* Cél porttartomány: 445
* Protokoll: TCP
* Művelet: Engedélyezés
* Prioritás: 700
* Név: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Ismert problémák működtetése az Azure App Service az Azure Stack felhő-rendszergazdák számára

A dokumentáció a [Azure Stack 1804 kibocsátási megjegyzései](azure-stack-update-1903.md)

## <a name="next-steps"></a>További lépések

- Az Azure App Service áttekintését lásd: [Azure App Service az Azure Stack áttekintése](azure-stack-app-service-overview.md).
- Hogyan készíti elő az Azure Stack App Service üzembe helyezése kapcsolatos további információkért lásd: [az App Service-ben az Azure Stack használatának megkezdése előtt](azure-stack-app-service-before-you-get-started.md).
