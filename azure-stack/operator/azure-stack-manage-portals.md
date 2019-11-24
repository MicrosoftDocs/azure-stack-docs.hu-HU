---
title: A Azure Stack felügyeleti portál használata | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Azure Stack a felügyeleti portált.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/07/2019
ms.author: justinha
ms.reviewer: efemmano
ms.lastreviewed: 06/07/2019
ms.openlocfilehash: a5a2c86d4680d38b3923eea4a04cacbf61fff108
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283497"
---
# <a name="use-the-administrator-portal-in-azure-stack"></a>A Azure Stack felügyeleti portáljának használata

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Két portál található Azure Stackban: a felügyeleti Portálon és a felhasználói portálon. Azure Stack kezelőként a felügyeleti portálon használhatja a Azure Stack napi felügyeletét és műveleteit.

## <a name="access-the-administrator-portal"></a>Hozzáférés a felügyeleti portálhoz

A felügyeleti portál eléréséhez keresse meg a portál URL-címét, és jelentkezzen be a Azure Stack-kezelő hitelesítő adataival. Integrált rendszer esetén a portál URL-címe a Azure Stack üzemelő példányának régió neve és külső teljes tartományneve (FQDN) alapján változik. A felügyeleti portál URL-címe mindig megegyezik a Azure Stack Development Kit (ASDK) üzemelő példányok esetében.

| Környezet | Felügyeleti portál URL-címe |   
| -- | -- | 
| AZURE STACK FEJLESZTŐI KÉSZLET (ASDK)| https://adminportal.local.azurestack.external  |
| Integrált rendszerek | https://adminportal.&lt; *régió*&gt;.&lt;*FQDN*&gt; | 
| | |

> [!TIP]
> ASDK-környezet esetén először győződjön meg arról, hogy Távoli asztali kapcsolat vagy virtuális magánhálózati (VPN) [kapcsolaton keresztül tud csatlakozni a fejlesztői csomag gazdagépéhez](../asdk/asdk-connect.md) .

 ![Azure Stack felügyeleti portál](media/azure-stack-manage-portals/admin-portal.png)

Az összes Azure Stack üzemelő példány alapértelmezett időzónája az egyezményes világidő (UTC) szerint van beállítva.

A felügyeleti portálon a következőkhöz hasonló műveleteket végezhet:

* [Az Azure Stack regisztrálása az Azure-ban](azure-stack-registration.md)
* [A piactér feltöltése](azure-stack-download-azure-marketplace-item.md)
* [Csomagok, ajánlatok és előfizetések létrehozása felhasználók számára](service-plan-offer-subscription-overview.md)
* [Állapotfelügyelet és riasztások](azure-stack-monitor-health.md)
* [Azure Stack frissítések kezelése](azure-stack-updates.md)

A gyors üzembe helyezési **útmutató** csempéje a leggyakoribb feladatokhoz kapcsolódó online dokumentációra mutató hivatkozásokat tartalmaz.

Bár az operátorok olyan erőforrásokat is létrehozhatnak, mint például a virtuális gépek, a virtuális hálózatok és a Storage-fiókok a felügyeleti portálon, az erőforrások létrehozásához és teszteléséhez be kell [jelentkezniük a felhasználói portálra](../user/azure-stack-use-portal.md) .

>[!NOTE]
>A gyors üzembe helyezési oktatóanyag csempén a **virtuális gép létrehozása** hivatkozásra kattintva létrehoz egy virtuális gépet a felügyeleti portálon, de ez csak annak ellenőrzésére szolgál, hogy a Azure stack sikeresen telepítve lett-e.

## <a name="understand-subscription-behavior"></a>Az előfizetés viselkedésének megismerése

A felügyeleti portálon alapértelmezés szerint három előfizetés jön létre: a felhasználás, az alapértelmezett szolgáltató és a mérés. Operátorként többnyire az *alapértelmezett szolgáltatói előfizetést*fogja használni. Nem adhat hozzá más előfizetéseket, és nem használhatja azokat a felügyeleti portálon.

Más előfizetéseket a felhasználók a felhasználói portálon hozhatnak létre a számukra létrehozott csomagok és ajánlatok alapján. A felhasználói portál azonban nem biztosít hozzáférést a felügyeleti portál bármely adminisztratív vagy működési képességéhez.

