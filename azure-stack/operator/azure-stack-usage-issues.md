---
title: Kapcsolódási problémák és hibák a Azure Stack központban
description: Azure Stack hub-használati problémák és hibák elhárítása.
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 79f34d685998a6719bc580504d4d26a8e4be093f
ms.sourcegitcommit: 2377c6947cf846fd2a4a0274c41326293a2a239c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77075989"
---
# <a name="usage-connectivity-errors"></a>Használati csatlakozási hibák

Azure Stack hub használati adatait a rendszer az Azure-ba küldi az Azure Stack hub [*Azure Bridge*](azure-stack-usage-reporting.md) összetevőjében. Ha a Azure Stack hub-n belüli híd nem tud csatlakozni az Azure-beli használati szolgáltatáshoz, a következő hibaüzenet jelenik meg:

![használati híd hibája](media/azure-stack-usage-issues/usageerror2.png)

Az ablakban további információk is megadhatók a hibáról és a megoldásról:

![hiba feloldása](media/azure-stack-usage-issues/usageerror3.png)

## <a name="resolve-connectivity-issues"></a>Kapcsolódási problémák megoldása

A probléma megoldásához próbálkozzon a következő lépésekkel:

- Ellenőrizze, hogy a hálózati konfiguráció lehetővé teszi-e a Azure Bridge számára a távoli szolgáltatáshoz való kapcsolódást.

- A régió- [ **felügyeleti** > **Tulajdonságok** ](azure-stack-registration.md#verify-azure-stack-hub-registration) paneljén megkeresheti a regisztrációhoz, az erőforráscsoporthoz és a regisztrációs erőforrás NEVÉhez használt Azure-előfizetés azonosítóját. Győződjön meg arról, hogy a regisztrációs erőforrás létezik a megfelelő Azure-előfizetési AZONOSÍTÓban Azure Portalban. Ehhez nyissa meg az Azure-előfizetés azonosítója alatt létrehozott **összes erőforrást** , és jelölje be a **rejtett típusok megjelenítése** jelölőnégyzetet. Ha nem találja a regisztrációs erőforrást, kövesse a [megújítása vagy a regisztráció módosítása](azure-stack-registration.md#renew-or-change-registration) az Azure stack hub újbóli regisztrálásához című témakör lépéseit.

  ![Portál](media/azure-stack-usage-issues/stackres.png)

## <a name="error-codes"></a>Hibakódok

Ez a szakasz a használati hibakódokat ismerteti.

| Hibakód                 | Probléma                                                                                                                                             | Szervizelés                                                                                                                                                                                                                                                                                        |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NetworkError               | Azure Stack hub-híd nem tud elküldeni egy kérést a használati szolgáltatási végpontnak az Azure-ban.                                                            | Ellenőrizze, hogy a proxy blokkolja-e vagy elfogja-e a használati szolgáltatás végpontjának elérését.                                                                                                                                                                                                             |
| RequestTimedOut            | A rendszer elküldte a kérelmet a Azure Bridge, de az Azure-beli használati szolgáltatás nem válaszolt az időtúllépési időszakon belül.                             | Ellenőrizze, hogy a proxy blokkolja-e vagy elfogja-e a használati szolgáltatás végpontjának elérését.                                                                                                                                                                                                                        |
| LoginError                 | Nem sikerült hitelesíteni a Microsoft Azure Active Directory.                                                                                                             | Győződjön meg arról, hogy az Azure AD bejelentkezési végpontja Azure Stack hub összes XRP virtuális gépről elérhető.                                                                                                                                                                                                                     |
| CertificateValidationError | Az Azure-híd nem tudja elküldeni a kérést, mert nem tud hitelesíteni az Azure szolgáltatással.                                    | Ellenőrizze, hogy van-e proxy a Azure Stack hub XRP-gép és a használati átjáró végpontja közötti HTTPS-forgalom elfogásával.                                                                                                                                                                                      |
| Nem engedélyezett               | Az Azure-híd nem tud adatokat leküldeni a használati szolgáltatásnak az Azure-ban, mert az Azure-szolgáltatás nem tudja hitelesíteni az Azure Stack hub-hidat. | Ellenőrizze, hogy a regisztrációs erőforrás módosult-e, és ha igen, regisztrálja újra Azure Stack hubot. <br><br> Időnként a Azure Stack hub és az Azure AD közötti szinkronizálási probléma okozhatja ezt a hibát. Ebben az esetben ügyeljen arra, hogy az Azure Stack hub XRP virtuális gépei hányszor legyenek szinkronizálva az Azure AD-vel. |
|                            |                                                                                                                                                   |                                                                                                                                                                                                                                                                                                    |

Emellett előfordulhat, hogy az alábbi [lépéseket](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)követve meg kell adnia a naplófájlokat a Azure Bridge, a és a WASPublic összetevőkhöz.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure stack hub használati adatainak az Azure-ba történő jelentéskészítéséről](azure-stack-usage-reporting.md).
- Ha a regisztrációs folyamat során aktiválják a hibaüzeneteket, tekintse át a [bérlői regisztrációs hibaüzeneteket](azure-stack-registration-errors.md).
- További információ a [felhőalapú megoldások szolgáltatóinak használati jelentési infrastruktúráról](azure-stack-csp-ref-infrastructure.md).
