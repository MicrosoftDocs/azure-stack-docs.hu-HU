---
title: App Service Azure Stack hub Update 2 kibocsátási megjegyzései
description: Ismerkedjen meg a Azure Stack hub App Service a 2. frissítésében szereplő javításokkal, javításokkal és ismert problémákkal.
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/18/2018
ms.openlocfilehash: 029601b9bac3bc3b14d2ab4202df072b6d9063de
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875793"
---
# <a name="app-service-on-azure-stack-hub-update-2-release-notes"></a>App Service Azure Stack hub Update 2 kibocsátási megjegyzései

Ezek a kibocsátási megjegyzések ismertetik a Azure App Service Azure Stack hub Update 2 verziójának javításait, javításait és ismert problémáit. Az ismert problémák három szakaszra oszlanak: az üzembe helyezéshez közvetlenül kapcsolódó problémák, a frissítési folyamattal kapcsolatos problémák és a build (telepítés utáni) problémák.

> [!IMPORTANT]
> Alkalmazza az 1804-es frissítést az Azure Stack hub integrált rendszerére, vagy telepítse a legújabb Azure Stack Development Kit (ASDK) a Azure App Service 1,2 telepítése előtt.

## <a name="build-reference"></a>Build referenciája

A App Service Azure Stack hub Update 2 Build száma **72.0.13698.10**.

### <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> A Azure App Service Azure Stack hub-beli új központi telepítései mostantól egy [három tárgyú helyettesítő karaktert](azure-stack-app-service-before-you-get-started.md#get-certificates) igényelnek, mivel a kudu-hez tartozó SSO-t a Azure app Service-ben kezelik. Az új Tárgy: **\*. SSO. appservice.\<régió\>.\<tartománynév\>.\<bővítmény\>**

Az üzembe helyezés megkezdése előtt tekintse át a [app Service telepítésének Előfeltételeit Azure stack hub-on](azure-stack-app-service-before-you-get-started.md) .

### <a name="new-features-and-fixes"></a>Új funkciók és javítások

A Azure App Service on Azure Stack hub Update 2 a következő javításokat és javításokat tartalmazza:

- A **app Service bérlő, a rendszergazda, a functions portál és a kudu eszközök**frissítései. Konzisztens Azure Stack hub Portal SDK verziójával.

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

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)

- A feldolgozók nem tudják elérni a fájlkiszolgálón, ha a App Service egy meglévő virtuális hálózaton van telepítve, és a fájlkiszolgáló csak a magánhálózaton érhető el.

Ha úgy döntött, hogy egy meglévő virtuális hálózatra és egy belső IP-címet helyez üzembe a fájlkiszolgálón való kapcsolódáshoz, hozzá kell adnia egy kimenő biztonsági szabályt, amely engedélyezi az SMB-forgalmat a munkavégző alhálózat és a fájlkiszolgáló között. Nyissa meg a WorkersNsg a felügyeleti portálon, és adjon hozzá egy kimenő biztonsági szabályt a következő tulajdonságokkal:

* Forrás: bármely
* Forrásoldali porttartomány: *
* Cél: IP-címek
* Célként megadott IP-címtartomány: a fájlkiszolgáló IP-címeinek tartománya
* Célport tartománya: 445
* Protokoll: TCP
* Művelet: Engedélyezés
* Prioritás: 700
* Név: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Ismert problémák a Cloud adminok operációs Azure App Service Azure Stack központban

Tekintse meg az [Azure stack Hub 1804 kibocsátási megjegyzései](azure-stack-update-1903.md) dokumentációját

## <a name="next-steps"></a>Következő lépések

- A Azure App Service áttekintését lásd: [Azure App Service Azure stack hub – áttekintés](azure-stack-app-service-overview.md).
- További információ a App Service telepítésének előkészítéséről Azure Stack hub-on: [app Service üzembe helyezésének előfeltételei az Azure stack hub-on](azure-stack-app-service-before-you-get-started.md).