A rendszergazdák és a felhasználói portálok a Azure Resource Manager külön példányain vannak támogatva. A Azure Resource Manager elkülönítése miatt az előfizetések nem kereszteznek portálokat. Ha például Azure Stack operátorként jelentkezik be a felhasználói portálra, az *alapértelmezett szolgáltatói előfizetés*nem érhető el. Habár nem rendelkezik hozzáféréssel egyetlen rendszergazdai feladathoz sem, létrehozhat előfizetéseket saját magához az elérhető nyilvános ajánlatok közül. Ha bejelentkezett a felhasználói portálra, akkor a bérlői felhasználónak minősül.

  >[!NOTE]
  >ASDK-környezetben, ha a felhasználó ugyanahhoz a bérlői címtárhoz tartozik, mint a Azure Stack operátor, nem tiltják le a bejelentkezést a felügyeleti portálra. Azonban nem férhetnek hozzá a rendszergazdai funkciókhoz, vagy előfizetések hozzáadásával hozzáférhetnek a felhasználói portálon elérhető ajánlatokhoz.

## <a name="administrator-portal-tips"></a>Rendszergazdai portál – tippek

### <a name="customize-the-dashboard"></a>Az irányítópult testreszabása

Az irányítópult az alapértelmezett csempék készletét tartalmazza. Az irányítópult **szerkesztése** lehetőség kiválasztásával módosíthatja az alapértelmezett irányítópultot, vagy kiválaszthat egy **új irányítópultot** egyéni irányítópult hozzáadásához. Csempéket is hozzáadhat az irányítópulthoz. Válassza például a **+ erőforrás létrehozása**lehetőséget, kattintson a jobb gombbal az **ajánlatok + csomagok**lehetőségre, majd válassza **a rögzítés az irányítópulton**lehetőséget.

Időnként előfordulhat, hogy megjelenik egy üres irányítópult a portálon. Az irányítópult helyreállításához kattintson az **irányítópult szerkesztése**lehetőségre, majd kattintson a jobb gombbal, és válassza **a visszaállítás az alapértelmezett állapotba**lehetőséget.

### <a name="quick-access-to-online-documentation"></a>Gyors hozzáférés az online dokumentációhoz

A Azure Stack kezelő dokumentációjának eléréséhez használja a Súgó és támogatás ikont (kérdőjel) a felügyeleti portál jobb felső sarkában. Vigye a kurzort az ikonra, majd válassza a **Súgó + támogatás**lehetőséget.

### <a name="quick-access-to-help-and-support"></a>Gyors hozzáférés a súgóhoz és támogatáshoz

Ha a felügyeleti portál jobb felső sarkában található Súgó ikonra (kérdőjel) kattint, kattintson a **Súgó + támogatás**lehetőségre, majd az **új támogatási kérelem** a **támogatás**alatt lehetőségre, a következő eredmények egyike történik:

- Ha integrált rendszerű szolgáltatást használ, akkor a művelet megnyit egy webhelyet, ahol közvetlenül nyithat egy támogatási jegyet a Microsoft ügyfélszolgálati szolgáltatásaival (CSS). Ha segítségre van szüksége annak megismeréséhez, hogy a Microsoft támogatási szolgálata vagy az eredeti berendezésgyártó (OEM) hardvergyártó támogatását használja, olvassa el a következő [témakört](azure-stack-manage-basics.md#where-to-get-support) :.
- Ha a ASDK használja, ez a művelet közvetlenül a [Azure stack Fórumok webhelyet](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack) nyitja meg. Ezeket a fórumokat rendszeresen figyelik. Mivel a ASDK egy kiértékelési környezet, a Microsoft CSS-ben nem érhető el hivatalos támogatás.

### <a name="quick-access-to-the-azure-roadmap"></a>Gyors hozzáférés az Azure-ütemtervhez

Ha a felügyeleti portál jobb felső sarkában a **Súgó és támogatás** (kérdőjel) lehetőséget választja, majd kiválasztja az **Azure-útitervet**, megnyílik egy új böngésző lap, amely az Azure-útitervre mutat. Ha beírja a **Azure stack** kifejezést a **Products (termékek** ) keresőmezőbe, megtekintheti az összes Azure stack-ütemterv frissítését.

## <a name="next-steps"></a>További lépések

[Regisztrálja Azure stack az Azure](azure-stack-registration.md) -ban, és töltse fel [Azure stack Marketplace](azure-stack-marketplace.md) elemet a felhasználók számára.
