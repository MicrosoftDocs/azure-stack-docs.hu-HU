---
title: App Service on Azure Stack Update 2 kibocsátási megjegyzései | Microsoft Docs
description: Ismerkedjen meg a javításokkal, javításokkal és a 2. frissítéssel kapcsolatos ismert problémákkal a Azure Stack App Service.
services: azure-stack
documentationcenter: ''
author: bryanla
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
ms.reviewer: anwestg
ms.lastreviewed: 05/18/2018
ms.openlocfilehash: f427a31001f8f486fd231af7e59ef2bb30592661
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974810"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>App Service on Azure Stack Update 2 kibocsátási megjegyzései

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ezek a kibocsátási megjegyzések ismertetik a Azure App Service Azure Stack Update 2 verziójának javításait, javításait és ismert problémáit. Az ismert problémák három szakaszra oszlanak: az üzembe helyezéshez közvetlenül kapcsolódó problémák, a frissítési folyamattal kapcsolatos problémák és a build (telepítés utáni) problémák.

> [!IMPORTANT]
> Alkalmazza a 1804-es frissítést a Azure Stack integrált rendszerre, vagy telepítse a legújabb Azure Stack Development Kitt (ASDK) a Azure App Service 1,2 üzembe helyezése előtt.

## <a name="build-reference"></a>Hivatkozás létrehozása

A (z) Azure Stack Update 2 Build számának App Service **72.0.13698.10**.

### <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> A Azure App Service on Azure Stack új központi telepítései a következő [három tulajdonos helyettesítő karaktert](azure-stack-app-service-before-you-get-started.md#get-certificates) igénylik, mert a kudu-hez tartozó SSO-t a Azure app Service kezeli. Az új Tárgy:  **\*. SSO.\< appservice. régió\>.\< tartománynév\>.\< kiterjesztés\>**

A telepítés megkezdése előtt tekintse át a [App Service Azure stack üzembe helyezésének előfeltételeit](azure-stack-app-service-before-you-get-started.md) .

### <a name="new-features-and-fixes"></a>Új funkciók és javítások

A Azure App Service on Azure Stack Update 2 a következő javításokat és javításokat tartalmazza:

- A **app Service bérlő, a rendszergazda, a functions portál és a kudu eszközök**frissítései. Konzisztens a Azure Stack Portal SDK verziójával.

- A **Azure functions Runtime** és a **v 1.0.11612**frissítése.

- Az alapszolgáltatások frissítései a megbízhatóság és a hibák javításához, ami lehetővé teszi a gyakori problémák egyszerűbb diagnosztizálását.

- **A következő alkalmazás-keretrendszerek és eszközök frissítései**:
  - A .NET-keretrendszer 4.7.1 hozzáadva
  - **Node. js** -verziók hozzáadva:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - **NPM** -verziók hozzáadva:
    - 5.6.0
  - A .NET Core-összetevők frissítése a nyilvános felhőben Azure App Service konzisztens.
  - Frissített kudu

- Üzembe helyezési pontok automatikus cseréje engedélyezve – [automatikus felcserélés beállítása](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap).

- Az éles környezetben való tesztelés engedélyezve van – [Bevezetés az éles környezetben végzett tesztelésbe](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/).

- Azure Functions-proxyk engedélyezve – [a Azure functions-proxyk használata](https://docs.microsoft.com/azure/azure-functions/functions-proxies).

- A App Service felügyeleti bővítmény UX-támogatása a következőhöz lett hozzáadva:
  - Titkos kód elforgatása
  - Tanúsítvány elforgatása
  - Rendszer hitelesítő adatainak elforgatása
  - A kapcsolatok karakterláncának elforgatása

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

- A feldolgozók nem tudják elérni a fájlkiszolgálón, ha a App Service egy meglévő virtuális hálózaton van telepítve, és a fájlkiszolgáló csak a magánhálózaton érhető el.

Ha úgy döntött, hogy egy meglévő virtuális hálózatra és egy belső IP-címet helyez üzembe a fájlkiszolgálón való kapcsolódáshoz, hozzá kell adnia egy kimenő biztonsági szabályt, amely engedélyezi az SMB-forgalmat a munkavégző alhálózat és a fájlkiszolgáló között. Nyissa meg a WorkersNsg a felügyeleti portálon, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:

* Forrás: Any
* Forrásoldali porttartomány: *
* Cél: IP-címek
* Cél IP-címtartomány: A fájlkiszolgáló IP-címeinek tartománya
* Célport tartománya: 445
* Protokoll: TCP
* Művelet: Allow
* Fontosság: 700
* Név: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Ismert problémák a Cloud adminok operációs Azure App Service Azure Stack

Tekintse meg a dokumentációt a [Azure Stack 1804 kibocsátási megjegyzésekben](azure-stack-update-1903.md)

## <a name="next-steps"></a>További lépések

- A Azure App Service áttekintését lásd: [Azure App Service Azure stack áttekintése](azure-stack-app-service-overview.md).
- A Azure Stack App Service telepítésének előkészítésével kapcsolatos további információkért lásd: [app Service üzembe helyezésének előfeltételei a Azure stack](azure-stack-app-service-before-you-get-started.md).
