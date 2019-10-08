---
title: Kapcsolódási problémák és hibák a Azure Stackban | Microsoft Docs
description: Hibaelhárítás Azure Stack használati problémák és hibák elhárítása.
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
ms.date: 10/04/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 265645b69e909c7c9762cd03e4f49035baa2872b
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019423"
---
# <a name="usage-connectivity-errors"></a>Használati csatlakozási hibák

Azure Stack a használati adatokat az Azure-ba küldi a rendszer a Azure Stack [ *Azure Bridge* összetevője](azure-stack-usage-reporting.md) . Ha a Azure Stackon belüli híd nem tud csatlakozni az Azure-beli használati szolgáltatáshoz, a következő hibaüzenet jelenik meg:

![használati híd hibája](media/azure-stack-usage-issues/usageerror2.png)

Az ablakban további információk is megadhatók a hibáról és a megoldásról:

![hiba feloldása](media/azure-stack-usage-issues/usageerror3.png)

## <a name="resolve-connectivity-issues"></a>Kapcsolódási problémák megoldása

A probléma megoldásához próbálkozzon a következő lépésekkel:

- Ellenőrizze, hogy a hálózati konfiguráció lehetővé teszi-e a Azure Bridge számára a távoli szolgáltatáshoz való kapcsolódást.

- Lépjen a [ **régió kezelése** > **Tulajdonságok panelre** ](azure-stack-registration.md#verify-azure-stack-registration) , ahol megkeresheti a regisztrációhoz, az erőforráscsoporthoz és a regisztrációs erőforrás NEVÉhez használt Azure-előfizetés azonosítóját. Győződjön meg arról, hogy a regisztrációs erőforrás létezik a megfelelő Azure-előfizetési AZONOSÍTÓban Azure Portalban. Ehhez nyissa meg az Azure-előfizetés azonosítója alatt létrehozott **összes erőforrást** , és jelölje be a **rejtett típusok megjelenítése** jelölőnégyzetet. Ha nem találja a regisztrációs erőforrást, kövesse a [megújítása vagy a regisztráció módosítása](azure-stack-registration.md#renew-or-change-registration) a Azure stack újbóli regisztrálásához című témakör lépéseit.

  ![Portál](media/azure-stack-usage-issues/stackres.png)

## <a name="error-codes"></a>Hibakódok

Ez a szakasz a használati hibakódokat ismerteti.

| Hibakód                 | Probléma                                                                                                                                             | Szervizelés                                                                                                                                                                                                                                                                                        |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NetworkError               | Azure Stack híd nem tud elküldeni egy kérést a használati szolgáltatás végpontjának az Azure-ban.                                                            | Ellenőrizze, hogy a proxy blokkolja-e vagy elfogja-e a használati szolgáltatás végpontjának elérését.                                                                                                                                                                                                             |
| RequestTimedOut            | A rendszer elküldte a kérelmet a Azure Bridge, de az Azure-beli használati szolgáltatás nem válaszolt az időtúllépési időszakon belül.                             | Ellenőrizze, hogy a proxy blokkolja-e vagy elfogja-e a használati szolgáltatás végpontjának elérését.                                                                                                                                                                                                                        |
| LoginError                 | Nem sikerült hitelesíteni a Microsoft Azure Active Directory.                                                                                                             | Győződjön meg arról, hogy az Azure AD bejelentkezési végpontja elérhető a Azure Stack összes XRP virtuális gépről.                                                                                                                                                                                                                     |
| CertificateValidationError | Az Azure-híd nem tudja elküldeni a kérést, mert nem tud hitelesíteni az Azure szolgáltatással.                                    | Ellenőrizze, hogy van-e proxy a Azure Stack XRP-gép és a használati átjáró végpontja között a HTTPS-forgalom elfogásával.                                                                                                                                                                                      |
| Nem engedélyezett               | Az Azure-híd nem tud adatokat leküldeni a használati szolgáltatásnak az Azure-ban, mert az Azure-szolgáltatás nem tudja hitelesíteni a Azure Stack hidat. | Ellenőrizze, hogy a regisztrációs erőforrás módosult-e, és ha igen, regisztrálja újra Azure Stack. <br><br> Időnként a Azure Stack és az Azure AD közötti szinkronizálási probléma okozhatja ezt a hibát. Ebben az esetben ügyeljen arra, hogy az Azure Stack XRP virtuális gépeken lévő idő szinkronban legyen az Azure AD-vel. |
|                            |                                                                                                                                                   |                                                                                                                                                                                                                                                                                                    |

Emellett előfordulhat, hogy az alábbi [lépéseket](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep-to-collect-diagnostic-logs)követve meg kell adnia a naplófájlokat a Azure Bridge, a és a WASPublic összetevőkhöz.

## <a name="next-steps"></a>További lépések

- További információ a [Azure stack használati adatok Azure-ba történő jelentéskészítéséről](azure-stack-usage-reporting.md).
- Ha a regisztrációs folyamat során aktiválják a hibaüzeneteket, tekintse át a [bérlői regisztrációs hibaüzeneteket](azure-stack-registration-errors.md).
- További információ a [felhőalapú megoldások szolgáltatóinak használati jelentési infrastruktúráról](azure-stack-csp-ref-infrastructure.md).
