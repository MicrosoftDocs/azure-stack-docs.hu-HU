---
title: Használati kapcsolódási problémák és hibák az Azure Stackben |} A Microsoft Docs
description: Hibaelhárítás az Azure Stack használati problémákat és hibákat.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 57c546ec3583c9e04594e4da542a3c2ce3f72c62
ms.sourcegitcommit: bcaad8b7db2ea596018d973cb29283d8c6daebfb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2019
ms.locfileid: "67419757"
---
# <a name="usage-connectivity-errors"></a>Használati kapcsolódási hibák

Az Azure Stack használati adatokat küld a az Azure által a [ *Azure híd* összetevő](azure-stack-usage-reporting.md) az Azure Stackben. Ha az Azure Stack belül híd nem lehet csatlakozni az Azure-használat szolgáltatás, a következő hiba jelenik meg:

![használati híd hiba](media/azure-stack-usage-issues/usageerror2.png)

Az ablak előfordulhat, hogy adja meg a hiba és a megoldással kapcsolatos további információt:

![hiba feloldása](media/azure-stack-usage-issues/usageerror3.png)

## <a name="resolve-connectivity-issues"></a>Kapcsolati problémák elhárításához

A probléma megoldásához próbálja meg a következőket:

- Győződjön meg arról, hogy a hálózati konfiguráció lehetővé teszi, hogy az Azure híd a távoli szolgáltatáshoz való csatlakozáshoz.

- Nyissa meg a [ **régiók kezelése** > **tulajdonságok** ](azure-stack-registration.md#verify-azure-stack-registration) panelen található az Azure-előfizetési Azonosítóját, a regisztráció, erőforráscsoport és nevét, a regisztráció az erőforrás. Győződjön meg arról, hogy a regisztrációs erőforrás létezik-e az Azure Portalon a megfelelő Azure-előfizetés azonosítója alapján. Ehhez nyissa meg **összes erőforrás** létrehozott Azure-előfizetési Azonosítóját, és ellenőrizze a **rejtett típusok megjelenítése** mezőbe. Ha a regisztráció erőforrás nem található, kövesse a [megújítása vagy módosítás regisztrációs](azure-stack-registration.md#renew-or-change-registration) újra regisztrálni az Azure Stack.

  ![Portál](media/azure-stack-usage-issues/stackres.png)

## <a name="error-codes"></a>Hibakódok

Ez a szakasz ismerteti a használati hibakódok.

| Hibakód                 | Probléma                                                                                                                                             | Szervizelés                                                                                                                                                                                                                                                                                        |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NetworkError               | Az Azure Stack-híd nem tudja kérés küldése a szolgáltatásvégpont használat az Azure-ban.                                                            | Ha a proxy blokkolja-e vagy elfogja a használati szolgáltatásvégpont való hozzáférés ellenőrzése.                                                                                                                                                                                                             |
| RequestTimedOut            | Kérelem el lett küldve, az Azure hídról, de a használatmérő szolgáltatást az Azure-ban nem válaszol az időkorláton belül.                             | Ha a proxy blokkolja-e vagy elfogja a használati szolgáltatásvégpont való hozzáférés ellenőrzése.                                                                                                                                                                                                                        |
| LoginError                 | Nem lehet hitelesíteni a Microsoft Azure Active Directoryval.                                                                                                             | Győződjön meg arról, az Azure AD bejelentkezési végpont érhető el minden XRP virtuális gép az Azure Stackben.                                                                                                                                                                                                                     |
| CertificateValidationError | Az Azure híd nem sikerül, a kérelem elküldéséhez, mert nem tudja hitelesíteni az Azure-szolgáltatások.                                    | Ellenőrizze, hogy van-e a proxy elfogja a HTTPS-forgalmat az Azure Stack XRP számítógép és a használati felhőátjárók végpontjaival.                                                                                                                                                                                      |
| Nem engedélyezett               | Az Azure híd nem sikerül adatok leküldése az Azure-ban, a használatmérő szolgáltatást, mert az Azure-szolgáltatás nem képes hitelesíteni az Azure Stack híd. | Ellenőrizze, hogy a regisztrációs erőforrás módosult, és ha igen, az Azure Stack regisztrálja újra. <br><br> Egyes esetekben egy Azure Stack és az Azure AD sync problémát okozhat, ez a hiba ideje. Ebben az esetben ügyeljen arra, az időpontokat, az Azure Stacken XRP virtuális gépeken az Azure ad-vel szinkronizált. |
|                            |                                                                                                                                                   |                                                                                                                                                                                                                                                                                                    |

Ezenkívül, szükség lehet a adja meg a naplófájlokat az Azure-hidat, WAS és WASPublic összetevők a következő [ezeket a lépéseket](azure-stack-diagnostics.md#log-collection-tool).

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Azure Stack használati adatokról szóló jelentéseket küldeni az Azure-bA](azure-stack-usage-reporting.md).
- Tekintse át a hibaüzeneteket, ha a regisztrációs folyamat során el, lásd: [bérlő regisztrációs hibaüzenetek](azure-stack-registration-errors.md).
- Tudjon meg többet a [használati jelentéskészítési infrastruktúra felhőszolgáltatók](azure-stack-csp-ref-infrastructure.md).
